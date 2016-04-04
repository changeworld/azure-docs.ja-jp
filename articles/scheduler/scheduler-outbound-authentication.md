<properties
 pageTitle="Scheduler 送信認証"
 description=""
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="03/09/2016"
 ms.author="krisragh"/>

# Scheduler 送信認証

Scheduler ジョブでは、認証を必要とするサービスを呼び出すことが必要になる場合があります。このようにして、呼び出されたサービスは、Scheduler ジョブがそのリソースにアクセスできるかどうかを確認できます。このようなサービスには、他の Azure サービス、Salesforce.com、Facebook、およびセキュリティで保護されたカスタム Web サイトが含まれます。

## 認証の追加と削除

Scheduler ジョブに認証を追加するのは簡単です。ジョブを作成または更新するときに JSON の子要素 `authentication` を `request` 要素に追加するだけです。PUT 要求、PATCH 要求、または POST 要求で `authentication` オブジェクトの一部として Scheduler サービスに渡されたシークレットが応答で返されることはありません応答では、シークレット情報は null に設定されます。または、認証済みのエンティティを表す公開トークンが含まれる場合があります。

認証を削除するには、ジョブに対する PUT または PATCH 操作を明示的に実行して、`authentication` オブジェクトを null に設定します。応答に認証プロパティが含まれることはありません。

現在サポートされている認証の種類は、(SSL/TLS クライアント証明書を使用するための) `ClientCertificate` モデル、(基本認証用の) `Basic` モデル、(Active Directory の OAuth 認証用の) `ActiveDirectoryOAuth` モデルのみです。

## ClientCertificate 認証の要求本文

`ClientCertificate` モデルを使用して認証を追加する場合は、要求本文に次の要素を指定します。

|要素|説明|
|:---|:---|
|_authentication (親要素)_|SSL クライアント証明書を使用するための認証オブジェクト。|
|_type_|必須。認証の種類。SSL クライアント証明書の場合、値 `ClientCertificate` を使用する必要があります。|
|_pfx_|必須。Base64 でエンコードされた PFX ファイルのコンテンツ。|
|_password_|必須。PFX ファイルにアクセスするためのパスワード。|


## ClientCertificate 認証の応答本文

認証情報を含めて要求を送信した場合、応答には次の認証に関連する要素が含まれます。

|要素 |説明 |
|:--|:--|
|_authentication (親要素)_ |SSL クライアント証明書を使用するための認証オブジェクト。|
|_type_ |認証の種類。SSL クライアント証明書の場合、値 `ClientCertificate` を使用します。|
|_certificateThumbprint_ |証明書の拇印。|
|_certificateSubjectName_ |証明書のサブジェクト識別名。|
|_certificateExpiration_ |証明書の有効期限日。|

## 基本認証の要求本文

`Basic` モデルを使用して認証を追加する場合は、要求本文に次の要素を指定します。

|要素|説明|
|:--|:--|
|_authentication (親要素)_ |基本認証を使用するための認証オブジェクト。|
|_type_ |必須。認証の種類。基本認証の場合、値 `Basic` を使用する必要があります。|
|_username_ |必須。認証するユーザー名。|
|_password_ |必須。認証するパスワード。|

## 基本認証の応答本文

認証情報を含めて要求を送信した場合、応答には次の認証に関連する要素が含まれます。

|要素|説明|
|:--|:--|
|_authentication (親要素)_ |基本認証を使用するための認証オブジェクト。|
|_type_ |認証の種類。基本認証の場合、値 `Basic` を使用します。|
|_username_ |認証されたユーザー名。|

## ActiveDirectoryOAuth 認証の要求本文

`ActiveDirectoryOAuth` モデルを使用して認証を追加する場合は、要求本文に次の要素を指定します。

|要素 |説明 |
|:--|:--|
|_authentication (親要素)_ |ActiveDirectoryOAuth 認証を使用するための認証オブジェクト。|
|_type_ |必須。認証の種類。ActiveDirectoryOAuth 認証の場合、値 `ActiveDirectoryOAuth` を使用する必要があります。|
|_tenant_ |必須。Azure AD テナントのテナント ID です。|
|_audience_ |必須。https://management.core.windows.net/.| に設定します。
|_clientId_ |必須。Azure AD アプリケーションのクライアント識別子を指定します。|
|_secret_ |必須。トークンを要求しているクライアントのシークレット。|

### テナント ID の確認

Azure AD テナントのテナント ID は、Azure PowerShell で `Get-AzureAccount` を実行すると確認できます。

## ActiveDirectoryOAuth 認証の応答本文

認証情報を含めて要求を送信した場合、応答には次の認証に関連する要素が含まれます。

|要素 |説明 |
|:--|:--|
|_authentication (親要素)_ |ActiveDirectoryOAuth 認証を使用するための認証オブジェクト。|
|_type_ |認証の種類。ActiveDirectoryOAuth 認証の場合、値 `ActiveDirectoryOAuth` を使用します。|
|_tenant_ |Azure AD テナントのテナント ID です。 |
|_audience_ |https://management.core.windows.net/.| に設定します。
|_clientId_ |Azure AD アプリケーションのクライアント識別子。|

## 関連項目


 [What is Scheduler? (Scheduler とは)](scheduler-intro.md)

 [Azure Scheduler の概念、用語集、エンティティ階層構造](scheduler-concepts-terms.md)

 [Azure ポータル内で Scheduler を使用した作業開始](scheduler-get-started-portal.md)

 [Azure Scheduler のプランと課金](scheduler-plans-billing.md)

 [Azure Scheduler REST API リファレンス](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler PowerShell コマンドレット リファレンス](scheduler-powershell-reference.md)

 [Azure Scheduler の高可用性と信頼性](scheduler-high-availability-reliability.md)

 [Azure Scheduler の制限、既定値、エラー コード](scheduler-limits-defaults-errors.md)

<!---HONumber=AcomDC_0323_2016-->