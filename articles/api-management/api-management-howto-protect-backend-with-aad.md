---
title: Azure Active Directory と API Management で OAuth 2.0 を使用して API を保護する | Microsoft Docs
description: Azure Active Directory と API Management で Web API バックエンドを保護する方法について説明します。
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2018
ms.author: apimpm
ms.openlocfilehash: 2c05407d761a8848f9e032aa219960cd7ea6fa93
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-protect-an-api-using-oauth-20-with-azure-active-directory-and-api-management"></a>Azure Active Directory と API Management で OAuth 2.0 を使用して API を保護する方法

このガイドでは、API Management (APIM) インスタンスを構成して、Azure Active Directory (AAD) で OAuth 2.0 プロトコルを使用して API を保護する方法について説明します。 

## <a name="prerequisite"></a>前提条件
この記事の手順を実行するには、以下が必要です。
* APIM インスタンス
* APIM インスタンスを使用して公開されている API
* Azure AD テナント

## <a name="overview"></a>概要

このガイドでは、APIM で OAuth 2.0 を使用して API を保護する方法について説明します。 この記事では、Azure AD が Authorization Server (OAuth Server) として使用されています。 手順の概要は次のとおりです。

1. API を表すアプリケーション (バックエンドアプリ) を Azure AD に登録する
2. API を呼び出す必要があるクライアント アプリケーションを表す別のアプリケーション (クライアントアプリ) を Azure AD に登録する
3. Azure AD で、クライアントアプリがバックエンドアプリを呼び出すことができるようにアクセス許可を付与する
4. OAuth 2.0 のユーザー承認を使用するように Developer Console を設定する
5. validate-jwt ポリシーを追加して、すべての着信要求に対して OAuth トークンを検証する

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>API を表すアプリケーションを Azure AD に登録する

Azure AD で API を保護するには、まず API を表すアプリケーションを Azure AD に登録します。 

Azure AD テナントに移動し、**[アプリの登録]** に移動します。

**[新しいアプリケーションの登録]** を選択します。 

アプリケーションの名前を入力します。 この例では、`backend-app` が使用されています。  

**[アプリケーションの種類]** として **[Web アプリ/API]** を選択します。 

**[サインオン URL]** にはプレースホルダーとして `https://localhost` を使用できます。

**[作成]** をクリックします。

アプリケーションが作成されたら、次の手順で使用する **[アプリケーション ID]** をメモします。 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>クライアント アプリケーションを表す別のアプリケーションを Azure AD に登録する

API を呼び出す必要のあるすべてのクライアント アプリケーションも、Azure AD にアプリケーションとして登録する必要があります。 このガイドでは、サンプル クライアント アプリケーションとして、APIM Developer Portal の Developer Console を使用します。 

Developer Console を表す別のアプリケーションを Azure AD に登録する必要があります。

**[新しいアプリケーションの登録]** をもう一度クリックします。 

アプリケーションの名前を入力し、**[アプリケーションの種類]** として **[Web アプリ/API]** を選択します。 この例では、`client-app` が使用されています。  

**[サインオン URL]** にはプレースホルダーとして `https://localhost` を使用するか、実際の APIM インスタンスのサインイン URL を使用できます。 この例では、`https://contoso5.portal.azure-api.net/signin` が使用されています。

**[作成]** をクリックします。

アプリケーションが作成されたら、次の手順で使用する **[アプリケーション ID]** をメモします。 

次に、以降の手順で使用するこのアプリケーションのクライアント シークレットを作成する必要があります。

**[設定]** をもう一度クリックし、**[キー]** に移動します。

**[パスワード]** の **[キーの説明]** を入力し、キーの有効期限を選択して **[保存]** をクリックします。

キーの値をメモします。 

## <a name="grant-permissions-in-aad"></a>AAD でアクセス許可を付与する

API (つまりバックエンドアプリ) と Developer Console (つまりクライアント アプリ) を表す 2 つのアプリケーションを登録したら、次はクライアントアプリがバックエンドアプリを呼び出すことができるようにアクセス許可を付与する必要があります。  

もう一度 **[アプリの登録]** に移動します。 

`client-app` をクリックし、**[設定]** に移動します。

**[必要なアクセス許可]**、**[追加]** の順にクリックします。

**[API を選択します]** をクリックし、`backend-app` を検索します。

**[委任されたアクセス許可]** の `Access backend-app` をオンにします。 

**[選択]**、**[完了]** の順にクリックします。 

> [!NOTE]
> **Windows** **Azure Active Directory** が他のアプリケーションに対するアクセス許可の下に表示されない場合、**[追加]** をクリックし、その一覧から追加します。
> 
> 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>Developer Console で OAuth 2.0 のユーザー承認を有効にする

これまでの手順では、Azure AD でアプリケーションを作成し、クライアントアプリがバックエンドアプリを呼び出すことができる適切なアクセス許可を付与しました。 

このガイドでは、Developer Console をクライアントアプリとして使用します。 ここでは、Developer Console で OAuth 2.0 のユーザー承認を有効にする方法について説明します。 

APIM インスタンスに移動します。

**[OAuth 2.0]**、**[追加]** の順にクリックします。

**[表示名]** と **[説明]** を入力します。

[クライアント登録ページの URL] に、**「`http://localhost`」などのプレースホルダーの値を入力します。  **[クライアント登録ページ URL]** では、ユーザーによるアカウント管理をサポートする OAuth 2.0 プロバイダーについて、ユーザーが自身のアカウントを作成および構成するために使用できるページを指定します。 この例では、ユーザーは自身のアカウントを作成も構成もしないため、プレースホルダーを使用します。

