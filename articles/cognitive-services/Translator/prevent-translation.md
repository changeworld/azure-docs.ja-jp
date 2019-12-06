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
ms.date: 11/21/2019
ms.author: swmachan
ms.openlocfilehash: 15a36451c18d65df6667f24284f3f69f3d1c06b8
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326754"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Translator Text API によるコンテンツの翻訳を防止する方法

Translator Text API では、翻訳されないようコンテンツにタグを付けることができます。 たとえば、コードやブランド名、ローカライズしても意味がない語句にタグを付けることが考えられます。

## <a name="methods-for-preventing-translation"></a>翻訳を防止する方法
1. Twitter タグ (@somethingtopassthrough または #somethingtopassthrough) でエスケープする。 翻訳後にエスケープを解除します。 `\B@[A-Za-z]+[A-Za-z0-9_]+)` は有効な twitter タグの正規表現です。 タグの先頭には "@" 記号を付け、それに続けて 1 文字の英字、それに続けて 1 文字以上の英字、数字、またはアンダースコアを指定する必要があります。 タグを短くして、開始タグの前にスペースを付けることをお勧めします。

2. コンテンツに `notranslate` でタグ付けします。 入力の textType が HTML に設定されている場合にのみ機能するのは仕様です。

   例:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. [動的ディクショナリ](dynamic-dictionary.md)を使用して決まった翻訳を指示する。

4. Translator Text API に翻訳対象の文字列を渡さない。

5. カスタム トランスレーター: 語句に対して 100% の確率で決まる翻訳は、[カスタム トランスレーターのディクショナリ](custom-translator/what-is-dictionary.md)を使用して指示します。


## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Translator API 呼び出しでの翻訳を防止する](reference/v3-0-translate.md)
