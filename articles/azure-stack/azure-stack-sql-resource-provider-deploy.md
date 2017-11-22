---
title: "Azure Stack での SQL データベースの使用 | Microsoft Docs"
description: "SQL データベースを Azure Stack にサービスとしてデプロイする方法と、SQL Server リソース プロバイダー アダプターの簡単なデプロイ手順について説明します"
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: JeffGo
ms.openlocfilehash: 6e65af68dcd2306aabda65efdf8fe056c0d9b4a4
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2017
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Microsoft Azure Stack で SQL データベースを使用する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

SQL Server リソースプロバイダー アダプターを使用して、SQL データベースを [Azure Stack](azure-stack-poc.md) のサービスとして公開します。 リソースプロバイダーをインストールして 1 つまたは複数の SQL Server インスタンスに接続すると、御社および御社のユーザーは次のものを作成できます。
- クラウド ネイティブ アプリ向けデータベース
- SQL に基づいた Web サイト
- SQL に基づいたワークロード。SQL Server をホストする仮想マシン (VM) を毎回プロビジョニングする必要はありません。

リソース プロバイダーでは、[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) のすべてのデータベース管理機能がサポートされるわけではありません。 たとえば、エラスティック データベース プールと、データベースのパフォーマンスを自動的に増減する機能は使用できません。 ただし、リソース プロバイダーでは、同様の作成、読み取り、更新、および削除 (CRUD) 操作がサポートされます。 API は、SQL DB と互換性がありません。

## <a name="sql-server-resource-provider-adapter-architecture"></a>SQL Server リソース プロバイダー アダプターのアーキテクチャ
リソース プロバイダーは、次の 3 つのコンポーネントで構成されています。

- **SQL リソース プロバイダー アダプター VM**。これはプロバイダー サービスを実行する Windows 仮想マシンです。
- **リソース プロバイダー自体**。これはプロビジョニング要求を処理し、データベース リソースを公開します。
- **SQL Server をホストするサーバー**。これはデータベースに容量を提供し、ホスティング サーバーと呼ばれます。

1 つ (以上) の SQL Server を作成する、または外部 SQL インスタンスへのアクセスを提供する、あるいはその両方が必要です。

## <a name="deploy-the-resource-provider"></a>リソース プロバイダーのデプロイ

1. まだ開発キットを登録していない場合は登録して、Marketplace の管理からダウンロードできる Windows Server 2016 Datacenter Core イメージをダウンロードします。 Windows Server 2016 Core イメージを使用する必要があります。 スクリプトを使用して [Windows Server 2016 イメージ](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)を作成することもできます。必ずコアのオプションを選択してください。 .NET 3.5 ランタイムは必要なくなりました。

2. 特権エンドポイント VM にアクセスできるホストにサインインします。

    a. Azure Stack Development Kit (ASDK) のインストールで、物理ホストにサインインします。

    b. マルチノードのシステムでは、ホストは特権エンドポイントにアクセスできるシステムである必要があります。

