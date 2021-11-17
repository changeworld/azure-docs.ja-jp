---
title: 'チュートリアル: 複数のサブネットに可用性グループを構成する'
description: 'このチュートリアルでは、Azure Virtual Machines (VM) の複数のサブネット内に SQL Server の Always On 可用性グループを作成する方法を説明します。 '
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: aeef6f689e9a27b6a813b53e73ceb11ff982af97
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132159215"
---
# <a name="tutorial-configure-an-availability-group-in-multiple-subnets-sql-server-on-azure-vms"></a>チュートリアル: 複数のサブネットに可用性グループを構成する (Azure VM 上の SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> 同じ Azure 仮想ネットワーク内の複数のサブネットに SQL Server VM を作成することで、Always On 可用性グループ (AG) に対して Azure Load Balancer が不要になります。

このチュートリアルでは、Azure Virtual Machines (VM) の複数のサブネット内に SQL Server の Always On 可用性グループを作成する方法を説明します。 チュートリアル全体では、Windows Server フェールオーバー クラスターを 1 つを作成し、さらに、2 つの SQL Server レプリカと 1 つのリスナーを含む可用性グループを 1 つ作成します。

 
**推定所要時間**: このチュートリアルの所要時間は、[前提条件](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md)が満たされていると仮定して約 30 分です。 


## <a name="prerequisites"></a>前提条件

次の表に、このチュートリアルを開始する前に完了している必要がある[前提条件](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md)を示します。

| 要件 | 説明 |
|----- |----- |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial-multi-subnet/square.png" border="false":::   **2 つの SQL Server インスタンス** | - 各 VM が 2 つの異なる Azure 可用性ゾーンまたは同じ可用性セットに存在すること <br/> - Azure Virtual Network 内の別個のサブネットに存在すること <br/> - 2 つのセカンダリ IP が各 VM に割り当てられていること <br/> - 単一のドメイン内 <br/> |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial-multi-subnet/square.png" border="false":::   **SQL Server サービス アカウント** | 各マシンの SQL Server サービスによって使用されるドメイン アカウント | 
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial-multi-subnet/square.png" border="false":::   **ファイアウォールのポートを開きます** | - SQL Server:**1433** (既定インスタンス用) <br/> - データベース ミラーリング エンドポイント:**5022** または使用可能な任意のポート <br/> |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial-multi-subnet/square.png" border="false":::   **ドメインのインストール アカウント** | - 各 SQL Server 上のローカル管理者 <br/> - SQL Server の各インスタンスの SQL Server sysadmin 固定サーバー ロールのメンバー  |

このチュートリアルでは、[SQL Server Always On 可用性グループ](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)の基本的な知識があることを前提としています。 

<a name="CreateCluster"></a>

## <a name="create-the-cluster"></a>クラスターを作成する

Always On 可用性グループは Windows Server フェールオーバー クラスター インフラストラクチャ上で動作するため、可用性グループをデプロイするにあたってはまず、Windows Server フェールオーバー クラスターを構成する作業、たとえばその機能の追加、クラスターの作成、クラスターの IP アドレスの設定が必要となります。 

### <a name="add-failover-cluster-feature"></a>フェールオーバー クラスターの機能を追加する

両方の SQL Server VM にフェールオーバー クラスターの機能を追加します。 これを行うには、次のステップに従います。 

1. [前提条件の記事](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md)で作成した **CORP\Install** ドメイン アカウントなど、AD にオブジェクトを作成するためのアクセス許可が割り当てられたドメイン アカウントを使用し、リモート デスクトップ プロトコル (RDP) を通じて SQL Server 仮想マシンに接続します。 
1. **[サーバー マネージャー] ダッシュボード** を開きます。
1. ダッシュボードの **[役割と機能の追加]** リンクを選択します。

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/09-add-features.png" alt-text="ダッシュボードの [役割と機能の追加] リンクを選択します。":::

