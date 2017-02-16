---
title: "Azure Stream Analytics JavaScript ユーザー定義関数 | Microsoft Docs"
description: "IoT センサー タグと、Stream Analytics によるデータ ストリームとリアルタイムのデータ処理"
keywords: "IoT ソリューション, IoT の概要, ツール"
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 822f0870396e1fa5fe2dfee3bae410aad3da1122
ms.openlocfilehash: 959fd48cb7d7f21385f6eea21f79ad26e8164bc0

---

# <a name="azure-stream-analytics-javascript-user-defined-functions"></a>Azure Stream Analytics JavaScript ユーザー定義関数
Azure Stream Analytics は、JavaScript で記述されたユーザー定義関数 (UDF) をサポートします。 JavaScript が提供する一連の豊富な String、RegExp、Math、Array、Date メソッドによって、Stream Analytics ジョブを伴う複雑なデータ変換の作成が容易になります。

## <a name="overview"></a>概要
JavaScript UDF は、外部との接続を必要としない、ステートレスの計算のみのスカラー関数をサポートします。 関数の戻り値には、スカラー (単一) 値のみを指定できます。 関数を&1; 度ジョブに追加すると、組み込みスカラー関数のようにクエリーのどの位置でも使用することができます。

ここでは、JavaScript UDF が役立つシナリオの例を示します。
* 正規表現関数を用いた文字列の解析と操作 (例: Regexp_Replace()、Regexp_Extract())
* エンコード データのデコード (例: バイナリから&16; 進数への変換)
* JavaScript の Math 関数を用いた数値計算
* 並び替え、結合、検索、塗りつぶしなどの配列操作

Stream Analytics の JavaScript UDF では実行できないことを次に示します。
* 外部の REST エンドポイントの呼び出し (IP の逆引き参照や、外部ソースからの参照データのプル)
* 入力や出力におけるカスタム イベントフォーマットのシリアル化または逆シリアル化
* カスタム集計

関数定義において禁止されているわけではありませんが、Date.GetDate() や Math.random() などの関数の使用は避ける必要があります。 これらの関数では呼び出すたびに同じ結果が **返らず**、Azure Stream Analytics サービスは関数呼び出しや戻り値のジャーナルを保持しません。 したがって、関数が同じイベントで異なる結果を返す場合、ユーザーや Stream Analytics サービスによってジョブが再起動された際の再現性は保証されません。

## <a name="adding-a-javascript-udf-from-azure-portal"></a>Azure Portal から JavaScript UDF を追加する
既存の Stream Analytics ジョブの下で単一の JavaScript ユーザー定義関数を作成するには、次の手順に従います。

1.  Azure Portal を開きます。

2.  Stream Analytics ジョブを検索し、**[ジョブ トポロジ]** の下の関数リンクをクリックします。
 
3.  既存の関数の空のリストが表示されたら、**[追加]**アイコンをクリックして、新しい UDF を追加します。

4.  **[新しい関数]** ブレードで関数の種類に JavaScript を選択すると、既定の関数テンプレートがエディターに表示されます。
 
5.  UDF エイリアスを _hex2Int_ として、関数の実装を次のように変更します。

    ```
    // Convert Hex value to integer.
    function main(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  **[保存]** ボタンをクリックすると、関数リストに関数が表示されます。 

7.  新しい関数の **hex2Int** をクリックすると、関数の定義を確認できます。 すべての関数には、関数のエイリアスの前にプレフィックス “UDF” が追加されていることに注意してください。 Stream Analytics クエリでは、**プレフィックスを用いて**関数を呼び出す必要があります (この場合、**UDF.hex2Int**となります)。
 
## <a name="calling-javascript-udf-in-a-query"></a>クエリで JavaScript UDF を呼び出す

1. **[ジョブ トポロジ]** の下の **[クエリ]** をクリックして、クエリ エディターを開きます。 

2.  次に示すように、クエリを編集して、追加した UDF を呼び出します。

    ```
    SELECT 
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  ジョブ入力を右クリックして、サンプル データ ファイルをアップロードする 
 
4.  **[テスト]** をクリックして、クエリをテストします。


## <a name="supported-javascript-objects"></a>サポートされている JavaScript オブジェクト
Azure Stream Analytics の JavaScript UDF は、JavaScript 言語のビルトイン オブジェクトをサポートしています。 オブジェクトの一覧については、[グローバル オブジェクト](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects)に関するページを参照してください。

### <a name="stream-analytics-and-javascript-type-conversion"></a>Stream Analytics と JavaScript の型変換

Stream Analytics クエリ言語と JavaScript でサポートされる型の間には違いがあります。 次の表は、2 つの型の変換マッピングの一覧です。


---
Stream Analytics | JavaScript
--- | ---
bigint | Number (JavaScript では最大 2^53 の精度の整数しか表現できません)
DateTime | Date (JavaScript ではミリ秒のみサポートされています) 
double | Number
nvarchar(MAX) | String
レコード | オブジェクト
array | array
NULL | Null


JavaScript から ASA への変換も掲載されています。

---
JavaScript | Stream Analytics
--- | ---
Number | 数値が四捨五入され、long.MinValue と long.MaxValue の間の場合は Bigint、それ以外は double
日付 | DateTime
String | nvarchar(MAX)
オブジェクト | レコード
array | array
Null、未定義 | NULL
それ以外の型 (例: Function、Error など) | サポートされていません - 実行時エラー

## <a name="troubleshooting"></a>トラブルシューティング
JavaScript ランタイム エラーは致命的とみなされ、アクティビティ ログに表示されます。 Azure Portal からログを取得するには、ジョブに移動し、**[アクティビティ ログ]** をクリックします。
 

## <a name="other-javascript-udf-usage-patterns"></a>その他の JavaScript UDF の使用パターン

### <a name="writing-nested-json-to-output"></a>入れ子になった JSON を記述して出力する
Stream Analytics ジョブ出力を入力として受け取り、その入力が JSON フォーマットを必要とするフォローアップ処理手順の場合、JSON 文字列を記述して出力するタスクが一般的です。 次の例では JSON.stringify() 関数を呼び出し、入力のすべての名前/値のペアをまとめ、単一の文字列として出力します。 

### <a name="javascript-udf-definition"></a>JavaScript UDF の定義:

```
function main(x) {
return JSON.stringify(x);
}
```

**サンプル クエリ:**
```
SELECT 
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--HONumber=Feb17_HO1-->


