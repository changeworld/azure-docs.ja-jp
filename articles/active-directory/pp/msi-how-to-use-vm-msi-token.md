---
title: "ユーザー割り当ての管理対象サービス ID を使用して VM 上にアクセス トークンを取得する方法"
description: "Azure VM からユーザー割り当て MSI を使用して OAuth アクセス トークンを取得するための詳細な手順と例。"
services: active-directory
documentationcenter: 
author: BryanLa
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5c9bf052ecb2e9c79e0eb627a0fd709d587125cd
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2017
---
# <a name="acquire-an-access-token-for-a-vm-user-assigned-managed-service-identity-msi"></a>ユーザー割り当ての管理対象サービス ID (MSI) で VM のアクセス トークンを取得する

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]この記事では、トークンの取得に使用する各種コードとスクリプトの例を提供するほか、トークンの有効期限や HTTP エラーの処理などの重要なトピックに関するガイダンスも提供します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

この記事の Azure PowerShell の例を使用する場合は、[Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) の最新バージョンをインストールする必要があります。

> [!IMPORTANT]
> - この記事のすべてのサンプル コード/スクリプトでは、クライアントが、MSI 対応の仮想マシンで実行されていることを前提としています。 お使いの VM にリモート接続するには、Azure ポータルで VM への "接続" 機能を使用します。 VM で MSI を有効にする方法の詳細については、[Azure CLI を使用して、VM にユーザー割り当ての管理対象サービス ID (MSI) を構成する](msi-qs-configure-cli-windows-vm.md)手順に関する記事や、関連するいずれかの記事 (ポータル、PowerShell、テンプレート、または Azure SDK を使用) をご覧ください。 

## <a name="overview"></a>概要

