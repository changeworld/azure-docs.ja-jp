---
title: Microsoft Sentinel の新機能
description: この記事では、過去数か月間に追加された Microsoft Sentinel の新機能について説明します。
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ffeb90d11b2bc65cc8dceb996cd3d6eecee9e03e
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519897"
---
# <a name="whats-new-in-microsoft-sentinel"></a>Microsoft Sentinel の新機能

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、Microsoft Sentinel 用に最近追加された機能と、Microsoft Sentinel のユーザー エクスペリエンスを向上させる関連サービスの新機能について説明します。

6 か月以上前の項目をお探しの場合は、[Sentinel の最新情報のアーカイブ](whats-new-archive.md)に関するページを参照してください。 以前に提供された機能については、[Tech Community のブログ](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New)を参照してください。

> [!IMPORTANT]
> 記載されている機能は、現在プレビュー段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

> [!TIP]
> Microsoft の脅威ハンティング チームは、クエリ、プレイブック、ブック、およびノートブックを [Microsoft Sentinel コミュニティ](https://github.com/Azure/Azure-Sentinel)に投稿します。これには、ユーザーのチームが適応して使用できる特定の[ハンティング クエリ](https://github.com/Azure/Azure-Sentinel)などが含まれます。
>
> ユーザーも投稿することができます。 [Microsoft Sentinel Threat Hunters GitHub コミュニティ](https://github.com/Azure/Azure-Sentinel/wiki)にぜひご参加ください。

## <a name="november-2021"></a>2021 年 11 月

### <a name="windows-forwarded-events-connector-now-available-public-preview"></a>Windows 転送イベント コネクタが利用可能に (パブリック プレビュー)

この新しいデータ コネクタにより、Windows イベント コレクションまたは Windows イベント転送 (WEC/WEF) を使用して、Azure Sentinel ワークスペースに接続されている Windows Server からイベント ログをストリーミングできます。 コネクタは、新しい Azure Monitor エージェント (AMA) を使用します。これは、レガシ Log Analytics エージェント (MMA とも呼ばれる) に対して多くの利点を提供します。

- **スケーラビリティ:** Windows イベント コレクション (WEC) を有効にしている場合は、WEC コンピューターに Azure Monitor エージェント (AMA) をインストールして、多くのサーバーから 1 つの接続ポイントを使用してログを収集できます。

- **速度:** AMA は、5K EPS の向上した速度でデータを送信でき、データの更新を高速化できます。

- **効率性:** AMA を使用すると、複雑なデータ収集ルール (DCR) を設計して、ソースのログをフィルター処理し、ワークスペースにストリーム配信する正確なイベントを選択できます。 DCR は、不要なイベントを除外して、ネットワーク トラフィックとインジェスト コストを削減するのに役立ちます。

- **カバレッジ:** WEC/WEF を使用すると、レガシ (オンプレミスおよび物理) サーバーから、また、エージェントのインストールが望ましくないドメイン コントローラーなど、使用率の高い、または機密性の高いコンピューターからも Windows イベント ログを収集できます。

データ正規化の完全なサポートを保証するために、このコネクタに [Azure Sentinel Information Model (ASIM)](normalization.md) パーサーをインストールして使用することをお勧めします。

詳細については、[Windows 転送イベント コネクタ](data-connectors-reference.md#windows-forwarded-events-preview)に関するページを参照してください。

### <a name="near-real-time-nrt-threat-detection-rules-now-available-public-preview"></a>ほぼリアルタイム (NRT) の脅威検出ルールが利用可能になりました (パブリック プレビュー)

セキュリティ上の脅威に直面した場合、時間と速度が重要です。 脅威を含め、迅速に分析して対応できるよう、脅威が実現する場合は、脅威に注意する必要があります。 Microsoft Sentinel のほぼリアルタイム (NRT) 分析ルールを使用すると、オンプレミス SIEM に匹敵するスピードで脅威を検出し、特定のシナリオで応答時間を短縮できます。

Microsoft Sentinel の[ほぼリアルタイム分析ルール](detect-threats-built-in.md#nrt)では、面倒な設定なし、かつ最新の方法で脅威を検出できます。 この型のルールは、わずか1分サイクル間隔でクエリを実行することにより、応答性が高くなるように設計されています。

[NRT ルール](near-real-time-rules.md)と[それらを使用する方法](create-nrt-rules.md)の詳細を確認します。

### <a name="fusion-engine-now-detects-emerging-and-unknown-threats-public-preview"></a>Fusion エンジンで新たな未知の脅威が検出されるようになりました (パブリック プレビュー)

Microsoft Sentinel の ML を利用した Fusion エンジンを使用すると、[定義済みのシナリオ](fusion-scenario-reference.md)に基づいて攻撃を検出できるだけでなく、拡張 ML 分析を適用してより広範な異常のシグナルを関連付けることにより、アラートによる負担を低く保ちながら、環境内で新たな未知の脅威を見つけるために役立ちます。

Fusion エンジンの ML アルゴリズムでは、既存の攻撃から継続的に学習し、セキュリティ アナリストの考えに基づいて分析が適用されます。 これにより、環境全体のキルチェーンで数百万の異常な動作から以前は検出されなかった脅威を検出できるため、攻撃者の一歩先を行くことができます。

[新たな脅威に対する Fusion](fusion.md#fusion-for-emerging-threats) の詳細を確認してください。

また、[Fusion の分析ルールがさらに構成可能になり](configure-fusion-rules.md)、機能の向上が反映されています。

### <a name="get-fine-tuning-recommendations-for-your-analytics-rules-public-preview"></a>分析ルールの微調整に関する推奨事項を取得する (パブリック プレビュー)

SIEM での脅威検出ルールの微調整は、脅威検出の対象範囲の最大化と擬陽性の割合の最小化の間のバランスをとるという、困難で、繊細、かつ継続的なプロセスである場合があります。 Microsoft Sentinel によってこのプロセスが簡素化および合理化されます。そのために、機械学習を使用してデータ ソースからの数十億のシグナル、インシデントへの対応を経時的に分析して、パターンを推論し、チューニングのオーバーヘッドを大幅に低下させて、実際の脅威の検出と対応に集中できるアクション可能な推奨事項と分析情報を提供します。

[チューニングに関する推奨事項と分析情報](detection-tuning.md)が分析ルールに組み込まれるようになりました。

### <a name="free-trial-updates"></a>無料試用版の更新プログラム

無料試用版の Microsoft Sentinel では、最初の 31 日間、新規または既存の Log Analytics ワークスペースが追加のコストなしで引き続きサポートされます。
現在の無料試用版のエクスペリエンスは、次の更新プログラムが含まれるように展開していきます。

- **新しい Log Analytics ワークスペース** では、最初の 31 日間、最大で 1 日 10 GB のログ データを無料で取り込むことができます。 新しいワークスペースには、3 日を経過していないワークスペースが含まれます。

   31 日間の試用期間中、Log Analytics のデータ インジェストと Microsoft Sentinel の料金が免除されます。 この無料試用版には、Azure テナントごとに 20 個のワークスペースという制限が適用されます。

- **既存の Log Analytics ワークスペース** では、追加のコストなしで Azure Sentinel を有効にすることができます。 既存のワークスペースには、作成後の経過時間が 3 日を超えているワークスペースが含まれます。

   31 日間の試用期間中、Microsoft Sentinel の料金のみが免除されます。

これらの制限を超える使用量については、[Microsoft Sentinel の価格](https://azure.microsoft.com/pricing/details/azure-sentinel)に関するページに記載されている価格に従って課金されます。 [自動化](automation-in-azure-sentinel.md)と[独自の機械学習](bring-your-own-ml.md)用の追加機能に関連する料金は、無料試用中であっても適用されます。

> [!TIP]
> 無料試用中は、Microsoft Sentinel の **[ニュースとガイド] > [無料試用版]** タブで、コスト管理やトレーニングなどのリソースを利用できます。 このタブには、無料試用版の日付と、有効期限が切れるまでの残り日数に関する詳細も表示されます。
>

詳細については、「[Microsoft Sentinel のコストを計画および管理する](azure-sentinel-billing.md)」を参照してください。

### <a name="content-hub-and-new-solutions-public-preview"></a>コンテンツ ハブと新しいソリューション (パブリック プレビュー)

Microsoft Sentinel では、**コンテンツ ハブ** が提供されるようになりました。これは、Microsoft Sentinel のすぐに使用できる (組み込み) コンテンツとソリューションを見つけて Microsoft Sentinel ワークスペースにデプロイするための一元的な場所です。 必要なコンテンツを見つけるには、コンテンツの種類、サポート モデル、カテゴリなどでフィルター処理するか、強力なテキスト検索を使用します。

**[コンテンツ管理]** で **[コンテンツ ハブ]** を選択します。 ソリューションを選択して右側に詳細を表示し、 **[インストール]** を クリックしてワークスペースにインストールします。

:::image type="content" source="media/whats-new/solutions-list.png" alt-text="新しい Microsoft Sentinel コンテンツ ハブのスクリーンショット。" lightbox="media/whats-new/solutions-list.png":::

次の一覧に、コンテンツ ハブに追加されたすぐに使用できる新しいソリューションのハイライトを示します。

:::row:::
   :::column span="":::
      - Microsoft Sentinel トレーニング ラボ
      - Cisco ASA
      - Cisco Duo Security
      - Cisco Meraki
      - Cisco StealthWatch
      - Digital Guardian
      - Dynamics 365
      - GCP Cloud DNS
   :::column-end:::
   :::column span="":::
      - GCP CloudMonitor
      - GCP Identity and Access Management
      - FalconForce
      - FireEye NX
      - Flare Systems Firework
      - Forescout
      - Fortinet Fortigate
      - Imperva Cloud FAW
   :::column-end:::
   :::column span="":::
      - Insiders Risk Management
      - IronNet CyberSecurity Iron Defense
      - Lookout
      - McAfee Network Security Platform
      - Microsoft MITRE ATT&CK Solution for Cloud
      - Palo Alto PAN-OS
   :::column-end:::
   :::column span="":::
      - Rapid7 Nexpose / Insight VM
      - ReversingLabs
      - RSA SecurID
      - Semperis
      - Tenable Nessus Scanner
      - Vectra Stream
      - ゼロ トラスト
   :::column-end:::
:::row-end:::

詳細については、次を参照してください。

- [Microsoft Azure Sentinel ソリューションについて](sentinel-solutions.md)
- [Microsoft Azure Sentinel ソリューションを検出してデプロイする](sentinel-solutions-deploy.md)
- [Microsoft Azure Sentinel ソリューション カタログ](sentinel-solutions-catalog.md)

### <a name="enable-continuous-deployment-from-your-content-repositories-public-preview"></a>コンテンツ リポジトリからの継続的配置を有効にする (パブリック プレビュー)

Microsoft Sentinel の新しい **[リポジトリ]** ページでは、Azure portal での管理の代替として、GitHub または Azure DevOps リポジトリからカスタム コンテンツを管理およびデプロイする機能が提供されます。 この機能により、Microsoft Sentinel ワークスペース全体のコンテンツを管理およびデプロイするための、より合理化および自動化された方法が導入されます。

Microsoft Sentinel の外部で管理するためにカスタム コンテンツを外部リポジトリに格納する場合は、そのリポジトリを Microsoft Sentinel ワークスペースに接続できるようになりました。 リポジトリで追加、作成、または編集したコンテンツは、Microsoft Sentinel ワークスペースに自動的にデプロイされ、**分析**、**ハンティング**、**ブック** などのさまざまな Microsoft Sentinel ギャラリーから表示できます。

詳細については、[リポジトリからのカスタム コンテンツのデプロイ](ci-cd.md)に関する記事を参照してください。

### <a name="enriched-threat-intelligence-with-geolocation-and-whois-data-public-preview"></a>位置情報と Whois データを使用した強化された脅威インテリジェンス (パブリック プレビュー)

データ コネクタとロジック アプリ プレイブックを介して Microsoft Sentinel に取り込まれたか、Microsoft Sentinel で作成された脅威インテリジェンス データは、位置情報と WhoIs の情報で自動的に強化されるようになりました。

位置情報と Whois のデータを使用すると、選択した侵害のインジケーター (IOC) が検出された調査のコンテキストがさらに提供されます。

たとえば、位置情報データを使用してインジケーターの "*組織*" や "*国*" などの詳細を検索し、Whois データを使用して "*レジストラー*" や "*レコード作成*" などのデータを検索します。

位置情報と WhoIs のデータは、 **[脅威インテリジェンス]** ペインで、Microsoft Sentinel にインポートした侵害インジケーターごとに表示できます。 インジケーターの詳細は右側に表示されます (利用可能な位置情報と Whois のデータを含む)。

たとえば、次のように入力します。

:::image type="content" source="media/whats-new/geolocation-whois-ti.png" alt-text="位置情報と Whois のデータを含むインジケーターの詳細のスクリーンショット。" lightbox="media/whats-new/geolocation-whois-ti.png":::

> [!TIP]
> 位置情報と Whois の情報は、API 経由でもアクセス可能な Microsoft 脅威インテリジェンス サービスから取得されます。 詳細については、「[API を介した地理位置情報データを使用したエンティティの強化](geolocation-data-api.md)」を参照してください。
>

詳細については、次を参照してください。

- [Microsoft Sentinel の脅威インテリジェンスについて](understand-threat-intelligence.md)
- [脅威インテリジェンスの統合](threat-intelligence-integration.md)
- [Microsoft Sentinel で脅威インジケーターを操作する](work-with-threat-indicators.md)
- [脅威インテリジェンス プラットフォームの接続](connect-threat-intelligence-tip.md)

### <a name="use-notebooks-with-azure-synapse-analytics-in-microsoft-sentinel-public-preview"></a>Microsoft Sentinel でノートブックを Azure Synapse Analytics と共に使用する (パブリック プレビュー)

Microsoft Sentinel では、大規模なセキュリティ分析シナリオのために、Jupyter Notebook と Azure Synapse が統合されるようになりました。

これまで、Microsoft Sentinel 内の Jupyter Notebook は Azure Machine Learning と統合されていました。 この機能では、ノートブック、一般的なオープンソースの機械学習ツールキット、TensorFlow などのライブラリ、独自のカスタム モデルをセキュリティ ワークフローに組み込むユーザーがサポートされます。

新しい Azure Synapse 統合では、次のような追加の分析能力が提供されます。

- **セキュリティ ビッグ データの分析**。コストが最適化されたフル マネージドの Azure Synapse Apache Spark コンピューティング プールを使用します。

- **コスト効率の高いデータ レイク アクセス**。Azure Data Lake Storage Gen2 を介して履歴データに対する分析を構築します。これは、Azure Blob Storage の上に構築されたビッグ データ分析専用の一連の機能です。

- **データ ソースを統合する柔軟性**。複数のソースと形式からセキュリティ操作ワークフローに統合します。

- Python と組み合わせて Spark フレームワークを使用するための **Python ベースの API である PySpark**。Python に既に慣れていれば、新しいプログラミング言語を学習する必要性が少なくなります。

この統合をサポートするために、Microsoft Sentinel から直接 Azure Synapse ワークスペースを作成および起動する機能が追加されました。 また、Azure Synapse 環境の構成、Log Analytics から Azure Data Lake Storage への継続的なデータ エクスポート パイプラインの設定、そのデータの大規模なハンティングをガイドする新しいサンプル ノートブックも追加されました。

詳細については、[ノートブックと Azure Synapse の統合](notebooks-with-synapse.md)に関する記事を参照してください。

### <a name="enhanced-notebooks-area-in-microsoft-sentinel"></a>Microsoft Sentinel の機能拡張されたノートブック領域

Microsoft Sentinel の **[Notebooks]** 領域にも、 **[概要]** タブが表示されるようになりました。このタブには、ノートブックに関する基本情報と、表示される各ノートブックの種類を示す **[テンプレート]** タブの新しい **[Notebook の種類]** 列が表示されます。 たとえば、ノートブックの種類としては、**作業の開始**、**構成**、**ハンティング**、**Synapse** があります。

たとえば、次のように入力します。

:::image type="content" source="media/whats-new/notebooks-synapse.png" alt-text="[ノートブック] ページの新しい Azure Synapse 機能のスクリーンショット。" lightbox="media/whats-new/notebooks-synapse.png":::

詳細については、「[Jupyter Notebook を使用してセキュリティの脅威を検出する](notebooks.md)」を参照してください。

## <a name="microsoft-sentinel-renaming"></a>Microsoft Sentinel の名前変更

2021 年 11 月以降、Azure Sentinel の名前は Microsoft Sentinel に変更され、ポータル、ドキュメント、およびその他のリソースの今後の更新が同時に表示されます。

この記事の前のエントリと、[Sentinel の新着情報のアーカイブ](whats-new-archive.md)に関する古いページでは、*Azure* Sentinel という名前が引き続き使用されます。これは、これらの機能が新しかったときのサービス名であるためです。

詳細については、[最新のセキュリティ強化に関するブログ](https://aka.ms/secblg11)を参照してください。

### <a name="deploy-and-monitor-azure-key-vault-honeytokens-with-azure-sentinel"></a>Azure Sentinel を使用した Azure Key Vault ハニートークンのデプロイと監視

新しい **Azure Sentinel 詐欺** ソリューションを使用すると、選択した Azure キー コンテナーに "*ハニー*" トークンと呼ばれる分離キーとシークレットをデプロイし、キー コンテナー内の悪意のあるアクティビティを監視できます。

デプロイが完了すると、ハニートークン キーとシークレットを使用したアクセスまたは操作によってインシデントが生成され、Azure Sentinel でそのインシデントを調査できます。

実際にハニートークン キーとシークレットを使用する理由は何もないため、ワークスペース内の同様のアクティビティは悪意のあるものになる可能性があり、調査する必要があります。

**Azure Sentinel 詐欺** ソリューションには、大規模または一度に 1 つ、ハニートークンをデプロイするのに役立つブック、作成されたハニートークンを追跡するための監視リスト、必要に応じてインシデントを生成するための分析ルールが含まれています。

詳細については、「[Azure Sentinel を使用した Azure Key Vault ハニートークンのデプロイと監視 (パブリック プレビュー)](monitor-key-vault-honeytokens.md)」を参照してください。

## <a name="october-2021"></a>2021 年 10 月

- [Azure Monitor エージェントを使用する Windows セキュリティ イベント コネクタが一般提供になりました](#windows-security-events-connector-using-azure-monitor-agent-now-in-ga)
- [Microsoft 365 Defender コネクタで使用できるようになった Defender for Office 365 イベント (パブリック プレビュー)](#defender-for-office-365-events-now-available-in-the-microsoft-365-defender-connector-public-preview)
- [プレイブックのテンプレートとギャラリーを使用できるようになりました (パブリック プレビュー)](#playbook-templates-and-gallery-now-available-public-preview)
- [スケジュールされた分析ルールのテンプレート バージョンを管理する (パブリック プレビュー)](#manage-template-versions-for-your-scheduled-analytics-rules-public-preview)
- [DHCP 正規化スキーマ (パブリック プレビュー)](#dhcp-normalization-schema-public-preview)

### <a name="windows-security-events-connector-using-azure-monitor-agent-now-in-ga"></a>Azure Monitor エージェントを使用する Windows セキュリティ イベント コネクタが一般提供になりました

Azure Monitor エージェントに基づく新しいバージョンの Windows セキュリティ イベント コネクタが、一般提供されるようになりました。 詳細については、「[セキュリティ イベントを収集するために Windows サーバーに接続する](connect-windows-security-events.md?tabs=AMA)」を参照して下さい。

### <a name="defender-for-office-365-events-now-available-in-the-microsoft-365-defender-connector-public-preview"></a>Microsoft 365 Defender コネクタで使用できるようになった Defender for Office 365 イベント (パブリック プレビュー)

Microsoft Defender for Endpoint からのものに加えて、[Microsoft 365 Defender コネクタ](connect-microsoft-365-defender.md)を通じて [Microsoft Defender for Office 365](/microsoft-365/security/office-365-security/overview) から生の[高度なハンティング イベント](/microsoft-365/security/defender/advanced-hunting-overview)を取り込むことができるようになりました。 [詳細については、こちらを参照してください](microsoft-365-defender-sentinel-integration.md#advanced-hunting-event-collection)。

### <a name="playbook-templates-and-gallery-now-available-public-preview"></a>プレイブックのテンプレートとギャラリーを使用できるようになりました (パブリック プレビュー)

プレイブック テンプレートは、事前に構築されてテストされた、すぐに使用できるワークフローであり、ニーズに合わせてカスタマイズできます。 テンプレートは、プレイブックをゼロから開発するときのベスト プラクティスのリファレンスとして、または新しい自動化シナリオのためのインスピレーションを得るためにも、役に立つ場合があります。

プレイブック テンプレートは、Sentinel コミュニティ、独立系ソフトウェア ベンダー (ISV)、Microsoft 社内の専門家によって開発されたものであり、 **[Playbook templates]\(プレイブック テンプレート\)** タブ ( **[オートメーション]** の下)、[Azure Sentinel ソリューション](sentinel-solutions.md)の一部、または [Azure Sentinel GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)で見つけることができます。

詳細については、「[組み込みテンプレートからプレイブックを作成してカスタマイズする](use-playbook-templates.md)」を参照してください。

### <a name="manage-template-versions-for-your-scheduled-analytics-rules-public-preview"></a>スケジュールされた分析ルールのテンプレート バージョンを管理する (パブリック プレビュー)

[組み込みの Azure Sentinel ルール テンプレート](detect-threats-built-in.md)から分析ルールを作成すると、事実上、テンプレートのコピーが作成されます。 この時点を過ぎると、アクティブなルールは、元のテンプレートに加えられた変更に合わせて動的に更新 "***されなくなります***"。

ただし、テンプレートから作成したルールには、どのテンプレートから作成されたかの情報が記録されて "***いる***" ため、以下の 2 つの利点があります。

- テンプレートからルールを作成する際 (またはその後) に変更を加えた場合、いつでもそのルールを (テンプレートのコピーとして) 元のバージョンに戻すことができます。

- テンプレートが更新されたときには通知を受けることができ、ルールを新しいバージョンのテンプレートに更新するか、そのままにしておくかを選択することができます。

[これらのタスクの管理方法](manage-analytics-rule-templates.md)と注意点を確認してください。 これらの手順は、テンプレートから作成されたすべての[スケジュールされた](detect-threats-built-in.md#scheduled)分析ルールに適用されます。

### <a name="dhcp-normalization-schema-public-preview"></a>DHCP 正規化スキーマ (パブリック プレビュー)

Advanced SIEM Information Model (ASIM) は、DHCP 正規化スキーマをサポートするようになりました。これは DHCP サーバーから報告されたイベントを記述するために使用され、Azure Sentinel によってソースに依存しない分析を可能にするために使用されます。 

DHCP 正規化スキーマに記述されるイベントには、クライアント システムからリースされた DHCP IP アドレスの要求の処理、リースを許可した DNS サーバーの更新などがあります。

詳細については、次を参照してください。

- [Azure Sentinel の DHCP 正規化スキーマ リファレンス (パブリック プレビュー)](dhcp-normalization-schema.md)
- [正規化と Azure Sentinel 情報モデル (ASIM)](normalization.md)

## <a name="september-2021"></a>2021 年 9 月

- [ドキュメントの新機能: データ コネクタのスケーリングに関するドキュメント](#new-in-docs-scaling-data-connector-documentation)
- [Azure ストレージ アカウントのコネクタの変更](#azure-storage-account-connector-changes)

### <a name="new-in-docs-scaling-data-connector-documentation"></a>ドキュメントの新機能: データ コネクタのスケーリングに関するドキュメント

Azure Sentinel 用の組み込みデータ コネクタを継続的に追加しているため、この拡大を反映するようデータ コネクタのドキュメントを再編成しました。

ほとんどのデータ コネクタでは、個々のコネクタについて説明する完全な記事を、一連の一般的な手順と、現在サポートされているすべてのコネクタの完全なリファレンスに置き換えました。

関連する一般的な手順や、必要な追加情報や構成のリファレンスなどのコネクタの詳細については、[Azure Sentinel データ コネクタのリファレンス](data-connectors-reference.md)に関するページを参照してください。

詳細については、次を参照してください。

- **概念について**: [データ ソースの接続](connect-data-sources.md)

- **一般的な方法に関する記事**:

   - [Azure、Windows、Microsoft、Amazon サービスへの接続](connect-azure-windows-microsoft-services.md)
   - [データ ソースを Azure Sentinel のデータ コレクター API に接続してデータを取り込む](connect-rest-api-template.md)
   - [デバイスまたはアプライアンスの CEF 形式のログを Azure Sentinel に取得する](connect-common-event-format.md)
   - [Syslog を使用して Linux ベースのソースからデータを収集する](connect-syslog.md)
   - [Log Analytics エージェントを使用してカスタム ログ形式のデータを Azure Sentinel に収集する](connect-custom-logs.md)
   - [Azure Functions を使用してデータ ソースを Azure Sentinel に接続する](connect-azure-functions-template.md)
   - [Azure Sentinel カスタム コネクタを作成するためのリソース](create-custom-connector.md)

### <a name="azure-storage-account-connector-changes"></a>Azure ストレージ アカウントのコネクタの変更

Azure Storage アカウントのリソース構成自体に変更があったため、コネクタの再構成も必要になりました。
ストレージ アカウント (親) リソースには、ファイル、テーブル、キュー、BLOB など、ストレージの種類ごとに他の (子) リソースがあります。

ストレージ アカウントの診断を構成する場合は、次の項目を順番に選択して構成する必要があります。
- **Transaction** メトリックをエクスポートする親アカウント リソース。
- すべてのログとメトリックをエクスポートする、子ストレージの種類の各リソース (上の表を参照)。

実際にリソースを定義したストレージの種類だけが表示されます。

:::image type="content" source="media/whats-new/storage-diagnostics.png" alt-text="Azure Storage の診断構成のスクリーンショット。":::

## <a name="august-2021"></a>2021 年 8 月

- [高度なインシデント検索 (パブリック プレビュー)](#advanced-incident-search-public-preview)
- [ランサムウェアの Fusion 検出 (パブリック プレビュー)](#fusion-detection-for-ransomware-public-preview)
- [UEBA データ用のウォッチリスト テンプレート](#watchlist-templates-for-ueba-data-public-preview)
- [ファイル イベントの正規化スキーマ (パブリック プレビュー)](#file-event-normalization-schema-public-preview)
- [ドキュメントの新事項: ベスト プラクティス ガイダンス](#new-in-docs-best-practice-guidance)

### <a name="advanced-incident-search-public-preview"></a>高度なインシデント検索 (パブリック プレビュー)

既定では、インシデントの検索は、**インシデント ID**、**タイトル**、**タグ**、**所有者**、および **製品名** の値に対してのみ実行されます。 Azure Sentinel では、アラートの詳細、説明、エンティティ、戦術など、より多くのデータを検索するための[高度な検索オプション](investigate-cases.md#search-for-incidents)が提供されるようになりました。

次に例を示します。

:::image type="content" source="media/tutorial-investigate-cases/advanced-search.png" alt-text="[インシデント] ページの高度な検索オプションのスクリーンショット。":::

詳細については、「[インシデントの検索](investigate-cases.md#search-for-incidents)」を参照してください。

### <a name="fusion-detection-for-ransomware-public-preview"></a>ランサムウェアの Fusion 検出 (パブリック プレビュー)

Azure Sentinel では、ランサムウェア アクティビティについて新しい Fusion 検出が提供されて、**ランサムウェア アクティビティ検出に関連する可能性のある複数のアラート** というタイトルのインシデントが生成されるようになりました。

アラートがランサムウェア アクティビティに関連付けられている可能性があり、これらが特定の期間に発生し、攻撃の実行および防御回避の段階に関連付けられているときに、インシデントが生成されます。 インシデントに示されているアラートを使用して、攻撃者がホストまたはデバイスに侵入し、検出を回避するために使用する可能性がある手法を分析します。

サポートされているデータ コネクタとして、以下があります。

- [Azure Defender (Azure Security Center)](connect-azure-security-center.md)
- [Microsoft Defender for Endpoint](./data-connectors-reference.md#microsoft-defender-for-endpoint)
- [Microsoft Defender for Identity](./data-connectors-reference.md#microsoft-defender-for-identity)
- [Microsoft Cloud App Security](./data-connectors-reference.md#microsoft-defender-for-cloud-apps)
- [Azure Sentinel のスケジュールされた分析ルール](detect-threats-built-in.md#scheduled)

詳細については、「[ランサムウェア アクティビティ検出に関連する可能性のある複数のアラート](fusion.md#fusion-for-ransomware)」を参照してください。

### <a name="watchlist-templates-for-ueba-data-public-preview"></a>UEBA データ用のウォッチリスト テンプレート (パブリックプレビュー)

Azure Sentinel で提供されるようになった UEBA データ用の組み込みのウォッチリスト テンプレートは、環境に合わせてカスタマイズし、調査中に使用することができます。

UEBA ウォッチリストにデータが入力されると、そのデータを分析ルールに関連付けて、それをエンティティ ページや調査グラフに洞察として表示したり、カスタム使用を作成して VIP や機密性の高いユーザーを追跡したりすることができます。

ウォッチリスト テンプレートの現在の内容を以下に示します。

- **VIP ユーザー**。 組織で大きな影響値を持つ従業員のユーザー アカウントの一覧。
- **退職した従業員**。 退職した、または退職しようとしている従業員のユーザー アカウントの一覧。
- **サービス アカウント**。 サービス アカウントとその所有者の一覧。
- **ID 相関関係**。 同じ人物の所属下にある、関連するユーザー アカウントの一覧。
- **価値の高い資産**。 組織の中で重要な価値を持つデバイス、リソース、または他の資産の一覧。
- **ネットワーク マッピング**。 IP サブネットとそれぞれの組織コンテキストの一覧。

詳細については、[テンプレートを使用して新しいウォッチリストを作成する](watchlists.md#create-a-new-watchlist-using-a-template-public-preview)とに[組み込みウォッチリスト スキーマ](watchlist-schemas.md)に関するページをご覧ください。



### <a name="file-event-normalization-schema-public-preview"></a>ファイル イベントの正規化スキーマ (パブリック プレビュー)

Azure Sentinel Information Model (ASIM) でサポートされるようになったファイル イベント正規化スキーマは、ファイルやドキュメントの作成、変更、削除などのファイルのアクティビティを記述するために使用されます。 ファイル イベントは、オペレーティング システム、Azure Files などのファイル ストレージ システム、Microsoft SharePoint などのドキュメント管理システムによって報告されます。

詳細については、次を参照してください。

- [Azure Sentinel ファイル イベント正規化スキーマ リファレンス (パブリック プレビュー)](file-event-normalization-schema.md)
- [正規化と Azure Sentinel 情報モデル (ASIM)](normalization.md)


### <a name="new-in-docs-best-practice-guidance"></a>ドキュメントの新事項: ベスト プラクティス ガイダンス

お客様やサポート チームからの多数の要求に応じて、一連のベストプラクティス ガイダンスを当社のドキュメントに追加しました。

詳細については、次を参照してください。

- [Azure Sentinel をデプロイするための前提条件](prerequisites.md)
- [Azure Sentinel のベスト プラクティス](best-practices.md)
- [Azure Sentinel ワークスペース アーキテクチャのベスト プラクティス](best-practices-workspace-architecture.md)
- [Azure Sentinel ワークスペース アーキテクチャを設計する](design-your-workspace-architecture.md)
- [Azure Sentinel のサンプル ワークスペースの設計](sample-workspace-designs.md)
- [データ収集のベスト プラクティス](best-practices-data.md)

> [!TIP]
> ドキュメント全体で追加されたガイダンスについては、関連する概念と方法に関する記事を参照してください。 詳細については、[ベスト プラクティス リファレンス](best-practices.md#best-practice-references)に関するページを参照してください。
>

## <a name="july-2021"></a>2021 年 7 月

- [Microsoft 脅威インテリジェンス照合分析 (パブリック プレビュー)](#microsoft-threat-intelligence-matching-analytics-public-preview)
- [Azure Sentinel の情報テーブルで Azure AD データを使用する (パブリック プレビュー)](#use-azure-ad-data-with-azure-sentinels-identityinfo-table-public-preview)
- [API を介した地理位置情報データを使用したエンティティの強化 (パブリック プレビュー)](#enrich-entities-with-geolocation-data-via-api-public-preview)
- [ADX クロスリソース クエリのサポート (パブリック プレビュー)](#support-for-adx-cross-resource-queries-public-preview)
- [ウォッチリストは一般提供中です](#watchlists-are-in-general-availability)
- [データ レジデンシーをより多くの地域でサポート](#support-for-data-residency-in-more-geos)
- [Azure Defender コネクタでの双方向同期 (パブリック プレビュー)](#bidirectional-sync-in-azure-defender-connector-public-preview)

### <a name="microsoft-threat-intelligence-matching-analytics-public-preview"></a>Microsoft 脅威インテリジェンス照合分析 (パブリック プレビュー)

Azure Sentinel では、Microsoft が生成した脅威インテリジェンス データをログと照合する、組み込みの **Microsoft 脅威インテリジェンス照合分析** ルールが提供されるようになりました。 このルールで生成される忠実度の高いアラートとインシデントには、適切な重大度が、検出されたログのコンテキストに基づいて付けられています。 一致が検出されると、インジケーターは Azure Sentinel 脅威インテリジェンス リポジトリにも発行されます。

**Microsoft 脅威インテリジェンス照合分析** ルールは、現在、次のログ ソースに対するドメイン インジケーターと一致しています。

- [CEF](connect-common-event-format.md)
- [DNS](./data-connectors-reference.md#windows-dns-server-preview)
- [Syslog](connect-syslog.md)

詳細については、「[照合分析を使用した脅威の検出 (パブリックプレビュー)](work-with-threat-indicators.md#detect-threats-using-matching-analytics-public-preview)」を参照してください。

### <a name="use-azure-ad-data-with-azure-sentinels-identityinfo-table-public-preview"></a>Azure Sentinel の情報テーブルで Azure AD データを使用する (パブリック プレビュー)

攻撃者は多くの場合、組織独自のユーザーとサービス アカウントを使用するため、アナリストにとっては、ユーザー ID や特権など、それらのユーザー アカウントに関するデータが調査中に重要です。

これで、Azure Sentinel ワークスペースで [UEBA を有効に](enable-entity-behavior-analytics.md)すると、Azure AD データも Log Analytics の新しい **IdentityInfo** テーブルに同期されるようになりました。 Azure AD と **IdentifyInfo** テーブルの間の同期化によって、ユーザー メタデータ、グループ情報、各ユーザーに割り当てられた Azure AD ロールなど、ユーザー プロファイル データのスナップショットが作成されます。

調査中には、また、組織の分析ルールを微調整して偽陽性を減らすために、**IdentityInfo** テーブルを使用します。

詳細については、『UEBA エンリッチメント リファレンス』の「[IdentityInfo テーブル](ueba-enrichments.md#identityinfo-table-public-preview)」と「[UEBA データを使用して擬陽性を分析する](investigate-with-ueba.md#use-ueba-data-to-analyze-false-positives)」を参照してください。

### <a name="enrich-entities-with-geolocation-data-via-api-public-preview"></a>API を介した地理位置情報データを使用したエンティティの強化 (パブリック プレビュー)

Azure Sentinel では、地理位置情報を使用してデータを強化する API が提供されるようになりました。 その地理位置情報データを使用して、セキュリティ インシデントの分析と調査を行うことができます。

詳細については、「[REST API を介して地理位置情報データで Azure Sentinel のエンティティを強化する (パブリック プレビュー)](geolocation-data-api.md)」と「[Azure Sentinel でエンティティを使用してデータを分類および分析する](entities-in-azure-sentinel.md)」を参照してください。


### <a name="support-for-adx-cross-resource-queries-public-preview"></a>ADX クロスリソース クエリのサポート (パブリック プレビュー)

Azure Sentinel での検索エクスペリエンスでは、[ADX クロスリソース クエリ](../azure-monitor/logs/azure-monitor-data-explorer-proxy.md#cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer)がサポートされるようになりました。
 
Log Analytics は Azure Sentinel で分析を実行するためのプライマリ データ ストレージの場所ですが、コスト、保有期間、または他の要因のためにデータを格納する際に ADX が必要になる場合があります。 この機能により、お客様は、より広範なデータセットを探して、[Azure Sentinel 検索エクスペリエンス](hunting.md)(検索クエリ、[ライブストリーム](livestream.md)、Log Analytics 検索ページなど) で結果を確認できます。

ADX クラスターに格納されているデータのクエリを実行するには、adx () 関数を使用して ADX クラスター、データベース名、および目的のテーブルを指定します。 それから他のテーブルのように、出力に対してクエリを実行できます。 詳細について上でリンクしたページを参照してください。




### <a name="watchlists-are-in-general-availability"></a>ウォッチリストは一般提供中です

[ウォッチリスト](watchlists.md)機能は現在一般提供されています。 ウォッチリストを使用してアラートをビジネス データで強化し、アクセス イベントを照合するための許可リストやブロック リストを作成し、脅威を調査してアラートの疲労を軽減しましょう。

### <a name="support-for-data-residency-in-more-geos"></a>データ レジデンシーをより多くの地域でサポート

Azure Sentinel では、次の追加地域で完全データ レジデンシーがサポートされるようになりました。

ブラジル、ノルウェー、南アフリカ、韓国、ドイツ、アラブ首長国連邦 (UAE)、スイス。

データ レジデンシーが[サポートされている地域の完全な一覧](quickstart-onboard.md#geographical-availability-and-data-residency)を参照してください。

### <a name="bidirectional-sync-in-azure-defender-connector-public-preview"></a>Azure Defender コネクタでの双方向同期 (パブリック プレビュー)

Azure Defender コネクタでは現在、アラートの状態の Defender と Azure Sentinel の間の双方向同期がサポートされています。 Defender アラートを含む Sentinel インシデントを閉じると、アラートは Defender ポータルでも自動的に閉じます。

この「[更新された Azure Defender コネクタの詳細](connect-azure-security-center.md)」を参照してください。

## <a name="june-2021"></a>2021 年 6 月

- [正規化と Azure Sentinel Information Model のアップグレード](#upgrades-for-normalization-and-the-azure-sentinel-information-model)
- [更新されたサービス間コネクタ](#updated-service-to-service-connectors)
- [分析ルールのエクスポートとインポート (パブリック プレビュー)](#export-and-import-analytics-rules-public-preview)
- [アラート エンリッチメント: アラートの詳細 (パブリック プレビュー)](#alert-enrichment-alert-details-public-preview)
- [プレイブックに関するその他のヘルプ](#more-help-for-playbooks)
- [新しいドキュメントの再編成](#new-documentation-reorganization)

### <a name="upgrades-for-normalization-and-the-azure-sentinel-information-model"></a>正規化と Azure Sentinel Information Model のアップグレード

Azure Sentinel Information Model を使用すると、ソースに依存しないコンテンツの使用と作成ができます。これにより、Azure Sentinel ワークスペース内のデータの分析が簡単になります。

今月の更新では、正規化に関するドキュメントを強化し、新たなレベルの詳細と完全 DNS、プロセス イベント、認証正規化スキーマを提供しています。

詳細については、次を参照してください。

- [正規化と Azure Sentinel Information Model (ASIM)](normalization.md)(更新版)
- [Azure Sentinel 認証正規化スキーマ リファレンス (パブリック プレビュー)](authentication-normalization-schema.md) (新登場)
- [Azure Sentinel データ正規化スキーマ リファレンス](./network-normalization-schema.md)
- [Azure Sentinel DNS 正規化スキーマ リファレンス (パブリック プレビュー)](dns-normalization-schema.md) (新登場)
- [Azure Sentinel プロセス イベント正規化スキーマ リファレンス (パブリック プレビュー)](process-events-normalization-schema.md)(新登場)
- [Azure Sentinel レジストリ イベント正規化スキーマ リファレンス (パブリック プレビュー)](registry-event-normalization-schema.md)(新登場)


### <a name="updated-service-to-service-connectors"></a>更新されたサービス間コネクタ

最もよく使用されている 2 つのコネクタが、メジャー アップグレードされました。

- [Windows セキュリティ イベント コネクタ (パブリック プレビュー)](connect-windows-security-events.md)は、新しい Azure Monitor エージェント (AMA) に基づくため、取り込むデータを選択する柔軟性がはるかに高く、最小限のコストで最大限の可視性を実現できます。

- 現在、[Azure アクティビティ ログ コネクタ](./data-connectors-reference.md#azure-activity)は診断設定パイプラインに基づいており、より完全なデータが提供され、取り込み遅延が大幅に減り、パフォーマンスと信頼性が向上します。

アップグレードは自動で行なわれません。 これらのコネクタのユーザーに、新しいバージョンを有効にしていただきます。

### <a name="export-and-import-analytics-rules-public-preview"></a>分析ルールのエクスポートとインポート (パブリック プレビュー)

Azure Sentinel デプロイをコードとして管理および制御する一環として、分析ルールを JSON 形式の Azure Resource Manager (ARM) テンプレート ファイルへエクスポートし、それらのルールを同様のファイルにインポートできます。 **Scheduled** だけでなく、すべての種類の [分析ルール](detect-threats-built-in.md)を ARM テンプレートにエクスポートできます。 テンプレート ファイルにはルールの情報が、そのクエリから、それに割り当てられた MITRE ATT&CK 戦術まで、すべて含まれます。

詳細については、「[ARM テンプレート間で分析ルールをエクスポートおよびインポートします](import-export-analytics-rules.md)」を参照してください。

### <a name="alert-enrichment-alert-details-public-preview"></a>アラート エンリッチメント: アラートの詳細 (パブリック プレビュー)

エンティティ マッピングとカスタム詳細を使用してアラートの内容を強化するだけでなく、アラートが提示および表示される方法 (拡張機能、インシデント別) を、それぞれのコンテンツに基づいてカスタマイズできるようになりました。 他のアラート エンリッチメント機能と同様に、これは[分析ルール ウィザード](detect-threats-custom.md)で構成できます。

詳細については、「[Azure Sentinel でアラートの詳細をカスタマイズする](customize-alert-details.md)」を参照してください。


### <a name="more-help-for-playbooks"></a>プレイブックに関するその他のヘルプ

2 つの新しいドキュメントが、プレイブックの作成や操作を始めたり、より快適に行うのに役立ちます。
- [Azure Sentinel に対するプレイブックの認証](authenticate-playbooks-to-sentinel.md)は、Logic Apps ベースのプレイブックが Azure Sentinel に接続して情報にアクセスできるさまざまな認証方法と、それぞれを使用する適切な場合を理解するのに役立ちます。
- [プレイブックでトリガーとアクションを使用する](playbook-triggers-actions.md)では、**インシデント トリガー** と **アラート トリガー** の違いと、いつ使用するのかについて説明し、[カスタムの詳細](playbook-triggers-actions.md#work-with-custom-details)の情報にアクセスする方法など、インシデントに対応してプレイブックで実行できるさまざまなアクションの一部を示しています。

プレイブック ドキュメントでは、マルチテナント MSSP シナリオにも明示的に対応しています。

### <a name="new-documentation-reorganization"></a>新しいドキュメントの再編成

今月は [Azure Sentinel ドキュメント](index.yml)を再編成し、よくある顧客体験を取り上げた直感的なカテゴリに再構成しました。 フィルター処理されたドキュメント検索と更新されたランディング ページを使用して、Azure Sentinel ドキュメントを検索してください。

:::image type="content" source="media/whats-new/new-docs.png" alt-text="新しい Azure Sentinel ドキュメントの再編成。" lightbox="media/whats-new/new-docs.png":::


## <a name="may-2021"></a>2021 年 5 月

- [Azure Sentinel PowerShell モジュール](#azure-sentinel-powershell-module)
- [アラート グループの機能強化](#alert-grouping-enhancements)
- [Azure Sentinel のソリューション (パブリック プレビュー)](#azure-sentinel-solutions-public-preview)
- [Continuous Threat Monitoring for SAP ソリューション (パブリック プレビュー)](#continuous-threat-monitoring-for-sap-solution-public-preview)
- [脅威インテリジェンスの統合 (パブリック プレビュー)](#threat-intelligence-integrations-public-preview)
- [スケジュールされたアラートに対する Fusion (パブリック プレビュー)](#fusion-over-scheduled-alerts-public-preview)
- [SOC-ML 異常検知 (パブリック プレビュー)](#soc-ml-anomalies-public-preview)
- [IP エンティティ ページ (パブリック プレビュー)](#ip-entity-page-public-preview)
- [アクティビティのカスタマイズ (パブリック プレビュー)](#activity-customization-public-preview)
- [[Hunting]\(ハンティング\) ダッシュボード (パブリック プレビュー)](#hunting-dashboard-public-preview)
- [インシデント チーム - Microsoft Teams での共同作業 (パブリック プレビュー)](#azure-sentinel-incident-team---collaborate-in-microsoft-teams-public-preview)
- [ゼロ トラスト (TIC3.0) ブック](#zero-trust-tic30-workbook)


### <a name="azure-sentinel-powershell-module"></a>Azure Sentinel PowerShell モジュール

毎日の運用タスクを自動化するための Azure Sentinel PowerShell モジュール GA として正式リリースされました。

ダウンロードはこちらから: [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.SecurityInsights/)。

詳細については、PowerShell のドキュメントをご覧ください: [Az.SecurityInsights](/powershell/module/az.securityinsights/)

### <a name="alert-grouping-enhancements"></a>アラート グループの機能強化

分析ルールを構成してアラートを、特定のエンティティの種類に一致したときだけでなく、特定のアラート名、重大度、または構成されたエンティティの他のカスタム詳細と一致したときにも、1 つのインシデントにグループ化できるようになりました。 

分析ルール ウィザードの **[インシデントの設定]** タブで、アラートのグループ化を有効にし、 **[Group alerts into a single incident if the selected entity types and details match]\(選択したエンティティの種類と詳細が一致する場合にアラートを 1 つのインシデントにグループ化する\)** オプションを選択します。 

次に、エンティティの種類と、照合させたい関連する詳細を選択します。

:::image type="content" source="media/whats-new/alert-grouping-details.png" alt-text="エンティティの詳細を照合してアラートをグループ化します。":::

詳細については、「[アラートのグループ化](detect-threats-custom.md#alert-grouping)」を参照してください。

### <a name="azure-sentinel-solutions-public-preview"></a>Azure Sentinel のソリューション (パブリック プレビュー)

データ コネクタ、ブック、分析ルール、プレイブック、ハンティング クエリ、パーサー、ウォッチリストなど、Azure Sentinel の各種コンポーネントを組み合わせた **パッケージ コンテンツ** [ソリューション](sentinel-solutions-catalog.md)が新たに提供されます。

製品内での見つけやすさや、シングル ステップ デプロイ、エンド ツー エンドの製品シナリオが改良されたソリューションとなっています。 詳細については、[組み込みのコンテンツとソリューションの一元的な検出およびデプロイ](sentinel-solutions-deploy.md)に関する記事を参照してください。

### <a name="continuous-threat-monitoring-for-sap-solution-public-preview"></a>Continuous Threat Monitoring for SAP ソリューション (パブリック プレビュー)

Azure Sentinel ソリューションに **Continuous Threat Monitoring for SAP** が加わりました。SAP システムを監視して、ビジネス レイヤーおよびアプリケーション レイヤーにおける高度な脅威に対応することができます。

SAP データ コネクタは、SAP システム ランドスケープ全体から 14 もの多くのアプリケーション ログをストリーム配信し、NetWeaver RFC 呼び出しを介して Advanced Business Application Programming (ABAP) から、また OSSAP 制御インターフェイスを介してファイル ストレージ データからログを収集します。 SAP データ コネクタによって、基になる SAP インフラストラクチャを監視する機能が Azure Sentinels に追加されます。

SAP ログを Azure Sentinel に取り込むには、お使いの SAP 環境に Azure Sentinel SAP データ コネクタがインストールされている必要があります。 SAP データ コネクタをデプロイしたら、SAP ソリューションの多彩なセキュリティ コンテンツをデプロイし、組織の SAP 環境に関する分析情報をスムーズに取得して、関連するセキュリティ運用機能を向上させることができます。

詳細については、[SAP 用 Azure Sentinel ソリューション (パブリック プレビュー) のデプロイに関するチュートリアル](sap-deploy-solution.md)を参照してください。

### <a name="threat-intelligence-integrations-public-preview"></a>脅威インテリジェンスの統合 (パブリック プレビュー)

既知の脅威を検出して優先順位を付けるセキュリティ アナリストの能力を高めるために、Azure Sentinel には、[脅威インテリジェンスのフィードを使用](./understand-threat-intelligence.md)する方法がいくつか用意されています。

新たに提供されている数多くの統合脅威インテリジェンス プラットフォーム (TIP、Threat Intelligence Platform) 製品の 1 つを使用し、TAXII サーバーに接続して STIX に適合したあらゆる脅威インテリジェンス ソースを活用できるほか、[Microsoft Graph Security tiIndicators API](/graph/api/resources/tiindicator) と直接やり取りするあらゆるカスタム ソリューションを利用できるようになりました。

効果的な調査や対応の措置につながる TI 情報でインシデントをエンリッチメント処理するために、プレイブックから脅威インテリジェンス ソースに接続することもできます。

詳細については、「[Azure Sentinel での脅威インテリジェンスの統合](threat-intelligence-integration.md)」を参照してください。

### <a name="fusion-over-scheduled-alerts-public-preview"></a>スケジュールされたアラートに対する Fusion (パブリック プレビュー)

機械学習の相関分析エンジンである **Fusion** でマルチステージ攻撃を検出できるようになりました。他のデータ ソースからインポートされたアラートに加え、一連の [スケジュール化された分析ルール](detect-threats-custom.md)から生成されるアラートが、その相関分析の中で使用されます。

詳細については、「[Azure Sentinel の高度なマルチステージ攻撃の検出](fusion.md)」を参照してください。

### <a name="soc-ml-anomalies-public-preview"></a>SOC-ML 異常検知 (パブリック プレビュー)

Azure Sentinel の SOC-ML 機械学習ベースの異常検知であれば、他の方法では検出の目をかいくぐられてしまうような異常な行動でも特定できます。

SOC-ML では、面倒な設定が要らない分析ルール テンプレートを使用することができます。 異常そのものが悪意のある行動や疑わしい動作を意味しているとは限りませんが、検出、調査、脅威ハンティングの正確さを高める目的で、それらを使用することはできます。

詳細については、「[Azure Sentinel で SOC-ML 異常検知を使用して脅威を検出する](soc-ml-anomalies.md)」を参照してください。

### <a name="ip-entity-page-public-preview"></a>IP エンティティ ページ (パブリック プレビュー)

Azure Sentinel が新たに IP アドレス エンティティに対応しました。新しい IP エンティティ ページで IP エンティティ情報を確認することができます。

ユーザー エンティティ ページとホスト エンティティ ページと同様、IP ページは、IP に関する一般情報のほか、その IP が関係していると判明したアクティビティの一覧など、さまざまな情報を含んでおり、かつてないほど豊富な情報を提供することで、セキュリティ インシデントの調査を強力に支援します。

詳細については、「[エンティティ ページ](identify-threats-with-entity-behavior-analytics.md#entity-pages)」を参照してください。

### <a name="activity-customization-public-preview"></a>アクティビティのカスタマイズ (パブリック プレビュー)

エンティティ ページについては言えば、必要なエンティティのカスタム アクティビティを新たに作成できるようになっています。このアクティビティは、既製のアクティビティと共に、それぞれのエンティティ ページで追跡および表示されます。

詳細については、「[エンティティ ページのタイムラインのアクティビティをカスタマイズする](customize-entity-activities.md)」を参照してください。

### <a name="hunting-dashboard-public-preview"></a>[Hunting]\(ハンティング\) ダッシュボード (パブリック プレビュー)

**[Hunting]\(ハンティング\)** ブレードが更新されました。 新しいダッシュボードではシングルクリックで、すべてのクエリを実行したり特定のサブセットだけを実行したりすることができます。

結果の件数やスパイク、24 時間における件数の変化に着目することによってハンティングの出発点を特定できます。 お気に入りやデータ ソース、MITRE ATT&CK の戦術や手法、結果、結果の差分を基準に並べ替えやフィルタリングを行うこともできます。 まだ必要なデータ ソースが接続されていないクエリを確認すると共に、それらのクエリを有効にする方法についての推奨事項を入手できます。

詳細については、「[Azure Sentinel で脅威を検出する](hunting.md)」を参照してください。

### <a name="azure-sentinel-incident-team---collaborate-in-microsoft-teams-public-preview"></a>Azure Sentinel インシデント チーム - Microsoft Teams でコラボレーションを行う (パブリック プレビュー)

Azure Sentinel が Microsoft Teams との直接統合に対応しました。外部の関係者との間で、また組織の垣根を越えてシームレスにコラボレーションを行うことができます。

意思の疎通や調整において中心的な役割を果たす新しい "*インシデント チーム*" を、Azure Sentinel のインシデントから直接作成できます。

インシデント チームの能力が特に発揮されるのは、重要度が高い進行中のインシデントに特化した会議ブリッジとしての役割を担うときです。 コミュニケーションとコラボレーションに Microsoft Teams を既に使用している組織は、Azure Sentinel の統合を使用して、会話や日常業務に直接セキュリティ データを取り入れることができます。

Microsoft Teams では、最も妥当なデータをチームに提供できるよう、新しいチームの **[Incident page]\(インシデント ページ\)** タブに絶えず Azure Sentinel からの最新のデータが表示されます。

[ ![Microsoft Teams の [インシデント] ページ。](media/collaborate-in-microsoft-teams/incident-in-teams.jpg) ](media/collaborate-in-microsoft-teams/incident-in-teams.jpg#lightbox)

詳細については、「[Microsoft Teams での共同作業 (パブリック プレビュー)](collaborate-in-microsoft-teams.md)」を参照してください。

### <a name="zero-trust-tic30-workbook"></a>ゼロ トラスト (TIC3.0) ブック

新しい Azure Sentinel ゼロ トラスト (TIC3.0) ブックでは、[トラステッド インターネット接続](https://www.cisa.gov/trusted-internet-connections) (TIC) フレームワークを横断的に説明した[ゼロ トラスト](/security/zero-trust/)の原則を自動的に視覚化できます。

私たちは、コンプライアンスは単なる 1 年限りの要件ではなく、組織はその構成要素を細かく監視する必要があることを認識しています。 Azure Sentinel のゼロ トラスト ブックでは、Azure、Office 365、Teams、Intune、Windows Virtual Desktop などにわたり、Microsoft セキュリティ オファリング全体が使用されています。

[ ![ゼロ トラスト ブック。](media/zero-trust-workbook.gif) ](media/zero-trust-workbook.gif#lightbox)

**ゼロ トラスト ブック**:

- 実装者、SecOps アナリスト、評価者、セキュリティおよびコンプライアンスの意思決定者、MSSP などは、クラウド ワークロードのセキュリティ状態に関する状況認識を得ることができます。
- ナビゲーション用に選択可能な GUI ボタンを備えた、TIC 3.0 セキュリティ機能と連携する 75 を超えるコントロール カードを使用できます。
- 自動化、人工知能、機械学習、クエリ/アラート生成、視覚化、カスタマイズされたレコメンデーション、および各ドキュメントの参照によって、スタッフ配置が強化されるように設計されています。

詳細については、「[データの視覚化と監視](monitor-your-data.md)」を参照してください。

## <a name="april-2021"></a>2021 年 4 月

- [Azure Policy ベースのデータ コネクタ](#azure-policy-based-data-connectors)
- [インシデントのタイムライン (パブリック プレビュー)](#incident-timeline-public-preview)

### <a name="azure-policy-based-data-connectors"></a>Azure Policy ベースのデータ コネクタ

Azure Policy を使用すると、Azure Sentinel に取り込むログを持つ特定の種類のすべての (現在および将来の) リソースに、診断ログの設定の共通セットを適用できます。

[Azure Policy](../governance/policy/overview.md) の機能をデータ コレクション構成のタスクに活かすための取り組みを続けながら、[Azure ストレージ アカウント](./data-connectors-reference.md#azure-storage-account) リソース用の別の Azure Policy 拡張データ コレクターを提供し、パブリック プレビューにリリースしています。

また、[Azure Key Vault](./data-connectors-reference.md#azure-key-vault) と [Azure Kubernetes Service](./data-connectors-reference.md#azure-kubernetes-service-aks) 用の 2 つのプレビュー コネクタが、現在一般提供 (GA) され、[Azure SQL Databases](./data-connectors-reference.md#azure-sql-databases) コネクタに加わっています。

### <a name="incident-timeline-public-preview"></a>インシデントのタイムライン (パブリック プレビュー)

インシデントの詳細ページの最初のタブが **[タイムライン]** になりました。このタブには、インシデントのアラートとブックマークのタイムラインが表示されます。 インシデントのタイムラインを使用すると、インシデントについて理解を深め、関連するアラートやブックマーク全体で攻撃者のアクティビティのタイムラインを再構築できます。

- タイムライン内の項目を選択すると、インシデントのコンテキストを離れることなく、その詳細が表示されます。
- タイムラインのコンテンツをフィルター処理して、アラートやブックマークのみ、または特定の重要度や MITRE の戦術の項目を表示します。
- **[システム アラート ID]** リンクを選択してレコード全体を表示したり、 **[イベント]** リンクを選択して **[ログ]** 領域に関連イベントを表示したりできます。

次に例を示します。

:::image type="content" source="media/tutorial-investigate-cases/incident-timeline.png" alt-text="[インシデント] の [タイムライン] タブ":::

詳細については、「[チュートリアル:Azure Sentinel でインシデントを調査する](investigate-cases.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[Azure Sentinel をオンボードする](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[アラートの視覚化](get-visibility.md)
