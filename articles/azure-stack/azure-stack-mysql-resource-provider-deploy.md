---
title: "Azure Stack で MySQL データベースを PaaS として使用する | Microsoft Docs"
description: "Azure Stack で MySQL リソース プロバイダーをデプロイし、MySQL データベースをサービスとして提供する方法を説明します"
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
ms.date: 11/29/2017
ms.author: JeffGo
ms.openlocfilehash: e1752bfe40fb53568b79e2b7eec56ca9f3139d4c
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2017
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Microsoft Azure Stack で MySQL データベースを使用する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack に MySQL リソース プロバイダーをデプロイできます。 リソース プロバイダーをデプロイしたら、Azure Resource Manager デプロイ テンプレートを使用して MySQL サーバーおよびデータベースを作成し、MySQL データベースをサービスとして提供できます。 MySQL データベースは、Web サイトで一般的であり、多くの Web サイト プラットフォームをサポートしています。 たとえば、リソース プロバイダーをデプロイした後に、Azure Stack のサービスとしての Azure Web Apps プラットフォーム (PaaS) アドオンから WordPress Web サイトを作成できます。

インターネットにアクセスできないシステムに MySQL プロバイダーをデプロイするには、[mysql-connector-net-6.9.9.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.9.9.msi) ファイルをローカル共有にコピーします。 そして、プロンプトが表示されたら、その共有名を指定します。 Azure および Azure Stack PowerShell モジュールもインストールする必要があります。


## <a name="mysql-server-resource-provider-adapter-architecture"></a>MySQL サーバー リソース プロバイダー アダプターのアーキテクチャ

リソース プロバイダーは、次の 3 つのコンポーネントで構成されています。

- **MySQL リソース プロバイダー アダプター VM**。これはプロバイダー サービスを実行する Windows 仮想マシンです。
- **リソース プロバイダー自体**。これはプロビジョニング要求を処理し、データベース リソースを公開します。
- **MySQL サーバーをホストするサーバー**。これはデータベースに容量を提供し、ホスティング サーバーと呼ばれます。

このリリースでは、MySQL インスタンスは作成されなくなりました。 それらを作成するか、外部 SQL インスタンスへのアクセスを提供する必要があります。 [Azure Stack Quickstart Gallery](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) では、以下のことができるテンプレートの例をご覧いただけます。
- 自分の MySQL サーバーを作成する
- Marketplace から MySQL Server をダウンロードし、デプロイします。

> [!NOTE]
> マルチノードの Azure Stack にインストールされるホスティング サーバーは、テナント サブスクリプションから作成する必要があります。 既定のプロバイダー サブスクリプションからは作成できません。 つまり、テナント ポータルから、または適切なログインによる PowerShell セッションから作成する必要があります。 すべてのホスティング サーバーは課金対象の仮想マシンであり、適切なライセンスを必要とします。 サービス管理者は、そのサブスクリプションの所有者になることができます。

### <a name="required-privileges"></a>必要な特権
システム アカウントには、次の特権が必要です。

1.  データベース: 作成、ドロップ
2.  ログイン: 作成、設定、ドロップ、許可、取り消し

## <a name="deploy-the-resource-provider"></a>リソース プロバイダーのデプロイ

1. まだ開発キットを登録していない場合は登録して、Marketplace の管理からダウンロードできる Windows Server 2016 Datacenter Core イメージをダウンロードします。 Windows Server 2016 Core イメージを使用する必要があります。 スクリプトを使用して [Windows Server 2016 イメージ](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)を作成することもできます。必ずコアのオプションを選択してください。 .NET 3.5 ランタイムは必要なくなりました。


2. 特権エンドポイント VM にアクセスできるホストにサインインします。

    a. Azure Stack Development Kit (ASDK) のインストールで、物理ホストにサインインします。

    b. マルチノードのシステムでは、ホストは特権エンドポイントにアクセスできるシステムである必要があります。

