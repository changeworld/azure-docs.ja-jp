---
title: インクルード ファイル
description: インクルード ファイル
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: a9c8d604e5564526936f37edcc9eec5891443a47
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779344"
---
## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [Java Development Kit (JDK)](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true) バージョン 8 以降。
- [Apache Maven](https://maven.apache.org/download.cgi)。
- デプロイされた Communication Services リソースと接続文字列。 [Communication Services のリソースを作成する](../create-communication-resource.md)。

## <a name="setting-up"></a>設定

### <a name="create-a-new-java-application"></a>新しい Java アプリケーションを作成する

ターミナルまたはコマンド ウィンドウを開き、Java アプリケーションを作成するディレクトリに移動します。 次のコマンドを実行して、maven-archetype-quickstart テンプレートから Java プロジェクトを生成します。

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

"generate" タスクによって、`artifactId` と同じ名前のディレクトリが作成されたことがわかります。 このディレクトリの下の src/main/java ディレクトリにはプロジェクトのソース コードが含まれており、 `src/test/java directory` にはテスト ソースが含まれており、`pom.xml` ファイルはプロジェクトのプロジェクト オブジェクト モデル (POM) です。

### <a name="install-the-package"></a>パッケージをインストールする

テキスト エディターで **pom.xml** ファイルを開きます。 依存関係のグループに、次の dependency 要素を追加します。

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-administration</artifactId>
    <version>1.0.0-beta.2</version> 
</dependency>
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクト ディレクトリで次の操作を行います。

1. */src/main/java/com/communication/quickstart* ディレクトリに移動します
1. 使用しているエディターで *App.java* ファイルを開きます
1. `System.out.println("Hello world!");` ステートメントを置き換えます
1. `import` ディレクティブを追加します

次のコードを使用して開始します。

```java
import com.azure.communication.common.CommunicationUser;
import com.azure.communication.administration.models.CommunicationIdentityToken;
import com.azure.communication.administration.CommunicationIdentityClient;
import com.azure.communication.administration.CommunicationIdentityClientBuilder;
import java.io.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - User Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>クライアントを認証する

リソースのアクセス キーとエンドポイントを使用して、`CommunicationIdentityClient` をインスタンス化します。 [リソースの接続文字列を管理する](../create-communication-resource.md#store-your-connection-string)方法について確認してください。

`main` メソッドに次のコードを追加します。

```java
// Your can find your endpoint and access token from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessToken = "SECRET";

// Create an HttpClient builder of your choice and customize it
// Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .endpoint(endpoint)
    .credential(new CommunicationClientCredential(accessToken))
    .httpClient(httpClient)
    .buildClient();
```

`com.azure.core.http.HttpClient` インターフェイスを実装する任意のカスタム HTTP クライアントを使用して、クライアントを初期化できます。 上記のコードは、`azure-core` によって提供される [Azure Core Netty HTTP クライアント](https://docs.microsoft.com/java/api/overview/azure/core-http-netty-readme?view=azure-java-stable&preserve-view=true)の使用方法を示しています。

## <a name="create-a-user"></a>ユーザーを作成する

Azure Communication Services は、軽量の ID ディレクトリを保持します。 `createUser` メソッドを使用して、一意の `Id` を持つディレクトリに新しいエントリを作成します。 お使いのアプリケーションのユーザーと、Communication Services で生成された ID の間のマッピングを (アプリケーション サーバーのデータベースにこれらを格納するなどによって) 維持する必要があります。

```java
CommunicationUser user = communicationIdentityClient.createUser();
System.out.println("\nCreated a user with ID: " + user.getId());
```

## <a name="issue-user-access-tokens"></a>ユーザー アクセス トークンを発行する

Communication Services ユーザーのアクセス トークンを発行するには、`issueToken` メソッドを使用します。 オプションの `user` パラメーターを指定しない場合は、新しいユーザーが作成され、トークンと共に返されます。

```java
// Issue an access token with the "voip" scope for a new user
List<String> scopes = new ArrayList<>(Arrays.asList("voip"));
CommunicationUserToken response = communicationIdentityClient.issueToken(user, scopes);
OffsetDateTime expiresOn = response.getExpiresOn();
String token = response.getToken();
String userId = response.getUser().getId();
System.out.println("\nIssued a access token with 'voip' scope for identity with ID: " + userId + ": " + token);
System.out.println(token);
```

ユーザー アクセス トークンは、ユーザーのサービス中断を防ぐために再発行する必要がある、有効期間の短い資格情報です。 `expiresAt` 応答プロパティは、トークンの有効期間を示します。

## <a name="revoke-user-access-tokens"></a>ユーザー アクセス トークンを取り消す

場合によっては、ユーザーがサービスへの認証に使用するパスワードを変更したときなどに、ユーザー アクセス トークンを明示的に取り消す必要があります。 これには `revokeTokens` メソッドを使用して、すべてのユーザーのアクセス トークンを無効にします。

```java  
communicationIdentityClient.revokeTokens(user, OffsetDateTime.now());
System.out.println("\nRevoked tokens for the user with ID: " + user.getId());
```

## <a name="delete-a-user"></a>ユーザーの削除

ユーザーを削除すると、すべてのアクティブなトークンが取り消され、その ID についての後続のトークンを発行できなくなります。 また、ユーザーに関連付けられているすべての永続化されたコンテンツも削除されます。

```java
communicationIdentityClient.deleteUser(user);
System.out.println("\nSuccessfully deleted the identity with ID: " + user.getId());
```

## <a name="run-the-code"></a>コードの実行

*pom.xml* ファイルが格納されているディレクトリに移動し、次の `mvn` コマンドを使用してプロジェクトをコンパイルします。

```console
mvn compile
```

次に、パッケージをビルドします。

```console
mvn package
```

次の `mvn` コマンドを実行して、アプリを実行します。

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
