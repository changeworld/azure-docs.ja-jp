---
title: Azure のための信頼できるインターネット接続ガイダンス
description: Azure と SaaS サービスのための信頼できるインターネット接続ガイダンス
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: 9d71efa35713500911c67d1df15612b64c8e97da
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990831"
---
# <a name="trusted-internet-connection-guidance"></a>信頼できるインターネット接続ガイダンス

## <a name="background"></a>バックグラウンド

TIC (Trusted Internet Connections/信頼できるインターネット接続) イニシアティブの目的は、連邦機関によって現在利用されている個々の外部ネットワーク接続のセキュリティを最適化し、標準化することです。 この方針の概要は、OMB (行政管理予算局) の[覚え書き M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf) にあります。

2007 年 11 月、OMB は連邦ネットワーク周辺のセキュリティとインシデントに対する対応機能を改善する目的で TIC プログラムを作成しました。 TIC は、.gov のあらゆる送受信トラフィックをネットワーク分析し、署名やパターンベース データを特定し、ボットネット アクティビティなど、異常な行動を発見するように設計されています。 外部ネットワーク接続を統合すること、限られたネットワーク エンド ポイント (TIC と呼ばれる) でホストされる侵入の検出/防止デバイス (EINSTEIN) を通過するようにあらゆるトラフィックの経路を指定することが機関に命じられました。

簡単に言えば、TIC の目的は機関に次のことを認識してもらうことです。
- 誰が自分たちのネットワーク上にいるのか (承認されているか、無許可か)?
- 自分たちのネットワークはいつ、どのような理由でアクセスされているのか?
- どのようなリソースがアクセスされているのか?

現在、機関のあらゆる外部接続に、OMB が承認した TIC を通過するように経路指定する必要があります。 連邦機関には、TICAP (TIC アクセス プロバイダー) として TIC プログラムに参加するか、MTIPS (Managed Trusted Internet Protocol Service) プロバイダーと呼ばれている、主要なティア 1 インターネット サービス プロバイダーの 1 つとサービス契約することで TIC プログラムに参加することが求められます。  TIC には重要な必須の機能が含まれ、それは機関と MTIPS プロバイダーによって現在実行されています。 現行版の TIC では、EINSTEIN バージョン 2 侵入検出デバイスと EINSTEIN バージョン 3A (3 Accelerated) 侵入防止デバイスが各 TICAP と各 MTIPS にデプロイされています。機関は DHS (国土安全保障省) と了解の覚書を交わし、EINSTEIN 機能を連邦システムにデプロイします。

.gov ネットワークの保護というその責任の一環として、DHS は連邦エンタープライズ全体のインシデントを関連付け、特別なツールで分析する目的で、機関の未加工ネット フロー データのフィードを要求します。 DHS ルーターは、インターフェイスに出入りする IP ネットワーク トラフィックを収集する機能を提供します。 ネット フロー データを分析することで、ネットワーク管理者はトラフィックの送信元、送信先、サービスのクラスなどを判断できます。ネット フロー データは "コンテンツ以外のデータ" (ヘッダー、送信元 IP、送信先 IP など) と見なされ、DHS はコンテンツを取り巻く情報を、つまり、誰が何をどのくらいの間していたかなどを知ることができます。

このイニシアティブには、セキュリティに関する方針、ガイドライン、オンプレミス インフラストラクチャを前提とするフレームワークも含まれています。 政府機関がコスト削減、運用効率性、技術革新を達成するためにクラウドに移行する中、TIC の実装要件は、一部のケースでは、ネットワーク トラフィックを遅くし、政府ユーザーが自分のクラウドベース データにアクセスするときのスピードとアジャイル性を制限します。

この記事では、政府機関が Microsoft Azure Government を使用し、IaaS サービスと PaaS サービスの両方において TIC ポリシーに準拠する方法について説明します。

## <a name="summary-of-azure-networking-options"></a>Azure ネットワーク オプションの概要

Azure サービスに接続するとき、主なオプションが 3 つあります。

