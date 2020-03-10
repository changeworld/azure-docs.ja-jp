---
title: コマンド ライン ツールを使用して Azure Functions を Azure Storage に接続する
description: コマンド ライン プロジェクトに出力バインディングを追加して Azure Functions を Azure Storage キューに接続する方法を説明します。
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: e3c37b368b723cc95302949baa8e85e2a8b621be
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/29/2020
ms.locfileid: "78201003"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>コマンド ライン ツールを使用して Azure Functions を Azure Storage に接続する

この記事では、[前のクイックスタート](functions-create-first-azure-function-azure-cli.md)で作成したストレージ アカウントと関数に Azure Storage キューを統合します。 この統合は、HTTP 要求からキュー内のメッセージにデータを書き込む "*出力バインディング*" を使用して実現します。 この記事を完了しても、前のクイックスタートの数セントを超えるコストが追加で発生することはありません。 バインドの詳細については、「[Azure Functions でのトリガーとバインドの概念](functions-triggers-bindings.md)」を参照してください。

## <a name="configure-your-local-environment"></a>ローカル環境を構成する

開始する前に、[Azure Functions プロジェクトをコマンド ラインから作成する方法に関するクイックスタート](functions-create-first-azure-function-azure-cli.md)記事を済ませておく必要があります。 その記事の最後でリソースをクリーンアップした場合は、もう一度手順に従って Azure で関数アプリと関連リソースを再作成してください。

## <a name="retrieve-the-azure-storage-connection-string"></a>Azure Storage の接続文字列を取得する

前のクイックスタートで Azure に関数アプリを作成したときに、ストレージ アカウントも作成しました。 このアカウントの接続文字列は、Azure のアプリ設定に安全に格納されています。 関数をローカルで実行するときは、*local.settings.json* ファイルに設定をダウンロードすることにより、その接続を使用して同じアカウントのストレージ キューへの書き込みを行うことができます。 

1. プロジェクトのルートから、次のコマンドを実行します。`<APP_NAME>` は、前のクイックスタートの関数アプリ名に置き換えてください。 このコマンドを実行すると、ファイル内の既存の値はすべて上書きされます。

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. *local.settings.json* を開き、`AzureWebJobsStorage` という名前の値を検索します。それがストレージ アカウントの接続文字列です。 `AzureWebJobsStorage` という名前と接続文字列は、この記事の他のセクションで使用します。

> [!IMPORTANT]
> *local.settings.json* には、Azure からダウンロードされたシークレットが含まれているため、このファイルは必ずソース管理から除外してください。 ローカル関数プロジェクトで作成される *.gitignore* ファイルからは、このファイルが既定で除外されます。

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

## <a name="add-an-output-binding-definition-to-the-function"></a>出力バインディングの定義を関数に追加する

関数に割り当てることができるトリガーは 1 つだけですが、入力と出力のバインディングは複数割り当てることができます。これらを使用すると、カスタム統合コードを記述しなくても、他の Azure サービスやリソースに接続できます。 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
関数フォルダーの *function.json* ファイルでそれらのバインディングを宣言します。 前のクイックスタートの *HttpExample* フォルダーにある *function.json* ファイルでは、`bindings` コレクション内に 2 つのバインディングが含まれています。  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
それぞれのバインディングには、少なくとも型、方向、名前があります。 上の例の 1 つ目のバインディングは、型が `httpTrigger` で、方向が `in` になっています。 `in` 方向の場合、トリガーによって呼び出された関数に送信される入力パラメーターの名前が `name` で指定されます。  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
コレクションの 2 つ目のバインディングの名前は `res` です。 この `http` バインディングは、HTTP 応答の書き込みに使用される出力バインディング (`out`) です。 

この関数から Azure Storage キューに書き込みを行うには、次のコードで示すように、`queue` 型の `out` バインディングを `msg` という名前で追加します。

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
コレクションの 2 つ目のバインディングは、型が `http` で方向が `out` です。この場合、このバインディングは入力パラメーターを渡すものではなく、関数の戻り値を使用するものであることが、`$return` という特殊な `name` からわかります。

この関数から Azure Storage キューに書き込みを行うには、次のコードで示すように、`queue` 型の `out` バインディングを `msg` という名前で追加します。

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
コレクションの 2 つ目のバインディングの名前は `res` です。 この `http` バインディングは、HTTP 応答の書き込みに使用される出力バインディング (`out`) です。 

この関数から Azure Storage キューに書き込みを行うには、次のコードで示すように、`queue` 型の `out` バインディングを `msg` という名前で追加します。

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
このケースでは、`msg` が出力引数として関数に与えられています。 型 `queue` では、`queueName` にキューの名前を指定し、(*local.settings.json* から得られる) Azure Storage 接続の "*名前*" を `connection` に指定する必要もあります。 
::: zone-end  

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  

