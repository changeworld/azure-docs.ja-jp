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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e1505761a0bd1ea9dabdd0b2cbab7af902198311
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938334"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>SQL Server リソース プロバイダーを Azure Stack にデプロイする

Azure Stack SQL Server リソース プロバイダーを使用して、SQL データベースを Azure Stack サービスとして公開します。 SQL リソース プロバイダーは、Windows Server 2016 Server Core 仮想マシン (VM) 上でサービスとして実行されます。

## <a name="prerequisites"></a>前提条件

Azure Stack SQL リソース プロバイダーをデプロイする前に、いくつかの前提条件が満たされている必要があります。 これらの要件を満たすには、特権エンドポイント VM にアクセスできるコンピューターで次の手順を実行します。

- まだ登録していない場合は、Azure Marketplace 項目をダウンロードできるよう、Azure に [Azure Stack を登録](.\azure-stack-registration.md)します。
- **Windows Server 2016 Datacenter - Server Core** イメージをダウンロードして、必要な Windows Server Core VM を Azure Stack Marketplace に追加します。 スクリプトを使用して [Windows Server 2016 イメージ](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)を作成することもできます。 スクリプトの実行時にコア オプションを選択するようにしてください。

  >[!NOTE]
  >更新プログラムをインストールする必要がある場合は、1 つの MSU パッケージをローカルの依存関係のパスに配置できます。 複数の MSU ファイルが見つかった場合、SQL リソース プロバイダーのインストールに失敗します。

