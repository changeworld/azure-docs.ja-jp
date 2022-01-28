---
title: 'クイックスタート: Java デーモンからの Microsoft Graph の呼び出し | Azure'
titleSuffix: Microsoft identity platform
description: このクイックスタートでは、Java アプリでアクセス トークンを取得し、Microsoft ID プラットフォーム エンドポイントによって保護されている API を、アプリ自体の ID を使用して呼び出す方法について説明します
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 01/10/2022
ROBOTS: NOINDEX
ms.author: marsma
ms.custom: aaddev, "scenarios:getting-started", "languages:Java", devx-track-java, mode-api
ms.openlocfilehash: 386d1ba5e887b584a73d03cd2fd3434d259b23a3
ms.sourcegitcommit: 3f20f370425cb7d51a35d0bba4733876170a7795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/27/2022
ms.locfileid: "137803287"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-java-console-app-using-apps-identity"></a>クイックスタート: トークンを取得し、Java コンソール アプリからアプリの ID を使用して Microsoft Graph API を呼び出す

このクイックスタートでは、Java アプリケーションでアプリの ID を使ってアクセス トークンを取得して、Microsoft Graph API を呼び出し、ディレクトリ内の[ユーザーの一覧](/graph/api/user-list)を表示する方法を示すコード サンプルをダウンロードして実行します。 このコード サンプルでは、ユーザーの ID ではなく、アプリケーション ID を使用して、無人のジョブまたは Windows サービスを実行する方法を示します。

## <a name="prerequisites"></a>前提条件

このサンプルを実行するには、以下が必要です。

