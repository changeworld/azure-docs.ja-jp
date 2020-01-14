---
title: Azure Container Instances で Form Recognizer コンテナーを実行する
titleSuffix: Azure Cognitive Services
description: Form Recognizer コンテナーを Azure Container Instance にデプロイし、Web ブラウザーでテストします。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 4e16eb2ad6c2634569f981ddcdce753ecaa43f2a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446549"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Azure Container Instances に Form Recognizer コンテナーをデプロイする

Cognitive Services [Form Recognizer](form-recognizer-container-howto.md) コンテナーを Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/) にデプロイする方法について説明します。 この手順では、Azure Form Recognizer リソースの作成方法を示します。 次に、関連するコンテナー イメージをプルする方法について説明します。 最後に、ブラウザーからこの 2 つのオーケストレーションを行う機能を取り上げます。 コンテナーを使用することで、開発者の関心をインフラストラクチャの管理から切り離し、アプリケーション開発に専念させることができます。

> [!IMPORTANT]
> Form Recognizer コンテナーでは、現在、Form Recognizer API バージョン1.0 が使用されています。 API の最新バージョンにアクセスするには、代わりにマネージド サービスを使用します。

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>コンテナー レジストリへのアクセスの要求

コンテナーへのアクセスを要求するには、最初に [Cognitive Services Form Recognizer コンテナー アクセス要求フォーム](https://aka.ms/FormRecognizerRequestAccess)に記入して送信する必要があります。 そうすることで、Computer Vision にサインアップすることにもなります。 Computer Vision 要求フォームに別途サインアップする必要はありません。 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
