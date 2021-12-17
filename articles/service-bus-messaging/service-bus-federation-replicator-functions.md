---
title: メッセージ レプリケーションのタスクとアプリケーション - Azure Service Bus | Microsoft Docs
description: この記事では、Azure Functions を使用したメッセージ レプリケーション タスクとアプリケーションの構築の概要について説明します。
ms.topic: article
ms.date: 09/28/2021
ms.openlocfilehash: 78598401816adc32eecb34e2bd11fa49e8a44b0f
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129211812"
---
# <a name="message-replication-tasks-and-applications"></a>メッセージ レプリケーションのタスクとアプリケーション

[メッセージ レプリケーションとクロスリージョン フェデレーション](service-bus-federation-overview.md)に関する記事で説明されているように、Service Bus エンティティのペア間、および Service Bus と他のメッセージ ソースおよびターゲット間のメッセージ シーケンスのレプリケーションは、通常、Azure Functions に依存しています。

[Azure Functions](../azure-functions/functions-overview.md) は、[メッセージ レプリケーションとフェデレーション](service-bus-federation-overview.md) タスクを含む、サーバーレス アプリケーションを構成および実行するためのスケーラブルで信頼性の高い実行環境です。

この概要では、このようなアプリケーション用の Azure Functions の組み込み機能、変換タスクに適応して変更できるコード ブロック、および Service Bus と他の Azure メッセージング サービスと理想的に統合されるように Azure Functions アプリケーションを構成する方法について説明します。 詳細については、この記事で紹介する Azure Functions のドキュメントを参照してください。

[!INCLUDE [messaging-replicator-functions](../../includes/messaging-replicator-functions.md)]
