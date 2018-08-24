---
title: Microsoft Translator Text API の文字数カウント | Microsoft Docs
description: Microsoft Translator Text API での文字数のカウント方法。
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: 1b4987509c17e4064d7c54608395e272efa8de3b
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2018
ms.locfileid: "41931341"
---
# <a name="how-the-microsoft-translator-text-api-counts-characters"></a>Microsoft Translator Text API での文字数のカウント方法

Microsoft Translator は、入力のすべての文字をカウントします。 バイト数ではなく、Unicode の意味での文字数です。 Unicode サロゲートは 2 文字としてカウントされます。 空白とマークアップは、文字としてカウントされます。 応答の長さは関係ありません。

Detect メソッドと BreakSentence メソッドへの呼び出しは、文字消費においてカウントされません。 ただし、Detect メソッドと BreakSentence メソッドへの呼び出しは、カウントされる他の関数の使用に対して妥当な割合となっていることが求められます。 Microsoft は、Detect と BreakSentence のカウントを開始する権利を留保します。 

文字数のカウントの詳細については、[Microsoft Translator に関する FAQ](https://www.microsoft.com/en-us/translator/faq.aspx) を参照してください。
