<properties
	pageTitle="ロジック アプリに遅延を追加する |Microsoft Azure"
	description="遅延アクションとこの時間まで遅延アクションの概要と Azure ロジック アプリでの使用方法。"
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

# 遅延アクションとこの時間まで遅延アクションの概要

遅延アクションとこの時間まで遅延アクションを使用すると、次のようなワークフロー シナリオを完了できます。

- 電子メールで状態の更新情報を送信するのを平日まで待つ
- HTTP 呼び出しが完了するまでワークフローを遅らせてから、再開して結果を取得する

ロジック アプリでの遅延アクションの使用を開始するには、[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)に関する記事を参照してください。

---

## 遅延アクションの使用

アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。[アクションの詳細についてはこちらを参照してください](connectors-overview.md)。

ロジック アプリで遅延ステップを使用する方法の例を次に示します。

1. トリガーを追加したら、**[New Step (新しいステップ)]** をクリックしてアクションを追加します。
1. 「遅延」を検索して、遅延アクションを表示します。この例では、**[遅延]** を選択します。

	![Delay actions](./media/connectors-native-delay/using-action-1.png)

1. 遅延を構成するアクション プロパティをすべて設定します。

	![Delay config](./media/connectors-native-delay/using-action-2.png)

1. [保存] をクリックして、ロジック アプリを発行してアクティブ化します。

---

## 技術的な詳細

遅延アクションとこの時間まで遅延アクションの詳細を次に示します。

### アクションの詳細

定期実行のトリガーには、構成可能な次のプロパティがあります。

#### 遅延

一定期間、実行を遅延します。* は必須フィールドを意味します。

|表示名|プロパティ名|説明|
|---|---|---|
|カウント*|count|遅延する時間の単位数|
|単位*|unit|時間の単位 (`Second`、`Minute`、`Hour`、または `Day`)|
<br>

#### この時間まで遅延

指定した日付/時刻まで実行を遅延します。* は必須フィールドを意味します。

|表示名|プロパティ名|説明|
|---|---|---|
|年*|timestamp|遅延後の年 (GMT)|
|月*|timestamp|遅延後の月 (GMT)|
|日*|timestamp|遅延後の日 (GMT)|
<br>


## 次のステップ

ロジック アプリに取り組む方法とコミュニティの詳細を次に示します。

## ロジック アプリを作成します

ここで、プラットフォームと、[ロジック アプリの作成](../app-service-logic/app-service-logic-create-a-logic-app.md)を試してみましょう。[API の一覧](apis-list.md)を参照すると、ロジック アプリで使用可能な他のコネクタが見つかります。

<!---HONumber=AcomDC_0727_2016-->