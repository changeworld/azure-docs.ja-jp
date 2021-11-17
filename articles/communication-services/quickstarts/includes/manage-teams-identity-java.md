---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: gistefan
manager: soricos
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 10/08/2021
ms.topic: include
ms.custom: include file
ms.author: gistefan
ms.openlocfilehash: 2efbb9772e70afd699d467668fd1eadd69502b04
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461212"
---
## <a name="set-up-prerequisites"></a>前提条件の設定

- [Java Development Kit (JDK)](/azure/developer/java/fundamentals/java-jdk-install) バージョン 8 以降。
- [Apache Maven](https://maven.apache.org/download.cgi)。

## <a name="set-up"></a>設定

### <a name="create-a-new-java-application"></a>新しい Java アプリケーションを作成する

ターミナルまたはコマンド ウィンドウを開きます。 Java アプリケーションを作成するディレクトリに移動します。 次のコマンドを実行して、`maven-archetype-quickstart` テンプレートから Java プロジェクトを生成します。

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

"generate" タスクによって、`artifactId` と同じ名前のディレクトリが作成されたことがわかります。 このディレクトリの下の `src/main/java` ディレクトリにはプロジェクトのソース コードが含まれており、`src/test/java directory` にはテスト ソースが含まれており、`pom.xml` ファイルはプロジェクトのプロジェクト オブジェクト モデル (POM) です。

### <a name="install-the-package"></a>パッケージをインストールする

テキスト エディターで `pom.xml` ファイルを開きます。 依存関係のグループに、次の dependency 要素を追加します。

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-communication-identity</artifactId>
        <version>1.2.0-beta.1</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>msal4j</artifactId>
      <version>1.11.0</version>
    </dependency>
</dependencies>
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクト ディレクトリで次の操作を行います。

1. `/src/main/java/com/communication/quickstart` ディレクトリに移動します
1. エディターで `App.java` ファイルを開きます
1. `System.out.println("Hello world!");` ステートメントを置き換えます
1. `import` ディレクティブを追加します

次のコードを使用して開始します。

```java
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.core.credential.*;
import com.microsoft.aad.msal4j.IAuthenticationResult;
import com.microsoft.aad.msal4j.InteractiveRequestParameters;
import com.microsoft.aad.msal4j.PublicClientApplication;

import java.io.IOException;
import java.time.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws Exception
    {
        System.out.println("Azure Communication Services - Communication access token Quickstart");
        // Quickstart code goes here
    }
}
```

### <a name="step-1-receive-the-azure-active-directory-user-token-via-the-msal-library"></a>手順 1: MSAL ライブラリを使用して Azure Active Directory ユーザー トークンを受け取る

トークン交換フローの最初の手順は、[Microsoft.Identity.Client](../../../active-directory/develop/reference-v2-libraries.md) を使用して、Teams ユーザーのトークンを取得することです。

```java
String appId = "<contoso_application_id>";
String authority = "https://login.microsoftonline.com/<contoso_tenant_id>";

PublicClientApplication pca = PublicClientApplication.builder(appId)
        .authority(authority)
        .build();

String redirectUri = "http://localhost";
Set<String> scope = new HashSet<String>();
scope.add("https://auth.msft.communication.azure.com/VoIP");

InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI(redirectUri))
                    .scopes(scope)
                    .build();

IAuthenticationResult result = pca.acquireToken(parameters).get();
```

### <a name="step-2-initialize-the-communicationidentityclient"></a>手順 2: CommunicationIdentityClient を初期化する

リソースのアクセス キーとエンドポイントを使用して、`CommunicationIdentityClient` をインスタンス化します。 [リソースの接続文字列を管理する](../create-communication-resource.md#store-your-connection-string)方法について確認してください。 加えて、クライアントは、`com.azure.core.http.HttpClient` インターフェイスを実装する任意のカスタム HTTP クライアントを使用して初期化できます。

`main` メソッドに次のコードを追加します。

```java
//You can find your connection string from your resource in the Azure portal
String connectionString = "<connection_string>";

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .connectionString(connectionString)
    .buildClient();
```

### <a name="step-3-exchange-the-azure-ad-user-token-for-the-teams-access-token"></a>手順 3: Azure AD ユーザー トークンを Teams アクセス トークンと交換する

`getTokenForTeamsUser` メソッドを使用して、Azure Communication Services SDK で使用できる Teams ユーザーのアクセス トークンを発行します。

```java
var accessToken = communicationIdentityClient.getTokenForTeamsUser(result.accessToken());
System.out.println("Token: " + accessToken.getToken());
```

## <a name="run-the-code"></a>コードの実行

`pom.xml` ファイルが格納されているディレクトリに移動し、次の `mvn` コマンドを使用してプロジェクトをコンパイルします。

次に、パッケージをビルドします。

```console
mvn package
```

次の `mvn` コマンドを実行して、アプリを実行します。

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
