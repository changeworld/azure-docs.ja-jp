---
title: Microsoft Sentinel で脅威インジケーターを操作する | Microsoft Docs
description: この記事では、Microsoft Sentinel で脅威インテリジェンス インジケーターを使用して脅威を表示、作成、管理、視覚化、検出する方法について説明します。
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 583d86c64014bc107994880b587e4fe9f5309597
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132710993"
---
# <a name="work-with-threat-indicators-in-microsoft-sentinel"></a>Microsoft Sentinel で脅威インジケーターを操作する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

次のアクティビティによって脅威インテリジェンス (TI) を Microsoft Sentinel に統合できます。

- さまざまな TI [プラットフォーム](connect-threat-intelligence-tip.md)および [フィード](connect-threat-intelligence-taxii.md)に対して **データ コネクタ** を有効化して、Microsoft Sentinel に **脅威インテリジェンスをインポート** します。

- インポートした脅威インテリジェンスを **[ログ]** や Microsoft Sentinel の **[脅威インテリジェンス]** ページに **表示して、管理します**。

- **脅威を検出** し、インポートした脅威インテリジェンスに基づいて、組み込みの **分析** ルール テンプレートを使用してセキュリティ アラートとインシデントを生成します。

- **脅威インテリジェンス ブック** を使用して、Microsoft Sentinel で、インポートした脅威インテリジェンスに関する **重要な情報を視覚化** します。

## <a name="view-your-threat-indicators-in-microsoft-sentinel"></a>Microsoft Sentinel で脅威インジケーターを表示する

### <a name="find-and-view-your-indicators-in-logs"></a>ログでインジケーターを見つけて表示する

この手順では、使用されているソース フィードやコネクタに関係なく、インポートした脅威インジケーターを他の Microsoft Sentinel イベント データと一緒に Microsoft Sentinel の **[ログ]** 領域に表示する方法について説明します。

インポートした脅威インジケーターは、 **[Microsoft Sentinel] > [ThreatIntelligenceIndicator]** テーブルに一覧表示されます。これが、分析やブックなど、Microsoft Sentinel の他の場所で実行される脅威インテリジェンス クエリの基盤です。

**[ログ] 内の脅威インテリジェンス インジケーターを表示するには**:

