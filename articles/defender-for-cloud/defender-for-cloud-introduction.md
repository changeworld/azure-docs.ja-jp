---
title: Microsoft Defender for Cloud - 概要
description: Microsoft Defender for Cloud を使用して、Azure、ハイブリッド、マルチクラウドのリソースとワークロードを保護します。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.custom: mvc
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: b809efec6a34179b97f7389cf09662a2600d61b4
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373423"
---
# <a name="what-is-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud とは

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender for Cloud は、セキュリティ体制管理と脅威保護のためのツールです。 クラウド リソースのセキュリティ体制が強化され、統合された Microsoft Defender プランにより、Defender for Cloud は Azure、ハイブリッド、その他のクラウド プラットフォームで実行されているワークロードを保護します。

Defender for Cloud により、リソースの強化、セキュリティ体制の追跡、サイバー攻撃からの保護、セキュリティ管理の効率化を行うのに必要なツールが提供されます。 ネイティブに統合されているので、Defender for Cloud のデプロイは簡単で、リソースをセキュリティで保護するための簡単な自動プロビジョニングが既定で提供されます。

クラウドとオンプレミスでリソースとワークロードのセキュリティを管理する場合、Defender for Cloud は 3 つの重要なニーズを満たします。

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-synopsis.png" alt-text="Microsoft Defender for Cloud のコア機能について。":::

|セキュリティ要件  | Defender for Cloud ソリューション|
|---------|---------|
|**継続的な評価** - 現在のセキュリティの状態を理解します。   | **セキュリティ スコア** - 現在のセキュリティの状況を一目で確認できるようにする 1 つのスコア。スコアが高くなるほど、識別されたリスク レベルが低下します。       |
|**セキュリティ保護** - 接続されているすべてのリソースとサービスを強化します。 | **セキュリティに関する推奨事項** - ご使用の体制を改善するためのカスタマイズされ、優先順位が付けられた強化タスク。 推奨事項に記載されている詳細な修復手順に従って、推奨事項を実装します。 多くの推奨事項に対して、Defender for Cloud には、自動化された実装のための "修正" ボタンが用意されています。|
|**防御** - これらのリソースとサービスに対する脅威を検出して解決します。 | **セキュリティ アラート** - 強化されたセキュリティ機能を有効にすると、Defender for Cloud によってリソースとワークロードに対する脅威が検出されます。 これらのアラートは Azure portal に表示され、Defender for Cloud は組織内の関連担当者にメールで送信することもできます。 必要に応じて、SIEM、SOAR、または IT サービス管理ソリューションにアラートをストリーミングすることもできます。 |

## <a name="posture-management-and-workload-protection"></a>体制管理とワークロード保護

Microsoft Defenders for Cloud の機能は、クラウド セキュリティの 2 つの大きな柱であるクラウド セキュリティ体制管理とクラウド ワークロード保護をカバーしています。

### <a name="cloud-security-posture-management-cspm"></a>クラウド セキュリティ態勢管理 (CSPM)

Defender for Cloud では、体制管理機能によって次の機能が提供されます。

- **可視性** - 現在のセキュリティ状況の把握に役立ちます
- **強化ガイダンス** - セキュリティの効率的かつ効果的な向上に役立ちます

これらの目標を達成できるようにする Defender for Cloud の中心となる機能が、**セキュリティ スコア** です。 Defender for Cloud は、セキュリティの問題について、リソース、サブスクリプション、組織を継続的に評価します。 その後、すべての結果を 1 つのスコアに集約して、現在のセキュリティの状況を一目で確認できるようにします。スコアが高くなるほど、識別されたリスク レベルが低下します。

Defender for Cloud を初めて開く場合は、次のように可視性と強化の目標を満たします。

1. [Azure セキュリティ ベンチマーク](/security/benchmark/azure/overview)のガイダンスと比較して、接続されているリソースの評価に基づいてサブスクリプションの **セキュリティ スコア** を生成します。 スコアを使用してセキュリティ体制を理解し、コンプライアンス ダッシュボードを使用して、組み込みのベンチマークへの準拠を確認します。 強化されたセキュリティ機能を有効にした場合は、コンプライアンスの評価に使用される標準をカスタマイズしたり、他の規制 (NIST や Azure CIS など) や組織固有のセキュリティ要件を追加したりできます。

1. 任意の特定されたセキュリティの誤った構成と弱点に基づいて、**強化の推奨事項を提供** します。 これらのセキュリティに関する推奨事項を使用して、組織の Azure、ハイブリッド、マルチクラウドのリソースのセキュリティ体制を強化します。