3. [SQL リソースプロバイダー バイナリ ファイルをダウンロード](https://aka.ms/azurestacksqlrp)し、自己展開形式ファイルを実行してコンテンツを一時ディレクトリに展開します。

4. Azure Stack のルート証明書は、特権エンドポイントから取得されます。 ASDK には、このプロセスの一環として自己署名証明書が作成されます。 マルチノードの場合は、適切な証明書を提供する必要があります。

    独自の証明書を提供する必要がある場合は、次の証明書が必要です。

    \*.dbadapter.\<region\>.\<external fqdn\> のワイルドカード証明書。 この証明書は、証明機関によって発行されるなど、信頼済みのものである必要があります。 つまり、信頼チェーンが中間証明書の必要なしに存在する必要があります。 単一サイト用証明書は、インストール時に使用される明示的な VM 名 [sqladapter] で使用できます。


5. **新しい**管理者特権の (管理の) PowerShell コンソールを開き、ファイルを抽出したディレクトリに変更します。 新しいウィンドウを使用すると、システムに不適切な PowerShell モジュールが既に読み込まれている場合に発生する可能性のある問題を回避できます。

6. [Azure PowerShell バージョン 1.2.11 をインストールします](azure-stack-powershell-install.md)。

7. 以下に示すパラメーターを指定して DeploySqlProvider.ps1 スクリプトを実行します。

スクリプトでは次の手順が実行されます。

- Azure Stack 上のストレージ アカウントに証明書とその他のアーティファクトをアップロードします。
- ギャラリー パッケージを公開して、ギャラリーを通じて SQL データベースをデプロイできるようにします。
- ホスティング サーバーをデプロイするためにギャラリー パッケージを発行します。
- 手順 1 で作成した Windows Server 2016 イメージを使用して VM をデプロイし、リソース プロバイダーをインストールします。
- リソース プロバイダー VM にマップされるローカル DNS レコードを登録します。
- リソースプロバイダーをローカル Azure Resource Manager (ユーザーと管理者) に登録します。

> [!NOTE]
> インストールに要する時間が 90 分を超える場合は、インストールが失敗し、スクリーンとログ ファイルにエラー メッセージが表示されることがありますが、デプロイは失敗した手順から再試行されます。 推奨されるメモリと vCPU 仕様を満たしていないシステムは、SQL RP をデプロイできないことがあります。
>

PowerShell プロンプトから実行できる例を次に示します (ただし、必要に応じてアカウント情報とパスワードを変更してください)。

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack
$domain = "AzureStack"
# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privileged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files
# and adjust the endpoints
.$tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds -VMLocalCredential $vmLocalAdminCreds -CloudAdminCredential $cloudAdminCreds -PrivilegedEndpoint '10.10.10.10' -DefaultSSLCertificatePassword $PfxPass -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 パラメーター
これらのパラメーターはコマンド ラインで指定できます。 指定しない場合、またはいずれかのパラメーター検証が失敗する場合は、必要なパラメーターの指定を求められます。

| パラメーター名 | Description | コメントまたは既定値 |
| --- | --- | --- |
| **CloudAdminCredential** | 特権エンドポイントへのアクセスに必要な、クラウド管理者の資格情報です。 | _必須_ |
| **AzCredential** | Azure Stack サービス管理者アカウントの資格情報を指定します。 Azure Stack のデプロイに使用したのと同じ資格情報を使用します。 | _必須_ |
| **VMLocalCredential** | SQL リソース プロバイダー VM のローカル Administrator アカウントの資格情報を定義します。 | _必須_ |
| **PrivilegedEndpoint** | 特権エンドポイントの IP アドレスまたは DNS 名を指定します。 |  _必須_ |
| **DependencyFilesLocalPath** | 証明書 PFX ファイルはこのディレクトリにも配置する必要があります。 | _省略可能_ (マルチノードには_必須_) |
| **DefaultSSLCertificatePassword** | .pfx 証明書のパスワード | _必須_ |
| **MaxRetryCount** | 障害がある場合は、各操作を再試行する回数を定義します。| 2 |
| **RetryDuration** | 再試行間のタイムアウトを秒単位で定義します。 | 120 |
| **アンインストール** | リソース プロバイダーおよび関連付けられているすべてのリソース (以下のメモを参照) を削除します | いいえ |
| **DebugMode** | 障害発生時に自動クリーンアップが行われないようにします | いいえ |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Azure Stack ポータルを使用してデプロイを確認する

> [!NOTE]
>  インストール スクリプトが完了したら、ポータルを最新の情報に更新して [管理者] ブレードを表示する必要があります。


1. 管理ポータルにサービス管理者としてサインインします。

2. デプロイが成功したことを確認します。 **[リソース グループ]** &gt; を参照して、**system.\<location\>.sqladapter** リソース グループをクリックし、4 つすべてのデプロイが成功したことを確認します。

      ![SQL RP のデプロイの確認](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)





## <a name="removing-the-sql-adapter-resource-provider"></a>SQL アダプター リソース プロバイダーの削除

リソース プロバイダーを削除するには、最初に依存関係を削除することが重要です。

1. このバージョンのリソース プロバイダーに対してダウンロードした元のデプロイ パッケージがあることを確認します。

2. すべてのユーザー データベースをリソースプロバイダーから削除する必要があります (データは削除されません)。 これは、ユーザー自身で実行する必要があります。

3. 管理者は、SQL アダプターからホスティング サーバーを削除する必要があります

4. 管理者は、SQL アダプターを参照するプランをすべて削除する必要があります。

5. 管理者は、SQL アダプターに関連付けられている SKU とクォータをすべて削除する必要があります。

6. -Uninstall パラメーター、Azure Resource Manager エンドポイント、DirectoryTenantID、サービス管理者アカウントの資格情報を指定してデプロイ スクリプトを再実行します。


## <a name="next-steps"></a>次のステップ


[MySQL Server リソース プロバイダー](azure-stack-mysql-resource-provider-deploy.md)や [App Services リソース プロバイダー](azure-stack-app-service-overview.md)のような他の [PaaS サービス](azure-stack-tools-paas-services.md)を試します。
