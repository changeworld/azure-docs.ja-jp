---
title: Language Understanding (LUIS) の作成済みエンティティ
titleSuffix: Azure Cognitive Services
description: LUIS には、日付、時刻、数字、測定値、通貨など、一般的な情報の種類を認識するための作成済みエンティティのセットが含まれています。 作成済みエンティティのサポートは、LUIS アプリのカルチャによって異なります。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: 2f7c724b14efd569a5993f9a9319c9004874bc43
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647597"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>一般的なデータ型を認識するための作成済みエンティティ

LUIS には、日付、時刻、数字、測定値、通貨など、一般的な情報の種類を認識するための作成済みエンティティのセットが含まれています。 

## <a name="add-a-prebuilt-entity"></a>作成済みエンティティの追加

1. **[マイ アプリ]** ページで名前をクリックしてアプリを開き、左側で **[エンティティ]** をクリックします。 

1. **[エンティティ]** ページで **[Manage prebuilt entities]\(作成済みエンティティの管理\)** をクリックします。

1. **[Add prebuilt entities]\(事前構築済みエンティティの追加\)** ダイアログ ボックスで、datetimeV2 事前構築済みエンティティを選択します。 

    ![[Add prebuilt entities]\(作成済みエンティティの追加\) ダイアログ ボックス](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. **[完了]** を選択します。

## <a name="publish-the-app"></a>アプリの発行

事前構築済みエンティティの値を見る最も簡単な方法は、公開されているエンドポイントに対してクエリを行うことです。 

1. 上部ツール バーの **[発行]** を選択します。 **[運用]** に発行します。 

1. 緑の成功通知が表示されたら、**[Refer to the list of endpoints]\(エンドポイントの一覧を参照する\)** リンクを選択して、エンドポイントを表示します。

1. エンドポイントを選択します。 新しいブラウザー タブでそのエンドポイントが開きます。 ブラウザー タブを開いたままにして、**[テスト]** セクションに移動します。

## <a name="test"></a>テスト
エンティティを追加した後、アプリをトレーニングする必要はありません。 

**q** パラメーターに値を追加して、エンドポイントで新しい意図をテストします。 **q** に対する推奨される発話の次の表を使用します。

|テスト発話|エンティティの値|
|--|:--|
|明日の便を予約する|2018-10-19|
|3 月 3 日の予定をキャンセルする|発話で年が指定されていないため、LUIS は過去で直近の 3 月 3 日 (2018-03-03) と未来で直近の 3 月 3日 (2019-03-03) を返しました。|
|午前 10 時に会議をスケジュールする|10:00:00|


## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [事前構築済みのエンティティのリファレンス](./luis-reference-prebuilt-entities.md)