1. **[Server Features (サーバーの機能)]** セクションが表示されるまで **[次へ]** を選択します。
1. **[機能]** で **[フェールオーバー クラスタリング]** を選択します。
1. その他の必要な機能を追加します。
1. **[インストール]** を選択して機能を追加します。
1. もう一方の SQL Server VM についても同じ手順を繰り返します。 


### <a name="create-cluster"></a>クラスターの作成

それぞれの SQL Server VM にクラスター機能を追加したら、Windows Server フェールオーバー クラスターを作成する準備は完了です。 

クラスターを作成するには、次の手順に従います。 

1. [前提条件の記事](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md)で作成した **CORP\Install** ドメイン アカウントなど、AD にオブジェクトを作成するためのアクセス許可が割り当てられたドメイン アカウントを使用し、リモート デスクトップ プロトコル (RDP) を使用して 1 つ目の SQL Server VM (**SQL-VM-1** など) に接続します。 
2. **[サーバー マネージャー]** ダッシュボードで、 **[ツール]** を選択し、 **[フェールオーバー クラスター マネージャー]** を選択します。
3. 左側のペインで、 **[フェールオーバー クラスター マネージャー]** を右クリックし、 **[クラスターの作成]** を選択します。

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/01-create-cluster.png" alt-text="クラスターの作成":::

4. **クラスターの作成ウィザード** の各ページの手順に従い、次の表に示した設定を使って 2 ノード クラスターを作成します。

   | ページ | 設定 |
   | --- | --- |
   | はじめに |既定値を使用します。 |
   | サーバーの選択 |**[サーバー名を入力してください]** に 1 つ目の SQL Server の名前 (**SQL-VM-1** など) を入力し、 **[追加]** を選択します。<br/>**[サーバー名を入力してください]** に 2 つ目の SQL Server の名前 (**SQL-VM-2** など) を入力し、 **[追加]** を選択します。 |
   | 検証の警告 |**[はい。[次へ] をクリックして、構成検証テストを実行し、その後クラスターの作成プロセスに戻ります]** を選択します。 |
   | 開始する前に | [次へ] を選択します。 |
   | 選択するテストのみを実行する | **[Run only the tests I select]\(選択したテストのみを実行する\)** を選択します。 |
   | テストの選択 | [Storage]\(ストレージ\) をクリアします。 **[インベントリ]** 、 **[ネットワーク]** 、 **[システム構成]** が選択されていることを確認してください。 
   | 確認 | [次へ] を選択します。<br/>検証の完了を待ちます。<br/>**[レポートの表示]** を選択してレポートを確認します。 VM に到達できるネットワーク インターフェイスが 1 つしかないという警告は無視してかまいません。 Azure インフラストラクチャは物理的な冗長性を備えています。したがって、別途ネットワーク インターフェイスを追加する必要はありません。<br/> **[完了]** を選択します。|
   | クラスター管理用のアクセス ポイント |**[クラスター名]** にクラスター名を入力します (例: **SQLAGCluster1**)。|
   | 確認 | **[使用可能な記憶域をすべてクラスターに追加する]** をオフにし、 **[次へ]** を選択します。 |
   | まとめ | **[完了]** を選択します。 | 

   >[!WARNING]
   >**[使用可能な記憶域をすべてクラスターに追加する]** をオフにしないと、Windows はクラスター作成処理中に仮想ディスクをデタッチします。 その結果、仮想ディスクはディスク マネージャーやエクスプローラーに表示されなくなり、表示するためには、記憶域をクラスターから削除し、PowerShell を使って再アタッチしなければなりません。 
   > 
   

### <a name="set-the-failover-cluster-ip-address"></a>フェールオーバー クラスターの IP アドレスを設定する

通常、クラスターに割り当てられる IP アドレスは VM に割り当てられた IP アドレスと同じです。つまり Azure では、クラスターの IP アドレスがエラー状態になるので、オンラインにすることができません。 IP リソースをオンラインにするには、クラスターの IP アドレスを変更してください。 

