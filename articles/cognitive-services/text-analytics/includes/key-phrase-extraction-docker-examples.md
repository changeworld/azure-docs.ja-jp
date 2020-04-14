---
title: キー フレーズ抽出コンテナー docker の例
titleSuffix: Azure Cognitive Services
description: キー フレーズ抽出コンテナー docker の例
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 1c079df3171d89da4ef40e5008aaeb08b6504e66
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878677"
---
### <a name="key-phrase-extraction-container-docker-examples"></a>キー フレーズ抽出コンテナー docker の例

次の docker 例はキー フレーズ抽出コンテナーに対するものです。

#### <a name="basic-example"></a>基本的な例 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} 
  ```

#### <a name="logging-example"></a>ログの例 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/keyphrase \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Console:LogLevel:Default=Information
  ```
