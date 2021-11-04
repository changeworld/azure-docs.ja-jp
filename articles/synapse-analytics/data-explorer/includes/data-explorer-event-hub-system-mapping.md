---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: e7e723394bd7a90f6fd4cda8db7c7cb27c4cb9a5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477914"
---
> [!NOTE]
> * システム プロパティは、`json` および表形式 (`csv` や `tsv` など) でサポートされており、圧縮データではサポートされていません。 サポートされていない形式を使用してもデータは取り込まれますが、プロパティは無視されます。
> * 表形式データの場合、システム プロパティは単一レコードのイベント メッセージでのみサポートされます。
> * JSON データの場合、システム プロパティは複数レコードのイベント メッセージでもサポートされます。 このような場合、システム プロパティは、イベント メッセージの最初のレコードにのみ追加されます。 
> * `csv` マッピングの場合、[システム プロパティ](../ingest-data/data-explorer-ingest-event-hub-overview.md#system-properties)の表に示された順序でプロパティがレコードの先頭に追加されます。
> * `json` マッピングの場合、[システム プロパティ](../ingest-data/data-explorer-ingest-event-hub-overview.md#system-properties)の表のプロパティ名に従ってプロパティが追加されます。