- [Java Development Kit (JDK)](https://openjdk.java.net/) 8 以降
- [Maven](https://maven.apache.org/)

> [!div class="sxs-lookup"]
### <a name="download-and-configure-the-quickstart-app"></a>クイックスタート アプリをダウンロードして構成する

#### <a name="step-1-configure-the-application-in-azure-portal"></a>手順 1: Azure portal でのアプリケーションの構成
このクイック スタート用サンプル コードを動作させるには、クライアント シークレットを作成し、Graph API の **User.Read.All** アプリケーションのアクセス許可を追加します。
> [!div class="nextstepaction"]
> [これらの変更を行います]()

> [!div class="alert alert-info"]
> ![構成済み](media/quickstart-v2-netcore-daemon/green-check.png) アプリケーションはこれらの属性で構成されています。

#### <a name="step-2-download-the-java-project"></a>手順 2:Java プロジェクトのダウンロード

> [!div class="sxs-lookup nextstepaction"]
> [コード サンプルをダウンロードします](https://github.com/Azure-Samples/ms-identity-java-daemon/archive/master.zip)

> [!div class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

#### <a name="step-3-admin-consent"></a>手順 3:管理者の同意

この時点でアプリケーションを実行すると、*HTTP 403 - Forbidden* エラー "`Insufficient privileges to complete the operation`" が表示されます。 これは、すべての "*アプリ専用のアクセス許可*" には管理者の同意が必要であるために発生します。ディレクトリのグローバル管理者にお使いのアプリケーションに同意してもらう必要があります。 ご自身のロールに応じて、次のオプションのいずれかを選択します。

##### <a name="global-tenant-administrator"></a>グローバル テナント管理者

グローバル管理者の場合は、 **[API のアクセス許可]** ページに移動し、 **[Enter_the_Tenant_Name_Here に管理者の同意を与えます]** を選択します。
> [!div id="apipermissionspage"]
> [[API のアクセス許可] ページに移動する]()

##### <a name="standard-user"></a>標準ユーザー

テナントの標準ユーザーの場合は、お使いのアプリケーションに管理者の同意を与えるようグローバル管理者に依頼する必要があります。 これを行うには、次の URL を管理者に知らせます。

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```
#### <a name="step-4-run-the-application"></a>手順 4:アプリケーションの実行

このサンプルは、IDE から ClientCredentialGrant.java の main メソッドを実行することで直接テストできます。

シェルまたはコマンド ラインから:

```
$ mvn clean compile assembly:single
```

/targets ディレクトリに msal-client-credential-secret-1.0.0.jar ファイルが生成されます。 次のように Java 実行可能ファイルを使用してこれを実行します。

```
$ java -jar msal-client-credential-secret-1.0.0.jar
```

実行後、構成済みのテナントに存在するユーザーが一覧表示されます。

> [!IMPORTANT]
> このクイック スタート アプリケーションは、クライアント シークレットを使用して、それ自体を機密クライアントとして識別します。 クライアント シークレットはプロジェクト ファイルにプレーン テキストとして追加されるため、セキュリティ上の理由から、アプリケーションを運用アプリケーションと見なす前に、クライアント シークレットの代わりに証明書を使用することをお勧めします。 証明書の使用方法の詳細については、このサンプルと同じ GitHub リポジトリの 2 つ目のフォルダー **msal-client-credential-certificate** にある [これらの手順](https://github.com/Azure-Samples/ms-identity-java-daemon/tree/master/msal-client-credential-certificate)を参照してください。

## <a name="more-information"></a>説明

### <a name="msal-java"></a>MSAL Java

[MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java) はユーザーのサインインを処理し、Microsoft ID プラットフォームによって保護されている API にアクセスするトークンを要求するために使用するライブラリです。 説明したとおり、このクイック スタートでは、委任されたアクセス許可ではなく、アプリケーション自体の ID を使用してトークンを要求しています。 ここで使用される認証フローは、" *[クライアント資格情報 OAuth フロー](v2-oauth2-client-creds-grant-flow.md)* " と呼ばれます。 デーモン アプリでの MSAL Java の使用方法の詳細については、[この記事](scenario-daemon-overview.md)を参照してください。

Maven または Gradle を使用して、アプリケーションに MSAL4J を追加し、アプリケーションの pom.xml (Maven) または build.gradle (Gradle) ファイルに対して以下の変更を行うことで、依存関係を管理します。

pom.xml 内:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

build.gradle 内:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="msal-initialization"></a>MSAL の初期化

MSAL for Java を使用するファイルの先頭に次のコードを追加して、MSAL4J への参照を追加します。

```Java
import com.microsoft.aad.msal4j.*;
```

続いて、次のコードを使用して MSAL を初期化します。

```Java
IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);

ConfidentialClientApplication cca =
        ConfidentialClientApplication
                .builder(CLIENT_ID, credential)
                .authority(AUTHORITY)
                .build();
```

> | 各値の説明: |説明 |
> |---------|---------|
> | `CLIENT_SECRET` | Azure portal 上でアプリケーションに対して作成されるクライアント シークレット。 |
> | `CLIENT_ID` | Azure portal に登録されているアプリケーションの "**アプリケーション (クライアント) ID**"。 この値は、Azure portal のアプリの **[概要]** ページで確認できます。 |
> | `AUTHORITY`    | ユーザーが認証するための STS エンドポイント。 通常、パブリック クラウド上では `https://login.microsoftonline.com/{tenant}` です。{tenant} はご自分のテナントの名前またはテナント ID です。|

### <a name="requesting-tokens"></a>トークンの要求

アプリの ID を使用してトークンを要求するには、`acquireToken` メソッドを使用します。

```Java
IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
```

> |各値の説明:| 説明 |
> |---------|---------|
> | `SCOPE` | 要求されるスコープが含まれています。 機密クライアントの場合は、`{Application ID URI}/.default` のような形式を使用して、要求されるスコープが Azure portal で設定されるアプリ オブジェクト内に静的に定義されたものであることを示す必要があります (Microsoft Graph では、`{Application ID URI}` は `https://graph.microsoft.com` を指します)。 カスタム Web API の場合、`{Application ID URI}` は、Azure portal で **[アプリの登録]** の **[API の公開]** セクションに定義されます。|

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>次のステップ

デーモン アプリケーションの詳細については、シナリオのランディング ページを参照してください。

> [!div class="nextstepaction"]
> [Web API を呼び出すデーモン アプリケーション](scenario-daemon-overview.md)
