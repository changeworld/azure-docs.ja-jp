---
title: Azure Functions で関数を無効にする方法
description: Azure Functions で関数を無効または有効にする方法を学びます。
ms.topic: conceptual
ms.date: 02/03/2021
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: cbb84308507ea15f1c44c00122a9a59472f12a88
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551045"
---
# <a name="how-to-disable-functions-in-azure-functions"></a>Azure Functions で関数を無効にする方法

この記事では、Azure Functions で関数を無効にする方法について説明します。 関数を *無効にする* には、その関数用に定義された自動トリガーをランタイムが無視するようにします。 これにより、Function App 全体を停止することなく、特定の関数の実行を防ぐことができます。

関数を無効にするに方法として推奨されるのは、アプリの設定で形式 `AzureWebJobs.<FUNCTION_NAME>.Disabled` を `true` に設定することです。 このアプリケーション設定は、[Azure CLI](/cli/azure/) を使用したり、[Azure portal](https://portal.azure.com) で関数の **[概要]** タブを使用したりするなど、さまざまな方法で作成または編集できます。 

> [!NOTE]  
> この記事で説明されている方法を使用して、HTTP によってトリガーされる機能を無効にしても、ご利用のローカル コンピューター上で実行すると、エンドポイントにアクセスできる可能性があります。  

## <a name="use-the-azure-cli"></a>Azure CLI の使用

Azure CLI でアプリの設定を作成および変更するには、[`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) コマンドを使います。 `AzureWebJobs.QueueTrigger.Disabled` という名前のアプリの設定を作成して `true` に設定することにより `QueueTrigger` という名前の関数を無効にするコマンドを次に示します。 

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=true
```

関数を再度有効にするには、値 `false` を指定して同じコマンドを再実行します。

```azurecli-interactive
az functionapp config appsettings set --name <myFunctionApp> \
--resource-group <myResourceGroup> \
--settings AzureWebJobs.QueueTrigger.Disabled=false
```

## <a name="use-the-portal"></a>ポータルを使用する

関数の **[概要]** ページの **[有効にする]** ボタンと **[無効にする]** ボタンを使用することもできます。 `AzureWebJobs.<FUNCTION_NAME>.Disabled` アプリ設定の値を変更することでこれらのボタンは機能します。 この関数固有設定は、初めて無効にしたときに作成されます。 

![[関数の状態] スイッチ](media/disable-function/function-state-switch.png)

ローカル プロジェクトから関数アプリに公開しても、引き続きポータルを使用して関数アプリ内の関数を無効にすることができます。 

> [!NOTE]  
> ポータルに統合されたテスト機能では、`Disabled` 設定が無視されます。 つまり、ポータルの **[テスト]** ウィンドウから開始した場合、関数は無効にされていても実行されます。 

## <a name="localsettingsjson"></a>local.settings.json

関数は、ローカルで実行する場合と同じ方法で無効にすることができます。 `HttpExample` という名前の関数を無効にするには、次のように、local.settings.json ファイルの Values コレクションにエントリを追加します。

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "UseDevelopmentStorage=true", 
    "AzureWebJobs.HttpExample.Disabled": "true"
  }
}
``` 

## <a name="other-methods"></a>その他の方法

このアプリケーション設定方法をすべての言語およびすべてのランタイム バージョンに対して使用するようお勧めしますが、関数を無効にする方法は他にもいくつかあります。 これらのメソッドは、言語とランタイム バージョンによって異なり、旧バージョンとの下位互換性が保持されています。 

### <a name="c-class-libraries"></a>C# クラス ライブラリ

クラス ライブラリ関数では、`Disable` 属性を使用して、関数がトリガーされないようにすることもできます。 この属性を使用すると、関数の無効化に使用する設定の名前をカスタマイズできます。 次の例に示すように、ブール値アプリ設定を参照するコンストラクター パラメーターを定義できる属性のバージョンを使用します。

```csharp
public static class QueueFunctions
{
    [Disable("MY_TIMER_DISABLED")]
    [FunctionName("QueueTrigger")]
    public static void QueueTrigger(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        TraceWriter log)
    {
        log.Info($"C# function processed: {myQueueItem}");
    }
}
```

この方法では、アプリ設定を変更することで、関数を有効および無効にすることができます。再コンパイルや再デプロイは必要ありません。 アプリ設定を変更すると Function App が再起動されるため、無効状態の変更がすぐに認識されます。

設定名の文字列を受け取らないパラメーターのコンストラクターもあります。 このバージョンの属性は推奨されません。 このバージョンを使用する場合は、プロジェクトを再コンパイルして再デプロイし、関数の無効状態を変更する必要があります。

### <a name="functions-1x---scripting-languages"></a>Functions 1.x - スクリプト言語

バージョン 1.x では、*function.json* ファイルの `disabled` プロパティを使用して、関数をトリガーしないようにランタイムを指定することもできます。 このメソッドは、C# スクリプトや JavaScript などのスクリプト言語に対してのみ機能します。 この `disabled` プロパティは、`true` またはアプリ設定の名前に設定できます：

```json
{
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ],
    "disabled": true
}
```
or 

```json
    "bindings": [
        ...
    ],
    "disabled": "IS_DISABLED"
```

2 つ目の例では、`true` または 1 に設定されている IS_DISABLED という名前のアプリ設定がある場合に関数が無効になります。

>[!IMPORTANT]  
>ポータルでは、アプリケーション設定を使用して v1.x 関数を無効にします。 アプリケーション設定が function.json ファイルと競合するとき、エラーが発生することがあります。 エラーを回避するには、function.json ファイルから `disabled` プロパティを削除してください。 


## <a name="next-steps"></a>次のステップ

この記事は、自動トリガーの無効化に関する記事です。 トリガーについて詳しくは、[トリガーとバインド](functions-triggers-bindings.md)に関するページをご覧ください。
