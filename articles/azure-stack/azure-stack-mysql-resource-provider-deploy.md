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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e4c3eb1d7dfd4894576d5fbed52cf4de5fed9e44
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938115"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>Azure Stack への MySQL リソース プロバイダーのデプロイ

MySQL サーバー リソース プロバイダーを使用して、MySQL データベースを Azure Stack サービスとして公開します。 MySQL リソース プロバイダーは、Windows Server 2016 Server Core 仮想マシン (VM) 上でサービスとして実行されます。

## <a name="prerequisites"></a>前提条件

Azure Stack MySQL リソース プロバイダーをデプロイする前に、いくつかの前提条件が満たされている必要があります。 これらの要件を満たすには、特権エンドポイント VM にアクセスできるコンピューターでこの記事の手順を実行します。

* まだ登録していない場合は、Azure Marketplace 項目をダウンロードできるよう、Azure に [Azure Stack を登録](.\azure-stack-registration.md)します。
* **Windows Server 2016 Datacenter - Server Core** イメージをダウンロードして、必要な Windows Server Core VM を Azure Stack Marketplace に追加します。 スクリプトを使用して [Windows Server 2016 イメージ](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)を作成することもできます。 スクリプトの実行時にコア オプションを選択するようにしてください。

  >[!NOTE]
  >更新プログラムをインストールする必要がある場合は、ローカルの依存関係のパスに .MSU パッケージを 1 つ配置できます。 複数の .MSU ファイルが見つかった場合、MySQL リソース プロバイダーのインストールは失敗します。

* MySQL リソース プロバイダー バイナリをダウンロードした後、自己展開ツールを実行してコンテンツを一時ディレクトリに展開します。

  >[!NOTE]
  >インターネットにアクセスできないシステムに MySQL プロバイダーをデプロイするには、[mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) ファイルをローカル共有にコピーします。 プロンプトが表示されたら、その共有名を指定します。 Azure および Azure Stack PowerShell モジュールをインストールする必要があります。

