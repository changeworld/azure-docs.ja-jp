---
title: "Java と Maven を使用して Azure で初めての関数を作成する | Microsoft Docs"
description: "Java と Maven を使用して、HTTP によってトリガーされる簡単な関数作成し、Azure に発行します。"
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: "Azure Functions, 関数, イベント処理, コンピューティング, サーバーなしのアーキテクチャ"
ms.service: functions
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/03/2017
ms.author: routlaw, glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: 50fd59c288312c7aa5ffe6abf1318a5ec2f406e6
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2017
---
# <a name="create-your-first-function-with-java-and-maven-preview"></a>Java と Maven を使用して初めての関数を作成する (プレビュー)

このクイックスタートでは、Maven で[サーバーレス](https://azure.microsoft.com/overview/serverless-computing/)関数プロジェクトを作成し、ローカルでテストして、Azure Functions にデプロイする手順について説明します。 完了すると、HTTP によってトリガーされる関数アプリが Azure で実行されるようになります。

 ![コマンド ラインから cURL で Hello World 関数にアクセスする](media/functions-create-java-maven/hello-azure.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件
Java で関数アプリを開発するには、以下のものがインストールされている必要があります。

-  [.NET Core](https://www.microsoft.com/net/core) の最新バージョン。
-  [Java Developer Kit](https://www.azul.com/downloads/zulu/) バージョン 1.8。
-  [Azure CLI](https://docs.microsoft.com/cli/azure)
-  [Apache Maven](https://maven.apache.org) バージョン 3.0 以降。
-  [Node.js](https://nodejs.org/download/) バージョン 8.6 以降。

> [!IMPORTANT] 
> このクイックスタートを行うには、JAVA_HOME 環境変数を JDK のインストール場所に設定する必要があります。

## <a name="install-the-azure-functions-core-tools"></a>Azure Functions Core Tools のインストール

[Azure Functions Core Tools 2.0](https://www.npmjs.com/package/azure-functions-core-tools) では、Azure Functions を記述、実行、デバッグするためのローカル開発環境が提供されます。 [Node.js](https://nodejs.org/) に含まれる [npm](https://www.npmjs.com/) でツールをインストールします。

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

Maven では、プロジェクトの生成を終了するための値の入力を求められます。 _groupId_、_artifactId_、_version_ の値については、[Maven の名前付け規則](https://maven.apache.org/guides/mini/guide-naming-conventions.html)をご覧ください。 _appName_ の値は Azure 全体で一意である必要があるため、Maven は既定値として前に入力された _artifactId_ を基にしてアプリ名を生成します。 _packageName_ の値により、生成される関数コードの Java パッケージが決まります。

```Output
Define value for property 'groupId': com.fabrikam.functions
Define value for property 'artifactId' : fabrikam-functions
Define value for property 'version' 1.0-SNAPSHOT : 
Define value for property 'package': com.fabrikam.functions
Define value for property 'appName' fabrikam-functions-20170927220323382:
Confirm properties configuration: Y
```

Maven は、_artifactId_ という名前の新しいフォルダーに、プロジェクト ファイルを作成します。 プロジェクトで生成されるコードは、[HTTP によってトリガーされる](/azure/azure-functions/functions-bindings-http-webhook)、要求の本文をエコーする簡単な関数です。

```java
public class Function {
    @FunctionName("hello")
    public String hello(@HttpTrigger(name = "req", methods = {"get", "post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req,
                        ExecutionContext context) {
        return String.format("Hello, %s!", req);
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

関数を実行すると次のような出力が表示されます。

```Output
Listening on http://localhost:7071
Hit CTRL-C to exit...

Http Functions:

   hello: http://localhost:7071/api/hello
```

新しいターミナルで curl を使ってコマンド ラインから関数をトリガーします。

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/hello
```

```Output
Hello LocalFunction!
```

関数のコードを停止するには、ターミナルで `Ctrl-C` を使います。

## <a name="deploy-the-function-to-azure"></a>関数を Azure にデプロイする

Azure Functions へのデプロイ プロセスでは、Azure CLI からアカウントの資格情報を使います。 [Azure CLI でログイン](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)し、`azure-functions:deploy` Maven ターゲットを使って新しい関数アプリにコードをデプロイします。

```
az login
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

curl を使って Azure で実行して関数アプリをテストします。

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/hello -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="next-steps"></a>次のステップ

簡単な HTTP トリガーを使って Java 関数アプリを作成し、Azure Functions にデプロイしました。

- 「[Azure Functions Java developer guide](functions-reference-java.md)」(Azure Functions Java 開発者ガイド) で、Java 関数の開発の詳細について確認します。
- `azure-functions:add` Maven ターゲットを使って、異なるトリガーの新しい関数をプロジェクトに追加します。
- Visual Studio Code でローカルに関数をデバッグします。 [Java 拡張パック](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)をインストールし、Visual Studio Code で Functions プロジェクトを開いて、ポート 5005 に[デバッガーをアタッチ](https://code.visualstudio.com/Docs/editor/debugging#_launch-configurations)します。 その後、エディターでブレークポイントを設定し、ローカルに実行しながら関数をトリガーします。![Visual Studio Code で関数をデバッグする](media/functions-create-java-maven/vscode-debug.png)



