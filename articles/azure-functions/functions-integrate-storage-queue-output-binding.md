---
title: "キュー メッセージでトリガーされる Azure 関数の作成 | Microsoft Docs"
description: "Azure Functions を使用して、Azure Storage キューに送信されたメッセージによって呼び出されるサーバーレスの関数を作成します。"
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: ccafe4d440f533560fcb3780d4e0e440a4399021
ms.contentlocale: ja-jp
ms.lasthandoff: 05/18/2017

---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>Functions を使用して Azure Storage キューにメッセージを追加する

Azure Functions では、入力および出力バインディングによって、関数から外部サービス データに接続する宣言方法が提供されます。 このトピックでは、Azure Queue Storage にメッセージを送信する出力バインディングを追加することで既存の関数を更新する方法について説明します。  

![ログ内のメッセージを表示します。](./media/functions-integrate-storage-queue-output-binding/functions-integrate-storage-binding-in-portal.png)

## <a name="prerequisites"></a>前提条件 

[!INCLUDE [Previous topics](../../includes/functions-quickstart-previous-topics.md)]

[Microsoft Azure Storage エクスプローラー](http://storageexplorer.com/)をダウンロードしてインストールする必要もあります。 

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="add-binding"></a>出力バインディングを追加する
 
1. Function App と関数の両方を展開します。

2. **[Integrate] (統合)** と **[+ New output] (+ 新しい出力)** をクリックしてから、**[Azure Queue storage] (Azure Queue Storage)** をクリックし、**[Select] (選択)** をクリックします。
    
    ![Azure Portal 内の関数に Queue Storage の出力バインディングを追加します。](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding.png)

3. テーブルに指定されている設定を使用し、**[Save] (保存)** をクリックします。 

    ![Azure Portal 内の関数に Queue Storage の出力バインディングを追加します。](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding-2.png)

    | 設定      |  推奨値   | Description                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **キュー名**   | myqueue-items    | ストレージ アカウント内の接続先のキューの名前。 |
    | **ストレージ アカウント接続** | AzureWebJobStorage | Function App によって既に使用されているストレージ アカウント接続を使用するか、新しく作成できます。  |
    | **メッセージ パラメーター名** | outQueueItem | 出力バインディング パラメーターの名前。 | 

出力バインディングが定義されたので、コードを更新し、バインディングを使用して、メッセージをキューに追加する必要があります。  

## <a name="update-the-function-code"></a>関数コードを更新する

1. 関数をクリックし、エディターに関数コードを表示します。 

2. C# 関数の場合は、次のように関数定義を更新して、**outQueueItem** ストレージ バインディング パラメーターを追加します。 JavaScript 関数の場合は、この手順をスキップします。

    ```cs   
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, 
        ICollector<string> outQueueItem, TraceWriter log)
    {
        ....
    }
    ```

3. メソッドが戻る直前に、関数に次のコードを追加します。 関数の言語に合ったスニペットを使用します。

    ```javascript
    context.bindings.outQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ```cs
    outQueueItem.Add("Name passed to the function: " + name);     
    ```

4. **[保存]** をクリックして変更を保存します。

HTTP トリガーに渡される値は、キューに追加されるメッセージに含まれています。
 
## <a name="test-the-function"></a>関数をテストする 

1. コードの変更が保存されたら、**[Run] (実行)** をクリックします。 

    ![Azure Portal 内の関数に Queue Storage の出力バインディングを追加します。](./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png)

2. ログを確認して、関数が成功したことを確認します。 出力バインディングを最初に使用するときに、**outqueue** という名前の新しいキューが、Functions ランタイムによってストレージ アカウントに作成されます。

次に、ストレージ アカウントに接続して、追加した新しいキューとメッセージを確認することができます。 

## <a name="connect-to-the-queue"></a>キューに接続する

既に Storage エクスプローラーをインストールしてストレージ アカウントに接続している場合は、最初の 3 つの手順をスキップします。    

1. 関数で、**[Integrate] (統合)** と新しい **[Azure Queue storage] (Azure Queue Storage)** 出力バインディングをクリックし、**[Documentation] (ドキュメント)** を展開します。 **[アカウント名]** と **[アカウント キー]** の両方をコピーします。 これらの資格情報を使用して、ストレージ アカウントに接続します。
 
    ![ストレージ アカウント接続の資格情報を取得します。](./media/functions-integrate-storage-queue-output-binding/function-get-storage-account-credentials.png)

2. [Microsoft Azure Storage Explorer](http://storageexplorer.com/) ツールを実行し、左側の接続アイコンをクリックして、**[Use a storage account name and key] (ストレージ アカウント名とキーを使用)** を選択し、**[次へ]** をクリックします。

    ![ストレージ アカウント エクスプローラー ツールを実行します。](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-1.png)
    
3. 手順 1 の **[アカウント名]** と **[アカウント キー]** を入力し、**[次へ]** をクリックし、**[接続]** をクリックします。 
  
    ![ストレージ資格情報を入力し、接続します。](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-2.png)

4. 接続されたストレージ アカウントを展開し、**[Queues] (キュー)** を右クリックして、**myqueue-items** という名前のキューが存在することを確認します。 既にメッセージもキューに表示されているはずです。  
 
    ![ストレージ キューを作成します。](./media/functions-integrate-storage-queue-output-binding/function-queue-storage-output-view-queue.png)
 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次のステップ

既存の関数に出力バインディングを追加しました。 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Queue Storage へのバインディングの詳細については、「[Azure Functions における Storage キュー バインド](functions-bindings-storage-queue.md)」を参照してください。 




