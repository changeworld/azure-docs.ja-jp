---
title: 既存の SIEM から Microsoft Sentinel に移行します。
description: 組織全体でスケーラブルかつインテリジェントなセキュリティ分析を行うために、既存の SIEM から Microsoft Sentinel への最適な移行を行う方法について学習します。
services: sentinel
documentationcenter: na
author: batamig
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5e1946affdf33239c89bbc5556f5905ccad6ed4f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132713177"
---
# <a name="migrate-to-microsoft-sentinel-from-an-existing-siem"></a>既存の SIEM から Microsoft Sentinel に移行する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

セキュリティ オペレーション センター (SOC) チームは、一元化されたセキュリティ情報およびイベント管理 (SIEM) とセキュリティ オーケストレーション、オートメーション、および応答 (SOAR) ソリューションを使用して、次第に分散型になっているデジタル資産を保護します。

レガシ SIEM は多くの場合、オンプレミスであり、オンプレミス資産の高いカバレッジ率を維持できます。 ただし、オンプレミスのアーキテクチャでは、Azure、Microsoft 365、AWS、または Google Cloud Platform (GCP) など、クラウド資産のカバレッジが不十分である可能性があります。 これに対し、Microsoft Sentinel では、オンプレミスとクラウドの両方の資産からデータを取り込み、資産全体のカバレッジを確保できます。

この記事では、既存のレガシ SIEM から Microsoft Sentinel にサイド バイ サイド構成で、または完全な Microsoft Sentinel デプロイに移行する方法について説明します。

## <a name="plan-your-migration"></a>移行を計画する

ビジネス ニーズと利用可能なリソースに応じて、Microsoft Sentinel への直接または段階的な移行を開始することを決定できます。

移行によってカバレッジにギャップが生じず、組織のセキュリティが危険にさらされる可能性が生じないように、移行を適切に計画する必要があります。

最初に、主要なコア機能と最優先の要件を特定します。 現在の SIEM でカバーされている主なユース ケースを評価し、Microsoft Sentinel で引き続きカバレッジを提供する必要がある検出と機能を決定します。

