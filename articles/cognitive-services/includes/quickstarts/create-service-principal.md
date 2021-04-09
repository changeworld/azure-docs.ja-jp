---
title: Azure サービス プリンシパルを作成する
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 2f60759fbbae2c0f712a475ef397ca260e1f0415
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473836"
---
## <a name="create-an-azure-service-principal"></a>Azure サービス プリンシパルを作成する

アプリケーションが Azure アカウントと対話できるようにするには、アクセス許可を管理するための Azure サービス プリンシパルが必要です。 [Azure サービス プリンシパルの作成](/powershell/azure/create-azure-service-principal-azureps)に関するページの手順に従ってください。

サービス プリンシパルを作成すると、それにシークレット値、ID、およびアプリケーション ID が含まれていることがわかります。 後の手順のために、アプリケーション ID とシークレットを一時的な場所に保存します。
