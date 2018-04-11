---
title: Azure Stack で MySQL データベースを PaaS として使用する | Microsoft Docs
description: Azure Stack で MySQL リソース プロバイダーをデプロイし、MySQL データベースをサービスとして提供する方法を説明します。
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: mabrigg
ms.reviewer: jeffgo
ms.openlocfilehash: 66e1d5691b431be0c3d040570b13e8d16b1669ef
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Microsoft Azure Stack で MySQL データベースを使用する

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack に MySQL リソース プロバイダーをデプロイできます。 リソース プロバイダーをデプロイしたら、Azure Resource Manager デプロイ テンプレートを使用して MySQL サーバーとデータベースを作成できます。 MySQL データベースは、サービスとしても提供できます。 

MySQL データベースは、Web サイトで一般的であり、多くの Web サイト プラットフォームをサポートしています。 たとえば、リソース プロバイダーをデプロイした後に、Azure Stack のサービスとしての Web Apps プラットフォーム (PaaS) アドオンから WordPress Web サイトを作成できます。

インターネットにアクセスできないシステムに MySQL プロバイダーをデプロイするには、[mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) ファイルをローカル共有にコピーします。 プロンプトが表示されたら、その共有名を指定します。 Azure および Azure Stack PowerShell モジュールをインストールする必要があります。


## <a name="mysql-server-resource-provider-adapter-architecture"></a>MySQL サーバー リソース プロバイダー アダプターのアーキテクチャ

リソース プロバイダーは、次の 3 つのコンポーネントで構成されています。

- **MySQL リソース プロバイダー アダプター VM**。これはプロバイダー サービスを実行する Windows 仮想マシンです。

- **リソース プロバイダー自体**。これはプロビジョニング要求を処理し、データベース リソースを公開します。

- **MySQL サーバーをホストするサーバー**。これはデータベースに容量を提供し、ホスティング サーバーと呼ばれます。

今回のリリースでは、MySQL インスタンスは作成されなくなりました。 つまり、自分で作成するか、外部 SQL インスタンスへのアクセスを指定するか、またはその両方を行う必要があります。 [Azure Stack Quickstart Gallery](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) では、以下のことができるテンプレートの例をご覧いただけます。
- 自力で MySQL サーバーを作成する。
- Azure Marketplace から MySQL Server をダウンロードしてデプロイする。

> [!NOTE]
> Azure Stack 統合システムにインストールされたホスティング サーバーは、テナント サブスクリプションから作成する必要があります。 既定のプロバイダー サブスクリプションからは作成できません。 それらは、テナント ポータルから、または適切なサインインで PowerShell セッションから作成する必要があります。 すべてのホスティング サーバーは課金対象の仮想マシンであり、適切なライセンスを必要とします。 サービス管理者は、テナント サブスクリプションの所有者になることができます。

### <a name="required-privileges"></a>必要な特権
システム アカウントには、次の特権が必要です。

1.  データベース: 作成、ドロップ
2.  ログイン: 作成、設定、ドロップ、許可、取り消し

## <a name="deploy-the-resource-provider"></a>リソース プロバイダーのデプロイ

1. まだ開発キットを登録していない場合は登録して、Marketplace 管理からダウンロードできる Windows Server 2016 Datacenter Core イメージをダウンロードします。 Windows Server 2016 Core イメージを使用する必要があります。 スクリプトを使用して [Windows Server 2016 イメージ](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image)を作成することもできます。 (コア オプションを必ず選択してください)


2. 特権エンドポイント VM にアクセスできるホストにサインインします。

    - Azure SDK のインストールで、物理ホストにサインインします。 
    - マルチノード システムでは、ホストは特権エンドポイントにアクセスできるシステムである必要があります。
    
    >[!NOTE]
    > スクリプトが実行されるシステムは、最新バージョンの .NET ランタイムがインストールされている Windows 10 または Windows Server 2016 システムである*必要があります*。 それ以外の場合、インストールは失敗します。 Azure Stack SDK ホストはこの条件を満たしています。
    

