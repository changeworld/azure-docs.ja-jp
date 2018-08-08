---
title: Azure Site Recovery での VMware ディザスター リカバリーのために構成サーバーを管理する | Microsoft Docs
description: この記事では、Azure Site Recovery で Azure への VMware ディザスター リカバリー用に既存の構成サーバーを管理する方法について説明します。
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: df5b2ecce2a5c9d7c263ee0acc3a49b859b93f7f
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346122"
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>VMware VM 用の構成サーバーの管理

Azure への VMware 仮想マシンと物理サーバーのディザスター リカバリーに [Azure Site Recovery](site-recovery-overview.md) を使うときは、オンプレミスの構成サーバーを設定します。 構成サーバーは、オンプレミスの VMware と Azure の間の通信を調整し、データのレプリケーションを管理します。 この記事は、展開後に構成サーバーを管理するための一般的なタスクをまとめたものです。



## <a name="modify-vmware-settings"></a>VMware の設定を変更する

次のように構成サーバーにアクセスすることができます。
    - デプロイ先の VM にサインインし、デスクトップのショートカットから Azure Site Recovery 構成マネージャーを起動します。
    - または、**https://*ConfigurationServerName*/:44315/** から構成サーバーにリモートでアクセスできます。 管理者の資格情報でサインインします。
   
### <a name="modify-vmware-server-settings"></a>VMware サーバーの設定を変更する

1. 別の VMware サーバーを構成サーバーと関連付けるには、サインイン後に **[vCenter Server/vSphere ESXi サーバーの追加]** を選択します。
2. 詳細を入力し、**[OK]** を選択します。


### <a name="modify-credentials-for-automatic-discovery"></a>自動検出用の資格情報を変更する

1. VMware VM の自動検出のための VMware サーバーへの接続に使う資格情報を更新するには、サインイン後に **[編集]** を選択します。
2. 新しい資格情報を入力して、**[OK]** を選択します。

    ![VMware の変更](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)


## <a name="modify-credentials-for-mobility-service-installation"></a>モビリティ サービス インストール用の資格情報を変更する

レプリケーションを有効にする VMware VM にモビリティ サービスを自動インストールするために使う資格情報を変更します。