クライアント アプリケーションは、指定されたリソースにアクセスするために、MSI の[アプリ専用アクセス トークン](~/articles/active-directory/develop/active-directory-dev-glossary.md#access-token)を要求できます。 このトークンは、[MSI サービス プリンシパル](msi-overview.md#how-does-it-work)に基づいています。 そのため、独自のサービス プリンシパルでアクセス トークンを取得するために、クライアントそのものを登録する必要がありません。 トークンは、[クライアント資格情報を必要とするサービス間の呼び出し](~/articles/active-directory/active-directory-protocols-oauth-service-to-service.md)のベアラー トークンとしての使用に適しています。

|  |  |
| -------------- | -------------------- |
| [HTTP を使用してトークンを取得する](#get-a-token-using-http) | MSI トークン エンドポイントのプロトコルの詳細 |
| [CURL を使用してトークンを取得する](#get-a-token-using-curl) | Bash クライアントまたは CURL クライアントからの MSI REST エンドポイントの使用例 |
| [トークンの有効期限の処理](#handling-token-expiration) | 有効期限が切れたアクセス トークンの処理に関するガイダンス |
| [エラー処理](#error-handling) | MSI トークン エンドポイントから返される HTTP エラーの処理に関するガイダンス |
| [Azure サービスのリソース ID](#resource-ids-for-azure-services) | サポートされている Azure サービスのリソース ID を取得する場所 |

## <a name="get-a-token-using-http"></a>HTTP を使用してトークンを取得する 

アクセス トークンの取得に使用する基本的なインターフェイスは REST に基づいているため、HTTP REST の呼び出しを行える VM 上で実行されている、すべてのクライアント アプリケーションにアクセスできます。 これは、クライアントが仮想マシン上の localhost エンドポイントを使用する点以外は、Azure AD のプログラミング モデルと同じです (Azure AD のプログラミング モデルでは、Azure AD エンドポイントを使用)。

要求のサンプル:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id=712eac09-e943-418c-9be6-9fd5c91078bl HTTP/1.1
Metadata: true
```

| 要素 | [説明] |
| ------- | ----------- |
| `GET` | HTTP 動詞。エンドポイントからデータを取得する必要があることを示します。 この例では、OAuth アクセス トークンです。 | 
| `http://localhost:50342/oauth2/token` | 構成可能な MSI エンドポイント。既定のポートは 50342 です。 |
| `resource` | クエリ文字列パラメーター。ターゲット リソースのアプリ ID URI です。 発行されたトークンの `aud` (audience) 要求にも表示されます。 この例では、アプリ ID URI が https://management.azure.com/ の Azure Resource Manager にアクセスするためのトークンを要求しています。 |
| `client_id` | ユーザー割り当ての MSI を表すサービス プリンシパルのクライアント ID (アプリ ID とも呼ばれる) を示す、クエリ文字列パラメーター。 この値は、ユーザー割り当て MSI の作成中に `clientId` プロパティに返されます。 この例では、クライアント ID "712eac09-e943-418c-9be6-9fd5c91078bl" のトークンを要求します。 |
| `Metadata` | HTTP 要求ヘッダー フィールド。サーバー側のリクエスト フォージェリ (SSRF) 攻撃に対する軽減策として MSI に必要です。 この値は、"true" に設定し、すべて小文字にする必要があります。

応答のサンプル:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
  "client_id":"712eac09-e943-418c-9be6-9fd5c91078bl"
}
```

| 要素 | [説明] |
| ------- | ----------- |
| `access_token` | 要求されたアクセス トークン。 セキュリティで保護された REST API を呼び出すとき、トークンは `Authorization` 要求ヘッダー フィールドに "ベアラー" トークンとして埋め込まれ、API が呼び出し元を認証できるようにします。 | 
| `expires_in` | アクセス トークンが発行されてから期限切れになるまでの有効継続時間 (秒単位)。 発行時刻はトークンの `iat` 要求で確認できます。 |
| `expires_on` | アクセス トークンが期限切れになるまでの期間。 日付は "1970-01-01T0:0:0Z UTC" からの秒数として表されます (トークンの `exp` 要求に対応)。 |
| `not_before` | アクセス トークンが有効になり、承認されるまでの期間。 日付は "1970-01-01T0:0:0Z UTC" からの秒数として表されます (トークンの `nbf` 要求に対応)。 |
| `resource` | アクセス トークンの要求対象リソース。要求の `resource` クエリ文字列パラメーターと一致します。 |
| `token_type` | トークンの種類。つまり "ベアラー" アクセス トークン。リソースが、このトークンのベアラーへのアクセスを提供できることを意味します。 |
| `client_id` | ユーザー割り当ての MSI を表すサービス プリンシパルのクライアント ID (アプリ ID とも呼ばれる)。トークンは、このクライアント ID のために要求されました。 |

## <a name="get-a-token-using-curl"></a>CURL を使用してトークンを取得する

`client_id` パラメーターの <MSI CLIENT ID> 値では、必ずお使いのユーザー割り当て MSI のサービス プリンシパルのクライアント ID (アプリ ID とも呼ばれる) を使用します。 この値は、ユーザー割り当て MSI の作成中に `clientId` プロパティに返されます。

   ```bash
   response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/&client_id=<MSI CLIENT ID>" -H Metadata:true -s)
   access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   echo The MSI access token is $access_token
   ```

   応答の例:

   ```bash
   user@vmLinux:~$ response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/&client_id=9d484c98-b99d-420e-939c-z585174b63bl" -H Metadata:true -s)
   user@vmLinux:~$ access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   user@vmLinux:~$ echo The MSI access token is $access_token
   The MSI access token is eyJ0eXAiOiJKV1QiLCJhbGciO...
   ```

## <a name="handling-token-expiration"></a>トークンの有効期限の処理

ローカル MSI サブシステムではトークンがキャッシュされます。 したがって、トークンは好きなだけ呼び出すことができます。また、次の場合にのみ、Azure AD の結果に対するネットワーク呼び出しが可能です。
- キャッシュにトークンがないため、キャッシュ ミスが発生した
- トークンの有効期限が切れている

コードでトークンをキャッシュする場合は、リソースがトークンの期限切れ示している場合のシナリオを処理できるよう準備する必要があります。

## <a name="error-handling"></a>エラー処理 

MSI エンドポイントは、HTTP 応答メッセージのヘッダーに含まれる状態コード フィールドを通じて、4xx エラーまたは 5xx エラーのいずれかとして、エラーを通知します。

| 状態コード | エラーの理由 | 処理方法 |
| ----------- | ------------ | ------------- |
| 要求の 4xx エラーです。 | 1 つ以上の要求パラメーターが正しくありませんでした。 | 再試行はしないでください。  詳しくは、エラーの詳細を確認します。  4xx エラーは、デザイン時のエラーです。|
| サービスからの 5xx の一時的なエラーです。 | MSI のサブシステムまたは Azure Active Directory から、一時的なエラーが返されました。 | 少なくとも 1 秒間待機した後に、安全に再試行できます。  再試行が早すぎる場合や再試行の回数が多すぎる場合は、Azure AD からレート制限エラー (429) が返されることがあります。|

エラーが発生すると、対応する HTTP 応答本文に、JSON とエラーの詳細が含まれます。

| 要素 | [説明] |
| ------- | ----------- |
| error   | エラー識別子。 |
| error_description | エラーの詳細な説明。 **エラーの説明は、予告なく変更になる場合があります。エラーの説明に含まれる値に基づいて分岐するコードを作成しないでください。**|

### <a name="http-response-reference"></a>HTTP 応答リファレンス

このセクションでは、想定されるエラー応答について説明します。 "200 OK" の状態は成功応答であり、access_token 要素内の応答本文の JSON にアクセス トークンが含まれています。

| 状態コード | エラー | エラーの説明 | 解決策 |
| ----------- | ----- | ----------------- | -------- |
| 400 Bad Request | invalid_resource | AADSTS50001: *\<URI\>* という名前のアプリケーションが *\<TENANT-ID\>* という名前のテナントに見つかりませんでした。 このエラーは、アプリケーションがテナントの管理者によってインストールされていない場合や、アプリケーションがテナント内のいずれのユーザーによっても同意されていない場合に発生することがあります。 間違ったテナントに認証要求を送信した可能性があります。\ | (Linux のみ) |
| 400 Bad Request | bad_request_102 | 必要なメタデータ ヘッダーが指定されていません | 要求で `Metadata` 要求ヘッダー フィールドが見つからないか、形式が正しくありません。 値は `true` として指定し、すべて小文字にする必要があります。 例については、「[HTTP を使用してトークンを取得する](#get-a-token-using-http) 」セクションの「要求のサンプル」をご覧ください。|
| 401 権限がありません | unknown_source | 不明なソース *\<URI\>* | HTTP GET 要求の URI の形式が正しいことを確認します。 `scheme:host/resource-path` 部分は、`http://localhost:50342/oauth2/token` として指定する必要があります。 例については、「[HTTP を使用してトークンを取得する](#get-a-token-using-http) 」セクションの「要求のサンプル」をご覧ください。|
|           | invalid_request | 要求に必要なパラメーターが含まれていないか、要求に無効なパラメーター値が含まれているか、要求に複数回パラメーターが含まれているか、要求の形式が正しくないかのいずれかです。 |  |
|           | unauthorized_client | クライアントには、このメソッドを使用してアクセス トークンを要求する権限がありません。 | 拡張機能の呼び出しにローカル ループバックを使用しなかった要求や、MSI が正しく構成されていない VM が原因です。 VM の構成についてサポートが必要な場合は、「[Azure Portal を使用して、VM 管理対象サービス ID (MSI) を構成する](msi-qs-configure-portal-windows-vm.md)」をご覧ください。 |
|           | access_denied | リソース所有者または承認サーバーによって、要求が拒否されました。 |  |
|           | unsupported_response_type | このメソッドを使用したアクセス トークンの取得は、承認サーバーによってサポートされていません。 |  |
|           | invalid_scope | 要求されたスコープが無効、不明、または形式が正しくありません。 |  |
| 500 内部サーバー エラー | unknown | Active Directory からのトークンの取得に失敗しました。 詳細については、*\<file path\>* のログを参照してください | VM で MSI が有効化されていることを確認します。 VM の構成についてサポートが必要な場合は、「[Azure Portal を使用して、VM 管理対象サービス ID (MSI) を構成する](msi-qs-configure-portal-windows-vm.md)」をご覧ください。<br><br>また、HTTP GET 要求の URI、特にクエリ文字列で指定されたリソース URI の形式が正しいかどうかを確認します。 例については、「[HTTP を使用してトークンを取得する](#get-a-token-using-http)」の「要求のサンプル」を参照してください。または、「[Azure AD 認証をサポートしている Azure サービス](msi-overview.md#azure-services-that-support-azure-ad-authentication)」で、サービスの一覧と、そのリソース ID を参照してください。

## <a name="resource-ids-for-azure-services"></a>Azure サービスのリソース ID

Azure AD をサポートし、MSI でテスト済みのリソースの一覧と、そのリソース ID については、「[Azure AD 認証をサポートしている Azure サービス](msi-overview.md#azure-services-that-support-azure-ad-authentication)」をご覧ください。


## <a name="next-steps"></a>次の手順

- Azure VM 上で MSI を有効にするには、[Azure CLI を使用して、VM にユーザー割り当ての管理対象サービス ID (MSI) を構成する](msi-qs-configure-cli-windows-vm.md)手順に関する記事をご覧ください。

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。








