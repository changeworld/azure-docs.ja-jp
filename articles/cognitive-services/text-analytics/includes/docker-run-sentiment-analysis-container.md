---
title: docker run コマンドのコンテナー実行の例
titleSuffix: Azure Cognitive Services
description: 感情分析コンテナー用の docker run コマンド
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: 8e8a48620a8591dee7fc98b06f37bc073b0ead2a
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051342"
---
### <a name="run-container-example-of-docker-run-command"></a>docker run コマンドのコンテナー実行の例

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/sentiment \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

このコマンドは、次の操作を行います。

* コンテナー イメージから*感情分析*コンテナーを実行します。
* 1 つの CPU コアと 4 ギガバイト (GB) のメモリを割り当てます
* TCP ポート 5000 を公開し、コンテナーに pseudo-TTY を割り当てます
* コンテナーの終了後にそれを自動的に削除します。 ホスト コンピューター上のコンテナー イメージは引き続き利用できます。