3. MySQL リソース プロバイダー バイナリをダウンロードし、自己展開形式ファイルを実行してコンテンツを一時ディレクトリに展開します。

    >[!NOTE] 
    > リソース プロバイダーのビルドは、Azure Stack のビルドに対応します。 実行されている Azure Stack のバージョンに適合する正しいバイナリをダウンロードする必要があります。

    | Azure Stack ビルド | MySQL RP インストーラー |
    | --- | --- |
    | 1.0.171122.1 | [MySQL RP バージョン 1.1.10.0](https://aka.ms/azurestackmysqlrp) |
    | 1.0.171028.1 | [MySQL RP バージョン 1.1.8.0](https://aka.ms/azurestackmysqlrp1710) |
    | 1.0.170928.3 | [MySQL RP バージョン 1.1.3.0](https://aka.ms/azurestackmysqlrp1709) |

4.  Azure Stack のルート証明書は、特権エンドポイントから取得されます。 ASDK には、このプロセスの一環として自己署名証明書が作成されます。 マルチノードの場合は、適切な証明書を提供する必要があります。

    独自の証明書を提供する必要がある場合は、次の証明書が必要です。

    \*.dbadapter.\<region\>.\<external fqdn\> のワイルドカード証明書。 この証明書は、証明機関によって発行されるなど、信頼済みのものである必要があります。 つまり、信頼チェーンが中間証明書の必要なしに存在する必要があります。 1 つのサイト証明書は、インストール時に使用される明示的な VM 名 [mysqladapter] で使用できます。


5. **新しい**管理者特権の (管理の) PowerShell コンソールを開き、ファイルを抽出したディレクトリに変更します。 新しいウィンドウを使用すると、システムに不適切な PowerShell モジュールが既に読み込まれている場合に発生する可能性のある問題を回避できます。

6. [Azure PowerShell バージョン 1.2.11 をインストールします](azure-stack-powershell-install.md)。

7. DeploySqlProvider.ps1 スクリプトを実行します。

スクリプトでは次の手順が実行されます。

* MySQL コネクタ バイナリをダウンロードします (これはオフラインで提供できます)。
* Azure Stack 上のストレージ アカウントに証明書とその他のアーティファクトをアップロードします。
* ギャラリー パッケージを公開して、ギャラリーを通じて SQL データベースをデプロイできるようにします。
* ホスティング サーバーをデプロイするためにギャラリー パッケージを発行します。
* 手順 1 で作成した Windows Server 2016 イメージを使用して VM をデプロイし、リソース プロバイダーをインストールします。
* リソース プロバイダー VM にマップされるローカル DNS レコードを登録します。
* リソース プロバイダーをローカル Azure Resource Manager (テナントと管理者) に登録します。


次のようにすることができます。
- コマンドラインで、少なくとも、必須のパラメーターを指定する
- または、パラメーターなしで実行する場合は、入力を求められたら入力します。

PowerShell プロンプトから実行できる例を次に示します (ただし、必要に応じてアカウント情報とパスワードを変更してください)。


```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack and the default prefix is AzS
# For integrated systems, the domain and the prefix will be the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privleged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files
# Find the ERCS01 IP address first and make sure the certificate
# file is in the specified directory
. $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```


### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 パラメーター
これらのパラメーターはコマンド ラインで指定できます。 指定しない場合、またはいずれかのパラメーター検証が失敗する場合は、必要なパラメーターの指定を求められます。

| パラメーター名 | 説明 | コメントまたは既定値 |
| --- | --- | --- |
| **CloudAdminCredential** | 特権エンドポイントへのアクセスに必要な、クラウド管理者の資格情報です。 | _必須_ |
| **AzCredential** | Azure Stack サービス管理者アカウントの資格情報を指定します。 Azure Stack のデプロイに使用したのと同じ資格情報を使用します。 | _必須_ |
| **VMLocalCredential** | MySQL リソースプロバイダー VM のローカル管理者アカウントの資格情報を定義します。 | _必須_ |
| **PrivilegedEndpoint** | 特権エンドポイントの IP アドレスまたは DNS 名を指定します。 |  _必須_ |
| **DependencyFilesLocalPath** | [mysql-connector-net-6.9.9.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.9.9.msi) を格納するローカル共有のパス。 これを提供する場合は、証明書ファイルもこのディレクトリに配置する必要があります。 | _省略可能_ (マルチノードには_必須_) |
| **DefaultSSLCertificatePassword** | .pfx 証明書のパスワード | _必須_ |
| **MaxRetryCount** | 障害がある場合は、各操作を再試行する回数を定義します。| 2 |
| **RetryDuration** | 再試行間のタイムアウトを秒単位で定義します。 | 120 |
| **アンインストール** | リソース プロバイダーおよび関連付けられているすべてのリソース (以下のメモを参照) を削除します | いいえ |
| **DebugMode** | 障害発生時に自動クリーンアップが行われないようにします | いいえ |
| **AcceptLicense** | プロンプトをスキップして、GPL ライセンス (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) に同意します | |


システムのパフォーマンスとダウンロード速度によっては、インストールに 20分しかかからない場合も、数時間にも及ぶことがあります。 MySQLAdapter ブレードを使用できない場合は、管理ポータルを最新の情報に更新してください。

> [!NOTE]
> インストールに要する時間が 90 分を超える場合は、インストールが失敗している可能性があり、画面とログ ファイルにエラー メッセージが表示されることがあります。 失敗した手順からデプロイが再試行されます。 推奨されるメモリとコア仕様を満たしていないシステムは、MySQL RP をデプロイできないことがあります。



## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Azure Stack ポータルを使用してデプロイを確認する

> [!NOTE]
>  インストール スクリプトが完了したら、ポータルを最新の情報に更新して [管理者] ブレードを表示する必要があります。


1. 管理ポータルにサービス管理者としてサインインします。

2. デプロイが成功したことを確認します。 **[リソース グループ]** &gt; を参照して **system.\<location\>.mysqladapter** リソース グループをクリックし、4 つすべてのデプロイが成功したことを確認します。

      ![MySQL RP デプロイの確認](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>MySQL ホスティング サーバに接続して容量を提供する

1. Azure Stack ポータルにサービス管理者としてサインインします。

2. **[管理リソース]** &gt; **[MySQL Hosting Servers]\(MySQL ホスティング サーバー\)** &gt; **[+Add]\(+追加\)** の順に参照します。

    **[MySQL Hosting Servers] \(MySQL ホスティング サーバー)** ブレードでは、リソース プロバイダーのバックエンドとして機能する MySQL サーバーの実際のインスタンスに MySQL サーバー リソース プロバイダーを接続できます。

    ![ホスティング サーバー](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. MySQL サーバー インスタンスの接続詳細をフォームに入力します。 短い VM 名ではなく、完全修飾ドメイン名 (FQDN) または有効な IPv4 アドレスを指定します。 このインストールでは、既定の MySQL インスタンスが提供されなくなりました。 指定したサイズは、リソース プロバイダーがデータベース容量を管理するのに役立ちます。 それはデータベース サーバーの物理容量に近い値にする必要があります。

    > [!NOTE]
    > MySQL インスタンスがテナントと管理者の Azure Resource Manager によってアクセスできる限り、リソース プロバイダーの管理下に置くことができます。 MySQL インスタンスは RP に排他的に割り当てられている__必要があります__。

4. サーバーを追加する際に、サービス内容を区別できるように新規または既存の SKU に割り当てる必要があります。
  たとえば、次のものを提供するエンタープライズ インスタンスを持つことができます。
    - データベース容量
    - 自動バックアップ
    - 各部門のための高性能なサーバーの予約
 

SKU 名は、テナントが各自のデータベースを適切に配置できるように、プロパティを表している必要があります。 SKU 内のすべてのホスティング サーバーの機能が同じである必要があります。

![MySQL SKU を作成する](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)


>[!NOTE]
> SKU はポータルに表示されるまで最大 1 時間かかることがあります。 SKU が作成されるまで、データベースを作成できません。


## <a name="to-test-your-deployment-create-your-first-mysql-database"></a>デプロイをテストするために最初の MySQL データベースを作成する


1. Azure Stack ポータルにサービス管理者としてサインインします。

2. **[新規]** ボタン &gt; **[データ + ストレージ]** &gt; **[MySQL データベース]** をクリックします。

3. フォームにデータベースの詳細を入力します。

    ![テスト MySQL データベースの作成](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. SKU を選択します。

    ![SKU の選択](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. ログイン設定を作成します。 ログイン設定は再利用するか、新しい設定を作成できます。 この設定には、データベースのユーザー名とパスワードが含まれます。

    ![新しいデータベース ログインの作成](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    接続文字列には、実際のデータベース サーバー名が含まれます。 それをポータルからコピーします。

    ![MySQL データベースの接続文字列の取得](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> ユーザー名の長さは MySQL 5.7 では 32文字、またはそれ以前のエディションでは 16文字を超えることはできません。


## <a name="add-capacity"></a>容量を追加する

容量を追加するには、Azure Stack ポータルに追加の MySQL サーバを追加します。 追加サーバーは、新規または既存の SKU に追加できます。 サーバー特性が同じであることを確認してください。


## <a name="make-mysql-databases-available-to-tenants"></a>MySQL データベースをテナントで使用できるようにする
プランとサービスを作成して、MySQL データベースをテナントで使用できるようにします。 Microsoft.MySqlAdapter サービスを追加したり、クォータを追加したりします。

![データベースに含めるプランとサービスの作成](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="update-the-administrative-password"></a>管理パスワードの更新
パスワードの変更は、最初に MySQL サーバー インスタンス上で変更することで可能です。 **[管理リソース]** &gt; **[MySQL Hosting Servers]\(MySQL ホスティング サーバー\)** &gt; を参照し、ホスティング サーバーをクリックします。 設定パネルで、[パスワード] をクリックします。

![管理パスワードの更新](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="remove-the-mysql-resource-provider-adapter"></a>MySQL リソースプロバイダーのアダプターの削除

リソース プロバイダーを削除するには、最初にすべての依存関係を削除する必要があります。

1. このバージョンのリソース プロバイダーに対してダウンロードした元のデプロイ パッケージがあることを確認します。

2. すべてのテナント データベースを、リソースプロバイダーから削除する必要があります (データは削除されません)。 これは、テナント自体で実行する必要があります。

3. テナントを名前空間から登録解除する必要があります。

4. 管理者は、MySQL アダプターからホスティング サーバーを削除する必要があります

5. 管理者は、MySQL アダプターを参照するすべてのプランを削除する必要があります。

6. 管理者は、MySQL アダプターに関連付けられているすべてのクォータを削除する必要があります。

7. -Uninstall パラメーター、Azure Resource Manager エンドポイント、DirectoryTenantID、サービス管理者アカウントの資格情報を指定してデプロイ スクリプトを再実行します。




## <a name="next-steps"></a>次のステップ

[SQL Server リソース プロバイダー](azure-stack-sql-resource-provider-deploy.md)や [App Services リソース プロバイダー](azure-stack-app-service-overview.md)のような他の [PaaS サービス](azure-stack-tools-paas-services.md)を試します。
