---
title: IoT Hub の IP アドレスについて | Microsoft Docs
description: IoT Hub の IP アドレスとそのプロパティにクエリを実行する方法について説明します。 IoT Hub の IP アドレスは、ディザスター リカバリーやリージョン間フェールオーバーなど、特定のシナリオを通じて変更される可能性があります。
author: eross-msft
ms.author: lizross
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: b4d3dd6f8ffe3016f5d9842d4b562141ae73dee4
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548731"
---
# <a name="iot-hub-ip-addresses"></a>IoT Hub の IP アドレス

IoT Hub  パブリック エンドポイントの IP アドレス プレフィックスは、_AzureIoTHub_ [サービス タグ](../virtual-network/service-tags-overview.md)で定期的に発行されます。

> [!NOTE]
> オンプレミス ネットワーク内にデプロイされているデバイスの場合、Azure IoT Hub は、プライベート エンドポイントとの VNET 接続の統合をサポートします。 詳細については、[IoT Hub による VNet のサポート](./virtual-network-support.md)に関する記事を参照してください。


これらの IP アドレス プレフィックスを使用して、さまざまなネットワーク分離の目標を実装するために、IoT Hub とデバイスまたはネットワーク資産間の接続を制御することができます。

| 目標 | 該当するシナリオ | アプローチ |
|------|-----------|----------|
| デバイスとサービスが IoT Hub エンドポイントのみと通信するようにする | [Device-to-cloud](./iot-hub-devguide-messaging.md)、および [cloud-to-device](./iot-hub-devguide-messages-c2d.md) メッセージング、[ダイレクト メソッド](./iot-hub-devguide-direct-methods.md)、[デバイスとモジュールのツイン](./iot-hub-devguide-device-twins.md) および [デバイス ストリーム](./iot-hub-device-streams-overview.md) | _AzureIoTHub_ および _EventHub_ サービス タグを使用して IoT Hub およびイベント ハブの IP アドレス プレフィックスを検出し、それに応じて、デバイスとサービスのファイアウォール設定の許可規則を構成します。デバイスやサービスが通信する必要のない他の宛先 IP アドレスにトラフィックをドロップします。 |
| IoT Hub デバイスのエンドポイントが、使用しているデバイスとネットワーク資産からのみ接続を受信するようにする | [Device-to-cloud](./iot-hub-devguide-messaging.md)、および [cloud-to-device](./iot-hub-devguide-messages-c2d.md) メッセージング、[ダイレクト メソッド](./iot-hub-devguide-direct-methods.md)、[デバイスとモジュールのツイン](./iot-hub-devguide-device-twins.md) および [デバイス ストリーム](./iot-hub-device-streams-overview.md) | 使用しているデバイスとネットワーク資産の IP アドレスからの接続を許可するには、IoT Hub [IP フィルター機能](iot-hub-ip-filtering.md) を使用します ([制限事項](#limitations-and-workarounds)に関するセクションを参照してください)。 | 
| ルートのカスタム エンドポイント リソース (ストレージ アカウント、Service Bus、およびイベント ハブ) がネットワーク資産からのみアクセス可能であることを確認する | [メッセージ ルーティング](./iot-hub-devguide-messages-d2c.md) | 接続の制限に関するリソースのガイダンスに従います (たとえば、[ファイアウォール規則](../storage/common/storage-network-security.md)、[プライベート リンク](../private-link/private-endpoint-overview.md)、[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md) 経由など)。_AzureIoTHub_ サービス タグを使用して IoT Hub IP アドレス プレフィックスを検出し、リソースのファイアウォール構成の IP プレフィックスに対して許可規則を追加します ([制限事項](#limitations-and-workarounds)に関するセクションを参照してください)。 |

## <a name="best-practices"></a>ベスト プラクティス

* IoT ハブの IP アドレスは、予告なしに変更される可能性があります。 中断を最小限に抑えるために、可能な限り、ネットワーキングとファイアウォールの構成には IoT ハブ ホスト名 (例: myhub.azure-devices.net) を使用します。 

* ドメイン名解決 (DNS) を使用しない制約付き IoT システムの場合、変更を有効にする前に、IoT Hub IP アドレス範囲がサービス タグを使用して定期的に公開されます。 そのため、最新のサービス タグを定期的に取得して使用するためのプロセスを開発することが重要です。 このプロセスは、[Service Tag Discovery API](../virtual-network/service-tags-overview.md#service-tags-on-premises) 経由で自動化できます。 Service Tag Discovery API はまだプレビュー段階であり、場合によってはタグと IP アドレスの完全な一覧が生成されないことがあります。 Discovery API が一般提供されるまでは、[ダウンロード可能な JSON 形式で サービス タグ](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)を使用することを検討してください。 


* 特定のリージョン内の IoT Hub エンドポイントによって使用される IP プレフィックスを識別するには、*AzureIoTHub.[リージョン名]* タグを使用します。 データセンターのディザスター リカバリーまたは[リージョン内フェールオーバー](iot-hub-ha-dr.md)に対処するには、IoT Hub の geo ペア リージョンの IP プレフィックスへの接続も有効になっていることを確認してください。

* IoT Hub でファイアウォール ルールを設定すると、IoT Hub に対して Azure CLI および PowerShell コマンドを実行するために必要な接続がブロックされる可能性があります。 これを回避するには、クライアントの IP アドレスのプレフィックスに ALLOW ルールを追加し、CLI または PowerShell クライアントが再び IoT Hub と通信できるようにします。  

* デバイスのファイアウォール構成で許可規則を追加する場合、[適用されるプロトコルで使用される特定のポート](./iot-hub-devguide-protocols.md#port-numbers)を指定することをお勧めします。

## <a name="limitations-and-workarounds"></a>制限事項と回避策

* IoT Hub IP フィルター機能では、規則の数は 100 個に制限されています。 この上限は、Azure カスタマー サポートへリクエストすることで上げることができます。 

* 構成済みの [IP フィルタリング規則](iot-hub-ip-filtering.md) は、IoT Hub の組み込みイベント ハブ エンドポイントではなく、IoT Hub IP エンドポイントにのみ適用されます。 また、メッセージが格納されているイベント ハブに IP フィルターを適用する必要がある場合は、独自のイベント ハブ リソースを使用して、目的の IP フィルタリング規則を直接構成することもできます。 これを行うには、独自のイベント ハブ リソースをプロビジョニングし、[メッセージ ルーティング](./iot-hub-devguide-messages-d2c.md) を設定して、IoT Hub の組み込みイベント ハブの代わりにメッセージをそのリソースに送信する必要があります。 最後に、上の表で説明したように、メッセージ ルーティング機能を有効にするには、IoT Hub の IP アドレス プレフィックスからプロビジョニング済みのイベント ハブリソースへの接続を許可する必要もあります。

* ストレージ アカウントにルーティングする場合、IoT Hub の IP アドレス プレフィックスからのトラフィックを許可できるのは、ストレージ アカウントが IoT Hub とは異なるリージョンにある場合のみです。

## <a name="support-for-ipv6"></a>IPv6 のサポート 

現在、IPv6 は IoT Hub ではサポートされていません。
