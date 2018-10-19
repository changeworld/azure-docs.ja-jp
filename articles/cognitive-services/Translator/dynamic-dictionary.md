---
title: Translator Text API の動的ディクショナリ
titlesuffix: Azure Cognitive Services
description: Translator Text API の動的ディクショナリ機能を使用する方法。
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 56558a2da5f29611d90021e9efb292720d1cea35
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128130"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-translator-text-api"></a>Translator Text API の動的ディクショナリ機能を使用する方法

単語や語句に適用する翻訳があらかじめわかっている場合は、それを要求内でマークアップとして指定することができます。 動的ディクショナリは、固有名詞や製品名のような複合名詞に対してのみ安全に使用できます。 

**構文:** 

<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>

**例: en-de:**

ソース入力: The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.

ターゲット出力: Das Wort "wordomatic" ist ein Wörterbucheintrag.

この機能は、HTML を使用する場合と使用しない場合で同じように動作します。 

この機能は慎重に使用する必要があります。 翻訳をカスタマイズするには、Custom Translator を使用するのが適切で望ましい方法です。 Custom Translator では、コンテキストおよび統計的確率を最大限に活用します。 コンテキスト内の単語または語句を表示するトレーニング データがある場合、またはそれを作成できる場合、はるかに良い結果が得られます。 Custom Translator の詳細については、[http://aka.ms/CustomTranslator](http://aka.ms/CustomTranslator) で確認できます。

