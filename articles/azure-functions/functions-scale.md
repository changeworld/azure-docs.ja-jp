---
title: Azure Functions のスケーリング方法 | Microsoft Docs
description: ユーザーのイベント ドリブン ワークロードのニーズに合わせて Azure Functions が拡張する方法を説明します。
services: functions
documentationcenter: na
author: dariagrigoriu
manager: erikre
editor: ''
tags: ''
keywords: Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ

ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/03/2016
ms.author: dariagrigoriu

---
# Azure Functions のスケーリング方法
## はじめに
Azure Functions の利点は、コンピューティング リソースが必要な場合にのみ使用されることです。つまり、アイドル状態の VM に対して課金されたり、必要なときに備えて容量を予約したりする必要がありません。代わりに、プラットフォームは、コードの実行時に処理能力を割り当て、負荷の処理の必要性に応じてスケールアップし、コードを実行していないときはスケールダウンします。

この新しい機能のためのメカニズムは、動的サービス プランです。

この記事では、動的サービス プランの動作と、コードを実行するために必要に応じてプラットフォームが拡張する方法の概要を示します。

Azure Functions についてまだよくわからない場合は、「[Azure Functions の概要](functions-overview.md)」を読んで機能を理解してください。

## Azure Functions の構成
スケーリングに関しては 2 つの主要な設定があります。

* [Azure App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)または動的サービス プラン
* 実行環境のメモリ サイズ

関数のコストは、選択したサービス プランによって変わります。動的サービス プランの場合、コストは実行時間、メモリ サイズ、および実行回数によって変わります。料金は、コードが実際に実行されている間のみ発生します。

App Service プランでは、既存の VM 上の関数をホストします。これは、他のコードの実行にも使用される可能性があります。これらの VM の毎月の料金を払う以外に、VM 上で関数を実行するための追加料金はありません。

## サービス プランの選択
関数を作成するときに、動的サービス プランと [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)のどちらで実行するかを選択できます。App Service プランでは、関数は現在動いている Web Apps (Basic、Standard、Premium SKU) と同じように、専用の VM 上で実行します。この専用 VM はアプリと関数に割り当てられ、コードがアクティブに実行されているかどうかに関係なく、常に使用できます。このプランは、他のコードを既に実行している既存の VM があるが使用率が低い場合、または (ほぼ) 継続的に関数を実行する予定がある場合に適したオプションです。VM を使用すると、コストが実行時間とメモリ サイズの両方から切り離されます。その結果、長時間実行される多数の関数のコストを、それらを実行する 1 つ以上の VM のコストに制限できます。

[!INCLUDE [動的サービス プラン](../../includes/functions-dynamic-service-plan.md)]

<!---HONumber=AcomDC_0803_2016-->