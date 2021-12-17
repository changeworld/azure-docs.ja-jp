---
title: Microsoft Sentinel のユーザー/エンティティ行動分析 (UEBA) を使用して高度な脅威を特定する | Microsoft Docs
description: エンティティ (ユーザー、ホスト名、IP アドレス) の行動ベースラインを作成し、それを使用して異常な行動を検出し、ゼロディの高度な永続的脅威 (APT) を特定します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: c6abd0f799f3f8f2f457352de3a23bdd2ecbd9de
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721083"
---
# <a name="identify-advanced-threats-with-user-and-entity-behavior-analytics-ueba-in-microsoft-sentinel"></a>Microsoft Sentinel のユーザー/エンティティ行動分析 (UEBA) を使用して高度な脅威を特定する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - UEBA およびエンティティ ページ機能は、Microsoft Sentinel の "**_すべて_**" の地域とリージョンで **一般提供** になりました。 
>
> - **IP アドレス エンティティ** は現在 **プレビュー** の段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="what-is-user-and-entity-behavior-analytics-ueba"></a>ユーザーとエンティティの行動分析 (UEBA) の概要

組織内の脅威とその潜在的な影響を特定することは (それが侵害されたエンティティ、または悪意のある内部関係者かどうかには関係なく) 常に、時間のかかる、労働集約的なプロセスでした。 アラートの選別、ドットの接続、積極的なハンティングがすべて一緒になって膨大な量の時間と労力が費やされますが、最小限の結果しか得られず、高度な脅威の可能性は簡単に検出を回避します。 特に、対象を絞った、ゼロディの高度な永続的脅威などの捕らえにくい脅威は、組織にとって最も危険なものになることがあるため、それらの検出がますます重要になっています。

Microsoft Sentinel の UEBA 機能は、アナリストのワークロードから面倒な作業を、またその取り組みから不確実性を排除し、忠実度が高い、すぐに使用可能なインテリジェンスを提供するため、アナリストは調査と修復に集中することができます。

Microsoft Sentinel では、接続されているすべてのデータ ソースからログとアラートを収集すると、それらを分析して、時間とピア グループの期間全体にわたる組織のエンティティ (ユーザー、ホスト、IP アドレス、アプリケーションなど) のベースライン行動プロファイルを構築します。 さまざまな手法や機械学習機能を使用して、Microsoft Sentinel で異常なアクティビティを特定でき、資産が侵害されているかどうかを判定するのに役立ちます。 それだけではなく、特定の資産の相対的な機密性を見つけたり、資産のピア グループを識別したり、特定の侵害された資産の潜在的な影響 (その "影響範囲") を評価したりすることもできます。 これらの情報を利用して、調査やインシデント処理に効果的に優先順位を付けることができます。 

### <a name="ueba-analytics-architecture"></a>UEBA 分析アーキテクチャ

:::image type="content" source="media/identify-threats-with-entity-behavior-analytics/entity-behavior-analytics-architecture.png" alt-text="エンティティの行動分析のアーキテクチャ":::

### <a name="security-driven-analytics"></a>セキュリティ主導の分析

UEBA ソリューションに対する Gartner のパラダイムから着想を得て、Microsoft Sentinel では、次の 3 つの参照フレームに基づいた "アウトサイドイン" アプローチが提供されます。

- **ユース ケース:** さまざまなエンティティを被害者、加害者、またはピボット ポイントとしてキル チェーン内に配置する、戦術、テクニック、サブテクニックの MITRE ATT&CK フレームワークに対応したセキュリティ調査に基づいて、関連する攻撃ベクトルとシナリオに優先順位を付けることにより、Microsoft Sentinel は特に、各データ ソースが提供できる最も重要なログに焦点を置いています。

- **データ ソース:** まず何よりも Azure データ ソースをサポートしていますが、Microsoft Sentinel では、脅威シナリオに一致するデータを提供するためにサードパーティのデータ ソースを慎重に選択します。

- **分析:** さまざまな機械学習 (ML) アルゴリズムを使用して、Microsoft Sentinel では、異常なアクティビティを特定し、コンテキスト エンリッチメントの形式で明確かつ簡潔に証拠を提供します。そのいくつかの例を次に示します。

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/behavior-analytics-top-down.png" alt-text="行動分析のアウトサイドイン アプローチ":::

