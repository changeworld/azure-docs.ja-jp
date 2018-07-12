---
title: Microsoft Translator Text API 動的ディクショナリ | Microsoft Docs
description: Microsoft Translator Text API の動的ディクショナリ機能を使用する方法。
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a18348c9786669ac41c4e149577d97cd631d5531
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35378414"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-microsoft-translator-text-api"></a>Microsoft Translator Text API の動的ディクショナリ機能を使用する方法

単語や語句に適用する翻訳があらかじめわかっている場合は、それを要求内でマークアップとして指定することができます。 動的ディクショナリは、固有名詞や製品名のような複合名詞に対してのみ安全に使用できます。 

**構文:** 

<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>

**例: en-de:**

ソース入力: The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.

ターゲット出力: Das Wort "wordomatic" ist ein Wörterbucheintrag.

この機能は、HTML を使用する場合と使用しない場合で同じように動作します。 

この機能は慎重に使用する必要があります。 翻訳をカスタマイズするには、Microsoft Translator Hub を使用するのが適切で望ましい方法です。 この Hub では、コンテキストおよび統計的確率を最大限に活用します。 コンテキスト内の単語または語句を表示するトレーニング データを作成する余裕がある場合、非常に良い結果が得られます。 ハブの詳細については、[http://hub.microsofttranslator.com](http://hub.microsofttranslator.com) で確認できます。