1. [Azure portal](https://portal.azure.com/) を開き、**Microsoft Sentinel** サービスに移動します。

1. いずれかの脅威インテリジェンス データ コネクタを使用して、脅威インジケーターをインポートしたワークスペースを選択します。

1. Microsoft Sentinel メニューの **[全般]** セクションで **[ログ]** を選択します。

1. **ThreatIntelligenceIndicator** テーブルは、**Microsoft Sentinel** グループの下にあります。

1. テーブル名の横にある **[データのプレビュー]** アイコン (目のアイコン) を選択し、 **[クエリ エディターで表示]** ボタンを選択してこのテーブルのレコードを表示するクエリを実行します。

結果は、次に示す脅威インジケーターの例のようになります。

:::image type="content" source="media/work-with-threat-indicators/threat-intel-sample-query.png" alt-text="サンプル クエリ データ":::

### <a name="find-and-view-your-indicators-in-the-threat-intelligence-page"></a>[脅威インテリジェンス] ページでインジケーターを検索して表示する

この手順では、Microsoft Sentinel のメイン メニューからアクセスできる **[脅威インテリジェンス]** ページでインジケーターを表示し、管理する方法を説明します。 **[脅威インテリジェンス]** ページを使用して、Log Analytics クエリを作成することなく、インポートした脅威インジケーターの並べ替え、フィルター処理、検索を行います。

**[脅威インテリジェンス] ページで脅威インテリジェンス インジケーターを表示するには**:

1. [Azure portal](https://portal.azure.com/) を開き、**Microsoft Sentinel** サービスに移動します。

1. コネクタまたはプレイブックを使用して脅威インジケーターをインポートしたワークスペースを選択するか、脅威インテリジェンス データを作成したワークスペースを選択します。

1. 左側の **[脅威管理]** セクションで **[脅威インテリジェンス]** を選択します。

1. グリッドから、詳細を表示しようとしているインジケーターを選択します。 右側にインジケーターの詳細が表示され、信頼度のレベル、タグ、脅威の種類などの情報が示されます。

    Microsoft では、位置情報と WhoIs の追加データによって各インジケーターをエンリッチし、選択したインジケーターが見つかった調査についてより多くのコンテキストを提供しています。

    たとえば、次のように入力します。

    :::image type="content" source="media/whats-new/geolocation-whois-ti.png" alt-text="位置情報と WhoIs のデータを示すインジケーターが表示されている [脅威インテリジェンス] ページのスクリーンショット。" lightbox="media/whats-new/geolocation-whois-ti.png":::

**[脅威インテリジェンス]** ページでは、Microsoft Sentinel のインターフェイス内で直接脅威インジケーターを作成したり、インジケーターのタグ付けやセキュリティ調査に関連した新しいインジケーターの作成という最も一般的な 2 つの脅威インテリジェンス管理タスクを実行したりすることもできます。

> [!IMPORTANT]
> 位置情報と WhoIs のエンリッチメントは、現在プレビュー段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

#### <a name="create-a-new-indicator"></a>新しいインジケーターを作成する

1. [Azure portal](https://portal.azure.com/) から、**Microsoft Sentinel** サービスに移動します。

1. いずれかの脅威インテリジェンス データ コネクタを使用して脅威インジケーターをインポートした **ワークスペース** を選択します。

1. Microsoft Sentinel メニューの [脅威管理] セクションで **[脅威インテリジェンス]** を選択します。

1. ページ上部のメニュー バーから **[新規追加]** ボタンを選択します。

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-add-new-indicator.png" alt-text="新しい脅威インジケーターの追加" lightbox="media/work-with-threat-indicators/threat-intel-add-new-indicator.png":::

1. インジケーターの種類を選択し、 **[新しいインジケーター]** パネルのフォームに入力します。 必須フィールドには、赤いアスタリスク (*) が付いています。

1. **[適用]** を選択します。 インジケーターがインジケーター リストに追加され、 **[ログ]** の *ThreatIntelligenceIndicator* テーブルにも送信されます。

#### <a name="tag-threat-indicators"></a>脅威インジケーターにタグを付ける

タグ付けは、脅威インジケーターをグループ化して見つけやすくする簡単な方法です。 通常は、特定のインシデントに関連するインジケーター、または特定の既知のアクターや既知の攻撃キャンペーンからの脅威を表すものにタグを適用できます。 脅威インジケーターは個別にタグ付けすることも、インジケーターを複数選択してすべて一度にタグ付けすることもできます。 複数のインジケーターにインシデント ID をタグ付けする例を次に示します。 タグ付けは自由形式であるため、脅威インジケーター タグの標準的な名前付け規則を作成することをお勧めします。 各インジケーターに複数のタグを適用できます。

:::image type="content" source="media/work-with-threat-indicators/threat-intel-tagging-indicators.png" alt-text="脅威インジケーターにタグを適用する" lightbox="media/work-with-threat-indicators/threat-intel-tagging-indicators.png":::

## <a name="detect-threats-with-threat-indicator-based-analytics"></a>脅威インジケーターに基づく分析を使用して脅威を検出する

Microsoft Sentinel のような SIEM ソリューションでの脅威インジケーターの最も重要なユース ケースは、脅威検出の分析ルールを強化することです。 このようなインジケーターに基づくルールでは、データ ソースの未加工イベントが脅威インジケーターと比較されて、組織内にセキュリティ上の脅威が存在するかどうかが判断されます。 Microsoft Sentinel の **[分析]** で、スケジュールに従って実行されセキュリティ アラートを生成する、分析ルールを作成します。 このルールは、クエリと、ルールの実行頻度、セキュリティ アラートとインシデントを生成するクエリ結果の種類、および対応してトリガーされる自動化を決定する構成によって動作します。

新しい分析ルールを最初から作成することは常に可能ですが、Microsoft Sentinel には、Microsoft セキュリティ エンジニアが作成した組み込みのルール テンプレートのセットが用意されていて、それらをそのまま使用することも、ニーズに合わせて変更することもできます。 脅威インジケーターを使用するルール テンプレートは、すべて "*TI map*..." (TI で ... にマップする) で始まるタイトルが付いているため、簡単に特定できます。 これらのルール テンプレートはすべて同じように動作しますが、使用される脅威インジケーターの種類 (ドメイン、電子メール、ファイル ハッシュ、IP アドレス、または URL) と照合するイベントの種類が異なります。 各テンプレートには、ルールを機能させるために必要なデータ ソースが一覧表示されます。そのため、必要なイベントが Microsoft Sentinel に既にインポートされているかどうかを一目で確認できます。 既存のルール テンプレートを編集して保存するか、新しいルールを作成すると、それは既定で有効になります。

### <a name="configure-a-rule-to-generate-security-alerts"></a>セキュリティ アラートを生成するルールを構成する

以下に、Microsoft Sentinel にインポートした脅威インジケーターを使用してセキュリティ アラートを生成するルールを有効化して構成する方法の例を示します。 この例では、 **[TI map IP entity to AzureActivity]\(TI で IP エンティティを AzureActivity にマップする\)** というルール テンプレートを使用します。 このルールで、あらゆる IP アドレスの種類の脅威インジケーターとすべての Azure アクティビティ イベントを照合します。 一致が見つかると、セキュリティ運用チームによる調査のために、**アラート** と、対応する **インシデント** が生成されます。 この分析ルールは、**脅威インテリジェンス** データ コネクタ (脅威インジケーターをインポートする) の一方または両方と、**Azure アクティビティ** データ コネクタ (Azure サブスクリプション レベルのイベントをインポートする) を有効にした場合にのみ、正常に機能します。

1. [Azure portal](https://portal.azure.com/) から、**Microsoft Sentinel** サービスに移動します。

1. **脅威インテリジェンス** データ コネクタを使用して脅威インジケーターをインポートした先、および **Azure アクティビティ** データ コネクタを使用して Azure アクティビティ データをインポートした先の **ワークスペース** を選択します。

1. Microsoft Sentinel メニューの **[構成]** セクションで **[分析]** を選択します。

1. **[規則のテンプレート]** タブを選択して、使用可能な分析ルール テンプレートの一覧を表示します。

1. 下に示すように、 **[TI map IP entity to AzureActivity]\(TI で IP エンティティを AzureActivity にマップする\)** というタイトルの付いたルールを見つけ、必要なすべてのデータ ソースを接続済みであることを確認します。

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-required-data-sources.png" alt-text="必要なデータ ソース":::

1. **[TI で IP エンティティを AzureActivity にマップする]** ルールを選択してから、 **[ルールの作成]** を選択してルール構成ウィザードを開きます。 ウィザードで設定を構成してから、 **[次へ: ルールのロジックを設定]** を選択します。

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-create-analytics-rule.png" alt-text="分析ルールの設定":::

1. ウィザードのルール ロジック部分には、次の項目が事前に設定されています。
    - ルールで使用されるクエリ。

    - エンティティ マッピング。これはアカウント、IP アドレス、URL などのエンティティの認識方法を Microsoft Sentinel に指示するもので、これにより、**インシデント** および **調査** において、このルールによって生成されたセキュリティ アラートでのデータ処理方法が理解されるようになります。

    - このルールを実行するスケジュール。

    - セキュリティ アラートが生成されるまでに必要なクエリ結果の数。

    テンプレートの既定の設定は次のとおりです。
    - 1 時間に 1 回実行します。

    - **ThreatIntelligenceIndicator** テーブルのすべての IP アドレス脅威インジケーターを、**AzureActivity** テーブルにある過去 1 時間のイベントで見つかったすべての IP アドレスと照合します。

    - クエリ結果が 0 件より大きい (一致が見つかったことを意味する) 場合に、セキュリティ アラートを生成します。

    既定の設定は、そのままにすることも、要件に合わせて変更することもできます。また、 **[インシデントの設定]** タブでインシデント生成設定を定義できます。詳細については、「[脅威を検出するためのカスタム分析規則を作成する](detect-threats-custom.md)」を参照してください。 終了したら、 **[自動応答]** タブを選択します。

1. この分析ルールからセキュリティ アラートが生成されたときにトリガーする自動化を構成します。 Microsoft Sentinel のオートメーションは、Azure Logic Apps によって提供される **オートメーション ルール** と **プレイブック** の組み合わせを使用して行われます。 詳細については、「[チュートリアル: Microsoft Sentinel でオートメーション ルールとプレイブックを使用する](./tutorial-respond-threats-playbook.md)」を参照してください。 終了したら、 **[次へ: レビュー >]** ボタンを選択して続行します。

1. ルールの検証に合格したことを示すメッセージが表示されたら、 **[作成]** ボタンを選択すると完了です。

Microsoft Sentinel の **[分析]** セクションにある **[アクティブな規則]** タブで、有効になっているルールを確認できます。 そこから、アクティブなルールの編集、有効化、無効化、複製、削除を行えます。 新しいルールはアクティブ化すると直ちに実行され、それ以降は定義したスケジュールで実行されます。

既定の設定に従うと、ルールがスケジュールに従って実行されるたびに、結果が見つかった場合はセキュリティ アラートが生成されます。 Microsoft Sentinel のセキュリティ アラートは、Microsoft Sentinel の **[ログ]** セクションで、**Microsoft Sentinel** グループの下にある **SecurityAlert** テーブルで確認できます。

Microsoft Sentinel では、分析ルールから生成されたアラートでもセキュリティ インシデントが生成され、Microsoft Sentinel メニューの **[脅威管理]** にある **[インシデント]** で確認できます。 インシデントは、セキュリティ運用チームが、適切な応答アクションを判断するためにトリアージして調査する対象です。 詳細については、「[チュートリアル: Microsoft Sentinel を使用してインシデントを調査する](./investigate-cases.md)」を参照してください。

## <a name="detect-threats-using-matching-analytics-public-preview"></a>照合分析を使用して脅威を検出する (パブリック プレビュー)

> [!IMPORTANT]
> 照合分析は現在、プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。
>

組み込みの **Microsoft 脅威インテリジェンス照合分析** 分析ルール テンプレートを使用して、Microsoft Sentinel で、Microsoft で生成された脅威インテリジェンス データを、Microsoft Sentinel に取り込んだログと照合する[ルールを作成](detect-threats-built-in.md#use-built-in-analytics-rules)します。

脅威インテリジェンス データをログと照合すると、適切な重大度が適用された高忠実度のアラートとインシデントを生成するのに役立ちます。 一致が検出されると、生成されたアラートはインシデントにグループ化されます。

アラートは、24 時間の期間で、観察可能単位でグループ化されます。 したがって、たとえば、24 時間の期間に生成され、`abc.com` ドメインに一致するアラートはすべて、1 つのインシデントにグループ化されます。

### <a name="triage-through-an-incident-generated-by-matching-analytics"></a>照合分析によって生成されたインシデントをトリアージする

検出された一致があると、生成されたすべてのアラートはインシデントにグループ化されます。

**Microsoft 脅威インテリジェンス照合分析** ルールによって生成されたインシデントをトリアージするには、次の手順を使用します。

1. **Microsoft 脅威インテリジェンス照合分析** ルールを有効にした Microsoft Sentinel ワークスペースで、 **[インシデント]** を選択し、「**Microsoft 脅威インテリジェンス分析**」を検索します。

    見つかったインシデントはグリッドに表示されます。

1. **[すべての詳細を表示]** を選択して、特定のアラートなど、インシデントに関するエンティティとその他の詳細を表示します。

    次に例を示します。

    :::image type="content" source="media/work-with-threat-indicators/matching-analytics.png" alt-text="サンプルの一致した分析の詳細。":::

一致が検出されると、インジケーターはまた Log Analytics の **ThreatIntelligenceIndicators** に発行され、 **[脅威インテリジェンス]** ページに表示されます。 このルールから発行されたインジケーターの場合、ソースは **Microsoft 脅威インテリジェンス分析** と定義されます。

たとえば、**ThreatIntelligenceIndicators** ログでは、次のようになります。

:::image type="content" source="media/work-with-threat-indicators/matching-analytics-logs.png" alt-text="ThreatIntelligenceIndicators ログに照合分析が表示されています。":::

**[脅威インテリジェンス]** ページでは、次のようになります。

:::image type="content" source="media/work-with-threat-indicators/matching-analytics-threat-intelligence.png" alt-text="[脅威インテリジェンス] ページに照合分析が表示されています。":::

### <a name="supported-log-sources-for-matching-analytics"></a>照合分析でサポートされているログ ソース

**Microsoft 脅威インテリジェンス照合分析** ルールは、現在、次のログ ソースでサポートされています。

| ログ ソース | 説明 |
| --------- | --------- |
| [CEF](connect-common-event-format.md) | Log Analytics の **CommonSecurityLog** テーブルに取り込まれたすべての CEF ログに対して照合が行われます (`DeviceVendor` に `Cisco` と示されているものを除く)。 <br><br>Microsoft で生成された脅威インテリジェンスを CEF ログと照合するには、必ず、CEF ログの `RequestURL` フィールドにドメインをマップしてください。 |
| [DNS](./data-connectors-reference.md#windows-dns-server-preview) | クライアントから DNS サービスへの参照 DNS クエリ (`SubType == "LookupQuery"`) であるすべての DNS ログに対して照合が行われます。 DNS クエリは、IPv4 (`QueryType=”A”`) と IPv6 クエリ (`QueryType=” AAAA”`) に対してのみ処理されます。<br><br>Microsoft で生成された脅威インテリジェンスを DNS ログと照合するために、手動での列のマッピングは必要ありません。これは、すべての列は Windows DNS サーバーからの標準であり、ドメインは既定で `Name` 列に含まれるからです。 |
| [Syslog](connect-syslog.md) | 現在、照合は、`Facility` が `cron` である Syslog イベントに対してのみ実行されます。 <br><br>Microsoft で生成された脅威インテリジェンスを Syslog と照合するために、手動で列をマッピングする必要ありません。 詳細は、既定で Syslog の `SyslogMessage` フィールドに表示され、ルールによって SyslogMessage から直接ドメインの解析が行われます。 |
|  |  |


## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>ブックによって脅威インテリジェンスに関する分析情報を提供する

専用の Microsoft Sentinel ブックを使用して、Microsoft Sentinel で脅威インテリジェンスに関する重要な情報を視覚化できます。このブックは、ビジネス ニーズに応じて簡単にカスタマイズできます。

Microsoft Sentinel で提供される脅威インテリジェンス ブックを見つける方法と、ブックを編集してカスタマイズする方法の例を次に示します。

1. [Azure portal](https://portal.azure.com/) から、**Microsoft Sentinel** サービスに移動します。

1. いずれかの脅威インテリジェンス データ コネクタを使用して脅威インジケーターをインポートした **ワークスペース** を選択します。

1. Microsoft Sentinel メニューの **[脅威管理]** セクションで、 **[ブック]** を選択します。

1. **[脅威インテリジェンス]** というタイトルの付いたブックを見つけ、下に示すように、**ThreatIntelligenceIndicator** テーブルにデータがあることを確認します。

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-verify-data.png" alt-text="データの確認":::

1. **[保存]** ボタンを選択し、ブックを格納する Azure の場所を選択します。 この手順は、ブックを任意の方法で変更し、変更内容を保存する場合に必要です。

1. 次に、 **[保存されたブックの表示]** ボタンを選択して、表示および編集用にブックを開きます。

1. これで、テンプレートによって提供される既定のグラフが表示されます。 グラフを変更するには、ページ上部にある **[編集]** ボタンを選択して、ブックの編集モードに入ります。

1. 脅威の種類別に脅威インジケーターを示す新しいグラフを追加します。 ページの下部までスクロールし、 **[クエリの追加]** を選択します。

1. **Log Analytics ワークスペースのログ クエリ** のテキスト ボックスに次のテキストを追加します。
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. **[視覚化]** ドロップダウンで、 **[横棒グラフ]** を選択します。

1. **[編集が完了しました]** ボタンを選択します。 ブックの新しいグラフが作成されました。

    :::image type="content" source="media/work-with-threat-indicators/threat-intel-bar-chart.png" alt-text="横棒グラフ":::

ブックには、Microsoft Sentinel のあらゆる側面に関する分析情報を提供する、強力な対話型ダッシュボードが用意されています。 ブックでは多くのことができますが、提供されるテンプレートを最適な出発点として、使い慣れてきたらテンプレートをカスタマイズしたり、さまざまなデータ ソースを組み合わせた新しいダッシュボードを作成したりできます。これにより、独自の方法でデータを視覚化できるようになります。 Microsoft Sentinel ブックは Azure Monitor ブックに基づいているため、広範なドキュメントが既にあり、さらに多くのテンプレートを利用できます。 [Azure Monitor ブックを使用した対話型レポートの作成](../azure-monitor/visualize/workbooks-overview.md)の方法に関するこの記事から読み始めることをお勧めします。 

また、[GitHub 上に Azure Monitor ブック](https://github.com/microsoft/Application-Insights-Workbooks)の活発なコミュニティがあり、より多くのテンプレートをダウンロードしたり、独自のテンプレートを投稿したりすることができます。

## <a name="next-steps"></a>次のステップ

この記事では、Microsoft Sentinel 全体で脅威インテリジェンス インジケーターを操作するすべての方法について学習しました。 Microsoft Sentinel の脅威インテリジェンスに関するさらなる情報については、次の記事を参照してください。
- [Microsoft Sentinel の脅威インテリジェンスについて](understand-threat-intelligence.md)。
- Microsoft Azure Sentinel を [STIX、TAXII 脅威インテリジェンス フィード](./connect-threat-intelligence-taxii.md)に接続する。
- Microsoft Sentinel に[脅威インテリジェンス プラットフォームを接続する](./connect-threat-intelligence-tip.md)。
- Microsoft Sentinel と容易に統合できる [TIP プラットフォーム、TAXII フィード、エンリッチメント](threat-intelligence-integration.md)を確認する。
