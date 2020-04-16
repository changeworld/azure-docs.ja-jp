---
title: コンテナー サポート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: trbye
ms.openlocfilehash: 8abd520a7bb80f1f9a2d8ebcbe46b90fe59a96ec
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422049"
---
## <a name="create-a-speech-resource"></a>Speech リソースを作成する

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. [**Speech** リソースの作成](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)をクリックします。
1. すべての必須設定を入力します。

    |設定|値|
    |--|--|
    |Name|任意の名前 (2 から 64 文字)|
    |サブスクリプション|適切なサブスクリプションを選択します|
    |Location|近くにある任意の使用可能な場所を選択します|
    |価格レベル|`F0` -最小限の価格レベル|
    |リソース グループ|使用可能なリソース グループを選択します|

1. **[作成]** をクリックして、リソースが作成されるまで待ちます。 作成後、リソース ページに移動します。
1. 構成された `endpoint` と API キーを収集します。

    |ポータルでの [リソース] タブ|設定|値|
    |--|--|--|
    |**概要**|エンドポイント|エンドポイントをコピーします。 それは、`https://speech.cognitiveservices.azure.com/sts/v1.0/issuetoken` のようなものです|
    |**[キー]**|API キー|2 つのキーのうちの 1 つをコピーします。 スペースやダッシュを含まない 32 文字の英数字文字列 (`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`) です。|
