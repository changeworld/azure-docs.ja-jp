---
title: LUIS リソースの作成
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/23/2019
ms.author: dapine
ms.openlocfilehash: a765ac27936da9da5a2f41464c17491e3561f44b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73465937"
---
## <a name="create-a-luis-resource"></a>LUIS リソースの作成

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. [**Language Understanding** の作成](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)をクリックします
1. すべての必須設定を入力します。

    |Setting|値|
    |--|--|
    |名前|任意の名前 (2 から 64 文字)|
    |Subscription|適切なサブスクリプションを選択します|
    |Location|近くにある任意の使用可能な場所を選択します|
    |価格レベル|`F0` -最小限の価格レベル|
    |リソース グループ|使用可能なリソース グループを選択します|

1. **[作成]** をクリックして、リソースが作成されるまで待ちます。 作成後、リソース ページに移動します。
1. 構成済み `endpoint` と API キーを収集します。[必要なパラメーターの収集](#gathering-required-parameters)に関するトピックをご覧ください。

[!INCLUDE [Gathering required parameters](../../containers/includes/container-gathering-required-parameters.md)]
