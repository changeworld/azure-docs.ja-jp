---
title: アプリケーションの設定
titleSuffix: Azure Cognitive Services
description: Language Understanding アプリのアプリケーション設定について理解します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 3682b9e0c38344be1522440290b46f8c10bd5607
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275906"
---
# <a name="application-settings"></a>アプリケーションの設定

これらのアプリケーション設定は、REST API を使用して [exported](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) アプリと [updated](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) に格納されます。 アプリのバージョン設定を変更すると、アプリのトレーニング状態がトレーニングなしにリセットされます。

|Setting|Default value|メモ|
|--|--|--|
|NormalizePunctuation|True|句読点を削除します。|
|NormalizeDiacritics|True|分音文字を削除します。|

## <a name="diacritics-normalization"></a>分音文字の正規化 

LUIS JSON アプリ ファイルに対して、分音記号の発話の正規化をオンにするには、`settings` パラメーターを使用します。

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

次の発話は、分音記号の正規化が発話に与える影響を示しています。

|分音記号を false に設定した場合|分音記号を true に設定した場合|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>分音記号の言語サポート

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>ポルトガル語 (ブラジル) `pt-br` の分音記号

|分音記号を false に設定|分音記号を true に設定|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`| 
|||

#### <a name="dutch-nl-nl-diacritics"></a>オランダ語 `nl-nl` の分音記号

|分音記号を false に設定|分音記号を true に設定|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`| 
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>フランス語 `fr-` の分音記号

これには、フランスとカナダ両方のサブカルチャが含まれます。

|分音記号を false に設定|分音記号を true に設定|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`| 
|`ê`|`e`| 
|`î`|`i`| 
|`ô`|`o`| 
|`û`|`u`| 
|`ç`|`c`| 
|`ë`|`e`| 
|`ï`|`i`| 
|`ü`|`u`| 
|`ÿ`|`y`| 

#### <a name="german-de-de-diacritics"></a>ドイツ語 `de-de` の分音記号

|分音記号を false に設定|分音記号を true に設定|
|--|--|
|`ä`|`a`|
|`ö`|`o`| 
|`ü`|`u`| 

#### <a name="italian-it-it-diacritics"></a>イタリア語 `it-it` の分音記号

|分音記号を false に設定|分音記号を true に設定|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`| 
|`í`|`i`| 
|`î`|`i`| 
|`ò`|`o`| 
|`ó`|`o`| 
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>スペイン語 `es-` の分音記号

これには、スペインとカナダ両方のメキシコ人が含まれます。

|分音記号を false に設定|分音記号を true に設定|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`| 
|`ó`|`o`| 
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>句読点の正規化

LUIS JSON アプリ ファイルに対して、句読点の発話の正規化をオンにするには、`settings` パラメーターを使用します。

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

次の発話は、分音記号が発話に与える影響を示しています。

|分音記号を False に設定した場合|分音記号を True に設定した場合|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>句読点を削除

`NormalizePunctuation` が true に設定されている場合、次の句読点は削除されます。

|句読点|
|--|
|`-`| 
|`.`| 
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|
