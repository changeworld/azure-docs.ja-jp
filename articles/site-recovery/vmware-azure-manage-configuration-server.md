---
title: "Azure Site Recovery での VMware ディザスター リカバリーのために構成サーバーを管理する | Microsoft Docs"
description: "この記事では、Azure Site Recovery で Azure への VMware ディザスター リカバリー用に既存の構成サーバーを管理する方法について説明します。"
services: site-recovery
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: ddb1c9426874634fea54f1b67509d4ac2af70eba
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>VMware VM 用の構成サーバーの管理

Azure への VMware 仮想マシンと物理サーバーのディザスター リカバリーに [Azure Site Recovery](site-recovery-overview.md) を使うときは、オンプレミスの構成サーバーを設定します。 構成サーバーは、オンプレミスの VMware と Azure の間の通信を調整し、データのレプリケーションを管理します。 この記事は、展開後に構成サーバーを管理するための一般的なタスクをまとめたものです。


## <a name="modify-vmware-settings"></a>VMware の設定を変更する

構成サーバーが接続する VMware サーバーの設定を変更します。

1. 構成サーバーを実行しているコンピューターにサインインします。
2. デスクトップのショートカットから Azure Site Recovery 構成マネージャーを起動します。 または、[このリンク](https://configuration-server-name/IP:44315)を開きます。
3. **[vCenter Server/vSphere ESXi サーバーの資格情報の管理]** を選択して、次の操作を行います。

    * 別の VMware サーバーを構成サーバーと関連付けるには、**[vCenter Server/vSphere ESXi サーバーの追加]** を選択します。 サーバーの詳細を入力します。

    * VMware VM の自動検出のための VMware サーバーへの接続に使う資格情報を更新するには、**[編集]** を選択します。 新しい資格情報を入力して、**[OK]** を選択します。

    ![VMware の変更](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

## <a name="modify-credentials-for-mobility-service-installation"></a>モビリティ サービス インストール用の資格情報を変更する

レプリケーションを有効にする VMware VM にモビリティ サービスを自動インストールするために使う資格情報を変更します。

1. 構成サーバーを実行しているコンピューターにサインインします。
2. デスクトップのショートカットから Site Recovery 構成マネージャーを起動します。 または、[このリンク](https://configuration-server-name/IP:44315)を開きます。
3. **[仮想マシンの資格情報の管理]** を選択して、新しい資格情報を入力します。 次に、**[OK]** を選択して、設定を更新します。

    ![モビリティ サービスの資格情報を変更する](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>プロキシの設定を変更する

Azure へのインターネット アクセスのために構成サーバー マシンが使うプロキシの設定を変更します。 構成サーバー マシンで実行されている既定のプロセス サーバーだけでなく、プロセス サーバー マシンがある場合は、両方のマシンで設定を変更します。

1. 構成サーバーを実行しているコンピューターにサインインします。
2. デスクトップのショートカットから Site Recovery 構成マネージャーを起動します。 または、[このリンク](https://configuration-server-name/IP:44315)を開きます。
3. **[接続の管理]** を選択して、プロキシの値を更新します。 次に、**[保存]** を選択して、設定を更新します。

## <a name="add-a-network-adapter"></a>ネットワーク アダプターを追加する

Open Virtualization Format (OVF) テンプレートは、ネットワーク アダプターが 1 つの構成サーバー VM を展開します。 [VM に新しいアダプターを追加する](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter)ことはできますが、構成サーバーをコンテナーに登録する前に追加する必要があります。

構成サーバーをコンテナーに登録した後でアダプターを追加するには、VM のプロパティでアダプターを追加します。 次に、サーバーをコンテナーに再登録します。


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>同じコンテナーに構成サーバーを登録する

必要な場合は、同じコンテナーに構成サーバーを再登録することができます。 構成サーバー マシンで実行されている既定のプロセス サーバーだけでなく、他のプロセス サーバー マシンがある場合は、両方のマシンを再登録します。


  1. コンテナーで、**[管理]** > **[Site Recovery インフラストラクチャ]** > **[構成サーバー]** を開きます。
  2. **[サーバー]** で **[登録キーのダウンロード]** を選択して、コンテナーの資格情報ファイルをダウンロードします。
  3. 構成サーバー マシンにサインインします。
  4. **%ProgramData%\ASR\home\svagent\bin** で、**cspsconfigtool.exe** を開きます。
  5. **[Vault Registration] (コンテナー登録)** タブで **[参照]** を選択して、ダウンロードしたコンテナー資格情報ファイルを探します。
  6. 必要な場合は、プロキシ サーバーの詳細を指定します。 次に、**[登録]** を選択します。
  7. 管理者の PowerShell コマンド ウィンドウを開き、次のコマンドを実行します。

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

## <a name="upgrade-the-configuration-server"></a>構成サーバーをアップグレードする

構成サーバーを更新するには、更新プログラムのロールアップを実行します。 更新は N-4 までのバージョンに適用できます。 例: 

- 9.7、9.8、9.9、または 9.10 を実行している場合は、9.11 に直接アップグレードできます。
- 9.6 以前を実行している場合に、9.11 にアップグレードするには、まずバージョン 9.7 にアップグレードしてから、 9.11 にアップグレードする必要があります。

すべてのバージョンの構成サーバーにアップグレードするための更新プログラムのロールアップへのリンクは、[wiki の更新プログラムのページ](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx)にあります。

次のようにサーバーをアップグレードします。

1. 更新プログラムのインストーラー ファイルを構成サーバーにダウンロードします。
2. インストーラーをダブルクリックして実行します。
3. インストーラーがマシン上で実行中の現在のバージョンを検出します。
4. **[OK]** を選択して確認し、アップグレードを実行します。 


## <a name="delete-or-unregister-a-configuration-server"></a>構成サーバーを削除または登録解除する

1. 構成サーバーの下にあるすべての VM の[保護を無効化](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)します。
2. 構成サーバーからすべてのレプリケーション ポリシーの[関連付けを解除](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)して、レプリケーション ポリシーを[削除](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)します。
3. 構成サーバーに関連付けられているすべての vCenter サーバー/vSphere ホストを[削除](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)します。
4. コンテナーで、**[Site Recovery インフラストラクチャ]** > **[構成サーバー]** を開きます。
5. 削除する構成サーバーを選択します。 次に、**[詳細]** ページで、**[削除]** を選択します。

    ![構成サーバーを削除する](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### <a name="delete-with-powershell"></a>PowerShell で削除する

必要に応じて、PowerShell を使って構成サーバーを削除できます。

1. Azure PowerShell モジュールを[インストール](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0)します。
2. 次のコマンドを使用して Azure アカウントにサインインします。
    
    `Login-AzureRmAccount`
3. コンテナーのサブスクリプションを選びます。

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  コンテナーのコンテキストを設定します。
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. 構成サーバーを検索します。

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. 構成サーバーを削除します。

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> Remove-AzureRmSiteRecoveryFabric で **-Force** オプションを使うと、構成サーバーを強制的に削除できます。
 


## <a name="renew-ssl-certificates"></a>SSL 証明書を更新する

構成サーバーには Web サーバーが組み込まれていて、この Web サーバーにより、構成サーバーに接続されたモビリティ サービス、プロセス サーバー、マスター ターゲット サーバーのアクティビティが調整されます。 Web サーバーは、SSL 証明書を使ってクライアントを認証します。 証明書は 3 年で有効期限が切れ、いつでも更新できます。

### <a name="check-expiry"></a>有効期限を確認する

2016 年 5 月より前の構成サーバーの展開では、証明書の有効期限は 1 年間に設定されていました。 証明書の有効期限が近づくと、次のようになります。

- 有効期限が 2 か月以内になると、サービスはポータルとメール (Site Recovery 通知に登録している場合) で通知の送信を開始します。
- コンテナーのリソース ページに通知バナーが表示されます。 詳細については、バナーを選択してください。
- **[今すぐアップグレード]** ボタンが表示される場合は、現在の環境にまだ 9.4.xxxx.x 以上のバージョンにアップグレードされていないコンポーネントがあることを示しています。 証明書を更新する前に、コンポーネントをアップグレードしてください。 古いバージョンでは更新できません。

### <a name="renew-the-certificate"></a>証明書を更新する

1. コンテナーで、**[Site Recovery インフラストラクチャ]** > **[構成サーバー]** を開きます。 必要な構成サーバーを選択します。
2. **[Configuration Server の正常性]** に有効期限日が表示されます。
3. **[証明書の更新]** を選択します。 


## <a name="next-steps"></a>次の手順

Azure への [VMware VM](vmware-azure-tutorial.md) のディザスター リカバリーの設定に関するチュートリアルをご覧ください。
