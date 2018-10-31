---
title: スケジュールされたクリーンアップ タスクを Azure Functions で実行する | Microsoft Docs
description: Azure Functions を使用して、Azure SQL Database に接続し、定期的に行をクリーンアップするタスクをスケジュールします。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 05/22/2017
ms.author: glenga
ms.openlocfilehash: 024958d8a548313b53fc24ade5805de036a89afb
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2018
ms.locfileid: "49351917"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Azure Functions を使用して Azure SQL Database に接続する
このトピックでは、Azure Functions を使用して Azure SQL Database のテーブルの行をクリーンアップするスケジュール済みジョブを作成する方法を示します。 この新しい C# スクリプト関数は、Azure Portal の定義済みタイマー トリガー テンプレートに基づいて作成されます。 このシナリオを実現するには、別途データベースの接続文字列を関数アプリのアプリ設定として設定する作業が必要となります。 このシナリオではデータベースに対する一括操作を使用しています。 

Mobile Apps のテーブルにおける、作成、読み取り、更新、および削除 (CRUD) のそれぞれの操作を関数で処理する場合は、[Mobile Apps バインド](functions-bindings-mobile-apps.md)を使用する必要があります。

> [!IMPORTANT]
> このドキュメント内のサンプルは 1.x ランタイムに適用できます。 1.x 関数アプリを作成する方法については、[こちらにあります](./functions-versions.md#creating-1x-apps)。

## <a name="prerequisites"></a>前提条件

+ このトピックでは、タイマーによってトリガーされる関数を使用します。 トピック「[Azure でタイマーによってトリガーされる関数を作成する](functions-create-scheduled-function.md)」の手順を実行して、この関数の C# バージョンを作成します。   

+ このトピックでは、AdventureWorksLT サンプル データベースの **SalesOrderHeader** テーブルに対して一括クリーンアップ操作を実行する Transact-SQL コマンドの例を取り上げています。 AdventureWorksLT サンプル データベースを作成するには、トピック「[Azure Portal で Azure SQL データベースを作成する](../sql-database/sql-database-get-started-portal.md)」の手順を実行します。 

## <a name="get-connection-information"></a>接続情報の取得

「[Azure Portal で Azure SQL データベースを作成する](../sql-database/sql-database-get-started-portal.md)」を完了したときに作成したデータベースの接続文字列を取得する必要があります。

1. [Azure Portal](https://portal.azure.com/) にログインします。
 
3. 左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。

4. **[データベース接続文字列の表示]** を選択し、完全な **ADO.NET** の接続文字列をコピーします。 

    ![ADO.NET の接続文字列をコピーします。](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>接続文字列の設定 

Function App は、Azure での関数の実行をホストします。 接続文字列をはじめとするシークレットは、関数アプリの設定に保存することをお勧めします。 アプリケーション設定を使用して、コードによって誤って接続文字列が公開されることを防ぎます。 

1. 「[Azure でタイマーによってトリガーされる関数を作成する](functions-create-scheduled-function.md)」で作成した関数アプリに移動します。

2. **[プラットフォーム機能]** > **[アプリケーション設定]** を選択します。
   
    ![関数アプリのアプリケーション設定。](./media/functions-scenario-database-table-cleanup/functions-app-service-settings.png)

2. **[接続文字列]** まで下へスクロールして、テーブルに指定されている設定を使用して接続文字列を追加します。
   
    ![接続文字列を関数アプリ設定に追加します。](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-strings.png)

    | Setting       | 推奨値 | Description             | 
    | ------------ | ------------------ | --------------------- | 
    | **名前**  |  sqldb_connection  | 関数コードで保存されている接続文字列にアクセスするために使用します。    |
    | **値** | コピーされた文字列  | 前のセクションでコピーした接続文字列を貼り付け、`{your_username}` および `{your_password}` プレースホルダーを実際の値で置き換えます。 |
    | **種類** | SQL Database | 既定の SQL Database 接続を使用します。 |   

3. **[Save]** をクリックします。

それでは、SQL Database に接続する C# 関数のコードを追加しましょう。

## <a name="update-your-function-code"></a>関数コードの更新

1. ポータルの関数アプリで、タイマーによってトリガーされる関数を選択します。
 
3. 既存の C# スクリプト関数コードの一番上に次のアセンブリ参照を追加します。

    ```cs
    #r "System.Configuration"
    #r "System.Data"
    ```
    >[!NOTE]
    >これらの例のコードは、ポータルの C# スクリプトです。 プリコンパイル済み C# 関数をローカルで開発する場合は、代わりにローカル プロジェクトでこれらのアセンブリへの参照を追加する必要があります。  

3. 次の `using` ステートメントを関数に追加します。
    ```cs
    using System.Configuration;
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

4. 既存の `Run` 関数を次のコードに置き換えます。
    ```cs
    public static async Task Run(TimerInfo myTimer, TraceWriter log)
    {
        var str = ConfigurationManager.ConnectionStrings["sqldb_connection"].ConnectionString;
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " + 
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.Info($"{rows} rows were updated");
            }
        }
    }
    ```

    このサンプル コマンドは、出荷日に基づいて `Status` 列を更新します。 これは、32 行のデータが更新されるはずです。

5. **[保存]** をクリックして、次に関数が実行されるようすを **[ログ]** ウィンドウで観察し、**SalesOrderHeader** テーブルの更新された行数をメモします。

    ![関数のログを表示します。](./media/functions-scenario-database-table-cleanup/functions-logs.png)

## <a name="next-steps"></a>次の手順

次に、Logic Apps で Functions を使用して、その他のサービスと統合する方法を説明します。

> [!div class="nextstepaction"] 
> [Logic Apps と統合される関数を作成する](functions-twitter-email.md)

Functions の詳細については、次のトピックを参照してください。

* [Azure Functions 開発者向けリファレンス](functions-reference.md)  
  関数のコーディングとトリガーおよびバインドの定義に関するプログラマ向けリファレンスです。
* [Azure Functions のテスト](functions-test-a-function.md)  
  関数をテストするための各種ツールと手法について説明します。  