1. サインイン後に、**[仮想マシンの資格情報の管理]** を選択します
2. 新しい資格情報を入力して、**[OK]** を選択します。

    ![モビリティ サービスの資格情報を変更する](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>プロキシの設定を変更する

Azure へのインターネット アクセスのために構成サーバー マシンが使うプロキシの設定を変更します。 構成サーバー マシンで実行されている既定のプロセス サーバーだけでなく、プロセス サーバー マシンがある場合は、両方のマシンで設定を変更します。

1. 構成サーバーにサインインした後で、**[接続の管理]** を選択します。
2. プロキシの値を更新します。 次に、**[保存]** を選択して、設定を更新します。

## <a name="add-a-network-adapter"></a>ネットワーク アダプターを追加する

Open Virtualization Format (OVF) テンプレートは、ネットワーク アダプターが 1 つの構成サーバー VM を展開します。

- [VM にさらにアダプターを追加する](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter)ことはできますが、構成サーバーをコンテナーに登録する前に追加する必要があります。
- 構成サーバーをコンテナーに登録した後でアダプターを追加するには、VM のプロパティでアダプターを追加します。 次に、サーバーをコンテナーに再登録する必要があります。


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>同じコンテナーに構成サーバーを登録する

必要な場合は、同じコンテナーに構成サーバーを再登録することができます。 構成サーバー マシンで実行されている既定のプロセス サーバーだけでなく、他のプロセス サーバー マシンがある場合は、両方のマシンを再登録します。


  1. コンテナーで、**[管理]** > **[Site Recovery インフラストラクチャ]** > **[構成サーバー]** を開きます。
  2. **[サーバー]** で **[登録キーのダウンロード]** を選択して、コンテナーの資格情報ファイルをダウンロードします。
  3. 構成サーバー マシンにサインインします。
  4. **%ProgramData%\ASR\home\svsystems\bin** で、**cspsconfigtool.exe** を開きます。
  5. **[Vault Registration]\(コンテナーの登録\)** タブで **[参照]** を選択して、ダウンロードしたコンテナー資格情報ファイルを探します。
  6. 必要な場合は、プロキシ サーバーの詳細を指定します。 次に、**[登録]** を選択します。
  7. 管理者の PowerShell コマンド ウィンドウを開き、次のコマンドを実行します。
   ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

      >[!NOTE] 
      >構成サーバーからスケール アウト プロセス サーバーに**最新の証明書を取得する**には、コマンド *“<Installation Drive\Microsoft Azure Site Recovery\agent\cdpcli.exe>" --registermt* を実行します

  8. 最後に、次のコマンドを実行して obengine を再起動します。
  ```
          net stop obengine
          net start obengine

## Upgrade the configuration server

You run update rollups to update the configuration server. Updates can be applied for up to N-4 versions. For example:

- If you run 9.7, 9.8, 9.9, or 9.10, you can upgrade directly to 9.11.
- If you run 9.6 or earlier and you want to upgrade to 9.11, you must first upgrade to version 9.7. before 9.11.

Links to update rollups for upgrading to all versions of the configuration server are available in the [wiki updates page](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Upgrade the server as follows:

1. In the vault, go to **Manage** > **Site Recovery Infrastructure** > **Configuration Servers**.
2. If an update is available, a link appears in the **Agent Version** > column.
    ![Update](./media/vmware-azure-manage-configuration-server/update2.png)
3. Download the update installer file to the configuration server.

    ![Update](./media/vmware-azure-manage-configuration-server/update1.png)

4. Double-click to run the installer.
5. The installer detects the current version running on the machine. Click **Yes** to start the upgrade.
6. When the upgrade completes the server configuration validates.

    ![Update](./media/vmware-azure-manage-configuration-server/update3.png)
    
7. Click **Finish** to close the installer.

## Delete or unregister a configuration server

1. [Disable protection](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) for all VMs under the configuration server.
2. [Disassociate](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) and [delete](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) all replication policies from the configuration server.
3. [Delete](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) all vCenter servers/vSphere hosts that are associated with the configuration server.
4. In the vault, open **Site Recovery Infrastructure** > **Configuration Servers**.
5. Select the configuration server that you want to remove. Then, on the **Details** page, select **Delete**.

    ![Delete configuration server](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### Delete with PowerShell

You can optionally delete the configuration server by using PowerShell.

1. [Install](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) the Azure PowerShell module.
2. Sign in to your Azure account by using this command:
    
    `Connect-AzureRmAccount`
3. Select the vault subscription.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Set the vault context.
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault> Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Retrieve the configuration server.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Delete the configuration server.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> You can use the **-Force** option in Remove-AzureRmSiteRecoveryFabric for forced deletion of the configuration server.
 
## Generate configuration server Passphrase

1. Sign in to your configuration server, and then open a command prompt window as an administrator.
2. To change the directory to the bin folder, execute the command **cd %ProgramData%\ASR\home\svsystems\bin**
3. To generate the passphrase file, execute **genpassphrase.exe -v > MobSvc.passphrase**.
4. Your passphrase will be stored in the file located at **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## Renew SSL certificates

The configuration server has an inbuilt web server, which orchestrates activities of the Mobility Service, process servers, and master target servers connected to it. The web server uses an SSL certificate to authenticate clients. The certificate expires after three years and can be renewed at any time.

### Check expiry

For configuration server deployments before May 2016, certificate expiry was set to one year. If you have a certificate that is going to expire, the following occurs:

- When the expiry date is two months or less, the service starts sending notifications in the portal, and by email (if you subscribed to Site Recovery notifications).
- A notification banner appears on the vault resource page. For more information, select the banner.
- If you see an **Upgrade Now** button, it indicates that some components in your environment haven't been upgraded to 9.4.xxxx.x or higher versions. Upgrade the components before you renew the certificate. You can't renew on older versions.

### Renew the certificate

1. In the vault, open **Site Recovery Infrastructure** > **Configuration Server**. Select the required configuration server.
2. The expiry date appears under **Configuration Server health**.
3. Select **Renew Certificates**. 


## Next steps

Review the tutorials for setting up disaster recovery of [VMware VMs](vmware-azure-tutorial.md) to Azure.
