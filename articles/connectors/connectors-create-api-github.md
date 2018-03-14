---
title: "Azure Logic Apps を使用して GitHub に接続する | Microsoft Docs"
description: "Azure Logic Apps を使用して GitHub のワークフローを自動化します"
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 03/02/2018
ms.author: mandia; ladocs
ms.openlocfilehash: cd7cd3babbfb7efc5917d3a7ec5b9d10112ba791
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2018
---
# <a name="connect-to-github"></a>GitHub に接続する

GitHub は、Git のすべての分散リビジョン コントロールとソース コード管理 (SCM) 機能、およびその他の機能を提供する、Web ベースの Git リポジトリ ホスティング サービスです。

GitHub コネクタの使用を開始するには、[最初にロジック アプリを作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)します。

## <a name="create-a-connection-to-github"></a>GitHub への接続を作成する

ロジック アプリで GitHub コネクタを使用するには、最初に "*接続*" を作成してから、以下のプロパティの詳細を指定します。 

| プロパティ | 必須 | [説明] | 
| -------- | -------- | ----------- | 
| トークン | [はい] | GitHub の資格情報を指定します。 |

接続を作成したら、アクションを実行して、この記事で説明するトリガーをリッスンできます。

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

Swagger で定義されているトリガーとアクション、および制限事項については、[コネクタの詳細](/connectors/github/)に関するページをご覧ください。

## <a name="find-more-connectors"></a>その他のコネクタ

* [コネクタの一覧](apis-list.md)をご覧ください。