1. 直接インターネット接続: 開放されているインターネットを介して Azure サービスに直接接続します。 媒体は接続と同様にパブリックです。 プライバシーの確保はアプリケーションとトランスポートのレベルの暗号化に依存します。 帯域幅はサイトのインターネットへの接続性によって制限されます。複数のアクティブ プロバイダーを利用することで回復性を確保できます。
1. 仮想プライベート ネットワーク: VPN Gateway を使用し、Azure Virtual Network にプライベート接続します。
媒体はパブリックです。サイトの標準インターネット接続を通過しますが、接続はトンネルで暗号化され、プライバシーが確保されます。 帯域幅の上限は、選択した VPN デバイスと構成に依存します。 Azure のポイント対サイト接続の場合、通常、100 Mbps に制限されます。サイト対サイト接続の場合、1.25 Gbps に制限されます。
1. Microsoft ExpressRoute: ExpressRoute の場合、Microsoft サービスに直接接続します。 分離されたファイバー チャネルを介するため、この接続は、使用される構成に基づいてパブリックまたはプライベートになります。 帯域幅は一般的に最大 10 Gbps に制限されます。

DHS の "Trusted Internet Connections (TIC) Reference Architecture Document, Version 2.0" にある TIC 付録 H (クラウドに関する考慮事項) 要件はいくつかの方法で満たすことができます。 このドキュメントを通して、DHS TIC ガイダンスは TIC 2.0 と呼ばれています。

省庁または機関 (D/A) の TIC を通過するようにトラフィックに経路指定することなく、D/A から Azure または Office 365 への接続を有効にするには、D/A は暗号化トンネルかクラウド サービス プロバイダー (CSP) への専用接続を利用する必要があります。 CSP サービスは、機関の人間に直接アクセスするとき、D/A クラウド アセットへの接続が公共のインターネットに与えられないように取り計らいます。

O365 は、[Microsoft ピアリング](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains)を有効にして Express Route を利用するか、TLS 1.2 ですべてのトラフィックを暗号化するインターネット接続を利用して TIC 2.0 付録 H に準拠します。  D/A ネットワークの D/A エンド ユーザーは、機関ネットワークと TIC インフラストラクチャを介してインターネット接続できます。 O365 へのリモート インターネット アクセスはすべてブロックされ、機関を通過するように経路指定されます。 D/A は Microsoft ピアリングを有効にした Express Route で O365 に接続することもできます。Microsoft ピアリングはパブリック ピアリングの一種です。  

Azure の場合のみ、オプション 2 (VPN) とオプション 3 (ExpressRoute) は、インターネットへのアクセスを制限するサービスと併用するとき、これらの要件を満たすことができます。

![Microsoft TIC の図](media/tic-diagram-a.png)

## <a name="how-azure-infrastructure-as-a-service-offerings-can-help-with-tic-compliance"></a>Azure IaaS (サービスとしてのインフラストラクチャ) で TIC に準拠する方法

IaaS を利用して TIC ポリシーに準拠する方法は比較的単純です。Azure の顧客が自分の仮想ネットワークの経路を管理するからです。

TIC 参照アーキテクチャに準拠するための主な要件は、VNet (仮想ネットワーク) が D/A ネットワークのプライベートな拡張になるように取り計らうことです。 _プライベート_な拡張にするために、オンプレミス TIC ネットワーク接続経由以外、トラフィックをネットワークから出さないようにすることがポリシーで要求されます。 このプロセスは "トンネリングの強制適用" と呼ばれています。これは、TIC コンプライアンスに利用されるとき、CSP 環境にあるあらゆるシステムからのトラフィックをすべて、組織のネットワークにあるオンプレミス ゲートウェイを通過し、TIC でインターネットに入るように経路指定するプロセスとなります。

Azure IaaS TIC コンプライアンスは大きく 2 つの手順に分割できます。

1. 構成
1. 監査

### <a name="azure-iaas-tic-compliance-configuration"></a>Azure IaaS TIC コンプライアンス構成

Azure で TIC 準拠アーキテクチャを構成するには、まず、仮想ネットワークへの直接的なインターネット アクセスを防ぎ、それからオンプレミス ネットワークを通過するようにインターネット トラフィックに強制する必要があります。

