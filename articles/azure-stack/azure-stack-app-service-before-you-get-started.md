---
title: "App Service on Azure Stack をデプロイする前に | Microsoft Docs"
description: "App Service on Azure Stack をデプロイする前に済ましておく必要がある手順です"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: anwestg
ms.openlocfilehash: 17967131853d4334ae2c0ba3c0aa01089b7f3b61
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2017
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>App Service on Azure Stack を開始する前に

Azure App Service on Azure Stack には、デプロイ前に完了しておく必要のある、次のような前提条件手順があります。

- Azure App Service on Azure Stack ヘルパー スクリプトをダウンロードする
- 高可用性
- Azure App Service on Azure Stack に必要な証明書
- ファイル サーバーを準備する
- SQL Server を準備する
- Azure Active Directory アプリケーションを作成する
- Active Directory フェデレーション サービス アプリケーションを作成する

## <a name="download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts"></a>Azure App Service on Azure Stack インストーラー/ヘルパー スクリプトをダウンロードする

1. [App Service on Azure Stack デプロイ ヘルパー スクリプト](https://aka.ms/appsvconmashelpers)をダウンロードします。
2. [App Service on Azure Stack インストーラー](https://aka.ms/appsvconmasinstaller)をダウンロードします。
3. ヘルパー スクリプトの zip ファイルからファイルを展開します。 次のファイルおよびフォルダー構造が表示されます。
  - Common.ps1
  - Create-AADIdentityApp.ps1
  - Create-ADFSIdentityApp.ps1
  - Create-AppServiceCerts.ps1
  - Get-AzureStackRootCert.ps1
  - Remove-AppService.ps1
  - モジュール
    - GraphAPI.psm1
    
## <a name="high-availability"></a>高可用性

Azure Stack でワークロードをデプロイできるのは 1 つの障害ドメインに限られるため、Azure App Service on Azure Stack では現在、高可用性を提供できません。

高可用性用に Azure App Service on Azure Stack を準備するには必ず、必要なファイル サーバーと SQL Server を高可用性構成でデプロイしてください。 Azure Stack で複数の障害ドメインをサポートできるようになった時点で、高可用性構成で Azure App Service on Azure Stack を有効にする方法についてのガイダンスを提供します。


## <a name="certificates-required-for-azure-app-service-on-azure-stack"></a>Azure App Service on Azure Stack に必要な証明書

### <a name="certificates-required-for-the-azure-stack-development-kit"></a>Azure Stack Development Kit に必要な証明書

この 1 つ目のスクリプトでは、Azure Stack 証明機関と連携して、App Service で必要な 4 つの証明書を作成します。

| ファイル名 | 用途 |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | App Service の既定の SSL 証明書 |
| Api.appservice.local.azurestack.external.pfx | App Service API の SSL 証明書 |
| ftp.appservice.local.azurestack.external.pfx | App Service パブリッシャーの SSL 証明書 |
| Sso.appservice.local.azurestack.external.pfx | App Service ID アプリケーションの証明書 |

スクリプトを Azure Stack Development Kit ホストで実行し、PowerShell が azurestack\CloudAdmin として実行されていることを確認します。

1. azurestack\AzureStackAdmin として実行されている PowerShell セッションで、ヘルパー スクリプトを展開したフォルダーから Create-AppServiceCerts.ps1 スクリプトを実行します。 このスクリプトでは、App Service で必要となる証明書作成スクリプトと同じフォルダーに 4 つの証明書が作成されます。
2. .pfx ファイルを保護するためのパスワードを入力し、そのパスワードを書き留めておきます。 それを App Service on Azure Stack インストーラーに入力する必要があります。

#### <a name="create-appservicecertsps1-parameters"></a>Create-AppServiceCerts.ps1 のパラメーター

| パラメーター | 必須/省略可能 | 既定値 | Description |
| --- | --- | --- | --- |
| pfxPassword | 必須 | Null | 証明書の秘密キーを保護するためのパスワード |
| DomainName | 必須 | local.azurestack.external | Azure Stack のリージョンとドメイン サフィックス |

### <a name="certificates-required-for-a-production-deployment-of-azure-app-service-on-azure-stack"></a>Azure App Service on Azure Stack の運用環境デプロイに必要な証明書

運用環境でリソースプロバイダーを操作するには、次の 4 つの証明書を提供する必要があります。

#### <a name="default-domain-certificate"></a>既定のドメイン証明書

既定のドメイン証明書は、フロントエンド ロールに配置されます。 ユーザー アプリケーションにより、ワイルドカードや、Azure App Service への既定のドメイン要求で使用されます。 この証明書は、ソース管理操作 (KUDU) にも使用されます。

この証明書は .pfx 形式の、2 つのサブジェクトのワイルドカード証明書である必要があります。 これにより、既定のドメインとソース管理操作の scm エンドポイントの両方に 1 つの証明書で対応できます。

| 形式 | 例 |
| --- | --- |
| \*.appservice.\<region\>.\<DomainName\>.\<extension\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.appservice.scm.redmond.azurestack.external |

#### <a name="api-certificate"></a>API 証明書

API 証明書は管理ロールに配置され、リソースプロバイダーによって API 呼び出しを保護するために使用されます。 公開用の証明書には、API の DNS エントリと一致するサブジェクトが含まれている必要があります。

| 形式 | 例 |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>公開証明書

パブリッシャー ロール用の証明書は、コンテンツ アップロード時に、アプリケーション所有者の FTPS トラフィックを保護します。  公開証明書には、FTPS の DNS エントリと一致するサブジェクトが含まれている必要があります。

| 形式 | 例 |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>ID 証明書

ID アプリケーション用の証明書は以下が可能です。
- AAD/ADFS ディレクトリ、Azure Stack、App Service の統合によるコンピューティング リソースプロバイダーとの統合のサポート。
- Azure App Service on Azure Stack 内の複数の高度な開発者ツールでのシングル サインオン シナリオ。
この ID 証明書には、次の形式に一致するサブジェクトが含まれている必要があります。

| 形式 | 例 |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |

### <a name="extract-the-azure-stack-azure-resource-manager-root-certificate"></a>Azure Stack Azure Resource Manager のルート証明書を展開する

azurestack\CloudAdmin として実行されている PowerShell セッションで、ヘルパー スクリプトを展開したフォルダーから Get-AzureStackRootCert.ps1 スクリプトを実行します。 このスクリプトでは、App Service で必要となる証明書作成スクリプトと同じフォルダーに 4 つの証明書が作成されます。

| Get-AzureStackRootCert.ps1 パラメーター | 必須/省略可能 | 既定値 | 説明 |
| --- | --- | --- | --- |
| PrivelegedEndpoint | 必須 | AzS-ERCS01 | 特権エンドポイント。 |
| CloudAdminCredential | 必須 | AzureStack\CloudAdmin | Azure Stack Cloud Admin ドメイン アカウントの資格情報 |


## <a name="prepare-the-file-server"></a>ファイル サーバーを準備する

Azure App Service では、ファイル サーバーを使用する必要があります。 運用環境の場合、ファイル サーバーは高可用性サーバーとしてエラーを処理できるように構成する必要があります。

Azure Stack Development Kit のデプロイのみで使用する場合、この例の Azure Resource Manager Deployment Template (https://aka.ms/appsvconmasdkfstemplate) を使用して、構成済みの単一ノードのファイル サーバーをデプロイできます。 この単一ノードのファイル サーバーは、ワークグループ内に存在します。

### <a name="provision-groups-and-accounts-in-active-directory"></a>Active Directory でグループとアカウントをプロビジョニングする


1. 次の Active Directory グローバル セキュリティ グループを作成します。
    - FileShareOwners
    - FileShareUsers
2. 次の Active Directory アカウントをサービス アカウントとして作成します。
    - FileShareOwner
    - FileShareUser

    セキュリティのベスト プラクティスとしては、これらのアカウント (およびすべての Web ロール) のユーザーは互いに区別可能で、強力なユーザー名とパスワードを使用する必要があります。
    次の条件でパスワードを設定します。
     - **[パスワードを無期限にする]** を有効にします。
     - **[ユーザーはパスワードを変更できない]** を有効にします。
     - **[ユーザーは次回ログオン時にパスワードの変更が必要]** を無効にします。
3. 次のように、グループのメンバーシップにアカウントを追加します。
    - **FileShareOwner** を **FileShareOwners** グループに追加します。
    - **FileShareUser** を **FileShareUsers** グループに追加します。

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>ワークグループでグループとアカウントをプロビジョニングする

>[!NOTE]
> 管理者コマンド プロンプト セッションで、ファイル サーバーを構成するとき、次のコマンドを実行します。  **PowerShell は使用しないでください。**

上記の Azure Resource Manager テンプレートを使用する時点で、ユーザーは既に作成済みです。

1. 次のコマンドを実行して、FileShareOwner アカウントと FileShareUser アカウントを作成します。 <password> を独自の値に置き換えます。
``` DOS
net user FileShareOwner <password> /add /expires:never /passwordchg:no
net user FileShareUser <password> /add /expires:never /passwordchg:no
```
2. 次の WMIC コマンドを実行して、アカウントのパスワードを無期限に設定します。
``` DOS
WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
```
3. ローカル グループ FileShareUsers と FileShareOwners を作成し、最初の手順で作成したアカウントをそれらに追加します。
``` DOS
net localgroup FileShareUsers /add
net localgroup FileShareUsers FileShareUser /add
net localgroup FileShareOwners /add
net localgroup FileShareOwners FileShareOwner /add
```

### <a name="provision-the-content-share"></a>コンテンツ共有をプロビジョニングする

コンテンツ共有には、テナント Web サイトのコンテンツが含まれています。 1 つのファイル サーバーでコンテンツ共有をプロビジョニングする手順は、Active Directory とワークグループの両方の環境で同じですが、Active Directory のフェールオーバー クラスターでは異なります。

#### <a name="provision-the-content-share-on-a-single-file-server-ad-or-workgroup"></a>1 つのファイル サーバー (AD または ワークグループ) でコンテンツ共有をプロビジョニングする

1 つのファイル サーバーで、管理者特権のコマンド プロンプトで次のコマンドを実行します。 'C:\WebSites' の値を、使用する環境の対応するパスで置き換えます。

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="to-enable-winrm-add-the-fileshareowners-group-to-the-local-administrators-group"></a>WinRM を有効にするには、ローカルの Administrators グループに FileShareOwners グループを追加します

Windows リモート管理を正しく機能させるには、ローカルの Administrators グループに FileShareOwners グループを追加する必要があります。

#### <a name="active-directory"></a>Active Directory

ファイル サーバーで、またはすべてのファイル サーバー フェールオーバー クラスター ノードで、管理者特権のコマンド プロンプトで次のコマンドを実行します。 <DOMAIN> の値を、使用するドメイン名に置き換えます。

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>ワークグループ

ファイル サーバーで、管理者特権のコマンド プロンプトで次のコマンドを実行します。

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>共有へのアクセス制御を構成する

ファイル サーバーまたはファイル サーバー フェールオーバー クラスター ノード (現在のクラスター リソース所有者) で、管理者特権のコマンド プロンプトで次のコマンドを実行します。 斜体の値を、使用する環境の固有の値に置き換えます。

#### <a name="active-directory"></a>Active Directory
```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>ワークグループ
```DOS
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

## <a name="prepare-the-sql-server"></a>SQL Server を準備する

Azure App Service on Azure Stack のホスティング データベースと測定データベースについては、Azure App Service データベースを保持するように SQL Server を準備する必要があります。

Azure Stack Development Kit で使用する場合は、SQL Express 2014 SP2 以降を使用できます。

運用と高可用性のためには、SQL 2014 SP2 以降の完全バージョンを使用し、混合モード認証を有効にして、[高可用性構成](https://docs.microsoft.com/en-us/sql/sql-server/failover-clusters/high-availability-solutions-sql-server)でデプロイを行う必要があります。

Azure App Service on Azure Stack SQL Server は、すべての App Service ロールからアクセスできる必要があります。 SQL Server は、Azure Stack の既定のプロバイダー サブスクリプション内でデプロイできます。 あるいは、組織内の既存のインフラストラクチャーを利用できます (Azure Stack に接続されている場合)。 Azure Marketplace イメージを使用している場合は、適宜ファイアウォールを構成してください。 

どの SQL Server ロールの場合も、既定のインスタンスまたは名前付きインスタンスを使用できます。 ただし、名前付きインスタンスを使用する場合は、手動で SQL Browser サービスを開始してポート 1434 を開いてください。

## <a name="create-an-azure-active-directory-application"></a>Azure Active Directory アプリケーションを作成する

Azure AD サービス プリンシパルで以下をサポートするように構成します。
- Worker 層での仮想マシン スケール セットの統合。
- Azure Functions ポータルと高度な開発者ツールのための SSO。

次の手順は、Azure AD で保護されている Azure Stack 環境のみに適用されます。

管理者は、SSO を次のように構成する必要があります。
- App Service 内の高度な開発者ツール (Kudu) を有効にする。
- Azure Functions ポータルのエクスペリエンスを有効にする。

次の手順に従います。

1. azurestack\AzureStackAdmin として PowerShell インスタンスを開きます。
2. [前提条件の手順](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts)でダウンロードして展開したスクリプトの場所に移動します。
3. [Azure Stack PowerShell をインストールします](azure-stack-powershell-install.md)。
4. **Create-AADIdentityApp.ps1** スクリプトを実行します。 Azure AD テナント ID の指定を求められたら、Azure Stack のデプロイに使っている Azure AD テナント ID (例: myazurestack.onmicrosoft.com) を入力します。
5. **[資格情報]** ウィンドウで、Azure AD サービスの管理者アカウントとパスワードを入力します。 **[OK]**をクリックします。
6. [先ほど作った証明書](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack)について、証明書ファイル パスと証明書パスワードを入力します。 既定でこの手順のために作られる証明書は、**sso.appservice.local.azurestack.external.pfx** です。
7. このスクリプトにより、テナント Azure AD で新しいアプリケーションが作成されます。 PowerShell の出力で返されるアプリケーション ID を書き留めておきます。 インストール時にこの情報が必要になります。
8. 新しいブラウザー ウィンドウを開き、**Azure Active Directory サービス管理者**として Azure Portal (portal.azure.com) にサインインします。
9. Azure AD リソース プロバイダーを開きます。
10. **[アプリの登録]** をクリックします。
11. 手順 7 で返された**アプリケーション ID** を検索します。 一覧に App Service アプリケーションが表示されます。
12. 一覧で **[アプリケーション]** をクリックします
13. **[必要なアクセス許可]** > **[アクセス許可の付与]** > **[はい]** の順にクリックします。

| Create-AADIdentityApp.ps1 パラメーター | 必須/省略可能 | 既定値 | Description |
| --- | --- | --- | --- |
| DirectoryTenantName | 必須 | Null | Azure AD テナント ID。 GUID または文字列を指定します (例: myazureaaddirectory.onmicrosoft.com) |
| AdminArmEndpoint | 必須 | Null | Admin Azure Resource Manager エンドポイント (adminmanagement.local.azurestack.external など) |
| TenantARMEndpoint | 必須 | Null | Tenant Azure Resource Manager エンドポイント (management.local.azurestack.external など) |
| AzureStackAdminCredential | 必須 | Null | Azure AD サービス管理者の資格情報 |
| CertificateFilePath | 必須 | Null | 先ほど生成された ID アプリケーション証明書ファイルへのパス。 |
| CertificatePassword | 必須 | Null | 証明書の秘密キーを保護するためのパスワード。 |

## <a name="create-an-active-directory-federation-services-application"></a>Active Directory フェデレーション サービス アプリケーションを作成する

AD FS によって保護されている Azure Stack 環境の場合、 AD FS サービス プリンシパルで以下をサポートするように構成する必要があります。
- Worker 層での仮想マシン スケール セットの統合。
- Azure Functions ポータルと高度な開発者ツールのための SSO。

次のことを行うには、管理者は SSO を構成する必要があります。
- Worker 層での仮想マシン スケール セット統合のためにサービス プリンシパルを構成する。
- App Service 内の高度な開発者ツール (Kudu) を有効にする。
- Azure Functions ポータルのエクスペリエンスを有効にする。

次の手順に従います。

1. azurestack\azurestackadmin として PowerShell インスタンスを開きます。
2. [前提条件の手順](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts)でダウンロードして展開したスクリプトの場所に移動します。
3. [Azure Stack PowerShell をインストールします](azure-stack-powershell-install.md)。
4.  **Create-ADFSIdentityApp.ps1** スクリプトを実行します。
5.  **[資格情報]** ウィンドウで、AD FS クラウドの管理者アカウントとパスワードを入力します。 **[OK]**をクリックします。
6.  [先ほど作った証明書](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack)について、証明書ファイル パスと証明書パスワードを入力します。 既定でこの手順のために作られる証明書は、sso.appservice.local.azurestack.external.pfx です。

| Create-ADFSIdentityApp.ps1 パラメーター | 必須/省略可能 | 既定値 | 説明 |
| --- | --- | --- | --- |
| AdminArmEndpoint | 必須 | Null | 管理者の Azure Resource Manager エンドポイント。 例: adminmanagement.local.azurestack.external。 |
| PrivilegedEndpoint | 必須 | Null | 特権エンドポイント。 AzS-ERCS01 など。 |
| CloudAdminCredential | 必須 | Null | Azure Stack cloudadmin ドメイン アカウントの資格情報。 例: Azurestack\CloudAdmin。 |
| CertificateFilePath | 必須 | Null | ID アプリケーションの証明書 PFX ファイルへのパス。 |
| CertificatePassword | 必須 | Null | 証明書の秘密キーを保護するためのパスワード。 |


## <a name="next-steps"></a>次のステップ

[App Service リソース プロバイダーをインストールする](azure-stack-app-service-deploy.md)。