* リソース プロバイダーには、対応する最低限の Azure Stack ビルドがあります。 必ず、実行している Azure Stack のバージョンに適切なバイナリをダウンロードしてください。

    | Azure Stack バージョン | MySQL RP バージョン|
    | --- | --- |
    | バージョン 1804 (1.0.180513.1)|[MySQL RP バージョン 1.1.24.0](https://aka.ms/azurestackmysqlrp1804) |
    | バージョン 1802 (1.0.180302.1) | [MySQL RP バージョン 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) |
    | バージョン 1712 (1.0.180102.3 または 1.0.180106.1 (統合システム)) | [MySQL RP バージョン 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |

### <a name="certificates"></a>証明書

ASDK には、このインストール プロセスの一環として自己署名証明書が作成されます。 Azure Stack 統合システムの場合は、適切な証明書を提供する必要があります。 独自の証明書を提供する必要がある場合は、次の条件と合致する **DependencyFilesLocalPath** に .pfx ファイルを配置します。

* \*.dbadapter.\<region\>.\<external fqdn\> のワイルドカード証明書、または mysqladapter.dbadapter.\<region\>.\<external fqdn\> の一般名を持つ 1 つのサイト証明書のどちらか。
* この証明書は信頼できる必要があります。 信頼チェーンが中間証明書の必要なしに存在する必要があります。
* DependencyFilesLocalPath には 1 つの証明書ファイルしか存在しません。
* このファイル名に特殊文字やスペースを含めることはできません。

## <a name="deploy-the-resource-provider"></a>リソース プロバイダーのデプロイ

前提条件がすべてインストールされたら、**DeployMySqlProvider.ps1** スクリプトを実行して MYSQL リソース プロバイダーをデプロイします。 DeployMySqlProvider.ps1 スクリプトは、Azure Stack のバージョンに応じてダウンロードした MySQL リソース プロバイダーのバイナリの一部として展開されます。

> [!IMPORTANT]
> スクリプトを実行しているシステムは、最新バージョンの .NET ランタイムがインストールされている Windows 10 または Windows Server 2016 システムである必要があります。

MySQL リソース プロバイダーをデプロイするには、管理者特権で新しい PowerShell コンソール ウィンドウを開き、MySQL リソース プロバイダーのバイナリ ファイルを展開したディレクトリに移動します。 既に読み込まれている PowerShell モジュールによって発生する可能性のある問題を回避するには、新しい PowerShell ウィンドウを使用することをお勧めします。

**DeployMySqlProvider.ps1** スクリプトを実行して次のタスクを完了します。

* Azure Stack 上のストレージ アカウントに証明書とその他のアーティファクトをアップロードします。
* ギャラリー パッケージを公開して、ギャラリーを使用して MySQL データベースをデプロイできるようにします。
* ホスティング サーバーをデプロイするためのギャラリー パッケージを発行します。
* ダウンロードした Windows Server 2016 Core イメージを使用して VM をデプロイした後、MySQL リソース プロバイダーをインストールします。
* リソース プロバイダー VM にマップされるローカル DNS レコードを登録します。
* リソース プロバイダーをオペレーターとユーザー アカウントのローカルの Azure Resource Manager に登録します。
* 必要に応じて、リソース プロバイダーのインストール時に、1 つの Windows Server の更新プログラムをインストールします。

> [!NOTE]
> MySQL リソース プロバイダーのデプロイが開始され、**system.local.mysqladapter** リソース グループが作成されます。 このリソース グループに必要な 4 つのデプロイが完了するまで最大で 75 分かかる可能性があります。

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 パラメーター

これらのパラメーターをコマンド ラインで指定できます。 必須パラメーターの指定がない場合、またはいずれかのパラメーターの検証が失敗した場合は、指定することを求められます。

| パラメーター名 | 説明 | コメントまたは既定値 |
| --- | --- | --- |
| **CloudAdminCredential** | 特権エンドポイントへのアクセスに必要な、クラウド管理者の資格情報。 | _必須_ |
| **AzCredential** | Azure Stack サービス管理者アカウントの資格情報。 Azure Stack のデプロイに使用したのと同じ資格情報を使用します。 | _必須_ |
| **VMLocalCredential** | MySQL リソースプロバイダー VM のローカル管理者アカウントの資格情報。 | _必須_ |
| **PrivilegedEndpoint** | 特権エンドポイントの IP アドレスまたは DNS 名。 |  _必須_ |
| **DependencyFilesLocalPath** | [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) を格納するローカル共有のパス。 これらのパスのいずれかを提供する場合は、このディレクトリにも証明書ファイルを配置する必要があります。 | 単一ノードの場合は "_省略可能_"、マルチノードの場合は "_必須_"。 |
| **DefaultSSLCertificatePassword** | .pfx 証明書のパスワード。 | _必須_ |
| **MaxRetryCount** | 障害がある場合に各操作を再試行する回数。| 2 |
| **RetryDuration** | 再試行間のタイムアウト間隔 (秒単位)。 | 120 |
| **アンインストール** | リソース プロバイダーと関連付けられているすべてのリソースを削除します (以下のメモを参照してください)。 | いいえ  |
| **DebugMode** | 障害発生時に自動クリーンアップが行われないようにします。 | いいえ  |
| **AcceptLicense** | GPL ライセンスに同意するためのプロンプトをスキップします。  <http://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

> [!NOTE]
> SKU はポータルに表示されるまで最大 1 時間かかることがあります。 SKU がデプロイされて実行されるまで、データベースを作成することはできません。

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>カスタム スクリプトを使用して MySQL リソース プロバイダーをデプロイする

リソース プロバイダーをデプロイする際の手動による構成を除外するには、次のスクリプトをカスタマイズできます。 必要に応じて、Azure Stack のデプロイに適した既定のアカウント情報とパスワードを変更します。

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
# Find the ERCS01 IP address first, and make sure the certificate file is in the specified directory.
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

リソース プロバイダーのインストール スクリプトが終了したら、最新の更新プログラムが表示されるようにブラウザーを最新の情報に更新します。

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Azure Stack ポータルを使用してデプロイを確認する

1. 管理ポータルにサービス管理者としてサインインします。
2. **[リソース グループ]** を選択します
3. **system.\<location\>.mysqladapter** リソース グループを選択します。
4. リソース グループの概要の概要ページで、**[デプロイ]** にメッセージ "**3 成功**" と表示されます。
5. リソース プロバイダーのデプロイの詳細情報は、**[設定]** で確認できます。 **[デプロイ]** を選択すると、各デプロイの状態、タイムスタンプ、期間などの情報を取得できます。

## <a name="next-steps"></a>次の手順

[ホスティング サーバーを追加する](azure-stack-mysql-resource-provider-hosting-servers.md)
