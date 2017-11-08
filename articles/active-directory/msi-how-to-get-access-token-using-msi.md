---
title: "サインインとトークン取得に Azure VM の管理対象サービス ID を使用する方法"
description: "サインインおよびアクセス トークン取得のために、Azure VM の MSI サービス プリンシパルを使用する方法をステップ バイ ステップで説明します。"
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2017
ms.author: bryanla
ms.openlocfilehash: 905e7b0d8a0c45c98a86882a8c8f387be0950f9f
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2017
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in-and-token-acquisition"></a>サインインとトークン取得に Azure VM の管理対象サービス ID (MSI) を使用する方法 
[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)] Azure VM で MSI を有効にした後、サインインに MSI を使用して、アクセス トークンを要求することができます。 この記事では、次に示すさまざまな方法で、MSI [サービス プリンシパル](develop/active-directory-dev-glossary.md#service-principal-object)を使用してサインインし、[アプリ専用のアクセス トークン](develop/active-directory-dev-glossary.md#access-token)を取得します。

- PowerShell または Azure CLI からのサイレント/無人のサインイン
- .NET、PowerShell、Bash/CURL、REST など、さまざまなクライアントのトークン取得
- .NET、Java、Node.js、Python、Ruby など、Azure SDK を使用したサインイン

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

この記事の PowerShell の例を使用する場合は、[Azure PowerShell バージョン 4.3.1](https://www.powershellgallery.com/packages/AzureRM) 以上をインストールする必要があります。 この記事で使用する Azure CLI の例には、次の 3 つのオプションがあります。
- Azure Portal から [Azure Cloud Shell](../cloud-shell/overview.md) を使用する。
- Azure CLI コード ブロックの右上隅にある "お試しください" ボタンを利用して、埋め込まれた Azure Cloud Shell を使用する。
- [CLI 2.0 の最新バージョン (2.0.13 以降) をインストールする](https://docs.microsoft.com/cli/azure/install-azure-cli) (ローカル コマンド プロンプトで CLI を使用する場合)。 


> [!IMPORTANT]
> - この記事のすべてのサンプル コード/スクリプトでは、クライアントが、MSI 対応の仮想マシンで実行されていることを前提としています。 VM で MSI を有効にする方法の詳細については、「[Azure Portal を使用して、VM 管理対象サービス ID (MSI) を構成する](msi-qs-configure-portal-windows-vm.md)」、または関連する記事 (PowerShell、CLI、テンプレート、または Azure SDK を使用) のいずれかを参照してください。 
> - コード・スクリプトの例で承認エラー (403/AuthorizationFailed) が発生しないように、VM の ID には、VM スコープで "読み取り" アクセス権を付与して、VM での Azure Resource Manager 操作を許可する必要があります。 詳細については、「[Azure Portal を使用して、リソースに管理対象サービス ID (MSI) アクセスを割り当てる](msi-howto-assign-access-portal.md)」を参照してください。
> - 以降のセクションに進む前に、Azure Portal で VM の "接続" 機能を使用して、MSI 対応 VM にリモート接続する必要があります。

## <a name="how-to-sign-in-from-powershell-or-cli-using-msi"></a>MSI を使用して PowerShell または CLI からサインインする方法

以前は、独自の ID でスクリプトを実行する場合は、次の操作を行っていました。
- Azure AD で機密/Web クライアント アプリケーションとして登録する
- アプリケーションのクライアント ID/シークレット資格情報を使用してサインインする

MSI を使用すると、スクリプト クライアントで、Azure AD に登録する必要も、資格情報を提供する必要もありません。 以降の例では、VM の MSI サービス プリンシパルを使用してサインインする方法を示します。

### <a name="azure-powershell"></a>Azure PowerShell

次のスクリプトは、以下を行う方法を示しています。

- Azure VM の MSI アクセス トークンを取得する
- アクセス トークンを使用して、対応する MSI サービス プリンシパルで Azure AD にサインインする
- MSI サービス プリンシパルで Azure Resource Manager を呼び出して、サービス プリンシパルの ID を取得する

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token

# Use the access token to sign in under the MSI service principal
Login-AzureRmAccount -AccessToken $access_token -AccountId “CLIENT”

# The MSI service principal is now signed in for this session.
# Next, a call to Azure Resource Manager is made to get the service principal ID for the VM's MSI. 
$spID = (Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>).identity.principalid
echo "The MSI service principal ID is $spID"
```
   
### <a name="azure-cli"></a>Azure CLI

次のスクリプトは、以下を行う方法を示しています。

- VM の MSI サービス プリンシパルで Azure AD にサインインする
- MSI サービス プリンシパルで Azure Resource Manager を呼び出して、サービス プリンシパルの ID を取得する

```azurecli-interactive
az login --msi
spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
echo The MSI service principal ID is $spID
```

## <a name="how-to-acquire-an-access-token-from-msi"></a>MSI からアクセス トークンを取得する方法

MSI を使用すると、クライアント アプリケーション/スクリプトは、Azure AD に登録したりクライアント ID やシークレットを提示したりしなくても、アクセス トークンを取得できます。 クライアントは、ローカル MSI エンドポイントにアクセス トークンを要求するだけです。アプリケーション資格情報を提示する必要はありません。 アプリ専用アクセス トークンは MSI サービス プリンシパルに対して発行され、[クライアント資格情報を必要とするサービス間の呼び出し](active-directory-protocols-oauth-service-to-service.md)のベアラー トークンとしての使用に適しています。

以降の例では、VM の MSI を使用してトークンを取得する方法を示します。

### <a name="net"></a>.NET

> [!IMPORTANT]
> Azure AD Authentication Library (ADAL) は、MSI と統合されていません。 MSI を使用してアクセス トークンを取得するには、VM でローカル MSI エンドポイントに要求を送信してください。 詳細については、[MSI のよく寄せられる質問と既知の問題](msi-known-issues.md#frequently-asked-questions-faqs)に関する記事をご覧ください。

```csharp
// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

### <a name="azure-powershell-token"></a>Azure PowerShell

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"
```

### <a name="bashcurl"></a>Bash/CURL

```bash
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

### <a name="rest"></a>REST ()

要求のサンプル:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| 要素 | Description |
| ------- | ----------- |
| `GET` | HTTP 動詞。エンドポイントからデータを取得する必要があることを示します。 この例では、OAuth アクセス トークンです。 | 
| `http://localhost:50342/oauth2/token` | 構成可能な MSI エンドポイント。既定のポートは 50342 です。 |
| `resource` | クエリ文字列パラメーター。ターゲット リソースのアプリ ID URI です。 発行されたトークンの `aud` (audience) 要求にも表示されます。 この例では、アプリ ID URI が https://management.azure.com/ の Azure Resource Manager にアクセスするためのトークンを要求しています。 |
| `Metadata` | HTTP 要求ヘッダー フィールド。サーバー側のリクエスト フォージェリ (SSRF) 攻撃に対する軽減策として MSI に必要です。 この値は、"true" に設定し、すべて小文字にする必要があります。

応答のサンプル:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| 要素 | Description |
| ------- | ----------- |
| `access_token` | 要求されたアクセス トークン。 REST API を呼び出すとき、トークンは `Authorization` 要求ヘッダー フィールドに "ベアラー" トークンとして埋め込まれ、API が呼び出し元を認証できるようにします。 | 
| `refresh_token` | MSI では使用されません。 |
| `expires_in` | アクセス トークンが発行されてから期限切れになるまでの有効継続時間 (秒単位)。 発行時刻はトークンの `iat` 要求で確認できます。 |
| `expires_on` | アクセス トークンが期限切れになるまでの期間。 日付は "1970-01-01T0:0:0Z UTC" からの秒数として表されます (トークンの `exp` 要求に対応)。 |
| `not_before` | アクセス トークンが有効になり、承認されるまでの期間。 日付は "1970-01-01T0:0:0Z UTC" からの秒数として表されます (トークンの `nbf` 要求に対応)。 |
| `resource` | アクセス トークンの要求対象リソース。要求の `resource` クエリ文字列パラメーターと一致します。 |
| `token_type` | トークンの種類。つまり "ベアラー" アクセス トークン。リソースが、このトークンのベアラーへのアクセスを提供できることを意味します。 |

## <a name="how-to-use-msi-with-azure-sdk-libraries"></a>Azure SDK ライブラリで MSI を使用する方法

Azure は、一連の [Azure SDK](https://azure.microsoft.com/downloads) によって、複数のプログラミング プラットフォームをサポートしています。 そのいくつかが、MSI を使用したサインインをサポートするために更新され、対応するサンプルを提供することで、その使用方法を示しています。 次の一覧は、サポートが追加されると更新されます。

| SDK | サンプル |
| --- | ------ | 
| .NET | [管理対象サービス ID ](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) を使用して、Windows VM から ARM テンプレートを展開する |
| .NET Core | [管理対象サービス ID を使用して、Linux VM から Azure サービスを呼び出す](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js| [管理対象サービス ID を使用してリソースを管理する](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python | [MSI を使用して VM 内から認証する](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby   | [MSI 対応 VM からリソースを管理する](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) | 

## <a name="additional-information"></a>追加情報

### <a name="token-expiration"></a>トークンの有効期限

ローカル MSI サブシステムではトークンがキャッシュされます。 したがって、トークンは好きなだけ呼び出すことができます。また、次の場合にのみ、Azure AD の結果に対するネットワーク呼び出しが可能です。
- キャッシュにトークンがないため、キャッシュ ミスが発生した
- トークンの有効期限が切れている

コードでトークンをキャッシュする場合は、リソースがトークンの期限切れ示している場合のシナリオを処理できるよう準備する必要があります。

### <a name="resource-ids-for-azure-services"></a>Azure サービスのリソース ID

MSI をサポートするサービスの一覧と、そのリソース ID については、「[Azure AD 認証をサポートしている Azure サービス](msi-overview.md#azure-services-that-support-azure-ad-authentication)」を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="sign-in-or-token-acquisition-fails"></a>サインインまたはトークンの取得に失敗する

MSI が正しく有効化されていることを確認します。 [Azure Portal](https://portal.azure.com) で Azure VM に戻り、次の操作を行います。

- [構成] ページを参照し、MSI の有効が [はい] になっていることを確認します。
- [拡張] ページを参照して、MSI 拡張機能が正常に展開されていることを確認します。

いずれかが正しくない場合は、リソース上で MSI を再デプロイするか、デプロイ エラーのトラブルシューティングを行う必要があります。

### <a name="http-request-error-responses"></a>HTTP 要求エラーの応答

- "*bad_request_102: 必要なメタデータ ヘッダーが指定されていません*"

  要求で `Metadata` 要求ヘッダー フィールドが見つからないか、形式が正しくありません。 値は `true` として指定し、すべて小文字にする必要があります。 例については、[上記の「REST」セクション](#rest)の「要求のサンプル」を参照してください。

- "*不明: Active Directory からのトークンの取得に失敗しました。詳細については、\<file path\> のログを参照してください*"

  HTTP GET 要求の URI、特にクエリ文字列で指定されたリソース URI の形式が正しいかどうかを確認します。 例については、[上記の「REST」セクション](#rest)の「要求のサンプル」を参照してください。または、「[Azure AD 認証をサポートしている Azure サービス](msi-overview.md#azure-services-that-support-azure-ad-authentication)」で、サービスの一覧と、そのリソース ID を参照してください。

- "*unknown_source: 不明なソース \<URI\>*"

  HTTP GET 要求の URI の形式が正しいことを確認します。 `scheme:host/resource-path` 部分は、`http://localhost:50342/oauth2/token` として指定する必要があります。 例については、[上記の「REST」セクション](#rest)の「要求のサンプル」を参照してください。

## <a name="related-content"></a>関連コンテンツ

- MSI の概要については、[管理対象サービス ID の概要](msi-overview.md)に関する記事をご覧ください。
- Azure VM で MSI を有効にするには、「[PowerShell を使用して Azure VM 管理対象サービスID (MSI) を構成する](msi-qs-configure-powershell-windows-vm.md)」を参照してください。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。

