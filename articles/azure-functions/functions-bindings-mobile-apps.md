---
title: Azure Functions における Mobile Apps のバインド | Microsoft Docs
description: Azure Functions で Azure Mobile Apps のバインドを使用する方法について説明します。
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: ''
tags: ''
keywords: Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ

ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/30/2016
ms.author: glenga

---
# Azure Functions における Mobile Apps のバインド
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で Azure Mobile Apps のバインドを構成したりコーディングしたりする方法について説明します。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Azure App Service Mobile Apps を使用すると、テーブル エンドポイント データをモバイル クライアントに公開できます。この同じ表形式のデータは、Azure Functions の入力バインドと出力バインドの両方で使用できます。動的スキーマをサポートしているため、Node.js バックエンドのモバイル アプリは、関数で使用するために表形式のデータを公開するのに最適です。動的スキーマは既定で有効になっていますが、運用環境のモバイル アプリでは無効にする必要があります。Node.js バックエンドにおけるテーブル エンドポイントの詳細については、[テーブル操作の概要に関するセクション](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations)を参照してください。Mobile Apps では、Node.js バックエンドは、ポータル内の参照とテーブルの編集をサポートします。詳細については、Node.js SDK トピックの[ポータルでの編集に関するセクション](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#in-portal-editing)を参照してください。Azure Functions で .NET バックエンドのモバイル アプリを使用する場合は、関数からの必要に応じて、データ モデルを手動で更新する必要があります。.NET バックエンドのモバイル アプリのテーブル エンドポイントの詳細については、.NET バックエンドの SDK トピックの「[方法: テーブル コントローラーを定義する](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller)」を参照してください。

## モバイル アプリ バックエンドの URL の環境変数を作成する
現在、モバイル アプリのバインドでは、モバイル アプリ バックエンド自体の URL を返す環境変数を作成する必要があります。この URL は、[Azure ポータル](https://portal.azure.com)でモバイル アプリを検索してブレードを開くと、見付けることができます。

![Azure ポータルの Mobile Apps ブレード](./media/functions-bindings-mobile-apps/mobile-app-blade.png)

この URL を環境変数として関数アプリに設定するには、次の手順に従います。

1. [Azure Functions ポータル](https://functions.azure.com/signin) の関数アプリで、**[Function App Settings (関数アプリの設定)]**、**[Go to App Service Settings (App Service の設定に移動)]** の順にクリックします。
   
    ![関数アプリの設定のブレード](./media/functions-bindings-mobile-apps/functions-app-service-settings.png)
2. 関数アプリで、**[すべての設定]** をクリックして **[アプリケーションの設定]** までスクロールし、**[アプリ設定]** で新しい**名前** を環境変数に入力して、URL を **[値]** に貼り付けます。HTTPS スキームが使用できることを確認したら、**[保存]** をクリックして関数アプリのブレードを閉じ、Functions ポータルに戻ります。
   
    ![アプリ設定の環境変数を追加する](./media/functions-bindings-mobile-apps/functions-app-add-app-setting.png)

これで、新しい環境変数を*接続*フィールドとしてバインドに設定できるようになりました。

## <a id="mobiletablesapikey"></a>API キーを使用した Mobile Apps テーブル エンドポイントへの安全なアクセス
Azure Functions のモバイル テーブルのバインドでは、API キーを指定できます。これは、関数以外のアプリからの望ましくないアクセスを回避するために使用できる共有シークレットです。Mobile Apps には、API キー認証向けのサポートが組み込まれていません。ただし、「[Azure App Service Mobile Apps backend implementing an API key (API キーを実装する Azure App Service Mobile Apps バックエンド)](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)」の例に従って、Node.js バックエンド モバイル アプリに API キーを実装できます。同様に、[.NET バックエンドのモバイル アプリ](https://github.com/Azure/azure-mobile-apps-net-server/wiki/Implementing-Application-Key)に API キーを実装することができます。

> [!IMPORTANT]
> この API キーはモバイル アプリ クライアントで配布する必要があり、Azure Functions のようなサービス側のクライアントにのみ安全に配布する必要があります。
> 
> 

## <a id="mobiletablesinput"></a>Azure Mobile Apps の入力バインド
入力バインドでは、モバイル テーブル エンドポイントからレコードを読み込んで、バインドに直接渡すことができます。レコード ID は、関数を呼び出したトリガーに基づいて決定されます。C# 関数で、レコードに加えられた変更は、関数が正常に終了したときに、テーブルに送り返されます。

#### Mobile Apps 入力バインドの function.json
*function.json* ファイルでは、次のプロパティがサポートされます。

* `name`: 新しいレコードの関数コードで使用される変数名。
* `type`: バインドの型は *mobileTable* に設定する必要があります。
* `tableName`: 新しいレコードの作成先のテーブル。
* `id`: 取得するレコードの ID。このプロパティは、`{queueTrigger}` と同様のバインドをサポートします。ここでは、レコード ID としてキュー メッセージの文字列値を使用します。
* `apiKey`: モバイル アプリ用のオプションの API キーを指定するアプリケーション設定である文字列。これは、モバイル アプリが API キーを使用してクライアント アクセスを制限するときに必要です。
* `connection`: モバイル アプリ バックエンドの URL を指定する、アプリケーション設定の環境変数の名前を示す文字列。
* `direction`: バインドの方向。*in* に設定する必要があります。

*function.json* ファイルの例:

    {
      "bindings": [
        {
          "name": "record",
          "type": "mobileTable",
          "tableName": "MyTable",
          "id" : "{queueTrigger}",
          "connection": "My_MobileApp_Url",
          "apiKey": "My_MobileApp_Key",
          "direction": "in"
        }
      ],
      "disabled": false
    }

#### Azure Mobile Apps のコード例 (C# キュー トリガー)
上記の function.json の例に基づいて、入力バインドで、Mobile Apps テーブル エンドポイントからキュー メッセージ文字列と一致する ID を持つレコードを取得し、*record* パラメーターに渡します。レコードが検出されなかった場合、パラメーターは null になります。関数の終了時に、レコードは新しい *Text* 値で更新されます。

    #r "Newtonsoft.Json"    
    using Newtonsoft.Json.Linq;

    public static void Run(string myQueueItem, JObject record)
    {
        if (record != null)
        {
            record["Text"] = "This has changed.";
        }    
    }

#### Azure Mobile Apps のコード例 (Node.js キュー トリガー)
上記の function.json の例に基づいて、入力バインドで、Mobile Apps テーブル エンドポイントからキュー メッセージ文字列と一致する ID を持つレコードを取得し、*record* パラメーターに渡します。Node.js 関数では、更新されたレコードは再びテーブルに送信されません。このコード例では、取得されたレコードをログに書き込みます。

    module.exports = function (context, input) {    
        context.log(context.bindings.record);
        context.done();
    };


## <a id="mobiletablesoutput"></a>Azure Mobile Apps の出力バインド
関数により、出力バインドを使用して、レコードを Mobile Apps テーブル エンドポイントに書き込めます。

#### Mobile Apps 出力バインドの function.json
function.json ファイルは、次のプロパティをサポートします。

* `name`: 新しいレコードの関数コードで使用される変数名。
* `type`: *mobileTable* に設定する必要があるバインドの型。
* `tableName`: 新しいレコードが作成されるテーブル。
* `apiKey`: モバイル アプリ用のオプションの API キーを指定するアプリケーション設定である文字列。これは、モバイル アプリが API キーを使用してクライアント アクセスを制限するときに必要です。
* `connection`: モバイル アプリ バックエンドの URL を指定する、アプリケーション設定の環境変数の名前を示す文字列。
* `direction`: バインドの方向。*out* に設定する必要があります。

function.json の例:

    {
      "bindings": [
        {
          "name": "record",
          "type": "mobileTable",
          "tableName": "MyTable",
          "connection": "My_MobileApp_Url",
          "apiKey": "My_MobileApp_Key",
          "direction": "out"
        }
      ],
      "disabled": false
    }

#### Azure Mobile Apps のコード例 (C# キュー トリガー)
次の C# コードの例では、*Text* プロパティを持つ Mobile Apps テーブル エンドポイントの新しいレコードを、上記のバインドで指定したテーブルに挿入します。

    public static void Run(string myQueueItem, out object record)
    {
        record = new {
            Text = $"I'm running in a C# function! {myQueueItem}"
        };
    }

#### Azure Mobile Apps のコード例 (Node.js キュー トリガー)
次の Node.js コードの例では、*Text* プロパティを持つ Mobile Apps テーブル エンドポイントの新しいレコードを、上記のバインドで指定したテーブルに挿入します。

    module.exports = function (context, input) {

        context.bindings.record = {
            text : "I'm running in a Node function! Data: '" + input + "'"
        }   

        context.done();
    };

## 次のステップ
[!INCLUDE [次のステップ](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0907_2016-->