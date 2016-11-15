---
title: "Azure サービスにバインドする Azure 関数を作成する | Microsoft Docs"
description: "他の Azure サービスと対話するサーバーレス アプリケーションである Azure Function を作成します。"
services: functions
documentationcenter: dev-center-name
author: yochay
manager: manager-alias
editor: 
tags: 
keywords: "Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: ab86065d-6050-46c9-a336-1bfc1fa4b5a1
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/25/2016
ms.author: rachelap@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9ffd199c9e3c621a808ade109ed044b6c9b689b7


---
# <a name="create-an-azure-function-which-binds-to-an-azure-service"></a>Azure サービスにバインドする Azure Function を作成する
[!INCLUDE [Getting Started Note](../../includes/functions-getting-started.md)]

この短いビデオでは、Azure Queue でメッセージをリッスンし、メッセージを Azure BLOB にコピーする Azure 関数を作成する方法を説明します。

## <a name="watch-the-video"></a>ビデオを見る
>[!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-an-Azure-Function-which-binds-to-an-Azure-service/player]
>
>

## <a name="create-an-input-queue-trigger-function"></a>入力キュー トリガー関数を作成する
この関数の目的は、10 秒ごとにメッセージをキューに書き込むことです。 これを実現するには、関数とメッセージ キューを作成し、新しく作成されたキューにメッセージを書き込むコードを追加する必要があります。

1. Azure Portal に移動し、使用する Azure Function App を見つけます。
2. **[新しい関数]** > **[TimerTrigger - Node]** をクリックします。 関数に **FunctionsBindingsDemo1** という名前を付けます。
3. [スケジュール] の値として「0/10 * * * * *」と入力します。 この値は cron 式の形式です。 この値により、10 秒ごとに実行するようにタイマーのスケジュールが設定されます。
4. **[作成]** をクリックして関数を作成します。
   
    ![トリガー タイマー関数を追加する](./media/functions-create-an-azure-connected-function/new-trigger-timer-function.png)
5. ログのアクティビティを表示して、関数の動作を確認します。 ログ ウィンドウを表示するために、右上隅の **[ログ]** リンクをクリックすることが必要な場合があります。
   
   ![ログを表示して関数の動作を確認する](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-view-log.png)

### <a name="add-a-message-queue"></a>メッセージ キューを追加する
1. **[統合]** タブに移動します。
2. **[新しい出力]** > **[Azure Storage キュー]** > **[選択]** をクリックします。
3. **[メッセージ パラメーター名]** ボックスに「**myQueueItem**」と入力します。
4. ストレージ アカウントを選択します。既存のアカウントがない場合は、**[新規]** をクリックしてストレージ アカウントを作成します。
5. **[キュー名]** ボックスに「**functions-bindings**」と入力します。
6. **[保存]**をクリックします。  
   
   ![トリガー タイマー関数を追加する](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-integrate-tab.png)

### <a name="write-to-the-message-queue"></a>メッセージ キューに書き込む
1. **[開発]** タブに戻り、関数の既存のコードの後に次のコードを追加します。
   
    ```javascript
   
    function myQueueItem() 
      {
        return {
        msg: "some message goes here",
        time: "time goes here"
      }
    }
   
    ```
2. 既存の関数コードを変更して、手順 1. で追加したコードを呼び出します。 関数の 9 行目あたり、*if* ステートメントの後に次のコードを挿入します。
   
    ```javascript
   
    var toBeQed = myQueueItem();
    toBeQed.time = timeStamp;
    context.bindings.myQueue = toBeQed;
   
    ```
   
    このコードでは、**myQueueItem** を作成し、その **time** プロパティを現在の timeStamp に設定します。 その後、コンテキストの myQueue バインドに新しいキュー アイテムを追加します。
3. **[保存および実行]** をクリックします。
4. Visual Studio でキューを表示して、コードの動作を確認します。
   
   * Visual Studio を開き、**[ビュー]** > **[クラウド** **エクスプローラー]** に移動します。
   * myQueue キューの作成時に使用したストレージ アカウントと **functions-bindings** キューを見つけます。 ログ データの行が表示されます。 Visual Studio から Azure にサインインすることが必要な場合があります。  

