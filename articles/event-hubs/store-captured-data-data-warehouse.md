---
title: チュートリアル:Azure Synapse Analytics にイベント データを移行する - Azure Event Hubs
description: Azure Event Grid と Functions を使用して、Event Hubs でキャプチャされたデータを Azure Synapse Analytics に移行する方法について説明します。
services: event-hubs
ms.date: 12/07/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 619bb452b421c4ba21cd03c3fee9692e9c436764
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2021
ms.locfileid: "112416841"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-azure-synapse-analytics-using-event-grid-and-azure-functions"></a>チュートリアル:Event Grid と Azure Functions を使用して、キャプチャされた Event Hubs データを Azure Synapse Analytics に移行する
Azure Event Hubs [Capture](./event-hubs-capture-overview.md) を使用すると、Event Hubs のストリーミング データを Azure BLOB ストレージまたは Azure Data Lake Storage で自動的にキャプチャすることができます。 このチュートリアルでは、[Event Grid](../event-grid/overview.md) によってトリガーされる Azure 関数を使用して、キャプチャされた Event Hubs データを Storage から Azure Synapse Analytics に移行する方法について説明します。

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../event-grid/includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>次のステップ 
Data Warehouse で強力なデータ視覚化ツールを使用して、実用的な分析情報を取得できます。

この記事では、[Azure Synapse Analytics で Power BI を使用する方法](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)について説明しています。