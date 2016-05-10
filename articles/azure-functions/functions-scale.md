<properties
   pageTitle="Azure Functions のスケーリング方法 | Microsoft Azure"
   description="ユーザーのイベント ドリブン ワークロードのニーズに合わせて Azure Functions が拡張する方法を説明します。"
   services="functions"
   documentationCenter="na"
   authors="eduardolaureano"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="reference"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="03/09/2016"
   ms.author="edlaure"/>
  
# Azure Functions のスケーリング方法
     
## はじめに

Azure Functions の利点の 1 つは、リソースが実行中のコードで必要なだけ消費されることです。つまり、アイドル状態の VM に対して課金されたり、必要なときに備えて容量を予約したりする必要がありません。代わりに、プラットフォームは、コードの実行時に処理能力を割り当て、負荷の処理の必要性に応じてスケールアップし、コードが実行していないときは再び処理能力を下げます。

この新しい機能のためのメカニズムは、動的サービス プランです。この新しいサービス プランは必要に応じてスケールアップする動的なコンテナーをコードに提供する一方で、課金はコードが使用したメモリの量と、ギガバイト秒単位で測定された実行時間に対してのみ行われます。

この記事では、動的サービス プランの動作と、コードを実行するために必要に応じてプラットフォームが拡張する方法の概要を示します。

Azure Functions にまだ慣れていない場合は、「[Azure Functions の概要](functions-overview.md)」を読んで機能を理解してください。

## 関数アプリの構成

スケーリングに関しては 2 つの主要な設定があります。

* [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)または動的サービス プラン 
* 実行環境のメモリ サイズ 

関数のコストは、選択したサービス プランの種類によって変わります。動的サービス プランでは、コストは実行時間、メモリ サイズ、および実行数の関数です。実際にコードを実行したときにのみ変更が発生します。

通常のサービス プランでは、他のコードの実行にも使用される可能性のある既存の VM で関数をホストできます。これらの VM の毎月の料金を払う以外に、VM 上で関数を実行するための追加料金はありません。

## サービス プランの選択

関数アプリを作成するときに、動的サービス プラン (新機能) または通常の [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)のどちらで実行するかを選択できます。App Service プランでは、関数は現在動いている Web アプリ (Basic、Standard、Premium SKU) と同じように、専用の VM 上で実行します。この専用 VM では、アプリと関数のどちらか一方または両方に割り当てられ、アクティブに実行されているコードに関係なく使用できます。このプランは、他のコードを既に実行している既存の VM があり、それがフル稼働していない場合、または関数を継続的またはほぼ継続的に実行する予定がある場合に、適したオプションです。VM を使用するとコストが実行時間とメモリ サイズの両方から切り離され、長時間実行される多数の関数のコストを、それらが実行する 1 つまたは複数の VM のコストに制限できます。

[AZURE.INCLUDE [動的サービス プラン](../../includes/functions-dynamic-service-plan.md)]

<!---HONumber=AcomDC_0427_2016-->