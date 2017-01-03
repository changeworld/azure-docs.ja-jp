---
title: "Azure Functions のスケーリング方法 | Microsoft Docs"
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
ms.date: 08/03/2016
ms.author: dariagrigoriu
translationtype: Human Translation
ms.sourcegitcommit: 1a8e8bdb95b1eff6feb992727d28051463710813
ms.openlocfilehash: fce766358c61494e77464a4506dcc0e466e4e1e4


---
# <a name="scaling-azure-functions"></a>Azure Functions のスケーリング

## <a name="introduction"></a>はじめに

Azure Functions プラットフォームは、コードの実行時に処理能力を割り当て、負荷の処理の必要性に応じてスケールアウトし、コードを実行していないときはスケールインします。 つまり、アイドル状態の VM に対して課金されることはなく、必要になる前に容量を予約する必要もありません。 この機能のためのメカニズムが、従量課金サービス プランです。 この記事では、従量課金サービス プランのしくみについて、概要を説明します。 

まだ Azure Functions に慣れていない場合は、「[Azure Functions の概要](functions-overview.md)」を参照してください。

## <a name="choose-a-service-plan"></a>サービス プランの選択

関数アプリを作成するときに、アプリ内の関数のホスティング プランを構成する必要があります。 使用可能なホスティング プランは、**従量課金プラン**と [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)です。 現在、この選択は、関数アプリの作成中に行う必要があります。 この 2 つのオプションは、作成後は変更できません。 [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)のレベル間でスケーリングすることはできます。 従量課金プランではスケーリングが動的に行われるため、現在、変更はサポートされていません。

### <a name="consumption-plan"></a>従量課金プラン

**従量課金プラン**では、関数アプリはコンピューティング処理インスタンスに割り当てられます。 必要に応じて、インスタンスが動的に追加されたり削除されたりします。 さらに、関数を並行して実行することで、要求の処理に必要な合計時間を最小限に抑えます。 各関数の実行時間は、関数を含んでいる関数アプリによって集計されます。 コストは、メモリ サイズと、関数アプリ内のすべての関数にわたる合計実行時間 (ギガバイト秒単位で測定) によって決まります。 これは、実際に使用したコンピューティング リソースに対してのみ支払うだけで済むため、コンピューティング ニーズが断続的であったり、ジョブ時間が非常に短い場合に最適なオプションです。 

### <a name="app-service-plan"></a>App Service プラン

**App Service プラン**では、関数アプリは専用の VM 上で実行されます。これは、現在、Web アプリが Basic、Standard、または Premium SKU で動作するのと同様です。 専用 VM は App Service アプリと関数アプリに割り当てられ、コードがアクティブに実行されているかどうかに関係なく、常に使用できます。 このプランは、他のコードを既に実行している既存の VM があるが使用率が低い場合、または (ほぼ) 継続的に関数を実行する予定がある場合に適したオプションです。 VM を使用すると、コストが実行時間とメモリ サイズの両方から切り離されます。 その結果、長時間実行される多数の関数のコストを、それらを実行する 1 つ以上の VM のコストに制限できます。

## <a name="consumption-service-plan"></a>従量課金サービス プラン

従量課金サービス プランでは、関数アプリ内の関数の実行時のニーズに基づいて処理インスタンスを追加することで、CPU とメモリ リソースが自動的にスケーリングされます。 各関数アプリの処理インスタンスには、最大で 1.5 GB のメモリ リソースが割り当てられます。

### <a name="runtime-scaling"></a>実行時のスケーリング

Azure Functions プラットフォームは、中央リスナーを使用して、構成されたトリガーに基づいてコンピューティングのニーズを評価し、スケールアウトまたはスケールインのタイミングを判断します。 中央リスナーは、常にメモリ要件とトリガー固有のデータ ポイントのヒントを処理します。 たとえば、Azure Queue Storage トリガーの場合、データ ポイントはキューの長さや最も古いエントリのキューの時間などです。

![](./media/functions-scale/central-listener.png)

スケーリングの単位は、関数アプリです。 この場合、スケールアウトは関数アプリのインスタンスの追加を意味します。 反対に、コンピューティングの需要が減ると、関数アプリのインスタンスが削除され、何も実行されていない場合、最終的に 0 にスケールインされます。 

### <a name="billing-model"></a>課金モデル

従量課金サービス プランの課金の詳細については、[Azure Functions の価格](https://azure.microsoft.com/pricing/details/functions)に関するページを参照してください。 使用量は、コードが実行されている時間の分だけ、関数アプリごとにレポートされます。 課金の単位は、次のとおりです。 
* メモリ サイズと、関数アプリで実行されているすべての関数の実行時間の組み合わせとして計算される、**GB-s (ギガバイト秒) でのリソース使用量**。 
* イベントに応じてバインドによってトリガーされる関数が実行されるたびにカウントされる**実行回数**。



<!--HONumber=Nov16_HO4-->