- SQL リソース プロバイダー バイナリをダウンロードした後、自己展開ツールを実行してコンテンツを一時ディレクトリに抽出します。 リソース プロバイダーには、対応する最低限の Azure Stack ビルドがあります。 必ず、実行している Azure Stack のバージョンに適切なバイナリをダウンロードしてください。

    |Azure Stack バージョン|SQL RP バージョン|
    |-----|-----|
    |バージョン 1804 (1.0.180513.1)|[SQL RP バージョン 1.1.24.0](https://aka.ms/azurestacksqlrp1804)
    |バージョン 1802 (1.0.180302.1)|[SQL RP バージョン 1.1.18.0](https://aka.ms/azurestacksqlrp1802)|
    |バージョン 1712 (1.0.180102.3、1.0.180103.2 または 1.0.180106.1 (統合システム))|[SQL RP バージョン 1.1.14.0](https://aka.ms/azurestacksqlrp1712)|
    |     |     |

### <a name="certificates"></a>証明書

統合システムのインストールのみを対象。 [Azure Stack のデプロイの PKI 要件](.\azure-stack-pki-certs.md#optional-paas-certificates)に関するページの「オプションの PaaS 証明書」セクションで説明されている SQL PaaS PKI 証明書を指定する必要があります。 **DependencyFilesLocalPath** パラメーターで指定された場所に .pfx ファイルを配置します。

## <a name="deploy-the-sql-resource-provider"></a>SQL リソース プロバイダーをデプロイする

前提条件がすべてインストールされたら、**DeploySqlProvider.ps1** スクリプトを実行して SQL リソース プロバイダーをデプロイします。 DeploySqlProvider.ps1 スクリプトは、Azure Stack のバージョンに応じてダウンロードした SQL リソース プロバイダーのバイナリの一部として抽出されます。

> [!IMPORTANT]
> スクリプトを実行しているシステムは、最新バージョンの .NET ランタイムがインストールされている Windows 10 または Windows Server 2016 システムである必要があります。

SQL リソース プロバイダーをデプロイするには、管理者特権で**新しい** PowerShell コンソールを開き、SQL リソース プロバイダーのバイナリ ファイルを抽出したディレクトリに変更します。 既に読み込まれている PowerShell モジュールによって発生する可能性のある問題を回避するには、新しい PowerShell ウィンドウを使用することをお勧めします。

DeploySqlProvider.ps1 スクリプトを実行すると、次のタスクが完了します。

- Azure Stack 上のストレージ アカウントに証明書とその他のアーティファクトをアップロードします。
- ギャラリー パッケージを発行して、ギャラリーを使用して SQL データベースをデプロイできるようにします。
- ホスティング サーバーをデプロイするためのギャラリー パッケージを発行します。
- ダウンロードした Windows Server 2016 Core イメージを使用して VM をデプロイした後、SQL リソース プロバイダーをインストールします。
- リソース プロバイダー VM にマップされるローカル DNS レコードを登録します。
- リソース プロバイダーをオペレーターとユーザー アカウントのローカルの Azure Resource Manager に登録します。
- 必要に応じて、リソース プロバイダーのインストール時に、1 つの Windows Server の更新プログラムをインストールします。

> [!NOTE]
> SQL リソース プロバイダーのデプロイが開始されると、**system.local.sqladapter** リソース グループが作成されます。 このリソース グループに対して必要な 4 つのデプロイが完了するまで最大で 75 分かかる可能性があります。

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 パラメーター

コマンド ラインから次のパラメーターを指定できます。 必須パラメーターの指定がない場合、またはいずれかのパラメーターの検証が失敗した場合は、指定することを求められます。

| パラメーター名 | 説明 | コメントまたは既定値 |
| --- | --- | --- |
| **CloudAdminCredential** | 特権エンドポイントへのアクセスに必要な、クラウド管理者の資格情報。 | _必須_ |
| **AzCredential** | Azure Stack サービス管理者アカウントの資格情報。 Azure Stack のデプロイに使用したのと同じ資格情報を使用します。 | _必須_ |
| **VMLocalCredential** | SQL リソース プロバイダー VM のローカル Administrator アカウントの資格情報。 | _必須_ |
| **PrivilegedEndpoint** | 特権エンドポイントの IP アドレスまたは DNS 名。 |  _必須_ |
| **DependencyFilesLocalPath** | 証明書 .pfx ファイルはこのディレクトリにも配置する必要があります。 | _省略可能_ (統合システムでは_必須_) |
| **DefaultSSLCertificatePassword** | .pfx 証明書のパスワード。 | _必須_ |
| **MaxRetryCount** | エラーが 発生した場合に各操作を再試行する回数。| 2 |
| **RetryDuration** | 再試行間のタイムアウト間隔 (秒単位)。 | 120 |
| **アンインストール** | リソース プロバイダーと関連付けられているすべてのリソースを削除します (以下のメモを参照してください)。 | いいえ  |
| **DebugMode** | 障害発生時に自動クリーンアップが行われないようにします。 | いいえ  |

>[!NOTE]
> SKU はポータルに表示されるまで最大 1 時間かかることがあります。 SKU がデプロイされて実行されるまで、データベースを作成することはできません。

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>カスタム スクリプトを使用して SQL リソース プロバイダーをデプロイする

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
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory If folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

リソース プロバイダーのインストール スクリプトが終了したら、最新の更新プログラムが表示されるようにブラウザーを最新の情報に更新します。

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Azure Stack ポータルを使用してデプロイを確認する

次の手順を実行すると、SQL リソース プロバイダーが正常にデプロイされたことを確認できます。

1. 管理ポータルにサービス管理者としてサインインします。
2. **[リソース グループ]** を選択します。
3. **system.\<location\>.sqladapter** リソース グループを選択します。
4. 次の画面キャプチャに示されている、**[デプロイ]** の下にあるメッセージは **[4 Succeeded]\(4 成功しました\)** となります。

      ![SQL リソース プロバイダーのデプロイの確認](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)

5. リソース プロバイダーのデプロイの詳細情報は、**[設定]** で確認できます。 **[デプロイ]** を選択すると、各デプロイの状態、タイムスタンプ、期間などの情報を取得できます。

## <a name="next-steps"></a>次の手順

[ホスティング サーバーを追加する](azure-stack-sql-resource-provider-hosting-servers.md)
