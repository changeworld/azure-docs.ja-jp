---
title: Translator Text API の文字数カウント
titlesuffix: Azure Cognitive Services
description: Translator Text API の文字数のカウント方法。
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: c6234a46ae55d73739dcc23110c5e0f6375c3f96
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128742"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Translator Text API の文字数のカウント方法

Translator Text API は、入力のすべての文字をカウントします。 バイト数ではなく、Unicode の意味での文字数です。 Unicode サロゲートは 2 文字としてカウントされます。 空白とマークアップは、文字としてカウントされます。 応答の長さは関係ありません。

Detect メソッドと BreakSentence メソッドへの呼び出しは、文字消費においてカウントされません。 ただし、Detect メソッドと BreakSentence メソッドへの呼び出しは、カウントされる他の関数の使用に対して妥当な割合となっていることが求められます。 Microsoft は、Detect と BreakSentence のカウントを開始する権利を留保します。 

文字数のカウントの詳細については、[Microsoft Translator に関する FAQ](https://www.microsoft.com/en-us/translator/faq.aspx) を参照してください。