#### <a name="prevent-direct-internet-access"></a>直接インターネット アクセスの禁止

Azure IaaS ネットワーキングは、サブネットから構成される仮想ネットワーク経由で実施されます。このサブネットには、仮想マシンの NIC (ネットワーク インターフェイス コントローラー) が関連付けられます。

TIC コンプライアンスをサポートする最も単純なシナリオは、仮想マシンか仮想マシンの集合があらゆる外部リソースに接続できなくすることです。 外部ネットワークの遮断は NSG (ネットワーク セキュリティ グループ) を利用して確保できます。NSG は仮想ネットワーク内の 1 つまたは複数の NIC またはサブネットへのトラフィックを制御するために利用できます。 NSG には、トラフィックの方向、プロトコル、ソース アドレスとポート、および送信先アドレスとポートに基づいて、トラフィックを許可または拒否するアクセス制御ルールが含まれています。 NSG のルールは、いつでも変更でき、変更は関連付けられているすべてのインスタンスに適用されます。  NSG の作成方法の詳細については、NSG 作成に関する[こちらの](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal)記事をご覧ください。

#### <a name="force-internet-traffic-through-on-premises-network"></a>オンプレミス ネットワークを通過することをインターネット トラフィックに強制する

Azure では、システム ルートが自動的に作成され、仮想ネットワークの各サブネットに割り当てられます。 システム ルートは作成することも削除することもできませんが、システム ルートをカスタム ルートでオーバーライドすることはできます。 Azure では、サブネットごとに既定のシステム ルートが作成されます。また、Azure の特定の機能を使用するときは、特定のサブネットまたはすべてのサブネットにオプションの既定のルートが追加されます。 この経路指定により、仮想ネットワーク内を宛先とするトラフィックは仮想ネットワーク内にとどまり、10.0.0.0/8 など、IANA 指定のプライベート アドレス空間は破棄され (仮想ネットワークのアドレス空間に含まれていない限り)、"最後の手段" である 0.0.0.0/0 の経路が仮想ネットワークのインターネット エンドポイントに指定されます。

![TIC のトンネリング強制](media/tic-diagram-c.png)

すべてのトラフィックに D/A TIC を通過させるには、仮想ネットワークから出るすべてのトラフィックがオンプレミス接続を通過するように経路指定する必要があります。  カスタム ルートを作成するには、ユーザー定義ルートを作成するか、オンプレミス ネットワーク ゲートウェイと Azure 仮想ネットワーク ゲートウェイの間でボーダー ゲートウェイ プロトコル (BGP) ルートを交換します。 ユーザー定義のルートに関する詳細は https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined にあります。 ボーダー ゲートウェイ プロトコルに関する詳細は https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol にもあります。

#### <a name="user-defined-routes"></a>ユーザー定義のルート

ルートベースの仮想ネットワーク ゲートウェイを利用している場合、送信する UDR (ユーザー定義ルート) 設定の 0.0.0.0/0 トラフィックを仮想ネットワーク ゲートウェイの "次のホップ" に追加することで、トンネリングを強制できます。 Azure はシステム定義ルートよりユーザー定義ルートを優先させます。そのため、結果的に、VNet 以外のトラフィックはすべて仮想ネットワーク ゲートウェイに送信され、仮想ネットワーク ゲートウェイがトラフィックをオンプレミスに送信します。 ユーザー定義ルートは定義後、Azure 環境にあるすべての仮想ネットワーク内にすべてのサブネットに関連付ける必要があります。既存のサブネットにも、新規作成されたサブネットにも関連付けます。

![ユーザー定義ルートと TIC](media/tic-diagram-d.png)

#### <a name="border-gateway-protocol"></a>ボーダー ゲートウェイ プロトコル

ExpressRoute またはボーダー ゲートウェイ プロトコル (BGP) 対応の仮想ネットワーク ゲートウェイを使用している場合、ルートをアドバタイズするための機構としては BGP が推奨されます。 BGP がルート 0.0.0.0/0 をアドバタイズするとき、ExpressRoute と BGP 対応仮想ネットワーク ゲートウェイは、このデフォルト ルートが仮想ネットワーク内のすべてのサブネットに適用されるようにします。

