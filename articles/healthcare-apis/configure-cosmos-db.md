---
title: Azure API for FHIR で Cosmos DB の設定を構成する
description: この記事では、Azure API for FHIR で Cosmos DB の設定を構成する方法について説明します。
author: matjazl
ms.service: healthcare-apis
ms.topic: reference
ms.date: 05/22/2019
ms.author: matjazl
ms.openlocfilehash: 3d4f2ade64c55c757634e9c2b68aa9dcef51f117
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299597"
---
# <a name="configure-cosmos-db-settings"></a>Cosmos DB の設定を構成する 

Azure API for FHIR では、FHIR データを格納する基盤のデータベースとして Cosmos DB を使用します。 新しい Azure API for FHIR をプロビジョニングする場合は、サービスに使用される Cosmos DB の RU (要求ユニット) の数を指定するオプションがあります。

Cosmos DB の RU の詳細については、「[Azure Cosmos DB の要求ユニット](https://docs.microsoft.com/azure/cosmos-db/request-units)」を参照してください。

Azure portal でこの設定を変更するには、Azure API for FHIR に移動して Cosmos DB ブレードを開き、パフォーマンスのニーズに応じてスループットを目的の値に変更します。 この値は最大 10,000 RU/秒まで変更できます。* より高い値が必要な場合は、Azure サポートにお問い合わせください。

* **"注:** 値が高いほど、Cosmos DB のスループットが高く、サービスのコストが高いことを意味します。

![](media/cosmosdb/cosmosdb-config.png)
