---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.openlocfilehash: 6d1ca85c59f03ae0d008342f71597f4d3ca5d97a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2020
ms.locfileid: "91544997"
---
**[Azure リソース]** ページ (左側メニュー) の **[管理]** セクション (右上メニュー) で、**サンプルクエリ**の URL をコピーし、新しいブラウザー タブに貼り付けます。

エンドポイント URL は次の形式になります。YOUR-CUSTOM-SUBDMAIN、APP-ID、KEY-ID は、自分のカスタム サブドメイン、アプリ ID、エンドポイント キーに置き換えます。

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```