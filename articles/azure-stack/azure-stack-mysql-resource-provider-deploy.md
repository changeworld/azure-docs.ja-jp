---
title: Azure Stack での MySQL データベースの使用 | Microsoft Docs
description: MySQL データベースを Azure Stack にサービスとしてデプロイする方法と、MySQL Server リソース プロバイダー アダプターの簡単なデプロイ手順について説明します。
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 061d9431890622dafafa8c180027b8df735c100e
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296013"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Microsoft Azure Stack で MySQL データベースを使用する
Azure Stack MySQL Server リソースプロバイダーを使用して、MySQL データベースを Azure Stack のサービスとして公開します。 MySQL リソース プロバイダー サービスは、Windows Server コア仮想マシンである MySQL リソース プロバイダー VM 上で動作します。

## <a name="deploy-the-resource-provider"></a>リソース プロバイダーのデプロイ 
1. まだ開発キットを登録していない場合は登録して、Marketplace 管理からダウンロードできる Windows Server 2016 Datacenter Core イメージをダウンロードします。 Windows Server 2016 Core イメージを使用する必要があります。 スクリプトを使用して [Windows Server 2016 イメージ](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)を作成することもできます。 (コア オプションを必ず選択してください) 

2. 特権エンドポイント VM にアクセスできるホストにサインインします。
    - Azure SDK のインストールで、物理ホストにサインインします。  
    - 統合システムでは、ホストは特権エンドポイントにアクセスできるシステムである必要があります。 
    
    >[!NOTE] 
    > スクリプトが実行されるシステムは、最新バージョンの .NET ランタイムがインストールされている Windows 10 または Windows Server 2016 システムである*必要があります*。 それ以外の場合、インストールは失敗します。 Azure Stack ASDK ホストはこの条件を満たしています。 
    
