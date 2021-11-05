---
title: Cognitive Services 言語リソースを作成する
titleSuffix: Azure Cognitive Services
description: Cognitive Services Language リソースを作成する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9d21f76e73e9ef4581355890b56876ff2e71a954
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091956"
---
## <a name="create-a-cognitive-services-language-resource"></a>Cognitive Services 言語リソースを作成する

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[リソースの作成]** を選択し、 **[AI + 機械学習]**  >  **[言語]** に移動します。
   または、[言語リソースの作成](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)に関するページを参照してください。
1. 必須の設定をすべて入力します。

    |設定|値|
    |--|--|
    |名前|名前 (2 文字から 64 文字) を入力します。|
    |サブスクリプション|適切なサブスクリプションを選択します。|
    |場所|近くの場所を選択します。|
    |Pricing tier| 「**S**」 (Standard 価格レベル) と入力します。|
    |Resource group|使用可能なリソース グループを選択します。|

1. **[作成]** を選択し、リソースが作成されるまで待ちます。 ブラウザーが、新たに作成されたリソースのページに自動的にリダイレクトされます。
1. 構成された `endpoint` と API キーを収集します。

    |ポータルでの [リソース] タブ|設定|値|
    |--|--|--|
    |**概要**|エンドポイント|エンドポイントをコピーします。 `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0` のように表示されます。|
    |**[キー]**|API キー|2 つのキーのうちの 1 つをコピーします。 スペースやダッシュを含まない 32 文字の英数字文字列 <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`> です。|
