---
title: "Azure Migrate の依存関係の視覚化 | Microsoft Docs"
description: "Azure Migrate サービスにおけるアセスメントの計算の概要を説明します。"
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 78e52157-edfd-4b09-923f-f0df0880e0e0
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: a8a8cee327dac8adfb0ae53d101c382ef20599d2
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2017
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

はい。 Log Analytics ワークスペースは既定で作成されますが、Azure Migrate で依存関係の視覚化を使用しない限り、使用されません。 依存関係の視覚化を使用する場合またはワークスペースを Azure Migrate 以外で使用する場合は、ワークスペースの使用料が発生します。  Service Map ソリューションの価格については、[こちら](https://azure.microsoft.com/pricing/details/insight-analytics/)を参照してください。 

## <a name="how-do-i-manage-the-workspace"></a>ワークスペースの管理方法

Azure Migrate 以外で Log Analytics ワークスペースを使用できます。 作成した移行プロジェクトを削除しても、ワークスペースは削除されません。 ワークスペースが不要になった場合は手動で[削除](../log-analytics/log-analytics-manage-access.md)します。

移行プロジェクトを削除する場合を除き、Azure Migrate で作成されたワークスペースは削除しないでください。 削除すると、依存関係は期待どおりに動作しません。

## <a name="next-steps"></a>次のステップ

[マシンの依存関係マッピングを使用したマシンのグループ化](how-to-create-group-machine-dependencies.md)