Microsoft Sentinel を利用すると、セキュリティ アナリストは、ユーザーのベースライン プロファイルのコンテキストで、またそれとの比較で異常なアクティビティを明確に理解するために役立つ成果物を取得できます。 ユーザー (またはホスト、またはアドレス) によって実行されたアクションは、次のようにコンテキストから評価されます。ここで、"true" の結果は特定された異常を示します。
- 地理的な場所、デバイス、環境にまたがって。
- 時間と頻度の期間にまたがって (ユーザー独自の履歴と比較して)。
- ピアの行動と比較して。
- 組織の行動と比較して。

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/context.png" alt-text="エンティティのコンテキスト":::


### <a name="scoring"></a>ポイントの計算

各アクティビティは、"調査の優先順位のスコア" でスコア付けされます。これにより、ユーザーとそのピアの行動学習に基づいて、特定のユーザーが特定のアクティビティを実行する確率が決定されます。 最も異常であると識別されたアクティビティが最高のスコアを受け取ります (スケールは 0 ～ 10)。

この動作の例については、[Microsoft Defender for Cloud Apps](https://techcommunity.microsoft.com/t5/microsoft-security-and/prioritize-user-investigations-in-cloud-app-security/ba-p/700136) で行動分析がどのように使用されているかを参照してください。

## <a name="entity-pages"></a>エンティティ ページ

[Microsoft Sentinel のエンティティ](entities.md)に関する詳細と、[サポートされているエンティティと識別子](entities-reference.md)の完全な一覧をご覧ください。

エンティティ検索、アラート、または調査でユーザーまたはホスト エンティティ (IP アドレス エンティティはプレビュー段階です) を検出した場合は、そのエンティティを選択して **エンティティ ページ** に移動できます。これは、そのエンティティに関する役立つ情報が豊富に含まれているデータシートです。 このページで見つけることができる情報の種類には、そのエンティティについての基本的な事実、このエンティティに関連した注目すべきイベントのタイムライン、そのエンティティの行動に関する分析情報が含まれます。
 
エンティティ ページは、次の 3 つの部分で構成されます。
- 左側のパネルには、Azure Active Directory、Azure Monitor、Microsoft Defender for Cloud、CEF/Syslog、Microsoft 365 Defender などのデータ ソースから収集された、そのエンティティの識別情報が含まれます。

- 中央のパネルには、そのエンティティに関連した注目すべきイベント (アラート、ブックマーク、アクティビティなど) のグラフとテキストの両方のタイムラインが表示されます。 アクティビティは、Log Analytics からの注目すべきイベントの集計です。 これらのアクティビティを検出するクエリは、Microsoft のセキュリティリ調査チームによって開発されており、[独自のカスタムクエリを追加して、任意のアクティビティを検出できる](customize-entity-activities.md)ようになりました。 

- 右側のパネルには、エンティティに関する行動分析情報が表示されます。 これらの分析情報は、異常やセキュリティの脅威をすばやく特定するために役立ちます。 この分析情報は Microsoft のセキュリティ調査チームによって開発され、異常検出モデルに基づいています。

> [!NOTE]
> **IP アドレス エンティティ ページ** (現在はプレビュー段階にあります) には、**Microsoft の脅威インテリジェンス サービス** によって提供される **位置情報データ** が含まれています。 このサービスは、Microsoft ソリューションとサードパーティのベンダーやパートナーが提供する位置情報データを組み合わせたものです。 このデータは、セキュリティ インシデントのコンテキストで分析や調査に利用できます。 詳細については、「[Microsoft Sentinel において REST API を使用して位置情報データでエンティティをエンリッチする (パブリック プレビュー)](geolocation-data-api.md)」も参照してください。

### <a name="the-timeline"></a>タイムライン

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-timeline.png" alt-text="エンティティ ページのタイムライン":::

タイムラインは、エンティティ ページが Microsoft Sentinel の行動分析に寄与している内容の主要な部分です。 ここには、エンティティ関連のイベントに関する項目が表示されるため、特定の期間内のエンティティのアクティビティを理解するのに役立ちます。

事前に設定されたいくつかのオプション ( *[過去 24 時間]* など) の中から **[時間範囲]** を選択するか、またはそれをカスタム定義の期間に設定できます。 さらに、タイムライン内の情報を特定の種類のイベントまたはアラートに制限するフィルターを設定できます。

タイムラインには、次の種類の項目が含まれています。

- アラート - そのエンティティが **[マップされたエンティティ]** として定義されているすべてのアラート。 組織で[分析ルールを使用したカスタム アラート](./detect-threats-custom.md)が作成されている場合は、ルールのエンティティ マッピングが正しく実行されていることを確認する必要がある点に注意してください。

- ブックマーク - ページにその特定のエンティティが表示されているすべてのブックマーク。

- アクティビティ - そのエンティティに関連した注目すべきイベントの集計。 さまざまなアクティビティが自動的に収集され、[任意のアクティビティを追加することで、このセクションをカスタマイズできる](customize-entity-activities.md)ようになりました。

### <a name="entity-insights"></a>エンティティ分析情報
 
エンティティ分析情報は、アナリストがより効率的かつ効果的に調査できるようにするために Microsoft のセキュリティ研究者によって定義されたクエリです。 この分析情報はエンティティ ページの一部として表示され、ホストとユーザーに関する重要なセキュリティ情報を表形式データとグラフの両方の形式で提供します。 ここに情報が表示されるため、Log Analytics に迂回する必要はありません。 この分析情報には、サインイン、グループの追加、異常なイベントなどに関連したデータや、異常な行動を検出するための高度な ML アルゴリズムが含まれています。 

この分析情報は、次のデータ ソースに基づいています。
- Syslog (Linux)
- SecurityEvent (Windows)
- AuditLogs (Azure AD)
- SigninLogs (Azure AD)
- OfficeActivity (Office 365)
- BehaviorAnalytics (Microsoft Sentinel UEBA)
- Heartbeat (Azure Monitor Agent)
- CommonSecurityLog (Microsoft Sentinel)
- ThreatIntelligenceIndicators (Microsoft Sentinel)

### <a name="how-to-use-entity-pages"></a>エンティティ ページを使用する方法

エンティティ ページは、複数の使用シナリオの一部になるように設計されており、インシデント管理、調査グラフ、ブックマークから、または Microsoft Sentinel のメイン メニューの **[エンティティの行動分析]** の下のエンティティ検索ページから直接アクセスできます。

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/entity-pages-use-cases.png" alt-text="エンティティ ページのユース ケース":::

エンティティ ページの情報は、「[Microsoft Sentinel UEBA エンリッチメント リファレンス](ueba-enrichments.md)」で詳細に説明されているように、**BehaviorAnalytics** テーブルに格納されます。

## <a name="querying-behavior-analytics-data"></a>行動分析データへのクエリ実行

[KQL](/azure/data-explorer/kusto/query/) を使用すると、行動分析テーブルにクエリを実行できます。

たとえば、Azure リソースへのサインインに失敗したユーザーのすべてのケースを見つけたい場合 (それがそのユーザーの特定の国からの最初の接続の試みであり、そのユーザーのピアに対してもその国からの接続はまれである場合) は、次のクエリを使用できます。

```Kusto
BehaviorAnalytics
| where ActivityType == "FailedLogOn"
| where FirstTimeUserConnectedFromCountry == True
| where CountryUncommonlyConnectedFromAmongPeers == True
```

### <a name="user-peers-metadata---table-and-notebook"></a>ユーザー ピア メタデータ - テーブルとノートブック

ユーザー ピア メタデータは、脅威の検出、インシデントの調査、潜在的な脅威のハンティングにおける重要なコンテキストを提供します。 セキュリティ アナリストは、あるユーザーのピアの通常のアクティビティを観察して、そのユーザーのアクティビティがピアのアクティビティと比較して異常であるかどうかを判定できます。

Microsoft Sentinel では、ユーザーの Azure AD セキュリティ グループ メンバーシップ、メーリング リストなどに基づいて、そのユーザーのピアを計算してランク付けし、1 から 20 にランク付けされたピアを **UserPeerAnalytics** テーブルに格納します。 次のスクリーンショットは UserPeerAnalytics テーブルのスキーマを示し、ユーザー Kendall Collins の上位 8 つのランク付けされたピアを表示しています。 Microsoft Sentinel では、"*用語の出現頻度/逆文書頻度*" (TF-IDF) アルゴリズムを使用して、ランク付けを計算するための重みを正規化します。グループが小さいほど、重みが大きくなります。 

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-peers-metadata.png" alt-text="ユーザー ピア メタデータ テーブルのスクリーンショット":::

Microsoft Sentinel GitHub リポジトリで提供されている [Jupyter Notebook](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) を使用して、ユーザー ピア メタデータを視覚化できます。 このノートブックを使用する方法の詳細な手順については、[ガイド付き分析 - ユーザー セキュリティ メタデータ](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb)のノートブックを参照してください。

### <a name="permission-analytics---table-and-notebook"></a>アクセス許可分析 - テーブルとノートブック

アクセス許可分析は、攻撃者による組織資産の侵害の潜在的な影響を特定するために役立ちます。 この影響は、資産の "影響範囲" とも呼ばれます。 セキュリティ アナリストは、この情報を使用して、調査やインシデント処理に優先順位を付けることができます。

Microsoft Sentinel では、ユーザーが直接、またはグループやサービス プリンシパル経由でアクセスできる Azure サブスクリプションを評価することで、特定のユーザーが Azure リソースに対して保持する直接および推移的なアクセス権を特定します。 この情報や、そのユーザーの Azure AD セキュリティ グループ メンバーシップの完全な一覧は、その後 **UserAccessAnalytics** テーブルに格納されます。 次のスクリーンショットは、ユーザー Alex Johnson に関する UserAccessAnalytics テーブル内のサンプル行を示しています。 **[ソース エンティティ]** はユーザーまたはサービス プリンシパル アカウントであり、 **[ターゲット エンティティ]** はソース エンティティがアクセスできるリソースです。 **[アクセス レベル]** と **[アクセスの種類]** の値は、ターゲット エンティティのアクセス制御モデルによって異なります。 Alex が Azure サブスクリプション *Contoso Hotels Tenant* に対する共同作成者のアクセス権を持っていることがわかります。 このサブスクリプションのアクセス制御モデルは Azure RBAC です。

:::image type="content" source="./media/identify-threats-with-entity-behavior-analytics/user-access-analytics.png" alt-text="ユーザー アクセス分析テーブルのスクリーンショット":::

Microsoft Sentinel GitHub リポジトリの [Jupyter Notebook](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/BehaviorAnalytics/UserSecurityMetadata) (前に説明したものと同じノートブック) を使用して、アクセス許可分析データを視覚化できます。 このノートブックを使用する方法の詳細な手順については、[ガイド付き分析 - ユーザー セキュリティ メタデータ](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/BehaviorAnalytics/UserSecurityMetadata/Guided%20Analysis%20-%20User%20Security%20Metadata.ipynb)のノートブックを参照してください。

### <a name="hunting-queries-and-exploration-queries"></a>ハンティング クエリと探索クエリ

Microsoft Sentinel には、**BehaviorAnalytics** テーブルに基づいた、ハンティング クエリ、探索クエリ、および **ユーザー/エンティティ行動分析** ブックのすぐに使えるセットが用意されています。 これらのツールは、異常な行動を示す、特定のユース ケースに焦点を絞ったエンリッチ処理されたデータを提供します。

詳細については次を参照してください:

- [Microsoft Sentinel を使用して脅威を追求する](hunting.md)
- [ データの視覚化と監視](monitor-your-data.md)

従来の防御ツールが古いものになるにつれて、膨大な数のデジタル資産を所有している組織は、組織の環境が直面している可能性のあるリスクと体制の全体像を把握することが困難になります。 分析やルールなどの事後対応型の取り組みに依存しすぎると、悪意のあるアクターがそれらの取り組みを回避する方法を学んでしまいます。 ここが UEBA の出番です。リスクのスコアリング方法とアルゴリズムにより、実際に何が起こっているのかを把握できます。


## <a name="next-steps"></a>次のステップ
このドキュメントでは、Microsoft Sentinel のエンティティの行動分析機能について学習しました。 実装や、取得した分析情報を使用する方法に関する実用的なガイダンスについては、次の記事を参照してください。

- Microsoft Sentinel で[エンティティの行動分析を有効にする](./enable-entity-behavior-analytics.md)。
- [UEBA データを使用してインシデントを調査する](investigate-with-ueba.md)。
- [セキュリティの脅威を検出する](./hunting.md)。

詳細については、「[Microsoft Sentinel UEBA エンリッチメント リファレンス](ueba-enrichments.md)」もご覧ください。
