---
title: Azure Monitor アラートのアクション ルール
description: アクション ルールとはどのようなものか、およびそれを構成して管理する方法を説明します。
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: 212e6b042caec5f24a620dc491dc674417816df7
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310382"
---
# <a name="action-rules-preview"></a>アクション ルール (プレビュー)

アクション ルールを使用すると、Resource Manager の任意のスコープ (サブスクリプション、リソース グループ、リソース) で、アクション (またはアクションの抑制) を定義できます。 さまざまなフィルターを使用して、操作するアラート インスタンスの特定のサブセットを絞り込むことができます。 

## <a name="why-and-when-should-you-use-action-rules"></a>アクション ルールを使用する理由とタイミング

### <a name="suppression-of-alerts"></a>アラートの抑制

アラートによって生成される通知を抑制することが有用な状況はしばしば、また数多くあります。これは、計画メンテナンス期間中の抑制から営業時間外の抑制まで、多岐にわたります。 たとえば、'ContosoVM' が計画メンテナンス中のため、'ContosoVM' の担当チームは、次の週末はアラート通知を抑制したいと考えています。 'ContosoVM' 上で構成されている個々の、またすべてのアラート ルールを手動で無効にする (そして、メンテナンス後に再び有効にする) ことは可能ですが、簡単な作業ではありません。 アクション ルールを使用すれば、アラートの抑制を大規模に定義することができ、抑制する期間も柔軟に設定できます。 前の例に戻ると、週末のアラート通知をすべて抑制する 1 つのアクション ルールを、チームで 'ContosoVM' に対して定義できるようになります。


### <a name="actions-at-scale"></a>大規模なアクション

アラート ルールを使用すると、アラート生成時にトリガーされるアクション グループを定義できますが、顧客は多くの場合、業務スコープ全体にわたる共通のアクション グループを持つ傾向があります。 たとえば、リソース グループ 'ContosoRG' の担当チームはおそらく、'ContosoRG' 内で定義されているすべてのアラート ルールに対して同じアクション グループを定義します。 アクション ルールを使用すれば、構成されたスコープで生成されたすべてのアラートに対してアクション グループをトリガーできるよう、アクションを大規模に定義できるようになるため、このプロセスを簡略化できます。 前の例に戻ると、チームは 'ContosoRG' に、その中で生成されたすべてのアラートに対して同じアクション グループをトリガーする 1 つのアクション ルールを定義できるようになります。

> [!NOTE]
> アクション ルールは、現在、サービス正常性アラートには適用されません。

## <a name="configuring-an-action-rule"></a>アクション ルールの構成

Azure Monitor のアラート ランディング ページ から **[Manage actions]\(アクションの管理\)** を選択して、機能にアクセスできます。 次に、 **[Action Rules (Preview)]\(アクション ルール (プレビュー)\)** を選択します。 アラートのランディング ページのダッシュボードから **[Action Rules (Preview)]\(アクション ルール (プレビュー)\)** を選択して、アクセスできます。

![Azure Monitor ランディング ページからのアクション ルール](media/alerts-action-rules/action-rules-landing-page.png)

**[+ New Action Rule]\(+ 新しいアクション ルール\)** を選択します。 

![新しいアクション ルールを追加する](media/alerts-action-rules/action-rules-new-rule.png)

または、アラート ルールを構成するときにアクション ルールを作成することもできます。

![新しいアクション ルールを追加する](media/alerts-action-rules/action-rules-alert-rule.png)

アクション ルール作成フローが表示されます。 次の要素を構成します。 

