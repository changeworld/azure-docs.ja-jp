---
title: OAuth 2.0 と Azure Active Directory を使用して API Management で API バックエンドを保護する
titleSuffix: Azure API Management
description: OAuth 2.0 ユーザー認可と Azure Active Directory を使用して、Azure API Management と開発者ポータルで Web API バックエンドへのユーザー アクセスをセキュリティで保護する方法について説明します。
services: api-management
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 09/17/2021
ms.author: danlep
ms.custom: contperf-fy21q1
ms.openlocfilehash: 7739411364b187e437e9dda4c72dff85de26d741
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669570"
---
# <a name="protect-a-web-api-backend-in-azure-api-management-using-oauth-20-authorization-with-azure-active-directory"></a>OAuth 2.0 認可と Azure Active Directory を使用して Azure API Management で Web API バックエンドを保護する 

この記事では、[OAuth 2.0 プロトコルと Azure Active Directory (Azure AD)](../active-directory/develop/active-directory-v2-protocols.md) を使用して、API を保護するように [Azure API Management](api-management-key-concepts.md) インスタンスを構成する方法について説明します。 

> [!NOTE]
> この機能は、API Management の **Developer**、**Basic**、**Standard**、**Premium** の各レベルで使用できます。  
> 
> **Consumption** レベルでは、開発者ポータルからの API の呼び出しを除き、以下のすべての手順に従うことができます。

## <a name="prerequisites"></a>前提条件

この記事の手順に従う前に、次のものが必要です。

- API Management インスタンス
- API Management インスタンスを使用する公開された API
- Azure AD テナント

## <a name="overview"></a>概要

:::image type="content" source="media/api-management-howto-protect-backend-with-aad/overview-graphic-2021.png" alt-text="次のフローを視覚的に概念化するための概要図。":::

1. API を表すアプリケーション (バックエンドアプリ) を Azure AD に登録します。

1. API を呼び出す必要があるクライアント アプリケーションを表す別のアプリケーション (クライアントアプリ) を Azure AD に登録します。

1. Azure AD で、クライアントアプリがバックエンドアプリを呼び出すことができるようにアクセス許可を付与します。

1. 開発者ポータルの Developer Console を、OAuth 2.0 ユーザー認可を使用して API を呼び出すように構成します。

1. **validate-jwt** ポリシーを追加して、すべての着信要求に対して OAuth トークンを検証します。

## <a name="1-register-an-application-in-azure-ad-to-represent-the-api"></a>1. API を表すアプリケーションを Azure AD に登録する

Azure portal を使用して、API を表すアプリケーションを Azure AD に登録することにより、Azure AD で API を保護します。 

アプリの登録の詳細については、次を参照してください。「[クイックスタート:Web API を公開するようにアプリケーションを構成する](../active-directory/develop/quickstart-configure-app-expose-web-apis.md)」。

