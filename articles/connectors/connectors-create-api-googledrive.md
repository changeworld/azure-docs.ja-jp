---
title: Google Drive への接続
description: Azure Logic Apps を使用して Google ドライブ用のファイルを作成および管理するワークフローを自動化する
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 155a579438747ca7de67eba6449b8a0191e86e2c
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666841"
---
# <a name="create-and-manage-files-for-google-drive-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して Google ドライブ用のファイルを作成および管理する

Google Drive に接続して、ファイルの作成、行の取得などを行います。 Google Drive では、次の操作を実行できます。 

* 検索から取得したデータに基づいてビジネス フローを構築することができます。 
* 画像、ニュースなどを検索するアクションを使用できます。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 たとえば、動画を検索し、Twitter を使用して Twitter フィードに動画を投稿することができます。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="create-the-connection-to-google-drive"></a>Google Drive への接続を作成する

ロジック アプリにこのコネクタを追加するとき、Google Drive に接続するロジック アプリを承認する必要があります。

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]

接続を作成したら、フォルダー パスやファイル名など、Google Drive のプロパティを入力します。 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

コネクタの Swagger の説明に記載されているトリガー、アクション、および制限に関する技術的な詳細については、[コネクタのリファレンス ページ](/connectors/googledrive/)を確認してください。

## <a name="next-steps"></a>次のステップ

* 他の[Logic Apps コネクタ](apis-list.md)を確認します。
