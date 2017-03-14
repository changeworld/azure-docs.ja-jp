---
title: "Azure Functions のホスティング プランを選択する | Microsoft Docs"
description: "ユーザーのイベント ドリブン ワークロードのニーズに合わせて Azure Functions が拡張する方法を説明します。"
services: functions
documentationcenter: na
author: dariagrigoriu
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: dariagrigoriu, glenga
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 1c740ac1f98a07b08bdf922dde99ce54bac23ee5
ms.openlocfilehash: e41e246b081efbdf5edf70ee5de86cd2a68043b2
ms.lasthandoff: 03/01/2017


---
# <a name="choose-the-correct-service-plan-for-azure-functions"></a>Azure Functions の適切なサービス プランを選択する

## <a name="introduction"></a>はじめに

Azure Functions には、従量課金プランと App Service プランの&2; つの異なるサービス プランがあります。 従量課金プランでは、コードの実行時にコンピューティング能力を自動的に割り当て、負荷の処理の必要性に応じてスケールアウトし、コードを実行していないときはスケールインします。 つまり、アイドル状態の VM に対して課金されることはなく、必要になる前に容量を予約する必要もありません。 この記事では、従量課金サービス プランに焦点を当てます。 App Service プランの仕組みの詳細については、「[Azure App Service プランの詳細な概要](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)」を参照してください。 

まだ Azure Functions に慣れていない場合は、「[Azure Functions の概要](functions-overview.md)」を参照してください。

## <a name="service-plan-options"></a>サービス プラン オプション

関数アプリを作成するときに、アプリ内の関数のホスティング プランを構成する必要があります。 使用可能なホスティング プランは、**従量課金プラン**と [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)です。 この選択は現在、関数アプリの作成中に行う必要があります。 この&2; つのオプションは、作成後は変更できません。 [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)のレベル間でスケーリングすることはできます。 従量課金プランではスケーリングが動的に行われるため、現在、変更はサポートされていません。

### <a name="consumption-plan"></a>従量課金プラン

**従量課金プラン**では、関数アプリはコンピューティング処理インスタンスに割り当てられます。 必要に応じて、インスタンスが動的に追加されたり削除されたりします。 さらに、関数を並行して実行することで、要求の処理に必要な合計時間を最小限に抑えます。 各関数の実行時間は、含まれている関数アプリによって集計されます。 コストは、メモリ サイズと、関数アプリ内のすべての関数にわたる合計実行時間 (ギガバイト秒単位で測定) によって決まります。 これは、実際に使用したコンピューティング リソースに対してのみ支払うだけで済むため、コンピューティング ニーズが断続的であったり、ジョブ時間が非常に短い場合に最適なオプションです。 次のセクションでは、従量課金プランの仕組みの詳細について説明します。

### <a name="app-service-plan"></a>App Service プラン

**App Service プラン**では、関数アプリは専用の VM 上で実行されます。これは、現在、Web アプリが Basic、Standard、または Premium SKU で動作するのと同様です。 専用 VM は App Service アプリと関数アプリに割り当てられ、コードがアクティブに実行されているかどうかに関係なく、常に使用できます。 このプランは、他のコードを既に実行している既存の VM があるが使用率が低い場合、または (ほぼ) 継続的に関数を実行する予定がある場合に適したオプションです。 VM を使用すると、コストが実行時間とメモリ サイズの両方から切り離されます。 その結果、長時間実行される多数の関数のコストを、それらを実行する VM のコストに制限できます。 App Service プランの仕組みの詳細については、「[Azure App Service プランの詳細な概要](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)」を参照してください。 

## <a name="how-the-consumption-plan-works"></a>従量課金プランの仕組み

従量課金プランでは、関数アプリ内の関数の実行時の要件に基づいて処理インスタンスを追加することで、CPU とメモリ リソースが自動的にスケーリングされます。 各関数アプリの処理インスタンスには、最大で 1.5 GB のメモリ リソースが割り当てられます。

従量課金プランで実行しているときに関数アプリがアイドル状態になると、新しい BLOB の処理が最大で 10 分遅延する場合があります。 Function App が実行されはじめると、BLOB はより早く処理されます。 この初期段階での遅延を回避するには、Always On を有効にした状態で通常の App Service プランを使用するか、別のメカニズムを使用して、BLOB 名を含むキュー メッセージなどの BLOB 処理をトリガーします。 

### <a name="runtime-scaling"></a>実行時のスケーリング

Functions は、中央リスナーを使用して、構成されたトリガーに基づいてコンピューティングのニーズを評価し、スケールアウトまたはスケールインのタイミングを判断します。 中央リスナーは、継続的にメモリ要件とトリガー固有のデータ ポイントのヒントを処理します。 たとえば、Azure Queue Storage トリガーの場合、データ ポイントはキューの長さや最も古いエントリのキューの時間などです。

![](./media/functions-scale/central-listener.png)

スケーリングの単位は、関数アプリです。 この場合、スケールアウトは関数アプリのインスタンスの追加を意味します。 反対に、コンピューティングの需要が減ると、関数アプリのインスタンスが削除されます。 何も実行されていない場合、インスタンスの数は最終的に&0; にスケールダウンされます。 

### <a name="billing-model"></a>課金モデル

従量課金プランの課金の詳細については、[Azure Functions の価格](https://azure.microsoft.com/pricing/details/functions)に関するページをご覧ください。 使用量は、コードが実行されている時間の分だけ、関数アプリごとにレポートされます。 課金の単位は、次のとおりです。 
* メモリ サイズと、関数アプリで実行されているすべての関数の実行時間の組み合わせとして計算される、**GB-s (ギガバイト秒) でのリソース使用量**。 
* イベントに応じてバインドによってトリガーされる関数が実行されるたびにカウントされる**実行回数**。

