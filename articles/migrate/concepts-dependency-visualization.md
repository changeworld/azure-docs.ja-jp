---
title: "Azure Migrate の依存関係の視覚化 | Microsoft Docs"
description: "Azure Migrate サービスにおけるアセスメントの計算の概要を説明します。"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 2/21/2018
ms.author: raynew
ms.openlocfilehash: 886977764517f1fec89eee77fc3263d30ff9ab31
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2018
---
# <a name="dependency-visualization"></a>依存関係の視覚化

[Azure Migrate](migrate-overview.md) サービスは、Azure への移行についてオンプレミスのマシンのグループを評価します。 マシンをグループ化するために、依存関係の視覚化を使用できます。 この記事では、次の機能に関する情報を提供します。


## <a name="overview"></a>概要

Azure Migrate の依存関係の視覚化を使用すると、自信を持って移行評価のグループを作成できます。 依存関係の視覚化を使用すると、特定のマシン、またはマシンのグループ全体について、ネットワーク依存関係を確認できます。 この確認は、アプリとworker ロールが複数のマシンで実行されている場合に、移行プロセスで機能が損失する (またはマシンが忘れられる) ことを防ぐために役立ちます。  

## <a name="how-does-it-work"></a>それはどのように機能しますか?

Azure Migrate は、依存関係の視覚化のために [Log Analytics](../log-analytics/log-analytics-overview.md) の [Service Map](../operations-management-suite/operations-management-suite-service-map.md) ソリューションを使用します。
- Azure Migrate プロジェクトを作成すると、サブスクリプションに OMS Log Analytics ワークスペースが作成されます。
- ワークスペース名は、移行プロジェクト用に指定した名前です。先頭に **migrate-** と、状況に応じて末尾に番号が付きます。 
- プロジェクトの **[概要]** ページの **[基本]** セクションから Log Analytics ワークスペースに移動します。
- 作成されるワークスペースには、**MigrateProject** というキーと、**プロジェクト名**の値がタグに設定されます。 Azure Portal での検索にこれらの情報を使用できます。  

    ![Log Analytics ワークスペース](./media/concepts-dependency-visualization/oms-workspace.png)

依存関係の視覚化を使用するには、分析するオンプレミスの各マシンにエージェントをダウンロードしてインストールする必要があります。  

## <a name="do-i-need-to-pay-for-it"></a>使用料金が必要になる場合

Azure Migrate の価格については、[こちら](https://azure.microsoft.com/pricing/details/azure-migrate/)を参照してください。 

## <a name="how-do-i-manage-the-workspace"></a>ワークスペースの管理方法

Azure Migrate 以外で Log Analytics ワークスペースを使用できます。 作成した移行プロジェクトを削除しても、ワークスペースは削除されません。 ワークスペースが不要になった場合は手動で[削除](../log-analytics/log-analytics-manage-access.md)します。

移行プロジェクトを削除する場合を除き、Azure Migrate で作成されたワークスペースは削除しないでください。 削除すると、依存関係は期待どおりに動作しません。

## <a name="next-steps"></a>次の手順

[マシンの依存関係マッピングを使用したマシンのグループ化](how-to-create-group-machine-dependencies.md)