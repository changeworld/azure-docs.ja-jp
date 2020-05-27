---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: a5f829fc0e153c3a427fd1c7cc7d5c613cd624f6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648249"
---
Azure Functions では、クロス オリジン リソース共有 (CORS) がサポートされています。 CORS は[ポータル内で](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors)、[Azure CLI](/cli/azure/functionapp/cors) によって構成されます。 CORS の許可配信元一覧は、関数アプリ レベルで適用されます。 CORS を有効にすると、応答に `Access-Control-Allow-Origin` ヘッダーが含まれます。 詳細については、「 [クロス オリジン リソース共有](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors)」を参照してください。 