---
title: Logic Apps での Salesforce コネクタの使用方法 | Microsoft Docs
description: Azure App Service を使用してロジック アプリを作成します。 Salesforce コネクタは、Salesforce オブジェクトを操作するための API を提供します。
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 54fe5af8-7d2a-4da8-94e7-15d029e029bf
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/05/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 4278837bb5653b66223374aa728bdc81b279fff7
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38237303"
---
# <a name="get-started-with-the-salesforce-connector"></a>Salesforce コネクタの使用
Salesforce コネクタは、Salesforce オブジェクトを操作するための API を提供します。

[任意のコネクタ](apis-list.md)を使用するには、まずロジック アプリを作成する必要があります。 [ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)から始めることができます。

## <a name="connect-to-salesforce-connector"></a>Salesforce コネクタに接続する
ロジック アプリから任意のサービスにアクセスできるようにするには、まず、そのサービスへの*接続*を作成する必要があります。 [接続](connectors-overview.md)により、ロジック アプリと別のサービスとの接続が実現します。  

### <a name="create-a-connection-to-salesforce-connector"></a>Salesforce コネクタへの接続を作成する
> [!INCLUDE [Steps to create a connection to Salesforce Connector](../../includes/connectors-create-api-salesforce.md)]
> 
> 

## <a name="use-a-salesforce-connector-trigger"></a>Salesforce コネクタ トリガーを使用する
トリガーとは、ロジック アプリで定義されたワークフローの開始に使用できるイベントです。 トリガーの詳細については[こちら](../logic-apps/logic-apps-overview.md#logic-app-concepts)を参照してください。

> [!INCLUDE [Steps to create a Salesforce trigger](../../includes/connectors-create-api-salesforce-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>条件を追加する
> [!INCLUDE [Steps to create a Salesforce condition](../../includes/connectors-create-api-salesforce-condition.md)]
> 
> 

## <a name="use-a-salesforce-connector-action"></a>Salesforce コネクタ アクションを使用する
アクションとは、ロジック アプリで定義されたワークフローによって実行される操作です。 アクションの詳細については[こちら](../logic-apps/logic-apps-overview.md#logic-app-concepts)を参照してください。

> [!INCLUDE [Steps to create a Salesforce action](../../includes/connectors-create-api-salesforce-action.md)]
> 
> 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/salesforce/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。 

## <a name="next-steps"></a>次の手順
[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)