[セキュリティ スコアの詳細](secure-score-security-controls.md)。

### <a name="cloud-workload-protection-cwp"></a>クラウド ワークロード保護 (CWP)

Defender for Cloud には、[Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) を利用したセキュリティ アラートが用意されています。 また、ワークロードに対する高度でインテリジェントな、さまざまな保護も含まれています。 ワークロード保護は、サブスクリプション内のリソースの種類に固有の Microsoft Defender プランを通じて提供されます。 たとえば、**Microsoft Defender for Storage** を有効にして、ご使用の Azure Storage アカウントに関連する不審なアクティビティに関するアラートを受け取ることができます。

## <a name="azure-hybrid-and-multi-cloud-protections"></a>Azure、ハイブリッド、マルチクラウドの保護

Defender for Cloud は Azure ネイティブ サービスなので、多くの Azure サービスは、デプロイを必要とせずに監視および保護されます。

必要に応じて、Defender for Cloud で Log Analytics エージェントを自動的にデプロイして、セキュリティ関連のデータを収集できます。 Azure マシンの場合、デプロイは直接処理されます。 ハイブリッドおよびマルチクラウドの環境では、Microsoft Defender プランは [Azure Arc](https://azure.microsoft.com/services/azure-arc/) を使用して Azure 以外のマシンに拡張されます。CSPM の機能は、エージェントを必要とせずにマルチクラウド マシンに拡張されます (「[他のクラウドで実行されているリソースの保護](#defend-resources-running-on-other-clouds)」を参照してください)。

### <a name="azure-native-protections"></a>Azure ネイティブ保護

Defender for Cloud は、次の脅威を検出するのに役立ちます。

- **Azure PaaS サービス** - Azure App Service、Azure SQL、Azure Storage アカウント、その他のデータ サービスを含む Azure サービスを対象とした脅威を検出できます。 Microsoft Defender for Cloud Apps (旧称: Microsoft Cloud App Security) とのネイティブ統合を使用して、Azure アクティビティ ログで異常検出を実行することもできます。

- **Azure データ サービス** - Defender for Cloud には、Azure SQL でデータを自動的に分類するのに役立つ機能が含まれています。 また、Azure SQL と Storage サービス全体の潜在的な脆弱性の評価と、それらを緩和する方法の推奨事項を取得することもできます。

- **Networks** - Defender for Cloud は、ブルートフォース攻撃への露出を制限するために役立ちます。 Just-In-Time VM アクセスを使用して仮想マシン ポートへのアクセスを減らすことにより、不要なアクセスを防止してネットワークを強化することができます。 許可されたユーザー、許可されたソース IP アドレスの範囲または IP アドレス、および制限された期間にのみ、選択したポートに対するセキュリティで保護されたアクセス ポリシーを設定することができます。

### <a name="defend-your-hybrid-resources"></a>ハイブリッド リソースを防御する

Azure 環境を保護するだけでなく、Defender for Cloud の機能をハイブリッド クラウド環境に追加して、Azure 以外のサーバーを保護することもできます。 最も重要な問題に専念できるように、特定の環境に応じてカスタマイズされた脅威インテリジェンスと優先順位が付けられたアラートを取得します。

オンプレミスのマシンに保護を拡張するには、[Azure Arc](https://azure.microsoft.com/services/azure-arc/) をデプロイし、Defender for Cloud の強化されたセキュリティ機能を有効にしてください。 詳細については、「[Azure Arc を使用して Azure 以外のマシンを追加する](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc)」をご覧ください。

### <a name="defend-resources-running-on-other-clouds"></a>他のクラウドで実行されているリソースを保護する

Defender for Cloud を使用すると、他のクラウド (AWS や GCP など) 内のリソースを保護できます。

たとえば、Azure サブスクリプションに[アマゾン ウェブ サービス (AWS) アカウントを接続して](quickstart-onboard-aws.md)いる場合は、次のいずれかの保護を有効にすることができます。

- **Defender for Cloud の CSPM 機能** が、AWS リソースまで拡張されています。 セキュリティ スコアに含まれているこのエージェントレス プランでは、AWS 固有のセキュリティの推奨事項に従って AWS リソースを評価します。 これらのリソースは、AWS (AWS CIS、AWS PCI DSS、および AWS の基本的なセキュリティのベスト プラクティス) に固有の組み込み標準に準拠しているかについても評価されます。 Defender for Cloud の[資産インベントリ ページ](asset-inventory.md)は、Azure リソースと AWS リソースを共に管理するのに役立つマルチクラウド対応機能です。
- **Microsoft Defender For Kubernetes** のコンテナーの脅威検出と高度な防御の対象が **Amazon EKS Linux クラスター** まで拡張されました。
- **Microsoft Defender for servers** を使用することで、お使いの Windows と Linux の EC2 インスタンスに脅威検出および高度な防御を利用できるようになります。 このプランには、Microsoft Defender for Endpoint の統合ライセンス、セキュリティ ベースラインと OS レベルの評価、脆弱性評価スキャン、適応型アプリケーション制御 (AAC)、ファイルの整合性の監視 (FIM) などが含まれます。

詳細については、[AWS](quickstart-onboard-aws.md) と [GCP](quickstart-onboard-gcp.md) のアカウントを Microsoft Defender for Cloud に接続するに関する記事を参照してください。

## <a name="vulnerability-assessment-and-management"></a>脆弱性の評価と管理

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-expanded-assess.png" alt-text="Microsoft Defender for Cloud の評価機能にフォーカスします。":::

Defender for Cloud には、強化されたセキュリティ機能の一部として、仮想マシン、コンテナー レジストリ、SQL サーバーの脆弱性評価ソリューションが含まれています。 一部のスキャナーは Qualys を搭載しています。 しかし、Qualys ライセンスも Qualys アカウントも必要ありません。すべてが Defender for Cloud 内でシームレスに処理されます。

Microsoft Defender for servers には、Microsoft Defender for Endpoint との、自動的でネイティブな統合機能が備わっています。 詳細については、「[Defender for Cloud の統合 EDR ソリューションを使用してエンドポイントを保護する: Microsoft Defender for Endpoint](integration-defender-for-endpoint.md)」を参照してください。 この統合を有効にした場合は、**Microsoft の脅威と脆弱性の管理** から脆弱性の検出結果にアクセスできるようになります。 詳細については、「[Microsoft Defender for Endpoint の脅威と脆弱性の管理を使用して弱点を調査する](deploy-vulnerability-assessment-tvm.md)」を参照してください。

これらの脆弱性スキャナーの結果を確認し、すべての結果に対して Defender for Cloud 内から対応します。 この広範なアプローチにより、Defender for Cloud は、すべてのクラウド セキュリティの取り組みの 1 つのウィンドウに近づいています。

詳細については、次のページを参照してください。

- [Azure およびハイブリッドのマシンに対する Defender for Cloud の統合された Qualys スキャナー](deploy-vulnerability-assessment-vm.md)
- [Azure コンテナー レジストリでイメージの脆弱性を識別する](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)

## <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>推奨されるコントロールを構成してセキュリティを最適化し、強化する

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-expanded-secure.png" alt-text="Microsoft Defender for Cloud の 'セキュリティ保護' 機能にフォーカスします。":::

これは、ワークロードがセキュリティで保護されていることを把握し、確実にするための基本的なセキュリティです。まず、セキュリティ ポリシーを適切に調整して配置することから始めます。 Defender for Cloud のポリシーは Azure Policy 制御を基礎にして構築されているため、**世界レベルのポリシー ソリューション** のすべての範囲と柔軟性を利用できます。 Defender for Cloud では、管理グループ、サブスクリプション全体、さらにはテナント全体に対して実行するようにポリシーを設定できます。

Defender for Cloud は、ワークロード全体にデプロイされている新しいリソースを継続的に検出し、セキュリティのベスト プラクティスに従って構成されているかどうかを評価します。 フラグが設定されていない場合には、フラグが設定され、修正する必要がある推奨事項の優先順位付きの一覧が表示されます。 推奨事項を利用すると、各リソースの攻撃対象領域を減らすことができます。

推奨事項の一覧は、Azure セキュリティ ベンチマークにより有効になり、サポートされます。 この Microsoft が作成した、Azure 固有のベンチマークは、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関するガイドラインのセットを提供します。 詳細については、「[Azure セキュリティ ベンチマークの概要](/security/benchmark/azure/introduction)」を参照してください。

このように、Defender for Cloud では、セキュリティ ポリシーを設定するだけでなく、*セキュリティで保護された構成基準をリソース全体に適用することができます*。

:::image type="content" source="./media/defender-for-cloud-introduction/recommendation-example.png" alt-text="Defender for Cloud の推奨事項の例。":::

Defender for Cloud では、全体的なセキュリティ体制に対する各推奨事項の重要度が理解しやすいよう、推奨事項がセキュリティ コントロールにグループ化され、各コントロールには **セキュア スコア** 値が追加されます。 これは、**セキュリティ作業の優先度付け** を可能にするうえで重要です。

:::image type="content" source="./media/defender-for-cloud-introduction/sc-secure-score.png" alt-text="Defender for Cloud のセキュリティ スコア。":::

## <a name="defend-against-threats"></a>脅威から防御する

:::image type="content" source="media/defender-for-cloud-introduction/defender-for-cloud-expanded-defend.png" alt-text="Microsoft Defender for Cloud の '防御' 機能にフォーカスします。":::

Defender for Cloud では以下が提供されます。

- **[セキュリティのアラート]** - Defender for Cloud によって環境のいずれかの領域で脅威が検出されると、セキュリティ アラートが生成されます。 これらのアラートでは、影響を受けるリソースや推奨される修復手順の詳細と、場合によっては、ロジック アプリを応答でトリガーするオプションが示されます。 アラートが Defender for Cloud によって生成されたか、統合セキュリティ製品から Defender for Cloud によって受信されたかにかかわらず、アラートをエクスポートできます。 アラートを Microsoft Sentinel、サードパーティの SIEM、またはその他の外部ツールにエクスポートする場合は、「[SIEM、SOAR、または IT サービス管理ソリューションにアラートをストリーミングする](export-to-siem.md)」の手順に従ってください。 Defender for Cloud の脅威の防止機能には、サイバー キルチェーン分析に基づいて環境内のアラートを自動的に相関させるフュージョン キルチェーン分析が含まれており、攻撃キャンペーンの詳細 (開始点、リソースに対する影響の種類) を詳細に把握することができます。 [Defender for Cloud でサポートされているキル チェーンの意図は、MITRE ATT&CK マトリックスのバージョン 7 に基づいています](alerts-reference.md#intentions)。

- 仮想マシン、SQL データベース、コンテナー、web アプリケーション、ネットワークなどの **高度な脅威保護機能** - 保護には、[ジャスト インタイム アクセス](just-in-time-access-overview.md)と [適応型アプリケーション制御](adaptive-application-controls.md)を使用した、VM の管理ポートのセキュリティ保護が含まれます。これにより、コンピューター上で実行すべきアプリや実行する必要がないアプリを示す許可リストが作成されます。

Microsoft Defender for Cloud の **Defender プラン** ページでは、環境のコンピューティング、データ、サービス レイヤーの包括的な防御について、次の計画を提供しています。

- [Microsoft Defender for servers](defender-for-servers-introduction.md)
- [Microsoft Defender for App Service](defender-for-app-service-introduction.md)
- [Microsoft Defender for Storage](defender-for-storage-introduction.md)
- [Microsoft Defender for SQL](defender-for-sql-introduction.md)
- [Microsoft Defender for Kubernetes](defender-for-kubernetes-introduction.md)
- [コンテナー レジストリ用 Microsoft Defender](defender-for-container-registries-introduction.md)
- [Microsoft Defender for Key Vault](defender-for-key-vault-introduction.md)
- [Microsoft Defender for Resource Manager](defender-for-resource-manager-introduction.md)
- [Microsoft Defender for DNS](defender-for-dns-introduction.md)
- [オープンソース リレーショナル データベース用 Microsoft Defender](defender-for-databases-introduction.md)

[ワークロード保護ダッシュボード](workload-protections-dashboard.md)の [高度な保護] タイルを使用して、これらの保護をそれぞれ監視し、構成します。

> [!TIP]
> Microsoft Defender for IoT は、個別の製品です。 詳細については、「[Microsoft Defender for IoT の概要](../defender-for-iot/overview.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- Defender for Cloud を使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションをお持ちでない場合には、[試用版にサインイン](https://azure.microsoft.com/free/)してください。

- Azure portal の Defender for Cloud ページに初めてアクセスするとき、または REST API を使用してプログラムで有効にすると、現在のすべての Azure サブスクリプションで、Defender for Cloud の無料プランが有効になります。 高度なセキュリティ管理と脅威検出の機能を利用するには、強化されたセキュリティ機能 を有効にする必要があります。 これらの機能は、最初の 30 日間は無料です。 [価格の詳細について確認してください](https://azure.microsoft.com/pricing/details/security-center/)。

- 強化されたセキュリティ機能を今すぐ有効にする準備ができている場合は、「[クイック スタート: 強化されたセキュリティ機能を有効にする](enable-enhanced-security.md)」の手順に従ってください。

> [!div class="nextstepaction"]
> [Microsoft Defender プランを有効にする](enable-enhanced-security.md)
