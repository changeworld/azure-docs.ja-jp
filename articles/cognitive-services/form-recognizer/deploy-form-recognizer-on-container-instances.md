---
title: Azure Container Instances で Form Recognizer コンテナーを実行する
titleSuffix: Azure Cognitive Services
description: Form Recognizer コンテナーを Azure Container Instance にデプロイし、Web ブラウザーでテストします。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: aahi
ms.openlocfilehash: fb837dfc72a148d20d382a5dc356c99306aa400f
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913184"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Azure Container Instances に Form Recognizer コンテナーをデプロイする

[!INCLUDE [Form Recognizer containers limit](includes/container-limit.md)]

Cognitive Services [Form Recognizer](form-recognizer-container-howto.md) コンテナーを Azure [Container Instances](../../container-instances/index.yml) にデプロイする方法について説明します。 この手順では、Azure Form Recognizer リソースの作成方法を示します。 次に、関連するコンテナー イメージをプルする方法について説明します。 最後に、ブラウザーからこの 2 つのオーケストレーションを行う機能を取り上げます。 コンテナーを使用することで、開発者の関心をインフラストラクチャの管理から切り離し、アプリケーション開発に専念させることができます。

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]