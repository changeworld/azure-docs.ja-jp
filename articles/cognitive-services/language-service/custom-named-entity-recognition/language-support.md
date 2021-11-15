---
title: カスタム固有表現認識 (NER) の言語とリージョンのサポート
titleSuffix: Azure Cognitive Services
description: カスタム固有表現認識 (NER) でサポートされている言語とリージョンについて説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: references_regions, language-service-custom-ner, ignite-fall-2021
ms.author: aahi
ms.openlocfilehash: 996a04ae69e8e511a8dd74b5cad82b76eb1c6d8d
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131502580"
---
# <a name="language-support-for-custom-named-entity-recognition-ner"></a>カスタム固有表現認識 (NER) の言語のサポート

この記事では、カスタム固有表現認識 (NER) で現在サポートされている言語とリージョンについて説明します。

## <a name="multiple-language-support"></a>複数言語のサポート

カスタム NER を使用すると、1 つの言語でモデルをトレーニングし、別の言語でテストできます。 この機能は、時間と労力を節約するのに役立つため非常に強力です。すべての言語に対して個別のプロジェクトを構築する代わりに、1 つのプロジェクトで多言語データセットを処理することができます。 データセットは完全に同じ言語である必要はありませんが、プロジェクト作成時にこのオプションを指定する必要があります。 評価プロセス中に特定の言語でモデルのパフォーマンスが低い場合は、その言語のデータをトレーニング セットに追加してください。

> [!NOTE]
> 複数の言語のサポートを有効にするには、[プロジェクトを作成する](how-to/create-project.md)ときにこのオプションを有効にする必要があります。または、プロジェクト設定ページから後で有効にすることができます。

## <a name="language-support"></a>言語のサポート

カスタム NER では、次の言語の `.txt` ファイルがサポートされます。

| Language | Locale |  
|--|--|
| 英語 (米国) |`en-US` |
| フランス語 (フランス) |`fr-FR` |
| ドイツ語 |`de-DE` |
| イタリア語 |`it-IT` |
| スペイン語 (スペイン) |`es-ES` |
| ポルトガル語 (ポルトガル) | `pt-PT` |
| ポルトガル語 (ブラジル) | `pt-BR` |

## <a name="next-steps"></a>次のステップ

[カスタム NER の概要](overview.md)