### <a name="azure-iaas-tic-compliance-auditing"></a>Azure IaaS TIC コンプライアンス監査

Azure では、いくつかの方法で TIC コンプライアンスを監査できます。

#### <a name="effective-routes"></a>有効なルート

デフォルト ルートが伝達されていることを確認するには、特定の VM、特定の NIC、ユーザー定義ルート テーブルの "有効なルート" を確認するという方法があります。 これは Azure Portal か PowerShell で行うことができます。Azure Portal の説明は https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal に、PowerShell の説明は https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell にあります。 [有効なルート] ブレードには、関連ユーザー定義ルート、BGP アドバタイズ ルート、関連エンティティに適用されるシステム ルートが表示されます。下の画像をご覧ください。

![ルートのスクリーンショット](media/tic-screen-1.png)

**注**: 実行中の VM に関連付けられていない限り、NIC の有効なルートは表示できません。

#### <a name="network-watcher"></a>Network Watcher

Azure Network Watcher には、TIC コンプライアンスの監査に利用できるツールがいくつかあります。  Network Watcher の詳細は https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview にあります。

##### <a name="network-security-groups-flow-logs"></a>ネットワーク セキュリティ グループのフロー ログ 

Azure Network Watcher には、IP トラフィック関連のメタデータを示す、ネットワーク フロー ログを取得する機能があります。 その他のデータに加え、ネットワーク フロー ログには、ターゲットの送信元アドレスと送信先アドレスが含まれています。 このログと、仮想ネットワーク ゲートウェイ、オンプレミス エッジ デバイス、TIC からのログを合わせることで、すべてトラフィックが確かにオンプレミスで送信されていることを監視できます。 

## <a name="how-azure-platform-as-a-service-offerings-can-help-with-tic-compliance"></a>Azure PaaS (サービスとしてのプラットフォーム) で TIC に準拠する方法

Azure Storage などの Azure PaaS サービスにはインターネットでアクセス可能な URL を介してアクセスできます。 承認された資格情報が与えられた人なら誰でも、TIC を通過しなくても、あらゆる場所からストレージ アカウントなどのリソースにアクセスできます。 そのような理由から、政府関連の顧客の多くは、Azure PaaS サービスが TIC ポリシーに準拠しないという誤った判断を下します。 実際には、多くの Azure PaaS サービスは、VNet に接続できる場合、上記のように TIC 準拠 IaaS 環境と同じアーキテクチャを利用して TIC ポリシーに準拠できます。 Azure PaaS サービスと Azure VNet を統合することで、その VNet からサービスにプライベート アクセスできます。また、ユーザー定義ルートや BGP を介して 0.0.0.0/0 のカスタム ルーティングを適用できます。インターネットに送信されるすべてのトラフィックがオンプレミスで送信され、TIC を通過します。  一部の Azure サービスは次のパターンを使用して Vnet に統合できます。

- **サービスの専用インスタンスをデプロイする**: VNet 接続エンドポイントを利用し、専用インスタンスとしてデプロイできる PaaS サービスが増えています。 たとえば、ASE (App Service Environment) を "分離" モードでデプロイし、そのネットワーク エンドポイントを VNet に閉じ込めることができます。 その後、この ASE で、Web アプリ、API、関数など、多くの Azure PaaS サービスをホストできます。
- **VNet サービス エンドポイント**: エンドポイントをパブリック アドレスではなく VNet プライベート IP に移動できる PaaS サービスが増えています。

2018 年 5 月の時点で、VNet またはサービス エンドポイントに専用インスタンスをデプロイできるサービスは次の一覧表のようになっています *(Azure Commercial で利用できます。Azure Government での利用は保留になっています)。

### <a name="service-endpoints"></a>サービス エンドポイント