![新しいアクション ルールの作成フロー](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Scope (スコープ)

最初にスコープを選択します (つまり、ターゲットのリソース、リソース グループ、またはサブスクリプション)。 また、上記の任意の組み合わせを複数選択することもできます (1 つのサブスクリプション内)。 

![アクション ルールのスコープ](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>フィルター条件

フィルターを定義して、定義したスコープでのアラートの特定のサブセットをさらに絞り込むことができます。 

使用可能なフィルターは次のとおりです。 

* **[重大度]** :1 つまたは複数のアラートの重大度を選択します。 重大度 = Sev1 は、重大度が Sev1 であるすべてのアラートにアクション ルールが適用されることを意味します。
* **[サービスの監視]** : 生成元の監視サービスに基づいてフィルター処理します。 これも複数選択できます。 たとえば、サービスの監視 = "Application Insights" は、"Application insights" ベース のすべてのアラートにアクション ルールが適用されることを意味します。
* **[リソースの種類]** : 特定のリソースの種類に基づいてフィルター処理します。 これも複数選択できます。 たとえば、リソースの種類 = "仮想マシン" は、すべての仮想マシンにアクション ルールが適用されることを意味します。
* **[Alert Rule ID]\(アラート ルール ID\)** : アラート ルールの Resource Manager ID を使用して、特定のアラート ルールをフィルター処理できます。
* **[Monitor Condition]\(監視条件\)** : 監視条件として、"発生済み" または "解決済み" のいずれかでアラートのインスタンスをフィルター処理します。
* **説明**:アラート ルールの一部として定義されている説明内での正規表現の一致です。
* **[Alert context (payload)]\( アラート コンテキスト (ペイロード)\)** : アラート インスタンスの[アラート コンテキスト](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) フィールド内での正規表現の一致です。

これらのフィルターは、相互に組み合わせて適用されます。 たとえば、[リソースの種類] = [仮想マシン] および [重大度] = [Sev0] と設定すると、VM だけで "Sev0" のアラートが抽出されます。 

![アクション ルール フィルター](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>抑制またはアクション グループの構成

次に、アラートの抑制またはアクション グループのサポートに対するアクション ルールを構成します。 両方を選択することはできません。 構成は、前に定義したスコープとフィルターに一致するすべてのアラートのインスタンスに対して機能します。

#### <a name="suppression"></a>抑制

**抑制**を選択した場合、アクションと通知の抑制の期間を構成します。 次のいずれかを選択します。
* **[From now (always)]\(今から (常に)\)** : すべての通知を無期限に抑制します。
* **[At a scheduled time]\(スケジュールされた時間\)** : 指定された期間だけ、通知を抑制します。
* **[With a recurrence]\(繰り返し\)** : 定期的なスケジュールで抑制します (毎日、毎週、または毎月)。

![アクション ルールの抑制](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>アクション グループ

トグルで **[Action group]\(アクション グループ\)** を選択した場合、既存のアクション グループを追加するか、新しいアクション グループを作成します。 

> [!NOTE]
> アクション ルールと関連付けることができるアクション グループは 1 つだけです。

![アクション ルールのアクション グループ](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>アクション ルールの詳細

最後に、アクション ルールに対して次の詳細を構成します
* Name
* それが保存されるリソース グループ
* 説明 

## <a name="example-scenarios"></a>シナリオ例

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>シナリオ 1:重大度に基づくアラートの抑制

Contoso では、毎週末、サブスクリプション "ContosoSub" 内のすべての VM で、すべての Sev4 アラートの通知を抑制します。

**解決策:** 次のアクション ルールを作成します
* スコープ = "ContosoSub"
* フィルター
    * 重大度 = "Sev4"
    * リソースの種類 = "仮想マシン"
* 繰り返しの抑制、毎週、"土曜日" と "日曜日" をオン

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>シナリオ 2:アラート コンテキスト (ペイロード) に基づくアラートの抑制

Contoso では、メンテナンスのため、"ContosoSub" 内の "Computer-01" に対して生成されるすべてのログ アラートに対する通知を無期限に抑制します。

**解決策:** 次のアクション ルールを作成します
* スコープ = "ContosoSub"
* フィルター
    * サービスの監視 = "Log Analytics"
    * アラート コンテキスト (ペイロード) に "Computer-01" が含まれる
* 抑制を [From now (always)]\(今から (常に)\) に設定

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>シナリオ 3:リソース グループで定義されているアクション グループ

Contoso では、[サブスクリプション レベルでメトリック アラート](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor)を定義しますが、リソース グループ "ContosoRG" については別にアラートをトリガーするアクションを定義します。

**解決策:** 次のアクション ルールを作成します
* スコープ = "ContosoRG"
* フィルターなし
* アクション グループを "ContosoActionGroup" に設定

> [!NOTE]
> **アクション ルールとアラート ルールで定義されているアクション グループは独立して動作し、重複は除去されません**。 上記のシナリオでは、アラート ルールに定義されたアクション グループがある場合、アクション ルールに定義されたアクション グループと連動してトリガーされます。 

## <a name="managing-your-action-rules"></a>アクション ルールの管理

次に示すリスト ビューから、アクション ルールを表示および管理できます。

![アクション ルールのリスト ビュー](media/alerts-action-rules/action-rules-list-view.png)

ここでは、横にあるチェック ボックスをオンにすることで、アクション ルールをまとめて有効化/無効化/削除できます。 アクション ルールをクリックするとその構成ページが開き、定義を更新したり、有効/無効にしたりできます。

## <a name="best-practices"></a>ベスト プラクティス

["結果の数"](alerts-unified-log.md) オプションで作成されたログ アラートでは、検索結果全体を使用して **1 つのアラート インスタンス**が生成されます (たとえば、複数のコンピューターが含まれる可能性があります)。 このシナリオでは、アクション ルールで "アラート コンテキスト (ペイロード)" フィルターが使用されている場合、一致する限りアラート インスタンスに対して作用します。 前に説明したシナリオ 2 では、生成されたログ アラートの検索結果に、"Computer-01" と "Computer-02" の両方が含まれる場合は、通知全体が抑制されます (つまり、"Computer-02" に対しても通知はまったく生成されません)。

![アクション ルールとログ アラート (結果の数)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

アクション ルールでログ アラートを最適に利用するには、["メトリック測定"](alerts-unified-log.md) オプションでログ アラートを作成することをお勧めします。 このオプションを使用すると、グループ フィールドの定義に基づいて個別のアラート インスタンスが生成されます。 シナリオ 2 では、"Computer-01" と "Computer-02" に対して個別のアラート インスタンスが生成されます。 シナリオで説明したアクション ルールでは、"Computer-01" に対する通知のみが抑制され、"Computer-02" に対する通知は引き続き通常どおり発生します。

![アクション ルールとログ アラート (結果の数)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>FAQ

* Q. アクション ルールを構成するとき、通知の重複を回避するため、重複する可能性のあるすべてのアクション ルールを見たいと思います。 これを行うことはできますか?

    A. アクション ルールを構成するときにスコープを定義すると、同じスコープで重複するアクション ルール (ある場合) の一覧を確認できます。 この重複は、次のいずれかのオプションの可能性があります。
    * 完全一致: たとえば、定義しているアクション ルールと重複するアクション ルールが、同じサブスクリプションに対するものです。
    * サブセット: たとえば、定義しているアクション ルールはサブスクリプションに対するもので、重複するアクション ルールはそのサブスクリプション内のリソース グループに対するものです。
    * スーパーセット: たとえば、定義しているアクション ルールはリソース グループに対するもので、重複するアクション ルールはそのリソース グループを含むサブスクリプションに対するものです。
    * 交差: たとえば、定義しているアクション ルールは "VM1" と "VM2" に対するもので、重複するアクション ルールは "VM2" と "VM3" に対するものです。

    ![アクション ルールの重複](media/alerts-action-rules/action-rules-overlapping.png)

* Q. アラート ルールを構成するとき、定義しているアラート ルールに作用する可能性のあるアクション ルールが既に定義されているかどうかを確認することはできますか?

    A. アラート ルールのターゲット リソースを定義した後、[Actions]\(アクション\) セクションの [View configured actions]\(構成済みアクションの表示\) をクリックすることで、同じスコープで作用するアクション ルールの一覧を確認できます (ある場合)。 この一覧は、スコープの次のシナリオに基づいて設定されます。
    * 完全一致: たとえば、定義しているアラート ルールとアクション ルールが、同じサブスクリプションに対するものです。
    * サブセット: たとえば、定義しているアラート ルールはサブスクリプションに対するもので、アクション ルールはそのサブスクリプション内のリソース グループに対するものです。
    * スーパーセット: たとえば、定義しているアラート ルールはリソース グループに対するもので、アクション ルールはそのリソース グループを含むサブスクリプションに対するものです。
    * 交差: たとえば、定義しているアラート ルールは "VM1" と "VM2" に対するもので、アクション ルールは "VM2" と "VM3" に対するものです。
    
    ![アクション ルールの重複](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

* Q. アクション ルールによって抑制されているアラートを表示できますか?

    A. [アラート一覧ページ](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances)には、[Suppression Status]\(抑制状態\) という名前の選択可能な追加列があります。 アラート インスタンスの通知が抑制されている場合、一覧にその状態が表示されます。

    ![抑制されたアラート インスタンス](media/alerts-action-rules/action-rules-suppressed-alerts.png)

* Q. アクション グループを含むアクション ルールと、同じスコープに対する抑制がアクティブな別のアクション ルールがある場合は、どうなりますか?

    A. **同じスコープに対しては抑制が常に優先されます**。

* Q. 2 つの個別のアクション ルールで監視されているリソースがあると、どうなりますか? 受け取る通知は 1 つですか、2 つですか? たとえば、次のシナリオでの "VM2" のような場合です。

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1'
      action rule 'AR2' defined for 'VM2' and 'VM3' with action group 'AG1'

    A. "VM1" と "VM3" のすべてのアラートについては、アクション グループ "AG1" が 1 回トリガーされます。 "VM2" のすべてのアラートについては、アクション グループ "AG1" が 2 回トリガーされます (**アクション ルールではアクションは重複除去されません**)。 

* Q. 2 つの個別のアクション ルールで監視されているリソースがあり、一方でアクションを呼び出し、もう一方で抑制すると、どうなりますか? たとえば、次のシナリオでの "VM2" のような場合です。

      action rule 'AR1' defined for 'VM1' and 'VM2' with action group 'AG1' 
      action rule 'AR2' defined for 'VM2' and 'VM3' with suppression

    A. "VM1" のすべてのアラートについては、アクション グループ "AG1" が 1 回トリガーされます。 "VM2" と "VM3" に対するすべてのアラートのアクションと通知は抑制されます。 

* Q. 同じリソースに対して、異なるアクション グループを呼び出すアラート ルールとアクション ルールが定義されていると、どうなりますか? たとえば、次のシナリオでの "VM1" のような場合です。

      alert rule  'rule1' on          'VM1' with action group 'AG2'
      action rule 'AR1'   defined for 'VM1' with action group 'AG1' 
 
    A. "VM1" のすべてのアラートについては、アクション グループ "AG1" が 1 回トリガーされます。 アラート ルール "rule1" がトリガーされるときは常に、"AG2" もさらにトリガーされます。 **アクション ルールとアラート ルールで定義されているアクション グループは独立して動作し、重複は除去されません**。 

## <a name="next-steps"></a>次の手順

- [Azure でのアラートについてさらに詳しく学習する](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
