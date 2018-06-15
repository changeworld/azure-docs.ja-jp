---
title: Azure Logic Apps での Wunderlist コネクタ | Microsoft Docs
description: Wunderlist への接続を作成し、この接続を使用してロジック アプリでワークフローを構築します。
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 1ed9b19700157abca6e5ac4265f1e8c99a3d846d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296375"
---
# <a name="get-started-with-the-wunderlist-connector"></a>Wunderlist コネクタの使用
Wunderlist は ToDo リストおよびタスク マネージャーとしてユーザーのタスクの実行を支援します。  買い物メモを共有したり、プロジェクトで作業したり、休暇の計画を立てたりするときも、Wunderlist を使えばリスト項目を簡単に確認、共有、完了できます。 Wunderlist は携帯電話、タブレット、コンピューター間で即時に同期するため、すべてのタスクにどこからでもアクセスできます。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="create-a-connection-to-wunderlist"></a>Wunderlist への接続を作成する
Wunderlist を使用してロジック アプリを作成するには、まず**接続**を作成してから、次のプロパティの詳細を指定する必要があります。

| プロパティ | 必須 | 説明 |
| --- | --- | --- |
| トークン |[はい] |Wunderlist の資格情報を提供します |

接続を作成したら、その接続を使用してアクションを実行し、トリガーをリッスンできます。

> [!INCLUDE [Steps to create a connection to Wunderlist](../../includes/connectors-create-api-wunderlist.md)]
> 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/wunderlist/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。