|サービス                   |状態            |
|--------------------------|------------------|
|Azure KeyVault            | プライベート プレビュー  |
|Cosmos DB                 | プライベート プレビュー  |
|ID                  | プライベート プレビュー  |
|Azure Data Lake           | プライベート プレビュー  |
|Sql Postgress/Mysql       | プライベート プレビュー  |
|Azure SQL Data Warehouse  | パブリック プレビュー   |
|Azure SQL                 | 一般公開               |
|Storage                   | 一般公開               |

### <a name="vnet-injection"></a>VNet インジェクション

|サービス                            |状態            |
|-----------------------------------|------------------|
|SQL Managed Instance               | パブリック プレビュー   |
|Azure Container Service (AKS)       | パブリック プレビュー   |
|Service Fabric                     | 一般公開               |
|API Management                     | 一般公開               |
|Azure Active Directory             | 一般公開               |
|Azure Batch                        | 一般公開               |
|ASE                                | 一般公開               |
|Redis                              | 一般公開               |
|HDI                                | 一般公開               |
|Compute virtual machine scale set  | 一般公開               |
|Compute Cloud Service              | 一般公開               |

### <a name="vnet-integration-details"></a>VNet 統合の詳細

以下の図は、VNet インジェクションと VNet サービス トンネリングの両方を利用し、PaaS サービスにアクセスするための一般的なフローを示しています。  ネットワーク サービス ゲートウェイ、VNet、サービス タグに関する詳細は https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags にあります。

![TIC の PaaS 接続オプション](media/tic-diagram-e.png)

1. ExpressRoute を利用し、Azure にプライベート接続します。 ExpressRoute プライベート ピアリングと強制トンネリングを利用し、顧客の VNet トラフィックをすべて、ExpressRoute 経由でオンプレミスに戻すように強制します。 Microsoft ピアリングは不要です。
2. ExpressRoute Microsoft ピアリングと併用される Azure VPN Gateway を利用し、顧客の VNet とオンプレミス エッジの間でエンドツーエンドの IPSec 暗号化をオーバーレイできます。 
3. 顧客の VNet へのネットワーク接続は NSG (ネットワーク セキュリティ グループ) で制御され、IP、ポート、プロトコルに基づく許可/拒否を顧客に許可します。
4. 顧客の VNet は、顧客のサービスにサービス エンドポイントを作成することで、PaaS サービスまで拡張されます。
5. PaaS サービス エンドポイントはセキュリティで保護され、既定ですべて拒否し、顧客の VNet 内の指定サブネットからのアクセスのみを許可します。  既定の拒否には、インターネットからの接続も含まれます。
6. 顧客の VNet 内のリソースにアクセスする必要があるその他の Azure サービスには、次のいずれかであることが求められます。  
  a. VNet に直接デプロイされている  
  b. それぞれの Azure サービスからのガイダンスに基づいて選択的に許可されている

#### <a name="option-1-dedicated-instance-vnet-injection"></a>オプション 1: 専用インスタンス "VNet インジェクション"

VNet インジェクションを利用すると、顧客は、HDInsight など、所与の Azure サービスの専用インスタンスを独自の VNet に選択的にデプロイできます。 サービス インスタンスは、顧客の VNet の専用サブネットにデプロイされます。 VNet インジェクションによって、インターネット以外のルーティング可能アドレス経由でサービス リソースにアクセスできます。  オンプレミス インスタンスはこれらのサービス インスタンスに、公共のインターネット アドレス空間にファイアウォールを開く代わりに、ExpressRoute またはサイト対サイト VPN 経由で直接、VNet アドレス空間からアクセスできます。 専用インスタンスをエンドポイントに接続することで、IaaS TIC コンプライアンスに使用されるものと同じ方針を活用できます。既定のルーティングで、インターネットに宛てられたトラフィックがオンプレミス宛ての仮想ネットワーク ゲートウェイにリダイレクトされます。 内向き/外向きアクセスは、所与のサブネットの NSG (ネットワーク セキュリティ グループ) にさらに制御できます。

![VNet インジェクションの概要図](media/tic-diagram-f.png)

#### <a name="option-2-vnet-service-endpoints"></a>オプション 2: VNet サービス エンドポイント 

