---
titleSuffix: Cognitive Services
services: cognitive-services
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2021
ms.author: aahi
ms.openlocfilehash: 2d26c560cd05aad4962d318e8c2c798c576db811
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "131011658"
---
Cognitive Services コンテナーの実行で問題が発生している場合は、Microsoft 診断コンテナーを使用してみることができます。 このコンテナーを使用して、Cognitive Services コンテナーが想定どおりに機能しなくなる可能性がある、展開環境での一般的なエラーを診断します。

コンテナーを取得するには、次の Docker `pull` コマンドを使用します。

```bash
docker pull mcr.microsoft.com/azure-cognitive-services/diagnostic
```

次に、コンテナーを実行し、`{ENDPOINT_URI}` をエンドポイントに置き換え、`{API_KEY}`はお使いのリソースのキーに置き換えます。

```bash
docker run --rm mcr.microsoft.com/azure-cognitive-services/diagnostic \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

コンテナーは、課金エンドポイントへのネットワーク接続をテストします。