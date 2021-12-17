---
title: Azure Monitor に関するベスト プラクティス - 計画
description: Azure Monitor をデプロイする前の計画と設計に関するガイダンスと推奨事項。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: b04b73cfd7cd2482e14485556df5a477e47a9334
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347976"
---
# <a name="azure-monitor-best-practices---planning-your-monitoring-strategy-and-configuration"></a>Azure Monitor のベスト プラクティス - 監視の戦略と構成を計画する
この記事は、[Azure Monitor を構成する際の推奨事項](best-practices.md)に関する記事で紹介されているシナリオの一部です。 実装を開始する前に考慮すべき計画について説明します。 これをお読みいただければ、選択した構成方法で特定のビジネス要件を確実に満たすことができます。

監視の概念についてまだ馴染みのない方は、最初に [Azure 向けの Microsoft Cloud 導入フレームワーク](/cloud-adoption-framework/)の「[クラウド監視ガイド](/azure/cloud-adoption-framework/manage/monitor)」をお読みください。 ガイドには、全般的な監視の概念が定義されているほか、監視環境とそれを支える関連プロセスの要件を定義するうえでのガイダンスが記載されています。 そのガイドの中の、特定の計画ステップに関連したセクションには、この記事でも言及しています。
## <a name="understand-azure-monitor-costs"></a>Azure Monitor のコストについて
監視戦略の核となる目標はコストの最小化です。 Azure Monitor のデータ収集と機能には、コストがかからないものもあれば、特定の構成や収集されたデータ量、収集の実行頻度に応じたコストが発生するものもあります。 コストを含む推奨事項については、このシナリオの記事でも明らかにしていますが、コストの最適化に向けて実装を設計する際には、Azure Monitor の価格を読者も熟知しておく必要があります。 Azure Monitor の価格の詳細とガイダンスについては、次の記事を参照してください。

- [Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)
- [Azure Monitor での使用量と推定コストの監視](usage-estimated-costs.md)
- [Azure Monitor ログで使用量とコストを管理する](logs/manage-cost-storage.md)
- [Application Insights の使用量とコストを管理する](app/pricing.md)

## <a name="define-strategy"></a>戦略の定義
監視ソリューションを設計して実装する前に、計画の目標と要件がわかるように監視戦略を規定する必要があります。 戦略で定義するのは、アプリケーションのパフォーマンスと信頼性を最大化するための特定の要件とそれらを最も満たす構成、そして監視環境を活用するプロセスです。 Azure Monitor に関して選ぶ構成オプションは、その戦略に沿ったものでなければなりません。

監視戦略を策定する際に考慮すべきさまざまな要素については、「[クラウド監視ガイド: 監視戦略を策定する](/azure/cloud-adoption-framework/strategy/monitoring-strategy)」を参照してください。 「[クラウド デプロイ モデルの監視戦略](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview)」も参照してください。100%クラウドを使用した監視とハイブリッド モデルとの比較がわかりやすく説明されています。 

## <a name="gather-required-information"></a>必要な情報を収集する
実装の細部を決定する前に、その詳細を定義するうえで必要な情報を集めなければなりません。 以降のセクションでは、Azure Monitor を完全に実装するために一般的に必要となる情報について説明しています。

 ### <a name="what-needs-to-be-monitored"></a>監視すべきものは何か
 すべてのクラウド リソースを隅々まで監視するよう構成する必要はなく、むしろ重要なアプリケーションとその依存コンポーネントに的を絞って監視することになります。 そうすることで監視コストが下がるだけでなく、監視環境の複雑さも軽減されます。 必要なデータを定義するうえでのガイダンスについては、「[クラウド監視ガイド: 適切なデータの収集](/azure/cloud-adoption-framework/manage/monitor/data-collection)」を参照してください。

### <a name="who-needs-to-have-access-and-be-notified"></a>アクセスをだれに許可し、だれに通知すべきか
監視環境を構成する際は、監視データへのアクセスをどのユーザーに許可し、問題が検出されたときにどのユーザーに通知すべきかを決める必要があります。 その対象は、アプリケーションとリソースの所有者である場合もあれば、中央の監視チームの場合もあります。 この情報によって、データのアクセス許可とアラートの通知を構成する方法が決まります。 また、各種ユーザーに特定の情報一式を提供するカスタム ブックが必要になる場合もあります。

### <a name="service-level-agreements"></a>サービス レベル アグリーメント 
SLA によってアプリケーションのパフォーマンスとアップタイムのコミットメントを定義している組織もあります。 アラートなど時間的制約のある Azure Monitor の機能をどう構成すべきかは、これらの SLA で決まる場合があります。 また、監視シナリオの応答性と SLA への対応能力は [Azure Monitor のデータ待ち時間](logs/data-ingestion-time.md)に左右されるため、この待ち時間についての理解も必要です。

