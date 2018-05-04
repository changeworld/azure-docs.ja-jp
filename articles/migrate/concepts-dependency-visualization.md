---
title: Azure Migrate の依存関係の視覚化 | Microsoft Docs
description: Azure Migrate サービスにおけるアセスメントの計算の概要を説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 2/21/2018
ms.author: raynew
ms.openlocfilehash: d075ce9ee124d373a1284577324a50338245a03f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="dependency-visualization"></a>依存関係の視覚化

[Azure Migrate](migrate-overview.md) サービスは、Azure への移行についてオンプレミスのマシンのグループを評価します。 マシンをグループ化するために、依存関係の視覚化を使用できます。 この記事では、次の機能に関する情報を提供します。


## <a name="overview"></a>概要

Azure Migrate の依存関係の視覚化を使用すると、自信を持って移行評価のグループを作成できます。 依存関係の視覚化を使用すると、特定のマシン、またはマシンのグループ全体について、ネットワーク依存関係を確認できます。 この確認は、アプリとworker ロールが複数のマシンで実行されている場合に、移行プロセスで機能が損失する (またはマシンが忘れられる) ことを防ぐために役立ちます。  

## <a name="how-does-it-work"></a>それはどのように機能しますか?

Azure Migrate は、依存関係の視覚化のために [Log Analytics](../log-analytics/log-analytics-overview.md) の [Service Map](../operations-management-suite/operations-management-suite-service-map.md) ソリューションを使用します。
- Azure Migrate プロジェクトを作成すると、サブスクリプションに Log Analytics ワークスペースが作成されます。
- ワークスペース名は、移行プロジェクト用に指定した名前です。先頭に **migrate-** と、状況に応じて末尾に番号が付きます。 
- プロジェクトの **[概要]** ページの **[基本]** セクションから Log Analytics ワークスペースに移動します。
- 作成されるワークスペースには、**MigrateProject** というキーと、**プロジェクト名**の値がタグに設定されます。 Azure Portal での検索にこれらの情報を使用できます。  

    ![Log Analytics ワークスペース](./media/concepts-dependency-visualization/oms-workspace.png)

依存関係の視覚化を使用するには、分析するオンプレミスの各マシンにエージェントをダウンロードしてインストールする必要があります。  

## <a name="do-i-need-to-pay-for-it"></a>使用料金が必要になる場合

Azure Migrate は、追加料金なしで利用できます。 Azure Migrate で依存関係の視覚化機能を使用するには、Service Map が必要です。 Azure Migrate プロジェクトを作成すると、新しい Log Analytics ワークスペースが自動的に作成されます。

> [!NOTE]
> 依存関係の視覚化機能では、サービス マップは Log Analytics ワークスペース経由で使用されます。 Azure Migrate の一般提供が発表された 2018 年 2 月 28 日以降は、この機能を追加料金なしで使用できるようになりました。 無料のワークスペースを使用するには、新しいプロジェクトを作成する必要があります。 一般提供が開始される前の既存のワークスペースは課金対象のままとなるため、新しいプロジェクトに移動することをお勧めします。

1. この Log Analytics ワークスペース内で Service Map 以外のソリューションを使用すると、Standard の Log Analytics 料金が適用されます。 
2. 追加コストなしの移行シナリオをサポートするため、Service Map ソリューションでは、Azure Migrate プロジェクトを作成してから最初の 180 日間は料金が発生せず、それ以降に標準料金が適用されます。
3. 無料で使用できるのは、プロジェクト作成の一部として作成されたワークスペースに限られます。

ワークスペースにエージェントを登録する際には、エージェントのインストール手順のページで、プロジェクトによって指定された ID とキーを使用します。 既存のワークスペースを使用し、それを Azure Migrate プロジェクトに関連付けることはできません。

Azure Migrate プロジェクトが削除される際、それに伴ってワークスペースが削除されることはありません。 プロジェクトが削除された後は、Service Map を無料で使用することはできず、各ノードは Log Analytics ワークスペースの階層に応じて課金されます。

Azure Migrate の価格については、[こちら](https://azure.microsoft.com/pricing/details/azure-migrate/)を参照してください。 

## <a name="how-do-i-manage-the-workspace"></a>ワークスペースの管理方法

Azure Migrate 以外で Log Analytics ワークスペースを使用できます。 作成した移行プロジェクトを削除しても、ワークスペースは削除されません。 ワークスペースが不要になった場合は手動で[削除](../log-analytics/log-analytics-manage-access.md)します。

移行プロジェクトを削除する場合を除き、Azure Migrate で作成されたワークスペースは削除しないでください。 削除すると、依存関係は期待どおりに動作しません。

## <a name="next-steps"></a>次の手順

[マシンの依存関係マッピングを使用したマシンのグループ化](how-to-create-group-machine-dependencies.md)