Azure VNet に統合するための代替手法である "サービス エンドポイント" 機能を提供する Azure のマルチテナント サービスが増えています。 VNet サービス エンドポイントは、直接接続によって VNet IP アドレス空間を拡張し、VNet の ID をサービスに提供します。  VNet から Azure サービスへのトラフィックは常に、Azure のバックボーン ネットワーク内に残ります。 あるサービスのサービス エンドポイントが有効になると、そのサービスによって適用されるポリシーを通して、サービスへの接続がその VNet に制限されます。 アクセス チェックは Azure サービスによってプラットフォームで強制されます。ロックされているリソースへのアクセスは、許可されている VNet/サブネットから要求が送られてきたか、ExpressRoute を使用している場合、オンプレミス トラフィックの識別に 2 つの IP が使用されている場合にのみ与えられます。 これは、内向き/外向きトラフィックが PaaS サービスから直接離れることを効果的に防ぐために利用できます。

![サービス エンドポイントの概要図](media/tic-diagram-g.png)

## <a name="using-cloud-native-tools-for-network-situational-awareness"></a>ネットワークの状況認識にクラウド ネイティブのツールを使用する

Azure には、ネットワークのトラフィック フローを理解するために必要な状況認識に役立つクラウド ネイティブのツールがあります。 TIC ポリシーの準拠には必要ありませんが、セキュリティ機能を大幅に改善できます。

### <a name="azure-policy"></a>Azure Policy

