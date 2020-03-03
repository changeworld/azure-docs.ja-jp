---
title: Azure Cost Management での一般的なコスト分析の使用
description: この記事では、Azure Cost Management で一般的なコスト分析タスクの結果を取得する方法について説明します。
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/21/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 1d9a498185e76f9771ec98656b1cad914b0f3214
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562583"
---
# <a name="common-cost-analysis-uses"></a>一般的なコスト分析の使用

多くの場合、Azure Cost Management のユーザーは、他の多くのユーザーが持つ疑問に対する答えを必要としています。 この記事では、Cost Management での一般的なコスト分析タスクの結果を取得する手順について説明します。

## <a name="view-cost-breakdown-by-azure-service"></a>Azure サービス別のコストの内訳を表示する

Azure サービス別にコストを表示すると、インフラストラクチャで最もコストがかかっている部分をよりよく把握するのに役立ちます。 たとえば、VM のコンピューティング コストは小さい場合があります。 しかし、VM から生成される情報の量によって、かなりのネットワーク コストが生じる可能性があります。 必要に応じてサービスの使用を調整できるように、Azure サービスの主要なコスト要因を把握することが重要です。

1. Azure portal で、スコープの [コスト分析] に移動します。 次に例を示します。 **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** 。
1. **[サービスごとのコスト]** を選択し、 **[サービス レベル]** でグループ化します。
1. ビューを **[テーブル]** に変更します。

![Azure サービス別のコストの内訳](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>Azure リソース別のコストの内訳を表示する

サービスは、Azure リソースを使用して構築されています。 リソースに基づいてコストを確認すると、主要なコスト要因をすばやく特定するのに役立ちます。 サービスに高コストのリソースがある場合は、コストを削減するための変更を検討してください。

1. Azure portal で、スコープの [コスト分析] に移動します。 次に例を示します。 **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** 。
1. **[リソースごとのコスト]** を選択します。
1. ビューを **[テーブル]** に変更します。

![Azure リソース別のコストの内訳を表示する](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>選択したディメンション別のコストの内訳を表示する

ディメンションを使用すると、料金に表示されるさまざまなメタデータ値に基づいてコストを整理できます。 たとえば、場所別にコストをグループ化することができます。

1. Azure portal で、スコープの [コスト分析] に移動します。 次に例を示します。 **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** 。
1. **[Group by]\(グループ化\)** フィルターを選択します。  
    ![グループ化項目を選択する](./media/cost-analysis-common-uses/group-by.png)
1. 必要に応じて、後で使用するためにビューを保存します。
1. グラフの下にある円グラフをクリックすると、より詳細なデータが表示されます。  
    ![選択したディメンション別のコストの内訳を表示する](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>1 日あたりまたは月別のコストを表示する

日単位または月単位のコストを確認すると、1 週間または 1 年間のうちでコストが高くなる時期があるかどうかをよりよく把握できます。 休日期間により多くの顧客トラフィックがある場合、それに対応して Azure のコストが増加していますか。 金曜日は月曜日よりもコストがかかりますか。

1. Azure portal で、スコープの [コスト分析] に移動します。 次に例を示します。 **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** 。
1. **[細分性]** を **[Monthly]\(毎月\)** または **[Daily]\(毎日\)** に設定します。

![1 日あたりのコストを表示する](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>特定のタグのコストを表示する

多くの Azure ユーザーは、料金をより適切に分類するために、コスト センターや開発環境 (運用およびテスト) などのリソースにタグを適用します。 コスト分析では、タグはディメンションとして表示されます。 ディメンションを使用すると、カスタムのタグ付けの分類に関する洞察を得ることができます。

タグのサポートは、タグがリソースに適用された "*後*" にレポートされた使用状況に適用されます。 タグは、過去のコスト データには適用されません。

1. Azure portal で、スコープの [コスト分析] に移動します。 次に例を示します。 **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** 。
1. タグに対して **[Group by]\(グループ化\)** を選択します。

![特定のタグのコストを表示する](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>使用量の詳細のダウンロード

使用状況の詳細レポート ファイル (CSV 形式) には、請求書に対して計上されたすべての料金の内訳が記載されています。 このレポートを使用して、請求書と比較して理解を深めることができます。 請求書に記載されている各請求料金は、使用状況レポートの料金の内訳に対応しています。

1. Azure portal で、課金アカウントまたはサブスクリプションの **[使用状況と料金]** タブに移動します。 次に例を示します。 **[コストの管理と請求]**  >  **[請求]**  >  **[使用状況と料金]** 。
1. ダウンロード元の行項目を選択し、ダウンロード記号をクリックします。  
    ![使用状況と料金をダウンロードする](./media/cost-analysis-common-uses/download1.png)
1.  ダウンロードする使用状況ファイルを選択します。  
    ![ダウンロードする使用状況ファイルを選択する](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>毎月の EA コストの内訳を表示する

EA 加入契約では、組織全体のコストが計上されます。 コストがどのように計上され、一定期間にわたって請求されるかを把握すると、適切な利害関係者と協力して、コストが責任を持って管理されるようにするのに役立ちます。

コストは、アクティブな加入契約に対してのみ表示されます。 加入契約 (非アクティブ) を新しいもの (アクティブ) に転送した場合、以前の加入契約のコストは Cost Management に表示されません。


1. Azure portal で、 **[コスト管理と請求]**  >  **[概要]** に移動します。
1. 今月の **[内訳]** をクリックし、年額コミットメントのバーンダウンを表示します。  
    ![EA コストの概要 - 内訳の概要](./media/cost-analysis-common-uses/breakdown1.png)
1.  **[使用状況と料金]** タブをクリックし、選択した期間の前月の内訳を表示します。  
    ![[使用状況と料金] タブ](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>期間別の加入契約の月間コストを表示する

特定の期間のコスト傾向と請求金額を把握するには、加入契約の月額コストのグラフィカル ビューを使用します。

1. Azure portal で、スコープの [コスト分析] に移動します。 次に例を示します。 **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** 。
1. 加入契約を選択し、加入契約の期間を設定します。
1. 細分性を月単位に設定し、ビューを **[縦棒     (積み上げ)]** に設定します。

より詳細に分析するには、データをグループ化し、フィルターに掛けることができます。

![期間別の毎月の加入契約コスト](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>EA 加入契約の累積コスト

一定期間にわたる正味累計料金を表示して、指定した期間の組織の総支出を把握します。

1. Azure portal で、スコープの [コスト分析] に移動します。 次に例を示します。 **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** 。
1. 加入契約を選択し、現在の累積コストを表示します。

![加入契約の累積コスト](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>次のステップ
- Cost Management の最初のクイック スタートをまだ完了していない場合は、[コスト分析の開始](quick-acm-cost-analysis.md)に関する記事をご覧ください。
- [Cost Management のドキュメント](../index.yml)をお読みください。
