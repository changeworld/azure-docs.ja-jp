---
title: Dynamics 365 に接続する
description: Azure Logic Apps を使用して Dynamics 365 のレコードを作成および管理する
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 05/09/2020
tags: connectors
ms.openlocfilehash: 00bf8ea2b783e09711a95f203bdfcce0e6b90b2c
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994294"
---
# <a name="create-and-manage-records-in-dynamics-365-by-using-azure-logic-apps"></a>Azure Logic Apps を使用して Dynamics 365 のレコードを作成および管理する

Dynamics 365 では、[Common Data Service](https://docs.microsoft.com/powerapps/maker/common-data-service/data-platform-intro) を使用します。 Dynamics 365 への接続には、[Common Data Service コネクタ](https://docs.microsoft.com/connectors/commondataservice/)を使用します。

> [!IMPORTANT]
> [Dynamics 365 コネクタ](https://docs.microsoft.com/connectors/dynamicscrmonline/)は非推奨ですが、削除されるまで動作を続行します。 新しいロジック アプリには Dynamics 365 コネクタを使用しないでください。 Dynamics 365 コネクタを削除するためのタイムラインは、まだ発表されていません。 既存のロジック アプリを Common Data Service コネクタ、またはその他の計画された新しいコネクタに変換する必要はありませんが、コネクタが削除されるときは、ロジック アプリを変換する必要があります。 詳細については、[Dynamics 365 コネクタが非推奨になった](https://docs.microsoft.com/power-platform/important-changes-coming)ことに関する記事を参照してください。
>
> [Common Data Service コネクタ](https://docs.microsoft.com/connectors/commondataservice/)が提供するのは、非推奨の Dynamics 365 コネクタと同じ機能ですが、信頼性が向上する機能強化が含まれています。 ロジック アプリでの Common Data Service コネクタの使用の詳細については、[Azure Logic Apps を使用した Dynamics 365 のレコードの作成と管理](../connectors/connect-common-data-service.md)に関する記事を参照してください。

Common Data Service の詳細については、次のトピックを参照してください。

* [Learn: Common Data Service の開始方法](https://docs.microsoft.com/learn/modules/get-started-with-powerapps-common-data-service/)
* [Learn: Power Platform と Common Data Service を使用した Dynamics 365 データの接続および分析](https://docs.microsoft.com/learn/wwl/connect-analyze-dynamics-365-data/)