## <a name="create-an-output-queue-trigger-function"></a>出力キュー トリガー関数を作成する
1. **[新しい関数]** > **[QueueTrigger - C#]** をクリックします。 関数に **FunctionsBindingsDemo2** という名前を付けます。 同じ関数アプリに言語を混在させることができます (この例では Node と C#)。
2. **[キュー名]** フィールドに「**functions-bindings**」と入力します。
3. 使用するストレージ アカウントを選択するか、新しいアカウントを作成します。
4.  **[作成]**
5. 関数のログを表示し、Visual Studio で更新内容を表示して、新しい関数の動作を確認します。 関数のログには、関数が実行されており、アイテムがデキューされていることが示されています。 関数は入力トリガーとして **functions-bindings** 出力キューにバインドされているため、Visual Studio で **functions-bindings** キューを更新すると、アイテムがなくなっていることがわかります。 これらのアイテムはデキューされました。   
   
   ![出力キュー タイマー関数を追加する](./media/functions-create-an-azure-connected-function/functionsbindingsdemo2-integrate-tab.png)   

### <a name="modify-the-queue-item-type-from-json-to-object"></a>キュー アイテムの種類を JSON からオブジェクトに変更する
1. **FunctionsBindingsDemo2** のコードを次のコードに置き換えます。    
   
    ```cs
   
    using System;
   
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {
      TableItem myItem = new TableItem
      {
        PartitionKey = "key",
        RowKey = Guid.NewGuid().ToString(),
        Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
        Msg = myQueueItem.Msg,
        OriginalTime = myQueueItem.Time    
      };
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
   
    public class QItem
    {
      public string Msg { get; set;}
      public string Time { get; set;}
    }
   
    ```
   
    このコードでは、キューの読み取りとキューへの書き込みに使用する、**TableItem** と **QItem** の 2 つのクラスを追加します。 また、**string** と **TraceWriter** の代わりに、**QItem** および **TraceWriter** パラメーターを受け取るように **Run** 関数が変更されています。 
2. **[保存]** ボタンをクリックします。
3. ログを調べてコードの動作を確認します。 Azure 関数はオブジェクトを自動的にシリアル化および逆シリアル化するので、データを受け渡す際にオブジェクト指向でキューに簡単にアクセスできます。 

## <a name="store-messages-in-an-azure-table"></a>メッセージを Azure テーブルに保存する
複数のキューが連携しているので、キュー データの永続的なストレージとして Azure テーブルを追加します。

1. **[統合]** タブに移動します。
2. 出力用の Azure Storage テーブルを作成し、**myTable** という名前を付けます。
3. データの書き込み先のテーブルをたずねられたら、**functionsbindings** と答えます。
4. **PartitionKey** 設定を **{project-id}** から **{partition}** に変更します。
5. ストレージ アカウントを選択するか、新しいアカウントを作成します。
6. **[保存]**をクリックします。
7. **[開発]** タブに移動します。
8. Azure テーブルを表す **TableItem** クラスを作成し、新しく作成された TableItem オブジェクトを受け取るように Run 関数を変更します。 これを機能させるには、**PartitionKey** プロパティと **RowKey** プロパティを使用する必要があります。
   
    ```cs
   
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {    
      TableItem myItem = new TableItem
      {
        PartitionKey = "key",
        RowKey = Guid.NewGuid().ToString(),
        Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
        Msg = myQueueItem.Msg,
        OriginalTime = myQueueItem.Time    
      };
   
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.RowKey} | {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
    ```
9. **[保存]**をクリックします。
10. 関数のログを表示し、Visual Studio で関数を表示して、コードの動作を確認します。 Visual Studio で確認するには、**クラウド エクスプローラー**を使用して **functionbindings** Azure テーブルに移動し、テーブルに行が存在することを確認します。

[!INCLUDE [Getting Started Note](../../includes/functions-bindings-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO2-->


