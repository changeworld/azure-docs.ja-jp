<properties
    pageTitle="Azure VM での AlwaysOn 可用性グループの手動構成 - Microsoft Azure"
    description="Azure Virtual Machines で AlwaysOn 可用性グループを作成します。 このチュートリアルでは、スクリプトではなく、主にユーザー インターフェイスとツールを使用します。"
    services="virtual-machines"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="timlt"
    editor="monicar"
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/22/2016"
    ms.author="MikeRayMSFT" />


# <a name="configure-always-on-availability-group-in-azure-vm-manually---resource-manager"></a>Azure VM での AlwaysOn 可用性グループの手動構成 - Resource Manager

> [AZURE.SELECTOR]
- [Resource Manager: テンプレート](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)
- [Resource Manager: 手動](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
- [クラシック: UI](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md)
- [クラシック: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md)

<br/>

この詳細なチュートリアルでは、Azure Resource Manager の仮想マシンに SQL Server の可用性グループを実装する方法について説明します。 

このチュートリアルでは、次の要素を作成します。

- 2 つのサブネットから成る仮想ネットワーク (フロントエンドのサブネットとバックエンドのサブネットを含む)

- 可用性セット内の 2 つのドメイン コントローラーと Active Directory (AD) ドメイン 

- バックエンド サブネットにデプロイされ、AD ドメインに参加している可用性セット内の 2 つの SQL Server VM

- 3 ノードの WSFC クラスター (ノード マジョリティ クォーラム モデル)

- 可用性グループに IP アドレスを提供する内部ロード バランサー

- 可用性データベースの 2 つの同期コミット レプリカを含む可用性グループ

このソリューションをグラフィカルに表すと、次の図のようになります。

![Azure Resource Manager での可用性グループのアーキテクチャ](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/00-EndstateSampleNoELB.png)

これは、考えられる 1 つの構成です。 要件に合わせてこれを変更することができます。 たとえば、ドメイン コント ローラーをクォーラム ファイル共有監視として使用することにより、仮想マシンの数を減らすことができます。 これにより、2 つのレプリカを持つ可用性グループ用の VM の数が減少します。 この方法では、解答よりも VM 数が 1 つ減少します。

このチュートリアルは、複数の Azure 仮想マシンを構築して構成する必要があるため、完了に数時間がかかります。 このソリューション全体を自動的に構築することもできます。 Azure ポータルのギャラリーから、AlwaysOn 可用性グループ (リスナーを含む) をセットアップできるようになっています。 このギャラリーのセットアップでは、可用性グループに必要なものすべてが自動的に構成されます。 詳細については、 [ポータル - Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md)に関するページをご覧ください。 

[AZURE.INCLUDE [availability-group-template](../../includes/virtual-machines-windows-portal-sql-alwayson-ag-template.md)]

このチュートリアルでは、次のことを前提としています。

- Azure アカウントを既に所有している。

- GUI を使用して、仮想マシン ギャラリーから SQL Server VM をプロビジョニングする方法を知っている。 詳細については、 [Azure での SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)

- 可用性グループについて十分に理解している。 詳細については、「 [AlwaysOn 可用性グループ (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)」をご覧ください。

>[AZURE.NOTE] SharePoint での SQL Server 可用性グループの使用に関心がある場合は、「 [SQL Server 2012 の AlwaysOn 可用性グループを SharePoint 2013 用に構成する](https://technet.microsoft.com/library/jj715261.aspx)」をご覧ください。

## <a name="create-resource-group"></a>Create resource group

1. [Azure ポータル](http://portal.azure.com)にサインインします。 

1. **[+新規]** をクリックし、**Marketplace** の検索ウィンドウに「**リソース グループ**」と入力します。

  ![[リソース グループ]](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/01-resourcegroupsymbol.png)

1.  **[リソース グループ]** 

  ![New Resource Group](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/01-newresourcegroup.png)

1. **[作成]**をクリックします。 

1. **[リソース グループ]** ブレードの **[リソース グループ名]** に「**SQL-HA-RG**」と入力します。

1. 複数の Azure サブスクリプションがある場合は、可用性グループの作成先となる Azure サブスクリプションであることを確認します。 

1. 場所を選択します。 場所とは、可用性グループを作成する Azure リージョンのことです。 このチュートリアルでは、すべてのリソースを Azure 上の特定の場所に構築します。 

1. **[ダッシュボードにピン留めする]** がオンになっていることを確認します。 これは、Azure ポータルのダッシュボードにリソース グループのショートカットを作成するためのオプションです。この設定は省略してもかまいません。 

1. **[作成]** をクリックしてリソース グループを作成します。

リソース グループが Azure に作成され、そのショートカットがポータルにピン留めされます。

## <a name="create-network-and-subnets"></a>ネットワークとサブネットの作成

次は、Azure リソース グループにネットワークとサブネットを作成します。

ここでは、1 つの仮想ネットワークと 2 つのサブネットを使用します。 Azure におけるネットワークの詳細については、「 [Virtual Network の概要](../virtual-network/virtual-networks-overview.md) 」を参照してください。

仮想ネットワークを作成するには、次の手順に従います。

1. Azure ポータルで新しいリソース グループをクリックし、 **+** をクリックしてリソース グループに新しい項目を追加します。 Azure の **[すべて]** ブレードが開きます。 

  ![New Item](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/02-newiteminrg.png)

1. 「 **仮想ネットワーク**」を検索します。

  ![Search Virtual Network](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/04-findvirtualnetwork.png)

1. **[仮想ネットワーク]**をクリックします。

1. **[仮想ネットワーク]** ブレードで **[Resource Manager]** デプロイメント モデルをクリックし、**[作成]** をクリックします。

  ![[仮想ネットワークの作成]](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/05-createvirtualnetwork.png)
 

 
1. **[仮想ネットワークの作成]** ブレードで仮想ネットワークを構成します。 

  次の表に、仮想ネットワークの設定を示します。 

  | **フィールド** | 値 |
| ----- | ----- |
| **名前** | autoHAVNET |
| **アドレス空間** | 10.0.0.0/16 |
| **サブネット名** | Subnet-1 |
| **サブネットのアドレス範囲** | 10.0.0.0/24 |
| **サブスクリプション** | 使用するサブスクリプションを指定します。 ご利用のサブスクリプションが 1 つだけの場合、このフィールドは空になります。 |
| **場所** | Azure の場所を指定します。 |

  アドレス空間とサブネット アドレス範囲は、この表とは異なる場合があります。 サブスクリプションによっては、使用できるアドレス空間とそれに対応するサブネット アドレス範囲が Azure によって指定されます。 アドレス空間が足りない場合は、異なるサブスクリプションを使用してください。 

1.  **[作成]**

  ![Configure Virtual Network](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/06-configurevirtualnetwork.png)

新しいネットワークが作成されると、画面がポータル ダッシュボードに切り替わって通知が表示されます。

### <a name="create-the-second-subnet"></a>2 つ目のサブネットの作成

この時点で仮想ネットワークには Subnet-1 というサブネットが 1 つ存在します。 このサブネットはドメイン コントローラーが使用します。 SQL Server には、 **Subnet-2**というもう 1 つのサブネットを使用します。 次の手順に従って Subnet-2 を構成します。

1. ダッシュボードで、先ほど作成したリソース グループ ( **SQL-HA-RG**) をクリックします。 このリソース グループ内のネットワークを **[リソース]**から探します。

 **SQL-HA-RG** が表示されない場合は、**[リソース グループ]** をクリックし、リソース グループ名をフィルターで検索してください。

1. リソースの一覧で **[autoHAVNET]** をクリックします。 ネットワークの構成ブレードが開きます。

1. **autoHAVNET** 仮想ネットワークの *[すべての設定]* をクリックします。

1. **[設定]** ブレードで **[サブネット]** をクリックします。

  既に作成してあるサブネットに注目してください。 

  ![Configure Virtual Network](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/07-addsubnet.png)

1. 2 つ目のサブネットを作成します。 **[+ サブネット]**をクリックします。 

  **[サブネットの追加]** ブレードで、**[名前]** に「**subnet-2**」と入力してサブネットを構成します。 有効な **アドレス範囲**が Azure によって自動的に指定されます。 このアドレス範囲に含まれるアドレスが 10 個以上あることを確認してください。 運用環境では通常、さらに多くのアドレスが必要になります。 

1. **[OK]**をクリックします。
 
![Configure Virtual Network](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/08-configuresubnet.png)
  
以下の表は、仮想ネットワークと 2 つのサブネットの構成設定をまとめたものです。

| **フィールド** | 値 |
| ----- | ----- |
| **名前** | **autoHAVNET** |
| **アドレス空間** | サブスクリプションで使用できるアドレス空間によって異なります。 標準的な値は 10.0.0.0/16 です。 |
| **サブネット名** | **Subnet-1** |
| **サブネットのアドレス範囲** | サブスクリプションで使用できるアドレス範囲によって異なります。 標準的な値は 10.0.0.0/24 です。 |
| **サブネット名** | **Subnet-2** |
| **サブネットのアドレス範囲** | サブスクリプションで使用できるアドレス範囲によって異なります。 標準的な値は 10.0.1.0/24 です。 |
| **サブスクリプション** | 使用するサブスクリプションを指定します。 |
| **リソース グループ** | **SQL-HA-RG** |
| **場所** | リソース グループと同じ場所を指定します。 |

## <a name="create-availability-sets"></a>可用性セットを作成する

仮想マシンを作成する前に、可用性セットを作成する必要があります。 計画済みメンテナンスや計画外メンテナンスが発生した場合のダウンタイムは、可用性セットによって短縮できます。 Azure 可用性セットとは、リソースの論理的なグループです。このグループに基づいてリソースが物理的な障害ドメインと更新ドメインに自動的に配置されます。 可用性セットに属している仮想マシンの電源やネットワーク リソースは、障害ドメインによって確実に分離されます。 一方、可用性セットに属している複数の仮想マシンがメンテナンスによって同時に中断されることを防止する働きをするのが更新ドメインです。 [仮想マシンの可用性管理](virtual-machines-windows-manage-availability.md)

可用性セットは 2 つ必要です。 1 つ目はドメイン コントローラー用です。 2 つ目は SQL Server 用です。

可用性セットを作成するには、リソース グループに移動して **[追加]** をクリックします。 「**可用性セット**」と入力して結果をフィルター処理し、 その中の **[可用性セット]** をクリックして、 **[作成]**をクリックします。

次の表のパラメーターに従って 2 つの可用性セットを構成します。

| **フィールド** | ドメイン コントローラーの可用性セット | SQL Server の可用性セット |
| ----- | ----- | ----- |
| **名前** | adAvailabilitySet | sqlAvailabilitySet|
| **[リソース グループ]** | SQL-HA-RG | SQL-HA-RG |
| **障害ドメイン** | 3 | 3 |
| **更新ドメイン** | 5 | 3 |

可用性セットを作成したら、Azure ポータルでリソース グループに戻ります。

## <a name="create-domain-controllers"></a>ドメイン コントローラーを作成する

ネットワーク、サブネット、可用性セット、インターネット接続ロード バランサーを作成した後、 次に行う作業は、ドメイン コントローラーに使用する仮想マシンの作成です。

### <a name="create-the-virtual-machines-for-the-domain-controllers"></a>ドメイン コントローラーに使用する仮想マシンの作成

ドメイン コントローラーを作成して構成するために、 **SQL-HA-RG** リソース グループに戻ります。

1. [追加] をクリックします。 **[すべて]** ブレードが開きます。

1. 「 **Windows Server 2012 R2 Datacenter**」と入力します。 

1. **[Windows Server 2012 R2 Datacenter]**をクリックします。 **[Windows Server 2012 R2 Datacenter]** ブレードでデプロイ モデルが **[Resource Manager]** に設定されていることを確認し、**[作成]** をクリックします。 **[仮想マシンの作成]** ブレードが開きます。 

上記の手順を 2 回完了し、2 つの仮想マシンを作成します。 2 つの仮想マシンには、次のように名前を付けます。

- ad-primary-dc
- ad-secondary-dc

 >[AZURE.NOTE] **ad-secondary-dc** は Active Directory ドメイン サービスの高可用性を確保するためのオプション コンポーネントです。 

次の表に、この 2 つのマシンの設定を示します。

| **フィールド** | 値 
| ----- | ---- 
| **ユーザー名** | DomainAdmin
| **パスワード** | Contoso!0000 |
| **サブスクリプション** | *該当するサブスクリプション* |
| **[リソース グループ]** | SQL-HA-RG |
| **場所** | *該当する場所* 
| **サイズ** | D1_V2 (Standard)
| **ストレージの種類** | standard
| **ストレージ アカウント** | *自動的に作成*
| **Virtual Network** | autoHAVNET
| **サブネット** | Subnet-1
| **パブリック IP アドレス** | *VM と同じ名前*
| **ネットワーク セキュリティ グループ** | *VM と同じ名前*
| **診断** | 有効
| **診断ストレージ アカウント** | *自動的に作成*
| **可用性セット** | adAvailabilitySet

>[AZURE.NOTE] VM 上の可用性セットを作成後に変更することはできません。

Azure で仮想マシンが作成されます。

仮想マシンの作成後、ドメイン コントローラーを構成します。

### <a name="configure-the-domain-controller"></a>ドメイン コントローラーの構成

次の手順では、 **ad-primary-dc** マシンを corp.contoso.com のドメイン コントローラーとして構成します。

1. ポータルで **SQL-HA-RG** リソース グループを開き、**ad-primary-dc** マシンを選択します。 **[ad-primary-dc]** ブレードで **[接続]** をクリックし、リモート デスクトップ アクセス用の RDP ファイルを開きます。

    ![仮想マシンへの接続](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/20-connectrdp.png)

1. 構成した管理者アカウント (**\DomainAdmin**) とパスワード (**Contoso!0000**) でログインします。

1. 既定では、 **[サーバー マネージャー]** ダッシュボードが表示されます。

1. ダッシュボードの **[役割と機能の追加]** リンクをクリックします。

    ![サーバー エクスプローラー、ロールの追加](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784623.png)

1. **[サーバーの役割]** セクションが表示されるまで **[次へ]** を選択します。

1. **[Active Directory Domain Services]** と **[DNS サーバー]** という役割を選択します。 メッセージが表示されたら、これらの役割に必要なその他の機能を追加します。

    >[AZURE.NOTE] Windows から、静的 IP アドレスがないという警告が出されます。 構成をテストしている場合は、[続行] をクリックします。 運用シナリオの場合は、Azure ポータルで IP アドレスを静的アドレスに設定するか、 [PowerShell を使用して、ドメイン コントローラー コンピューターの静的 IP アドレスを設定](./virtual-network/virtual-networks-reserved-private-ip.md)してください。

    ![ロールの追加の図表](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784624.png)

1. **[確認]** セクションが表示されるまで **[次へ]** をクリックします。 **[必要に応じて対象サーバーを自動的に再起動する]** チェック ボックスをオンにします。

1. **[インストール]**をクリックします。

1. 機能のインストールが完了したら、 **[サーバー マネージャー]** ダッシュボードに戻ります。

1. 左側のウィンドウで新しい **[AD DS]** オプションを選択します。

1. 黄色の警告バーにある **[その他]** リンクをクリックします。

    ![DNS サーバー VM の AD DS の図表](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784625.png)

1. **[すべてのサーバー タスクの詳細]** ダイアログ ボックスの **[操作]** 列で、**[このサーバーをドメイン コントローラーに昇格する]** をクリックします。

1. **Active Directory ドメイン サービスの構成ウィザード**で、次の値を使用します。

| **ページ** |Setting|
|---|---|
|**デプロイ構成** |**[新しいフォレストの追加]** = 選択<br/>**[ルート ドメイン名]** = corp.contoso.com|
|**ドメイン コントローラー オプション**|**[DSRM パスワード]** = Contoso!0000<br/>**[パスワードの確認]** = Contoso!0000|

1. **[次へ]** をクリックして、ウィザード内の他のページを進めます。 **[前提条件のチェック]** ページで、"**すべての前提条件のチェックに合格しました**" というメッセージが表示されることを確認します。 関連する警告メッセージを確認する必要がありますが、インストールは続行できます。

1. **[インストール]**をクリックします。 **ad-primary-dc** 仮想マシンが自動的に再起動します。

### <a name="configure-the-second-domain-controller"></a>2 番目のドメイン コントローラーの構成

プライマリ ドメイン コントローラーの再起動後、2 つ目のドメイン コントローラーの構成を行うことができます。 これは高可用性を確保するための任意の手順です。 この手順を行うには、ドメイン コントローラーのプライベート IP アドレスを把握しておく必要があります。 Azure ポータルで確認してください。 次の手順に従って 2 つ目のドメイン コントローラーを構成します。

1. **ad-primary-dc** マシンにログインします。 

1. リモート デスクトップを開き、IP アドレスでセカンダリ ドメイン コントローラーに接続します。 セカンダリ ドメイン コントローラーの IP アドレスがわからない場合は、Azure ポータルにアクセスして、セカンダリ ドメイン コントローラーのネットワーク インターフェイスに割り当てられているアドレスを確認してください。 

1. 優先 DNS サーバーのアドレスをドメイン コントローラーのアドレスに変更します。 

1. プライマリ ドメイン コントローラー (**ad-primary-dc**) への RDP ファイルを起動し、構成済みの管理者アカウント (**BUILTIN\DomainAdmin**) とパスワード (**Contoso!0000**) で VM にログインします。

1. プライマリ ドメイン コントローラーからリモート デスクトップを起動し、 **ad-secondary-dc** に IP アドレスで接続します。 使用するアカウントとパスワードは同じです。

1. ログインすると、 **[サーバー マネージャー]** ダッシュボードが表示されます。 左側のウィンドウで **[ローカル サーバー]** をクリックします。

1. **[IPv4 アドレス (DHCP により割り当て)、IPv6 (有効)]** リンクを選択します。

1. **[ネットワーク接続]** ウィンドウで、ネットワーク アイコンを選択します。

    ![VM 優先 DNS サーバーの変更](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784629.png)

1. コマンド バーの **[この接続の設定を変更する]** をクリックします (ウィンドウのサイズによっては、右向き二重矢印をクリックしないと、このコマンドが表示されない場合があります)。

1. **[インターネット プロトコル バージョン 4 (TCP/IPv4)]** を選択し、[プロパティ] をクリックします。

1. [次の DNS サーバーのアドレスを使う] を選択し、プライマリ ドメイン コントローラーのアドレスを **[優先 DNS サーバー]**に指定します。

1. これは、Azure 仮想ネットワークの subnet-1 サブネット内の VM ( **ad-primary-dc**) に割り当てられているアドレスです。 **ad-primary-dc** の IP アドレスを確認するには、コマンド プロンプトから **nslookup ad-primary-dc** を使用します。

    ![NSLOOKUP を使用して DC の IP アドレスを検索](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC664954.png)

 >[AZURE.NOTE] DNS の設定後、メンバー サーバーへの RDP セッションが失われる場合があります。 その場合は、Azure ポータルから VM を再起動してください。


1. **[OK]**、**[閉じる]** の順にクリックして変更を適用します。 これで、VM を **corp.contoso.com**に参加させることができるようになりました。

1. 1 つ目のドメイン コントローラーを作成したときと同じ手順を繰り返します。ただし **Active Directory Domain Services 構成ウィザード**には、次の値を使用してください。

|ページ|Setting|
|---|---|
|**デプロイ構成**|**[既存のドメインにドメイン コントローラーを追加する]** = 選択<br/>**[ルート]** = corp.contoso.com|
|**ドメイン コントローラー オプション**|**[DSRM パスワード]** = Contoso!0000<br/>**[パスワードの確認]** = Contoso!0000|


### <a name="configure-domain-accounts"></a>ドメイン アカウントの構成

次の手順では、後で使用するために Active Directory (AD) アカウントを構成します。

1. 再度 **ad-primary-dc** マシンにログインします。

1. **[サーバー マネージャー]** で、**[ツール]** を選択し、**[Active Directory 管理センター]** をクリックします。

    ![[Active Directory 管理センター]](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784626.png)

1.  **[Active Directory 管理センター]** select **[corp (ローカル)]** を選択します。

1. 右側の **[タスク]** ウィンドウで、**[新規]** を選択し、**[ユーザー]** をクリックします。 次の設定を使用します。

|設定|値|
|---|---|
|**名**|[インストール]|
|**ユーザー SAM アカウント名**|[インストール]|
|**パスワード**|Contoso!0000|
|**パスワードの確認**|Contoso!0000|
|**その他のパスワード オプション**|オン|
|**パスワードを無期限にする**|オン|

1. **[OK]** をクリックして **Install** ユーザーを作成します。 このアカウントは、フェールオーバー クラスターと可用性グループの構成に使用します。

1. 同じ手順を使用して、さらに 2 つのユーザー (**CORP\SQLSvc1** と **CORP\SQLSvc2**) を作成します。 これらのアカウントは SQL Server サービスで使用されます。 次に、Windows Server フェールオーバー クラスタリング (WSFC) を構成するために必要なアクセス許可を **CORP\Install** に付与します。

1. **[Active Directory 管理センター]** で、左側のウィンドウの **[corp (ローカル)]** を選択します。 その後、右側の **[タスク]** ウィンドウで、**[プロパティ]** をクリックします。

    ![CORP ユーザー プロパティ](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784627.png)

1. **[拡張機能]** を選択し、**[セキュリティ]** タブの **[詳細設定]** をクリックします。

1. **[corp のセキュリティの詳細設定]** ウィンドウで、 **[追加]**をクリックします。

1. **[プリンシパルの選択]**をクリックします。 次に、「**CORP\Install**」を検索します。 **[OK]**をクリックします。

1. **[すべてのプロパティの読み取り]** アクセス許可と **[コンピューター オブジェクト作成]** アクセス許可を選択します。

    ![CORP ユーザー権限](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784628.png)

1. **[OK]** をクリックし、もう一度 **[OK]** をクリックします。 corp のプロパティ ウィンドウを閉じます。

これで Active Directory オブジェクトとユーザー オブジェクトの構成が終了したので、2 つの SQL Server VM と 1 つの監視サーバー VM を作成します。 その後、3 つすべてをドメインに参加させます。

## <a name="create-sql-servers"></a>SQL Server を作成する

###<a name="create-and-configure-the-sql-server-vms"></a>SQL Server VM の作成と構成

次に、2 つの SQL Server VM と 1 つの WSFC クラスター ノードの合計 3 つの VM を作成します。 それぞれの VM を作成するには、**SQL-HA-RG** リソース グループに戻って **[追加]** をクリックし、適切なギャラリー項目 (**仮想マシン**) を検索して、**[ギャラリーから]** をクリックします。 その後、次の表のテンプレートを使用すると、VM の作成に役立ちます。

|ページ|VM1|VM2|VM3|
|---|---|---|---|
|適切なギャラリー項目の選択|**Windows Server 2012 R2 Datacenter**|**SQL Server 2014 SP1 Enterprise on Windows Server 2012 R2**|**SQL Server 2014 SP1 Enterprise on Windows Server 2012 R2**|
| [Virtual machine configuration (仮想マシンの構成)] の **[基本]** | **[名前]** = cluster-fsw<br/>**[ユーザー名]** = DomainAdmin<br/>**パスワード** = Contoso!0000<br/>**[サブスクリプション]** = 自分のサブスクリプション<br/>**[リソース グループ]** = SQL-HA-RG<br/>**[場所]** = 該当する Azure の場所 | **[名前]** = sqlserver-0<br/>**[ユーザー名]** = DomainAdmin<br/>**パスワード** = Contoso!0000<br/>**[サブスクリプション]** = 自分のサブスクリプション<br/>**[リソース グループ]** = SQL-HA-RG<br/>**[場所]** = 該当する Azure の場所 | **[名前]** = sqlserver-1<br/>**[ユーザー名]** = DomainAdmin<br/>**パスワード** = Contoso!0000<br/>**[サブスクリプション]** = 自分のサブスクリプション<br/>**[リソース グループ]** = SQL-HA-RG<br/>**[場所]** = 該当する Azure の場所 |
|[Virtual machine configuration (仮想マシンの構成)] の **[サイズ]** |DS1 (1 コア、3.5 GB のメモリ)|**[サイズ]** = DS 2 (2 コア、7 GB のメモリ)|**[サイズ]** = DS 2 (2 コア、7 GB のメモリ)|
|[Virtual machine configuration (仮想マシンの構成)] の **[設定]**|**[記憶域]** = Premium (SSD)<br/>**[ネットワーク サブネット]** = autoHAVNET<br/>**[ストレージ アカウント]**: 自動的に生成されたストレージ アカウントを使用<br/>**[サブネット]** = subnet-2(10.1.1.0/24)<br/>**[パブリック IP アドレス]** = なし<br/>**[ネットワーク セキュリティ グループ]** = なし<br/>**[監視診断]** = 有効<br/>**[診断ストレージ アカウント]** = 自動的に生成されたストレージ アカウントを使用<br/>**[可用性セット]** = sqlAvailabilitySet<br/>|**[記憶域]** = Premium (SSD)<br/>**[ネットワーク サブネット]** = autoHAVNET<br/>**[ストレージ アカウント]**: 自動的に生成されたストレージ アカウントを使用<br/>**[サブネット]** = subnet-2(10.1.1.0/24)<br/>**[パブリック IP アドレス]** = なし<br/>**[ネットワーク セキュリティ グループ]** = なし<br/>**[監視診断]** = 有効<br/>**[診断ストレージ アカウント]** = 自動的に生成されたストレージ アカウントを使用<br/>**[可用性セット]** = sqlAvailabilitySet<br/>|**[記憶域]** = Premium (SSD)<br/>**[ネットワーク サブネット]** = autoHAVNET<br/>**[ストレージ アカウント]**: 自動的に生成されたストレージ アカウントを使用<br/>**[サブネット]** = subnet-2(10.1.1.0/24)<br/>**[パブリック IP アドレス]** = なし<br/>**[ネットワーク セキュリティ グループ]** = なし<br/>**[監視診断]** = 有効<br/>**[診断ストレージ アカウント]** = 自動的に生成されたストレージ アカウントを使用<br/>**[可用性セット]** = sqlAvailabilitySet<br/>
|[Virtual machine configuration (仮想マシンの構成)] の **[SQL Server の設定]**|適用不可|**[SQL 接続]** = プライベート (仮想ネットワーク内)<br/>**[ポート]** = 1433<br/>**[SQL 認証]** = 無効<br/>**[ストレージの構成]** = 全般<br/>**[自動修正]**= 日曜日 2:00<br/>**[自動バックアップ]** = 無効</br>**[Azure Key Vault の統合]** = 無効|**[SQL 接続]** = プライベート (仮想ネットワーク内)<br/>**[ポート]** = 1433<br/>**[SQL 認証]** = 無効<br/>**[ストレージの構成]** = 全般<br/>**[自動修正]**= 日曜日 2:00<br/>**[自動バックアップ]** = 無効</br>**[Azure Key Vault の統合]** = 無効|

<br/>

>[AZURE.NOTE] 前の構成は、Standard レベルの仮想マシンを想定しています。Basic レベルのマシンでは、可用性グループ リスナーに必要なエンドポイントの負荷分散がサポートされません。 また、ここで示されるマシンのサイズは、Azure VM での可用性グループのテストに対応しています。 運用時のワークロードに対して最適なパフォーマンスを得る方法については、「[Azure Virtual Machines における SQL Server のパフォーマンスのベスト プラクティス](virtual-machines-windows-sql-performance.md)」の SQL Server マシンのサイズと構成に関する推奨事項を参照してください。



3 つの VM が完全にプロビジョニングされたら、その 3 つの VM を **corp.contoso.com** ドメインに参加させて、それらに対する管理者権限を CORP\Install に付与する必要があります。

Azure の各 VM の仮想 IP アドレスは、この後の手順で必要となるため、ここでメモしておいてください。 各サーバーの IP アドレスを取得するには、 Azure SQL-HA-RG リソース グループで **[autohaVNET]** リソースをクリックします。 **[autohaVNET]** ブレードに、ネットワーク内の各マシンの IP アドレスが表示されます。

以下のデバイスの IP アドレスをメモしてください。 

| VM ロール | デバイス | IP アドレス
| ----- | ----- | -----
| プライマリ ドメイン コントローラー | ad-primary-dc |
| セカンダリ ドメイン コントローラー | ad-secondary-dc |
| クラスター ファイル共有監視 | cluster-fsw |
| SQL Server | sqlserver-0 | 
| SQL Server | sqlserver-1 | 

各 VM の DNS サービスは、これらのアドレスを使って構成します。 3 つの VM それぞれで、次の手順を実行します。


1. まず、各メンバー サーバーの優先 DNS サーバーのアドレスを変更します。 

1. プライマリ ドメイン コントローラー (**ad-primary-dc**) への RDP ファイルを起動し、構成済みの管理者アカウント (**BUILTIN\DomainAdmin**) とパスワード (**Contoso!0000**) で VM にログインします。

1. プライマリ ドメイン コントローラーからリモート デスクトップを起動し、 **sqlserver-0** に IP アドレスで接続します。 使用するアカウントとパスワードは同じです。

1. ログインすると、 **[サーバー マネージャー]** ダッシュボードが表示されます。 左側のウィンドウで **[ローカル サーバー]** をクリックします。

1. **[IPv4 アドレス (DHCP により割り当て)、IPv6 (有効)]** リンクを選択します。

1. **[ネットワーク接続]** ウィンドウで、ネットワーク アイコンを選択します。

    ![VM 優先 DNS サーバーの変更](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784629.png)

1. コマンド バーの **[この接続の設定を変更する]** をクリックします (ウィンドウのサイズによっては、右向き二重矢印をクリックしないと、このコマンドが表示されない場合があります)。

1. **[インターネット プロトコル バージョン 4 (TCP/IPv4)]** を選択し、[プロパティ] をクリックします。

1. [次の DNS サーバーのアドレスを使う] を選択し、プライマリ ドメイン コントローラーのアドレスを **[優先 DNS サーバー]**に指定します。

1. これは、Azure 仮想ネットワークの subnet-1 サブネット内の VM ( **ad-primary-dc**) に割り当てられているアドレスです。 **ad-primary-dc** の IP アドレスを確認するには、コマンド プロンプトから **nslookup ad-primary-dc** を使用します。

    ![NSLOOKUP を使用して DC の IP アドレスを検索](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC664954.png)

 >[AZURE.NOTE] DNS の設定後、メンバー サーバーへの RDP セッションが失われる場合があります。 その場合は、Azure ポータルから VM を再起動してください。


1. **[OK]**、**[閉じる]** の順にクリックして変更を適用します。 これで、VM を **corp.contoso.com**に参加させることができるようになりました。

1. **[ローカル サーバー]** ウィンドウに戻り、**[ワークグループ]** のリンクをクリックします。

1. **[コンピューター名]** セクションで **[変更]** をクリックします。

1. **[ドメイン]** チェック ボックスをオンにし、テキスト ボックスに「**corp.contoso.com**」を入力します。 **[OK]**をクリックします。

1. **[Windows セキュリティ]** ポップアップ ダイアログで、既定のドメイン管理者の資格情報であるアカウント (**CORP\DomainAdmin**) とパスワード (**Contoso!0000**) を指定します。

1. "corp.contoso.com ドメインへようこそ" のメッセージが表示されたら、 **[OK]**をクリックします。

1. **[閉じる]** をクリックし、ポップアップ ダイアログの **[今すぐ再起動]** をクリックします。

1. ファイル共有監視サーバーと各 SQL Server に対して以上の手順を繰り返します。 

### <a name="add-the-corp\install-user-as-an-administrator-on-each-cluster-vm:"></a>管理者として Corp\Install ユーザーを各クラスター VM に追加する

1. VM が再起動されるまで待って、プライマリ ドメイン コントローラーからもう一度 RDP ファイルを起動し、**BUILTIN\DomainAdmin** アカウントを使用して **sqlserver-0** にログインします。

1. **[サーバー マネージャー]** で、**[ツール]** を選択し、**[コンピューターの管理]** をクリックします。

    ![[コンピューターの管理]](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784630.png)

1. **[コンピューターの管理]** ウィンドウで、**[ローカル ユーザーとグループ]** を展開し、**[グループ]** を選択します。

1. **[Administrators]** グループをダブルクリックします。

1. **[Administrators のプロパティ]** ダイアログ ボックスで、**[追加]** をクリックします。

1. ユーザーに「**CORP\Install**」を入力し、**[OK]** をクリックします。 資格情報の入力を求められたら、**DomainAdmin** アカウントと **Contoso!0000** パスワードを使用します。

1. **[OK]** をクリックして **[管理者のプロパティ]** ダイアログ ボックスを閉じます。

1. 以上の手順を **sqlserver-1** と **cluster-fsw** で繰り返します。

## <a name="create-the-cluster"></a>クラスターを作成する

### <a name="add-the-**failover-clustering**-feature-to-each-cluster-vm."></a>クラスター VM に **フェールオーバー クラスタリング** 機能を追加します。

1. RDP で **sqlserver-0**に接続します。

1. **[サーバー マネージャー]** ダッシュボードの **[役割と機能の追加]** をクリックします。

1. **役割と機能の追加ウィザード**で、**[機能]** ページが表示されるまで **[次へ]** をクリックします。

1. **[フェールオーバー クラスタリング]**を選択します。 メッセージが表示されたら、その他の依存する機能を追加します。

    ![フェールオーバー クラスタリング機能を VM に追加](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784631.png)

1. **[次へ]** をクリックして、**[確認]** ページの **[インストール]** をクリックします。

1. **フェールオーバー クラスタリング**機能のインストールが完了したら、**[閉じる]** をクリックします。

1. VM からログアウトします。

1. このセクションの手順を **sqlserver-1** と **cluster-fsw** で繰り返します。

これで SQL Server VM がプロビジョニングされ、実行されている状態になりましたが、これらは SQL Server と共に既定のオプションでインストールされています。

### <a name="create-the-wsfc-cluster"></a>WSFC クラスターの作成

このセクションでは、可用性グループをホストする WSFC クラスターを作成します。 ここまでに、WSFC クラスター内の 3 つの VM に対して、次の操作を行いました。

- Azure で完全にプロビジョニングする

- VM をドメインに参加させる

- ローカルの Administrators グループに **CORP\Install** を追加する

- フェールオーバー クラスタリング機能を追加する

これらはすべて、WSFC クラスターに参加させるための各 VM の前提条件です。

また、Azure Virtual Network の動作は、オンプレミス ネットワークとは異なることに注意してください。 クラスターは、次の順序で作成する必要があります。

1. いずれかのノード (**sqlserver-0**) に単一ノード クラスターを作成します。

1. クラスターの IP アドレスを **sqlsubnet** 内の未使用の IP アドレスに変更します。

1. クラスター名をオンラインにします。

1. その他のノード (**sqlserver-1** と **cluster-fsw**) を追加します。

次の手順に従ってクラスターを構成します。

1. **sqlserver-0** の RDP ファイルを起動し、ドメイン アカウント **CORP\Install** を使用してログインします。

1. **[サーバー マネージャー]** ダッシュボードで、**[ツール]** を選択し、**[フェールオーバー クラスター マネージャー]** をクリックします。

1. 次に示すように、左側のウィンドウで、**[フェールオーバー クラスター マネージャー]** を右クリックし、**[クラスターの作成]** をクリックします。

    ![クラスターの作成](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784632.png)

1. クラスターの作成ウィザードの各ページで、次の設定を使用して、単一ノード クラスターを作成します。

|ページ|[設定]|
|---|---|
|開始する前に|既定値を使用|
|サーバーの選択|**[サーバー名を入力してください]** に「**sqlserver-0**」と入力し、**[追加]** をクリックします|
|検証の警告|**[いいえ、このクラスターに Microsoft のサポートは必要ありませんので、検証テストを実行しません。[次へ] をクリックして、クラスターの作成を続行します。]** を選択します。|
|クラスター管理用のアクセス ポイント|**[クラスター名]** に「**Cluster1**」と入力します。|
|[次へ]|記憶域スペースを使用している場合を除き、既定値を使用します。 この表の次の注を参照してください。|

>[AZURE.NOTE] 複数のディスクを記憶域プールにグループ化する[記憶域スペース](https://technet.microsoft.com/library/hh831739)を使用している場合は、**[確認]** ページの **[使用可能な記憶域をすべてクラスターに追加する]** チェック ボックスをオフにする必要があります。 このチェック ボックスをオフにしないと、クラスタリング処理中に Windows によって仮想ディスクがデタッチされます。 その結果、記憶域スペースがクラスターから削除され、PowerShell を使用して再アタッチされるまで、仮想ディスクはディスク マネージャーやエクスプローラーに表示されなくなります。

以上でクラスターの作成は完了です。構成を確認して残りのノードを追加しましょう。 

1. 中央のウィンドウで、下方法へスクロールして **[クラスター コア リソース]** セクションを表示し、**[名前: Clutser1]** の詳細を展開します。 **[名前]** と **[IP アドレス]** リソースの両方が **[失敗]** 状態で表示されます。 クラスターにコンピューター自体と同じ IP アドレスが割り当てられていて、アドレスが重複するため、IP アドレス リソースをオンラインにすることができません。

1. 失敗した **IP アドレス** リソースを右クリックし、**[プロパティ]** をクリックします。

    ![クラスターのプロパティ](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784633.png)

1. **[静的 IP アドレス]** を選択し、subnet-2 から使用できるアドレスを [アドレス] ボックスで指定します。 次に、 **[OK]**をクリックします

1. **[クラスター コア リソース]** セクションで、**[名前: Cluster1]** を右クリックして、**[オンラインにする]** をクリックします。 両方のリソースがオンラインになるまで待ちます。 クラスター名リソースがオンラインになると、新しい AD コンピューター アカウントで DC サーバーが更新されます。 この AD アカウントは、後で可用性グループのクラスター化サービスを実行するときに使用します。

1. 最後に、残りのノードをクラスターに追加します。 次に示すように、ブラウザー ツリーで **[Cluster1.corp.contoso.com]** を右クリックして、**[ノードの追加]** をクリックします。

    ![クラスターにノードを追加](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC784634.png)

1. **ノードの追加ウィザード**で、**[次へ]** をクリックします。 **[サーバーの選択]** ページで、**sqlserver-1** と **cluster-fsw** を一覧に追加します。それには、**[サーバー名を入力してください]** にサーバー名を入力し、**[追加]** をクリックします。 完了したら、**[次へ]** をクリックします。

1. **[検証の警告]** ページで **[いいえ]** をクリックします (実際の運用時には、検証テストを実施する必要があります)。 次に、 **[次へ]**をクリックします。

1. **[確認]** ページで **[次へ]** をクリックしてノードを追加します。

    >[AZURE.WARNING] 複数のディスクを記憶域プールにグループ化する [記憶域スペース](https://technet.microsoft.com/library/hh831739)を使用している場合は、 **[使用可能な記憶域をすべてクラスターに追加する]** チェック ボックスをオフにする必要があります。 このチェック ボックスをオフにしないと、クラスタリング処理中に仮想ディスクがデタッチされます。 その結果、記憶域スペースがクラスターから削除され、PowerShell を使用して再アタッチされるまで、仮想ディスクはディスク マネージャーやエクスプローラーにも表示されなくなります。

1. ノードがクラスターに追加されたら、 **[完了]**をクリックします。 フェールオーバー クラスター マネージャーでは、クラスターに 3 つのノードがあることが示され、その 3 つのノードが **[ノード]** コンテナーの一覧に表示されます。

1. リモート デスクトップ セッションからログアウトします。

## <a name="configure-availability-groups"></a>可用性グループを構成する

このセクションでは、**sqlserver-0** と **sqlserver-1** の両方で、次の操作を行います。

- 既定の SQL Server インスタンスの sysadmin ロールとして **CORP\Install** を追加する

- SQL Server、データベース ミラーリング エンドポイント、および Azure ロード バランサーのプローブ ポート用に、ファイアウォールを開放して SQL Server へのリモート アクセスを許可する 

- 可用性グループ機能を有効にする

- SQL Server サービス アカウントを **CORP\SQLSvc1** と **CORP\SQLSvc2** にそれぞれ変更する

上記の操作は、任意の順序で実行できます。 ただし、次の手順はこの順序どおりに実行します。 **sqlserver-0** と **sqlserver-1** の両方に対してこの手順を実行してください。

### <a name="add-installation-account-as-sysadmin-fixed-server-role-on-each-sql-server"></a>インストール アカウントを sysadmin 固定サーバー ロールとして各 SQL Server に追加する

1. VM のリモート デスクトップ セッションからログアウトしていない場合は、今すぐログアウトしてください。

1. **sqlserver-0** と **sqlserver-1** の RDP ファイルを起動し、**BUILTIN\DomainAdmin** としてログインします。

1. **SQL Server Management Studio** を起動し、**CORP\Install** を **sysadmin** ロールとして既定の SQL Server インスタンスに追加します。 **オブジェクト エクスプローラー**で、**[ログイン]** を右クリックし、**[新しいログイン]** をクリックします。

1. **[ログイン名]** に「**CORP\Install**」と入力します。

1. **[サーバーの役割]** ページで、**[sysadmin]** を選択します。 次に、 **[OK]**をクリックします 作成されたログインは、**オブジェクト エクスプローラー**で **[ログイン]** を展開すると表示されます。

### <a name="open-the-firewall-for-remote-access-to-sql-server-and-the-probe-port-on-each-sql-server"></a>SQL Server と各 SQL Server 上のプローブ ポートにリモート アクセスできるようファイアウォールを開放する

この例では、各 SQL Server に対しファイアウォールの規則が 2 つ必要となります。 SQL Server に対する入力方向のアクセスを許可する規則と、ロード バランサーとリスナーに対する入力方向のアクセスを許可する規則です。 

1. 次に、SQL Server のファイアウォール規則を作成します。 **スタート**画面から **[セキュリティが強化された Windows ファイアウォール]** を開きます。

1. 左側のウィンドウで、 **[受信の規則]**を選択します。 右側のウィンドウで、 **[新しい規則]**をクリックします。

1. **[規則の種類]** ページで、**[プログラム]** を選択して **[次へ]** をクリックします。

1. **[プログラム]** ページで、**[このプログラムのパス]** を選択し、テキスト ボックスに「**%ProgramFiles%\Microsoft SQL Server\MSSQL12.MSSQLSERVER\MSSQL\Binn\sqlservr.exe**」と入力します (SQL Server 2012 を使用してこの手順を実行している場合、SQL Server のディレクトリは **MSSQL11.MSSQLSERVER** になります)。 その後、 **[次へ]**をクリックします。

1. **[操作]** ページで、**[接続を許可する]** を選択したまま、**[次へ]** をクリックします。

1. **[プロファイル]** ページで、既定の設定をそのまま使用し、**[次へ]** をクリックします。

1. **[名前]** ページで、**[名前]** ボックスに規則の名前 (**SQL Server (プログラム規則)** など) を指定し、**[完了]** をクリックします。

1. プローブ ポート用に、もう 1 つ入力方向のファイアウォールの規則を作成します。 このチュートリアルで使用する TCP ポート 59999 に対する入力方向の規則となります。 この規則に **SQL Server Listener**という名前を付けます。


2 つの SQL Server に対してすべての手順を実行してください。

### <a name="enable-availability-groups-feature-on-each-sql-server"></a>各 SQL Server で可用性グループ機能を有効にする

以下の手順は、両方の SQL Server に対して実行してください。 

1. 次に、 **[AlwaysOn 可用性グループ]** 機能を有効にします。 **スタート**画面から **SQL Server 構成マネージャー**を起動します。

1. ブラウザー ツリーで、**[SQL Server のサービス]** をクリックし、**[SQL Server (MSSQLSERVER)]** サービスを右クリックして、**[プロパティ]** をクリックします。

1. 次に示すように、**[AlwaysOn 高可用性]** タブをクリックし、**[AlwaysOn 可用性グループを有効にする]** をオンにして、**[適用]** をクリックします。 ポップアップ ダイアログで **[OK]** をクリックします。プロパティ ウィンドウはまだ閉じないでください。 サービス アカウントを変更した後に SQL Server サービスを再起動します。

    ![AlwaysOn 可用性グループの有効化](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665520.gif)

### <a name="set-the-sql-server-service-account-on-each-sql-server"></a>SQL Server サービス アカウントを各 SQL Server に設定する

以下の手順は、両方の SQL Server に対して実行してください。

1. 次に、SQL Server サービス アカウントを変更します。 **[ログオン]** タブをクリックし、**[アカウント名]** に「**CORP\SQLSvc1**」(**sqlserver-0** の場合) または「**CORP\SQLSvc2**」(**sqlserver-1** の場合) と入力します。その後、パスワードを入力および確認入力し、**[OK]** をクリックします。

1. ポップアップ ウィンドウで **[はい]** をクリックして、SQL Server サービスを再起動します。 SQL Server サービスが再起動されると、プロパティ ウィンドウで行った変更が有効になります。

1. VM からログアウトします。

### <a name="create-the-availability-group"></a>可用性グループの作成

これで、可用性グループを構成できるようになりました。 この後の操作の概要は以下のとおりです。

- **sqlserver-0** に新しいデータベース (**MyDB1**) を作成する

- データベースの完全バックアップとトランザクション ログ バックアップの両方を作成する

- **NORECOVERY** オプションを使用して完全バックアップとログ バックアップを **sqlserver-1** に復元する

- 同期コミット、自動フェールオーバー、読み取り可能なセカンダリのレプリカを含む可用性グループ (**AG1**) を作成する

### <a name="create-the-mydb1-database-on-sqlserver-0:"></a>sqlserver-0 に MyDB1 データベースを作成する

1. **sqlserver-0** と **sqlserver-1** のリモート デスクトップ セッションからまだログアウトしていない場合は、ここでログアウトしてください。

1. **sqlserver-0** の RDP ファイルを起動し、**CORP\Install** としてログインします。

1. **エクスプローラー**で、**C:\** ドライブの下に **backup* というディレクトリを作成します。 このディレクトリは、データベースのバックアップと復元に使用します。

1. 次に示すように、新しいディレクトリを右クリックして **[共有]** をポイントし、**[特定のユーザー]** をクリックします。

    ![バックアップ フォルダーの作成](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665521.gif)

1. 次のように、**CORP\SQLSvc1** を追加して **[読み取り/書き込み]** アクセス許可を指定し、**CORP\SQLSvc2** を追加して **[読み取り/書き込み]** アクセス許可を指定した後、**[共有]** をクリックします。 ファイル共有プロセスが完了したら、 **[完了]**をクリックします。

    ![バックアップ フォルダーの権限を付与](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665522.gif)

1. 次に、データベースを作成します。 **[スタート]** メニューから **SQL Server Management Studio** を起動し、**[接続]** をクリックして既定の SQL Server インスタンスに接続します。

1. **オブジェクト エクスプローラー**で、**[データベース]** を右クリックし、**[新しいデータベース]** をクリックします。

1. **[データベース名]** に「**MyDB1**」と入力し、**[OK]** をクリックします。

### <a name="take-a-full-backup-of-mydb1-and-restore-it-on-sqlserver-1:"></a>MyDB1 の完全バックアップを作成して sqlserver-1 に復元する

1. 次に、データベースの完全バックアップを作成します。 **オブジェクト エクスプローラー**で、**[データベース]** を展開し、**[MyDB1]** を右クリックして **[タスク]** をポイントし、**[バックアップ]** をクリックします。

1. **[ソース]** セクションで、**[バックアップの種類]** を **[完全]** に設定しておきます。 **[バックアップ先]** セクションで、**[削除]** をクリックして、バックアップ ファイルの既定のファイル パスを削除します。

1. **[バックアップ先]** セクションで、**[追加]** をクリックします。

1. **[ファイル名]** ボックスに「**\\\\\\sqlserver-0\backup\MyDB1.bak**」と入力します。 **[OK]** をクリックし、もう一度 **[OK]** をクリックしてデータベースをバックアップします。 バックアップ操作が完了したら、もう一度 **[OK]** をクリックしてダイアログを閉じます。

1. 次に、データベースのトランザクション ログ バックアップを作成します。 **オブジェクト エクスプローラー**で、**[データベース]** を展開し、**[MyDB1]** を右クリックして **[タスク]** をポイントし、**[バックアップ]** をクリックします。

1. **[バックアップの種類]** で **[トランザクション ログ]** を選択します。 **[バックアップ先]** のファイル パスの設定を、前に指定したパスのままにし、**[OK]** をクリックします。 バックアップ操作が完了したら、もう一度 **[OK]** をクリックします。

1. 次に、 **sqlserver-1**で完全バックアップとトランザクション ログ バックアップを復元します。 **sqlserver-1** の RDP ファイルを起動し、**CORP\Install** としてログインします。 **sqlserver-0** のリモート デスクトップ セッションは、開いたままにします。

1. **[スタート]** メニューから **SQL Server Management Studio** を起動し、**[接続]** をクリックして既定の SQL Server インスタンスに接続します。

1. **オブジェクト エクスプローラー**で、**[データベース]** を右クリックし、**[データベースの復元]** をクリックします。

1. **[ソース]** セクションで **[デバイス]** を選択し、省略記号 (**…**) ボタンをクリックします。  ボタンを選択します。

1. **[バックアップ デバイスの選択]** で、**[追加]** をクリックします。

1. [バックアップ ファイルの場所] に「**\\\\\\sqlserver-0\backup**」と入力して [更新] をクリックし、[MyDB1.bak] を選択して [OK] をクリックした後、もう一度 [OK] をクリックします。 [復元するバックアップ セット] ウィンドウに完全バックアップとログ バックアップが表示されます。

1. [オプション] ページに移動し、[復旧状態] で [RESTORE WITH NORECOVERY] を選択し、[OK] をクリックしてデータベースを復元します。 復元操作が完了したら、[OK] をクリックします。

### <a name="create-the-availability-group:"></a>可用性グループを作成する

1. **sqlserver-0**のリモート デスクトップ セッションに戻ります。 次に示すように、SSMS の**オブジェクト エクスプローラー**で、**[AlwaysOn 高可用性]** を右クリックして **[新しい可用性グループ ウィザード]** をクリックします。

    ![[新しい可用性グループ ウィザード] の起動](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665523.gif)

1. **[説明]** ページで **[次へ]** をクリックします。 **[可用性グループ名の指定]** ページで、**[可用性グループ名]** に「**AG1**」と入力し、もう一度 **[次へ]** をクリックします。

    ![新しい AG ウィザード、AG 名の指定](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665524.gif)

1. **[データベースの選択]** ページで、**[MyDB1]** を選択し、**[次へ]** をクリックします。 対象とするプライマリ レプリカで完全バックアップを少なくとも 1 つは作成しているため、このデータベースは可用性グループの前提条件を満たしています。

    ![新しい AG ウィザード、データベースの選択](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665525.gif)

1. **[レプリカの指定]** ページで **[レプリカの追加]** をクリックします。

    ![新しい AG ウィザード、レプリカの指定](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665526.png)


1. **[サーバーに接続]** ダイアログが表示されます。 **[サーバー名]** に「**sqlserver-1**」と入力し、**[接続]** をクリックします。

    ![新しい AG ウィザード、サーバーに接続](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665527.png)

1. **[レプリカの指定]** ページに戻ると、**[可用性レプリカ]** の一覧に **sqlserver-1** が表示されていることがわかります。 次に示すようにレプリカを構成します。 

    ![新しい可用性グループ ウィザード、レプリカの指定 (完了)](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665528.png)

1. **[エンドポイント]** をクリックすると、この可用性グループによって使用されるデータベース ミラーリング エンドポイントが表示されます。 SQL Server の各インスタンスには、1 つのデータベース ミラーリング エンドポイントが必要です。 ウィザードでこのエンドポイント用に指定される TCP ポートに注意してください。 各サーバーで、この TCP ポートに関する受信ファイアウォール規則を作成します。
    
    構成し終わったら、 **[次へ]**をクリックします。

1. **[最初のデータの同期を選択]** ページで、**[結合のみ]** を選択し、**[次へ]** をクリックします。 データの同期は、**sqlserver-0** で完全バックアップとトランザクション バックアップを作成し、**sqlserver-1** で復元したときに既に手動で実行しています。 データベースのバックアップの作成操作と復元操作を実行しない代わりに、新しい可用性グループ ウィザードで **[完全]** を選択すると、データの同期を実行させることができます。 ただし、一部の企業で使用されている大規模なデータベースではこの方法はお勧めしません。

    ![新しい可用性グループ ウィザード、最初のデータの同期を選択](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665529.png)

1. **[検証]** ページで **[次へ]** をクリックします。 このページは、次のようになっています。 可用性グループ リスナーを構成していないため、リスナー構成に関する警告が表示されます。 このチュートリアルではリスナーを構成しないため、この警告を無視できます。 このチュートリアルでは、リスナーを後から作成します。

    ![新しい AG ウィザード、検証](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665530.gif)

1. **[概要]** ページで、**[完了]** をクリックし、新しい可用性グループが構成されるまで待ちます。 **[進行状況]** ページで **[詳細]** をクリックすると、進行状況が詳しく表示されます。 ウィザードが完了したら、**[結果]** ページを調べて、次のように、可用性グループが正常に作成されたことを確認します。その後、**[閉じる]** をクリックしてウィザードを終了します。

    ![新しい AG ウィザード、結果](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665531.png)

1. **オブジェクト エクスプローラー**で、**[AlwaysOn 高可用性]**、**[可用性グループ]** の順に展開します。 このコンテナー内に新しい可用性グループが表示されます。 **[AG1 (プライマリ)]** を右クリックして **[ダッシュボードの表示]** をクリックします。

    ![AG ダッシュボードの表示](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665532.png)

1. **AlwaysOn ダッシュボード** は次のように表示されます。 レプリカ、各レプリカのフェールオーバー モード、および同期の状態を確認できます。

    ![AG ダッシュボード](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665533.png)

1. **[サーバー マネージャー]** に戻り、**[ツール]** を選択し、**[フェールオーバー クラスター マネージャー]** を起動します。

1. **[Cluster1.corp.contoso.com]**、**[サービスとアプリケーション]** の順に展開します。 **[役割]** を選択し、**AG1** 可用性グループの役割が作成されていることに注目します。 リスナーを構成していないため、AG1 には、データベース クライアントが可用性グループに接続するときに使用する IP アドレスがありません。 読み取り/書き込み操作の場合はプライマリ ノードに、読み取り専用クエリの場合はセカンダリ ノードに直接接続できます。

    ![フェールオーバー クラスター マネージャー内のAG](./media/virtual-machines-windows-portal-sql-alwayson-availability-groups-manual/IC665534.png)

>[AZURE.WARNING] フェールオーバー クラスター マネージャーから、可用性グループのフェールオーバーを実行しないでください。 すべてのフェールオーバー操作は、SSMS の **AlwaysOn ダッシュボード** から実行する必要があります。 詳細については、 [WSFC フェールオーバー クラスター マネージャーと可用性グループの使用に関する制限事項](https://msdn.microsoft.com/library/ff929171.aspx)のページを参照してください。

## <a name="configure-internal-load-balancer"></a>内部 Load Balancer を構成する

可用性グループに直接接続するには、ロード バランサーを構成する必要があります。 ロード バランサーは、リスナーの IP アドレスとプローブ ポートにバインドされている VM にクライアント トラフィックをリダイレクトします。 このチュートリアルでは、内部ロード バランサー (ILB) を使用します。 ILB は、同じ仮想ネットワーク内からのトラフィックが SQL Server に接続することを許可します。 インターネット経由で SQL Server に接続する必要があるアプリケーションには、インターネットに接続する (または外部の) ロード バランサーが必要です。 詳細については、「 [Azure ロード バランサーの概要](../load-balancer/load-balancer-overview.md)」を参照してください。

>[AZURE.NOTE] 現在、Azure ポータルでは、特定のフロント エンド ポートをロード バランサーで 1 回だけ使用できます。 すべてのリスナーに同じポートを使用するには、PowerShell を使用してリスナーの IP アドレスをロード バランサーに接続します。 手順については、「 [Create availability group listener and load balancer | Azure (可用性グループ リスナーとロード バランサーの作成 | Azure)](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)」を参照してください。 

### <a name="create-the-load-balancer-in-azure"></a>Azure にロード バランサーを作成する

1. Azure ポータルで **[SQL-HA-RG]** に移動し、**[+ 追加]** をクリックします。

1. 「 **ロード バランサー**」を検索します。 Microsoft が公開しているロード バランサーを選んで **[作成]**をクリックします。

1. ロード バランサーに関して次のパラメーターを構成します。

| Setting | フィールド |
| --- | ---
| **名前** | sqlLB
| **スキーム** | 内部
| **Virtual Network** | autoHAVNET
| **サブネット** | subnet-2。 これは、クラスター リソース内のリスナーに対して設定する IP アドレスです。 
| **IP アドレスの割り当て** | 静的
| **IP アドレス** | subnet-2 の範囲内のアドレスを使用します。
| **サブスクリプション** | この例で使用されている他のリソースと同じサブスクリプションを使用します。
| **場所** | この例で使用されている他のリソースと同じ場所を使用します。

**[作成]**をクリックします。

ロード バランサーで次の設定を行います。

| Setting | フィールド |
| --- | ---|
| **バックエンド プール** 名 | sqlLBBE 
| **SQLLBBE 可用性セット** | sqlAvailabilitySet
| **SQLLBBE 仮想マシン** | sqlserver-0、sqlserver-1
| **SQLLBBE 使用者** | SQLAlwaysOnEndPointListener
| **プローブ** 名 | SQLAlwaysOnEndPointProbe
| **プローブ プロトコル** | TCP
| **プローブ ポート** | 59999 - 任意の未使用ポートを使用できます。
| **プローブの期間** | 5
| **プローブ異常しきい値** | 2
| **プローブ使用者** | SQLAlwaysOnEndPointListener
| **負荷分散規則** の名前 | SQLAlwaysOnEndPointListener
| **負荷分散規則のプロトコル** | TCP
| **負荷分散規則のポート** | 1433 * 
| **負荷分散規則のバックエンド ポート** | 1433
| **負荷分散規則のプローブ** | SQLAlwaysOnEndPointProbe
| **負荷分散規則のセッション永続化** | なし
| **負荷分散規則のアイドル タイムアウト** | 4
| **負荷分散規則のフローティング IP (Direct Server Return)** | 有効

> * 1433 は既定の SQL Server ポートです。 既定のインスタンスのフロント エンド ポートに使用します。 複数の可用性グループを必要とする場合は、可用性グループごとに追加の IP アドレスを作成する必要があります。 各可用性グループには、独自のフロント エンド ポートが必要です。 「 [Create availability group listener and load balancer | Azure (可用性グループ リスナーとロード バランサーの作成 | Azure)](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)」を参照してください。

>[AZURE.NOTE] 負荷分散規則の Direct Server Return は、作成時点で有効にする必要があります。

ロード バランサーの構成が済んだら、フェールオーバー クラスター上のリスナーに対する構成作業を行います。 

## <a name="configure-the-listener"></a>リスナーの構成

次の手順は、フェールオーバー クラスター上の可用性グループ リスナーの構成です。 

1. ad-primary-dc から RDP で sqlserver-0 の SQL Server に接続します。

1. フェールオーバー クラスター マネージャーで、クラスター ネットワークの名前をメモします。 クラスター ネットワークの名前は、**フェールオーバー クラスター マネージャー**の左側のウィンドウで **[ネットワーク]** をクリックして確認できます。 この名前は、PowerShell スクリプトの `$ClusterNetworkName` 変数に使用します。

1. フェールオーバー クラスター マネージャーでクラスター名を展開し、 **[ロール]**をクリックします。

1. **[ロール]** で可用性グループ名を右クリックし、**[リソースの追加]**、 > **[クライアント アクセス ポイント]** の順に選択します。 

1. **[名前]** には「**aglistener**」と入力します。 **[次へ]** を 2 回クリックし、**[完了]** をクリックします。 この時点では、リスナーまたはリソースをオンラインにしないでください。

1. **[リソース]** タブをクリックして、作成したクライアント アクセス ポイントを展開します。 IP リソースを右クリックし、[プロパティ] をクリックします。 IP アドレスの名前をメモします。 この名前は、PowerShell スクリプトの `$IPResourceName` 変数に使用します。

1. **[IP アドレス]** で **[静的 IP アドレス]** をクリックし、静的 IP アドレスを設定します。Azure Portal で **sqlLB** ロード バランサーに使用したものと同じアドレスを指定してください。 この同じ IP アドレスを、PowerShell スクリプトの `$ILBIP` 変数で使用します。 

1. このアドレスに対して NetBIOS を無効にし、 **[OK]**をクリックします。 

1. 現在プライマリ レプリカのホストとなっているクラスター ノードから、管理者特権で PowerShell ISE を開き、新しいスクリプトに次のコマンドを貼り付けます。

    ```
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = "<X.X.X.X>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn> # In this sample we've using 59999 for the probe port. 

    Import-Module FailoverClusters

    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```

1. 変数を修正して PowerShell スクリプトを実行し、新しいリスナーの IP アドレスとポートを構成します。

1. **フェールオーバー クラスター マネージャー**で可用性グループ リソースを右クリックし、**[プロパティ]** をクリックします。 **[依存関係]** タブで、リスナーのネットワーク名を使用するようにリソース グループを設定します。 

1. リスナーのポート プロパティを 1433 に設定します。 これを行うには、SQL Server Management Studio を開いて可用性グループ リスナーを右クリックし、[プロパティ] を選択して、 **[ポート]** を 1433 に設定します。 SQL Server 用に構成したのと同じポート番号を使用する必要があります。 

1. この時点で、リスナーをオンラインに切り替えることができます。 リスナー名を右クリックし、 **[オンラインにする]**をクリックします。

### <a name="test-the-connection-to-the-listener"></a>リスナーへの接続をテストする

接続をテストするには、次の手順に従います。

1. レプリカを所有していない SQL Server に RDP で接続します。

1. sqlcmd ユーティリティを使用して接続をテストします。 たとえば次のスクリプトは、リスナー経由で Windows 認証を使用し、プライマリ レプリカとの sqlcmd 接続を確立しています。

    sqlcmd -S "<listenerName>" -E

  リスナーが 1433 以外のポートを使用している場合は、そのポート番号をテストで指定する必要があります。 たとえば、次のクエリでは、ポート 1435 を使用するリスナー名への接続をテストします。

        sqlcmd -S "<listenerName>",1435 -E



## <a name="next-steps"></a>次のステップ

Azure での SQL Server の使用に関するその他の情報については、「 [Azure Virtual Machines における SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。



<!--HONumber=Oct16_HO2-->


