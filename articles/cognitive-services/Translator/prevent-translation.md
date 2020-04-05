---
title: コンテンツの翻訳を防止する - Translator Text API
titleSuffix: Azure Cognitive Services
description: Translator Text API によるコンテンツの翻訳を防止します。 Translator Text API では、翻訳されないようコンテンツにタグを付けることができます。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: c7be4a0ea1a9d24a8b262132632a0bbb63ae1b96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052481"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Translator Text API によるコンテンツの翻訳を防止する方法

Translator Text API では、翻訳されないようコンテンツにタグを付けることができます。 たとえば、コードやブランド名、ローカライズしても意味がない語句にタグを付けることが考えられます。

## <a name="methods-for-preventing-translation"></a>翻訳を防止する方法

1. コンテンツに `notranslate` でタグ付けします。 入力の textType が HTML に設定されている場合にのみ機能するのは仕様です。

   例:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. [動的ディクショナリ](dynamic-dictionary.md)を使用して決まった翻訳を指示する。

3. Translator Text API に翻訳対象の文字列を渡さない。

4. カスタム トランスレーター: 語句に対して 100% の確率で決まる翻訳は、[カスタム トランスレーターのディクショナリ](custom-translator/what-is-dictionary.md)を使用して指示します。


## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Translator API 呼び出しでの翻訳を防止する](reference/v3-0-translate.md)
