---
title: Microsoft Translator に接続する - Azure Logic Apps | Microsoft Docs
description: Microsoft Translator REST API と Azure Logic Apps を使用して、テキストを翻訳し、他の言語タスクを実行します
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
ms.openlocfilehash: 29212cf1c1f06ecae9892e0fc8aaaa4a01b2612b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "62105787"
---
# <a name="get-started-with-the-microsoft-translator-connector"></a>Microsoft Translator コネクタの使用
Microsoft Translator に接続して、テキストの翻訳、言語の検出などを行います。 Microsoft Translator では、次の操作を実行できます。 

* Microsoft Translator から取得したデータに基づいてビジネス フローを構築できます。 
* アクションを使用して、テキストの翻訳、言語の検出などを行うことができます。 また、これらのアクションで応答を取得すると、他のアクションから出力を使用できます。 たとえば、Dropbox で新しいファイルが作成されたときに、Microsoft Translator を使用してファイル内のテキストを別の言語に翻訳することができます。

まず、ロジック アプリを作成します。[ロジック アプリの作成](../logic-apps/quickstart-create-first-logic-app-workflow.md)に関する記事を参照してください。

## <a name="create-a-connection-to-microsoft-translator"></a>Microsoft Translator への接続を作成する
> [!INCLUDE [Steps to create a connection to Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]
> 
> 

## <a name="connector-specific-details"></a>コネクタ固有の詳細

[コネクタの詳細](/connectors/microsofttranslator/)に関するページに、Swagger で定義されているトリガーとアクション、さらに制限が記載されています。

## <a name="more-connectors"></a>その他のコネクタ
[API リスト](apis-list.md)に戻ります。