**[承認許可の種類]** として **[承認コード]** をオンにします。

次に、**[Authorization endpoint URL (認証エンドポイント URL)]** と **[Token endpoint URL (トークン エンドポイント URL)]** を指定します。

これらの値は、Azure AD テナントの **[エンドポイント]** ページから取得できます。 エンドポイントにアクセスするには、**[アプリ登録]** ページにもう一度移動し、**[エンドポイント]** をクリックします。

**[OAuth 2.0 承認エンドポイント]** をコピーし、**[承認エンドポイントの URL]** テキストボックスに貼り付けます。

**[OAuth 2.0 トークン エンドポイント]** をコピーし、**[トークン エンドポイントの URL]** テキストボックスに貼り付けます。

トークン エンドポイントに貼り付けるだけでなく、「**resource**」という名前の body パラメーターを追加し、値としてバックエンドアプリの**アプリケーション ID** を使用します。

次に、クライアントの資格情報を指定します。 これらはクライアントアプリの資格情報です。

**[クライアント ID]** には、クライアントアプリの**アプリケーション ID** を使用します。

**[クライアント シークレット]** には、先ほどクライアントアプリ用に作成したキーを使用します。 

クライアント シークレットの直後には、承認コード付与タイプの **redirect_url** があります。

この URL をメモします。

**[作成]** をクリックします。

クライアントアプリの **[設定]** ページに戻ります。

**[応答 URL]** をクリックし、最初の行に **redirect_url** を貼り付けます。 この例では、最初の行の `https://localhost` を URL に置き換えました。  

OAuth 2.0 承認サーバーを設定したので、Developer Console で Azure AD からアクセス トークンを取得できるようになりました。 

次の手順では、API の呼び出しを行う前に、Developer Console でユーザーの代理でアクセス トークンを取得する必要があることがわかるように、API の OAuth 2.0 ユーザー承認を有効にします。

APIM インスタンスに移動し、**[API]** に移動します。

保護する API をクリックします。 この例では、`Echo API` を使用します。

**[設定]**に移動します。

**[セキュリティ]** で **[OAuth 2.0]** を選択し、先ほど構成した OAuth 2.0 サーバーを選択します。 

**[Save]**をクリックします。

## <a name="successfully-call-the-api-from-the-developer-portal"></a>開発者ポータルから適切に API を呼び出す

`Echo API` で OAuth 2.0 ユーザー承認が有効にすると、Developer Console は API を呼び出す前にユーザーの代理でアクセス トークンを取得するようになります。

開発者ポータルの `Echo API` で任意の操作に移動し、**[テスト]** をクリックします。これで Developer Console が表示されます。

**[承認]** セクションの新しい項目は、先ほど追加した承認サーバーに対応しています。

承認ドロップダウン リストから **[承認コード]** を選択します。Azure AD テナントにサインインするように求められます。 既にアカウントでサインインしている場合、確認メッセージは表示されません。

サインインに成功すると、Azure AD のアクセス トークンを使用する要求に `Authorization` ヘッダーが追加されます。 

サンプル トークンは以下のようになります。これは Base64 でエンコードされています。

```
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
```

**[送信]** をクリックすると、API を呼び出すことができます。


## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>要求を事前承認する JWT 検証ポリシーを構成する

この時点で、ユーザーが Developer Console から呼び出そうとするとログインを求められ、Developer Console はユーザーの代理でアクセス トークンを取得します。 すべてが期待どおりに動作しています。 ただし、誰かがトークンを使用せず、または無効なトークンを使用して API を呼び出すとどうなるでしょうか。 たとえば、`Authorization` ヘッダーを削除しようとすると、API を呼び出すことができることがわかります。 理由は、APIM がこの時点でアクセス トークンを検証していないためです。 `Auhtorization` ヘッダーはバックエンド API に渡されます。

[JWT を検証する](api-management-access-restriction-policies.md#ValidateJWT)ポリシーを使用して、各受信要求のアクセス トークンを検証することで APIM で要求を事前承認できます。 要求に有効なトークンがない場合は API Management によってブロックされ、バックエンドに渡されません。 たとえば、以下のポリシーを `Echo API` の `<inbound>` ポリシー セクションに追加できます。 これは、アクセス トークンの受信者要求をチェックし、トークンが有効でない場合にエラー メッセージを返します。 ポリシーの構成方法については、[ポリシーの設定と編集](set-edit-policies.md)に関する記事をご覧ください。

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

## <a name="build-an-application-to-call-the-api"></a>アプリケーションをビルドして API を呼び出す

このガイドでは、サンプル クライアント アプリケーションとして APIM で Developer Console を使用して、OAuth 2.0 によって保護された `Echo API` を呼び出します。 アプリケーションのビルドと OAuth 2.0 フローの実装について詳しくは、「[Azure Active Directory のコード例](../active-directory/develop/active-directory-code-samples.md)」をご覧ください。

## <a name="next-steps"></a>次の手順
* [Azure Active Directory と OAuth2.0](../active-directory/develop/active-directory-authentication-scenarios.md) の詳細を確認します。
* API Management についてのその他の [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=api-management) をご覧ください。
* バックエンド サービスを保護するその他の方法については、「[相互証明書認証](api-management-howto-mutual-certificates.md)」をご覧ください。

[Create an API Management service instance]: get-started-create-service-instance.md
[Manage your first API]: import-and-publish.md
