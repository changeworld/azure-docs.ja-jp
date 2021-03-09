---
title: Web ジョブでバックグラウンド タスクを実行する
description: Azure App Service で Web ジョブを使用してバックグラウンド タスクを実行する方法を説明します。 さまざまなスクリプト形式から選択し、CRON 式を使用して実行します。
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
zone_pivot_groups: app-service-webjob
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4737f0f19acf199190df02386ecb2ece65fa571e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743836"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Azure App Service で Web ジョブを使用してバックグラウンド タスクを実行する

Azure での[バックグラウンド タスク](./webjobs-create-ieux-conceptual.md)の実行の概念は、Azure App Service Web ジョブを使用して提供されています。 デプロイ方法を学習する <abbr title="Web アプリ、API アプリ、またはモバイル アプリと同じインスタンス内のプログラムまたはスクリプト。">WebJobs</abbr> [Azure portal](https://portal.azure.com) を使用して実行可能ファイルまたはスクリプトをアップロードします。 

サポートされている Web ジョブには、次の 3 つがあります。

* **継続的**: 通常は無限ループで実行されていて、すぐに開始します。
* **スケジュール**: スケジュールされたトリガーから開始します
* **手動**: 手動トリガーから開始します

::: zone pivot="webjob-type-continuous"

[!INCLUDE [Continuous](./includes/webjobs-create-ieux-continuous.md)]

::: zone-end

::: zone pivot="webjob-type-scheduled"

[!INCLUDE [Scheduled](./includes/webjobs-create-ieux-scheduled.md)]

::: zone-end

::: zone pivot="webjob-type-manual"

[!INCLUDE [Manual](./includes/webjobs-create-ieux-manual.md)]

::: zone-end
   
## <a name="next-steps"></a><a name="NextSteps"></a> 次のステップ

* [Web ジョブとしてのバックグラウンド タスクの詳細について学習する](./webjobs-create-ieux-conceptual.md)
* [Web ジョブのログ履歴を表示する](./webjobs-create-ieux-view-log.md)

* [Web ジョブ SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) を使用して多くのプログラミング タスクを単純化する

* [Visual Studio を使用した Web ジョブの開発とデプロイ](webjobs-dotnet-deploy-vs.md)について学習する