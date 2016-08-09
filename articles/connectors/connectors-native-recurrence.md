<properties
	pageTitle="ロジック アプリに定期実行のトリガーを追加する |Microsoft Azure"
	description="定期実行のトリガーの概要と Azure ロジック アプリでの使用方法。"
	services=""
	documentationCenter="" 
	authors="jeffhollan"
	manager="erikre"
	editor=""
	tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# 定期実行のトリガーの概要

定期実行のトリガーを使用すると、クラウドに次のような強力なワークフローを作成できます。

- SQL ストアド プロシージャを毎日実行するようにワークフローをスケジュールします。
- 特定のハッシュタグに関する 1 週間以内のすべてのツイートの概要を電子メールで送信します。

ロジック アプリでの定期実行のトリガーの使用を開始するには、[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関する記事を参照してください。

---

## 定期実行のトリガーの使用

トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。[トリガーの詳細についてはこちらを参照してください](connectors-overview.md)。

ロジック アプリで定期実行のトリガーをセットアップする方法の例を次に示します。

1. ロジック アプリの最初の手順として、**定期実行**のトリガーを追加します。
1. 繰り返し間隔のパラメーターを入力します。
1. これで、ロジック アプリは、時間間隔ごとに実行が開始されます。

![HTTP トリガー](./media/connectors-native-recurrence/using-trigger.png)

---

## 技術的な詳細

定期実行のトリガーと構成可能なパラメーターの詳細を次に示します。

### トリガーの詳細

定期実行のトリガーには、構成可能な次のプロパティがあります。

#### 繰り返し
指定された期間の後にロジック アプリを起動します。* は必須フィールドを意味します。

|表示名|プロパティ名|説明|
|---|---|---|
|頻度*|frequency|時間の単位 (`Second`、`Minute`、`Hour`、`Day`、または `Year`)|
|間隔*|interval|指定した頻度で繰り返し実行する間隔|
|タイム ゾーン|timeZone|startTime が UTC オフセットなしで指定されている場合、この timeZone が使用されます|
|開始時刻|startTime|開始時刻は [ISO 8601 形式](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations)です。startTime が UTC オフセットなしで指定されている場合、この timeZone が使用されます|
<br>


## 次のステップ

ロジック アプリに取り組む方法とコミュニティの詳細を次に示します。

## ロジック アプリを作成します

ここで、プラットフォームと、[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)を試してみましょう。[API の一覧](apis-list.md)を参照すると、ロジック アプリで使用可能な他のコネクタが見つかります。

<!---HONumber=AcomDC_0727_2016-->