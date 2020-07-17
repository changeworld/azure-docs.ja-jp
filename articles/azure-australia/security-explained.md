---
title: Azure Australia のセキュリティの説明
description: オーストラリアのリージョンについてよく質問されることと、オーストラリア政府のポリシー、規制、法令の特定の要件を満たすための情報。
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 79f5773cf6f4906f93a5b95ce5c042da09c789fc
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575465"
---
# <a name="azure-australia-security-explained"></a>Azure Australia のセキュリティの説明

この記事では、Microsoft Azure Australia に関する調査、設計、および同サービスへのデプロイを行うオーストラリア政府機関向けに、いくつかの一般的な質問と関心領域を取り上げて説明します。

## <a name="irap-and-certified-cloud-services-list-documents"></a>IRAP および Certified Cloud Services List ドキュメント

Australian Cyber Security Centre (ACSC) では、サービスが Certified Cloud Services List (CCSL) に追加されると、その認定証、認定レポート、およびコンシューマー ガイドが提供されます。

Microsoft は現在、Azure、Office 365、Dynamics 365 CRM について CCSL に記載されています。

Microsoft では、[Microsoft Service Trust Portal](https://aka.ms/au-irap) のオーストラリアに固有のページで、監査、評価、および ACSC 認定ドキュメントをお客様およびパートナー様が利用できるようにしています。

## <a name="dissemination-limiting-markers-and-protected-certification"></a>Dissemination Limiting Markers および PROTECTED 認定

クラウド サービスを含むシステムを政府機関で使用する承認を得るためのプロセスは、ACSC によって作成および発行される [Information Security Manual (ISM)](https://acsc.gov.au/infosec/ism/) に定義されています。 ACSC はオーストラリア通信電子局 (ASD) 内の組織で、サイバー セキュリティとクラウドの認定を担当しています。

承認プロセスには、次の 2 つの手順があります。

1. **セキュリティ評価 (IRAP)** :登録されたプロフェッショナルが ISM の技術的コントロールに照らしてシステム、サービス、およびソリューションを評価し、コントロールが効果的に実装されたかどうかを評価するプロセス。 また、この評価では、承認機関が運用承認 (ATO) を発行する前に考慮する必要がある具体的なリスクも特定されます。

1. **運用承認**:政府機関の上級職員が、システムによって処理、保存、および交換される情報に対するシステムの残存リスクを正式に認識し、受容するプロセス。 このプロセスへの入力は、セキュリティ評価です。

Azure サービスの評価は PROTECTED レベルであり、PROTECTED 以下のデータの保存と処理に求められるセキュリティ コントロールが適切に実装され、効果的に機能していることが確認されました。

## <a name="australian-data-classification-changes"></a>オーストラリアにおけるデータ分類の変更

2018 年 10 月 1 日に、司法省では、Protective Security Policy Framework (PSPF) に対する変更 (具体的には、新しい[機密情報システム](https://www.protectivesecurity.gov.au/information/sensitive-classified-information/Pages/default.aspx)) を発表しました。

![改訂された PSPF 分類](media/pspf-classifications.png)

PSPF の下で活動するオーストラリア政府機関と組織はいずれも、これらの変更の影響を受けます。 現在の IRAP/CCSL 認定に影響を与える主な変更は、現行の Dissemination Limiting Markings (DLM) が廃止されたことです。 OFFICIAL: Sensitive というマーキングは、機密情報の保護に使用されるさまざまな DLM に代わるものです。 また、この変更では、機密性および分類のレベルを問わずあらゆる公式情報に適用できる、3 つの情報管理マーカーも導入されました。 PROTECTED 分類に変更はありません。

新しいシステムからは "Unclassified" という用語が削除されました。"Unofficial" という用語については、正式なマーキングは必要ありませんが、非政府情報に適用されます。

## <a name="choose-an-azure-region-for-official-sensitive-and-protected-workloads"></a>OFFICIAL: Sensitive および PROTECTED ワークロードのための Azure リージョンの選択

Azure OFFICIAL: Sensitive および PROTECTED 認定サービスは、オーストラリアの 4 つのデータ センター リージョンのすべてにデプロイされます: オーストラリア東部、オーストラリア東南部、オーストラリア中部、およびオーストラリア中部 2。 ACSC によって発行された認定レポートでは、サービスが利用可能な場合は、PROTECTED データをキャンベラの Azure Government リージョンにデプロイすることが推奨されます。 PROTECTED 認定 Azure サービスの詳細については、[Service Trust Portal のオーストラリアに関するページ](https://aka.ms/au-irap)を参照してください。

>[!NOTE]
>Microsoft では、政府データであれば機密性および分類のレベルを問わず、オーストラリア中部およびオーストラリア中部 2 のリージョンにデプロイすることをお勧めしています。これらは、政府のニーズに合わせて特別に設計および運用されているためです。

Azure Australia のリージョンの特別な性質に関する詳細については、[Azure オーストラリア中部リージョン](https://azure.microsoft.com/global-infrastructure/australia/)に関するページをご覧ください。

## <a name="how-microsoft-separates-classified-and-official-data"></a>Microsoft が機密区分データと公式データを分離する方法

Microsoft では、すべてのデータが機微であるまたは機密区分である場合と同じように Azure と Office 365 を運用しています。これにより、セキュリティ コントロールのレベルが引き上げられます。

Azure をサポートするインフラストラクチャでは、複数の分類のデータが提供される可能性があります。 お客様のデータが機密区分であるという前提であるため、適切な制御が行われます。 Microsoft では、サービスについて、PROTECTED の機密区分情報を格納および処理するための PSPF 要件に準拠したベースライン セキュリティ体制を採用しています。

Azure の 1 つのテナントが他のテナントのリスクにさらされていないことをお客様に保証するために、Microsoft では、包括的な多層防御式コントロールを実装しています。

Microsoft Azure プラットフォーム内に実装された機能のほか、お客様が管理するキーによる暗号化、ネストされた仮想化、Just-in-Time 管理アクセスなどの、お客様による構成が可能なコントロールを追加すると、リスクをさらに軽減できます。 キャンベラの Azure Government オーストラリア リージョン内では、オーストラリアおよびニュージーランドの政府および国家の重要なインフラストラクチャ組織のみを正式にホワイトリストに登録するプロセスが実施されています。 このコミュニティ クラウドは、共同テナントのリスクに敏感な組織に追加の保証を提供します。

Microsoft Azure の PROTECTED 認定レポートでは、PROTECTED の機密区分データの格納と処理およびそれらの隔離にこれらのコントロールが効果的であることが確認されています。

## <a name="relevance-of-irapccsl-to-state-government-and-critical-infrastructure-providers"></a>IRAP/CCSL と州政府および重要なインフラストラクチャ プロバイダーとの関連性

多くの州政府および重要なインフラストラクチャ プロバイダーでは、連邦政府の要件をそのセキュリティ ポリシーおよび保証フレームワークに組み込んでいます。 これらの組織では、OFFICIAL、OFFICIAL: Sensitive、および一部の PROTECTED 機密区分データも取り扱います。これらは、連邦政府とのやり取りから、または独自のものです。

オーストラリア政府では、政策と法令において、オーストラリアの安全保障と経済的繁栄に重大な影響を与える非政府データの保護をますます重視しています。 そのため、Azure Australia のリージョンと CCSL 認定は、これらの産業のすべてに関連しています。

![重要なインフラストラクチャのセクター](media/nci-sectors.png)

Microsoft の認定は、Azure サービスが、実施されているセキュリティ保護の徹底的かつ厳密で正式な評価を受け、そのような非常に機微なデータの取り扱いが承認されたことを証明するものです。

## <a name="location-and-control-of-microsoft-data-centres"></a>Microsoft データ センターの場所とコントロール

データを処理するクラウド サービスのデータ センターの場所と所有権について明確に認識することは、政府および重要なインフラストラクチャにとっての必須要件です。 Microsoft は、これらの場所と所有権に関する広範な情報を提供しているという点で、ハイパースケールのクラウド プロバイダーとして類のない存在です。

Microsoft の Azure Australia のリージョン (オーストラリア中部およびオーストラリア中部 2) は、CDC Datacentres の施設内で運用されています。 CDC Datacentres の所有権はオーストラリアが管理しており、持分の内訳は Commonwealth Superannuation Corporation が 48%、Infratil (オーストラリアとニュージーランドの両国の証券取引所に上場し、ニュージーランドに拠点を置く長期インフラストラクチャ アセット ファンド) が 48%、オーストラリア国籍の経営陣が 4% となっています。 

CDC Datacentres の経営には、オーストラリア政府による契約上の保証があり、将来の所有権と支配権の譲渡が制限されています。 Microsoft と CDC Datacentres とのパートナーシップによるサプライ チェーンと所有権のこの透明性は、[Whole-of-Government Hosting Strategy](https://www.dta.gov.au/our-projects/whole-government-hosting-strategy) の原則と Certified Sovereign Datacentre の定義に沿っています。

## <a name="azure-services-that-are-included-in-the-current-ccsl-certification"></a>現在の CCSL 認定に含まれている Azure サービス

2017 年 6 月に、ACSC では 41 個の Azure サービスを、Unclassified: DLM レベルでのデータの格納と処理について認定しました。 2018 年 4 月に、これらのサービスのうち 24 個は、PROTECTED 機密区分データに認定されました。

オーストラリアの Azure リージョンにおける ACSC 認定の Azure サービスの利用可能性は次のとおりです (太字のサービスは PROTECTED レベルで認定されています)。

|Azure Australia の中部リージョン|非リージョン サービスとその他のリージョン|
|---|---|
|API Management、App Gateway、Application Services、**Automation**、**Azure portal**、**Backup**、**Batch**、**Cloud Services**、Cosmos DB、Event Hubs、**ExpressRoute**、HDInsight、**Key Vault**、Load Balancer、Log Analytics、**Multi-factor Authentication**、Redis Cache、**Resource Manager**、**Service Bus**、**Service Fabric**、**Site Recovery**、**SQL Database**、**Storage**、Traffic Manager、**Virtual Machines**、**Virtual Network**、**VPN Gateway**|**Azure Active Directory**、CDN、Data Catalog、**Import Export**、**Information Protection**、**IOT Hub**、Machine Learning、Media Services、**Notification Hubs**、Power BI、**Scheduler**、**Security Centre**、Search、Stream Analytics|
|

Microsoft では、[Microsoft Azure コンプライアンスの概要](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/44/Microsoft%20Azure%20Compliance%20Offerings.pdf)に関するドキュメントを発行しています。これには、IRAP/CCSL を含め、Azure で実施されるグローバル、政府、業界、およびリージョンのあらゆるコンプライアンスおよび評価プロセスの範囲内のサービスがすべてリストされています。

## <a name="azure-service-not-listed-or-assessed-at-a-lower-level-than-needed"></a>リストされていない Azure サービス、または必要なレベルよりも低いレベルで評価されている Azure サービス

認定されていないサービス、または OFFICIAL: Sensitive で認定されており、PROTECTED レベルでは認定されていないサービスであっても、PROTECTED データをホストするソリューションと共に使用したり、その一部として使用したりすることができます。ただし、サービスが次のいずれかに該当することが条件です。

- 暗号化されていない PROTECTED データを保存または処理しない。
- リスク評価を完了し、サービスで PROTECTED データを保存することを承認した。

CCSL に含まれていないサービスを使用して、OFFICIAL データを格納および処理することはできます。しかし、クラウド サービス プロバイダーとの契約を締結または更新する前に、ACSC にその旨を書面で通知することが ISM で求められています。

PROTECTED ワークロードのために機関によって使用されるすべてのサービスは、[DTA の Secure Cloud Strategy](https://www.dta.gov.au/files/cloud-strategy/secure-cloud-strategy.pdf) の ISM および機関が管理する IRAP 評価プロセスで概説されているプロセスに従って、セキュリティの評価と承認を受ける必要があります。

![DTA の Secure Cloud Strategy 認定プロセス](media/certification.png)

Microsoft では継続的にサービスを評価して、プラットフォームがセキュリティで保護され、オーストラリア政府の目的に適していることを確認します。 現在、PROTECTED レベルの CCSL にないサービスに関するサポートが必要な場合は、Microsoft にお問い合わせください。

Microsoft では、Unclassified DLM と PROTECTED の両方の分類で CCSL で認定されたさまざまなサービスを提供しているため、少なくとも 2 年ごとにサービスの IRAP 評価を受けることが ISM により求められています。 Microsoft では、年に 1 回評価を受けています。これは、検討のために追加のサービスを含める機会でもあります。

## <a name="certified-protected-gateway-in-azure"></a>Azure の認定 PROTECTED ゲートウェイ

Microsoft では、政府認定の Secure Internet Gateway (SIG) を運用していません。これは、Gateway Consolidation Program で許可される SIG の数に制限があるためです。 ただし、SIG に求められる機能や必要な機能は、Microsoft Azure 内で構成できます。

Azure サービスの PROTECTED 認定を通じて、ACSC には、機関による Azure への接続について、およびセキュリティ ドメイン間 (たとえば、PROTECTED とインターネット間) のネットワークのセグメント化の実装について、一定の推奨事項があります。 これらの推奨事項には、ネットワーク セキュリティ グループ、ファイアウォール、および仮想プライベート ネットワークの使用が含まれます。 ACSC では、仮想ゲートウェイ アプライアンスの使用が推奨されています。 Azure には、ASD Evaluated Products List に同等の物理アプライアンスが存在する仮想アプライアンスや、Common Criteria Protection プロファイルに照らして評価され、Common Criteria ポータルに掲載されている仮想アプライアンスがいくつかあります。 これらの製品は、Common Criteria Recognition Arrangement の署名者としての ASD によって相互に認識されています。

Microsoft では、Azure ベースの機能の実装に関するガイダンスを作成しました。これは、異なるセキュリティ ドメイン間の境界を保護するために必要なセキュリティ機能を提供するもので、組み合わせると認定 SIG と同等のものを形成できます。 多数のパートナーがこれらの機能の設計と実装を支援することができ、同じ操作を行う多数のパートナー ソリューションを利用できます。

## <a name="security-clearances-and-citizenship-of-microsoft-support-personnel"></a>Microsoft のサポート担当者の安全検査と市民権

Microsoft のサービスをグローバルに運用しているのは、審査されトレーニングを受けたセキュリティ担当者です。 シドニーとメルボルンの施設に付き添いなしで物理的にアクセスできる担当者は、オーストラリア政府のベースライン安全検査に合格しています。 オーストラリア中部リージョンおよびオーストラリア中部 2 リージョン内の担当者は、(SECRET データに対応するための) 最小限の Negative Vetting 1 (NV1) 検査に合格しています。 これらの検査の要件により、Azure を運用するデータ センター内の担当者が高い信頼性を持つという追加の保証をお客様に提供することができます。

Microsoft には、ロールベースのアクセス制御に基づいた Just In Time および Just Enough Administration のシステムを介してアクセスが許可される、ゼロ スタンディング アクセス ポリシーがあります。 ほとんどの場合、Microsoft の管理者は、サービスのトラブルシューティングと保守を行うにあたって、顧客データに対するアクセスや特権を必要としません。 リモート実行のためのタスクの高度な自動化とスクリプト化により、顧客データに直接アクセスする必要はありません。

司法省は、Azure 内の Microsoft の担当者セキュリティ ポリシーおよび手順が、INFOSEC-9 の PSPF Access to Information 条項の意図と一致していることを確認しました。

## <a name="store-international-traffic-of-arms-regulations-itar-or-export-administration-regulations-ear-data"></a>International Traffic of Arms Regulations (ITAR) または Export Administration Regulations (EAR) データの格納

輸出規制の対象となるデータに関する義務をお客様が果たすのを支援する Azure の技術的コントロールは、Azure 内でグローバルに同じです。 重要なのは、輸出規制の対象となるデータに関する正式な評価と認定のフレームワークがないことです。

Azure Government および Office 365 US Government for Defense については、輸出規制の対象となるお客様をサポートするために、契約およびプロセスによる追加の対策を実施しています。 これらの追加の契約条項と、Azure リージョンの保証された米国国内サポートおよび管理は、オーストラリアでは実施されていません。

これは、オーストラリアの Azure を ITAR/EAR に使用できないことを意味するものではありませんが、輸出許可を通じて課される制限を明確に理解する必要があります。 また、Azure を使用してそのデータを格納する前に、これらの義務を果たすために追加の保護を実装する必要があります。 たとえば、次のことが必要になる場合があります。

- Azure Active Directory に属性として国籍を作成する。
- Azure Information Protection を使用してデータに暗号化ルールを適用し、それを輸出許可に含まれている米国およびその他の国籍のみに制限する。
- ITAR データに顧客キーまたは Hold Your Own Key を使用し、Azure に格納する前にオンプレミスですべてのデータを暗号化する。

ITAR は正式な認定ではないため、輸出許可に関連付けられている制限と制約を理解しておく必要があります。 その後、それらの要件を満たすための十分なコントロールが Azure にあるかどうかを確認できます。 この場合、詳細に検討すべき問題の 1 つは、輸出許可で承認された国籍でない可能性のあるエンジニアによるアクセスです。

## <a name="next-steps"></a>次の手順

 Azure Australia への VPN 接続の ISM 準拠の構成と実装については、「[Azure Australia の Azure VPN Gateway](vpn-gateway.md)」を参照してください。