1. [Azure portal](https://portal.azure.com) で、 **[アプリの登録]** を検索して選択します。

1. **[新規登録]** を選択します。 

1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。

   - **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します (例: *backend-app*)。 
   - **[サポートされているアカウントの種類]** セクションで、シナリオに適したオプションを選択します。 

1. [ **[リダイレクト URI]**](../active-directory/develop/reply-url.md) セクションは空のままにします。

1. **[登録]** を選択して、アプリケーションを作成します。 

1. アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を見つけ、後で使用するために記録します。

1. サイド メニューの **[管理]** セクションで、 **[API の公開]** を選択し、 **[アプリケーション ID URI]** を既定値に設定します。 この値は、後で使用するために記録しておきます。

1. **[スコープの追加]** ボタンを選択して、 **[スコープの追加]** ページを表示します。
    1. 新しい **[スコープ名]** 、 **[管理者の同意の表示名]** 、 **[管理者の同意の説明]** を入力します。
    1. **[有効]** のスコープ状態が選択されていることを確認します。

1. **[Scope の追加]** ボタンを選択してスコープを作成します。 

1. 手順 8. と 9. を繰り返して、API でサポートされているすべてのスコープを追加します。

1. スコープが作成されたら、以降の手順で使用するためにそれらを書き留めておきます。 

## <a name="2-register-another-application-in-azure-ad-to-represent-a-client-application"></a>2. クライアント アプリケーションを表す別のアプリケーションを Azure AD に登録する

API を呼び出すすべてのクライアント アプリケーションを Azure AD にアプリケーションとして登録します。 この例のクライアント アプリケーションは、API Management 開発者ポータルの **Developer Console** です。 

Developer Console を表すように別のアプリケーションを Azure AD に登録するには、次を実行します。

1. [Azure portal](https://portal.azure.com) で、 **[アプリの登録]** を検索して選択します。

1. **[新規登録]** を選択します。

1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。

   - **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します (例: *client-app*)。 
   - **[サポートされているアカウントの種類]** で、 **[任意の組織ディレクトリ内のアカウント (任意の Azure AD ディレクトリ - マルチテナント)]** を選択します。 

1. **[リダイレクト URI]** セクションで、[`Web`] を選択し、[URL] フィールドは今は空のままにします。

1. **[登録]** を選択して、アプリケーションを作成します。 

1. アプリの **[概要]** ページで、 **[アプリケーション (クライアント) ID]** の値を見つけ、後で使用するために記録します。

1. 以降の手順で使用するこのアプリケーションのクライアント シークレットを作成します。

   1. サイド メニューの **[管理]** セクションで、 **[証明書とシークレット]** を選択します。
   1. **[クライアント シークレット]** で、 **[新しいクライアント シークレット]** を選択します。
   1. **[クライアント シークレットの追加]** で、 **[説明]** を指定し、キーの有効期限がいつ切れるようにするかを選択します。
   1. **[追加]** を選択します。

シークレットが作成されたら、後の手順で使用するために、そのキー値を書き留めておきます。 

## <a name="3-grant-permissions-in-azure-ad"></a>3. Azure AD でアクセス許可付与する

API と Developer Console を表す 2 つのアプリケーションを登録したら、クライアントアプリがバックエンドアプリを呼び出すことができるようにアクセス許可を付与します。  

1. [Azure portal](https://portal.azure.com) で、 **[アプリの登録]** を検索して選択します。

1. 対象のクライアント アプリを選択します。 アプリのページの一覧から **[API のアクセス許可]** を選択します。

1. **[アクセス許可の追加]** を選択します。

1. **[API を選択します]** の下で、 **[自分の API]** を選択し、バックエンド アプリを検索して選択します。

1. **[委任されたアクセス許可]** を選択してから、バックエンド アプリへの適切なアクセス許可を選択します。

1. **[アクセス許可の追加]** を選択します.

省略可能:
1. クライアント アプリの **[API のアクセス許可]** ページに移動します。

1. **[\<your-tenant-name> に管理者の同意を与えます]** を選択して、このディレクトリ内のすべてのユーザーに代わって同意を付与します。 

## <a name="4-enable-oauth-20-user-authorization-in-the-developer-console"></a>4. Developer Console で OAuth 2.0 ユーザー認可を有効にする

これまでの手順では、Azure AD でアプリケーションを作成し、クライアントアプリがバックエンドアプリを呼び出すことができる適切なアクセス許可を付与しました。 

この例では、Developer Console (クライアント アプリ) で OAuth 2.0 ユーザー認可を有効にします。

1. Azure portal で、 **[承認エンドポイントの URL]** と **[トークン エンドポイントの URL]** を見つけ、後のために保存してきます。 
    1. **[アプリの登録]** ページを開きます。 
    1. **[エンドポイント]** を選択します。
    1. **[OAuth 2.0 認可エンドポイント]** と **[OAuth 2.0 トークン エンドポイント]** をコピーします。 

1. API Management インスタンスに移動します。

1. サイド メニューの **[開発者ポータル]** セクションで、 **[OAuth 2.0 + OpenID Connect]** を選択します。 

1. **[OAuth 2.0]** タブで、 **[追加]** を選択します。

1. **[表示名]** と **[説明]** を入力します。

1. **[クライアント登録ページの URL]** に、「`http://localhost`」などのプレースホルダーの値を入力します。 
    * **[クライアント登録ページの URL]** は、ユーザーが OAuth 2.0 プロバイダーでサポートされている独自のアカウントを作成して構成するページを指します。 
    * この例では、ユーザーは独自のアカウントの作成や構成を行わないため、プレースホルダーを使用します。

1. **[承認許可の種類]** として **[承認コード]** を選択します。

1. 前に保存した **[認可エンドポイントの URL]** と **[トークン エンドポイントの URL]** を指定します。 
    1. **[OAuth 2.0 認可エンドポイント]** をコピーして **[認可エンドポイントの URL]** テキスト ボックスに貼り付けます。 
    1. [承認要求方法] の下で **[POST]** を選択します。
    1. **[OAuth 2.0 トークン エンドポイント]** を入力し、それを **[トークン エンドポイントの URL]** テキスト ボックスに貼り付けます。 
        * **v1** エンドポイントを使用する場合:
          * **resource** という名前の本文パラメーターを追加します。
          * その値には、バックエンド アプリの **アプリケーション ID** を入力します。
        * **v2** エンドポイントを使用する場合:
          * **[既定のスコープ]** フィールドで作成したバックエンド アプリ スコープを使用します。
          * [アプリケーション マニフェスト](../active-directory/develop/reference-app-manifest.md)で、[`accessTokenAcceptedVersion`](../active-directory/develop/reference-app-manifest.md#accesstokenacceptedversion-attribute) プロパティの値を `2` に設定します。
          

   >[!IMPORTANT]
   > **v1** または **v2** エンドポイントのどちらでも使用できますが、v2 エンドポイントを使用することをお勧めします。 

1. クライアント アプリの資格情報を指定します。
    * **[クライアント ID]** には、クライアントアプリの **アプリケーション ID** を使用します。
    * **[クライアント シークレット]** には、先ほどクライアントアプリ用に作成したキーを使用します。 

1. 認可コードの付与タイプの **[リダイレクト URI]** を書き留めておきます。

1. **[作成]** を選択します。

1. クライアント アプリの登録に戻ります。 
 
1. **[管理]** で、 **[認証]** を選択します。

1. **[プラットフォーム構成]** で、次の操作を行います。
    * **[プラットフォームの追加]** をクリックします。
    * 種類を **[Web]** として選択します。 
    * 前に保存したリダイレクト URI を **[リダイレクト URI]** の下に貼り付けます。
    * **[構成]** ボタンをクリックして保存します。

   これで Developer Console が OAuth 2.0 認可サーバー経由で Azure AD からアクセス トークンを取得できるようになったので、API の OAuth 2.0 ユーザー認可を有効にします。 こうすることで、Developer Console は API を呼び出す前に、ユーザーの代わりにアクセス トークンを取得する必要があることを把握できるようになります。

15. API Management インスタンスに移動し、 **[API]** に移動します。

1. 保護する API を選択します。 たとえば、「 `Echo API` 」のように入力します。

1. **[設定]** に移動します。

1. **[セキュリティ]** で、次の操作を行います。
    1. **[OAuth 2.0]** を選択します。
    1. 前に構成した OAuth 2.0 サーバーを選択します。 

1. **[保存]** を選択します。

## <a name="5-successfully-call-the-api-from-the-developer-portal"></a>5. 開発者ポータルから API を正常に呼び出す

> [!NOTE]
> **Consumption** レベルの場合は、開発者ポータルがサポートされていないため、このセクションは適用されません。

ご使用の API で OAuth 2.0 ユーザー承認が有効になると、Developer Console は API を呼び出す前にユーザーの代理でアクセス トークンを取得するようになります。

1. 開発者ポータルで API の下にある任意の操作を参照します。 
1. **[試してみる]** を選択すると、Developer Console が表示されます。

1. **[承認]** セクションの新しい項目は、先ほど追加した承認サーバーに対応しています。

1. 認可ドロップダウン リストから **[認可コード]** を選択します。 
1. 求められたら、Azure AD テナントにサインインします。 
    * 既にアカウントにサインインしている場合は、求められない可能性があります。

1. サインインに成功すると、Azure AD のアクセス トークンを使用する要求に `Authorization` ヘッダーが追加されます。 以下はサンプル トークン (Base64 エンコード) です。

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

1. **[送信]** を選択して、API を適切に呼び出します。

## <a name="6-configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>6. 要求を事前認可する JWT 検証ポリシーを構成する

これまでに、次のことを行いました。
* Developer Console からの呼び出しを試しました。
* 求められ、Azure AD テナントにサインインしました。 
* Developer Console がユーザーに代わってアクセス トークンを取得し、API に対して行われる要求にトークンを含めます。

ただし、誰かがトークンを使用せず、または無効なトークンを使用して API を呼び出すとどうなるでしょうか。 たとえば、`Authorization` ヘッダーなしで API を呼び出した場合でも、API Management はアクセス トークンを検証しないため、その呼び出しはそのまま実行されます。 `Authorization` ヘッダーはバックエンド API に渡されます。

各受信要求のアクセス トークンを検証することによって、[JWT 検証](./api-management-access-restriction-policies.md#ValidateJWT)ポリシーにより API Management で要求を事前認可します。 要求に有効なトークンがない場合、API Management はその要求をブロックします。 

次のポリシーの例は、`<inbound>` ポリシー セクションに追加された場合、Azure AD から取得されたアクセス トークン内の受信者要求の値をチェックし、そのトークンが有効でない場合はエラー メッセージを返します。 


```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/v2.0/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>insert aud claim value expected in the token</value>
        </claim>
    </required-claims>
</validate-jwt>
```

> [!NOTE]
> 上記の `openid-config` URL は、v2 エンドポイントに対応しています。 v1 `openid-config` エンドポイントの場合は、`https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration` を使用します。

> [!TIP] 
> Azure AD テナント ID としての **{aad-tenant}** 値は、Azure portal の次のいずれかで見つけます。
> * Azure AD リソースの概要ページ、または
> * Azure AD リソースの **[管理] > [プロパティ]** ページ。

ポリシーの構成方法については、[ポリシーの設定と編集](./set-edit-policies.md)に関する記事をご覧ください。

## <a name="build-an-application-to-call-the-api"></a>アプリケーションをビルドして API を呼び出す

このガイドでは、サンプル クライアント アプリケーションとして API Management で Developer Console を使用して、OAuth 2.0 によって保護された `Echo API` を呼び出します。 アプリケーションをビルドし、OAuth 2.0 を実装する方法の詳細については、[Azure AD のコード サンプル](../active-directory/develop/sample-v2-code.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure AD と OAuth2.0](../active-directory/develop/authentication-vs-authorization.md) の詳細について学習します。
- API Management についてのその他の [ビデオ](https://azure.microsoft.com/documentation/videos/index/?services=api-management) をご覧ください。
- バックエンド サービスを保護するその他の方法については、「[相互証明書認証](./api-management-howto-mutual-certificates.md)」を参照してください。
- [API Management サービス インスタンスの作成](./get-started-create-service-instance.md)。
- [Azure API Management での最初の API の管理](./import-and-publish.md)。
