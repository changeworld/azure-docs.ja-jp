---
title: Java と Maven を使用して Azure で初めての関数を作成する | Microsoft Docs
description: Java と Maven を使用して、HTTP によってトリガーされる簡単な関数作成し、Azure に発行します。
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure Functions, 関数, イベント処理, コンピューティング, サーバーなしのアーキテクチャ
ms.service: azure-functions
ms.devlang: java
ms.topic: quickstart
ms.date: 08/10/2018
ms.author: routlaw, glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: fdd29bbfaf36619fd823220e5d32a48a1619679b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842069"
---
# <a name="create-your-first-function-with-java-and-maven-preview"></a>Java と Maven を使用して初めての関数を作成する (プレビュー)

> [!NOTE] 
> Azure Functions 用の Java は現在プレビュー段階です。

このクイックスタートでは、Maven で[サーバーレス](https://azure.microsoft.com/solutions/serverless/)関数プロジェクトを作成し、ローカルでテストして、Azure にデプロイする手順について説明します。 完了すると、Java 関数コードはクラウドで実行され、HTTP 要求からトリガーできるようになります。

![コマンド ラインから cURL で Hello World 関数にアクセスする](media/functions-create-java-maven/hello-azure.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件
Java で関数アプリを開発するには、以下のものがインストールされている必要があります。

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/) バージョン 8。
-  [Apache Maven](https://maven.apache.org) バージョン 3.0 以降。
-  [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> このクイックスタートを行うには、JAVA_HOME 環境変数を JDK のインストール場所に設定する必要があります。

## <a name="install-the-azure-functions-core-tools"></a>Azure Functions Core Tools のインストール

[Azure Functions Core Tools 2.0](https://www.npmjs.com/package/azure-functions-core-tools) では、Azure Functions を記述、実行、デバッグするためのローカル開発環境が提供されます。 

インストールするには、Azure Functions Core Tools プロジェクトの「[Installing (インストール)](https://github.com/azure/azure-functions-core-tools#installing)」セクションにアクセスし、オペレーティング システムに固有の手順を参照してください。

次の要件のインストール後、[Node.js](https://nodejs.org/) に付属する [npm](https://www.npmjs.com/) を使って手動でインストールすることもできます。

-  [.NET Core](https://www.microsoft.com/net/core) の最新バージョン。
-  [Node.js](https://nodejs.org/download/) バージョン 8.6 以降。

npm ベースのインストールを続行するには、次のコマンドを実行します。

```
npm install -g azure-functions-core-tools@core
```

> [!NOTE]
> Azure Functions Core Tools バージョン 2.0 のインストールで問題がある場合は、「[バージョン 2.x ランタイム](/azure/azure-functions/functions-run-local#version-2x-runtime)」をご覧ください。

## <a name="generate-a-new-functions-project"></a>新しい Functions プロジェクトを生成する

空のフォルダーで次のコマンドを実行して、[Maven アーキタイプ](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html)から Functions プロジェクトを生成します。

### <a name="linuxmacos"></a>Linux/MacOS

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

### <a name="windows-cmd"></a>Windows (CMD)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

Maven によって、プロジェクトの生成を終了するために必要な値の入力を求めるメッセージが表示されます。 _groupId_、_artifactId_、_version_ の値については、[Maven の名前付け規則](https://maven.apache.org/guides/mini/guide-naming-conventions.html)をご覧ください。 _appName_ の値は Azure 全体で一意である必要があるため、Maven は既定値として前に入力された _artifactId_ を基にしてアプリ名を生成します。 _packageName_ の値により、生成される関数コードの Java パッケージが決まります。

以下の `com.fabrikam.functions` および `fabrikam-functions` 識別子は例として使用されており、このクイック スタートの後の方の手順を読みやすくしています。 この手順では、Maven に独自の値を指定することをお勧めします。

```Output
Define value for property 'groupId': com.fabrikam.functions
Define value for property 'artifactId' : fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Confirm properties configuration: Y
```

Maven は、この例 `fabrikam-functions` の場合、_artifactId_ という名前の新しいフォルダーにプロジェクト ファイルを作成します。 プロジェクトで生成される、すぐに実行できるコードは、[HTTP によってトリガーされる](/azure/azure-functions/functions-bindings-http-webhook)、要求の本文をエコーする簡単な関数です。

```java
public class Function {
    /**
     * This function listens at endpoint "/api/hello". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/hello
     * 2. curl {your host}/api/hello?name=HTTP%20Query
     */
    @FunctionName("hello")
    public HttpResponseMessage<String> hello(
            @HttpTrigger(name = "req", methods = {"get", "post"}, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");

        // Parse query parameter
        String query = request.getQueryParameters().get("name");
        String name = request.getBody().orElse(query);

        if (name == null) {
            return request.createResponse(400, "Please pass a name on the query string or in the request body");
        } else {
            return request.createResponse(200, "Hello, " + name);
        }
    }
}

```

## <a name="run-the-function-locally"></a>関数をローカルで実行する

新しく作成されたプロジェクト フォルダーにディレクトリを変更し、Maven で関数をビルドして実行します。

```
cd fabrikam-function
mvn clean package 
mvn azure-functions:run
```

> [!NOTE]
> Java 9 でこの例外、`javax.xml.bind.JAXBException` が発生している場合は、[GitHub](https://github.com/jOOQ/jOOQ/issues/6477) で回避策を参照してください。

関数がシステム上でローカルに実行されていて、HTTP 要求に応答する準備ができている場合に、次の出力が表示されます。

```Output
Listening on http://localhost:7071
Hit CTRL-C to exit...

Http Functions:

   hello: http://localhost:7071/api/hello
```

新しいターミナル ウィンドウで curl を使用して、コマンド ラインから関数をトリガーします。

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/hello
```

```Output
Hello LocalFunction!
```

関数のコードを停止するには、ターミナルで `Ctrl-C` を使います。

## <a name="deploy-the-function-to-azure"></a>関数を Azure にデプロイする

Azure Functions へのデプロイ プロセスでは、Azure CLI からアカウントの資格情報を使います。 続行する前に、[Azure CLI にログイン](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)します。

```azurecli
az login
```

`azure-functions:deploy` Maven ターゲットを使用して、新しい関数アプリにコードをデプロイします。

```
mvn azure-functions:deploy
```

デプロイが完了すると、Azure 関数アプリへのアクセスに使うことができる URL が表示されます。

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

`cURL` を使用して、Azure で実行している関数アプリをテストします。 前の手順でデプロイされた独自の関数アプリの URL と一致するように、下のサンプルの URL を変更する必要があります。

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/hello -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>変更を加えて再デプロイする

関数アプリから返されるテキストに変更を加えるために、生成されたプロジェクトの `src/main.../Function.java` ソース ファイルを編集します。 変更するのは次の行です。

```java
return request.createResponse(200, "Hello, " + name);
```

これを次のように変更してください。

```java
return request.createResponse(200, "Hi, " + name);
```

変更内容を保存し、ターミナルから `azure-functions:deploy` を実行して再デプロイします。 関数アプリが更新されます。次の要求を実行してみましょう。

```bash
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

出力結果が更新されていることがわかります。

```Output
Hi, AzureFunctionsTest
```

## <a name="next-steps"></a>次の手順

簡単な HTTP トリガーを使って Java 関数アプリを作成し、Azure Functions にデプロイしました。

- 「[Azure Functions Java developer guide](functions-reference-java.md)」(Azure Functions Java 開発者ガイド) で、Java 関数の開発の詳細について確認します。
- `azure-functions:add` Maven ターゲットを使って、異なるトリガーの新しい関数をプロジェクトに追加します。
- [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)、[IntelliJ](functions-create-maven-intellij.md)、[Eclipse](functions-create-maven-eclipse.md) を使って関数を作成し、ローカルでデバッグします。 
- Azure にデプロイされた関数を Visual Studio Code でデバッグします。 その手順については、Visual Studio Code の[サーバーレス Java アプリケーション](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)に関するドキュメントを参照してください。
