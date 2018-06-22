---
title: Azure Stack での SQL データベースの使用 | Microsoft Docs
description: SQL データベースを Azure Stack にサービスとしてデプロイする方法と、SQL Server リソース プロバイダー アダプターの簡単なデプロイ手順について説明します。
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
ms.date: 05/24/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 8643e75a24ff7840b71dfaceae9934cdda566d30
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604422"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Microsoft Azure Stack で SQL データベースを使用する
Azure Stack SQL Server リソースプロバイダーを使用して、SQL データベースを Azure Stack のサービスとして公開します。 SQL リソース プロバイダー サービスは、Windows Server コア仮想マシンである SQL リソース プロバイダー VM 上で動作します。

## <a name="prerequisites"></a>前提条件
Azure Stack SQL リソース プロバイダーをデプロイする前に、いくつかの前提条件が満たされている必要があります。 特権エンドポイント VM にアクセスできるコンピューターで、次の手順を実行します:

- まだ登録していない場合、Azure Marketplace 項目をダウンロードできるよう、Azure に [Azure Stack を登録](.\azure-stack-registration.md)します。
- **Windows Server 2016 Server コア** イメージをダウンロードして、必要な Windows Server コア VM を Azure Stack Marketplace に追加します。 更新プログラムをインストールする必要がある場合は、ローカルの依存関係のパスに .MSU パッケージを 1 つ配置できます。 複数の .MSU ファイルが見つかった場合、SQL リソース プロバイダーのインストールは失敗します。
- SQL リソース プロバイダー バイナリをダウンロードした後、自己展開形式ファイルを実行してコンテンツを一時ディレクトリに展開します。 リソース プロバイダーには、対応する最低限の Azure Stack ビルドがあります。 必ず、実行している Azure Stack のバージョンに適合する正しいバイナリをダウンロードしてください:

    |Azure Stack バージョン|SQL RP バージョン|
    |-----|-----|
    |バージョン 1804 (1.0.180513.1)|[SQL RP バージョン 1.1.24.0](https://aka.ms/azurestacksqlrp1804)
    |バージョン 1802 (1.0.180302.1)|[SQL RP バージョン 1.1.18.0](https://aka.ms/azurestacksqlrp1802)|
    |バージョン 1712 (1.0.180102.3、1.0.180103.2 または 1.0.180106.1 (統合システム))|[SQL RP バージョン 1.1.14.0](https://aka.ms/azurestacksqlrp1712)|
    |     |     |
- 統合システムのインストールの場合に限り、「[Azure Stack deployment PKI requirements](.\azure-stack-pki-certs.md#optional-paas-certificates)(Azure Stack デプロイの PKI の要件)」の、省略可能な PaaS 証明書に関するセクションで説明されているように、**DependencyFilesLocalPath** パラメーターで指定された場所に .pfx ファイルを配置することによって、SQL PaaS PKI 証明書を提供する必要があります。

## <a name="deploy-the-sql-resource-provider"></a>SQL リソース プロバイダーをデプロイする
すべての前提条件を満たすことによって、SQL リソース プロバイダーをインストールするための準備を正しく行った後、**DeploySqlProvider.ps1** スクリプトを実行して SQL リソース プロバイダーをデプロイできます。 Azure Stack のバージョンに対応する、ダウンロードした SQL リソース プロバイダーのバイナリの一部として DeploySqlProvider.ps1 スクリプトが抽出されます。 

> [!IMPORTANT]
> スクリプトが実行されるシステムは、最新バージョンの .NET ランタイムがインストールされている Windows 10 または Windows Server 2016 システムである必要があります。


SQL リソース プロバイダーをデプロイするには、新しい管理者特権の (管理の) PowerShell コンソールを開き、SQL リソース プロバイダーのバイナリ ファイルを抽出したディレクトリに変更します。

> [!NOTE]
> 新しい PowerShell コンソール ウィンドウを使用すると、システムに不適切な PowerShell モジュールが既に読み込まれている場合に発生する可能性のある問題を回避できます。

DeploySqlProvider.ps1 スクリプトを実行します。このスクリプトは次の手順を実行します:
- Azure Stack 上のストレージ アカウントに証明書とその他のアーティファクトをアップロードします。
- ギャラリー パッケージを発行して、ギャラリーを通じて SQL データベースをデプロイできるようにします。
- ホスティング サーバーをデプロイするためのギャラリー パッケージを発行します。
- 手順 1 で作成した Windows Server 2016 イメージを使用して VM をデプロイした後、リソース プロバイダーをインストールします。
- リソース プロバイダー VM にマップされるローカル DNS レコードを登録します。
- リソース プロバイダーをローカル Azure Resource Manager (ユーザーと管理者) に登録します。
- RP のインストール時に、必要に応じて、1 つの Windows 更新プログラムをインストールします。

SQL リソース プロバイダーのデプロイが開始し、system.local.sqladapter リソース グループを作成します。 このリソース グループへの 4 つの必須デプロイが完了するまで最大で 75 分かかる可能性があります。

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 パラメーター
これらのパラメーターをコマンド ラインで指定できます。 必須パラメーターの指定がない場合、またはいずれかのパラメーターの検証が失敗した場合は、指定することを求められます。

| パラメーター名 | 説明 | コメントまたは既定値 |
| --- | --- | --- |
| **CloudAdminCredential** | 特権エンドポイントへのアクセスに必要な、クラウド管理者の資格情報。 | _必須_ |
| **AzCredential** | Azure Stack サービス管理者アカウントの資格情報。 Azure Stack のデプロイに使用したのと同じ資格情報を使用します。 | _必須_ |
| **VMLocalCredential** | SQL リソース プロバイダー VM のローカル Administrator アカウントの資格情報。 | _必須_ |
| **PrivilegedEndpoint** | 特権エンドポイントの IP アドレスまたは DNS 名。 |  _必須_ |
| **DependencyFilesLocalPath** | 証明書 .pfx ファイルはこのディレクトリにも配置する必要があります。 | _省略可能_ (統合システムでは_必須_) |
| **DefaultSSLCertificatePassword** | .pfx 証明書のパスワード。 | _必須_ |
| **MaxRetryCount** | 障害がある場合に各操作を再試行する回数。| 2 |
| **RetryDuration** | 再試行間のタイムアウト間隔 (秒単位)。 | 120 |
| **アンインストール** | リソース プロバイダーと関連付けられているすべてのリソースを削除します (以下のメモを参照してください)。 | いいえ  |
| **DebugMode** | 障害発生時に自動クリーンアップが行われないようにします。 | いいえ  |

>[!NOTE]
> SKU はポータルに表示されるまで最大 1 時間かかることがあります。 SKU が作成されるまで、データベースを作成できません。


## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>カスタム スクリプトを使用して SQL リソース プロバイダーをデプロイする
DeploySqlProvider.ps1 スクリプトの実行時に必要な情報を手動で入力するのを回避するために、既定のアカウント情報とパスワードを必要に応じて変更することにより、次のスクリプト例をカスタマイズすることができます。

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
$tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert
 ```

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Azure Stack ポータルを使用してデプロイを確認する
このセクションの手順は、SQL リソース プロバイダーが正常にデプロイされたことを確認するために使用できます。

> [!NOTE]
>  インストール スクリプトの実行が終了したら、ポータルを最新の情報に更新して [管理者] ブレードとギャラリー アイテムを表示する必要があります。

1. 管理ポータルにサービス管理者としてサインインします。

2. デプロイが成功したことを確認します。 **リソース グループ**に移動します。 **system.\<location\>.sqladapter** リソース グループを選択します。 4 つのすべてのデプロイが成功したことを確認します。

      ![SQL リソース プロバイダーのデプロイの確認](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="next-steps"></a>次の手順

[ホスティング サーバーを追加する](azure-stack-sql-resource-provider-hosting-servers.md)