バインディングの詳細については、「[Azure Functions でのトリガーとバインドの概念](functions-triggers-bindings.md)」と[キューの出力の構成](functions-bindings-storage-queue-output.md#configuration)に関する記事を参照してください。

## <a name="add-code-to-use-the-output-binding"></a>出力バインディングを使用するコードを追加する

キュー バインディングを *function.json* に指定したら、`msg` 出力パラメーターを受け取ってキューにメッセージを書き込むよう関数を更新することができます。

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

認証、キューの参照の取得、データの書き込みのためのコードを記述する必要が "*ない*" 点に注目してください。 これらの統合タスクはすべて、Azure Functions ランタイムとキュー出力バインディングで処理され、手間がかかりません。

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Azure Storage キューのメッセージを確認する

キューは、[Azure portal](../storage/queues/storage-quickstart-queues-portal.md) または [Microsoft Azure Storage Explorer](https://storageexplorer.com/) で確認できます。 次の手順に従って、Azure CLI でキューを確認することもできます。

1. 関数プロジェクトの *local.setting.json* ファイルを開き、接続文字列の値をコピーします。 ターミナルまたはコマンド ウィンドウで、次のコマンドを実行して、`AZURE_STORAGE_CONNECTION_STRING` という名前の環境変数を作成し、`<MY_CONNECTION_STRING>` の代わりに実際の接続文字列を貼り付けます。 (この環境変数を作成すれば、`--connection-string` 引数を使用して接続文字列を後続の各コマンドに指定する必要はありません。)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (省略可) [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) コマンドを使用して、ご利用のアカウント内のストレージ キューを表示します。 このコマンドからの出力には、`outqueue` という名前のキューが含まれています。これはこのキューに対する最初のメッセージを関数が書き込んだときに作成されたものです。
    
    ```azure-cli
    az storage queue list --output tsv
    ```

1. [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) コマンドを使用して、このキューからメッセージ (先ほど関数をテストするときに使用した名) を読み取ります。 このコマンドは、キューから最初のメッセージを読み取って削除します。 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    このスクリプトは、certutil を使用して、ローカルの一時ファイルから Base64 でエンコードされたメッセージのコレクションをデコードします。 出力が存在しない場合、エラーが発生しているといけないので、スクリプトから `> NUL` を削除して、certutil 出力の抑制を解除してみてください。 
    
    ---
    
    メッセージ本文は [base64 でエンコード](functions-bindings-storage-queue-trigger.md#encoding)された状態で保存されるため、表示するメッセージをあらかじめデコードしておく必要があります。 `az storage message get` を実行すると、メッセージがキューから削除されます。 `outqueue` にメッセージが 1 つしかない場合、このコマンドを 2 回目に実行したときにメッセージは取得されず、代わりにエラーが返されます。

## <a name="redeploy-the-project-to-azure"></a>Azure にプロジェクトを再デプロイする

関数から Azure Storage キューにメッセージが書き込まれたことをローカルで確認したので、プロジェクトを再デプロイして、Azure 上で実行するようにエンドポイントを更新することができます。

1. *LocalFunctionsProj* フォルダーで [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) コマンドを使用してプロジェクトを再デプロイし、`<APP_NAME>` を自分のアプリの名前に置き換えます。

    ```
    func azure functionapp publish <APP_NAME>
    ```
    
1. 前のクイックスタートと同様、ブラウザーまたは CURL を使用して、再デプロイした関数をテストします。

    # <a name="browser"></a>[ブラウザー](#tab/browser)
    
    publish コマンドの出力に表示されている完全な**呼び出し URL** にクエリ パラメーター `&name=Functions` を追加して、ブラウザーのアドレス バーにコピーします。 関数をローカルで実行したときと同様の出力がブラウザーに表示されるはずです。

    ![Azure 上で実行された関数の出力をブラウザーで表示したところ](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[curl](#tab/curl)
    
    **呼び出し URL** にパラメーター `&name=Functions` を追加して [`curl`](https://curl.haxx.se/) を実行します。 "Hello Functions" というテキストがコマンドの出力として表示されます。
    
    ![Azure 上で実行された関数の出力を CURL を使用して表示したところ](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. 前セクションの説明に従って、もう一度ストレージ キューを確認します。キューに新しいメッセージが書き込まれていることを確かめてください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

終了後は、追加コストの発生を避けるために、次のコマンドを使用して、リソース グループとそこに含まれるすべてのリソースを削除してください。

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>次のステップ

HTTP によってトリガーされる関数を、ストレージ キューにデータを書き込むように更新しました。 この後は、Core Tools と Azure CLI を使用してコマンド ラインから行う Functions の開発について理解を深めましょう。

+ [Azure Functions Core Tools の操作](functions-run-local.md)  

::: zone pivot="programming-language-csharp"  
+ [C# での完全な関数プロジェクトの例](/samples/browse/?products=azure-functions&languages=csharp)。

+ [Azure Functions C# developer reference (Azure Functions C# 開発者向けリファレンス)](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [JavaScript での完全な関数プロジェクトの例](/samples/browse/?products=azure-functions&languages=javascript)。

+ [Azure Functions の JavaScript 開発者向けガイド](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [TypeScript での完全な関数プロジェクトの例](/samples/browse/?products=azure-functions&languages=typescript)。

+ [Azure Functions の TypeScript 開発者向けガイド](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Python での完全な関数プロジェクトの例](/samples/browse/?products=azure-functions&languages=python)。

+ [Azure Functions の Python 開発者向けガイド](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [PowerShell での完全な関数プロジェクトの例](/samples/browse/?products=azure-functions&languages=azurepowershell)。

+ [Azure Functions の PowerShell 開発者向けガイド](functions-reference-powershell.md) 
::: zone-end
+ [Azure Functions triggers and bindings (Azure Functions のトリガーとバインド)](functions-triggers-bindings.md)

+ [Functions の価格に関するページ](https://azure.microsoft.com/pricing/details/functions/)

+ [従量課金プランのコストの見積もり](functions-consumption-costs.md) 
