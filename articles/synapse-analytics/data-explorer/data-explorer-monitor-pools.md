---
title: 'クイックスタート: Synapse Studio を使用して Data Explorer プールを監視する (プレビュー)'
description: この記事では、Data Explorer プールの監視に関する情報を提供します。
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: shsagir
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.custom: ignite-fall-2021
ms.openlocfilehash: d127abb6e8b493e0db3a281083d9ca17802cf529
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092118"
---
# <a name="quickstart-use-synapse-studio-to-monitor-your-data-explorer-pools-preview"></a>クイックスタート: Synapse Studio を使用して Data Explorer プールを監視する (プレビュー)

Azure Synapse Analytics では、Data Explorer を使用して、ワークスペース内の Data Explorer プールでクエリ、ダッシュボードなどを実行できます。

この記事では、Data Explorer プールを監視する方法について説明します。この方法により、さまざまなワークスペース ユーザーによって使用中の仮想コアの数を含め、プールの状態を監視することができます。

## <a name="access-data-explorer-pools-list"></a>Data Explorer プールの一覧にアクセスする

ワークスペースで Data Explorer プールの一覧を表示するには、最初に [Synapse Studio を開き](https://web.azuresynapse.net/)、ワークスペースを選択します。

![ワークスペースへのログイン](../monitoring/media/common/login-workspace.png)

ワークスペースを開いたら、左側の **[監視]** セクションを選択します。

![[監視] ハブを選択する](../monitoring/media/common/left-nav.png)

Data Explorer プールの一覧を表示するには、 **[Data Explorer pools]\(Data Explorer プール\)** を選択します。

![Data Explorer プールを選択する](media/monitor-data-explorer-pools/monitor-hub-nav-data-explorer-pools.png)

## <a name="filter-your-data-explorer-pools"></a>Data Explorer プールをフィルター処理する

Data Explorer プールの一覧をフィルター処理して、関心のあるものに絞ることができます。 画面の上部にあるフィルターを使用して、フィルター処理を行うフィールドを指定できます。

たとえば、ビューをフィルター処理して、"test" という名前を含む Data Explorer プールのみを表示することができます。

![サンプルのフィルター](media/monitor-data-explorer-pools/filter-example.png)

## <a name="view-details-about-a-specific-data-explorer-pool"></a>特定の Data Explorer プールに関する詳細を表示する

Data Explorer プールのいずれか 1 つの詳細を表示するには、詳細を表示する Data Explorer プールを選択します。

![Data Explorer プールの詳細](media/monitor-data-explorer-pools/data-explorer-pool-details.png)
