---
title: Azure Sentinel ソリューション カタログ | Microsoft Docs
description: この記事では、現在提供されている一連の Sentinel ソリューション パッケージとその詳細を紹介します。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: yelevin
ms.openlocfilehash: c983ff540637478d9283b76d060c7f04c319adef
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114442485"
---
# <a name="azure-sentinel-solutions-catalog"></a>Azure Sentinel ソリューション カタログ

> [!IMPORTANT]
>
> Azure Sentinel ソリューションのエクスペリエンスは現在 **プレビュー** 段階です。個々のソリューション パッケージもすべて同様です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

[Azure Sentinel ソリューション](sentinel-solutions.md)は、データ コネクタ、ブック、分析、オートメーションといった Azure Sentinel のコンテンツを 1 回のデプロイ ステップでワークスペースに取得する、統合された方法を提供します。

##  <a name="currently-available-solutions"></a>現在提供されているソリューション

現在、Azure Sentinel では、以下のソリューションが提供されています。 いずれも無料でインストールできますが、データ インジェストや Logic Apps の使用などには通常の料金が適用されます。


| 解決策 | 説明 |
| - | - |
| **Azure Firewall Solution for Sentinel** | Azure Firewall は、Azure Virtual Network リソースを保護するクラウドベースのマネージド ネットワーク セキュリティ サービスです。 完全にステートフルなサービスとしてのファイアウォールで、組み込みの高可用性とクラウドによる無制限のスケーラビリティを備えています。  Sentinel の Azure Firewall ソリューションは、Azure Sentinel を基盤として、カスタマイズ可能な組み込みの脅威検出を提供します。 ソリューションには、ブック、検出、ハンティング クエリ、プレイブックが含まれます。 |
| **Azure Sentinel 4 Dynamics 365** | Continuous Threat Monitoring for Dynamics 365 ソリューションでは、Dynamics 365 のログを収集して Dynamics 365 内のアクティビティを可視化し、それらを分析することで脅威や悪質なアクティビティを検出することができます。 このソリューションには、データ コネクタ、ブック、分析ルール、ハンティング クエリが含まれています。 |
| **SQL PaaS 用 Azure Sentinel**| このソリューションは、Azure SQL PaaS サービスに対して、Azure Sentinel に加えてカスタマイズ可能な組み込みの脅威検出を提供します。SQL 監査ログに基づいており、Azure Defender for SQL からのアラートにシームレスに統合されています。|
| **Azure Sentinel for Teams** | Teams は、Microsoft 365 クラウドでのコミュニケーションとデータの共有の両方において中心的役割を果たします。 Teams サービスは、クラウドの基盤となるさまざまなテクノロジと関係しているため、ログのハンティングに限らず、ミーティングのリアルタイム監視においても、人による分析と自動化された分析を有効活用することができます。 そうしたソリューションが、Azure Sentinel を通じて管理者に提供されます。 このソリューションには、分析ルール、ハンティング クエリ、プレイブックが含まれています。 |
| **Box ソリューション** | [Box](https://www.box.com/overview) は、ファイルの作成から共有、共同編集、署名、分類、保有まで、コンテンツ ライフサイクル全体を意図して作成された安全で使いやすい単一のプラットフォームです。 |
|**Check Point Azure Sentinel ソリューション** |Check Point Logic App Connector and Playbooks を使用すると、お客様はパッケージ化された Check Point コネクタとプレイブックを簡単に見つけて、Azure Sentinel から直接デプロイできます。 ユーザーは CloudGuard セキュリティ ゲートウェイとオンプレミスの Check Point ゲートウェイを使用して、脅威を自動的に修復するように Azure Sentinel の SOAR プレイブックを構成することができ、Microsoft Azure と Check Point CloudGuard の両方のセキュリティ機能が強化されます。 <br><br>Check Point Logic App Connector and Playbooks は、自動修復も提供できます。 お客様は、Check Point CloudGuard セキュリティ ゲートウェイを自動的にトリガーする SOAR プレイブックを構成して、セキュリティ ポリシーの更新や、悪意のあるトラフィックのブロックなどを実行できます。 Check Point Management API を利用することで、これらのセキュリティ運用タスクをコネクタによって自動化できます。これは IT スタッフやネットワーク管理者、セキュリティ担当者の時間を節約できるだけでなく、攻撃者がセキュリティの問題を悪用できる期間を大幅に削減することを可能にします。これらの問題を手動で修正する必要がなくなるためです。 その結果、ネットワークおよびセキュリティ管理者、セキュリティ アナリスト、DevOps/DevSecOps チームなどの時間を節約しながら、組織の攻撃面を最小限に抑えるのに役立ちます。 また、定義済みのプレイブックを使用することで、個々の API 呼び出しを記述する必要がなくなり、Check Point のプレイブックをすべてのネイティブな Azure サービスや、数百もの既存のロジック アプリ コネクタと簡単に統合できます。 |
|**Cisco ACI ソリューション** | [Cisco Application Centric Infrastructure (Cisco ACI)](https://www.cisco.com/c/en/us/solutions/collateral/data-center-virtualization/application-centric-infrastructure/solution-overview-c22-741487.html) は、業界をリードする Cisco Nexus 9000 プラットフォーム上に構築されたインテントベースのネットワーク フレームワークの一部であり、データ センターにおける機敏性と回復性の実現に貢献します。 Cisco ACI がもたらす自動化によって、インフラストラクチャのデプロイとガバナンスが促進され、管理が簡素化してマルチファブリックおよびマルチクラウド フレームワーク全体でワークロードを簡単に移動できるようになり、あらゆる場所から発生するリスクに対して事前にセキュリティを確保することが可能になります。 アプリケーションのデプロイ ライフサイクルが大幅に簡素化、最適化、および迅速化されます。|
| **Cisco ISE ソリューション** | [Cisco Identity Services Engine (ISE)](https://www.cisco.com/c/en/us/products/collateral/security/identity-services-engine/data_sheet_c78-656174.html) は、セキュリティ ポリシー管理の効率化と運用コストの削減を一括して提供するソリューションです。 ISE を使用すると、ユーザーとデバイスを把握し、ワイヤード (有線)、ワイヤレス、VPN など接続の種類に関係なく、企業ネットワークへのアクセスを制御することができます。 |
|**Cisco SEG ソリューション** | [Cisco Secure Email Gateway (SEG)](https://www.cisco.com/c/en/us/products/security/email-security/index.html) は、お使いの電子メールに、サイバー脅威に対する業界をリードする保護を提供します。 Secure Email による、オンプレミスおよびクラウドベースの電子メールに対する包括的な保護により、最も一般的で有害なサイバー脅威を防ぐことができます。|
| **Cisco Umbrella ソリューション** | [Cisco Umbrella](https://umbrella.cisco.com/) は、必要なときに必要な方法で柔軟に導入できるクラウド提供型のセキュリティです。 1 つのソリューションに複数のセキュリティ機能がまとめられていて、デバイスやリモート ユーザー、さまざまな分散拠点にまで保護の範囲を拡げることができます。  |
|**Cisco Web Security Appliance (WSA)** |[Cisco Secure Web Appliance](https://www.cisco.com/c/en/us/products/security/web-security-appliance/index.html) は、TLS 1.3 および高パフォーマンスな機能を使用して組織を保護する Web プロキシ ソリューションです。Web ベースの脅威を自動的に検出してブロックすることで、ユーザーを安全に保ちます。 |
| **Cloudflare ソリューション** | [Cloudflare](https://www.cloudflare.com/) は、Web サイト、API、アプリケーションなど外部に接続されたリソースを保護し、その信頼性を確保します。 保護の対象は、アプリケーション、チーム、デバイスなど、ファイアウォールの内側に存在する内部リソースにも及びます。 また、グローバルにスケーラブルなアプリケーションを開発するためのプラットフォームでもあります。 |
| **Continuous Threat Monitoring for SAP** | [Continuous Threat Monitoring for SAP](sap-deploy-solution.md) を使用すると、SAP システムを監視して、ビジネス レイヤーおよびアプリケーション レイヤーにおける高度な脅威に対応することができます。 SAP データ コネクタは、SAP システム ランドスケープ全体から 14 もの多くのアプリケーション ログをストリーム配信し、NetWeaver RFC 呼び出しを介して Advanced Business Application Programming (ABAP) から、また OSSAP 制御インターフェイスを介してファイル ストレージ データからログを収集します。 |
| **Contrast Protect Azure Sentinel ソリューション** | [Contrast Protect](https://www.contrastsecurity.com/runtime-application-self-protection-rasp) は、アプリケーションがどこで実行されていても、それらを防御する対応力をチームに提供します。自動化された精密な実行時保護機能をアプリケーションに埋め込むことで絶えず攻撃を監視し、阻止することができます。 取り組みの中心となったのは、アクションにつながるタイムリーなアプリケーション レイヤー脅威インテリジェンスです。これによってセキュリティ チームや運用チームが、脅威や攻撃の重大度を把握し、管理しやすいようになっています。 Azure Sentinel には Contrast Protect がシームレスに統合されており、セキュリティ リスクに関して、従来よりも優れた分析情報をアプリケーション レイヤーから得ることができます。 |
| **Corelight for Azure Sentinel** | インシデントに対応する担当者と脅威の追求に携わる担当者は、Azure Sentinel to [Corelight](https://corelight.com/) for Azure Sentinel を使用することで、これまでよりも迅速かつ効果的に連携することができます。 Corelight は、Zeek と Suricata というオープンソース テクノロジの最善の組み合わせに基づいて NDR (Network Detection and Response) ソリューションを提供しており、ネットワーク ディフェンダーは、その環境を広範囲に可視化することができます。<br><br>Zeek と Suricata のイベントは、データ コネクタから Corelight Sensors を介して Azure Sentinel に取り込むことができます。 また、Corelight for Azure Sentinel はブックとダッシュボード、ハンティング クエリ、分析ルールも備えており、Corelight と Azure Sentinel の組み合わせにより組織の効率的な調査とインシデント対応を後押しします。 |
| **CrowdStrike Falcon Endpoint Protection ソリューション** | [CrowdStrike Falcon Endpoint Protection](https://www.crowdstrike.com/resources/data-sheets/falcon-endpoint-protection-pro/) は、組み込みの脅威インテリジェンスと対応により、きわめて効果的な防御テクノロジと攻撃を完全に可視化する機能を組み合わせることによって、既存のソリューションの後継となる理想的なウイルス対策ソリューションを提供します。 |
| **HYAS Insight for Azure Sentinel Solutions Gallery** | [HYAS Insight](https://www.hyas.com/hyas-insight) は、脅威の調査と帰属特定のためのソリューションです。専用のデータ ソースと従来にないメカニズムを使用して、アナリスト、研究員、調査担当者の可視性と生産性を高めながら調査結果の正確性を高めます。 HYAS Insight は攻撃の過程や作戦行動を、セキュリティ侵害の数十億もの徴候に結び付けることで分析情報と可視性を提供します。 HYAS Insight は使いやすいユーザー インターフェイス、変換、API アクセスを備えることで、豊富な脅威データを統合して、リサーチと帰属特定のための強力なソリューションを実現します。 日々直面する脅威の性質をより深く理解できるよう組織を支援する HYAS Intelligence チームによって HYAS Insight は完成されました。 |
|**Google Cloud Platform DNS ソリューション (プレビュー)** | [Google Cloud Platform Domain Name System (DNS)](https://cloud.google.com/dns) は、コスト効率の高い方法でドメイン名をグローバル DNS に発行する、高パフォーマンスで回復性に優れたグローバル ドメイン ネーム システム (DNS) サービスです。|
| **Google Cloud Platform Cloud Monitoring ソリューション** |[Google Cloud Platform Cloud Monitoring](https://cloud.google.com/monitoring) では、Google Cloud サービス向けの、すぐに利用できる自動メトリック収集ダッシュボードが提供されます。 また、ハイブリッド環境とマルチクラウド環境の監視もサポートされており、アプリケーションとインフラストラクチャのパフォーマンス、可用性、正常性を可視化できます。 |
| **Google Cloud Platform IAM ソリューション** | [Google Cloud Platform Identity and Access Management (IAM)](https://cloud.google.com/iam) を使用すると、管理者は特定のリソースを操作できるユーザーを承認できるとともに、Google Cloud リソースを一元的に管理するための完全な制御と可視性を得ることができます。|
| **Infoblox Cloud Data Connector ソリューション** | [BloxOne DDI](https://www.infoblox.com/products/bloxone-ddi/) は、かつてないコスト効率で、ありとあらゆる場所の DDI (DNS、DHCP、IPAM) をクラウドから一元的に管理、自動化できる業界初の DDI ソリューションです。 クラウドネイティブの原則の下で構築され SaaS サービスとして提供される BloxOne DDI によって、従来の DDI 実装に伴う複雑さ、ボトルネック、スケーラビリティの制限から解放されるため、ネットワーク管理が大幅に簡素化されます。<br><br>BloxOne Threat Defense は既にある防御機構と連携してネットワークを保護し、SD-WAN、IoT、クラウドなどデジタル戦略に必要な要素にまでセキュリティを自動的に拡張することで、ブランドを最大限に保護します。 SOAR (Security Orchestration、Automation、Response) ソリューションを強化し、サイバー攻撃に対する調査と修復に要する時間を短縮すると共に、セキュリティ エコシステム全体のパフォーマンスを最適化し、企業の脅威防御にかかる総コストを削減します。 |
|**Juniper IDP** | [Juniper Intrusion Detection and Prevention](https://www.juniper.net/documentation/us/en/software/junos/idp-policy/topics/topic-map/security-idp-overview.html) は、ネットワークで発生しているイベントを監視し、セキュリティ ポリシーに対するインシデントや違反、または差し迫った脅威の可能性がある兆候を分析します。 これらのセキュリティ対策は、侵入検出システム (IDS) および侵入防止システム (IPS) として使用することができ、ネットワークの一部となって潜在的なインシデントを検出および停止します。|
| **McAfee ePolicy Orchestrator ソリューション** | [McAfee ePO](https://www.mcafee.com/enterprise/en-in/products/epolicy-orchestrator.html) は、エンドポイントとエンタープライズ セキュリティ製品に対するポリシーの管理とポリシーの適用を一元化するものです。 ネットワークを監視、管理して脅威を検出し、それらの脅威からエンドポイントを保護します。 |
|**McAfee Network Security Platform ソリューション** |[McAfee Network Security Platform](https://www.mcafee.com/enterprise/en-us/products/virtual-network-security-platform.html) は、ネットワーク保護を仮想化された環境全体に拡張し、仮想ネットワークに対するインテリジェントな脅威保護を提供します。 |
| **Oracle Database Audit ソリューション** | [Oracle Database](https://www.oracle.com/database/technologies/security/db-auditing.html) は、Enterprise Edition と Standard Edition の両方のデータベースで信頼性の高い監査に対応します。 Oracle Database Unified Auditing では、ポリシーと条件を使用して、Oracle データベース内部の選択的かつ効果的な監査を実行できます。 |
| **Palo Alto Prisma ソリューション** | [Prisma Cloud](https://www.paloaltonetworks.com/prisma/cloud) は、業界最先端の包括的なクラウド ネイティブ セキュリティ プラットフォームです。マルチクラウド環境とハイブリッドクラウド環境に、ライフサイクル全体にわたるセキュリティとフル スタックの保護を提供します。 |
| **PingFederate ソリューション** | [PingFederate®](https://www.pingidentity.com/en/resources/client-library/data-sheets/pingfederate-data-sheet.html) は、従業員、パートナー、顧客の各 ID タイプを対象に、ユーザー認証と標準ベースのシングル サインオン (SSO) を実現する優れたエンタープライズ フェデレーション サーバーです。 PingFederate により、組織が高価で自由度の低い従来の IAM ソリューションから解放され、企業の複雑なニーズを満たすよう設計された最新の ID とアクセス管理ソリューションを利用することができます。 |
| **Proofpoint POD ソリューション** | [Proofpoint on Demand Email Security](https://www.proofpoint.com/us/products/email-security-and-protection/email-protection) は、悪意のあるペイロードを伴わない脅威を検出してブロックしながら、さまざまなタイプのメールを正確に分類します。 疑わしいメールには自動的にタグを付けて、エンドユーザーの注意を喚起し、どのようなメールでも数秒で追跡することができます。 |
| **Proofpoint TAP ソリューション** | [Proofpoint Targeted Attack Protection (TAP)](https://www.proofpoint.com/us/products/advanced-threat-protection/targeted-attack-protection) を使用すると、メールを通じて人を標的にする高度な脅威、たとえば悪意のある添付ファイルや URL を使用してマルウェアをインストールしたりユーザーから巧妙にパスワードや機密情報をだまし取ったりする攻撃を検出し、軽減策を講じたりブロックしたりすることができます。 また、クラウド アプリに潜む脅威やリスクを検出し、資格情報の盗用などの攻撃に関連したメール攻撃を特定することもできます。 |
| **Qualys VM ソリューション** | [Qualys Vulnerability Management (VM)](https://www.qualys.com/apps/vulnerability-management/) は IT 資産が無防備となっている場所を包括的に可視化し、その保護方法を提供します。 クラウド コンピューティングやモビリティなど、デジタル変革のための画期的な技術の採用が企業の間で進む中、Qualys VM は、従来の境界があいまいになってきたハイブリッド IT 環境向けに次世代の脆弱性管理を提供します。 軽量の Qualys Cloud Agents を用いたエージェントベースの検出により、スキャンできない資産にまでネットワーク範囲を拡大することで実現されます。 |
| **Rapid7 InsightVM CloudAPI ソリューション** |[Rapid7 Insight プラットフォーム](https://www.rapid7.com/products/insightvm/)は、Rapid7 の脆弱性調査、悪用の知識、グローバルな攻撃者の行動、インターネット全体のスキャン データ、露出分析、そしてリアルタイム レポートのライブラリを組み合わせることにより、脆弱性データを収集して回答に変換するための、いつでも利用可能でスケーラブルかつ効率的な方法を提供します。 InsightVM は、ライブでの脆弱性とエンドポイント分析にこのプラットフォームを利用します。 |
| **RiskIQ セキュリティ インテリジェンス プレイブック** | Azure Sentinel プラットフォーム内のインシデントをエンリッチメント処理し、そこにコンテキストを追加する機能をひとまとめにした、いくつかの Azure Sentinel プレイブックが [RiskIQ](https://www.riskiq.com/) により作成されました。 これらのプレイブックは、Azure Sentinel ポータル内で自動的に実行するように構成できるほか、個別に実行することもできます。 ドメインや IP アドレスなど既知のインジケーターがインシデントに含まれている場合、その値が RiskIQ によってエンリッチメント処理されます。エンリッチメント処理には、インターネット上で他にどのようなものに接続されているかや、脅威をもたらす可能性があるかどうかなどの情報が使用されます。 インシデントには、RiskIQ の調査プラットフォーム内のさらに詳しい情報にリンクしたコメントが追加されます。 |
| **Senserva Offer for Azure Sentinel** | Azure Sentinel 用のクラウド セキュリティ態勢管理 (CSPM) 機能である [Senserva](https://www.senserva.com/product/) は、Azure Active Directory セキュリティ リスクが問題に発展する前に、優先度ベースのリスク評価を絶えず生成することで、その管理を省力化します。 Senserva の情報には、その管理下にあるすべての Azure オブジェクトの詳細なセキュリティ ランキングが含まれます。ユーザーは影響度の一番高いものから順に、最も重大な問題を解決していくことにより、最適な形で検出と修復を実施することができます。  Senserva のエンリッチメント処理された情報はすべて Azure Sentinel に送信され、Log Analytics ワークスペースを介してクエリ、ブック、プレイブックによって処理されます。<br><br>Senserva からは、Microsoft Cloud 環境内のイベントやアプリケーション、セキュリティ ユーザー アカウント、Azure Active Directory の構成 (条件付きアクセスを含む) についての詳しい分析が得られます。 Microsoft 365 と Microsoft Azure の管理者やセキュリティ チームは、Senserva の特許技術によって膨大な時間を節約することができます。 Senserva は、業界標準 (NIST 800-53、MITRE ATT&CK) や業界の推奨事項、ベンダーの推奨事項、さらに Senserva が独自に擁するエキスパートを組み合わせて (特許出願中) 作成されています。 |
| **Slack Audit ソリューション** | [Slack Audit](https://slack.com/) データ コネクタを使用すると、[Slack Audit Records](https://api.slack.com/admins/audit-logs) イベントを REST API で Azure Sentinel に取り込むことができます。 詳細については、[API のドキュメント](https://api.slack.com/admins/audit-logs#the_audit_event)を参照してください。 これらのイベントが可視化されることで、潜在的なセキュリティ リスクを調査したり、チームのコラボレーションの使用を分析したり、構成の問題を診断したりすることができます。 |
|**Sophos Endpoint Protection ソリューション** | [Sophos](https://www.sophos.com/en-us/company.aspx) は次世代サイバーセキュリティの世界的リーダーであり、150 を超える国の 500,000 を超える組織と数百万人もの消費者を、今日の最も高度なサイバー脅威から保護しています。 SophosLabs と SophosAI の脅威インテリジェンス、AI、機械学習を利用する Sophos は、ランサムウェアやマルウェア、悪用、フィッシング、その他の広範なサイバー攻撃に対してユーザーとネットワーク、エンドポイントをセキュリティで保護する、高度な製品とサービスの広範囲にわたるポートフォリオを提供しています。|
| **Sophos XG Firewall ソリューション** | [Sophos XG Firewall](https://www.sophos.com/products/next-gen-firewall.aspx) は、可視性、同期されたセキュリティ、自動化された対応を通じてオールインワンの保護を企業に提供します。 これには見えないリスクの顕在化、未知の脅威の阻止、感染したシステムの隔離などが含まれます。 また、セキュリティで保護されたネットワークを XG Firewall の VPN クライアントとハードウェアで拡張し、あらゆる場所の従業員に簡単に提供できます。 |
| **Symantec Endpoint Protection ソリューション** | [Symantec Endpoint Protection](https://www.broadcom.com/products/cyber-security/endpoint) は、サーバー コンピューターとデスクトップ コンピューター向けのマルウェア対策、侵入防止、ファイアウォール機能から成るセキュリティ ソフトウェア スイートです。 データ損失防止ソフトウェアの一般的な機能もいくつか備わっています。 その用途は、未承認のプログラムの実行を阻止したり、ファイアウォール ポリシーを適用してネットワーク トラフィックを拒否または許可したりすることです。 企業ネットワーク内または Web ブラウザーから送信される悪意のあるトラフィックを特定してブロックを試みます。 |
| **Symantec ProxySG ソリューション** | Symantec は、Web アクティビティを標的とする高度な脅威から通信を保護することを目的にしたハイパフォーマンスでスケーラブルな Web プロキシ アプライアンスによって組織を保護します。 [Symantec Secure Web Gateway](https://www.broadcom.com/products/cyber-security/network/gateway/proxy-sg-and-advanced-secure-gateway) ソリューションには独特のプロキシ サーバー アーキテクチャが活用されており、組織はトラフィックを効率的に監視、制御、保護して、Web とクラウドのエクスペリエンスの安全性を確保することができます。 |
| **TitaniumCloud File Enrichment ソリューション** | [ReversingLabs TitaniumCloud](https://www.reversinglabs.com/products/file-reputation-service) は、良性と悪性の 100 億以上のファイルに基づく最新のファイル レピュテーション サービス、脅威分類、豊富なコンテキストを提供する脅威インテリジェンス ソリューションです。 ファイルは ReversingLabs のファイル分解技術を使用して処理されます。 一連の強力な REST API クエリとフィード関数からは、ターゲット ファイルとマルウェアのインテリジェンスが得られ、脅威の特定、分析、インテリジェンス構築、脅威ハンティング サービスを実現できます。 |
|**Trend Micro Apex One ソリューション** |[Trend Micro Apex One](https://www.trendmicro.com/en_us/business/products/user-protection/sps/endpoint.html) 保護では、ファイルレスやランサムウェアなどの増え続けるさまざまな脅威に対する、高度な自動検出と対応が提供されます。 最新の手法の世代をまたいだ融合により、パフォーマンスと有効性を最大化する洗練されたエンドポイント保護を実現します。 |
| **Ubiquiti UniFi ソリューション** | [Ubiquiti Inc.](https://www.ui.com/) は企業や家庭向けに、複数のブランド名でワイヤレス データ通信とワイヤード製品を製造、販売するアメリカのテクノロジ企業です。 |
| **vArmour Application Controller と Azure Sentinel ソリューション** | [vArmour Application Controller](https://www.varmour.com/) は、企業を可視化して保護する革新的な手段、Application Relationship Management のための業界最先端のソリューションです。 Azure Sentinel と組み合わせることで、両者がシームレスに統合し、可視性の向上とセキュリティ運用の自動化がもたらされます。 デジタルファーストのビジネスは、ハイブリッド環境全体のユーザーとアプリケーションとを結ぶ何百万もの動的相互接続の上に構築されています。 今日、そうした相互接続の大部分は目に見えません。 環境面の複雑さが増すにつれて、組織のリスクも増大します。 Application Controller は簡単にデプロイできるソリューションです。アプリケーションの関係と依存関係をリアルタイムで包括的に可視化、制御できるため、運用上の意思決定を改善し、セキュリティの状態を強化すると共に、マルチクラウド デプロイ全体においてビジネス上のリスクを低減します。コストのかさむ新しいエージェントやインフラストラクチャを追加する必要は一切ありません。 その結果、アプリケーションの回復性とセキュリティが向上します。 |
| **VMware Carbon Black ソリューション** | [VMware Carbon Black](https://www.carbonblack.com/products/vmware-carbon-black-cloud-endpoint/) は、ユーザーのニーズに適合するクラウド ネイティブのエンドポイント保護でセキュリティを変革します。 VMware Carbon Black は、悪意のある攻撃の隠れ蓑となるわずかな変化を特定し、それに合った対策を講じることができるエンドポイント プラットフォームとなっています。 |
| | |

## <a name="next-steps"></a>次の手順

このドキュメントでは、Azure Sentinel ソリューションの概要と、それらを見つけてデプロイする方法について説明しました。

- [Azure Sentinel ソリューション](sentinel-solutions.md)について理解を深める。
- [Azure Sentinel ソリューションを検索してデプロイする](sentinel-solutions-deploy.md)。
