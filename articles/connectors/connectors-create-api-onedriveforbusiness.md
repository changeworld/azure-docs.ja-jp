---
title: OneDrive for Business | Microsoft Docs
description: Azure App Service を使用してロジック アプリを作成します。 OneDrive for Business に接続してファイルを管理します。 ファイルのアップロード、更新、取得、削除など、多様なアクションを実行できます。
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: cf9484e9-7a20-4de0-93c8-0fa132221f2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: a572d3d858bcaed817198420db7f2e09732b2366
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-the-onedrive-for-business-connector"></a>OneDrive for Business コネクタの使用
OneDrive for Business に接続してファイルを管理します。 ファイルのアップロード、更新、取得、削除など、多様なアクションを実行できます。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="create-a-connection-to-onedrive-for-business"></a>OneDrive for Business への接続の作成
OneDrive for Business を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。

| プロパティ | 必須 | [説明] |
| --- | --- | --- |
| トークン |[はい] |OneDrive for Business の資格情報を提供します |

接続を作成したら、その接続を使用してアクションを実行し、この記事で説明するトリガーをリッスンできます。

> [!INCLUDE [Steps to create a connection to OneDrive for Business](../../includes/connectors-create-api-onedriveforbusiness.md)]
> 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/onedriveforbusinessconnector/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。