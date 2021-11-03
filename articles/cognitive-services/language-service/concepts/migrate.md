---
title: LUIS、QnA Maker、Text Analytics から Azure Cognitive Service for Language に移行する
titleSuffix: Azure Cognitive Services
description: LUIS、QnA Maker、Text Analytics からアプリケーションを移行する必要がある場合は、この記事を使用して学習してください。
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1e2a2945700e0537b7ae4b220f97eafb1ce19a69
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089804"
---
# <a name="migrating-to-azure-cognitive-service-for-language"></a>Azure Cognitive Service for Language への移行

2021 年 11 月 2 日に、Azure Cognitive Service for Language がパブリック プレビューにリリースされました。 この言語サービスでは、Text Analytics、QnA Maker、LUIS のサービス内容が統合され、いくつかの新機能も提供されます。 

## <a name="do-i-need-to-migrate-to-the-language-service-if-i-am-using-text-analytics"></a>Text Analytics を使用している場合、言語サービスに移行する必要はありますか?

Text Analytics は言語サービスに組み込まれたので、その機能は引き続き使用できます。 Text Analytics を使用していた場合、アプリケーションは破壊的変更なしで引き続き動作し続けるはずです。 古いアプリケーションを更新する必要がある場合は、[Text Analytics v2 移行ガイド](migrate-from-text-analytics-v2.md)も参照してください。 

利用可能なクイックスタート記事のいずれかを使用して、サービス エンドポイントと API 呼び出しに関する最新情報を確認してください。 

## <a name="how-do-i-migrate-to-the-language-service-if-i-am-using-luis"></a>LUIS を使用している場合は、どのようにして言語サービスに移行しますか?

Language Understanding (LUIS) を使用している場合、新しい Conversational Language Understanding 機能に [LUIS JSON ファイルをインポート](../conversational-language-understanding/concepts/backwards-compatibility.md)できます。 

## <a name="how-do-i-migrate-to-the-language-service-if-i-am-using-qna-maker"></a>QnA Maker を使用している場合は、どのようにして言語サービスに移行しますか?

QnA Maker を使用している場合は、[移行ガイド](../question-answering/how-to/migrate-qnamaker.md) を参照して、QnA Maker から質問応答へのナレッジ ベースの移行に関する情報を確認してください。

## <a name="see-also"></a>関連項目

* [Azure Cognitive Service for Language の概要](../overview.md)
* [Conversational Language Understanding の概要](../conversational-language-understanding/overview.md)
* [質問応答の概要](../question-answering/overview.md)
