---
title: Azure Network Performance Monitor の価格についてよくあるご質問 | Microsoft Docs
description: よくあるご質問 - Azure Network Performance Monitor
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: cherylmc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: ajaycode
ms.openlocfilehash: 5b2335ee2584af07ed23ce87be92a869f3a07ba1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Azure Network Performance Monitor の価格の変更

お客様のフィードバックを参考にして、Azure のさまざまな監視サービスに[新しい価格エクスペリエンス](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)が最近導入されました。

このドキュメントでは、Azure [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM) に関連する価格の変更を、読みやすい質問と回答の形式で説明します。

Network Performance Monitor は 3 つのコンポーネントで構成されます。
* [パフォーマンス モニター](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [サービス エンドポイント モニター](https://docs.microsoft.com/azure/networking/network-monitoring-overview#service-endpoint-monitor)
* [ExpressRoute モニター](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

以下のセクションでは、これらコンポーネントの価格の変更について説明します。

## <a name="performance-monitor-pm"></a>パフォーマンス モニター (PM)

**古いモデルではパフォーマンス モニターの使用量はどのように請求されていましたか。**

NPM の請求は、2 つのコンポーネントの使用量/消費量に基づいていました。
* ノード数: すべての代理トランザクションは、ノードで開始して終了します。 ノードはエージェントまたは MMA (Microsoft Monitoring Agent) とも呼ばれます。
* データ量: さまざまなネットワーク テストの結果は、Log Analytics リポジトリに格納されます。

古いモデルの請求額は、ノードの数と、生成されるデータの量に基づいて計算されました。 

**新しいモデルではパフォーマンス モニターの使用量はどのように課金されますか。**

NPM のパフォーマンス モニター機能は、次の組み合わせで課金されるようになりました。 

* 監視されたサブネット リンク数
* データ量

**サブネット リンクとは何ですか。**

パフォーマンス モニターは、ネットワーク上の複数の場所の間の接続を監視します。  あるサブネット上のノード/エージェントのグループと、別のサブネット上のノードのグループの間の接続を、サブネット リンクと呼びます。

**2 つのサブネット (サブネット A と B) があり、各サブネット上に複数のエージェントがあります。パフォーマンス モニターは、サブネット A 上のすべてのエージェントからサブネット B 上のすべてのエージェントへの接続を監視します。料金はサブネット間の接続の数に基づいて請求されますか。**

いいえ。 請求の目的では、サブネット A からサブネット B へのすべての接続が 1 つのサブネット リンクにまとめられ、1 つの接続について請求されます。  パフォーマンス モニターは引き続き、各サブネット上のさまざまなエージェントの間の接続を監視します。

**サブネット リンクの監視にはどのようなコストがかかりますか。**

1 つのサブネット リンクの 1 か月間の監視コストについては、「[Ping メッシュ](https://azure.microsoft.com/pricing/details/network-watcher/)」というタイトルのセクションをご覧ください。

**パフォーマンス モニターによって生成されるデータはどのように課金されますか。**

データ インジェスト (Log Analytics へのデータのアップロード、処理、インデックス付け) の料金については、Log Analytics の[価格に関するページ](https://azure.microsoft.com/pricing/details/log-analytics/)をご覧ください  (セクション: データ インジェスト)。

データ保有 (つまり、最初の月を超える、顧客のオプションで保持されるデータ) の料金については、[価格に関するページ](https://azure.microsoft.com/pricing/details/log-analytics/)をご覧ください  (セクション: データ保持)。


## <a name="expressroute-monitor-erm"></a>ExpressRoute モニター (ERM)

**ExpressRoute モニターの使用量はどのように課金されますか。**

ExpressRoute モニターの料金は、監視中に生成されたデータの量に基づいて請求されます。   詳しくは、質問「パフォーマンス モニターによって生成されるデータはどのように課金されますか」を ご覧ください。

**ERM を使って複数の ExpressRoute 回線を監視しています。監視対象の回線の数に基づいて課金されるのですか。**

回線の数またはピアリングの種類 (プライベート ピアリング、Microsoft ピアリングなど) に基づいて課金されることはありません。  上で説明したように、データの量に課金されます。

**1 つの回線を監視する場合、生成されるデータの量はどれくらいですか。**

プライベート ピアリング接続を監視する場合、1 か月に生成されるデータの量は次のようになります。

|パーセンタイル      |データ/月 (MB)|
| :---:          |           ---:|
|50<sup> </sup> |            192|
|60<sup> </sup> |            256|
|70<sup> </sup> |            360|
|80<sup> </sup> |            498|
|90<sup> </sup> |            870|
|95<sup> </sup> |           1560|


上のテーブルに従うと、50 パーセンタイルのお客様は、192 MB のデータに対して課金されます。 最初の月は 2.30 米国ドル/GB なので、最初の月に回線の監視にかかるコストは 0.43 米国ドル (= 192 * 2.30 / 1024) です。

**データ量の変動にはどのような理由がありますか。**

生成される監視データの量は、次のような複数の要因に依存します。
* エージェントの数 - エージェントの数が多いほど、障害分離の精度が向上します
* ネットワーク上のホップの数
* 送信元と送信先の間のパスの数

パーセンタイルが高いお客様 (上の表) は、通常、オンプレミスのネットワーク上の複数の有利なポイントから回線を監視しています。  また、サービス プロバイダーのエッジ ルーターから離れたネットワークの深い場所に、複数のエージェントを配置しています。 多くの場合、エージェントは複数のユーザー サイト、ブランチ、およびデータ センターのラックに配置されます。

## <a name="service-endpoint-monitor-sepm"></a>サービス エンドポイント モニター (SEPM)

**サービス エンドポイント モニターの使用量はどのように課金されますか。**

サービス エンドポイント モニターの使用量の料金は、以下に基づいて計算されます。
* 接続の数
* データの量

**接続とは何ですか。**

接続とは、1 か月間に行われる、1 つのエージェントから 1 つのエンドポイント (URL またはネットワーク サービス) に対する到達可能性のテストです。 たとえば、3 つのエージェントから bing.com への接続を監視すると、接続の数は 3 つになります。

**サービス エンドポイント モニターのコストはどれくらいですか。**

- 1 か月間のエンドポイント監視コストについては、「[接続監視](https://azure.microsoft.com/pricing/details/network-watcher/)」セクションをご覧ください。
- データの料金については、Log Analytics の[価格に関するページ](https://azure.microsoft.com/pricing/details/log-analytics/)をご覧ください  (セクション: データ インジェスト)。

## <a name="references"></a>参照

- [Log Analytics の価格に関する FAQ](https://azure.microsoft.com/pricing/details/log-analytics/) - FAQ のセクションには、Free レベルやノード単位の価格などについての情報があります。

