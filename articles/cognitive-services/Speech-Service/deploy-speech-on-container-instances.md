---
title: Azure Container Instances を実行する - 音声サービス
titleSuffix: Azure Cognitive Services
description: 音声サービス コンテナーを Azure Container Instance にデプロイし、Web ブラウザーでテストします。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: e5d9143b0c84b579945b283ccae1cce36a7a4291
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717407"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>音声サービス コンテナーを Azure Container Instances にデプロイする

Cognitive Services の[音声サービス](speech-container-howto.md) コンテナーを Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/) にデプロイする方法について説明します。 この手順では、Azure 音声サービス リソースの作成方法を示します。 次に、関連するコンテナー イメージをプルする方法について説明します。 最後に、ブラウザーからこの 2 つのオーケストレーションを行う機能を取り上げます。 コンテナーを使用することで、開発者の関心をインフラストラクチャの管理から切り離し、アプリケーション開発に専念させることができます。

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>コンテナー レジストリへのアクセスの要求

コンテナーへのアクセスを要求するには、最初に [Cognitive Services Speech コンテナー要求フォーム](https://aka.ms/speechcontainerspreview/)に記入して送信する必要があります。 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create a Speech service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
