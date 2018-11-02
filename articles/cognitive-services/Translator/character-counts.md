---
title: 文字数カウント - Translator Text API
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
ms.openlocfilehash: 8e04158d34765b8a077fc56f2108ea6d7b4b03a6
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649196"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Translator Text API の文字数のカウント方法

Translator Text API は、入力のすべての文字をカウントします。 バイト数ではなく、Unicode の意味での文字数です。 Unicode サロゲートは 2 文字としてカウントされます。 空白とマークアップは、文字としてカウントされます。 応答の長さは関係ありません。

Detect メソッドと BreakSentence メソッドへの呼び出しは、文字消費においてカウントされません。 ただし、Detect メソッドと BreakSentence メソッドへの呼び出しは、カウントされる他の関数の使用に対して妥当な割合となっていることが求められます。 Microsoft は、Detect と BreakSentence のカウントを開始する権利を留保します。

文字数のカウントの詳細については、[Microsoft Translator に関する FAQ](https://www.microsoft.com/en-us/translator/faq.aspx) を参照してください。