## <a name="identify-monitoring-services-and-products"></a>監視サービスと製品を特定する
Azure Monitor は、正常性と状態を監視するという課題への対応を目的としています。 総合監視ソリューションには、複数の Azure サービスが含まれていることが一般的であり、場合によっては他の製品も含まれることがあります。 その他の監視の目標については、「クラウド監視ガイド」の[主な監視の目標](/azure/cloud-adoption-framework/strategy/monitoring-strategy#formulate-monitoring-requirements)に関するセクションに記載されています。これらについては、別途ソリューションが必要になる場合があります。 

以降のセクションでは、Azure Monitor と組み合わせて使用できる他のサービスと製品について説明しています。 これらのソリューションの実装に関するガイダンスは現在、このシナリオには含まれていません。それぞれ対応するドキュメントを参照してください。

### <a name="security-monitoring"></a>セキュリティの監視
Azure Monitor に格納されている運用データはセキュリティ インシデントの調査に役立つことがありますが、Azure の他のサービスはセキュリティを監視するために設計されています。 Azure のセキュリティは Microsoft Defender for Cloud と Microsoft Sentinel によって監視されます。

- [Microsoft Defender for Cloud](../security-center/security-center-introduction.md) では、Azure リソースとハイブリッド サーバーに関する情報が収集されます。 Defender for Cloud ではセキュリティ イベントを収集できますが、インベントリ データ、評価のスキャンの結果、ポリシーの監査の収集に重点が置かれており、脆弱性を目立つように表示し、是正措置を推奨します。 注目すべき機能には、対話型のネットワーク マップ、Just-In-Time の VM アクセス、アダプティブ ネットワークのセキュリティ強化機能、疑わしい実行可能ファイルをブロックする適応型アプリケーション制御が含まれます。

- [Microsoft Defender for Servers](../security-center/azure-defender.md) は、Defender for Cloud によって提供されるサーバー評価ソリューションです。 Defender for Servers では、Windows セキュリティ イベントを Log Analytics に送信できます。 Defender for Cloud は、アラートまたは分析に関して Windows のセキュリティ イベントに依存していません。 この機能を使用すると、調査などの目的でイベントを一元的にアーカイブできます。

- [Microsoft Sentinel](../sentinel/overview.md) は、セキュリティ情報イベント管理 (SIEM) およびセキュリティ オーケストレーション自動応答 (SOAR) ソリューションです。 Sentinel では、Microsoft およびサードパーティのさまざまなソースからセキュリティ データを収集して、アラート、視覚化、自動化を提供します。 このソリューションでは、可能な限り多くのセキュリティ ログ (Windows セキュリティ イベントを含む) を統合することに重点が置かれています。 Microsoft Sentinel では、Windows セキュリティ イベントのログを収集することもできます。また、通常、Log Analytics ワークスペースを Security Center と共有しています。 セキュリティ イベントは、同じワークスペースを共有している場合にのみ、Microsoft Sentinel または Defender for Cloud から収集できます。 Defender for Cloud とは異なり、セキュリティ イベントは Microsoft Sentinel において警告と分析の主要構成要素です。

- [Defender for Endpoint](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint) は、企業ネットワークによる高度な脅威に対する防御、検出、調査、対応を支援するように設計されたエンタープライズ エンドポイント セキュリティ プラットフォームです。 これは、Windows ユーザー デバイスの保護に主に重点を置いて設計されています。 Defender for Endpoint では、セキュリティの問題と脆弱性について、さまざまなオペレーティング システムのワークステーション、サーバー、タブレット、携帯電話を監視します。 Defender for Endpoint では、Microsoft エンドポイント マネージャーと密接に連携し、データを収集してセキュリティ評価が提供されます。 データ収集は主に ETW トレース ログに基づいており、分離されたワークスペースに格納されます。


### <a name="system-center-operations-manager"></a>System Center Operations Manager
読者の中には、既に System Center Operations Manager を導入してオンプレミスの仮想マシンで実行されるワークロードやリソースを監視しているお客様もいらっしゃるかもしれません。 [その監視機能を Azure Monitor に移行](azure-monitor-operations-manager.md)できるほか、両方の製品をハイブリッド構成で使い続けることもできます。 この 2 つの製品の比較については、「[クラウド監視ガイド: 監視プラットフォームの概要](/azure/cloud-adoption-framework/manage/monitor/platform-overview)」を参照してください。 2 つの製品をハイブリッド構成で使用する場合のガイダンスと実際の環境に最適なモデルの判断については、「[クラウド デプロイ モデルの監視戦略](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview)」を参照してください。



## <a name="next-steps"></a>次のステップ

- Azure Monitor におけるデータ収集を構成する際の手順と推奨事項については、「[データ収集を構成する](best-practices-data-collection.md)」を参照してください。