「前提条件」で、各 SQL Server VM に[セカンダリ IP アドレスを割り当て](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md#add-secondary-ips-to-sql-server-vms)ました。以下の表はその例です (実際の IP アドレスは異なる場合があります)。 

   | VM 名 | サブネット名 | サブネットのアドレス範囲 | セカンダリ IP 名 | セカンダリ IP アドレス |
   | --- | --- | --- | --- | --- |
   | SQL-VM-1 | SQL-subnet-1 | 10.38.1.0/24 | windows-cluster-ip | 10.38.1.10 |
   | SQL-VM-2 | SQL-subnet-2 | 10.38.2.0/24 | windows-cluster-ip | 10.38.2.10

これらの IP アドレスを、対象となる各サブネットのクラスターの IP アドレスとして割り当てます。 

> [!NOTE]
> Windows Server 2019 では、**クラスター ネットワーク名** の代わりに **分散サーバー名** がクラスターによって作成されます。また、クラスターの全ノードの IP アドレスにクラスター名オブジェクト (CNO) が自動的に登録されるので、Windows クラスター専用 IP アドレスは必要ありません。 Windows Server 2019 をご使用の場合は、このセクション、そして **クラスター コア リソース** に触れるその他の手順をスキップするか、または [PowerShell](failover-cluster-instance-storage-spaces-direct-manually-configure.md#create-windows-failover-cluster) を使用して仮想ネットワーク名 (VNN) ベースのクラスターを作成してください。 詳細については、「[フェールオーバー クラスター: クラスター ネットワーク オブジェクト](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)」のブログを参照してください。 


クラスターの IP アドレスを変更するには、次の手順に従います。 

1. **フェールオーバー クラスター マネージャー** で、 **[クラスター コア リソース]** まで下にスクロールして、クラスターの詳細を展開します。 各サブネットの **[名前]** リソースと 2 つの **[IP アドレス]** リソースが **エラー** 状態になっていることがわかります。
1. エラー状態の 1 つ目の **IP アドレス** リソースを右クリックし、 **[プロパティ]** を選択します。

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/02-failed-ip-address.png" alt-text="クラスターのプロパティ":::

1. **[静的 IP アドレス]** を選択し、1 つ目の SQL Server VM (**SQL-VM-1** など) に割り当てたサブネットの [Windows クラスター専用 IP アドレス](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md#add-secondary-ips-to-sql-server-vms)に更新します。 **[OK]** を選択します。
   
    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/03-first-static-ip-address.png" alt-text="**[静的 IP アドレス]** を選択し、「前提条件」の記事で SQL Server VM に割り当てた同じサブネットの Windows クラスター専用 IP アドレスに更新":::

1. 2 つ目の SQL Server VM (**SQL-VM-2** など) に割り当てたサブネットの [Windows クラスター専用 IP アドレス](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md#add-secondary-ips-to-sql-server-vms)を使用して、エラー状態の 2 つ目の **[IP アドレス]** リソースについても同じ手順を繰り返します。 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/04-second-static-ip-address.png" alt-text="もう一方の SQL Server VM に割り当てたサブネットの Windows クラスター専用 IP アドレスを使用して、エラー状態の 2 つ目の **[IP アドレス]** リソースについても同じ手順を繰り返します。":::

1. **[クラスター コア リソース]** セクションで、クラスター名を右クリックして、 **[オンラインにする]** を選択します。 名前リソースといずれかの IP アドレス リソースがオンラインになるまで待ちます。 

これらの SQL Server VM はそれぞれ異なるサブネットに存在するため、クラスターに使用されるのは、2 つの Windows クラスター専用 IP アドレスのうち、どちらか一方のみとなります。 クラスター名リソースがオンラインになると、新しい Active Directory (AD) コンピューター アカウントでドメイン コントローラー (DC) サーバーが更新されます。 クラスター コア リソースによってノードが移動された場合、一方の IP アドレスがオフラインになり、もう一方はオンラインに切り替わって、その新しい IP アドレスの関連付けで DC サーバーが更新されます。  

>[!TIP]
> 運用環境の Azure VM でクラスターを実行するときは、クラウド環境におけるクラスターの安定性と信頼性を高めるために、クラスターの設定をより緩やかな監視状態に変更してください。 詳細については、[SQL Server VM の HADR 構成のベスト プラクティス](hadr-cluster-best-practices.md#checklist)に関する記事を参照してください。

## <a name="configure-quorum"></a>クォーラムを構成する

2 ノード クラスターでは、クラスターの信頼性と安定性を確保するためにクォーラム デバイスが必要となります。 Azure VM では、クォーラム構成としてクラウド監視が推奨されますが、[その他のオプションも提供](hadr-cluster-quorum-configure-how-to.md)されています。 このセクションの手順では、クォーラムのクラウド監視を構成します。 ストレージ アカウントへのアクセス キーを確認した後、クラウド監視を構成します。 

## <a name="get-access-keys-for-storage-account"></a>ストレージ アカウントのアクセス キーを取得する 

Microsoft Azure ストレージ アカウントを作成すると、自動的に生成される 2 つのアクセス キー (プライマリ アクセス キーとセカンダリ アクセス キー) がそこに関連付けられます。 クラウド監視の初回作成時はプライマリ アクセス キーを使用しますが、それ以降は、クラウド監視に使用するキーに制限はありません。 

[前提条件の記事](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md#create-azure-storage-account)で作成した Azure ストレージ アカウントのストレージ アクセス キーを Azure portal で確認し、コピーしてください。 


ストレージ アクセス キーを確認してコピーするには、次の手順に従います。 

1. [Azure portal](https://portal.azure.com) で対象のリソース グループに移動し、作成したストレージ アカウントを選択します。 
1. **[セキュリティとネットワーク]** の **[アクセス キー]** を選択します。 
1. **[キーの表示]** を選択してキーをコピーします。 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/05-storage-account-keys.png" alt-text="**[キーの表示]** を選択してキーをコピーします":::

### <a name="configure-cloud-witness"></a>クラウド監視を構成する

アクセス キーをコピーしたら、クラスター クォーラムのクラウド監視を作成します。 

クラウド監視を作成するには、次の手順に従います。 

1. リモート デスクトップを使用して 1 つ目の SQL Server VM (**SQL-VM-1**) に接続します。
1. **Windows PowerShell** を管理者モードで開きます。
1. PowerShell スクリプトを実行して、接続の TLS (トランスポート層セキュリティ) の値を 1.2 に設定します。

    ```powershell
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    ```
    
1. PowerShell を使用してクラウド監視を構成します。 ストレージ アカウント名とアクセス キーの値は、実際の情報に置き換えてください。  

    ```powershell
    Set-ClusterQuorum -CloudWitness -AccountName "Storage_Account_Name&quot; -AccessKey &quot;Storage_Account_Access_Key"
    ```
    
1. 次の出力例は成功を示しています。  

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/06-configure-quorum.png" alt-text="出力結果には、この例のように &quot;クラウド監視&quot; と表示されます":::

クラスター コア リソースにクラウド監視が構成されます。

## <a name="enable-ag-feature"></a>AG 機能を有効にする 

Always On 可用性グループ機能は、既定では無効になっています。 両方の SQL Server インスタンスを対象に、**SQL Server 構成マネージャー** を使用してこの機能を有効にします。 

可用性グループの機能を有効にするには、次の手順に従います。 

1. sysadmin 固定サーバー ロールのメンバーであるドメイン アカウント、たとえば [前提条件のドキュメント](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md)で作成した **CORP\Install** ドメイン アカウントを使用し、RDP ファイルを起動して 1 つ目の SQL Server VM (**SQL-VM-1** など) に接続します。
1. いずれかの SQL Server VM の **スタート** 画面から **SQL Server 構成マネージャー** を起動します。
1. ブラウザー ツリーで、 **[SQL Server のサービス]** を強調表示し、 **[SQL Server (MSSQLSERVER)]** サービスを右クリックして、 **[プロパティ]** を選択します。
1. **[AlwaysOn 高可用性]** タブを選択し、 **[AlwaysOn 可用性グループを有効にする]** チェック ボックスをオンにします。

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/08-enable-always-on.png" alt-text="[AlwaysOn 可用性グループを有効にする]":::

1. **[適用]** を選択します。 ポップアップ ダイアログで **[OK]** を選択します。
1. SQL Server サービスを再起動します。
1. もう一方の SQL Server インスタンスについてもこれらの手順を繰り返します。


## <a name="create-database"></a>データベースの作成

データベースについては、このセクションの手順に従って新しいデータベースを作成するか、または [AdventureWorks データベース](/sql/samples/sql-samples-where-are)を復元してください。 また、データベースをバックアップしてログ チェーンを初期化する必要があります。 バックアップされていないデータベースは、可用性グループの前提条件を満たしません。 

データベースを作成するには、次の手順に従います。 

1. sysadmin 固定サーバー ロールのメンバーであるドメイン アカウント、たとえば [前提条件のドキュメント](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md)で作成した **CORP\Install** ドメイン アカウントを使用し、RDP ファイルを起動して 1 つ目の SQL Server VM (**SQL-VM-1** など) に接続します。
1. **SQL Server Management Studio** を開き、SQL Server インスタンスに接続します。
1. **オブジェクト エクスプローラー** で、 **[データベース]** を右クリックし、 **[新しいデータベース]** を選択します。
1. **[データベース名]** に「**MyDB1**」と入力します。 
1. **[オプション]** ページを選択し、 **[復旧モデル]** ボックスの一覧から **[完全]** (既定値) を選択します。 可用性グループに参加するという前提条件を満たすには、データベースが完全復旧モードになっている必要があります。 
1. **[OK]** を選択して **[新しいデータベース]** ページを閉じ、新しいデータベースを作成します。 


データベースをバックアップするには、次の手順に従います。 

1. **オブジェクト エクスプローラー** でデータベースを右クリックし、 **[タスク]** を強調表示して、 **[バックアップ]** を選択します。

1. **[OK]** を選択して、既定のバックアップ場所にデータベースの完全バックアップを作成します。

## <a name="create-file-share"></a>ファイル共有の作成

2 つの SQL Server VM とそのサービス アカウントからアクセスできるバックアップ ファイル共有を作成します。 

バックアップ ファイル共有を作成するには、次の手順に従います。 


1. **サーバー マネージャー** の 1 つ目の SQL Server VM で、 **[ツール]** を選択します。 **[コンピューターの管理]** を開きます。

2. **[共有フォルダー]** を選択します。

3. **[共有]** を右クリックして **[新しい共有]** を選択し、**共有フォルダーの作成ウィザード** を使用して共有を作成します。

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/09-new-share.png" alt-text="[新しい共有] を選択する":::

4. **[フォルダー パス]** で、 **[参照]** を選択して、データベース バックアップ共有フォルダーのパスを検索するか作成します (例: `C:\Backup`)。 **[次へ]** を選択します。

5. **[名前、説明および設定]** で、共有名とパスを確認します。 **[次へ]** を選択します。

6. **[共有フォルダーのアクセス許可]** で、 **[アクセス許可のカスタマイズ]** を設定します。 **[カスタム]** を選択します。

7. **[アクセス許可のカスタマイズ]** で、 **[追加]** を選択します。

8. "*両方*" の SQL Server サービス アカウント (`Corp\SQLSvc1` および `Corp\SQLSvc2`) について、 **[フル コントロール]** チェック ボックスをオンにして共有へのフル アクセスを付与します。  

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/10-backup-share-permission.png" alt-text="両方のサーバーの SQL Server サービス アカウントにフル コントロールを付与します。":::

9. **[OK]** を選択します。

10. **[共有フォルダーのアクセス許可]** で、 **[完了]** を選択します。 **[完了]** をもう一度選択します。  

## <a name="create-availability-group"></a>可用性グループを作成する 

データベースのバックアップが完了したら、可用性グループを作成することができます。可用性グループを作成することで自動的に、プライマリ SQL Server レプリカから完全バックアップとトランザクション ログ バックアップが作成され、**NORECOVERY** オプションでセカンダリ SQL Server インスタンスに復元されます。 

可用性グループを作成するには、次の手順に従います。 

1. 1 つ目の SQL Server VM (**SQL-VM-1** など) で、SQL Server Management Studio (SSMS) の **オブジェクト エクスプローラー** から **[Always On 高可用性]** を右クリックし、 **[新しい可用性グループ ウィザード]** を選択します。

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/11-new-ag-wizard.png" alt-text="新しい可用性グループ ウィザードを起動する":::

1. **[説明]** ページで **[次へ]** を選択します。 **[可用性グループ名の指定]** ページで、 **[可用性グループ名]** に可用性グループの名前を入力します (例: **AG1**)。 **[次へ]** を選択します。

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/12-new-ag-name.png" alt-text="新しい可用性グループ ウィザード、可用性グループ名の指定":::

1. **[データベースの選択]** ページで、対象のデータベースを選択し、 **[次へ]** を選択します。 データベースが前提条件を満たしていない場合は、データベースが完全復旧モードになっていることを確認して、[バックアップを作成](#create-database)してください。 

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/13-new-ag-select-database.png" alt-text="新しい可用性グループ ウィザード、データベースの選択":::

1. **[レプリカの指定]** ページで **[レプリカの追加]** を選択します。

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/14-new-ag-add-replica.png" alt-text="新しい可用性グループ ウィザード、レプリカの指定":::

1. **[サーバーに接続]** ダイアログが表示されます。 2 番目のサーバーの名前を **[サーバー名]** に入力します (例: **SQL-VM-2**)。 **[接続]** を選択します。
1. **[レプリカの指定]** ページの **[自動フェールオーバー]** チェック ボックスをオンにし、可用性モードのボックスの一覧から **[同期コミット]** を選択します。

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/15-new-ag-replica.png" alt-text="**[レプリカの指定]** ページの [自動フェールオーバー] チェック ボックスをオンにし、可用性モードに [同期コミット] を選択します":::

1. **[エンドポイント]** タブを選択して、データベース ミラーリング エンドポイントに使用されているポートが、[ファイアウォールで開放](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md#configure-the-firewall)したポートであることを確認します。 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/16-endpoint.png" alt-text="新しい可用性グループ ウィザード、最初のデータ同期を選択":::

1. **[リスナー]** タブを選択し、**可用性グループ リスナーを作成** します。リスナーには次の値を使用します。 

   |フィールド | 値 |
   | --- | --- |
   | リスナーの DNS 名 | AG1-Listener |
   | Port | 既定の SQL Server ポートを使用します。 1433 |
   | ネットワーク モード | 静的 IP |

1. **[追加]** を選択し、両方の SQL Server VM について、リスナーのセカンダリ専用 IP アドレスを指定します。 

   次の表に示したのは、[前提条件のドキュメント](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md)のリスナーに対して作成された IP アドレスの例です (実際の IP アドレスとは異なる場合があります)。
   
   | VM 名 | サブネット名 | サブネットのアドレス範囲 | セカンダリ IP 名 | セカンダリ IP アドレス |
   | --- | --- | --- | --- | --- |
   | SQL-VM-1 | SQL-subnet-1 | 10.38.1.0/24 | availability-group-listener | 10.38.1.11 |
   | SQL-VM-2 | SQL-subnet-2 | 10.38.2.0/24 | availability-group-listener | 10.38.2.11

1. **[IP アドレスの追加]** ダイアログ ボックスにあるドロップダウンから 1 つ目のサブネット (10.38.1.0/24 など) を選択し、リスナー専用のセカンダリ **IPv4 アドレス** (`10.38.1.11` など) を入力します。 **[OK]** を選択します。 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/18-add-listener-ip-subnet-1.png" alt-text="[IP アドレスの追加] ダイアログ ボックスにあるドロップダウンから 1 つ目のサブネット (10.38.1.0/24 など) を選択し、リスナー専用のセカンダリ IPv4 アドレス (10.38.1.11 など) を入力します":::

1. もう一度同じ手順を行います。ただし、ドロップダウンから選択するのは、もう一方のサブネット (10.38.2.0/24 など) です。また、入力するアドレスも、もう一方の SQL Server VM のリスナー専用セカンダリ **IPv4 アドレス** (`10.38.2.11` など) となります。 **[OK]** を選択します。 
    

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/19-add-listener-ip-subnet-2.png" alt-text="リスナーの IP の追加":::    

1. **[リスナー]** ページで値を確認した後、 **[次へ]** を選択します。 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/20-listener.png" alt-text="[リスナー] ページで値を確認した後、[次へ] を選択します。":::

1. **[最初のデータ同期を選択]** ページで **[完全なデータベースとログ バックアップ]** を選択し、[前に作成したネットワーク共有場所](#create-file-share) (`\\SQL-VM-1\Backup` など) を入力します。 

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/21-full-data-sync.png" alt-text="完全データ同期を選択":::

   > [!NOTE]
   > 完全同期では、SQL Server の 1 番目のインスタンスにあるデータベースの完全バックアップが作成されて、2 番目のインスタンスに復元されます。 大規模なデータベースの場合、完全同期は長い時間がかかることがあるのでお勧めできません。 手動でデータベースのバックアップを作成し、`NO RECOVERY` で復元することにより、この時間を短縮できます。 可用性グループを構成する前に 2 番目の SQL Server のデータベースが `NO RECOVERY` で既に復元されている場合は、 **[参加のみ]** を選びます。 可用性グループを構成した後でバックアップを作成する場合は、 **[最初のデータ同期をスキップ]** を選びます。
   

1. **[検証]** ページで、すべての検証チェックに成功していることを確認し、 **[次へ]** を選択します。 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/22-validation.png" alt-text="新しい可用性グループ ウィザード、検証":::

   
1. **サマリー** ページで **[完了]** を選択し、ウィザードによって新しい可用性グループが構成されるのを待ちます。 **[進行状況]** ページの **[詳細情報]** を選択して詳細な進行状況を表示します。 **[結果]** ページに "**ウィザードが正常に完了しました**" と入力されたら、サマリーを見て可用性グループとリスナーが正常に作成されたことを確認します。 

     :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/23-results.png" alt-text="新しい可用性グループ ウィザード、結果":::

1. **[閉じる]** を選択してウィザードを終了します。

## <a name="check-availability-group"></a>可用性グループを確認する

可用性グループの正常性は、**SQL Server Management Studio** と **フェールオーバー クラスター マネージャー** を使用して確認できます。  

可用性グループの状態を確認するには、次の手順に従います。 

1. **オブジェクト エクスプローラー** で **[Always On 高可用性]** を展開し、 **[可用性グループ]** を展開します。 このコンテナー内に新しい可用性グループが表示されます。 可用性グループを右クリックして **[ダッシュボードの表示]** をクリックします。

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/24-show-dashboard.png" alt-text="可用性グループ ダッシュ ボードを表示する":::

   可用性グループ ダッシュボードに、レプリカと各レプリカのフェールオーバー モード、同期状態が表示されます。以下はその例です。 

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/25-ag-dashboard.png" alt-text="可用性グループ ダッシュ ボード":::


2. **[フェールオーバー クラスター マネージャー]** を開き、該当するクラスターを選択して **[ロール]** を選択すると、そのクラスター内に作成した可用性グループのロールが表示されます。 **[AG1]** ロールを選択し、 **[リソース]** タブを選択すると、次の例のように、リスナーとそれに関連付けられている IP アドレスが表示されます。 

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/26-cluster-manager.png" alt-text="フェールオーバー クラスター マネージャー内の可用性グループ":::

この時点で可用性グループは完成しており、SQL Server の 2 つのインスタンス上にレプリカが存在するほか、対応する可用性グループ リスナーが 1 つ存在します。 リスナーを使用して接続したり、**SQL Server Management Studio** を使用して 2 つのインスタンス間で可用性グループを移動したりすることができます。 

> [!WARNING]
> フェールオーバー クラスター マネージャーを使用して、可用性グループのフェールオーバーを実行することは避けてください。 フェールオーバー操作はすべて **SQL Server Management Studio** 内から、**Always On ダッシュボード** や Transact-SQL (T-SQL) を使用するなどして実行する必要があります。 詳細については、[可用性グループに対するフェールオーバー クラスター マネージャーの使用に関する制限事項](/sql/database-engine/availability-groups/windows/failover-clustering-and-always-on-availability-groups-sql-server)のページを参照してください。



## <a name="test-listener-connection"></a>リスナー接続をテストする

可用性グループの準備が整い、適切なセカンダリ IP アドレスでリスナーを構成したら、リスナーへの接続をテストします。  

接続をテストするには、次の手順に従います。 

1. 同じ仮想ネットワーク内に存在するもののレプリカを保有していない SQL Server に RDP で接続します。クラスター内のもう一方の SQL Server インスタンスなど、**SQL Server Management Studio** がインストールされている VM であれば何でもかまいません。  

2. **SQL Server Management Studio** を開き、 **[サーバーに接続]** ダイアログ ボックスの **[サーバー名]** にリスナーの名前 (「**AG1-Listener**」など) を入力し、 **[オプション]** を選択します。 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/27-ssms-listener-connect.png" alt-text="SQL Server Management Studio を開いて、リスナーの名前 (「AG1-Listener」など) を [サーバー名] に入力します":::

3. **[追加の接続パラメーター]** ウィンドウに「`MultiSubnetFailover=True`」と入力して **[接続]** を選択すると、プライマリ SQL Server レプリカがホストされているいずれかのインスタンスへと自動的に接続されます。  

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/28-ssms-connection-parameters.png" alt-text="SSMS 接続":::

> [!NOTE]
> - 異なるサブネット上の可用性グループに接続するときは、「`MultiSubnetFailover=true`」に設定することで、現在のプライマリ レプリカの検出とレプリカへの接続にかかる時間が短縮されます。 「[MultiSubnetFailover を使用した接続](/dotnet/framework/data/adonet/sql/sqlclient-support-for-high-availability-disaster-recovery#connecting-with-multisubnetfailover)」を参照してください。
> - .NET Framework 4.6.1 以降のバージョンでは、`MultiSubnetFailover=True` の設定は不要です。

## <a name="next-steps"></a>次の手順


詳細については、以下をご覧ください。

- [Windows Server フェールオーバー クラスターと Azure VM 上の SQL Server](hadr-windows-server-failover-cluster-overview.md)
- [AlwaysOn 可用性グループと Azure VM 上の SQL Server](availability-group-overview.md)
- [AlwaysOn 可用性グループの概要](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
- [Azure VM 上の SQL Server に対する HADR 設定](hadr-cluster-best-practices.md)