---
title: 発音をカスタマイズする
description: 音声テキスト変換で音素を使用して単語の発音をカスタマイズします。
author: ut-karsh
ms.author: umaheshwari
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/19/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 6b8f2339668656ec067bae234d21ec4bf2d5741c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017555"
---
# <a name="customize-pronunciation"></a>発音をカスタマイズする

Custom Speech では、汎用音素セットを使用して、特定の単語に異なる発音を指定できます。 汎用音素セット (UPS) とは、国際音標文字 (IPA) に基づく、コンピューターで読み取り可能な音素セットです。 IPA は、世界中の言語学者によって使用され、標準として承認されています。

UPS の発音は、それぞれが空白で区切られた UPS 音素の文字列で構成されます。 音素セットでは大文字と小文字が区別されます。 UPS 音素ラベルはすべて ASCII 文字列を使用して定義されます。

UPS を実装する手順については、[音素セットのトレーニング用の構造化テキスト データ](how-to-custom-speech-test-and-train.md#structured-text-data-for-training-public-preview)に関する記事を参照してください

この構造化テキスト データは、[発音ファイル](how-to-custom-speech-test-and-train.md#pronunciation-data-for-training)とは異なり、同時に使用することはできません。

## <a name="languages-supported"></a>サポートされている言語

次の表を使用して、それぞれの言語の UPS に移動します。

| Language                | Locale  |
|-------------------------|---------|
| [英語 (米国)](phone-sets.md) | `en-US` |


## <a name="next-steps"></a>次のステップ

* [Custom Speech に UPS の発音を提供する](how-to-custom-speech-test-and-train.md#structured-text-data-for-training-public-preview)
