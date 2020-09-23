---
title: docker run コマンドのコンテナー実行の例
titleSuffix: Azure Cognitive Services
description: 言語検出コンテナー用の docker run コマンド
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: f1f36cc730ceca9a961769775d17de6be8d26948
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906002"
---
"*言語検出*" コンテナーを実行するには、次の `docker run` コマンドを実行します。 次のプレースホルダーを実際の値に置き換えてください。

| プレースホルダー | 値 | 形式または例 |
|-------------|-------|---|
| **{API_KEY}** | 自分の Text Analytics リソースのキー。 それは、Azure portal で、お使いのリソースの **[キーとエンドポイント]** ページで見つけることができます。 |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Text Analytics API にアクセスするためのエンドポイント。 それは、Azure portal で、お使いのリソースの **[キーとエンドポイント]** ページで見つけることができます。 | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |


```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/textanalytics/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから *Language Detection* コンテナーを実行します
* 1 つの CPU コアと 4 ギガバイト (GB) のメモリを割り当てます
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。