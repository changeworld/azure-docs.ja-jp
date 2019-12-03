---
title: コンテナー サポート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2019
ms.author: dapine
ms.openlocfilehash: 2cb2cfbdfbac5d496f109d85977b41a050766ab0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499092"
---
## <a name="create-an-computer-vision-resource"></a>Computer Vision リソースを作成する

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. [[**Computer Vision** リソースの作成]](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision) をクリックします。
1. すべての必須設定を入力します。

    |Setting|値|
    |--|--|
    |名前|任意の名前 (2 から 64 文字)|
    |Subscription|適切なサブスクリプションを選択します|
    |Location|近くにある任意の使用可能な場所を選択します|
    |価格レベル|`F0` -最小限の価格レベル|
    |リソース グループ|使用可能なリソース グループを選択します|

1. **[作成]** をクリックして、リソースが作成されるまで待ちます。 作成後、リソース ページに移動します。
1. 構成済みの `{ENDPOINT_URI}` と `{API_KEY}` を収集します。詳細については、[必要なパラメーターの収集](../computer-vision-how-to-install-containers.md#gathering-required-parameters)に関するトピックをご覧ください。
