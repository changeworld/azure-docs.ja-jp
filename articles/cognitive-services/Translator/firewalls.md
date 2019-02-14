---
title: ファイアウォールの内側で翻訳する - Translator Text API
titlesuffix: Azure Cognitive Services
description: IP ファイアウォールの内側で Translator Text API を使用して翻訳します。
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: v-jansko
ms.openlocfilehash: 1e598594f53bc94d3a740d2bd5cdd100456131fa
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884734"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>IP ファイアウォールの内側で Translator Text API を使用して翻訳する方法

Translator Text API による翻訳は、ドメイン名フィルタリングまたは IP フィルタリングを使用して、ファイアウォールの内側で実行できます。 可能であればドメイン名フィルタリングの使用をお勧めします。 IP フィルタリング ファイアウォールの内側から Microsoft Translator を実行することは**お勧めしません**。 この構成は将来、予告なく動作不能になる可能性があります。 

## <a name="translator-ip-addresses"></a>Translator の IP アドレス
2018 年 11 月 20 日における api.cognitive.microsofttranslator.com (Microsoft Translator Text API) の IP アドレスは次のとおりです。

* **アジア太平洋:** 40.90.139.163、104.44.89.44
* **ヨーロッパ:** 40.90.138.4、40.90.141.99
* **北米:** 40.90.139.36、40.90.139.2


## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [IP ファイアウォールの内側から Translator API 呼び出しで翻訳する](reference/v3-0-translate.md)
