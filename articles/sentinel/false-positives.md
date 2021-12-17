---
title: Microsoft Sentinel での擬陽性の処理
description: オートメーション ルールを作成するか、分析ルールを変更して例外を指定することで、Microsoft Azure Sentinel で偽陽性を解決する方法について学習します。
author: batamig
ms.author: bagol
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 817c87f1cf3c718eda9b1f8cbd6140c780d983d6
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723097"
---
# <a name="handle-false-positives-in-microsoft-sentinel"></a>Microsoft Sentinel での擬陽性の処理

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

ネットワークで疑わしい問題が発生すると、[Microsoft Azure Sentinel の分析ルール](detect-threats-built-in.md)によって通知されます。 完璧な分析ルールはありません。必ず処理が必要な偽陽性を受け取ります。 この記事では、オートメーションを使用するか、スケジュールが設定された分析ルールを変更して、偽陽性を処理する方法について説明します。

## <a name="false-positive-causes-and-prevention"></a>偽陽性の原因と防止

正しく構築された分析ルールでも、多くの場合、偽陽性はルールから除外する必要があるユーザーや IP アドレスのような特定のエンティティに由来します。

一般的なシナリオは、次のとおりです。

- 特定のユーザー (通常はサービス プリンシパル) による通常のアクティビティによって、疑わしいと思われるパターンが示される。
- 既知の IP アドレスからの意図的なセキュリティ スキャン アクティビティが、悪意のあるものとして検出される。
- プライベート IP アドレスが除外される規則で、プライベートではない一部の内部 IP アドレスも除外される必要がある。

この記事では、偽陽性を回避する 2 つの方法について説明します。

- **オートメーション ルール** では、分析ルールを変更することなく、例外を作成します。
- **スケジュールが設定された分析ルールの変更** では、より詳細で永続的な例外を許可します。

次の表で、各方法の特徴について説明します。


|Method|特徴|
|-|-|
|**オートメーション ルール**|<ul><li>複数の分析ルールに適用できる。</li><li>監査証跡が保持される。 例外によってインシデントの作成は回避されますが、アラートは監査のために引き続き記録されます。</li><li>多くの場合、アナリストによって生成される。</li><li>限られた時間で例外の適用が許可される。 たとえば、メンテナンス作業によって、メンテナンス期間外では真のインシデントになる偽陽性がトリガーされる場合があります。</li></ul>|
|**分析ルールの変更**|<ul><li>高度なブール式およびサブネットベースの例外を許可する。</li><li>ウォッチリストを使用して例外管理を一元化できる。</li><li>通常はセキュリティ オペレーション センター (SOC) のエンジニアによる実装が必要。</li><li>最も柔軟で完全な偽陽性ソリューションであるが、より複雑。</li></ul>|

## <a name="add-exceptions-by-using-automation-rules"></a>オートメーション ルールを使用して例外を追加する

