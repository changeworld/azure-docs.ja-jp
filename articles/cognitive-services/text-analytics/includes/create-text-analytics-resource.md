---
title: Cognitive Services Text Analytics リソースを作成する
titleSuffix: Azure Cognitive Services
description: Cognitive Services Text Analytics リソースを作成する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: daafea59b96cc8da6b78f0733c9f54e0e4d3a8c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779251"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Cognitive Services Text Analytics リソースを作成する

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[Create a resource]\(リソースを作成)** を選択して、**AI + Machine Learning** > **Text Analytics** に移動します。
   または、[[Create Text Analytics]\(Text Analytics の作成\)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) に移動します。
1. すべての必須設定を入力します。

    |設定|値|
    |--|--|
    |名前|名前 (2 から 64 文字) を入力します。|
    |サブスクリプション|適切なサブスクリプションを選択します。|
    |場所|近くの場所を選択します。|
    |Pricing tier| **S** (Standard 価格レベル) を入力します。|
    |Resource group|使用可能なリソース グループを選択します。|

1. **[作成]** を選択して、リソースが作成されるまで待ちます。 ブラウザーが、新たに作成されたリソースのページに自動的にリダイレクトされます。
1. 構成された `endpoint` と API キーを収集します。

    |ポータルでの [リソース] タブ|設定|値|
    |--|--|--|
    |**概要**|エンドポイント|エンドポイントをコピーします。 `https://northeurope.api.cognitive.microsoft.com/text/analytics/v2.0` のように表示されます。|
    |**[キー]**|API キー|2 つのキーのうちの 1 つをコピーします。 スペースやダッシュを含まない 32 文字の英数字文字列 <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`> です。|
