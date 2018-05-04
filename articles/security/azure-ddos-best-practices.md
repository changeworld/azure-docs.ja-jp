---
title: Azure DDoS Protection のベスト プラクティスと参照アーキテクチャ | Microsoft Docs
description: ログ データを使用して、アプリケーションに関する深い洞察を得る方法について説明します。
services: security
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/20/2018
ms.author: barclayn
ms.openlocfilehash: 4b2d785f5b9095a2decfc65ec46808ff6f65c38e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/23/2018
---
# <a name="azure-ddos-protection-best-practices-and-reference-architecture"></a>Azure DDoS Protection: ベスト プラクティスと参照アーキテクチャ

このドキュメントは、IT の意思決定者およびセキュリティ担当者を対象に書かれています。 Azure、ネットワーク、およびセキュリティについて理解している必要があります。

分散型サービス拒否 (DDoS) に対する回復性を設計するときは、さまざまな障害モードに対応した計画と設計が必要です。 このドキュメントでは、DDoS 攻撃に対する回復性を備えたアプリケーションを Azure で設計するためのベスト プラクティスを提供します。

## <a name="types-of-attacks"></a>攻撃の種類

DDoS は、アプリケーションのリソースを枯渇させることを目的として使われる攻撃の種類です。 目的は、正当な要求を処理するアプリケーションの可用性と能力に影響を与えることです。 攻撃は、いっそう高度なものになり、規模と影響も大きくなっています。 DDoS 攻撃は、インターネット経由で一般に到達可能なすべてのエンドポイントで実行できます。

Azure では、DDoS 攻撃に対する保護が継続的に提供されています。 この保護は、追加コストなしに、既定で Azure プラットフォームに統合されています。 プラットフォームで提供される DDoS の中核的な保護に加えて、"[Azure DDoS Protection Standard](https://azure.microsoft.com/services/ddos-protection/)" という名前の新しいプランもあります。このプランは、ネットワーク攻撃に対する高度な DDoS リスク軽減機能を提供し、特定の Azure リソースを保護するように自動的に調整されます。 保護は、新しい仮想ネットワークの作成時に簡単に有効にできます。 最初の作成の後で行うこともでき、アプリケーションまたはリソースを変更する必要はありません。

![](media/azure-ddos-best-practices/image1.png)

DDoS 攻撃は 3 つのカテゴリに大きく分類できます

### <a name="volumetric-attacks"></a>帯域幅消費型攻撃

帯域幅消費型攻撃は、DDoS 攻撃の最も一般的な種類です。 帯域幅消費型攻撃は、ネットワーク層とトランスポート層を対象とするブルート フォース攻撃です。 これらは、ネットワーク リンクなどのリソースを使い果たすことを試みます。 一般的に、これらの攻撃は、複数の感染したシステムを使用して、膨大な量の一見正当なトラフィックでネットワーク層をあふれさせます。 インターネット制御メッセージ プロトコル (ICMP)、ユーザー データグラム プロトコル (UDP)、伝送制御プロトコル (TCP) などのさまざまなネットワーク層プロトコルが、この種の攻撃に使われます。

