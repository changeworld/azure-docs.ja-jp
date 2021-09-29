---
title: イベント レプリケーションのタスクとアプリケーション - Azure Event Hubs | Microsoft Docs
description: この記事では、Azure Functions を使用したイベント レプリケーション タスクとアプリケーションの構築の概要について説明します。
ms.topic: article
ms.date: 09/28/2021
ms.openlocfilehash: 92c6357e5c4302232bddabdd1e1818e2f325246b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217336"
---
# <a name="event-replication-tasks-and-applications-with-azure-functions"></a>Azure Functions を使用したイベント レプリケーションのタスクとアプリケーション

> [!TIP]
> イベントのペイロードを考慮し、それらを変換、集計、エンリッチ、または削減する必要があるすべてのステートフル レプリケーション タスクには、Azure Functions の代わりに [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) を使用します。

[イベント レプリケーションとクロスリージョン フェデレーション](event-hubs-federation-overview.md)に関する記事で説明されているように、Event Hubs のペア間、および Event Hubs とその他のストリームのソースとターゲット間でのステートレス レプリケーションは、Azure Functions に依存しています。

[Azure Functions](../azure-functions/functions-overview.md) は、イベント レプリケーションとフェデレーション タスクを含む、サーバーレス アプリケーションを構成および実行するためのスケーラブルで信頼性の高い実行環境です。

この概要では、このようなアプリケーション用の Azure Functions の組み込み機能、変換タスクに適応して変更できるコード ブロック、および Event Hub と他の Azure メッセージング サービスと理想的に統合されるように Azure Functions アプリケーションを構成する方法について説明します。 詳細については、この記事で紹介する Azure Functions のドキュメントを参照してください。

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]









