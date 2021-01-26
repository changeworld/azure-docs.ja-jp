---
title: Azure サービス プリンシパルを作成する
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 99487fe962228f775582aa2f0a6fb60fe52862ac
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2020
ms.locfileid: "95095747"
---
## <a name="create-an-azure-service-principal"></a>Azure サービス プリンシパルを作成する

アプリケーションが Azure アカウントと対話できるようにするには、アクセス許可を管理するための Azure サービス プリンシパルが必要です。 [Azure サービス プリンシパルの作成](/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0)に関するページの手順に従ってください。

サービス プリンシパルを作成すると、それにシークレット値、ID、およびアプリケーション ID が含まれていることがわかります。 後の手順のために、アプリケーション ID とシークレットを一時的な場所に保存します。