3. MySQL リソース プロバイダー バイナリをダウンロードします。 次に、自己解凍ツールを実行して、その内容を一時ディレクトリに抽出します。 
    >[!NOTE]  
    > リソース プロバイダーには、対応する最低限の Azure Stack ビルドがあります。 必ず実行されている Azure Stack のバージョンに適合する正しいバイナリをダウンロードしてください。

    | Azure Stack バージョン | MySQL RP バージョン| 
    | --- | --- | 
    | バージョン 1804 (1.0.180513.1)|[MySQL RP バージョン 1.1.24.0](https://aka.ms/azurestackmysqlrp1804) | 
    | バージョン 1802 (1.0.180302.1) | [MySQL RP バージョン 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) | 
    | バージョン 1712 (1.0.180102.3 または 1.0.180106.1 (統合システム)) | [MySQL RP バージョン 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) | 

4.  ASDK には、このプロセスの一環として自己署名証明書が作成されます。 統合システムの場合は、適切な証明書を提供する必要があります。 独自の証明書を提供する必要がある場合は、次の条件と合致する **DependencyFilesLocalPath** に .pfx ファイルを配置します。 
    - \*.dbadapter.\<region\>.\<external fqdn\> のワイルドカード証明書、または mysqladapter.dbadapter.\<region\>.\<external fqdn\> の一般名を持つ 1 つのサイト証明書のどちらか。 
    - この証明書は信頼できる必要があります。 つまり、信頼チェーンが中間証明書の必要なしに存在する必要があります。 
    - DependencyFilesLocalPath には 1 つの証明書ファイルしか存在しません。 
    - このファイル名に特殊文字やスペースを含めることはできません。 

5. **新しい**管理者特権 (管理) PowerShell コンソールを開きます。 ファイルを展開したディレクトリに変更します。 新しいウィンドウを使用すると、システムに不適切な PowerShell モジュールが既に読み込まれている場合に発生する可能性のある問題を回避できます。 

6. **DeployMySqlProvider.ps1** スクリプトを実行します。 スクリプトでは次の手順が実行されます。 
    - MySQL コネクタ バイナリをダウンロードします (これはオフラインで提供できます)。 
    - Azure Stack 上のストレージ アカウントに証明書とその他のアーティファクトをアップロードします。 
    - ギャラリー パッケージを発行して、ギャラリーを通じて SQL データベースをデプロイできるようにします。 
    - ホスティング サーバーをデプロイするためのギャラリー パッケージを発行します。 
    - Windows Server 2016 Azure Stack マーケットプレース イメージを使用して VM をデプロイし、リソース プロバイダーをインストールします。 
    - リソース プロバイダー VM にマップされるローカル DNS レコードを登録します。 
    - リソース プロバイダーをローカル Azure Resource Manager (テナントと管理者) に登録します。 

    コマンド ラインで必要なパラメーターを指定するか、パラメーターを指定せずにスクリプトを実行して、プロンプトが表示されたら入力することができます。 

    PowerShell プロンプトから実行できる例を示します。 必要に応じてアカウント情報とパスワードを変更してください。 

    ```powershell 
    # Install the AzureRM.Bootstrapper module and set the profile. 
    Install-Module -Name AzureRm.BootStrapper -Force 
    Use-AzureRmProfile -Profile 2017-03-09-profile 

    # Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
    $domain = "AzureStack"  

    # For integrated systems, use the IP address of one of the ERCS virtual machines 
    $privilegedEndpoint = "AzS-ERCS01" 

    # Point to the directory where the resource provider installation files were extracted. 
    $tempDir = 'C:\TEMP\MYSQLRP' 

    # The service admin account (can be Azure Active Directory or Active Directory Federation Services). 
    $serviceAdmin = "admin@mydomain.onmicrosoft.com" 
    $AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
    $AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 

    # Set the credentials for the new resource provider VM local administrator account 
    $vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
    $vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass) 

    # And the cloudadmin credential required for privileged endpoint access. 
    $CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

    # Change the following as appropriate. 
    $PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 

    # Run the installation script from the folder where you extracted the installation files. 
    # Find the ERCS01 IP address first, and make sure the certificate 
    # file is in the specified directory. 
    $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds ` 
        -VMLocalCredential $vmLocalAdminCreds ` 
        -CloudAdminCredential $cloudAdminCreds ` 
        -PrivilegedEndpoint $privilegedEndpoint ` 
        -DefaultSSLCertificatePassword $PfxPass ` 
        -DependencyFilesLocalPath $tempDir\cert ` 
        -AcceptLicense 
    ``` 

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 パラメーター 
これらのパラメーターをコマンド ラインで指定できます。 必須パラメーターの指定がない場合、またはいずれかのパラメーターの検証が失敗した場合は、指定することを求められます。 

| パラメーター名 | 説明 | コメントまたは既定値 | 
| --- | --- | --- | 
| **CloudAdminCredential** | 特権エンドポイントへのアクセスに必要な、クラウド管理者の資格情報。 | _必須_ | 
| **AzCredential** | Azure Stack サービス管理者アカウントの資格情報。 Azure Stack のデプロイに使用したのと同じ資格情報を使用します。 | _必須_ | 
| **VMLocalCredential** | MySQL リソースプロバイダー VM のローカル管理者アカウントの資格情報。 | _必須_ | 
| **PrivilegedEndpoint** | 特権エンドポイントの IP アドレスまたは DNS 名。 |  _必須_ | 
| **DependencyFilesLocalPath** | [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) を格納するローカル共有のパス。 これらのパスのいずれかを提供する場合は、このディレクトリにも証明書ファイルを配置する必要があります。 | _省略可能_ (マルチノードでは_必須_) | 
| **DefaultSSLCertificatePassword** | .pfx 証明書のパスワード。 | _必須_ | 
| **MaxRetryCount** | 障害がある場合に各操作を再試行する回数。| 2 | 
| **RetryDuration** | 再試行間のタイムアウト間隔 (秒単位)。 | 120 | 
| **アンインストール** | リソース プロバイダーと関連付けられているすべてのリソースを削除します (以下のメモを参照してください)。 | いいえ  | 
| **DebugMode** | 障害発生時に自動クリーンアップが行われないようにします。 | いいえ  | 
| **AcceptLicense** | GPL ライセンスに同意するためのプロンプトをスキップします。  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

> [!NOTE] 
> SKU はポータルに表示されるまで最大 1 時間かかることがあります。 SKU が作成されるまで、データベースを作成できません。 

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Azure Stack ポータルを使用してデプロイを確認する 
> [!NOTE] 
>  インストール スクリプトの実行が終了したら、ポータルを最新の情報に更新して [管理者] ブレードを表示する必要があります。 

1. 管理ポータルにサービス管理者としてサインインします。 
2. デプロイが成功したことを確認します。 **[リソース グループ]** に移動し、**[system.\<場所\>.mysqladapter]** リソース グループを選択します。 4 つのすべてのデプロイが成功したことを確認します。

      ![MySQL RP のデプロイを確認する](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png) 


## <a name="next-steps"></a>次の手順
[ホスティング サーバーを追加する](azure-stack-mysql-resource-provider-hosting-servers.md)
