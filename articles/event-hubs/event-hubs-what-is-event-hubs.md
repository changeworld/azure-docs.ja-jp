---
title: Azure Event Hubs とは | Microsoft Docs
description: Azure Event Hubs の概要と説明
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''

ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: sethm

---
# Azure Event Hubs とは
Azure Event Hubs は、1 秒間に数百万件のイベントを取り込むことができる高度にスケーラブルなデータ受信サービスであり、接続されたデバイスとアプリケーションで生成される大量のデータを処理および分析できます。Event Hubs はイベント パイプラインの "玄関" として機能し、Event Hubs に収集されたデータは、任意のリアルタイム分析プロバイダーまたはバッチ処理/ストレージ アダプターを使用して変換および格納できます。Event Hubs はイベント ストリームの生成とイベントの使用を分離し、イベント コンシューマーが独自のスケジュールでイベントにアクセスできるようにします。技術的な詳細などについては、「[Event Hubs の概要](event-hubs-overview.md)」を参照してください。

## Event Hubs の機能
Event Hubs は、低遅延の動作と高い信頼性を確保しながら、膨大なイベントとテレメトリ処理を提供するイベント処理サービスです。このサービスは次のような場合に特に便利です。

* アプリケーションのインストルメンテーション
* ユーザー エクスペリエンスやワークフロー処理
* モノのインターネット (IoT) のシナリオ

Event Hubs の他の重要な機能としては、モバイル アプリでのビヘイビアー追跡、Web ファームからのトラフィック情報、コンソール ゲームにおけるゲーム中のイベント キャプチャ、産業機器またはコネクテッド カーから収集されたテレメトリなどがあります。

## 次のステップ
Event Hubs の詳細については、次のトピックを参照してください。

* [Event Hubs の概要](event-hubs-overview.md)
* [Event Hubs のプログラミング ガイド](event-hubs-programming-guide.md)
* [イベント ハブの可用性とサポートに関する FAQ](event-hubs-availability-and-support-faq.md)
* [Event Hubs の使用][Event Hubs の使用]
* [Event Hub を使用する完全なサンプル アプリケーション][Event Hub を使用する完全なサンプル アプリケーション]

[Event Hubs の使用]: event-hubs-csharp-ephcs-getstarted.md
[Event Hub を使用する完全なサンプル アプリケーション]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097

<!---HONumber=AcomDC_0907_2016-->