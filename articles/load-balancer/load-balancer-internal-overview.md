---
title: Azure 内部ロード バランサーの概要 | Microsoft Docs
description: Azure の内部ロード バランサーの機能と内部エンドポイントを構成するためのシナリオについて説明します。
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: 36065bfe-0ef1-46f9-a9e1-80b229105c85
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0511165225f5a336291e86e0c504e60989933f3c
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2018
---
# <a name="overview-of-azure-internal-load-balancer"></a>Azure 内部ロード バランサーの概要



Azure 内部ロード バランサー (ILB) は、クラウド サービス内のリソースまたは VPN を使用して Azure インフラストラクチャにアクセスするリソースにのみトラフィックを送信します。 この点で、ILB はインターネットに接続するロード バランサーとは異なります。 Azure インフラストラクチャでは、クラウド サービスの負荷分散仮想 IP (VIP) アドレスまたは仮想ネットワークへのアクセスが制限されます。 VIP アドレスと仮想ネットワークは、インターネット エンドポイントに直接公開されることはありません。 社内の基幹業務アプリケーションは Azure で実行され、Azure 内またはオンプレミス リソースからアクセスされます。

## <a name="why-you-might-need-an-internal-load-balancer"></a>内部ロード バランサーが必要な理由

内部ロード バランサーは、クラウド サービス内またはリージョン スコープを持つ仮想ネットワーク内にある仮想マシン (VM) 間で負荷を分散します。 リージョン スコープを持つ仮想ネットワークについては、Azure ブログの「[Regional Virtual Networks (リージョン仮想ネットワーク)](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) 」をご覧ください。 アフィニティ グループに構成されている既存の仮想ネットワークでは ILB を使用できません。

ILB により、次の種類の負荷分散が可能になります。

* クラウド サービス内: VM から、同じクラウド サービス内に存在する一連の VM に負荷を分散する。 こちらの<a href="#figure1">例</a>を参照してください。
* 仮想ネットワーク内: 仮想ネットワーク内の VM から、仮想ネットワークの同じクラウド サービス内に存在する一連の VM に負荷を分散する。 こちらの<a href="#figure2">例</a>を参照してください。
* クロスプレミス仮想ネットワークの場合: オンプレミスのコンピューターから、仮想ネットワークの同じクラウド サービス内に存在する一連の VM に負荷を分散する。 こちらの<a href="#figure3">例</a>を参照してください。
* 多層アプリケーションの場合: バックエンド層がインターネットに接続しない、インターネットに接続する多層アプリケーションの負荷を分散する。 バックエンド層では、インターネットに接続する層からのトラフィックを負荷分散する必要があります。
* 基幹業務アプリケーションの場合: ロード バランサーのハードウェアやソフトウェアを追加せずに、Azure でホストされている基幹業務アプリケーションの負荷を分散する。 このシナリオには、トラフィックが負荷分散されるコンピューターのセットに含まれるオンプレミスのサーバーが含まれます。

## <a name="load-balancing-for-internet-facing-multi-tier-applications"></a>インターネットに接続する多層アプリケーションの負荷分散

Web 層はインターネット クライアント用のインターネットに接続するエンドポイントを持ち、負荷分散セットに含まれています。 ILB は、TCP ポート 443 (HTTPS) の Web クライアントからの受信トラフィックを Web サーバーに分散します。

データベース サーバーは、Web サーバーがストレージに使用する ILB エンドポイントの背後にあります。 この ILB エンドポイントは、データベース サービスの負荷分散エンドポイントです。 トラフィックは、ILB セット内のデータベース サーバー間で負荷分散されます。

次の図は、同じクラウド サービス内のインターネットに接続する多層アプリケーションの内部負荷分散を示しています。

<a name="figure1"></a>
![インターネットに接続する多層アプリケーション](./media/load-balancer-internal-overview/IC736321.png)

多層アプリケーションで使用できるもう 1 つのシナリオがあります。 ロード バランサーは、ILB のサービスを使用するクラウド サービスとは異なるクラウド サービスにデプロイされます。

同じ仮想ネットワークを使用するクラウド サービスは、ILB エンドポイントにアクセスできます。 次の図は、データベース バックエンドとは異なるクラウド サービス内にあるフロントエンド Web サーバーを示しています。 フロントエンド サーバーは、同じ仮想ネットワーク内の ILB エンドポイントをバックエンドとして使用します。

<a name="figure2"></a>
![別のクラウド サービス内のフロントエンド サーバー](./media/load-balancer-internal-overview/IC744147.png)

## <a name="load-balancing-for-intranet-line-of-business-applications"></a>イントラネットの基幹業務アプリケーションの負荷分散

オンプレミス ネットワーク上のクライアントからのトラフィックは、Azure ネットワークへの VPN 接続を使用する基幹業務サーバーのセットに負荷分散されます。

クライアント コンピューターは、ポイント対サイト VPN を使用して、Azure VPN サービスから IP アドレスにアクセスできます。 基幹業務アプリケーションは、ILB エンドポイントの背後でホストできます。

<a name="figure3"></a>
![ILB エンドポイントの背後でホストされている基幹業務アプリケーション](./media/load-balancer-internal-overview/IC744148.png)

基幹業務アプリケーションのもう 1 つのシナリオは、ILB エンドポイントが構成されている仮想ネットワークへのサイト間 VPN です。 オンプレミス ネットワーク トラフィックは、ILB エンドポイントにルーティングされます。

<a name="figure4"></a>
![ILB エンドポイントにルーティングされるオンプレミス ネットワーク トラフィック](./media/load-balancer-internal-overview/IC744150.png)

## <a name="limitations"></a>制限事項

内部ロード バランサー構成では、SNAT はサポートされていません。 この記事では、SNAT はポート マスカレード ソース ネットワーク アドレス変換を伴うシナリオを指します。 ロード バランサー プール内の VM は、それぞれの内部ロード バランサーのフロントエンド IP アドレスに到達する必要があります。 フローの発生元の VM にフローを負荷分散すると、接続エラーが発生します。 これらのシナリオは、ILB ではサポートされていません。 代わりに、プロキシ スタイルのロード バランサーを使用する必要があります。

## <a name="next-steps"></a>次の手順

* [Azure Resource Manager による Azure Load Balancer のサポート](load-balancer-arm.md)
* [インターネットに接続するロード バランサーの構成の開始](load-balancer-get-started-internet-arm-ps.md)
* [内部ロード バランサーの構成の開始](load-balancer-get-started-ilb-arm-ps.md)
* [ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)
* [ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)
