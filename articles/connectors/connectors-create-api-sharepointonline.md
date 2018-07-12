---
title: ロジック アプリで SharePoint Online コネクタを使用する方法 | Microsoft Docs
description: SharePoint Online コネクタを使用するロジック アプリを作成して、SharePoint でリストを管理します。
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: e0ec3149-507a-409d-8e7b-d5fbded006ce
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/19/2016
ms.author: estfan; ladocs
ms.openlocfilehash: be104080e1056fa2181e54919d014154d16a60a4
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38237238"
---
# <a name="get-started-with-the-sharepoint-online-connector"></a>SharePoint Online コネクタの概要
SharePoint Online コネクタを使用すると、SharePoint リストを管理できます。  

[任意のコネクタ](apis-list.md)を使用するには、まずロジック アプリを作成する必要があります。 [ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)から始めることができます。

## <a name="connect-to-sharepoint-online"></a>SharePoint Online への接続
ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの*接続*を作成する必要があります。 [接続](connectors-overview.md)により、ロジック アプリと別のサービスとの接続が実現します。  

### <a name="create-a-connection-to-sharepoint-online"></a>SharePoint Online への接続を作成する
> [!INCLUDE [Steps to create a connection to SharePoint](../../includes/connectors-create-api-sharepointonline.md)]


## <a name="use-a-sharepoint-online-trigger"></a>SharePoint Online トリガーの使用
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 トリガーの詳細については[こちら](../logic-apps/logic-apps-overview.md#logic-app-concepts)を参照してください。  

> [!INCLUDE [Steps to create a SharePoint Online trigger](../../includes/connectors-create-api-sharepointonline-trigger.md)]


## <a name="use-a-sharepoint-online-action"></a>SharePoint Online アクションの使用
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 アクションの詳細については[こちら](../logic-apps/logic-apps-overview.md#logic-app-concepts)を参照してください。  

> [!INCLUDE [Steps to create a SharePoint Online action](../../includes/connectors-create-api-sharepointonline-action.md)]


## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/sharepoint/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="next-steps"></a>次の手順
[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)