最もよく使われるネットワーク層 DDoS 攻撃は、TCP SYN フラッド、ICMP エコー、UDP フラッド、DNS、および NTP 増幅攻撃です。 この種の攻撃は、サービスを中断するだけでなく、いっそう悪質で対象を絞ったネットワーク侵入に対する煙幕としても使われます。 最近の帯域幅消費型攻撃の例としては、GitHub に影響を与えた [Memcached エクスプロイト](https://www.wired.com/story/github-ddos-memcached/)があります。 この攻撃は UDP ポート 11211 を対象とし、1.35 Tb/秒の攻撃量が生成されました。

### <a name="protocol-attacks"></a>プロトコル攻撃

プロトコル攻撃の対象はアプリケーションのプロトコルです。 これらは、ファイアウォール、アプリケーション サーバー、ロード バランサーなどのインフラストラクチャ デバイスで利用可能なすべてのリソースを使用することを試みます。 プロトコル攻撃では、形式が正しくないパケットまたはプロトコル異常を含むパケットが使われます。 この攻撃は、多数のオープン要求を送信することによって行われ、サーバーと他の通信デバイスはそれに応答してパケット応答を待機します。 ターゲットはオープン要求への応答を試み、最終的にターゲット システムはクラッシュします。

プロトコル ベースの DDoS 攻撃の最も一般的な例は、TCP SYN フラッドです。 この攻撃では、ターゲットに対して連続的に送られる TCP SYN 要求が、ターゲットを過負荷にするために使われます。 目的は、ターゲットを応答不能にすることです。 2016 年の Dyn の停止も、アプリケーション層攻撃とは別に、Dyn の DNS サーバーのポート 53 を対象とする TCP SYN フラッドで構成されました。

### <a name="resource-attacks"></a>リソース攻撃

リソース攻撃の対象はアプリケーション レイヤーです。 リソース攻撃では、ターゲット システムを過負荷にすることを目的としてバックエンド プロセスがトリガーされます。 リソース攻撃では、一見正常に見えますが、CPU を大量に消費するクエリをサーバーに渡すトラフィックが悪用されます。 リソースを使い尽くすために必要なトラフィックの量は、他の種類の攻撃より少なくて済みます。 リソース攻撃のトラフィックは正当なトラフィックと区別できないため、検出するのは困難です。 最も一般的なリソース攻撃は、HTTP/HTTPS サービスおよび DNS サービスに対して行われます。

## <a name="shared-responsibility-in-the-cloud"></a>クラウドにおける共同責任

攻撃の種類の増加と高度化に対抗するには、多層防御戦略が必要です。 セキュリティは、お客様と Microsoft の共同責任です。 Microsoft ではこれを[共同責任モデル](https://azure.microsoft.com/blog/microsoft-incident-response-and-shared-responsibility-for-cloud-computing/)と呼んでいます。 次の図は、この責任の分担を示したものです。

![](media/azure-ddos-best-practices/image2.png)

Azure のユーザーには、ベスト プラクティスを確認し、障害に備えて設計およびテストされたグローバル分散アプリケーションを構築することによるメリットがあります。

## <a name="fundamental-best-practices"></a>基本的なベスト プラクティス

DDoS 攻撃は増加しています。 次のセクションでは、Azure で DDoS に対する回復力のあるサービスを構築するための規範的なガイダンスを提供します。

### <a name="design-for-security"></a>セキュリティ向けの設計

お客様は、設計と実装から、デプロイと運用まで、アプリケーションのライフ サイクル全体を通して、セキュリティを優先する必要があります。 アプリケーションには、比較的少量の精巧な要求によって膨大なリソースを使用し、結果としてサービスを停止させることができるような、バグが含まれる可能性があります。 Microsoft Azure で実行されるサービスを保護するには、お客様は、自分のアプリケーションのアーキテクチャをよく理解し、[ソフトウェア品質の 5 つの重要な要素](https://docs.microsoft.com/azure/architecture/guide/pillars)に注目する必要があります。
お客様は、標準的なトラフィックの量、アプリケーションと他のアプリケーションの間の接続モデル、パブリック インターネットに公開されるサービス エンドポイントについて知っておく必要があります。

また、アプリケーション自体を対象とするサービス拒否攻撃に対処するのに十分な回復力をアプリケーションに持たせることが、最も重要です。 Azure プラットフォームには、[セキュリティ開発ライフサイクル (Security Development Lifecycle: SDL)](https://www.microsoft.com/sdl/default.aspx) からセキュリティとプライバシーが組み込まれるようになっています。 SDL は、すべての開発段階でセキュリティに対処し、Azure がいっそう安全になるように継続的に更新されることを保証します。

### <a name="design-for-scalability"></a>スケーラビリティのための設計

拡張性は、増加した負荷を処理する、システムの能力です。 お客様は、[水平方向にスケーリングする](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out)ようにアプリケーションを設計し、増加した負荷の需要を満たす必要があります (特に、DDoS 攻撃の場合)。 アプリケーションがサービスの 1 つのインスタンスに依存する場合は、単一障害点が発生します。 複数のインスタンスをプロビジョニングすると、回復性とスケーラビリティの両方が改善されます。

[Azure App Service](../app-service/app-service-value-prop-what-is.md) の場合は、複数のインスタンスを提供する [App Service プラン](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)を選択してください。 Azure Cloud Services の場合は、[複数インスタンス](../cloud-services/cloud-services-choose-me.md)を使用するように各ロールを構成してください。 [Azure Virtual Machines (VM)](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-about/?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) の場合は、VM アーキテクチャに 1 つ以上の VM が含まれていることと、[可用性セット](../virtual-machines/virtual-machines-windows-manage-availability.md)に各 VM が含まれていることを確認してください。 自動スケーリング機能には[仮想マシン スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)を使うことをお勧めします。

### <a name="defense-in-depth"></a>多層防御

多層防御の基になっている考え方は、多様な防御戦略でリスクを管理することです。 アプリケーションのセキュリティ防御を多層化すると、攻撃が成功する可能性が減少します。 お客様には、Azure プラットフォームの組み込み機能を使って、アプリケーションのセキュリティ保護設計を実装することをお勧めします。

たとえば、攻撃のリスクは、アプリケーションの規模または攻撃対象領域と共に大きくなります。 攻撃対象領域を減らすには、ホワイトリストを通じてロード バランサーで不要な公開 IP アドレス空間やリスニング ポートを閉じることで ([ALB](../load-balancer/load-balancer-get-started-internet-portal.md)/[アプリ ゲートウェイ](../application-gateway/application-gateway-create-probe-portal.md))、または[ネットワーク セキュリティ グループ (NSG)](../virtual-network/virtual-networks-nsg.md) を介して行うことをお勧めします。
[サービス タグ](/virtual-network/security-overview.md)および[アプリケーション セキュリティ グループ](/virtual-network/security-overview.md)を使用して、セキュリティ規則作成の複雑さを軽減し、アプリケーションの構造の自然な延長としてネットワーク セキュリティを構成することができます。

お客様は、可能な限り、[仮想ネットワーク](../virtual-network/virtual-networks-overview.md)に Azure サービスを展開する必要があります。 このようにすると、サービス リソースはプライベート IP アドレスを通して通信できます。 仮想ネットワークからの Azure サービス トラフィックは、パブリック IP アドレスを発信元 IP アドレスとして既定で使用します。 [サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を使用すると、サービス トラフィックは、仮想ネットワークから Azure サービスにアクセスするときに、仮想ネットワークのプライベート アドレスを発信元 IP アドレスとして使用するように切り替わります。

Azure 内のリソースと供に、お客様のオンプレミスのリソースが攻撃されることがよくあります。 オンプレミスの環境を Azure に接続している場合は、オンプレミスのリソースのパブリック インターネットへの露出を最小限にすることをお勧めします。 よく知られたパブリック エンティティを Azure に展開することで、Azure のスケールと高度な DDoS 防御機能を使用できます。 これらのパブリックにアクセスできるエンティティは、DDoS 攻撃の対象になることがよくあるため、Azure 内に配置すると、オンプレミスのリソースへの影響が減少します。

## <a name="azure-ddos-protection"></a>Azure の DDoS 保護

Azure には、ネットワーク攻撃 (レイヤー 3 および 4) からの保護を提供する 2 つの DDoS サービス プランがあります。DDoS 基本保護と DDoS 標準保護です。 

### <a name="azure-ddos-basic-protection"></a>Azure の DDoS 基本保護

基本保護は、追加コストなしに、既定で Azure に統合されます。 Azure のグローバルに展開されるネットワークのフル スケールと容量が、常時有効なトラフィック監視とリアルタイムのリスク軽減によって、一般的なネットワーク層攻撃からの保護を提供します。 DDoS 基本保護には、ユーザーの構成またはアプリケーションの変更は不要です。 Azure DNS などの PaaS サービスを含むすべての Azure サービスが、DDoS 基本保護によって保護されます。

![](media/azure-ddos-best-practices/image3.png)

Azure の基本的な DDoS 保護は、ソフトウェア コンポーネントとハードウェア コンポーネントの両方で構成されます。 ソフトウェアのコントロール プレーンは、攻撃トラフィックを分析して除去するハードウェア アプライアンスにトラフィックを送る必要があるタイミング、場所、およびトラフィックの種類を決定します。 コントロール プレーンは、インフラストラクチャ全体にわたる DDoS 保護 "*ポリシー*" に基づいて、この決定を行います。このポリシーは、静的に設定され、すべての Azure ユーザーに普遍的に適用されます。

たとえば、DDoS Protection ポリシーでは、保護を "*トリガーする*" 必要がある (つまり、テナントのトラフィックをスクラブ アプライアンスにルーティングする必要がある) トラフィック量や、その後スクラブ アプライアンスで攻撃を "*軽減する*" 方法が指定されています。

Azure の DDoS Protection Basic サービスの対象は、インフラストラクチャの保護と、Azure プラットフォームの保護です。 このサービスは、マルチテナント環境の複数の顧客に影響を与える可能性があるほど非常に大きくレートを超えたトラフィックを軽減します。 警告機能や、顧客ごとのポリシーのカスタマイズは提供されません。

### <a name="azure-ddos-standard-protection"></a>Azure の DDoS 標準保護

標準保護は、強化された DDoS 軽減機能を提供し、仮想ネットワーク内の特定の Azure リソースを保護するために自動的に調整されます。 保護は新規または既存の仮想ネットワークで簡単に有効にでき、アプリケーションやリソースの変更は必要ありません。 ログ、アラート、テレメトリなど、基本サービスにはない利点がいくつかあります。 以下では、Azure DDoS Protection Standard サービスの主な違いについて説明します。

#### <a name="adaptive-realtime-tuning"></a>アダプティブ リアルタイム チューニング

Azure DDoS Protection Basic サービスはお客様の保護に役立ちますが、他のお客様への影響を防ぐために保護するだけです。 たとえば、インフラストラクチャ全体にわたる DDoS Protection ポリシーの "*トリガー レート*" より小さい正規の着信トラフィックの一般的な量に対してサービスがプロビジョニングされている場合、そのお客様のリソースに対する DDoS 攻撃は認識されない可能性があります。 さらに一般的には、最近の攻撃 (たとえば、複数ベクター DDoS) の複雑な性質と、テナントのアプリケーション固有の動作により、お客様ごとに保護ポリシーをカスタマイズする必要があります。
これは 2 つの分析情報を使って実現されます。

1. 顧客ごと (IP ごと) のレイヤー 3/4 トラフィック パターンの自動学習
2. Azure のスケールが大量のトラフィックを吸収できる場合の偽陽性の最小限化

![](media/azure-ddos-best-practices/image5.png)

#### <a name="ddos-protection-telemetry-monitoring--alerting"></a>DDoS 保護のテレメトリ、監視、アラート

DDoS Protection Standard では、DDoS 攻撃の間に [Azure Monitor](/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) によって豊富なテレメトリが公開されます。 DDoS Protection によって公開される Azure Monitor の任意のメトリックに対して、アラートを構成することができます。 Azure Monitor 診断インターフェースを介した高度な分析用に、ログを Splunk (Azure Event Hubs)、Azure Log Analytics、Azure Storage と統合できます。

##### <a name="ddos-mitigation-policies"></a>DDoS 軽減ポリシー

Azure Portal で **[モニター]** \> **[メトリック]** をクリックします。 **[メトリック]** 画面で、リソース グループ、パブリック IP アドレスのリソースの種類、Azure のパブリック IP アドレスを選択します。 DDoS のメトリックが、使用可能なメトリックのウィンドウに表示されます。

DDoS Protection Standard は、DDoS が有効になっている VNET 内で、保護されたリソースのパブリック IP アドレスごとに、**3 つの自動調整された軽減ポリシー (TCP SYN、TCP、UDP)** を適用します。 ポリシーのしきい値は、メトリック **[Inbound packets to trigger DDoS mitigation]\(DDoS 軽減をトリガーする着信パケット数\)** を選択することで確認できます。

![](media/azure-ddos-best-practices/image7.png)

ポリシーのしきい値は、機械学習ベースのネットワーク トラフィック プロファイルを使用して自動的に構成されます。 DDoS の軽減は、ポリシーのしきい値を超過した場合にのみ、攻撃を受けている IP アドレスに対して行われます。

##### <a name="under-ddos-attack"></a>DDoS 攻撃中

パブリック IP アドレスが攻撃を受けている場合、攻撃トラフィックに対して軽減が実行されると、メトリック [under DDoS attack or not]\(DDoS 攻撃中かどうか\) の値が 1 に切り替わります。

![](media/azure-ddos-best-practices/image8.png)

このメトリックに対してアラートを構成し、パブリック IP アドレスでアクティブな DDoS 軽減が実行されたときに通知を受け取ることをお勧めします。

詳しくは、「[Azure Portal を使用した Azure DDoS Protection Standard の管理](../virtual-network/ddos-protection-manage-portal.md)」をご覧ください。

#### <a name="resource-attacks"></a>リソース攻撃

アプリケーション レイヤーでのリソース攻撃に限り、お客様は Web アプリケーション ファイアウォール (WAF) を構成して Web アプリケーションのセキュリティ保護を強化する必要があります。 WAF は、着信 Web トラフィックを検査して、SQL インジェクション、クロスサイト スクリプティング、DDoS、その他のレイヤー 7 攻撃をブロックします。 Azure は、一般的な脆弱性やその悪用から Web アプリケーションを一元的に保護する [Application Gateway の機能として WAF](../application-gateway/application-gateway-web-application-firewall-overview.md) を提供します。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1) では Azure パートナーのさまざまな WAF プランを利用でき、ニーズにより適したものが見つかる可能性があります。

Web アプリケーション ファイアウォールを構成しても、帯域幅消費型攻撃や状態枯渇攻撃を受ける可能性があります。 WAF 仮想ネットワーク上で DDoS 標準保護を有効にして、帯域幅消費型攻撃やプロトコル攻撃から保護することを強くお勧めします。 詳しくは、参照アーキテクチャのセクションをご覧ください。

### <a name="protection-planning"></a>保護の計画

計画と準備は、DDoS 攻撃中にシステムがどのように動作するかを理解するために重要です。 この計画と準備は、インシデント管理対応計画の設計にも役立ちます。

お客様は、インターネットに接続するエンドポイントの仮想ネットワークで DDoS Protection Standard が有効になっていることを確認する必要があります。 DDoS アラートを構成すると、インフラストラクチャに対する攻撃の可能性を常時監視するのに役立ちます。 お客様は、お使いのアプリケーションを独自に監視する必要があります。 アプリケーションの通常の動作を理解する必要があります。 DDoS 攻撃中にアプリケーションが期待どおりに動作しない場合は、手順を実行する必要があります。

#### <a name="ddos-attacks-orchestration"></a>DDoS 攻撃のオーケストレーション

攻撃が発生していなくても、定期的にシミュレーションを実施して、攻撃に対するサービスの応答方法の想定をテストすることをお勧めします。 テストでは、サービスまたはアプリケーションが期待どおりに機能し続けること、およびエンド ユーザー エクスペリエンスが中断しないことを確認します。 テクノロジとプロセスの両方の観点からギャップを明らかにし、DDoS 対応戦略に組み込みます。 一般に、そのようなテストは、ステージング環境で実行するか、または運用環境への影響が最小限になるピーク時以外に実行することをお勧めします。

Microsoft は [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) と提携して、Azure のお客様がシミュレーションのために DDoS Protection を有効にしたパブリック エンドポイントに対してトラフィックを生成できるインターフェイスを構築しました。 [BreakPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) のシミュレーションでは、以下のことが可能です。

- Microsoft Azure DDoS Protection が Azure リソースを DDoS 攻撃から保護する方法を検証します

- DDoS 攻撃を受けているときにインシデント レスポンス プロセスを最適化します

- DDoS コンプライアンスを文書化します

- ネットワーク セキュリティ チームのトレーニングを行います

サイバーセキュリティは、防御技術の革新を常に必要とする絶え間ない戦闘です。 Azure の DDoS 標準保護は、複雑さを増す DDoS 攻撃を軽減するための効果的なソリューションを提供する最先端のプランです。

## <a name="components-of-a-ddos-response-strategy"></a>DDoS 対応戦略のコンポーネント

DDoS 攻撃の対象が Azure リソースである場合は、通常、エンド ユーザーの観点で必要な介入は最小限です。 それでも、組織のインシデント対応戦略の一環として DDoS 軽減を組み込むことで、ビジネス継続性への影響を最小限にできます。

### <a name="microsoft-threat-intelligence"></a>Microsoft の脅威インテリジェンス

Microsoft は、Microsoft のオンライン サービスをサポートする広範なセキュリティ コミュニティ、Microsoft パートナー、およびインターネット セキュリティ コミュニティ内のリレーションシップの集合的知識を使用する、広範な脅威インテリジェンス ネットワークを持っています。 重要なインフラストラクチャ プロバイダーである Microsoft は、脅威についての早期警告を受け取り、他の Microsoft Online Services や Microsoft のグローバルな顧客ベースから学習した脅威インテリジェンスを取得します。 Microsoft のすべての脅威インテリジェンスは、Azure DDoS Protection 製品に組み込まれます。

さらに、Microsoft の Digital Crimes Unit (DCU) は、DDoS 攻撃の指揮統制の一般的なソースであるボットネットに対して攻撃的戦略を実行します。

### <a name="perform-a-risk-evaluation-of-your-azure-resources"></a>Azure リソースのリスク評価を実行する

継続的に DDoS 攻撃のリスクの範囲を把握することが不可欠です。 お客様は定期的に次のことを確認する必要があります。新しくパブリックに使用可能になった Azure リソースで保護を必要とするものは何か。 サービスに単一障害点はあるか。 どのようにすれば、サービスを分離して攻撃の影響を制限しながら、有効な顧客がサービスを利用できるようにすることができるか。 DDoS Protection Standard を有効にする必要があるのに有効になっていない仮想ネットワークがあるか。 サービスは複数のリージョンをまたがるフェールオーバーについてアクティブ/アクティブになっているか。

### <a name="customer-ddos-response-team"></a>お客様の DDoS 対応チーム

DDoS 対応チームを作成することは、攻撃に効果的かつ迅速に対応するために重要なステップです。 お客様は、計画と実行の両方を監視する組織内のさまざまな連絡先を明らかにする必要があります。 DDoS 対応チームは、Azure DDoS Protection Standard サービスについてよく理解する必要があり、必要に応じて社内や Microsoft サポート チームなどの社外のさまざまな顧客と調整して、攻撃の識別と緩和を行う必要があります。

DDoS 対応チームの計画およびスケール テストなどのシミュレーション演習を、サービスの可用性と継続性の計画の通常部分として組み込むことをお勧めします。 

### <a name="during-an-attack"></a>攻撃中

Azure DDoS Protection Standard は、ユーザーの介入がなくても DDoS 攻撃を識別して軽減します。 保護されているパブリック IP に対してリスク軽減がアクティブになった場合に通知を受け取るには、メトリック "under DDoS attack or not (DDoS 攻撃中かどうか)" に対して[アラートを構成する](../virtual-network/ddos-protection-manage-portal.md)ことができます。 さらに、必要に応じて他の DDoS メトリックについてのアラートを確認して作成し、攻撃の規模やドロップされたトラフィックなどを把握することができます。

#### <a name="when-to-contact-microsoft-support"></a>どのようなときに Microsoft サポートに問い合わせるか

- DDoS 攻撃中に、保護されたリソースのパフォーマンスが著しく低下したり、リソースが使用できなくなったことを検出した場合。

- DDoS Protection サービスの動作が期待どおりではないと考えられる場合。 DDoS Protection サービスは、以下の条件が満たされる場合にのみ軽減策を開始します。

    - メトリック値 "Policy to trigger DDoS mitigation (TCP/TCP SYN/UDP) (DDoS 軽減をトリガーするポリシー (TCP、TCP SYN/UDP))" が、保護されたパブリック IP リソースで受信されるトラフィックを下回っている。

- ネットワーク トラフィックが大幅に増加する計画的なバイラル イベントが発生することがわかっている場合。

- リソースに対して DDoS 攻撃を行うという脅迫が攻撃者からあった場合。

ビジネスに影響する重要な問題については、重大度 A の[サポート チケット](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を作成します。

### <a name="post-attack-steps"></a>攻撃後の手順

攻撃を受けた後で事後分析を行い、必要に応じて DDoS 対応戦略を再調整するのは、常によい戦略です。 また、以下の点を考慮してください。

- スケーラブルなアーキテクチャがないため、サービスまたはエンド ユーザーのエクスペリエンスに中断が発生したか。

- どのアプリケーションまたはサービスが最も影響を受けたか。

- DDoS 対応戦略の効果はどの程度で、どうすればさらに改善できるか。

DDoS 攻撃を受けている可能性がある場合は、通常の Azure サポート チャネルを通じてエスカレートしてください。

## <a name="ddos-protection-reference-architectures"></a>DDoS 保護の参照アーキテクチャ

DDoS Protection Standard は、[仮想ネットワークにデプロイされるサービス用](../virtual-network/virtual-network-for-azure-services.md)に設計されています。 他のサービスには、既定の DDoS Protection Basic が適用されます。 以下に示す具体的な参照アーキテクチャは、アーキテクチャ パターンでグループ化されたシナリオで調整されています。

### <a name="virtual-machine-windowslinux-workloads"></a>仮想マシン (Windows/Linux) のワークロード

#### <a name="application-running-on-load-balanced-vms"></a>負荷分散された VM 上で実行しているアプリケーション

この参照アーキテクチャは、可用性とスケーラビリティを向上させるために、ロード バランサーの背後にあるスケール セット内で複数の Windows 仮想マシン (VM) を実行するための一連の実証済みの手法を示します。 このアーキテクチャは、Web サーバーなど、任意のステートレス ワークロードで使用できます。

![](media/azure-ddos-best-practices/image9.png)

このアーキテクチャでは、ワークロードが複数の VM インスタンスにわたって分散されます。 単一のパブリック IP アドレスが存在し、ロード バランサーを使用してインターネット トラフィックが VM に分散されます。 パブリック IP が関連付けられた Azure (インターネット) ロード バランサーの仮想ネットワークでは、DDoS Protection Standard が有効になります。

ロード バランサーは、受信インターネット要求を各 VM インスタンスに分散します。 VM スケール セットにより、VM の数を手動でスケールインまたはスケールアウトしたり、定義済みの規則に基づいて自動的に設定したりできるようになります。 これは、リソースが DDoS 攻撃を受けている場合に重要です。 この参照アーキテクチャについて詳しくは、[こちらの記事](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)をご覧ください。

#### <a name="applications-running-on-windows-n-tier"></a>Windows N 層で実行しているアプリケーション

N 層アーキテクチャを実装する方法は多数あります。 図は、典型的な 3 層 Web アプリケーションを示しています。 このアーキテクチャは「[スケーラビリティと可用性のために負荷分散された VM を実行する](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm)」に基づいて作成されています。 Web 層とビジネス層では、負荷分散された VM が使用されます。

![](media/azure-ddos-best-practices/image10.png)

上記のアーキテクチャでは、DDoS Protection Standard が仮想ネットワークで有効になっています。 仮想ネットワーク内のすべてのパブリック IP には、レイヤー 3/レイヤー 4 の DDoS Protection が適用されます。 レイヤー 7 を保護するには、WAF SKU の Application Gateway を展開する必要があります。 この参照アーキテクチャについて詳しくは、[こちらの記事](https://docs.microsoft.com/azure/architecture/reference-architectures/virtual-machines-windows/n-tier)をご覧ください。

#### <a name="paas-web-application"></a>PaaS Web アプリケーション

この参照アーキテクチャでは、単一リージョンでの Azure App Service アプリケーションの実行を示します。 このアーキテクチャは、[Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) と [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/) を使用する Web アプリケーションを対象とした一連の実証済みプラクティスを示しています。
フェールオーバー シナリオのためにスタンバイ リージョンを設定します。

![](media/azure-ddos-best-practices/image11.png)

Traffic Manager は、着信要求をいずれかのリージョンの Application Gateway にルーティングします。 通常の運用中は、アクティブなリージョンの Application Gateway に要求をルーティングします。 そのリージョンが使用できなくなった場合、Traffic Manager はスタンバイ リージョンの Application Gateway にフェールオーバーします。

インターネットから Web アプリケーションに宛てられたすべてのトラフィックは、Traffic Manager によって [Application Gateway のパブリック IP アドレス](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview)にルーティングされます。 このシナリオでは、アプリ サービス (Web アプリ) 自体は外部に対して直接公開されておらず、Application Gateway によって保護されています。 Application Gateway WAF SKU (禁止モード) を構成して、レイヤー 7 (HTTP/HTTPS/Web ソケット) の攻撃を防ぐことをお勧めします。 さらに、Web アプリは、ָ[Application Gateway の IP アドレスからのトラフィックのみを受け入れる](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)ように構成されます。

この参照アーキテクチャについて詳しくは、[こちらの記事](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)をご覧ください。

### <a name="mitigation-for-non-web-paas-services"></a>Web PaaS 以外のサービスに対するリスク軽減

#### <a name="hdinsight-on-azure"></a>Azure 上の HDInsight

この参照アーキテクチャでは、Azure 上の [HDInsight クラスター](https://docs.microsoft.com/azure/hdinsight/)に対する DDoS Protection Standard の構成を示します。 HDInsight クラスターが仮想ネットワークにリンクされていること、および DDoS Protection がその仮想ネットワークで有効になっていることを確認する必要があります。

![](media/azure-ddos-best-practices/image12.png)

![](media/azure-ddos-best-practices/image13.png)

このアーキテクチャでは、インターネットから HDInsight クラスター宛のトラフィックは、HDInsight ゲートウェイのロード バランサーに関連付けられているパブリック IP にルーティングされます。 ゲートウェイのロード バランサーは、トラフィックをヘッド ノードまたはワーカー ノードに直接送信します。 HDInsight の仮想ネットワークで DDoS Protection Standard が有効になっていると、仮想ネットワーク内のすべてのパブリック IP アドレスにはレイヤー 3/レイヤー 4 の DDoS Protection が適用されます。 上記の参照アーキテクチャは、N 層/マルチリージョンの参照アーキテクチャと組み合わせることができます。

この参照アーキテクチャについて詳しくは、「[Azure Virtual Network を使用した Azure HDInsight の拡張](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。

### <a name="azure-api-management"></a>Azure API Management

この参照アーキテクチャでは、組織の外部にいる顧客に API を公開する [API Management](../api-management/api-management-key-concepts.md) リソースのパブリック エンドポイントを保護します。 DDoS 保護 を有効にするには、外部仮想ネットワークに APIM を展開する必要があります。

![](media/azure-ddos-best-practices/image15.png)

外部仮想ネットワークを構成することにより、API Management のゲートウェイと開発者ポータルには、パブリック インターネットからパブリック ロード バランサーを使用してアクセスできます。 このアーキテクチャでは、APIM 仮想ネットワークの外部仮想ネットワークで DDoS Protection Standard を有効にします。 トラフィックはインターネットから APIM のパブリック IP アドレスにルーティングされ、パブリック IP アドレスはレイヤー 3/レイヤー 4 のネットワーク攻撃から保護されます。 レイヤー 7 の HTTP/HTTPS 攻撃から保護するには、WAF モードで Application Gateway を構成できます。

仮想ネットワークに展開され、DDoS Protection Standard 用に構成できるその他のサービスの一覧については、[こちら](../virtual-network/virtual-network-for-azure-services.md)をご覧ください。 DDoS Protection Standard は、Azure Resource Manager のリソースのみをサポートします。 *パブリック IP アドレスのある VNET に挿入されたアプリケーション サービス環境 (ASE) の展開は、ネイティブではサポートされません。* ASE 環境の保護について詳しくは、このセクションをご覧ください。

## <a name="next-steps"></a>次の手順

* [Azure DDoS Protection 製品のページ](https://azure.microsoft.com/services/ddos-protection/)

* [Azure DDoS Protection のブログ](http://aka.ms/ddosblog)

* [Azure DDoS Protection のドキュメント](../virtual-network/ddos-protection-overview.md)
