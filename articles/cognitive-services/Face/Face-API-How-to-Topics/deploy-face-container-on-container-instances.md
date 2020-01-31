---
title: Azure Container Instances での Face コンテナーの実行
titleSuffix: Azure Cognitive Services
description: Face コンテナーを Azure Container Instance にデプロイし、Web ブラウザーでテストします。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 4f8a08697443ec6920b3fa73de19a950e54e7f40
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716245"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>Azure Container Instances に Face コンテナーをデプロイする

Cognitive Services [Face](../face-how-to-install-containers.md) コンテナーを Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/) にデプロイする方法について説明します。 この手順では、Azure Face リソースの作成方法を実演します。 次に、関連するコンテナー イメージをプルする方法について説明します。 最後に、ブラウザーからこの 2 つのオーケストレーションを行う機能を取り上げます。 コンテナーを使用することで、開発者の関心をインフラストラクチャの管理から切り離し、アプリケーション開発に専念させることができます。

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-private-container-registry"></a>プライベート コンテナー レジストリへのアクセスの要求

[!INCLUDE [Request access to private container registry](../../../../includes/cognitive-services-containers-request-access.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]