---
title: Google Drive に接続する - Azure Logic Apps | Microsoft Docs
description: Google Drive REST API と Azure Logic Apps を使用して、ファイルを作成および管理します
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 4994687afbdd23f0265138cd1d4eff53bb47c163
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295885"
---
# <a name="get-started-with-the-google-drive-connector"></a>Google Drive コネクタの使用
Google Drive に接続して、ファイルの作成、行の取得などを行います。 Google Drive では、次の操作を実行できます。 

* 検索から取得したデータに基づいてビジネス フローを構築することができます。 
* 画像、ニュースなどを検索するアクションを使用できます。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 たとえば、動画を検索し、Twitter を使用して Twitter フィードに動画を投稿することができます。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="create-the-connection-to-google-drive"></a>Google Drive への接続を作成する
ロジック アプリにこのコネクタを追加するとき、Google Drive に接続するロジック アプリを承認する必要があります。

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]
> 
> 

接続を作成したら、フォルダー パスやファイル名など、Google Drive のプロパティを入力します。 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/googledrive/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。
