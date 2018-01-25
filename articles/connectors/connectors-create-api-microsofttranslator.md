---
title: "ロジック アプリに Microsoft Translator を追加する | Microsoft Docs"
description: "Microsoft Translator コネクタと REST API パラメーターの概要"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: da782baf-8bf8-4973-8238-e469865f5328
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia; ladocs
ms.openlocfilehash: 257f03f8ac71e2c88856864a6b1ff055ab08c347
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2018
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