Azure Policy (https://azure.microsoft.com/services/azure-policy/)) は、コンプライアンス イニシアティブを監査し、強制するためのすぐれた機能を組織に与える Azure サービスです。  現在のところ、Azure Commercial Clouds でパブリック プレビュー版をご利用いただけますが、Microsoft Azure for Government ではご利用いただけません。TIC にご関心がおありのお客様は、将来のコンプライアンスのために自分のポリシー ルールの計画とテストを開始できます。 Azure Policy はサブスクリプション レベルを対象とし、イニシアティブ、ポリシー定義、監査結果、強制結果、例外を管理するための一元的インターフェイスを提供します。 さまざまな組み込みの Azure Policy 定義に加え、管理者は簡単な JSON テンプレートを利用して独自の定義を作成できます。Microsoft では、可能な場合、強制より監査を優先させることをお勧めしています。

次のサンプル ポリシーは、TIC コンプライアンス シナリオに役立つことがあります。

|ポリシー  |サンプル シナリオ  |最初のテンプレート  |
|---------|---------|---------|
|ユーザー定義ルート テーブルの強制 |     すべての仮想ネットワークのデフォルト ルートを、オンプレミスに送信するための承認済み仮想ネットワーク ゲートウェイに向けます。 | https://docs.microsoft.com/azure/azure-policy/scripts/no-user-def-route-table |
|リージョンで Network Watcher が有効になっていない場合の監査  | 使用されているすべてのリージョンで Network Watcher を有効にします。  | https://docs.microsoft.com/azure/azure-policy/scripts/net-watch-not-enabled |
|すべてのサブネット上の NSG X  | インターネット トラフィックがブロックされている NSG (あるいは承認済み NSG のセット) をすべての VNet のすべてのサブネットに適用します。 | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-subnet |
|すべての NIC 上の NSG X | インターネット トラフィックがブロックされている NSG をすべての VM のすべての NIC に適用します。 | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-nic |
|VM ネットワーク インターフェイスでの承認された vNet の使用  | すべての NIC を承認済みの VNet に置きます。 | https://docs.microsoft.com/azure/azure-policy/scripts/use-approved-vnet-vm-nics |
|許可される場所 | VNet と Network Watcher 構成が方針に準拠しているリージョンにすべてのリソースをデプロイします。  | https://docs.microsoft.com/azure/azure-policy/scripts/allowed-locs |
|PublicIP など、許可されないリソースの種類  | コンプライアンス プランのない種類のリソースのデプロイを禁止します。 たとえば、このポリシーを利用し、パブリック IP アドレス リソースのデプロイを禁止できます。 NSG ルールを利用し、内向きインターネット トラフィックを効果的にブロックできる一方で、パブリック IP の使用を禁止することで、攻撃をさらに減らすことができます。    | https://docs.microsoft.com/azure/azure-policy/scripts/not-allowed-res-type  |

### <a name="azure-traffic-analyticshttpsazuremicrosoftcomen-inblogtraffic-analytics-in-preview"></a>Azure [Traffic Analytics](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/)

Azure Network Watcher の Traffic Analytics はフロー ログ データやその他のログを利用し、ネットワーク トラフィックの概要を提供します。 このデータは、TIC コンプライアンスの監査と問題点の特定に役立ちます。 概要ダッシュボードを利用し、インターネットと通信している VM をすばやく選別できます。その VM からさらに、TIC ルーティングを絞り込んだリストが与えられます。

![Traffic Analytics スクリーンショット](media/tic-traffic-analytics-1.png)

"Geo マップ" を利用し、VM のインターネット トラフィックの考えられる物理的宛先をすばやく特定できます。疑わしい場所やパターン変化を特定し、選別できます。

![Traffic Analytics スクリーンショット](media/tic-traffic-analytics-2.png)

ネットワーク トポロジ マップを利用し、既存の仮想ネットワークをすばやく調査できます。

![Traffic Analytics スクリーンショット](media/tic-traffic-analytics-3.png)

### <a name="azure-network-watcher-next-hop"></a>Azure Network Watcher の次のホップ

Network Watcher で監視されるリージョンのネットワークで "次のホップ" テストを実行し、簡単な歩タールベースのアクセスでサンプル ネットワーク フローのソースや宛先を入力できます。それに対して、Network Watcher は "次のホップ" の宛先を解決します。 これを VM やサンプルのインターネット アドレスに対して使用し、"次のホップ" が確かに仮想ネットワーク ゲートウェイであることを確認できます。

![Network Watcher の次のホップ](media/tic-network-watcher.png)

## <a name="conclusions"></a>まとめ

2018 年 5 月に記述され、定義された TIC 2.0 付録 H ガイダンスに準拠するように、Microsoft Azure、Office 365、Dynamics 365 アクセスを簡単に構成できます。  Microsoft はこのガイダンスに変更の可能性があることを認識しており、新しいガイダンスが発行されたらすぐにお客様にお届けするように努力します。

## <a name="appendix-tic-patterns-for-common-workloads"></a>付録: 一般的ワークロードの TIC パターン

| Category | ワークロード | IaaS | 専用 PaaS / VNet インジェクション  | サービス エンドポイント  |
|---------|---------|---------|---------|--------|
| コンピューティング | Linux Virtual Machines | はい | | |
| コンピューティング | Windows Virtual Machines | はい | | |
| コンピューティング | Virtual Machine Scale Sets | はい | | |
| コンピューティング | Azure Functions | | App Service Environment (ASE) 経由 | |
| Web とモバイル | 内部 Web アプリケーション | | App Service Environment (ASE) 経由 | |
| Web とモバイル | 内部モバイル アプリケーション | | App Service Environment (ASE) 経由 | |
| Web とモバイル | API Apps | | App Service Environment (ASE) 経由 | |
| Containers | Azure Container Service (ACS) | | | はい |
| Containers | Azure Container Service (AKS)* | | | はい |
| データベース | SQL Database | | Azure SQL Database Managed Instance* | Azure SQL |
| データベース | Azure Database for MySQL | | | はい |
| データベース | Azure Database for PostgreSQL | | | はい |
| データベース | SQL Data Warehouse | | | はい |
| データベース | Azure Cosmos DB | | | はい |
| データベース | Redis Cache | | はい | |
| Storage | BLOB | はい | | |
| Storage | ファイル | はい | | |
| Storage | キュー | はい | | |
| Storage | テーブル | はい | | |
| Storage | ディスク | はい | | |

*: 2018 年 5 月の時点で Azure Government でパブリック プレビュー  
*: 2018 年 5 月の時点で Azure Government でプライベート プレビュー