移行する正確なデータ ソースと検出ルールを考慮して、移行プロセスの各ステップでインプロセス計画を追加します。 詳細については、「[データの移行](#migrate-your-data)」と「[分析ルールの移行](#migrate-analytics-rules)」を参照してください。

> [!TIP]
> 現在の SIEM には、膨大な数の検出とユース ケースがある場合があります。 ビジネスに最も役立つものを特定し、移行する必要はない可能性があるものを決定します。 たとえば、過去 1 年間に結果が生成された検出を確認します。
>

### <a name="compare-your-legacy-siem-to-microsoft-sentinel"></a>従来の SIEM を Microsoft Sentinel と比較する

レガシ SIEM を Microsoft Sentinel と比較して移行の完了条件を絞り込み、Microsoft Sentinel でより多くの価値を抽出できる場所を理解します。

たとえば、次の重要な区分を評価します。

|評価区分 |説明  |
|---------|---------|
|**攻撃検出の対象範囲。**     | [MITRE ATT&CK](https://attack.mitre.org/) または同様のフレームワークを使用して、各 SIEM がすべての種類の攻撃をどれだけ適切に検出できるかを比較します。        |
|**応答性。**     |   平均応答時間 (MTTA) を測定します。これは、アラートが SIEM に表示されてから、アナリストがその作業を開始するまでの時間です。 この時間はおそらく、SIEM 間でほぼ同等です。      |
|**平均修復時間 (MTTR)。**     |    同等のスキル レベルのアナリストを想定して、各 SIEM によって調査されたインシデントの MTTR を比較します。     |
|**ハンティング速度と機敏性。**     | 完全な形式の仮説からデータのクエリ、各 SIEM プラットフォームでの結果の取得まで、チームがどれだけ速くハントできるのか測定します。        |
|**容量増加の摩擦。**     |  使用量の増加に応じて、容量を追加する場合の困難度を比較します。 クラウド サービスとアプリケーションは、従来のオンプレミス ワークロードよりも多くのログ データを生成する傾向があるという点に留意してください。       |
|     |         |

既存のオンプレミス SIEM への投資が制限されている場合、または投資がない場合、Microsoft Sentinel への移行は、簡単な直接デプロイにすることができます。 ただし、レガシ SIEM に多額の投資を行っている企業では、通常、移行タスクに対応するために複数ステージのプロセスが必要です。

Microsoft Sentinel ではオンプレミスとクラウドの両方に対して拡張データと応答を提供しますが、Microsoft Sentinel とレガシ SIEM を[サイド バイ サイド](#select-a-side-by-side-approach-and-method)で実行することで、移行を緩やかに開始できます。 サイド バイ サイド アーキテクチャでは、ローカル リソースはオンプレミスの SIEM リソースとクラウド リソースを使用し、新しいワークロードではクラウドベースの分析を使用できます。

長期的なサイド バイ サイド構成を選択する場合を除いて、完全な Microsoft Sentinel デプロイへの移行を完了して、インフラストラクチャ コストの削減、リアルタイムの脅威分析、クラウドのスケーラビリティを利用してください。

## <a name="select-a-side-by-side-approach-and-method"></a>サイド バイ サイドのアプローチと方法を選択する

サイド バイ サイド アーキテクチャは、組織の SIEM ニーズに応じて、完全にクラウドでホストされる SIEM につながる短期的な移行フェーズとして、または中長期の運用モデルとして使用します。

たとえば、推奨されるアーキテクチャでは、移行を完了するのに十分な時間だけサイド バイ サイド アーキテクチャを使用しますが、組織は、レガシ SIEM から移行する準備ができていない場合など、サイド バイ サイド構成を長期間使用する必要がある場合があります。 通常、長期的なサイド バイ サイド構成を使用する組織では、Microsoft Sentinel を使用してクラウド データのみを分析します。

移行に使用するアプローチを決定する際には、各アプローチの長所と短所を考慮してください。

> [!NOTE]
> 多くの組織では、コストと複雑さのために、複数のオンプレミス分析ソリューションを実行しないようにしています。
>
> Microsoft Sentinel では[従量課金制の価格](billing.md)と柔軟なインフラストラクチャが提供され、SOC チームは変更に適応する時間を得ることができます。 組織に最適なペースでコンテンツを移行してテストします。
>
### <a name="short-term-approach"></a>短期的なアプローチ

:::row:::
   :::column span="":::
      **長所**

        - SOC スタッフに、ワークロードと分析の移行時に新しいプロセスに適応する時間を提供します。

        - ハンティング シナリオのために、すべてのデータ ソース間で深い相関関係が得られます。

        - SIEM 間で分析を行い、転送ルールを作成し、2 か所で調査を終了する必要がなくなります。

        - SOC チームがレガシ SIEM ソリューションをすばやくダウングレードし、インフラストラクチャとライセンスのコストを排除できます。
   :::column-end:::
   :::column span="":::
      **短所**

        - SOC スタッフには急な学習曲線が必要な場合があります。
   :::column-end:::
:::row-end:::

### <a name="medium--to-long-term-approach"></a>中長期的なアプローチ

:::row:::
   :::column span="":::
      **長所**

        - レガシ SIEM から完全に移行せずに、AI、ML、調査機能など、Microsoft Sentinel の主要なベネフィットを使用できます。

        - Microsoft Sentinel でクラウドまたは Microsoft のデータを分析することで、レガシ SIEM と比較してコストを削減できます。
   :::column-end:::
   :::column span="":::
      **短所**

        - 異なるデータベース間で分析を分離することで、複雑さが増します。

        - 複数環境のインシデントについてケース管理と調査を分割します。

        - スタッフとインフラストラクチャのコストが増加します。

        - SOC スタッフは、2 つの異なる SIEM ソリューションに関する知識を要求されます。
   :::column-end:::
:::row-end:::



### <a name="send-alerts-from-a-legacy-siem-to-microsoft-sentinel-recommended"></a>レガシ SIEM から Microsoft Sentinel にアラートを送信する (推奨)

レガシ SIEM から Microsoft Sentinel にアラート (異常なアクティビティのインジケーター) を送信します。

- Microsoft Sentinel でクラウド データを取り込んで分析する
- レガシ SIEM を使用して、オンプレミスのデータを分析し、アラートを生成します。
- 単一のインターフェイスを確立するために、オンプレミスの SIEM から Microsoft Sentinel にアラートを転送します。

たとえば、[Logstash](connect-logstash.md)、[API](/rest/api/securityinsights/)、または [Syslog](connect-syslog.md) を使用してアラートを転送し、それらを [JSON](https://techcommunity.microsoft.com/t5/azure-sentinel/tip-easily-use-json-fields-in-sentinel/ba-p/768747) 形式で Microsoft Sentinel の [Log Analytics ワークスペース](../azure-monitor/logs/quick-create-workspace.md)に格納します。

レガシ SIEM から Microsoft Sentinel にアラートを送信することで、チームはそれらのアラートを Microsoft Sentinel で相互に関連付け、調査できます。 チームは、必要に応じて引き続きレガシ SIEM にアクセスして、より詳細な調査を行うことができます。 その間、データ ソースの移行は、移行期間を延長して続行できます。

この推奨されるサイド バイ サイド移行方法では、Microsoft Sentinel の完全な価値と、組織に合ったペースでデータ ソースを移行する機能が提供されます。 この方法では、データ ソースの移動中にデータ ストレージとインジェストのコストが重複しないようにします。

詳細については、次を参照してください。

- [QRadar の攻撃を Microsoft Sentinel に移行する](https://techcommunity.microsoft.com/t5/azure-sentinel/migrating-qradar-offenses-to-azure-sentinel/ba-p/2102043)
- [Splunk のデータを Microsoft Sentinel にエクスポートする](https://techcommunity.microsoft.com/t5/azure-sentinel/how-to-export-data-from-splunk-to-azure-sentinel/ba-p/1891237)。


### <a name="send-alerts-and-enriched-incidents-from-microsoft-sentinel-to-a-legacy-siem"></a>アラートとエンリッチされたインシデントを Microsoft Sentinel からレガシ SIEM に送信する

Microsoft Sentinel でクラウド データなどのデータを分析し、生成されたアラートをレガシ SIEM に送信します。 "*レガシ*" SIEM を単一のインターフェイスとして使用して、Microsoft Sentinel で生成されたアラートと相互に関連付けます。 Microsoft Sentinel で生成されたアラートの詳細な調査のために、Microsoft Sentinel を引き続き使用できます。

この構成はコスト効率が高く、コストを重複させたり、データの料金を 2 回支払ったりせずに、クラウド データ分析を Microsoft Sentinel に移行できます。 自分のペースで自由に移行できます。 引き続きデータ ソースと検出を Microsoft Sentinel に移行すると、プライマリ インターフェイスとして Microsoft Sentinel に移行しやすくなります。 ただし、エンリッチされたインシデントをレガシ SIEM に転送するだけでは、Microsoft Sentinel の調査、ハンティング、自動化の機能から得る価値が制限されます。

詳細については、次を参照してください。

- [エンリッチされた Microsoft Sentinel のアラートをレガシ SIEM に送信する](https://techcommunity.microsoft.com/t5/azure-sentinel/sending-enriched-azure-sentinel-alerts-to-3rd-party-siem-and/ba-p/1456976)
- [エンリッチされた Microsoft Sentinel のアラートを IBM QRadar に送信する](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-side-by-side-with-qradar/ba-p/1488333)
- [Microsoft Sentinel のアラートを Splunk に取り込む](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-side-by-side-with-splunk/ba-p/1211266)

### <a name="other-methods"></a>その他の方法

次の表では、推奨 "*されない*" サイド バイ サイド構成と、その理由の詳細について説明します。

|Method  |説明  |
|---------|---------|
|**Microsoft Sentinel のログをレガシ SIEM に送信する**     |  この方法では、オンプレミスの SIEM のコストとスケールの課題が引き続き残ります。 <br><br>Microsoft Sentinel でのデータ インジェストに対して、レガシ SIEM のストレージ コストと共に料金を支払います。また、Microsoft Sentinel の SIEM および SOAR 検出、分析、ユーザー エンティティ行動分析 (UEBA)、AI、または調査および自動化ツールを利用できません。       |
|**レガシ SIEM から Microsoft Sentinel にログを送信する**     |   この方法では Microsoft Sentinel のすべての機能が提供されますが、組織は 2 つの異なるデータ インジェスト ソースに対して引き続き料金を支払います。 このモデルでは、アーキテクチャの複雑さが増すだけでなく、コストが高くなる可能性があります。     |
|**Microsoft Sentinel とレガシ SIEM を完全に分離された 2 つのソリューションとして使用する**     |  Microsoft Sentinel を使用してクラウド データなどの一部のデータ ソースを分析し、他のソースに対してはオンプレミスの SIEM を引き続き使用することができます。 この設定では、各ソリューションを使用する場合の境界が明確になり、コストの重複が回避されます。 <br><br>ただし、相関関係が難しくなり、両方のデータ ソースにまたがる攻撃を完全には診断できなくなります。 今日の状況では、脅威はしばしば組織全体を横方向に移動しているため、このような可視性のギャップによってセキュリティ上の大きなリスクが生じる可能性があります。       |
|     |         |



## <a name="migrate-your-data"></a>データの移行

現在の主要なユース ケースを表すデータのみを移行するようにしてください。

1. 各ユース ケースをサポートするために必要なデータを特定します。

1. 現在のデータ ソースが貴重なデータを提供するかどうかを判断します。

1. 現在の SIEM の可視性のギャップと、それらを解消する方法を特定します。

1. データ ソースごとに、生ログを取り込む必要があるかどうか、コストがかかる可能性があるのはどれか、またはエンリッチされたアラートが重要なユース ケースに対して十分なコンテキストを提供するかどうかを検討してください。

      たとえば、組織全体のセキュリティ製品からエンリッチされたデータを取り込み、Microsoft Sentinel を使用してそれらを相互に関連付けることができます。データ ソース自体から生ログを取り込む必要はありません。

1. 次のリソースのいずれかを使用してデータを取り込みます。

    - **Microsoft Sentinel の[組み込みのデータ コネクタ](connect-data-sources.md)** を使用して、データの取り込みを開始します。 たとえば、クラウド データを使用して[無料試用版](billing.md#free-trial)を開始したり、[無料のデータ コネクタ](billing.md#free-data-sources)を使用して他の Microsoft 製品からデータを取り込んだりすることができます。

    - **[Syslog](connect-data-sources.md#syslog)、[Common Event Format (CEF)](connect-data-sources.md#common-event-format-cef)、または [REST API](connect-data-sources.md#rest-api-integration)** を使用して、他のデータ ソースを接続します。

        詳細については、[Microsoft Sentinel データ コネクタのリファレンス](data-connectors-reference.md)、および [Microsoft Sentinel のソリューション カタログ](sentinel-solutions-catalog.md)を参照してください。

> [!TIP]
> - 無料のデータ ソースのみに制限することで、お客様にとって重要なデータでテストする能力を制限できます。 テスト時には、無料と有料の両方のデータ コネクタからのデータ インジェストを制限して、テスト結果を最大限に活用することを検討してください。
>
> - Microsoft Sentinel で検出とビルドのユース ケースを移行する際には、取り込んだデータに注意して、重要な優先度に合わせてその価値を確認してください。 データ収集の会話を見直して、ユース ケース全体のデータの深さと幅を確認します。
>

## <a name="migrate-analytics-rules"></a>分析ルールを移行する

Microsoft Sentinel では、機械学習分析を使用して忠実で実用的なインシデントを作成します。既存の検出の一部は Microsoft Sentinel で冗長になる場合があります。 そのため、検出ルールと分析ルールをすべて無条件に移行しないようにしてください。

- ビジネスの優先度と効率を考慮して、ルールの移行を正当化するユース ケースを選択してください。

- 既にユース ケースに対処している可能性のある[組み込みの分析ルール](detect-threats-built-in.md)を確認します。 Microsoft Sentinel で、 **[構成] > [分析] > [ルール テンプレート]** タブにアクセスし、組み込みテンプレートに基づいてルールを作成します。

- 過去 6 ～ 12 か月以内にアラートをトリガーしていないルールを確認し、それでも関連性があるかどうかを判断します。

- 日常的に無視する低レベルの脅威やアラートを排除します。

**Microsoft Sentinel に分析ルールを移行するには、次のようにします**。

1. 移行するルールごとにテスト システムが配置されていることを確認します。

    1. 完全なテスト シナリオとスクリプトを含めて、移行したルールの **検証プロセスを準備します**。

    1. 移行したルールをテストするのに **役立つリソースがチームにあることを確認します**。

    1. **必要なデータ ソースが接続されていることを確認し**、データ接続方法を確認します。

1. Microsoft Sentinel で組み込みテンプレートとして検出が利用可能かどうかを確認します。

    - **組み込みのルールが十分な場合は**、組み込みのルール テンプレートを使用して、独自のワークスペースのルールを作成します。

        Microsoft Sentinel で、 **[構成] > [分析] > [ルール テンプレート]** タブにアクセスし、関連する各分析ルールを作成および更新します。

        詳細については、「[難しい設定なしで脅威を検出する](detect-threats-built-in.md)」を参照してください。

    - **Microsoft Sentinel の組み込みルールでカバーされていない検出がある場合は**、[Uncoder.io](https://uncoder.io/) などのオンライン クエリ コンバーターを使用してクエリを KQL に変換してください。

        トリガーの条件とルール アクションを特定し、KQL クエリを構築して確認します。

    - **組み込みのルールもオンライン ルール コンバーターも十分でない場合は**、ルールを手動で作成する必要があります。 このような場合は、次の手順を使用してルールの作成を開始します。

        1. **ルールで使用するデータ ソースを特定します**。 Microsoft Sentinel でデータ ソースとデータ テーブルの間にマッピング テーブルを作成し、クエリを実行するテーブルを特定します。

        1. ルールで使用するデータの **属性、フィールド、またはエンティティを特定します**。

        1. **ルールの条件とロジックを特定します**。 この段階で、KQL クエリを作成する方法のサンプルとしてルール テンプレートを使用することができます。

            フィルター、相関関係ルール、アクティブ リスト、参照セット、ウォッチリスト、検出の異常、集計などを検討してください。 レガシ SIEM から提供されている参照を使用して、クエリ構文を最適にマップする方法を理解することができます。

            たとえば、次を参照してください。

            - [ArcSight/QRadar と Microsoft Sentinel 間のルール マッピングのサンプル](https://github.com/Azure/Azure-Sentinel/blob/master/Tools/RuleMigration/Rule%20Logic%20Mappings.md)
            - [SPL から KQL へのマッピングのサンプル](https://github.com/Azure/Azure-Sentinel/blob/master/Tools/RuleMigration/Rule%20Logic%20Mappings.md) 

        1. **トリガーの条件とルール アクションを特定し、KQL クエリを構築して確認します**。 クエリを確認するときは、KQL の最適化に関するガイダンス リソースを検討してください。

1. 関連する各ユース ケースでルールをテストします。 期待される結果が得られない場合は、KQL を確認し、もう一度テストすることをお勧めします。

1. 問題がなければ、移行されたルールを検討できます。 必要に応じて、ルール アクションのプレイブックを作成します。 詳細については、「[Microsoft Sentinel のプレイブックを使用して脅威への対応を自動化する](automate-responses-with-playbooks.md)」を参照してください。

**詳細については、次を参照してください**。

- [**脅威を検出するためのカスタム分析規則を作成します**](detect-threats-custom.md)。 [アラートのグループ化](detect-threats-custom.md#alert-grouping)を使用して、特定の期間内に発生したアラートをグループ化することで、アラートの疲労を減らすことができます。
- [**データ フィールドを Microsoft Sentinel のエンティティにマップして**](map-data-fields-to-entities.md)、SOC エンジニアが調査中に追跡する証拠の一部としてエンティティを定義できるようにします。 また、エンティティ マッピングを使用すると、SOC アナリストは、時間と労力を削減するのに役立つ、直感的な調査グラフ (investigate-cases.md#use-the-investigation-graph-to-deep-dive) を活用することができます。
- 証拠を使用して、インシデントのプレビュー ウィンドウで特定のインシデントに関連付けられているイベント、アラート、ブックマークを表示する方法の例として、[**UEBA データを使用してインシデントを調査します**](investigate-with-ueba.md)。
- [**Kusto クエリ言語 (KQL)**](/azure/data-explorer/kusto/query/)。データを処理して結果を返すために、[Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) データベースに読み取り専用の要求を送信するために使用できます。 KQL は、[Microsoft Defender for Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender) や [Application Insights](../azure-monitor/app/app-insights-overview.md) など、他の Microsoft サービスでも使用されます。

## <a name="use-automation-to-streamline-processes"></a>自動化を使用してプロセスを効率化する

自動ワークフローを使用し、アラートを一般的なインシデントにグループ化して優先順位を付け、その優先順位を変更します。

詳細については、次を参照してください。

- [Microsoft Sentinel でのセキュリティ オーケストレーション、オートメーション、および応答 (SOAR)](automation.md)
- [Microsoft Sentinel のプレイブックを使用して脅威への対応を自動化する](automate-responses-with-playbooks.md)
- [自動化ルールで Microsoft Sentinel でのインシデント処理を自動化する](automate-incident-handling-with-automation-rules.md)

## <a name="retire-your-legacy-siem"></a>レガシ SIEM を削除する

次のチェックリストを使用して、Microsoft Sentinel に完全に移行し、レガシ SIEM を削除する準備ができていることを確認します。


|準備区分  |詳細  |
|---------|---------|
|**テクノロジの準備**     | **重要なデータの確認**: すべてのソースとアラートが Microsoft Sentinel で利用可能であることを確認します。 <br><br>**すべてのレコードのアーカイブ**: 組織の履歴を保持するために、重要な過去のインシデント レコードとケース レコードを保存します (生データはオプション)。   |
|**プロセスの準備**     |  **プレイブック**: [調査と検出プロセス](investigate-cases.md)を Microsoft Sentinel に更新します。<br><br>**メトリック**: Microsoft Sentinel からすべての主要なメトリックを取得できることを確認します。<br><br>**ブック**: [カスタム ブック](monitor-your-data.md)を作成するか、組み込みのブック テンプレートを使用して、[データ ソースに接続する](connect-data-sources.md)とすぐに洞察を得ることができます。<br><br>**インシデント**: 必要なソース データを含めて、現在のすべてのインシデントを新しいシステムに転送してください。        |
|**スタッフの準備**     |  **SOC アナリスト**: チームの全員が Microsoft Sentinel のトレーニングを受け、問題なくレガシ SIEM を離れることができることを確認します。   |
|     |         |
## <a name="next-steps"></a>次のステップ

移行後は、Microsoft の Microsoft Sentinel リソースを調べてスキルを伸ばし、Microsoft Sentinel を最大限に活用してください。

また、[統合された脅威保護](https://www.microsoft.com/security/business/threat-protection)のために [Microsoft 365 Defender](./microsoft-365-defender-sentinel-integration.md) および [Microsoft Defender for Cloud](../security-center/azure-defender.md) と共に Microsoft Sentinel を使用して、脅威保護を強化することも検討してください。 Microsoft Sentinel によって提供される幅広い可視性を活用しながら、詳細な脅威分析を深く掘り下げてください。

詳細については、次を参照してください。

- [ルールの移行のベスト プラクティス](https://techcommunity.microsoft.com/t5/azure-sentinel/best-practices-for-migrating-detection-rules-from-arcsight/ba-p/2216417)
- [ウェビナー: 検出ルールを変換するためのベスト プラクティス](https://www.youtube.com/watch?v=njXK1h9lfR4)
- [Microsoft Sentinel でのセキュリティ オーケストレーション、オートメーション、および応答 (SOAR)](automation.md)
- [インシデント メトリックを使用して SOC をより適切に管理する](manage-soc-with-incident-metrics.md)
- [Microsoft Sentinel のラーニング パス](/learn/paths/security-ops-sentinel/)
- [SC-200 Microsoft Security Operations Analyst 認定資格](/learn/certifications/exams/sc-200)
- [Microsoft Sentinel Ninja トレーニング](https://techcommunity.microsoft.com/t5/azure-sentinel/become-an-azure-sentinel-ninja-the-complete-level-400-training/ba-p/1246310)
- [Microsoft Sentinel を使用してハイブリッド環境での攻撃を調査する](https://mslearn.cloudguides.com/guides/Investigate%20an%20attack%20on%20a%20hybrid%20environment%20with%20Azure%20Sentinel)
