---
title: Microsoft Defender for Cloud のリリース ノート
description: Microsoft Defender for Cloud の新機能と変更内容の説明
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: reference
ms.date: 11/07/2021
ms.author: memildin
ms.openlocfilehash: 19c1cad67d170e406e01e8c7c4816ab11722e4e4
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990315"
---
# <a name="whats-new-in-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloudの新機能

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender for Cloud はアクティブな開発中であり、継続的に改善を受けています。 常に最新の開発情報を把握していただけるよう、このページでは新しい機能、バグの修正、非推奨になった機能に関する情報を提供します。

このページは頻繁に更新されるため、定期的にアクセスしてご確認ください。 

Defender for Cloud に間もなく公開される *計画中* の変更については、[Microsoft Defender for Cloud の今後の重要な変更](upcoming-changes.md)を参照してください。 

> [!TIP]
> 6 か月以上前の項目をお探しの場合は、「[Microsoft Defender for Cloud の最新情報のアーカイブ](release-notes-archive.md)」を参照してください。


## <a name="november-2021"></a>2021 年 11 月

この Ignite リリースには次のものが含まれます。

- [Azure Security Center と Azure Defender は Microsoft Defender for Cloud になりました。](#azure-security-center-and-azure-defender-become-microsoft-defender-for-cloud)
- [AWS 向けの CSPM、Amazon EKS 向けの脅威の防止、および AWS EC2](#native-cspm-for-aws-and-threat-protection-for-amazon-eks-and-aws-ec2)
- [データの機密度別のセキュリティ アクションの優先順位設定 (Azure Purview を利用) (プレビュー)](#prioritize-security-actions-by-data-sensitivity-powered-by-azure-purview-in-preview)
- [Azure Security ベンチマーク v3 を使用した拡張されたセキュリティ制御の評価](#expanded-security-control-assessments-with-azure-security-benchmark-v3)
- [一般公提供 (GA) のためにリリースされた Microsoft Sentinel コネクタのオプションの双方向のアラート同期](#microsoft-sentinel-connectors-optional-bi-directional-alert-synchronization-released-for-general-availability-ga)
- [Azure Kubernetes Service (AKS) ログを Sentinel にプッシュするための新しい推奨事項](#new-recommendation-to-push-azure-kubernetes-service-aks-logs-to-sentinel)
- [一般公開 (GA) のためにリリースされた MITRE ATT&CK® フレームワークにマップされた推奨事項](#recommendations-mapped-to-the-mitre-attck-framework---released-for-general-availability-ga)

11 月のその他の変更点は次のとおりです。

- [Microsoft 脅威と脆弱性の管理が脆弱性評価ソリューションとして追加され、一般提供 (GA) のためにリリースされました。](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution---released-for-general-availability-ga)
- [Linux 向け Microsoft Defender for Endpoint は、 Microsoft Defender for servers によってサポートされるようになり、一般公開 (GA) のためにリリースされました。](#microsoft-defender-for-endpoint-for-linux-now-supported-by-microsoft-defender-for-servers---released-for-general-availability-ga)
- [推奨事項とセキュリティの調査結果に関するスナップショットのエクスポート (プレビュー)](#snapshot-export-for-recommendations-and-security-findings-in-preview)
- [一般公開 (GA) のためにリリースされた脆弱性評価ソリューションの自動プロビジョニング](#auto-provisioning-of-vulnerability-assessment-solutions-released-for-general-availability-ga)
- [一般公開 (GA) のためにリリースされた資産インベントリのソフトウェア インベントリ フィルター](#software-inventory-filters-in-asset-inventory-released-for-general-availability-ga)

### <a name="azure-security-center-and-azure-defender-become-microsoft-defender-for-cloud"></a>Azure Security Center と Azure Defender は Microsoft Defender for Cloud になりました。

[2021 年のクラウドの状態レポート](https://info.flexera.com/CM-REPORT-State-of-the-Cloud#download)によれば、組織の 92% はマルチクラウド戦略を持つようになりました。 Microsoft では、これらの環境全体でセキュリティを一元管理し、セキュリティ チームがより効果的に作業できるようにすることを目標としています。

**Microsoft Defender For cloud** (旧称 Azure Security Center と Azure Defender) は、クライアント セキュリティの状態管理 (CSPM) およびクラウド ワークロード保護 (CWP) ソリューションであり、クラウド構成全体の脆弱性を検出し、環境の全体的なセキュリティ体制を強化し、複数のクラウド環境やハイブリッド環境でワークロードを保護します。

Ignite 2019 では、Microsoft Defender ブランドの下でデジタル資産をセキュリティで保護し、XDR テクノロジーを統合するための最も完全なアプローチを作成するための Microsoft のビジョンを紹介しました。 **Microsoft Defender For Cloud** という新しい名前で Azure Security Center と Azure defender を統合することは、Microsoft のセキュリティ オファリングとあらゆるクラウド プラットフォームをサポートする能力の統合機能を反映しています。


### <a name="native-cspm-for-aws-and-threat-protection-for-amazon-eks-and-aws-ec2"></a>AWS 向けの CSPM、Amazon EKS 向けの脅威の防止、および AWS EC2

新しい **[環境設定]** ページでは、管理グループ、サブスクリプション、および AWS アカウントの可視性と制御を向上させることができます。 このページは、AWS アカウントを大規模にオンボードするように設計されています。AWS **管理アカウント** を接続すると、既存のアカウントと将来のアカウントを自動的にオンボードすることができます。 

:::image type="content" source="media/release-notes/add-aws-account.png" alt-text="新しい [環境設定] ページを使用して、AWS アカウントを接続します。":::

AWS アカウントを追加すると、Defender for Cloud は、次のいずれかまたはすべてのプランを使用して AWS リソースを保護します。

- **Defender for Cloud の CSPM 機能** は、AWS リソースにまで拡張されています。 このエージェントレス プランでは、AWS 固有のセキュリティの推奨事項に従って AWS リソースを評価します。これは、セキュリティ スコアに含まれています。 これらのリソースは、AWS (AWS CIS、AWS PCI DSS、および AWS の基本的なセキュリティのベスト プラクティス) に固有の組み込み標準に準拠しているかについても評価されます。 Defender for Cloud の[資産インベントリ ページ](asset-inventory.md)は、Azure リソースと AWS リソースを共に管理するのに役立つマルチクラウド対応機能です。
- **Microsoft Defender For Kubernetes** のコンテナーの脅威検出と高度な防御が **Amazon EKS Linux クラスター** まで拡張されました。
- **Microsoft Defender for servers** では、脅威検出および高度な防御が、お使いの Windows と Linux の EC2 インスタンスにもたらされます。 このプランには、Microsoft Defender for Endpoint の統合ライセンス、セキュリティ ベースラインと OS レベルの評価、脆弱性評価スキャン、適応型アプリケーション制御 (AAC)、ファイルの整合性の監視 (FIM) などが含まれます。

詳細については、「[AWS アカウントを Microsoft Defender for Cloud に接続する](quickstart-onboard-aws.md)」を参照してください。


### <a name="prioritize-security-actions-by-data-sensitivity-powered-by-azure-purview-in-preview"></a>データの機密度別のセキュリティ アクションの優先順位設定 (Azure Purview を利用) (プレビュー)
データ リソースは、依然として脅威アクターによく狙われるターゲットです。 そのため、セキュリティ チームが、クラウド環境全体で機密のデータ リソースを識別し、優先順位付けして、セキュリティ保護することが重要です。

この課題に対処するために、Microsoft Defender for Cloud は[Azure Purview](../purview/overview.md) からの機密情報を統合するようになりました。 Azure Purviewは、マルチクラウドおよびオンプレミスのワークロード内のデータの機密性に関する豊富な分析情報を提供する統合されたデータガバナンスサービスです。

Azure Purview との統合により、Defender for Cloud のセキュリティの可視性が、インフラストラクチャ レベルからデータまで拡張されることで、セキュリティ チームのリソースとセキュリティ アクティビティの優先順位付けをまったく新しい方法で行うことができます。

詳細については、「[データの機密性に応じた、セキュリティ アクションの優先順位付け](information-protection.md)」を参照してください。


### <a name="expanded-security-control-assessments-with-azure-security-benchmark-v3"></a>Azure Security ベンチマーク v3 を使用した拡張されたセキュリティ制御の評価
Microsoft Defender for Cloud のセキュリティに関する推奨事項は、Azure セキュリティ ベンチマークでサポートされています。 

[Azure セキュリティ ベンチマーク](../security/benchmarks/introduction.md)は Microsoft が作成したもので、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Azure 固有のガイドラインのセットです。 この広く遵守されているベンチマークは、クラウド中心のセキュリティに重点を置いた [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) と [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) の統制に基づいています。

Ignite 2021 以降、Azure セキュリティ  ベンチマーク **v3** が、[Defender for Cloud の規制コンプライアンス ダッシュボード](update-regulatory-compliance-packages.md)で利用可能になり、Microsoft Defender for Cloud で保護されているすべての Azure サブスクリプションの新しい既定のイニシアチブとして有効になっています。 

V3 には以下の機能強化があります。 

- 業界フレームワーク [PCI-DSS v 3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf) および [CIS コントロール v8](https://www.cisecurity.org/controls/v8/) への追加のマッピング。
- 次の機能の導入によるコントロールのより詳細で実用的なガイダンス:
    - **セキュリティ原則** - 推奨事項の基礎を構築する全体的なセキュリティ目標に関する分析情報を提供します。
    - **Azure ガイダンス** - これらの目標を達成するための技術的な "方法"。

新しいコントロールには、脅威のモデル化やソフトウェア サプライ チェーンのセキュリティなどの問題のための DevOps セキュリティのほか、Azure のベスト プラクティスのためのキーと証明書の管理が含まれます。

詳細については、「[Azure セキュリティ ベンチマークの概要](/security/benchmark/azure/introduction)」を参照してください。


### <a name="microsoft-sentinel-connectors-optional-bi-directional-alert-synchronization-released-for-general-availability-ga"></a>一般公提供 (GA) のためにリリースされた Microsoft Sentinel コネクタのオプションの双方向のアラート同期

7月に、Microsoft では、[Microsoft Sentinel](../sentinel/index.yml) (Microsoft のクラウドネイティブ SIEM および SOAR ソリューション) に組み込まれているコネクタの **双方向アラート同期** をプレビュー機能として[発表しました](#azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview)。 今回、それが一般提供 (GA) リリースとなりました。

Microsoft Defender for Cloud を Microsoft Sentinel に接続すると、セキュリティ アラートの状態が 2 つのサービス間で同期されます。 たとえば、 Defender for Cloud でアラートが閉じられた場合、そのアラートは Microsoft Sentinel でも閉じられたものとして表示されます。 Defender for Cloud でアラートの状態を変更しても、同期された Microsoft Sentinel アラートを含む Microsoft Sentinel **インシデント** の状態には影響せず、同期されたアラートそのものの状態のみに影響します。

**双方向アラート同期** を有効にすると、元の  Defender for Cloud アラートの状態が、それらの Defender for Cloud アラートのコピーを含む Microsoft Sentinel インシデントと自動的に同期されます。 そのため、たとえば、Azure Defender アラートを含む Azure Sentinel インシデントが閉じられると、対応する元のアラートが Azure Defender によって自動的に閉じられます。

詳細については、「[Azure Security Center からの Azure Defender アラートの接続](../sentinel/connect-azure-security-center.md)」および「[Azure Sentinel にアラートをストリーミングする](export-to-siem.md#stream-alerts-to-microsoft-sentinel)」を参照してください。


### <a name="new-recommendation-to-push-azure-kubernetes-service-aks-logs-to-sentinel"></a>Azure Kubernetes Service (AKS) ログを Sentinel にプッシュするための新しい推奨事項

Defender for Cloud と Microsoft Sentinel を組み合わせた価値の拡張により、Microsoft Sentinel にログ データを送信していない Azure Kubernetes Service インスタンスが強調されるようになりました。

SecOps チームは、[推奨事項の詳細] ページから直接関連する Microsoft Sentinel ワークスペースを選択して、未処理のログのストリーミングを直ちに有効にすることができます。 この 2 つの製品間をシームレスに接続することで、セキュリティ チームは、ワークロード全体をカバーする完全なログ記録を確実に実行し、環境全体を容易に把握できるようになります。

新しい推奨事項である "Kubernetes サービスの診断ログを有効にする必要がある" には、修復を高速化するための [修正] オプションが含まれています。

また、同じ Sentinel ストリーミング機能を使用して、"SQL Server の監査を有効にする必要があります" という推奨事項を強化しました。 


### <a name="recommendations-mapped-to-the-mitre-attck-framework---released-for-general-availability-ga"></a>一般公開 (GA) のためにリリースされた MITRE ATT&CK® フレームワークにマップされた推奨事項

Defender for Cloud のセキュリティに関する推奨事項が拡張され、MITRE ATT&CK® フレームワークでの位置が示されるようになりました。 実際の観察に基づいた脅威アクターの戦術と手法に関するこのグローバルにアクセス可能な知識ベースは、環境に対する推奨事項に関連するリスクを理解するのに役立つ多くのコンテキストを提供します。

これらの戦術は、推奨情報にアクセスするすべての場所で確認できます。

- 関連する推奨事項に関する **Azure Resource Graph クエリ結果** には、MITRE ATT&CK® の戦術と手法が含まれています。

- **推奨事項の詳細ページ** には、関連するすべての推奨事項のマッピングが表示されます。

    :::image type="content" source="media/review-security-recommendations/tactics-window.png" alt-text="推奨事項の MITRE 戦術マッピングのスクリーンショット。":::

- **Defender For Cloud の [推奨事項] ページ** には、関連する戦術に従って推奨事項を選択するための新しい :::image type="icon" source="media/review-security-recommendations/tactics-filter-recommendations-page.png" border="false"::: フィルターがあります。

詳しくは、[セキュリティの推奨事項を確認する](review-security-recommendations.md)を参照してください。

### <a name="microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution---released-for-general-availability-ga"></a>Microsoft 脅威と脆弱性の管理が脆弱性評価ソリューションとして追加され、一般提供 (GA) のためにリリースされました。

10 月に [Microsoft 脅威と脆弱性の管理](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt)という新しい脆弱性評価のマシンへの提供をサポートするように、[Microsoft Defender for servers](defender-for-servers-introduction.md) と Microsoft Defender for Endpoint 間の統合の拡張を[発表しました](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview)。 今回、それが一般提供 (GA) リリースとなりました。

**脅威と脆弱性の管理** を使用し、[Microsoft Defender for Endpoint との統合](integration-defender-for-endpoint.md)を有効にして、準リアルタイムで脆弱性と構成の誤りを発見します。追加のエージェントや定期的なスキャンは必要ありません。 脅威と脆弱性の管理により、組織内の脅威の状況と検出結果に基づいて、脆弱性に優先順位が付けられます。

セキュリティに関する推奨事項の [[A vulnerability assessment solution should be enabled on your virtual machines]\(脆弱性評価ソリューションを仮想マシンで有効にする必要がある\)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/ffff0522-1e88-47fc-8382-2a80ba848f5d) を使用して、脅威と脆弱性の管理によって検出された[サポート対象マシン](/microsoft-365/security/defender-endpoint/tvm-supported-os?view=o365-worldwide&preserve-view=true)の脆弱性を表示します。 

既存および新規のマシンで、手動で推奨事項を修正することなく、自動的に脆弱性を表示するには、「[脆弱性評価ソリューションを自動で有効にできるようになりました (プレビュー段階)](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview)」を参照してください。

詳細については、「[Microsoft Defender for Endpoint の脅威と脆弱性の管理を使用して弱点を調査する](deploy-vulnerability-assessment-tvm.md)」を参照してください。

### <a name="microsoft-defender-for-endpoint-for-linux-now-supported-by-microsoft-defender-for-servers---released-for-general-availability-ga"></a>Linux 向け Microsoft Defender for Endpoint は、 Microsoft Defender for servers によってサポートされるようになり、一般公開 (GA) のためにリリースされました。

8月に、サポートされている Linux マシンに[Defender for Endpoint for Linux](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux) センサーを展開するためのプレビューサポートを[発表しました](#microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview)。 今回、それが一般提供 (GA) リリースとなりました。

[Microsoft Defender for servers](defender-for-servers-introduction.md) には、[Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) の統合ライセンスが含まれます。 同時に、包括的なエンドポイントの検出と対応 (EDR) 機能が提供されます。

Defender for Endpoint で脅威が検出されると、アラートがトリガーされます。 このアラートは Defender for Cloud に表示されます。 Defender for Cloud から、Defender for Endpoint コンソールにピボットし、詳細な調査を実行して攻撃の範囲を明らかにすることもできます。

詳細については、「[Security Center の統合 EDR ソリューションを使用してエンドポイントを保護する: Microsoft Defender for Endpoint](integration-defender-for-endpoint.md)」を参照してください。


### <a name="snapshot-export-for-recommendations-and-security-findings-in-preview"></a>推奨事項とセキュリティの調査結果に関するスナップショットのエクスポート (プレビュー)

Defender for Cloud では、詳細なセキュリティ アラートと推奨事項が生成されます。 これらは、ポータル内またはプログラム ツールで表示できます。 場合によっては、環境内の他の監視ツールでの追跡のために、この情報の一部または全部をエクスポートする必要があります。

Defender for Cloud の **連続エクスポート** 機能を使用して、エクスポートする "*内容*" とエクスポート先の "*場所*" を完全にカスタマイズできます。 詳細については、[Microsoft Defender for Cloud データの継続的なエクスポート](continuous-export.md)に関する記事を参照してください。

この機能は *連続* と呼ばれていますが、週単位のスナップショットをエクスポートするオプションもあります。 これまでは、これらの週単位のスナップショットは、セキュリティで保護されたスコアと規制コンプライアンス データに制限されていました。 推奨事項とセキュリティの結果をエクスポートする機能を追加しました。

### <a name="auto-provisioning-of-vulnerability-assessment-solutions-released-for-general-availability-ga"></a>一般公開 (GA) のためにリリースされた脆弱性評価ソリューションの自動プロビジョニング

10 月に、Defender for Cloud の自動プロビジョニング ページに脆弱性評価ソリューションを追加することを[発表しました](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview)。 これは、[Azure Defender for servers](defender-for-servers-introduction.md) によって保護されているサブスクリプションのAzure Virtual Machine と Azure Arc マシンに関連しています。 今回、それが一般提供 (GA) リリースとなりました。

また、[Microsoft Defender for Endpoint との統合](integration-defender-for-endpoint.md)を有効にすると、Defender for Cloud で次の脆弱性評価ソリューションを選択できるようになります。

- (**新規**) Microsoft Defender for Endpoint の Microsoft 脅威と脆弱性の管理モジュール ([リリース ノート](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution---released-for-general-availability-ga)を参照してください)
- 統合された Qualys エージェント

選択したソリューションは、サポート対象のマシン上で自動的に有効になります。

詳細については、「[マシンの脆弱性評価を自動的に構成する](auto-deploy-vulnerability-assessment.md)」を参照してください。

### <a name="software-inventory-filters-in-asset-inventory-released-for-general-availability-ga"></a>一般公開 (GA) のためにリリースされた資産インベントリのソフトウェア インベントリ フィルター

10 月に、[資産インベントリ](#software-inventory-filters-added-to-asset-inventory-in-preview) ページの特定のソフトウェアを実行し[ているマシンを選択する新](asset-inventory.md)しいフィルターを発表しました。さらに、対象のバージョンを指定することもできます。 今回、それが一般提供 (GA) リリースとなりました。

**Azure Resource Graph Explorer** でソフトウェア インベントリ データのクエリを実行することができます。

これらの機能を使用するには、[Microsoft Defender for Endpoint](integration-defender-for-endpoint.md) との統合を有効にする必要があります。 

Azure Resource Graph 用のサンプル Kusto クエリなどの詳細については、「[ソフトウェア インベントリにアクセスする](asset-inventory.md#access-a-software-inventory)」を参照してください。

### <a name="new-aks-security-policy-added-to-default-initiative--for-use-by-private-preview-customers-only"></a>既定のイニシアティブに追加された新しい AKS セキュリティ ポリシー – プライベート プレビューのお客様のみ使用

Kubernetes ワークロードが既定で確実に保護されるように、 Defender for Cloud によって、Kubernetes 受付制御を含む適用オプションなど、Kubernetes レベル ポリシーが追加され、レコメンデーションが強化されます。

このプロジェクトの一部として、Kubernetes クラスターでの展開を制御するためのポリシーと推奨事項 (既定では無効) を追加しました。 ポリシーは既定のイニシアチブに含まれていますが、関連するプライベート プレビューに登録する組織にのみ関連します。

ポリシーと推奨事項 ("Kubernetes クラスターで、脆弱性のあるイメージのデプロイを制限する必要がある") は無視しても問題はなく、環境には影響しません。 

プライベート プレビューに参加したい場合は、プライベート プレビュー リングのメンバーである必要があります。 まだメンバーでない場合は、[こちら](https://aka.ms/atscale)から要求を送信してください。 プレビューが開始されると、メンバーに通知されます。

## <a name="october-2021"></a>2021 年 10 月

10 月の更新プログラムには次のものが含まれます。

- [Microsoft 脅威と脆弱性の管理が脆弱性評価ソリューションとして追加されました (プレビュー段階)](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview)
- [脆弱性評価ソリューションを自動で有効化できるようになりました (プレビュー段階)](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview)
- [資産インベントリにソフトウェア インベントリ フィルターが追加されました (プレビュー段階)](#software-inventory-filters-added-to-asset-inventory-in-preview)
- [一部のアラートの種類のプレフィックスを "ARM_" から "VM_" に変更](#changed-prefix-of-some-alert-types-from-arm_-to-vm_)
- [Kubernetes クラスターのセキュリティに関する推奨事項のロジックの変更](#changes-to-the-logic-of-a-security-recommendation-for-kubernetes-clusters)
- [推奨事項の詳細ページに、関連する推奨事項が表示されるようになりました](#recommendations-details-pages-now-show-related-recommendations)
- [Azure Defender for Kubernetes の新しいアラート (プレビュー)](#new-alerts-for-azure-defender-for-kubernetes-in-preview)


### <a name="microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview"></a>Microsoft 脅威と脆弱性の管理が脆弱性評価ソリューションとして追加されました (プレビュー版)

[Microsoft 脅威と脆弱性の管理](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt)というマシンの新しい脆弱性評価のプロバイダーをサポートするように、[Azure Defender for servers](defender-for-servers-introduction.md) と Microsoft Defender for Endpoint 間の統合を拡張しました。 

**脅威と脆弱性の管理** を使用し、[Microsoft Defender for Endpoint との統合](integration-defender-for-endpoint.md)を有効にして、準リアルタイムで脆弱性と構成の誤りを発見します。追加のエージェントや定期的なスキャンは必要ありません。 脅威と脆弱性の管理により、組織内の脅威の状況と検出結果に基づいて、脆弱性に優先順位が付けられます。

セキュリティに関する推奨事項の [[A vulnerability assessment solution should be enabled on your virtual machines]\(脆弱性評価ソリューションを仮想マシンで有効にする必要がある\)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/ffff0522-1e88-47fc-8382-2a80ba848f5d) を使用して、脅威と脆弱性の管理によって検出された[サポート対象マシン](/microsoft-365/security/defender-endpoint/tvm-supported-os?view=o365-worldwide&preserve-view=true)の脆弱性を表示します。 

既存および新規のマシンで、手動で推奨事項を修正することなく、自動的に脆弱性を表示するには、「[脆弱性評価ソリューションを自動で有効にできるようになりました (プレビュー段階)](#vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview)」を参照してください。

詳細については、「[Microsoft Defender for Endpoint の脅威と脆弱性の管理を使用して弱点を調査する](deploy-vulnerability-assessment-tvm.md)」を参照してください。

### <a name="vulnerability-assessment-solutions-can-now-be-auto-enabled-in-preview"></a>脆弱性評価ソリューションを自動で有効にできるようになりました (プレビュー)

Security Center の自動プロビジョニング ページに、[Azure Defender for servers](defender-for-servers-introduction.md) で保護されたサブスクリプションの Azure 仮想マシンと Azure Arc マシンに対して、脆弱性評価ソリューションを自動的に有効にするオプションが追加されました。

また、[Microsoft Defender for Endpoint との統合](integration-defender-for-endpoint.md)を有効にすると、Defender for Cloud で次の脆弱性評価ソリューションを選択できるようになります。

- (**新規**) Microsoft Defender for Endpoint の Microsoft 脅威と脆弱性の管理モジュール ([リリース ノート](#microsoft-threat-and-vulnerability-management-added-as-vulnerability-assessment-solution-in-preview)を参照してください)
- 統合された Qualys エージェント

:::image type="content" source="media/deploy-vulnerability-assessment-tvm/auto-provision-vulnerability-assessment-agent.png" alt-text="Azure Security Center で、Microsoft の脅威と脆弱性の管理の自動プロビジョニングを構成します。":::

選択したソリューションは、サポート対象のマシン上で自動的に有効になります。

詳細については、「[マシンの脆弱性評価を自動的に構成する](auto-deploy-vulnerability-assessment.md)」を参照してください。

### <a name="software-inventory-filters-added-to-asset-inventory-in-preview"></a>資産インベントリにソフトウェア インベントリ フィルターが追加されました (プレビュー段階)

[資産インベントリ](asset-inventory.md) ページに、特定のソフトウェアを実行しているマシンを選択するフィルターが追加されました。さらに、対象のバージョンを指定することもできます。 

また、**Azure Resource Graph Explorer** でソフトウェア インベントリ データのクエリを実行することができます。

これらの新機能を使用するには、[Microsoft Defender for Endpoint](integration-defender-for-endpoint.md) との統合を有効にする必要があります。 

Azure Resource Graph 用のサンプル Kusto クエリなどの詳細については、「[ソフトウェア インベントリにアクセスする](asset-inventory.md#access-a-software-inventory)」を参照してください。

:::image type="content" source="media/deploy-vulnerability-assessment-tvm/software-inventory.png" alt-text="脅威と脆弱性のソリューションを有効にした場合、Security Center の資産インベントリにより、インストールされているソフトウェアによってリソースを選択するフィルターが提供されます。":::

### <a name="changed-prefix-of-some-alert-types-from-arm_-to-vm_"></a>一部のアラートの種類のプレフィックスを "ARM_" から "VM_" に変更 

2021 年 7 月に、[Azure Resource Manager アラート用の Azure Defender の論理的な再編成](release-notes.md#logical-reorganization-of-azure-defender-for-resource-manager-alerts)について発表しました。 

Azure Defender プランの論理的な再編成の一環として、21 個のアラートが [Azure Defender for Resource Manager](defender-for-resource-manager-introduction.md) から [Azure Defender for servers](defender-for-servers-introduction.md) に移動されました。

この更新プログラムでは、次の表に示すように、この再割り当てに一致するようにこれらのアラートのプレフィックスを変更し、"ARM_" を "VM_" に置き換えました。

| 元の名前                                  | この変更以降                              |
|------------------------------------------------|-----------------------------------------------|
| ARM_AmBroadFilesExclusion                      | VM_AmBroadFilesExclusion                      |
| ARM_AmDisablementAndCodeExecution              | VM_AmDisablementAndCodeExecution              |
| ARM_AmDisablement                              | VM_AmDisablement                              |
| ARM_AmFileExclusionAndCodeExecution            | VM_AmFileExclusionAndCodeExecution            |
| ARM_AmTempFileExclusionAndCodeExecution        | VM_AmTempFileExclusionAndCodeExecution        |
| ARM_AmTempFileExclusion                        | VM_AmTempFileExclusion                        |
| ARM_AmRealtimeProtectionDisabled               | VM_AmRealtimeProtectionDisabled               |
| ARM_AmTempRealtimeProtectionDisablement        | VM_AmTempRealtimeProtectionDisablement        |
| ARM_AmRealtimeProtectionDisablementAndCodeExec | VM_AmRealtimeProtectionDisablementAndCodeExec |
| ARM_AmMalwareCampaignRelatedExclusion          | VM_AmMalwareCampaignRelatedExclusion          |
| ARM_AmTemporarilyDisablement                   | VM_AmTemporarilyDisablement                   |
| ARM_UnusualAmFileExclusion                     | VM_UnusualAmFileExclusion                     |
| ARM_CustomScriptExtensionSuspiciousCmd         | VM_CustomScriptExtensionSuspiciousCmd         |
| ARM_CustomScriptExtensionSuspiciousEntryPoint  | VM_CustomScriptExtensionSuspiciousEntryPoint  |
| ARM_CustomScriptExtensionSuspiciousPayload     | VM_CustomScriptExtensionSuspiciousPayload     |
| ARM_CustomScriptExtensionSuspiciousFailure     | VM_CustomScriptExtensionSuspiciousFailure     |
| ARM_CustomScriptExtensionUnusualDeletion       | VM_CustomScriptExtensionUnusualDeletion       |
| ARM_CustomScriptExtensionUnusualExecution      | VM_CustomScriptExtensionUnusualExecution      |
| ARM_VMAccessUnusualConfigReset                 | VM_VMAccessUnusualConfigReset                 |
| ARM_VMAccessUnusualPasswordReset               | VM_VMAccessUnusualPasswordReset               |
| ARM_VMAccessUnusualSSHReset                    | VM_VMAccessUnusualSSHReset                    |
|||

[Azure Defender for Resource Manager](defender-for-resource-manager-introduction.md) プランと [Azure Defender for servers](defender-for-servers-introduction.md) プランの詳細を参照してください。

### <a name="changes-to-the-logic-of-a-security-recommendation-for-kubernetes-clusters"></a>Kubernetes クラスターのセキュリティに関する推奨事項のロジックの変更

「Kubernetesクラスターはデフォルトの名前空間を使用しないでください」という推奨事項により、さまざまなリソースタイプでデフォルトの名前空間を使用できません。 この推奨事項に含まれる2 つのリソースの種類が削除されました:ConfigMap と Secret。 

この推奨事項の詳細と Kubernetes クラスターの強化については [「Kubernetes クラスターのAzure Policyについて」を参照してください。](../governance/policy/concepts/policy-for-kubernetes.md)

### <a name="recommendations-details-pages-now-show-related-recommendations"></a>推奨事項の詳細ページに、関連する推奨事項が表示されるようになりました

さまざまな推奨事項の関係を明確にするため、**関連する推奨事項** 領域を多くの推奨事項の詳細ページに追加しました。 

これらのページに表示される 3 種類の関係は次のとおりです。

- **前提条件** - 選択した推奨事項の前に完了する必要がある推奨事項
- **代替** - 選択した推奨事項の目的を別の方法で達成する、異なる推奨事項
- **依存** - 選択した推奨事項が前提条件となる推奨事項

それぞれの関連推奨事項に対して、[影響を受けるリソース] 列に異常なリソースの数が表示されます。

> [!TIP]
> 関連推奨事項が淡色表示されている場合、その依存関係はまだ完了していないため、使用できません。

関連推奨事項の例を次に示します。

1. 脆弱性評価ソリューションがサポートされているか、お使いのコンピューターが Security Center によって確認されます。<br>
    **脆弱性評価ソリューションを仮想マシンで有効にする必要がある**

1. 見つかった場合は、検出された脆弱性に関する通知が表示されます。<br>
    **仮想マシンの脆弱性を修復する必要がある**

言うまでもなく、サポートされている脆弱性評価ソリューションが見つからない限り、Security Center では検出された脆弱性を通知できません。

そのため、次のようになります。

 - 推奨事項 #1 は推奨事項 #2 の前提条件となる
 - 推奨事項 #2 は推奨事項 #1 に依存する

:::image type="content" source="media/release-notes/related-recommendations-solution-not-found.png" alt-text="脆弱性評価ソリューションを展開するための推奨事項のスクリーンショット。":::

:::image type="content" source="media/release-notes/related-recommendations-vulnerabilities-found.png" alt-text="検出された脆弱性を解決するための推奨事項のスクリーンショット。":::



### <a name="new-alerts-for-azure-defender-for-kubernetes-in-preview"></a>Azure Defender for Kubernetes の新しいアラート (プレビュー)

Azure Defender for Kubernetes で提供される脅威の保護を拡張するために、2 つのプレビュー アラートを追加しました。

これらのアラートは、新しい機械学習モデルと Kubernetes の高度な分析に基づいて生成され、クラスターおよび、Azure Defender によって監視されるすべてのクラスター内の以前のアクティビティに対して、複数のデプロイとロールの割り当ての属性を測定します。

| アラート (アラートの種類)                                                                 | 説明                                                                                                                                                                                                                                                                                                                                                      | MITRE の方針 | 重大度 |
|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------:|----------|
| **Anomalous pod deployment (Preview) (異常なポッドのデプロイ (プレビュー))**<br>(K8S_AnomalousPodDeployment)             | Kubernetes 監査ログ分析で、以前のポッドのデプロイ アクティビティに基づいて異常なポッドのデプロイが検出されました。 このアクティビティは、デプロイ操作に見られるさまざまな特徴の相互関係を考慮した場合、異常と考えられます。 この分析によって監視される特徴には、使用されるコンテナー イメージ レジストリ、デプロイを実行するアカウント、曜日、このアカウントがポッドのデプロイを実行する頻度、操作で使用されるユーザー エージェント、ポッドのデプロイが頻繁に発生する名前空間などの特徴があります。 異常なアクティビティとしてこのアラートが発生する主な原因については、アラートの拡張プロパティに詳しい説明があります。 | 実行 | Medium |
| **Excessive role permissions assigned in Kubernetes cluster (Preview) (Kubernetes クラスターで割り当てられた過剰なロール権限 (プレビュー))**<br>(K8S_ServiceAcountPermissionAnomaly) | Kubernetes 監査ログの分析で、クラスターへの過剰なアクセス許可ロールの割り当てが検出されました。 ロールの割り当ての調査から判断して、リストされているアクセス許可は、特定のサービス アカウントにとって一般的ではありません。 この検出では、Azure によって監視されるクラスター全体での同じサービス アカウントへの以前のロールの割り当て、アクセス許可あたりのボリューム、および特定のアクセス許可の影響が考慮されます。 このアラートに使用される異常検出モデルでは、このアクセス許可が、Azure Defender によって監視されるすべてのクラスターでどのように使用されているかが考慮されます。 | Privilege Escalation (特権昇格) | 低 |
|||

Kubernetes アラートの完全な一覧については、[Kubernetes クラスターのアラート](alerts-reference.md#alerts-k8scluster)に関するセクションをご覧ください。

## <a name="september-2021"></a>2021 年 9 月

9 月に、次の更新プログラムがリリースされました。

### <a name="two-new-recommendations-to-audit-os-configurations-for-azure-security-baseline-compliance-in-preview"></a>Azure のセキュリティ ベースライン コンプライアンスの OS 構成を監査する 2 つの新しい推奨事項 (プレビュー段階)

[Windows のセキュリティ ベースライン](../governance/policy/samples/guest-configuration-baseline-windows.md)と [Linux セキュリティ ベースライン](../governance/policy/samples/guest-configuration-baseline-linux.md)へのマシンのコンプライアンスを評価するため、次の 2 つの推奨事項がリリースされました。

- Windows マシンの場合は、[Windows マシンのセキュリティ構成の脆弱性を修復する必要がある (ゲスト構成を利用)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6)
- Linux マシンの場合は、[Linux マシンのセキュリティ構成の脆弱性を修復する必要がある (ゲスト構成を利用)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda)

これらの推奨事項は、Azure Policy のゲスト構成機能を使用して、マシンの OS 構成と [Azure セキュリティ ベンチマーク](/security/benchmark/azure/overview)で定義されたベースラインとの比較を行っています。

これらの推奨事項を使用する方法の詳細については、[ゲスト構成を使用したマシンの OS 構成の強化](apply-security-baseline.md)に関するページをご覧ください。

## <a name="august-2021"></a>2021 年 8 月

8 月の更新プログラムには次のものが含まれます。

- [Microsoft Defender for Endpoint for Linux のサーバー用 Azure Defender によるサポート (プレビュー)](#microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview)
- [エンドポイント保護ソリューションを管理するための 2 つの新しい推奨事項 (プレビュー)](#two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview)
- [一般的な問題を解決するための組み込みのトラブルシューティングとガイダンス](#built-in-troubleshooting-and-guidance-for-solving-common-issues)
- [規制コンプライアンス ダッシュボードの Azure 監査レポートを一般提供 (GA) としてリリース](#regulatory-compliance-dashboards-azure-audit-reports-released-for-general-availability-ga)
- ["お使いのマシンで Log Analytics エージェントの正常性の問題を解決する必要がある" という推奨事項の廃止](#deprecated-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines)
- [コンテナー レジストリ用の Azure Defender が Azure Private Link で保護されたレジストリ内の脆弱性をスキャン](#azure-defender-for-container-registries-now-scans-for-vulnerabilities-in-registries-protected-with-azure-private-link)
- [Security Center による Azure Policy のゲスト構成拡張機能の自動プロビジョニング (プレビュー)](#security-center-can-now-auto-provision-the-azure-policys-guest-configuration-extension-in-preview)
- [Azure Defender プランを有効にする推奨事項での "強制" のサポート](#recommendations-to-enable-azure-defender-plans-now-support-enforce)
- [推奨事項データの CSV エクスポートを 20 MB に制限](#csv-exports-of-recommendation-data-now-limited-to-20-mb)
- [[推奨事項] ページに複数のビューが含まれる](#recommendations-page-now-includes-multiple-views)

### <a name="microsoft-defender-for-endpoint-for-linux-now-supported-by-azure-defender-for-servers-in-preview"></a>Microsoft Defender for Endpoint for Linux のサーバー用 Azure Defender によるサポート (プレビュー)

[サーバー用 Azure Defender](defender-for-servers-introduction.md) には、[Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) の統合ライセンスが含まれます。 同時に、包括的なエンドポイントの検出と対応 (EDR) 機能が提供されます。

Defender for Endpoint で脅威が検出されると、アラートがトリガーされます。 アラートは Security Center に表示されます。 Security Center から、Defender for Endpoint コンソールにピボットし、詳細な調査を実行して攻撃の範囲を明らかにすることもできます。

プレビュー期間中は、既に Windows マシンにデプロイしたかどうかに応じて 2 つの方法のいずれかで、サポートされる Linux マシンに [Defender for Endpoint for Linux](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint-linux) センサーをデプロイします。

- [Defender for Cloud の強化されたセキュリティ機能を有効にし、Microsoft Defender for Endpoint for Windows を使用している既存のユーザー](integration-defender-for-endpoint.md#existing-users-with-defender-for-clouds-enhanced-security-features-enabled-and-microsoft-defender-for-endpoint-for-windows)
- [Microsoft Defender for Endpoint for Windows との統合を有効にしたことがない新規ユーザー](integration-defender-for-endpoint.md?tabs=linux#new-users-whove-never-enabled-the-integration-with-microsoft-defender-for-endpoint-for-windows)

詳細については、「[Security Center の統合 EDR ソリューションを使用してエンドポイントを保護する: Microsoft Defender for Endpoint](integration-defender-for-endpoint.md)」を参照してください。

### <a name="two-new-recommendations-for-managing-endpoint-protection-solutions-in-preview"></a>エンドポイント保護ソリューションを管理するための 2 つの新しい推奨事項 (プレビュー)

マシンにエンドポイント保護ソリューションをデプロイして維持するための 2 つの **プレビュー** 推奨事項が追加されています。 どちらの推奨事項にも、Azure 仮想マシンと Azure Arc 対応サーバーに接続されているコンピューターのサポートが含まれています。

|推奨 |Description |重大度 |
|---|---|---|
|[エンドポイント保護をマシンにインストールする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/4fb67663-9ab9-475d-b026-8c544cced439) |脅威と脆弱性からマシンを保護するには、サポートされているエンドポイント保護ソリューションをインストールします。  <br> <a href="/azure/security-center/endpoint-protection-recommendations-technical">マシンのエンドポイント保護を評価する方法の詳細をご覧ください。</a><br />(関連ポリシー:[エンドポイント保護の不足を Azure Security Center で監視する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faf6cd1bd-1635-48cb-bde7-5b15693900b9)) |高 |
|[Endpoint Protection の正常性の問題を、お使いのコンピューターで解決する必要があります](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/37a3689a-818e-4a0e-82ac-b1392b9bb000) |仮想マシンでエンドポイント保護の正常性の問題を解決して、それらを最新の脅威と脆弱性から保護します。 Azure Security Center でサポートされているエンドポイント保護ソリューションについては、[こちら](./supported-machines-endpoint-solutions-clouds.md?tabs=features-windows)を参照してください。 エンドポイント保護の評価については、<a href='/azure/security-center/endpoint-protection-recommendations-technical'>こちら</a>を参照してください。<br />(関連ポリシー:[エンドポイント保護の不足を Azure Security Center で監視する](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2faf6cd1bd-1635-48cb-bde7-5b15693900b9)) |中 |
|||

> [!NOTE]
> 推奨事項の更新間隔は 8 時間と表示されますが、シナリオによっては、さらに長くかかる場合があります。 たとえば、オンプレミスのマシンが削除されたとき、Security Center が削除を識別するのに 24 時間かかります。 その後、評価によって情報が返されるまでに最大 8 時間かかります。 したがって、この特定の状況では、影響を受けたリソースの一覧からマシンが削除されるまでに 32 時間かかることがあります。
>
> :::image type="content" source="media/release-notes/freshness-interval.png" alt-text="Security Center のこれら 2 つの新しい推奨事項の更新間隔インジケーター":::

### <a name="built-in-troubleshooting-and-guidance-for-solving-common-issues"></a>一般的な問題を解決するための組み込みのトラブルシューティングとガイダンス

Azure portal の Azure Security Center ページの新しい専用領域は、Azure Security Center と Azure Defender に関する一般的な課題を解決するための、拡大し続ける照合されたセルフヘルプ資料のセットを提供します。

問題が発生している場合、またはサポート チームからのアドバイスを求めている場合には、 **[問題の診断と解決]** は、ソリューションを探すために役立つもう 1 つのツールです。

:::image type="content" source="media/release-notes/solve-problems.png" alt-text="Security Center の [問題の診断と解決] ページ":::
 

### <a name="regulatory-compliance-dashboards-azure-audit-reports-released-for-general-availability-ga"></a>規制コンプライアンス ダッシュボードの Azure 監査レポートを一般提供 (GA) としてリリース

規制コンプライアンス ダッシュボードのツールバーには、サブスクリプションに適用される基準に関する Azure および Dynamics の証明書レポートが用意されています 

:::image type="content" source="media/release-notes/audit-reports-regulatory-compliance-dashboard.png" alt-text="監査レポートを生成するためのボタンが表示された規制コンプライアンス ダッシュボードのツールバー。":::

関連するレポートの種類 (PCI、SOC、ISO など) のタブを選択し、フィルターを使用すると、必要なレポートを見つけることができます。

詳細については、[コンプライアンス状態レポートと証明書の生成](regulatory-compliance-dashboard.md#generate-compliance-status-reports-and-certificates)に関するページを参照してください。

:::image type="content" source="media/release-notes/audit-reports-list-regulatory-compliance-dashboard-ga.png" alt-text="使用可能な Azure 監査レポートのタブ リスト。ISO レポート、SOC レポート、PCI などのタブが表示されています。":::

### <a name="deprecated-recommendation-log-analytics-agent-health-issues-should-be-resolved-on-your-machines"></a>"お使いのマシンで Log Analytics エージェントの正常性の問題を解決する必要がある" という推奨事項の廃止

**お使いのマシンで Log Analytics エージェントの正常性の問題を解決する必要がある** という推奨事項は、Security Center のクラウド セキュリティ態勢管理 (CSPM) の取り組みと整合性がないという点で、セキュリティ スコアに影響することがわかりました。 通常、CSPM はセキュリティ構成の誤りを特定する方法に関連するものです。 エージェントの正常性の問題は、このカテゴリの問題には該当しません。

また、この推奨事項は、Security Center に関連する他のエージェントと比較した場合、特異なものです。これは、正常性の問題に関する推奨事項を持つ唯一のエージェントだからです。

この推奨事項は廃止されました。

この廃止を受けて、Log Analytics エージェントのインストールに関する推奨事項についても少し変更しました (**Log Analytics エージェントを ... にインストールする必要があります**)。

この変更は、セキュリティ スコアに影響を与える可能性があります。 ほとんどのサブスクリプションでは、変更によってスコアが上がることが期待できますが、インストールの推奨事項を更新すると、場合によってはスコアが下がる可能性があります。

> [!TIP]
> また、[[資産インベントリ]](asset-inventory.md) ページも、この変更の影響も受けています。マシンの監視状態 (監視、監視なし、または部分的な監視 - 正常性の問題があるエージェントを表す状態) が表示されるためです。


### <a name="azure-defender-for-container-registries-now-scans-for-vulnerabilities-in-registries-protected-with-azure-private-link"></a>コンテナー レジストリ用の Azure Defender が Azure Private Link で保護されたレジストリ内の脆弱性をスキャン
コンテナー レジストリ用の Azure Defender に、Azure Container Registry レジストリ内のイメージをスキャンする脆弱性スキャナーが含まれています。 レジストリをスキャンして結果を修復する方法については、「[コンテナー レジストリ用の Azure Defender を使用してイメージの脆弱性をスキャンする](defender-for-container-registries-usage.md)」を参照してください。

Azure Container Registry でホストされるレジストリへのアクセスを制限するには、「[Azure Private Link を使用して Azure Container Registry にプライベートで接続する](../container-registry/container-registry-private-link.md)」の説明に従って、仮想ネットワークのプライベート IP アドレスをレジストリ エンドポイントに割り当てて、Azure Private Link を使用します。

追加の環境やユースケースをサポートする継続的な取り組みの一環として、[Azure Private Link](../private-link/private-link-overview.md) で保護されるコンテナー レジストリのスキャンも Azure Defender によって行われるようになりました。


### <a name="security-center-can-now-auto-provision-the-azure-policys-guest-configuration-extension-in-preview"></a>Security Center による Azure Policy のゲスト構成拡張機能の自動プロビジョニング (プレビュー)
Azure Policy では、Azure 内で実行するマシンと Arc に接続されたマシンの両方に対して、マシン内の設定を監査できます。 検証は、クライアントとゲスト構成拡張機能によって実行されます。 詳細については、「[Azure Policy のゲストの構成の理解](../governance/policy/concepts/guest-configuration.md)」を参照してください。

この更新プログラムにより、サポートされているすべてのコンピューターにこの拡張機能を自動的にプロビジョニングするように Security Center を設定できるようになりました。 

:::image type="content" source="media/release-notes/auto-provisioning-guest-configuration.png" alt-text="ゲスト構成拡張の自動デプロイを有効にします。":::

自動プロビジョニングのしくみの詳細については、[エージェントと拡張機能の自動プロビジョニングの構成](enable-data-collection.md)に関する記事を参照してください。

### <a name="recommendations-to-enable-azure-defender-plans-now-support-enforce"></a>Azure Defender プランを有効にする推奨事項での "強制" のサポート
Security Center には、新しく作成されたリソースが安全な方法でプロビジョニングされるために役立つ、**強制** と **拒否** という 2 つの機能があります。 推奨事項によってこれらのオプションが提供されると、だれかがリソースを作成しようとするとき常に、セキュリティ要件が満たされていることを保証できます。

- **[拒否]** によって、異常なリソースの作成が止められます。
- **[強制]** によって、準拠していないリソースが作成されたときに自動的に修復されます。

この更新プログラムによって、強制オプションを推奨事項で使用して Azure Defender プラン ( **[Azure Defender for App Service を有効にする必要がある]** 、 **[Azure Defender for Key Vault を有効にする必要がある]** 、 **[Azure Defender for Storage を有効にする必要がある]** など) を有効にできるようになりました。

これらのオプションの詳細については、「[適用/拒否の推奨事項を使用した構成ミスの防止](prevent-misconfigurations.md)」を参照してください。

### <a name="csv-exports-of-recommendation-data-now-limited-to-20-mb"></a>推奨事項データの CSV エクスポートを 20 MB に制限

Security Center の推奨事項データをエクスポートする際に 20 MB に制限します。

:::image type="content" source="media/upcoming-changes/download-csv-report.png" alt-text="推奨事項に関するデータをエクスポートするための Security Center の [CSV レポートのダウンロード] ボタン。":::

大量のデータをエクスポートする必要がある場合は、選択する前に使用可能なフィルターを使用するか、またはサブスクリプションのサブセットを選択してデータをバッチでダウンロードします。

:::image type="content" source="media/upcoming-changes/filter-subscriptions.png" alt-text="Azure portal でサブスクリプションをフィルター処理する":::

詳細については、[セキュリティに関する推奨事項の CSV エクスポートの実行](continuous-export.md#manual-one-time-export-of-alerts-and-recommendations)に関するページを参照してください。



### <a name="recommendations-page-now-includes-multiple-views"></a>[推奨事項] ページに複数のビューが含まれる

[推奨事項] ページに、リソースに関連する推奨事項を別の方法で表示できるように 2 つのタブが用意されました。

- **[Secure score recommendations]\(セキュア スコア推奨事項\)** - このタブを使用して、セキュリティ コントロール別にグループ化された推奨事項の一覧を表示します。 これらのコントロールの詳細については、「[セキュリティ コントロールとその推奨事項](secure-score-security-controls.md#security-controls-and-their-recommendations)」を参照してください。
- **[すべての推奨事項]** - このタブを使用して、推奨事項の一覧をフラット リストとして表示します。 このタブは、推奨事項を生成したイニシアチブ (規制コンプライアンス基準を含む) を理解するのにも役立ちます。 イニシアチブと、推奨事項との関係の詳細については、「[セキュリティ ポリシー、イニシアチブ、および推奨事項とは](security-policy-concept.md)」を参照してください。

:::image type="content" source="media/release-notes/recommendations-tabs.png" alt-text="Azure Security Center の推奨事項一覧の表示を変更するためのタブ。":::

## <a name="july-2021"></a>2021 年 7 月

7 月の更新プログラムには次のものが含まれます。

- [Azure Sentinel コネクタにオプションの双方向アラート同期が含まれる (プレビュー)](#azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview)
- [Azure Resource Manager アラート用の Azure Defender の論理的な再編成](#logical-reorganization-of-azure-defender-for-resource-manager-alerts)                                           
- [Azure Disk Encryption (ADE) を有効にする推奨事項の強化](#enhancements-to-recommendation-to-enable-azure-disk-encryption-ade)                                     
- [セキュア スコアと規制コンプライアンス データの連続エクスポートを一般提供 (GA) としてリリース](#continuous-export-of-secure-score-and-regulatory-compliance-data-released-for-general-availability-ga)
- [規制コンプライアンス評価の変更によるワークフロー自動化のトリガー (GA)](#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-ga)
- [Assessments API フィールド 'FirstEvaluationDate' および 'StatusChangeDate' がワークスペース スキーマとロジック アプリで使用可能](#assessments-api-field-firstevaluationdate-and-statuschangedate-now-available-in-workspace-schemas-and-logic-apps)
- ['Compliance over time' ブック テンプレートが Azure Monitor ブック ギャラリーに追加](#compliance-over-time-workbook-template-added-to-azure-monitor-workbooks-gallery)

### <a name="azure-sentinel-connector-now-includes-optional-bi-directional-alert-synchronization-in-preview"></a>Azure Sentinel コネクタにオプションの双方向アラート同期が含まれる (プレビュー)

Security Center には、[Azure Sentinel](../sentinel/index.yml)(Azure のクラウドネイティブな SIEM および SOAR ソリューション) がネイティブに統合されています。 

Azure Sentinel には、サブスクリプションとテナントのレベルで Azure Security Center 用の組み込みコネクタが含まれています。 詳細については、「[Azure Sentinel にアラートをストリーミングする](export-to-siem.md#stream-alerts-to-microsoft-sentinel)」を参照してください。

Azure Defender を Azure Sentinel に接続すると、Azure Defender アラートの状態が Azure Sentinel に取り込まれる、2 つのサービス間で同期されます。 たとえば、Azure Defender でアラートが閉じられた場合、そのアラートは Azure Sentinel でも閉じられたと表示されます。 Azure Defender でアラートの状態を変更しても、同期された Azure Sentinel アラートを含む Azure Sentinel **インシデント** の状態には影響せず、同期されたアラートそのものの状態のみに影響します。

プレビュー機能である、この **双方向アラート同期** を有効にすると、元の Azure Defender アラートの状態が、それらの Azure Defender アラートのコピーを含む Azure Sentinel インシデントと自動的に同期されます。 そのため、たとえば、Azure Defender アラートを含む Azure Sentinel インシデントが閉じられると、対応する元のアラートが Azure Defender によって自動的に閉じられます。

詳細については、「[Azure Security Center からの Azure Defender アラートの接続](../sentinel/connect-azure-security-center.md)」を参照してください。

### <a name="logical-reorganization-of-azure-defender-for-resource-manager-alerts"></a>Azure Resource Manager アラート用の Azure Defender の論理的な再編成

次に示すアラートは、[Azure Defender For Resource Manager](defender-for-resource-manager-introduction.md) プランの一部として提供されていました。

Azure Defender プランの論理的な再編成の一環として、一部のアラートが **Azure Defender for Resource Manager** から **サーバー用 Azure Defender** に移動されました。

アラートは、次の 2 つの主要原則に従って編成されます。

- さまざまな種類の Azure リソースにわたるコントロールプレーンの保護を提供するアラートは、Azure Defender for Resource Manager の一部になります。
- 特定のワークロードを保護するアラートは、対応するワークロードに関連する Azure Defender プランに含まれます。

これらは、Azure Defender for Resource Manager に属するアラートでしたが、この変更の結果として、現在はサーバー用 Azure Defender にあります。

- ARM_AmBroadFilesExclusion
- ARM_AmDisablementAndCodeExecution
- ARM_AmDisablement
- ARM_AmFileExclusionAndCodeExecution
- ARM_AmTempFileExclusionAndCodeExecution
- ARM_AmTempFileExclusion
- ARM_AmRealtimeProtectionDisabled
- ARM_AmTempRealtimeProtectionDisablement
- ARM_AmRealtimeProtectionDisablementAndCodeExec
- ARM_AmMalwareCampaignRelatedExclusion
- ARM_AmTemporarilyDisablement
- ARM_UnusualAmFileExclusion
- ARM_CustomScriptExtensionSuspiciousCmd
- ARM_CustomScriptExtensionSuspiciousEntryPoint
- ARM_CustomScriptExtensionSuspiciousPayload
- ARM_CustomScriptExtensionSuspiciousFailure
- ARM_CustomScriptExtensionUnusualDeletion
- ARM_CustomScriptExtensionUnusualExecution
- ARM_VMAccessUnusualConfigReset
- ARM_VMAccessUnusualPasswordReset
- ARM_VMAccessUnusualSSHReset

[Azure Defender for Resource Manager](defender-for-resource-manager-introduction.md) と[サーバー用 Azure Defender](defender-for-servers-introduction.md) プランの詳細を参照してください。


### <a name="enhancements-to-recommendation-to-enable-azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE) を有効にする推奨事項の強化

ユーザーからのフィードバックに従い、 **[仮想マシンにディスク暗号化を適用する必要がある]** という推奨事項の名前を変更しました。

新しい推奨事項には同じ評価 ID が使用され、 **[コンピューティングとストレージのリソース間で一時ディスク、キャッシュ、データ フローを仮想マシンによって暗号化する必要がある]** と呼ばれます。

このセキュリティ強化推奨事項の目的をわかりやすくするために説明も更新しています。

| 推奨                                                                                               | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             | 重大度 |
|--------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------:|
| **コンピューティングとストレージのリソース間で一時ディスク、キャッシュ、データ フローを仮想マシンによって暗号化する必要がある** | 既定では、仮想マシンの OS とデータ ディスクは、プラットフォーム マネージド キーを使用して保存時に暗号化されます。一時ディスクとデータ キャッシュは暗号化されません。また、データがコンピューティング リソースとストレージ リソース間でやり取りされる際には暗号化されません。 Azure でのさまざまなディスク暗号化テクノロジの比較については、 https://aka.ms/diskencryptioncomparison を参照してください。<br>Azure Disk Encryption を使用してこのデータをすべて暗号化します。 次の場合は、この推奨事項を無視します。(1) ホスト上の暗号化機能を使用している場合。または、(2) Managed Disks のサーバー側暗号化がセキュリティ要件を満たしている場合。 詳細については、「Azure Disk Storage のサーバー側暗号化」を参照してください。 | 高     |
|                                                                                                              |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |          |


### <a name="continuous-export-of-secure-score-and-regulatory-compliance-data-released-for-general-availability-ga"></a>セキュア スコアと規制コンプライアンス データの連続エクスポートを一般提供 (GA) としてリリース

[連続エクスポート](continuous-export.md)によって、セキュリティ アラートと推奨事項を環境内の他の監視ツールで追跡するためにエクスポートするメカニズムが提供されます。

連続エクスポートを設定するときに、エクスポートする内容とエクスポート先の場所を構成します。 詳細については、[連続エクスポートの概要](continuous-export.md)に関するページを参照してください。

時間経過と共に、この機能の強化および拡張を行っています。

- 2020 年 11 月、**セキュア スコア** への変更をストリーミングするための **プレビュー** オプションを追加しました。<br/>詳細については、「[連続エクスポートでセキュア スコアが利用可能に (プレビュー)](release-notes-archive.md#secure-score-is-now-available-in-continuous-export-preview)」を参照してください。

- 2020 年 12 月、**規制コンプライアンス評価データ** への変更をストリーミングするための **プレビュー** オプションを追加しました。<br/>詳細については、「[連続エクスポートで新しいデータ型が利用可能に (プレビュー)](release-notes-archive.md#continuous-export-gets-new-data-types-and-improved-deployifnotexist-policies)」を参照してください。

この更新プログラムでは、これら 2 つのオプションが一般提供 (GA) としてリリースされます。 


### <a name="workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-ga"></a>規制コンプライアンス評価の変更によるワークフロー自動化のトリガー (GA)

2021 年 2 月、ワークフロー自動化のトリガー オプションに、規制コンプライアンス評価の変更という、**プレビュー** の 3 つ目のデータの種類が追加されました。 詳細については、「[規制コンプライアンス評価の変更によって、ワークフロー自動化をトリガー可能](release-notes-archive.md#workflow-automations-can-be-triggered-by-changes-to-regulatory-compliance-assessments-in-preview)」を参照してください。

この更新プログラムでは、このトリガー オプションが一般提供 (GA) としてリリースされます。

ワークフローの自動化ツールの使い方については、「[Security Center のトリガーへの応答を自動化する](workflow-automation.md)」を参照してください。

:::image type="content" source="media/release-notes/regulatory-compliance-triggers-workflow-automation.png" alt-text="規制コンプライアンス評価の変更を使用した、ワークフロー自動化のトリガー。" lightbox="media/release-notes/regulatory-compliance-triggers-workflow-automation.png":::

### <a name="assessments-api-field-firstevaluationdate-and-statuschangedate-now-available-in-workspace-schemas-and-logic-apps"></a>Assessments API フィールド 'FirstEvaluationDate' および 'StatusChangeDate' がワークスペース スキーマとロジック アプリで使用可能

2021 年 5 月、2 つのフィールド **FirstEvaluationDate** および **StatusChangeDate** を含むように Assessment API を更新しました。 詳細については、「[Assessments API を 2 つの新しいフィールドで拡張](release-notes-archive.md#assessments-api-expanded-with-two-new-fields)」を参照してください。

これらのフィールドには、REST API、Azure Resource Graph、連続エクスポート、および CSV エクスポートを使用してアクセスできました。

この変更では、Log Analytics ワークスペースとロジック アプリで情報を使用できるようにしています。


### <a name="compliance-over-time-workbook-template-added-to-azure-monitor-workbooks-gallery"></a>'Compliance over time' ブック テンプレートが Azure Monitor ブック ギャラリーに追加

3 月に、Security Center での統合 Azure Monitor ブック エクスペリエンスを発表しました (「[Azure Monitor ブックを Security Center に統合し、テンプレートを 3 件提供](release-notes-archive.md#azure-monitor-workbooks-integrated-into-security-center-and-three-templates-provided)」を参照してください)。

最初のリリースには、組織のセキュリティ体制に関して動的レポートとビジュアル レポートを構築するための 3 つのテンプレートが含まれていました。

ここでは、適用される規制または業界標準に関するサブスクリプションのコンプライアンスを追跡する専用のブックを追加しました。 

ここに挙げたレポートを使用する方法や、独自のレポートを作成する方法の詳細については、「[豊富な機能を備えた対話型の Security Center データ レポートを作成する](custom-dashboards-azure-workbooks.md)」を参照してください。

:::image type="content" source="media/custom-dashboards-azure-workbooks/compliance-over-time-details.png" alt-text="Azure Security Center のコンプライアンスの時間経過を示すブック":::


## <a name="june-2021"></a>2021 年 6 月

6 月の更新プログラムには次のものが含まれます。

- [Azure Defender for Key Vault の新しいアラート](#new-alert-for-azure-defender-for-key-vault)
- [既定では無効になっているカスタマー マネージド キー (CMK) を使用して暗号化するための推奨事項](#recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default)
- [Kubernetes アラートのプレフィックスを "AKS_" から "K8S_" に変更](#prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_)
- ["システムの更新プログラムを適用する" セキュリティ コントロールの 2 つの推奨事項を非推奨化](#deprecated-two-recommendations-from-apply-system-updates-security-control)


### <a name="new-alert-for-azure-defender-for-key-vault"></a>Azure Defender for Key Vault の新しいアラート

Azure Defender for Key Vault で提供される脅威の保護を拡張するために、次のアラートを追加しました。

| アラート (アラートの種類)                                                                 | 説明                                                                                                                                                                                                                                                                                                                                                      | MITRE の方針 | 重大度 |
|------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------:|----------|
| Access from a suspicious IP address to a key vault (疑わしい IP アドレスからのキー コンテナーへのアクセス)<br>(KV_SuspiciousIPAccess)  | キー コンテナーが、Microsoft 脅威インテリジェンスによって疑わしい IP アドレスとして識別された IP によって正常にアクセスされました。 これは、お使いのインフラストラクチャが侵害を受けたことを示しているおそれがあります。 さらに詳しく調査することをお勧めします。 [Microsoft の脅威インテリジェンス機能](https://go.microsoft.com/fwlink/?linkid=2128684)の詳細に関するページを参照してください。 | 資格情報アクセス                            | Medium   |
|||

詳細については、次を参照してください。
- [Azure Defender for Key Vault の概要](defender-for-resource-manager-introduction.md)
- [Azure Defender の Key Vault アラートに対応する](defender-for-key-vault-usage.md)
- [Azure Defender for Key Vault で提供されるアラートの一覧](alerts-reference.md#alerts-azurekv)


### <a name="recommendations-to-encrypt-with-customer-managed-keys-cmks-disabled-by-default"></a>既定では無効になっているカスタマー マネージド キー (CMK) を使用して暗号化するための推奨事項

Security Center には、カスタマー マネージド キーを使用して保存データを暗号化するための次のような複数の推奨事項があります。

- コンテナー レジストリは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある
- Azure Cosmos DB アカウントでは保存データを暗号化するためにカスタマー マネージド キーを使用する必要がある
- Azure Machine Learning ワークスペースは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある

Azure のデータは、プラットフォーム マネージド キーを使用して自動的に暗号化されるため、カスタマー マネージド キーの使用は、組織が適用を選択した特定のポリシーに準拠するために必要な場合にのみ適用する必要があります。

この変更に伴い、CMK を使用するための推奨事項は、**既定では無効** になりました。 組織に関係がある場合は、対応するセキュリティ ポリシーの *Effect* パラメーターを **AuditIfNotExists** または **Enforce** に変更することにより、有効にすることができます。 詳細については、「[セキュリティ ポリシーの有効化](tutorial-security-policy.md#enable-a-security-policy)」を参照してください。

この変更は、次の例で示すように、新しいプレフィックス **[必要に応じて有効にする]** を使用して推奨事項の名前に反映されています。

- [必要に応じて有効にする] ストレージ アカウントでは、カスタマー マネージド キーを使用して保存データを暗号化する必要がある
- [必要に応じて有効にする] コンテナー レジストリは、カスタマー マネージド キー (CMK) を使用して暗号化する必要がある
- [必要に応じて有効にする] Cosmos DB アカウントでは、カスタマー マネージド キーを使用して保存データを暗号化する必要がある

:::image type="content" source="media/upcoming-changes/customer-managed-keys-disabled.png" alt-text="Security Center の CMK に関する推奨事項は、既定では無効になります。" lightbox="media/upcoming-changes/customer-managed-keys-disabled.png":::


### <a name="prefix-for-kubernetes-alerts-changed-from-aks_-to-k8s_"></a>Kubernetes アラートのプレフィックスを "AKS_" から "K8S_" に変更

オンプレミスおよびマルチ クラウド環境でホストされている Kubernetes クラスターを保護するため、Azure Defender for Kubernetes が最近拡張されました。 詳細については、「[Azure Defender for Kubernetes を使用したハイブリッドおよびマルチクラウド Kubernetes デプロイの保護 (プレビュー)](release-notes-archive.md#use-azure-defender-for-kubernetes-to-protect-hybrid-and-multi-cloud-kubernetes-deployments-in-preview)」をご覧ください。

Azure Defender for Kubernetes によって提供されるセキュリティ アラートが Azure Kubernetes Service 上のクラスターに制限されなくなったという事実を反映するために、アラートの種類のプレフィックスを "AKS_" から "K8S_" に変更しました。 必要に応じて、名前と説明も更新されています。 たとえば、このアラートです。

|アラート (アラートの種類)|説明|
|----|----|
|Kubernetes penetration testing tool detected (Kubernetes 侵入テスト ツールが検出されました)<br>(**AKS** _PenTestToolsKubeHunter)|Kubernetes 監査ログ分析によって、**AKS** クラスターでの Kubernetes 侵入テスト ツールの使用が検出されました。 この動作は、正当である可能性もありますが、攻撃者はこのような公開ツールを悪意のある目的で使用する可能性があります。
|||

変更後:

|アラート (アラートの種類)|説明|
|----|----|
|Kubernetes penetration testing tool detected (Kubernetes 侵入テスト ツールが検出されました)<br>(**K8S** _PenTestToolsKubeHunter)|Kubernetes 監査ログ分析によって、**Kubernetes** クラスターでの Kubernetes 侵入テスト ツールの使用が検出されました。 この動作は、正当である可能性もありますが、攻撃者はこのような公開ツールを悪意のある目的で使用する可能性があります。|
|||

"AKS_" から始まるアラートを参照する抑制ルールは自動的に変換されています。 SIEM エクスポート、またはアラートの種類別に Kubernetes アラートを参照するカスタム自動化スクリプトを設定している場合は、それらを新しいアラートの種類で更新する必要があります。

Kubernetes アラートの完全な一覧については、[Kubernetes クラスターのアラート](alerts-reference.md#alerts-k8scluster)に関するセクションをご覧ください。

### <a name="deprecated-two-recommendations-from-apply-system-updates-security-control"></a>"システムの更新プログラムを適用する" セキュリティ コントロールの 2 つの推奨事項を非推奨化

次の 2 つの推奨事項は非推奨になりました。

- **クラウド サービス ロールの OS バージョンを更新する必要がある** - Azure では既定で、ゲスト OS は、サービス構成 (.cscfg) に指定した Windows Server 2016 などの OS ファミリ内でサポートされている最新のイメージに定期的に更新されます。
- **Kubernetes Service を脆弱性のない Kubernetes バージョンにアップグレードする必要がある** - この推奨事項の評価は、目標とする広範囲には及んでいません。 推奨事項は、よりお客様のセキュリティ ニーズに合った強化バージョンに置き換えられる予定です。