3. MySQL リソース プロバイダー バイナリをダウンロードします。 次に、自己解凍ツールを実行して、その内容を一時ディレクトリに抽出します。

    >[!NOTE] 
    > リソース プロバイダーには、対応する最低限の Azure Stack ビルドがあります。 必ず実行されている Azure Stack のバージョンに適合する正しいバイナリをダウンロードしてください。

    | Azure Stack のビルド | MySQL RP インストーラー |
    | --- | --- |
    | 1802: 1.0.180302.1 | [MySQL RP バージョン 1.1.18.0](https://aka.ms/azurestackmysqlrp1802) |
    | 1712: 1.0.180102.3 または 1.0.180106.1 (マルチノード) | [MySQL RP バージョン 1.1.14.0](https://aka.ms/azurestackmysqlrp1712) |
    | 1711: 1.0.171122.1 | [MySQL RP バージョン 1.1.12.0](https://aka.ms/azurestackmysqlrp1711) |
    | 1710: 1.0.171028.1 | [MySQL RP バージョン 1.1.8.0](https://aka.ms/azurestackmysqlrp1710) |

4.  Azure SDK では、このプロセスの一環として自己署名証明書が作成されます。 マルチノードの場合は、適切な証明書を提供する必要があります。

    独自の証明書を提供する必要がある場合は、次の条件と合致する **DependencyFilesLocalPath** に .pfx ファイルを配置します。

    - \*.dbadapter.\<region\>.\<external fqdn\> のワイルドカード証明書、または mysqladapter.dbadapter.\<region\>.\<external fqdn\> の一般名を持つ 1 つのサイト証明書のどちらか。

    - この証明書は信頼できる必要があります。 つまり、信頼チェーンが中間証明書の必要なしに存在する必要があります。

    - DependencyFilesLocalPath には 1 つの証明書ファイルしか存在しません。
    
    - このファイル名に特殊文字やスペースを含めることはできません。


5. **新しい**管理者特権 (管理) PowerShell コンソールを開きます。 ファイルを展開したディレクトリに変更します。 新しいウィンドウを使用すると、システムに不適切な PowerShell モジュールが既に読み込まれている場合に発生する可能性のある問題を回避できます。

6. [Azure PowerShell バージョン 1.2.11 をインストールします](azure-stack-powershell-install.md)。

7. `DeployMySqlProvider.ps1` スクリプトを実行します。

スクリプトでは次の手順が実行されます。

* MySQL コネクタ バイナリをダウンロードします (これはオフラインで提供できます)。
* Azure Stack 上のストレージ アカウントに証明書とその他のアーティファクトをアップロードします。
* ギャラリー パッケージを発行して、ギャラリーを通じて SQL データベースをデプロイできるようにします。
* ホスティング サーバーをデプロイするためのギャラリー パッケージを発行します。
* 手順 1 で作成した Windows Server 2016 イメージを使用して VM をデプロイします。 リソース プロバイダーもインストールされます。
* リソース プロバイダー VM にマップされるローカル DNS レコードを登録します。
* リソース プロバイダーをローカル Azure Resource Manager (テナントと管理者) に登録します。


次のようにすることができます。
- コマンドラインで必須パラメーターを指定する。
- パラメーターなしで実行し、入力を求められたら入力する。

PowerShell プロンプトから実行できる例を示します。 必要に応じてアカウント情報とパスワードを変更してください。


```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

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
. $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```


### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 パラメーター
これらのパラメーターをコマンド ラインで指定できます。 必須パラメーターの指定がない場合、またはいずれかのパラメーターの検証が失敗した場合は、指定することを求められます。

| パラメーター名 | [説明] | コメントまたは既定値 |
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


## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Azure Stack ポータルを使用してデプロイを確認する

> [!NOTE]
>  インストール スクリプトの実行が終了したら、ポータルを最新の情報に更新して [管理者] ブレードを表示する必要があります。


1. 管理ポータルにサービス管理者としてサインインします。

2. デプロイが成功したことを確認します。 **[リソース グループ]** に移動し、**[system.\<場所\>.mysqladapter]** リソース グループを選択します。 4 つのすべてのデプロイが成功したことを確認します。

      ![MySQL RP のデプロイを確認する](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>MySQL ホスティング サーバに接続して容量を提供する

1. Azure Stack ポータルにサービス管理者としてサインインします。

2. **[管理リソース]** > **[MySQL ホスティング サーバー]** > **[+追加]** を選択します。

    **[MySQL ホスティング サーバー]** ブレードで、リソース プロバイダーのバックエンドとして機能する MySQL サーバーの実際のインスタンスに MySQL サーバー リソース プロバイダーを接続できます。

    ![ホスティング サーバー](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. MySQL サーバー インスタンスの接続詳細を指定します。 短い VM 名ではなく、完全修飾ドメイン名 (FQDN) または有効な IPv4 アドレスを必ず指定してください。 このインストールでは、既定の MySQL インスタンスが提供されなくなりました。 指定したサイズは、リソース プロバイダーがデータベース容量を管理するのに役立ちます。 それはデータベース サーバーの物理容量に近い値にする必要があります。

    > [!NOTE]
    >テナントと管理者の Azure Resource Manager が MySQL インスタンスにアクセスできる場合は、MySQL インスタンスをリソース プロバイダーの管理下に置くことができます。 MySQL インスタンスは、リソース プロバイダーに排他的に割り当てる*必要があります*。

4. サーバーを追加する際に、サービス内容を区別できるように新規または既存の SKU に割り当てる必要があります。
  たとえば、次のものを提供するエンタープライズ インスタンスを持つことができます。
    - データベース容量
    - 自動バックアップ
    - 各部門のための高性能なサーバーの予約
 

SKU 名は、テナントが各自のデータベースを適切に配置できるように、プロパティを表している必要があります。 SKU 内のすべてのホスティング サーバーの機能が同じである必要があります。

![MySQL SKU を作成する](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)


>[!NOTE]
> SKU はポータルに表示されるまで最大 1 時間かかることがあります。 SKU が作成されるまで、データベースを作成できません。


## <a name="test-your-deployment-by-creating-your-first-mysql-database"></a>最初の MySQL データベースを作成してデプロイをテストする


1. Azure Stack ポータルにサービス管理者としてサインインします。

2. **[+ 新規]** > **[データ + ストレージ]** > **[MySQL データベース]** を選択します。

3. データベースの詳細を指定します。

    ![テスト MySQL データベースの作成](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. SKU を選択します。

    ![SKU の選択](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. ログイン設定を作成します。 既存のログイン設定を再利用するか、新しい設定を作成できます。 この設定には、データベースのユーザー名とパスワードが含まれます。

    ![新しいデータベース ログインの作成](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    接続文字列には、実際のデータベース サーバー名が含まれます。 それをポータルからコピーします。

    ![MySQL データベースの接続文字列の取得](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> MySQL 5.7 では、ユーザー名の長さは 32 文字を超えることはできません。 前のエディションでは、16 文字を超えることはできません。


## <a name="add-capacity"></a>容量を追加する

容量を追加するには、Azure Stack ポータルに追加の MySQL サーバを追加します。 追加サーバーは、新規または既存の SKU に追加できます。 サーバー特性が同じであることを確認してください。


## <a name="make-mysql-databases-available-to-tenants"></a>MySQL データベースをテナントで使用できるようにする
プランとサービスを作成して、MySQL データベースをテナントで使用できるようにします。 たとえば、Microsoft.MySqlAdapter サービスの追加やクォータの追加などを実行します。

![データベースに含めるプランとサービスの作成](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="update-the-administrative-password"></a>管理パスワードの更新
パスワードの変更は、最初に MySQL サーバー インスタンス上で変更することで可能です。 **[管理リソース]** > **[MySQL ホスティング サーバー]** を選択します。 次に、ホスティング サーバーを選択します。 **[設定]** パネルで、**[パスワード]** を選択します。

![管理パスワードの更新](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="update-the-mysql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>MySQL リソース プロバイダー アダプターを更新する (マルチノードのみ、ビルド 1710 以降)
Azure Stack ビルドの更新時に、新しい SQL リソース プロバイダー アダプターがリリースされる場合があります。 既存のアダプターが動作し続けている場合でも、できるだけ早く最新のビルドに更新することをお勧めします。 

リソース プロバイダーの更新には *UpdateMySQLProvider.ps1* スクリプトを使用します。 プロセスは、この記事の[リソース プロバイダーをデプロイする](#deploy-the-resource-provider)セクションに記述されている、リソース プロバイダーをインストールするプロセスと類似しています。 スクリプトはリソース プロバイダーのダウンロードに含まれています。

*UpdateMySQLProvider.ps1* スクリプトは、最新のリソース プロバイダーのコードを使って新しい VM を作成し、古い VM から新しい VM に設定を移行します。 移行される設定には、データベースおよびホスティング サーバーの情報や、必要な DNS レコードが含まれます。

スクリプトでは、DeployMySqlProvider.ps1 スクリプト用に記述されているものと同じ引数の使用が必要になります。 証明書も同様に指定します。 

次に、PowerShell プロンプトから実行することができる *UpdateMySQLProvider.ps1* スクリプトの例を示します。 必要に応じてアカウント情報とパスワードを変更してください。 

> [!NOTE]
> 更新プロセスは、統合システムにのみに適用されます。

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

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

# Set credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense
 ```

### <a name="updatemysqlproviderps1-parameters"></a>UpdateMySQLProvider.ps1 パラメーター
これらのパラメーターをコマンド ラインで指定できます。 必須パラメーターの指定がない場合、またはいずれかのパラメーター検証が失敗した場合は、指定することを求められます。

| パラメーター名 | [説明] | コメントまたは既定値 |
| --- | --- | --- |
| **CloudAdminCredential** | 特権エンドポイントへのアクセスに必要な、クラウド管理者の資格情報。 | _必須_ |
| **AzCredential** | Azure Stack サービス管理者アカウントの資格情報。 Azure Stack のデプロイに使用したのと同じ資格情報を使用します。 | _必須_ |
| **VMLocalCredential** |SQL リソース プロバイダー VM のローカル Administrator アカウントの資格情報。 | _必須_ |
| **PrivilegedEndpoint** | 特権エンドポイントの IP アドレスまたは DNS 名。 |  _必須_ |
| **DependencyFilesLocalPath** | 証明書 .pfx ファイルはこのディレクトリにも配置する必要があります。 | _省略可能_ (マルチノードでは_必須_) |
| **DefaultSSLCertificatePassword** | .pfx 証明書のパスワード | _必須_ |
| **MaxRetryCount** | 障害がある場合に各操作を再試行する回数。| 2 |
| **RetryDuration** | 再試行間のタイムアウト間隔 (秒単位)。 | 120 |
| **アンインストール** | リソース プロバイダーと関連付けられているすべてのリソースを削除します (以下のメモを参照してください)。 | いいえ  |
| **DebugMode** | 障害発生時に自動クリーンアップが行われないようにします。 | いいえ  |
| **AcceptLicense** | GPL ライセンスに同意するためのプロンプトをスキップします。  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


## <a name="collect-diagnostic-logs"></a>診断ログの収集
MySQL リソース プロバイダーは、ロック ダウンされた仮想マシンです。 仮想マシンからのログ収集が必要になった場合は、その用途のために PowerShell Just Enough Administration (JEA) エンドポイント _DBAdapterDiagnostics_ が用意されています。 このエンドポイントで使用できる 2 つのコマンドが 2 つあります。

* Get-AzsDBAdapterLog - RP 診断ログを含む zip パッケージを準備し、セッション ユーザー ドライブに配置します。 このコマンドは、パラメーターなしで呼び出すことができます。過去 4 時間分のログが収集されます。
* Remove-AzsDBAdapterLog - リソース プロバイダー VM 上の既存のログ パッケージを削除します。

RP ログを抽出する診断エンドポイントに接続するために、RP 展開中または更新中に _dbadapterdiag_ というユーザー アカウントが作成されます。 このアカウントのパスワードは、展開/更新中に指定したローカル管理者アカウントのパスワードと同じです。

これらのコマンドを使用するには、リソース プロバイダーの仮想マシンにリモート PowerShell セッションを作成し、コマンドを呼び出す必要があります。 必要に応じて、FromDate および ToDate パラメーターを指定できます。 これらの一方または両方を指定しない場合、FromDate は現在の時刻より 4 時間前になり、ToDate は現在の時刻になります。

このサンプル スクリプトは、これらのコマンドの使用方法を示します。

```
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<see above>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, $diagnosticsUserPassword)
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="maintenance-operations-integrated-systems"></a>保守操作 (統合システム)
MySQL リソース プロバイダーは、ロック ダウンされた仮想マシンです。 リソース プロバイダー仮想マシンのセキュリティの更新は、PowerShell Just Enough Administration (JEA) エンドポイント _DBAdapterMaintenance_ から実行できます。

これらの操作のためのスクリプトが、RP のインストール パッケージで提供されています。


### <a name="update-the-virtual-machine-operating-system"></a>仮想マシンのオペレーティング システムの更新
Windows Server VM を更新するには、いくつかの方法があります。
* 現在パッチが適用された Windows Server 2016 Core イメージを使用して最新のリソース プロバイダーのパッケージをインストールする
* RP の更新またはインストール中に Windows 更新プログラム パッケージをインストールする


### <a name="update-the-virtual-machine-windows-defender-definitions"></a>仮想マシンの Windows Defender の定義を更新する

これらの手順に従って Defender の定義を更新します。

1. [Windows Defender の定義](https://www.microsoft.com/en-us/wdsi/definitions)から Windows Defender 定義の更新をダウンロードします。

    そのページの [Manually download and install the definitions]\(定義を手動でダウンロードしてインストールする\) から "Windows Defender Antivirus for Windows 10 および Windows 8.1" 64 ビット ファイルをダウンロードします。 
    
    直接リンク: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. MySQL RP アダプター仮想マシンのメンテナンス エンドポイントに対し PowerShell セッションを作成します。
3. メンテナンス エンドポイントのセッションを使用して DB アダプター コンピューターに、定義更新ファイルをコピーします。
4. メンテナンス PowerShell セッションで _Update-DBAdapterWindowsDefenderDefinitions_ コマンドを呼び出します。
5. インストール後に使用される定義更新ファイルを削除することをお勧めします。 定義更新ファイルは、メンテナンス セッションで _Remove-ItemOnUserDrive_ コマンドを使用して削除できます。


Defender の定義を更新するサンプル スクリプトを次に示します (仮想マシンのアドレスまたは名前を実際の値に置き換えてください)。

```
# Set credentials for the diagnostic user
$diagPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$diagCreds = New-Object System.Management.Automation.PSCredential `
    ("dbadapterdiag", $vmLocalAdminPass)$diagCreds = Get-Credential

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "XX.XX.XX.XX"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64 `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $diagCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\mpam-fe.exe"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinitions -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```


## <a name="remove-the-mysql-resource-provider-adapter"></a>MySQL リソースプロバイダーのアダプターを削除する

リソース プロバイダーを削除するには、最初にすべての依存関係を削除する必要があります。

1. このバージョンのリソース プロバイダーに対してダウンロードした元のデプロイ パッケージがあることを確認します。

2. すべてのテナント データベースをリソース プロバイダーから削除する必要があります  (テナント データベースを削除してもデータは削除されません。)このタスクは、テナント自体で実行する必要があります。

3. テナントを名前空間から登録解除する必要があります。

4. 管理者は、MySQL アダプターからホスティング サーバーを削除する必要があります。

5. 管理者は、MySQL アダプターを参照するすべてのプランを削除する必要があります。

6. 管理者は、MySQL アダプターに関連付けられているすべてのクォータを削除する必要があります。

7. 次の要素を使用してデプロイ スクリプトを再実行します。
    - -Uninstall パラメーター
    - Azure Resource Manager エンドポイント
    - DirectoryTenantID
    - サービス管理者アカウントの資格情報
