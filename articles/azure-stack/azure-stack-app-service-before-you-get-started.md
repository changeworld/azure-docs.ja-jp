---
title: App Service on Azure Stack をデプロイする前に | Microsoft Docs
description: App Service on Azure Stack をデプロイする前に済ましておく必要がある手順です
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: anwestg
ms.openlocfilehash: 22901374988f6654bc1fb282315db81bb17c815f
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2018
ms.locfileid: "37857867"
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>App Service on Azure Stack を開始する前に

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure App Service on Azure Stack をデプロイする前に、この記事にある前提条件ステップを完了する必要があります。

> [!IMPORTANT]
> Azure App Service 1.2 をデプロイする前に、Azure Stack 統合システムに 1804 更新プログラムを適用するか、最新の Azure Stack Development Kit (ASDK) をデプロイしてください。

## <a name="download-the-installer-and-helper-scripts"></a>インストーラーおよびヘルパー スクリプトをダウンロードする

1. [App Service on Azure Stack デプロイ ヘルパー スクリプト](https://aka.ms/appsvconmashelpers)をダウンロードします。
2. [App Service on Azure Stack インストーラー](https://aka.ms/appsvconmasinstaller)をダウンロードします。
3. ヘルパー スクリプトの .zip ファイルからファイルを展開します。 次のファイルとフォルダーが展開されます。

   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - モジュール フォルダー
     - GraphAPI.psm1

## <a name="high-availability"></a>高可用性

Azure Stack 1802 更新プログラムは、障害ドメインのサポートを追加しました。 Azure App Service on Azure Stack の新規デプロイメントが障害ドメイン間で分散され、フォールト トレランスを実現します。

1802 更新プログラムの前にデプロイされた、Azure App Service on Azure Stack の既存のデプロイメントについては、[障害ドメイン間での App Service リソース プロバイダーの再調整](azure-stack-app-service-fault-domain-update.md)の記事を参照してください。

さらに、高可用性構成で、必要なファイル サーバーと SQL Server インスタンスをデプロイします。

## <a name="get-certificates"></a>証明書を取得する

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Azure Stack 用の Azure Resource Manager ルート証明書

Azure Stack 統合システムまたは Azure Stack Development Kit のホストで特権エンドポイントにアクセスできるコンピューター上で管理者特権の PowerShell セッションを開きます。

ヘルパー スクリプトを展開したフォルダーから *Get-AzureStackRootCert.ps1* スクリプトを実行します。 このスクリプトは、証明書を作成するために App Service で必要となるスクリプトと同じフォルダーにルート証明書を作成します。

次の PowerShell コマンドを実行するときに、AzureStack\CloudAdmin の特権エンドポイントおよび資格情報を提供する必要があります。

```PowerShell
    Get-AzureStackRootCert.ps1
```

#### <a name="get-azurestackrootcertps1-script-parameters"></a>Get-AzureStackRootCert.ps1 スクリプトのパラメーター

| パラメーター | 必須または省略可能 | 既定値 | 説明 |
| --- | --- | --- | --- |
| PrivilegedEndpoint | 必須 | AzS-ERCS01 | 特権エンドポイント |
| CloudAdminCredential | 必須 | AzureStack\CloudAdmin | Azure Stack クラウド管理者のドメイン アカウントの資格情報 |

### <a name="certificates-required-for-asdk-deployment-of-azure-app-service"></a>Azure での Azure App Service のデプロイメントに必要な証明書

*Create-AppServiceCerts.ps1* スクリプトは、Azure Stack 証明機関と連携して、App Service で必要となる次の 4 つの証明書を作成します。

| ファイル名 | 用途 |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | App Service の既定の SSL 証明書 |
| api.appservice.local.azurestack.external.pfx | App Service API の SSL 証明書 |
| ftp.appservice.local.azurestack.external.pfx | App Service パブリッシャーの SSL 証明書 |
| sso.appservice.local.azurestack.external.pfx | App Service ID アプリケーションの証明書 |

証明書を作成するには、以下の手順に従ってください。

1. AzureStack\AzureStackAdmin アカウントを使用して Azure Stack Development Kit ホストにサインインします。
2. 管理者特権の PowerShell セッションを開きます。
3. ヘルパー スクリプトを展開したフォルダーから *Create-AppServiceCerts.ps1* スクリプトを実行します。 このスクリプトは、証明書を作成するために App Service で必要となるスクリプトと同じフォルダーに 4 つの証明書を作成します。
4. .pfx ファイルを保護するためのパスワードを入力し、そのパスワードを書き留めておきます。 後でそれを App Service on Azure Stack インストーラーに入力する必要があります。

#### <a name="create-appservicecertsps1-script-parameters"></a>Create-AppServiceCerts.ps1 スクリプトのパラメーター

| パラメーター | 必須または省略可能 | 既定値 | 説明 |
| --- | --- | --- | --- |
| pfxPassword | 必須 | Null | 証明書の秘密キーを保護するのに役立つパスワード |
| DomainName | 必須 | local.azurestack.external | Azure Stack のリージョンとドメイン サフィックス |

### <a name="certificates-required-for-azure-stack-production-deployment-of-azure-app-service"></a>Azure App Service の Azure Stack の運用環境デプロイに必要な証明書

運用環境でリソース プロバイダーを実行するには、次の証明書を提供する必要があります。

- 既定のドメイン証明書
- API 証明書
- 公開証明書
- ID 証明書

#### <a name="default-domain-certificate"></a>既定のドメイン証明書

既定のドメイン証明書は、フロントエンド ロールに配置されます。 ユーザー アプリケーションが Azure App Service へのワイルドカードまたは既定のドメイン要求のためにこの証明書を使用します。 この証明書は、ソース管理操作 (Kudu) にも使用されます。

この証明書は .pfx 形式の、3 つのサブジェクトのワイルドカード証明書である必要があります。 この要件により、1 つの証明書で、既定のドメインとソース管理操作の SCM エンドポイントの両方に対応できます。

| 形式 | 例 |
| --- | --- |
| \*.appservice.\<region\>.\<DomainName\>.\<extension\> | \*.appservice.redmond.azurestack.external |
| \*.scm.appservice.<region>.<DomainName>.<extension> | \*.scm.appservice.redmond.azurestack.external |
| \*.sso.appservice.<region>.<DomainName>.<extension> | \*.sso.appservice.redmond.azurestack.external |

#### <a name="api-certificate"></a>API 証明書

API 証明書は、管理ロールに配置されます。 リソース プロバイダーは、これを使用して API 呼び出しをセキュリティ保護しやすくします。 公開用の証明書には、API の DNS エントリと一致するサブジェクトが含まれている必要があります。

| 形式 | 例 |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>公開証明書

パブリッシャー ロール用の証明書は、コンテンツ アップロード時に、アプリケーション所有者の FTPS トラフィックを保護します。 公開用の証明書には、FTPS の DNS エントリと一致するサブジェクトが含まれている必要があります。

| 形式 | 例 |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>ID 証明書

ID アプリケーション用の証明書は以下が可能です。

- コンピューティング リソース プロバイダーとの統合をサポートするための Azure Active Directory (Azure AD) または Active Directory フェデレーション サービス (AD FS) ディレクトリ、Azure Stack、および App Service の間の統合。
- Azure App Service on Azure Stack 内の複数の高度な開発者ツールでのシングル サインオン シナリオ。

ID 用の証明書には、次の形式に一致するサブジェクトが含まれている必要があります。

| 形式 | 例 |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |

## <a name="virtual-network"></a>Virtual network

Azure App Service on Azure Stack を使用すると、リソース プロバイダーを既存の仮想ネットワークにデプロイできます。あるいは、デプロイメントの一部として仮想ネットワークを作成できます。 既存の仮想ネットワークを使用すると、内部 IP を使用して、Azure App Service on Azure Stack で必要なファイル サーバーと SQL Server に接続できます。 Azure App Service on Azure Stack をインストールする前に、仮想ネットワークを次のアドレス範囲とサブネットで構成する必要があります。

仮想ネットワーク - /16

サブネット

- ControllersSubnet /24
- ManagementServersSubnet /24
- FrontEndsSubnet /24
- PublishersSubnet /24
- WorkersSubnet /21

## <a name="prepare-the-file-server"></a>ファイル サーバーを準備する

Azure App Service では、ファイル サーバーを使用する必要があります。 運用環境のデプロイの場合は、ファイル サーバーを高可用性で、かつ障害を処理できるように構成する必要があります。

Azure Stack Development Kit のデプロイのみの場合は、[Azure Resource Manager デプロイ テンプレートの例](https://aka.ms/appsvconmasdkfstemplate)を使用して、構成された単一ノードのファイル サーバーをデプロイできます。 この単一ノードのファイル サーバーは、ワークグループ内に存在します。

>[!IMPORTANT]
> App Service を既存の仮想ネットワークにデプロイするように選択した場合、ファイル サーバーを App Service とは別のサブネットにデプロイする必要があります。

### <a name="provision-groups-and-accounts-in-active-directory"></a>Active Directory でグループとアカウントをプロビジョニングする

1. 次の Active Directory グローバル セキュリティ グループを作成します。

   - FileShareOwners
   - FileShareUsers

2. 次の Active Directory アカウントをサービス アカウントとして作成します。

   - FileShareOwner
   - FileShareUser

   セキュリティのベスト プラクティスとしては、これらのアカウント (およびすべての Web ロール) のユーザーは一意であり、強力なユーザー名とパスワードを使用する必要があります。 次の条件でパスワードを設定します。

   - **[パスワードを無期限にする]** を有効にします。
   - **[ユーザーはパスワードを変更できない]** を有効にします。
   - **[ユーザーは次回ログオン時にパスワードの変更が必要]** を無効にします。

3. 次のように、グループのメンバーシップにアカウントを追加します。

   - **FileShareOwner** を **FileShareOwners** グループに追加します。
   - **FileShareUser** を **FileShareUsers** グループに追加します。

### <a name="provision-groups-and-accounts-in-a-workgroup"></a>ワークグループでグループとアカウントをプロビジョニングする

>[!NOTE]
> ファイル サーバーを構成している場合は、**管理者コマンド プロンプト**で次のすべてのコマンドを実行します。 <br>***PowerShell を使用しないでください。***

Azure Resource Manager テンプレートを使用するとき、ユーザーは既に作成されています。

1. 次のコマンドを実行して、FileShareOwner アカウントと FileShareUser アカウントを作成します。 `<password>` を独自の値に置き換えます。

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

コンテンツ共有には、テナント Web サイトのコンテンツが含まれています。 1 つのファイル サーバーでコンテンツ共有をプロビジョニングする手順は、Active Directory とワークグループの両方の環境で同じです。 ただし、Active Directory 内のフェールオーバー クラスターでは異なります。

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>1 つのファイル サーバー (Active Directory またはワークグループ) でコンテンツ共有をプロビジョニングする

1 つのファイル サーバーで、管理者特権のコマンド プロンプトで次のコマンドを実行します。 `C:\WebSites` の値を、使用している環境内の対応するパスに置き換えます。

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="add-the-fileshareowners-group-to-the-local-administrators-group"></a>ローカルの Administrators グループに FileShareOwners グループを追加する

Windows リモート管理を正しく機能させるには、ローカルの Administrators グループに FileShareOwners グループを追加する必要があります。

#### <a name="active-directory"></a>Active Directory

ファイル サーバーで、またはフェールオーバー クラスター ノードとして機能するすべてのファイル サーバーで、管理者特権でのコマンド プロンプトで次のコマンドを実行します。 `<DOMAIN>` の値を、使用するドメイン名に置き換えます。

```DOS
set DOMAIN=<DOMAIN>
net localgroup Administrators %DOMAIN%\FileShareOwners /add
```

#### <a name="workgroup"></a>ワークグループ

ファイル サーバーで、管理者特権でのコマンド プロンプトで次のコマンドを実行します。

```DOS
net localgroup Administrators FileShareOwners /add
```

### <a name="configure-access-control-to-the-shares"></a>共有へのアクセス制御を構成する

ファイル サーバーで、またはフェールオーバー クラスター ノード (現在のクラスター リソース所有者) で、管理者特権でのコマンド プロンプトで次のコマンドを実行します。 斜体の値を、使用している環境に固有の値に置き換えます。

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

## <a name="prepare-the-sql-server-instance"></a>SQL Server インスタンスを準備する

Azure App Service on Azure Stack のホスティングおよび計測データベースの場合は、App Service データベースを保持するための SQL Server インスタンスを準備する必要があります。

Azure Stack Development Kit のデプロイの場合は、SQL Server Express 2014 SP2 以降を使用できます。

運用と高可用性を目的とする場合は、SQL Server 2014 SP2 以降の完全バージョンを使用し、混合モード認証を有効にして、[高可用性構成](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server)でデプロイする必要があります。

Azure App Service on Azure Stack の SQL Server インスタンスは、すべての App Service ロールからアクセスできる必要があります。 SQL Server は、Azure Stack の既定のプロバイダー サブスクリプション内でデプロイできます。 あるいは、組織内の既存のインフラストラクチャーを利用できます (Azure Stack に接続されている場合)。 Azure Marketplace イメージを使用している場合は、それに応じてファイアウォールを構成することを忘れないでください。

>[!NOTE]
> Marketplace 管理機能により、多数の SQL IaaS 仮想マシン イメージを使用できます。 Marketplace 項目を使用して VM をデプロイする前に、必ず SQL IaaS 拡張機能の最新バージョンをダウンロードしてください。 これらの SQL イメージは、Azure で使用できる SQL VM と同じです。 これらのイメージから作成された SQL VM の場合、IaaS 拡張機能や対応するポータル拡張機能により、修正プログラムの自動適用やバックアップなどの機能が提供されます。
>
どの SQL Server ロールの場合も、既定のインスタンスまたは名前付きインスタンスを使用できます。 名前付きインスタンスを使用する場合は、手動で SQL Server Browser サービスを開始し、ポート 1434 を開くようにしてください。

>[!IMPORTANT]
> App Service を既存の仮想ネットワークにデプロイするように選択した場合、SQL Server を App Service とファイル サーバーとは別のサブネットにデプロイする必要があります。
>

## <a name="create-an-azure-active-directory-application"></a>Azure Active Directory アプリケーションを作成する

Azure AD サービス プリンシパルで以下の操作をサポートするように構成します。

- worker 層での仮想マシン スケール セットの統合。
- Azure Functions ポータルと高度な開発者ツールのための SSO。

次の手順は、Azure AD で保護されている Azure Stack 環境のみに適用されます。

管理者は、SSO を次のように構成する必要があります。

- App Service 内の高度な開発者ツール (Kudu) を有効にする。
- Azure Functions ポータルのエクスペリエンスを有効にする。

次の手順に従います。

1. azurestack\AzureStackAdmin として PowerShell インスタンスを開きます。
2. [前提条件の手順](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts)でダウンロードして展開したスクリプトの場所に移動します。
3. [PowerShell for Azure Stack をインストールします](azure-stack-powershell-install.md)。
4. **Create-AADIdentityApp.ps1** スクリプトを実行します。 メッセージが表示されたら、Azure Stack デプロイのために使用している Azure AD テナント ID を入力します。 たとえば、「**myazurestack.onmicrosoft.com**」と入力します。
5. **[資格情報]** ウィンドウで、Azure AD サービスの管理者アカウントとパスワードを入力します。 **[OK]** を選択します。
6. [先ほど作った証明書](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack)について、証明書ファイル パスと証明書パスワードを入力します。 既定でこの手順のために作られる証明書は、**sso.appservice.local.azurestack.external.pfx** です。
7. このスクリプトは、テナント Azure AD インスタンス内に新しいアプリケーションを作成します。 PowerShell の出力で返されるアプリケーション ID を書き留めておきます。 インストール時にこの情報が必要になります。
8. 新しいブラウザー ウィンドウを開き、Azure Active Directory サービス管理者として [Azure Portal](https://portal.azure.com) にサインインします。
9. Azure AD リソース プロバイダーを開きます。
10. **[アプリの登録]** を選択します。
11. 手順 7 の一部として返されたアプリケーション ID を検索します。 一覧に App Service アプリケーションが表示されます。
12. 一覧で **[アプリケーション]** を選択します。
13. **[設定]** を選択します。
14. **[必要なアクセス許可]** > **[アクセス許可の付与]** > **[はい]** の順に選択します。

```PowerShell
    Create-AADIdentityApp.ps1
```

| パラメーター | 必須または省略可能 | 既定値 | 説明 |
| --- | --- | --- | --- |
| DirectoryTenantName | 必須 | Null | Azure AD テナント ID。 GUID または文字列を指定します。 例として、myazureaaddirectory.onmicrosoft.com があります。 |
| AdminArmEndpoint | 必須 | Null | 管理者の Azure Resource Manager エンドポイント。 例として、adminmanagement.local.azurestack.external があります。 |
| TenantARMEndpoint | 必須 | Null | テナントの Azure Resource Manager エンドポイント。 例として、management.local.azurestack.external があります。 |
| AzureStackAdminCredential | 必須 | Null | Azure AD サービス管理者の資格情報。 |
| CertificateFilePath | 必須 | Null | 先ほど生成された ID アプリケーション証明書ファイルへの**完全なパス**。 |
| CertificatePassword | 必須 | Null | 証明書の秘密キーを保護するのに役立つパスワード。 |

## <a name="create-an-active-directory-federation-services-application"></a>Active Directory フェデレーション サービス アプリケーションを作成する

AD FS によって保護されている Azure Stack 環境の場合、 AD FS サービス プリンシパルで以下の操作をサポートするように構成する必要があります。

- worker 層での仮想マシン スケール セットの統合。
- Azure Functions ポータルと高度な開発者ツールのための SSO。

管理者は、SSO を次のように構成する必要があります。

- worker 層での仮想マシン スケール セットの統合のためにサービス プリンシパルを構成する。
- App Service 内の高度な開発者ツール (Kudu) を有効にする。
- Azure Functions ポータルのエクスペリエンスを有効にする。

次の手順に従います。

1. azurestack\AzureStackAdmin として PowerShell インスタンスを開きます。
2. [前提条件の手順](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#download-the-azure-app-service-on-azure-stack-installer-and-helper-scripts)でダウンロードして展開したスクリプトの場所に移動します。
3. [PowerShell for Azure Stack をインストールします](azure-stack-powershell-install.md)。
4. **Create-ADFSIdentityApp.ps1** スクリプトを実行します。
5. **[資格情報]** ウィンドウで、AD FS クラウドの管理者アカウントとパスワードを入力します。 **[OK]** を選択します。
6. [先ほど作った証明書](https://docs.microsoft.com/en-gb/azure/azure-stack/azure-stack-app-service-before-you-get-started#certificates-required-for-azure-app-service-on-azure-stack)について、証明書ファイル パスと証明書パスワードを入力します。 既定でこの手順のために作られる証明書は、**sso.appservice.local.azurestack.external.pfx** です。

```PowerShell
    Create-ADFSIdentityApp.ps1
```

| パラメーター | 必須または省略可能 | 既定値 | 説明 |
| --- | --- | --- | --- |
| AdminArmEndpoint | 必須 | Null | 管理者の Azure Resource Manager エンドポイント。 例として、adminmanagement.local.azurestack.external があります。 |
| PrivilegedEndpoint | 必須 | Null | 特権エンドポイント。 例として、AzS-ERCS01 があります。 |
| CloudAdminCredential | 必須 | Null | Azure Stack クラウド管理者のドメイン アカウントの資格情報。 例として、Azurestack\CloudAdmin があります。 |
| CertificateFilePath | 必須 | Null | ID アプリケーションの証明書 PFX ファイルへの**完全なパス**。 |
| CertificatePassword | 必須 | Null | 証明書の秘密キーを保護するのに役立つパスワード。 |

## <a name="next-steps"></a>次の手順

[App Service リソース プロバイダーのインストール](azure-stack-app-service-deploy.md)
