---
title: LUIS を使用して新しいアプリを作成する | Microsoft Docs
description: Language Understanding (LUIS) Web ページで、アプリケーションを作成し、管理します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 04/17/2018
ms.author: diberry
ms.openlocfilehash: 3adeecd4a4e2040a92689b7c92be9630c9a0d93b
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225416"
---
# <a name="create-an-app"></a>アプリを作成する
新しいアプリはさまざまな方法で作成します。 

* 空のアプリから[始め](#create-new-app)て、意図、発話、およびエンティティを作成する
* 空のアプリから[始め](#create-new-app)で、[事前構築済みのドメイン](luis-how-to-use-prebuilt-domains.md)を追加する
* 意図、発話、およびエンティティが既に格納されている JSON ファイルから [LUIS アプリをインポート](#import-new-app)する

## <a name="what-is-an-app"></a>アプリとは
アプリには、モデルの[バージョン](luis-how-to-manage-versions.md)とアプリの[コラボレーター](luis-how-to-collaborate.md)が含まれます。 アプリケーションを作成するときにカルチャ ([言語](luis-supported-languages.md)) を選択します。これは**後で変更できません**。 

新しいアプリの既定のバージョンは "0.1" です。 

アプリケーションは、**[マイ アプリ]** ページで作成および管理できます。 このページには、[LUIS](luis-reference-regions.md) Web サイトの上部にあるナビゲーション バーで **[マイ アプリ]** を選択するといつでもアクセスできます。 

[![](media/luis-create-new-app/apps-list.png "アプリの一覧のスクリーンショット")](media/luis-create-new-app/apps-list.png#lightbox)

## <a name="create-new-app"></a>新しいアプリの作成

1. **[マイ アプリ]** ページで、**[新しいアプリの作成]** を選択します。
2. ダイアログ ボックスで、アプリケーションに "TravelAgent" という名前を付けます。

    ![[新しいアプリの作成] ダイアログ ボックス](./media/luis-create-new-app/create-app.png)

3. アプリケーションのカルチャを選択 (TravelAgent アプリの場合は、英語を選択) し、**[完了]** を選択します。 

    >[!NOTE]
    >カルチャは、アプリケーションを作成した後に変更できません。 

## <a name="import-new-app"></a>新しいアプリのインポート
JSON ファイルに、アプリの名前 (最大 50 文字)、バージョン (最大 10 文字)、および説明を設定できます。 アプリケーション JSON ファイルの例については、[LUIS のサンプル](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/Examples-BookFlight)を入手できます。

1. **[マイ アプリ]** ページで、**[Import new app]\(新しいアプリのインポート\)** を選択します。
2. **[Import new app]\(新しいアプリのインポート\)** ダイアログ ボックスで、LUIS アプリを定義している JSON ファイルを選択します。

    ![[Import new app]\(新しいアプリのインポート\) ダイアログ ボックス](./media/luis-create-new-app/import-app.png)

## <a name="export-app"></a>アプリのエクスポート
1. **[マイ アプリ]** ページで、アプリの行の末尾にある省略記号 (***...***) を選択します。

    [![](media/luis-create-new-app/apps-list.png "アプリのアクションごとのポップアップ ダイアログのスクリーン ショット")](media/luis-create-new-app/three-dots.png#lightbox)

2. メニューで **[Export app]\(アプリのエクスポート\)** を選択します。 

## <a name="rename-app"></a>アプリの名前の変更

1. **[マイ アプリ]** ページで、アプリの行の末尾にある省略記号 (***...***) を選択します。 
2. メニューで **[名前の変更]** を選択します。
3. アプリの新しい名前を入力し、**[完了]** を選択します。

## <a name="delete-app"></a>アプリの削除

> [!CAUTION]
> すべてのコラボレーターと所有者について、アプリを削除することになります。 削除する前に、アプリを[エクスポート](#export-app)してください。 

1. **[マイ アプリ]** ページで、アプリの行の末尾にある省略記号 (***...***) を選択します。 
2. メニューで **[削除]** を選択します。
3. 確認ウィンドウで **[OK]** を選択します。

## <a name="export-endpoint-logs"></a>エンドポイント ログのエクスポート
ログには、クエリ、UTC 時刻、および LUIS JSON 応答が含まれます。

1. **[マイ アプリ]** ページで、アプリの行の末尾にある省略記号 (***...***) を選択します。 
2. メニューで **[Export endpoint logs]\(エンドポイント ログのエクスポート\)** を選択します。

```
Query,UTC DateTime,Response
text i'm driving and will be 30 minutes late to the meeting,02/13/2018 15:18:43,"{""query"":""text I'm driving and will be 30 minutes late to the meeting"",""intents"":[{""intent"":""None"",""score"":0.111048922},{""intent"":""SendMessage"",""score"":0.987501}],""entities"":[{""entity"":""i ' m driving and will be 30 minutes late to the meeting"",""type"":""Message"",""startIndex"":5,""endIndex"":58,""score"":0.162995353}]}"
```

## <a name="next-steps"></a>次の手順

アプリでの最初のタスクは、[意図の追加](luis-how-to-add-intents.md)です。