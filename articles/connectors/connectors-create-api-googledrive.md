---
title: Google Drive への接続
description: Google Drive REST API と Azure Logic Apps を使用して、ファイルを作成および管理します
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 6310c3b7e5b84915fa336708bc702e94317ad04c
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789698"
---
# <a name="get-started-with-the-google-drive-connector"></a>Google Drive コネクタの使用

Google Drive に接続して、ファイルの作成、行の取得などを行います。 Google Drive では、次の操作を実行できます。 

* 検索から取得したデータに基づいてビジネス フローを構築することができます。 
* 画像、ニュースなどを検索するアクションを使用できます。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 たとえば、動画を検索し、Twitter を使用して Twitter フィードに動画を投稿することができます。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="create-the-connection-to-google-drive"></a>Google Drive への接続を作成する

ロジック アプリにこのコネクタを追加するとき、Google Drive に接続するロジック アプリを承認する必要があります。

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]

接続を作成したら、フォルダー パスやファイル名など、Google Drive のプロパティを入力します。 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/googledrive/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ

[API リスト](apis-list.md)に戻ります。
