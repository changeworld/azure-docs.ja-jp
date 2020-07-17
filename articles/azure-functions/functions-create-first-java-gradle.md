---
title: Java と Gradle を使用して関数を Azure に発行する
description: Java と Gradle を使用して、HTTP によってトリガーされる関数を作成し、Azure に発行します。
author: KarlErickson
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: 34aab24bf39e387715cfa5783b801d45ed488750
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732723"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Java と Gradle を使用して関数を作成し、Azure に発行する

この記事では、Gradle コマンドライン ツールを使用して Java 関数プロジェクトを作成し、Azure Functions に発行する方法を示します。 完了すると、関数コードは Azure の[サーバーレス ホスティング プラン](functions-scale.md#consumption-plan)で実行され、HTTP 要求によってトリガーされます。 

> [!NOTE]
> Gradle が推奨される開発ツールでない場合は、[Maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java)、[IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions)、および [VS Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java) を使用する Java 開発者向けの同様のチュートリアルを確認してください。

## <a name="prerequisites"></a>前提条件

Java を使用して関数を開発するには、以下のものがインストールされている必要があります。

- [Java Developer Kit](https://aka.ms/azure-jdks)、バージョン 8
- [Azure CLI]
- [Azure Functions Core Tools](./functions-run-local.md#v2) バージョン 2.6.666 以降
- [Gradle](https://gradle.org/) バージョン 4.10 以降

アクティブな Azure サブスクリプションも必要です。 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> このクイックスタートを行うには、JAVA_HOME 環境変数を JDK のインストール場所に設定する必要があります。

## <a name="prepare-a-functions-project"></a>Functions プロジェクトを準備する

次のコマンドを使用して、サンプル プロジェクトを複製します。

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

`build.gradle` を開き、次のセクションの `appName` を一意の名前に変更して、Azure にデプロイするときにドメイン名が競合しないようにします。 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```

テキスト エディターで *src/main/java* パスの新しい Function.java ファイルを開き、生成されたコードを確認します。 このコードは、要求の本文をエコーする、[HTTP によってトリガーされる](functions-bindings-http-webhook.md)関数です。 

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>関数をローカルで実行する

次のコマンドを実行して、関数プロジェクトをビルドして実行します。

```bash
gradle jar --info
gradle azureFunctionsRun
```
プロジェクトをローカルで実行すると、次のような Azure Functions Core Tools からの出力が表示されます。

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

新しいターミナル ウィンドウで次の cURL コマンドを使用して、コマンド ラインから関数をトリガーします。

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

予想される出力は次のとおりです。

<pre>
Hello AzureFunctions!
</pre>

ローカルで実行する場合、[関数キー](functions-bindings-http-webhook-trigger.md#authorization-keys)は必要ありません。  
関数のコードを停止するには、ターミナルで `Ctrl+C` を使います。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>関数を Azure にデプロイする

最初に関数アプリをデプロイすると、関数アプリと関連リソースが Azure に作成されます。 デプロイする前に、[az login](/cli/azure/authenticate-azure-cli) Azure CLI コマンドを使用して、対象の Azure サブスクリプションにサインインします。 

```azurecli
az login
```

> [!TIP]
> 対象のアカウントで複数のサブスクリプションにアクセスできる場合は、[az account set](/cli/azure/account#az-account-set) を使用して、このセッションの既定のサブスクリプションを設定します。 

次のコマンドを使用して、対象のプロジェクトを新しい関数アプリにデプロイします。 

```bash
gradle azureFunctionsDeploy
```

これにより、gradle ファイルの値に基づいて、次のリソースが Azure に作成されます。

+ リソース グループ。 指定した _resourceGroup_ の名前が付けられます。
+ ストレージ アカウント。 Functions に必要です。 名前は、ストレージ アカウント名の要件に基づいてランダムに生成されます。
+ App Service プラン。 指定した _appRegion_ での、関数アプリのサーバーレス従量課金プラン ホスティング。 名前はランダムに生成されます。
+ 関数アプリ。 関数アプリは、関数のデプロイと実行の単位です。 名前は _appName_ で、ランダムに生成された番号が付加されます。 

また、デプロイにより、プロジェクト ファイルがパッケージ化され、[zip デプロイ](functions-deployment-technologies.md#zip-deploy)を使用して Run-From-Package モードが有効な状態で新しい関数アプリにデプロイされます。

公開した HTTP トリガーは `authLevel = AuthorizationLevel.FUNCTION` を使用するため、HTTP 経由で関数エンドポイントを呼び出すために関数キーを取得する必要があります。 関数キーを取得する最も簡単な方法は、[Azure portal] を利用することです。

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>HTTP トリガー URL を取得する

関数をトリガーするために必要な URL は、関数キーと共に Azure portal から取得できます。 

1. [Azure portal] を参照してサインインし、対象の関数アプリの _appName_ をページ上部の **[検索]** に入力して、Enter キーを押します。
 
1. 対象の関数アプリで、 **[関数 (読み取り専用)]** を展開し、対象の関数を選択します。次に、右上にある **[</> 関数の URL の取得]** を選択します。 

    ![Azure Portal からの関数 URL のコピー](./media/functions-create-java-maven/get-function-url-portal.png)

1. **[default (Function key)]\(既定 (関数キー)\)** を選択し、 **[コピー]** を選択します。 

これで、コピーした URL を使用して関数にアクセスできます。

## <a name="verify-the-function-in-azure"></a>Azure で関数を検証する

`cURL` を使用して Azure で実行されている関数アプリを検証するには、次のサンプルの URL を、ポータルからコピーした URL に置き換えます。

```console
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpExample?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

これにより、要求の本文に `AzureFunctions` が含まれる POST 要求が関数エンドポイントに送信されます。 次の応答が表示されます。

<pre>
Hello AzureFunctions!
</pre>

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>次のステップ

HTTP でトリガーされる関数を含む Java 関数プロジェクトを作成し、ローカル コンピューターでそれを実行し、Azure にデプロイしました。 次は以下の方法で関数を拡張します。

> [!div class="nextstepaction"]
> [Azure Storage キュー出力バインドを追加する](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure Portal]: https://portal.azure.com