例外を追加する最も簡単な方法は、偽陽性のインシデントが表示されたときに[オートメーション ルールを追加する](automate-incident-handling-with-automation-rules.md#creating-and-managing-automation-rules)ことです。

偽陽性を処理するオートメーション ルールを追加するには、次のようにします。

1. Microsoft Azure Sentinel の **[インシデント]** で、例外を作成するインシデントを選択します。
1. **[オートメーション ルールを作成する]** を選択します。
1. **[Create new automation rule]\(新しいオートメーション ルールの作成\)** サイドバーで、必要に応じて、アラート ルール名だけではなく、新しいルール名を変更して例外を識別します。
1. **[条件]** の下に、必要に応じて、例外を適用する **分析ルール名** を追加します。
1. サイドバーには、現在のインシデントで、偽陽性の原因となった可能性がある特定のエンティティが表示されます。 自動提案を受け入れるか、修正して例外を微調整します。 たとえば、IP アドレスの条件を変更して、サブネット全体に適用することができます。

   :::image type="content" source="media/false-positives/create-rule.png" alt-text="Microsoft Azure Sentinel でインシデントのオートメーション ルールを作成する方法を示すスクリーンショット。":::

1. トリガーを定義した後は、引き続きそのルールの動作を定義できます。

   :::image type="content" source="media/false-positives/apply-rule.png" alt-text="Microsoft Azure Sentinel でオートメーション ルールの作成と適用を完了する方法を示すスクリーンショット。":::

   - このルールは、例外条件を満たすインシデントをクローズするように既に構成されています。
   - 自動的にクローズされたインシデントには、例外を説明するコメントを追加できます。 たとえば、インシデントが既知の管理アクティビティからのものであることを指定できます。
   - 既定では、ルールは 24 時間後に自動的に期限が切れるように設定されます。 この有効期限は必要な場合があり、偽陰性のエラーが発生する可能性を減らします。 より長い例外が必要な場合は、 **[ルールの有効期限]** を後の時間に設定してください。

1. **[適用]** を選択して例外をアクティブにします。

> [!TIP]
> インシデントから開始することなく、オートメーション ルールをゼロから作成することもできます。 Microsoft Azure Sentinel の左側のナビゲーション メニューから **[オートメーション]** を選択し、 **[作成]**  >  **[新しいルールの追加]** を選択します。

## <a name="add-exceptions-by-modifying-analytics-rules"></a>分析ルールを変更して例外を追加する

例外を実装するためのもう 1 つのオプションは、分析ルール クエリを変更する方法です。 例外はルールに直接含めるか、可能な場合は、[ウォッチリスト](watchlists.md#use-watchlists-in-analytics-rules)への参照を使用することもできます。 その後、ウォッチリストで例外一覧を管理できます。

### <a name="modify-the-query"></a>クエリを変更する

既存の分析ルールを編集するには、Microsoft Azure Sentinel の左側のナビゲーション メニューから **[オートメーション]** を選択します。 編集するルールを選択し、右下にある **[編集]** を選択して、**分析ルール ウィザード** を開きます。

**分析ルール ウィザード** を使用して分析ルールを作成および編集する方法の詳細については、「[脅威を検出するためのカスタム分析規則を作成する](detect-threats-custom.md)」をご覧ください。

一般的なルール プリアンブルで例外を実装するために、ルール クエリの先頭付近に `where IPAddress !in ('<ip addresses>')` のような条件を追加できます。 この行により、ルールから特定の IP アドレスが除外されます。

```kusto
let timeFrame = 1d;
SigninLogs
| where TimeGenerated >= ago(timeFrame)
| where IPAddress !in ('10.0.0.8', '192.168.12.1')
...
```

この種類の例外は、IP アドレスに限定されません。 `UserPrincipalName` フィールドを使用して特定のユーザーを除外したり、`AppDisplayName` を使用して特定のアプリを除外したりできます。

複数の属性を除外することもできます。 たとえば、IP アドレス `10.0.0.8` またはユーザー `user@microsoft.com` からアラートを除外するには、次を使用します。

```kusto
| where IPAddress !in ('10.0.0.8')
| where UserPrincipalName != 'user@microsoft.com'
```

該当する場合により詳細な例外を実装し、偽陰性の可能性を減らすために、属性を組み合わせることができます。 次の例外は、両方の値が同じアラートに表示される場合にのみ適用されます。

```kusto
| where IPAddress != '10.0.0.8' and UserPrincipalName != 'user@microsoft.com'
```

### <a name="exclude-subnets"></a>サブネットを除外する

組織で使用される IP 範囲を除外するには、サブネットの除外が必要です。 次の例は、サブネットを除外する方法を示しています。

`ipv4_lookup` 演算子は、フィルター処理演算子ではなく、エンリッチメント演算子です。 `where isempty(network)` 行では、一致しないイベントを調べることによって、フィルター処理を実際に行います。

```kusto
let subnets = datatable(network:string) [ "111.68.128.0/17", "5.8.0.0/19", ...];
let timeFrame = 1d;
SigninLogs
| where TimeGenerated >= ago(timeFrame)
| evaluate ipv4_lookup(subnets, IPAddress, network, return_unmatched = true)
| where isempty(network)
...
```

### <a name="use-watchlists-to-manage-exceptions"></a>ウォッチリストを使用して例外を管理する

ウォッチリストを使用して、ルール自体以外の例外の一覧を管理できます。 該当する場合、このソリューションには次の利点があります。

- アナリストがルールを編集せずに例外を追加できる。これは、より SOC のベスト プラクティスに従っています。
- 同じウォッチリストをいくつかのルールに適用して、例外を一元的に管理できる。

ウォッチリストを使用することは、直接例外を使用することと似ています。 `_GetWatchlist('<watchlist name>')` を使用してウォッチリストを呼び出します。

```kusto
let timeFrame = 1d;
let logonDiff = 10m;
let allowlist = (_GetWatchlist('ipallowlist') | project IPAddress);
SigninLogs
| where TimeGenerated >= ago(timeFrame)
| where IPAddress !in (allowlist)
...
```

ウォッチリストを使用してサブネットのフィルター処理を行うこともできます。 たとえば、上記のサブネットを除外するコードでは、サブネットの `datatable` 定義をウォッチリストに置き換えることができます。

```kusto
let subnets = _GetWatchlist('subnetallowlist');
```

## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。
- [UEBA データを使用して擬陽性を分析する](investigate-with-ueba.md#use-ueba-data-to-analyze-false-positives)
- [オートメーション ルールで Microsoft Azure Sentinel でのインシデント処理を自動化する](automate-incident-handling-with-automation-rules.md)
- [脅威を検出するためのカスタム分析規則を作成する](detect-threats-custom.md)
- [Microsoft Sentinel ウォッチリストを使用する](watchlists.md)
