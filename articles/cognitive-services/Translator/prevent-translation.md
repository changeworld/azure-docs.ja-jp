---
title: コンテンツの翻訳を防止する - Translator
titleSuffix: Azure Cognitive Services
description: Translator によるコンテンツの翻訳を防止します。 Translator では、翻訳されないようコンテンツにタグを付けることができます。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: swmachan
ms.openlocfilehash: 33939976a0824ce8afeb2e6f6fb19e7033098683
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592697"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Translator によるコンテンツの翻訳を防止する方法

Translator では、翻訳されないようコンテンツにタグを付けることができます。 たとえば、コードやブランド名、ローカライズしても意味がない語句にタグを付けることが考えられます。

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

3. 文字列を翻訳対象として Translator に渡さないでください。

4. カスタム トランスレーター: 語句に対して 100% の確率で決まる翻訳は、[カスタム トランスレーターのディクショナリ](custom-translator/what-is-dictionary.md)を使用して指示します。


## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Translator 呼び出しでの翻訳を防止する](reference/v3-0-translate.md)
