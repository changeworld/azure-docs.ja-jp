---
title: Azure Lab Services のコスト管理ガイド
description: Lab Services のコストを表示するさまざまな方法について説明します。
author: rbest
ms.author: rbest
ms.date: 06/26/2020
ms.topic: article
ms.openlocfilehash: fbbaf4a3646260fc09467e214b82fd0213415635
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445306"
---
# <a name="cost-management-for-azure-lab-services"></a>Azure Lab Services のコスト管理

コスト管理は、コストの見積もりとコストの分析という 2 つの異なる領域に分類できます。  コストの見積もりは、ラボを設定するときに、ラボの初期構造を予想される予算内に収めるために行われます。  コストの分析は、コストを分析して、翌月の必要なアクションを決定するために、通常、月末に行われます。

## <a name="estimating-the-lab-costs"></a>ラボのコストの見積もり

各ラボ ダッシュボードには、**コストと課金**のセクションがあり、その月のラボのコストの概算見積りが提示されます。  コストの見積もりでは、最大ユーザー数での時間の使用量が、時間ごとの推定コストで要約されています。  最も正確な見積もりを取得するには、[スケジュール](how-to-create-schedules.md)を含めてラボを設定すると、ダッシュボードに推定コストが反映されます。  

この見積もりは、発生しうるすべてのコストではない可能性があり、含まれていないいくつかのリソースがあります。  テンプレートの準備コストは、コストの見積もりには含まれていません。  これは、テンプレートの作成に必要な時間によって大幅に変わる可能性があります。 テンプレートを実行するコストは、1 時間あたりの全体的なラボ コストと同じです。 ギャラリーは複数のラボ間で共有される可能性があるため、[Shared Image Gallery](how-to-use-shared-image-gallery.md) のコストはラボ ダッシュボードには含まれていません。  最後に、ラボの作成者がマシンを起動したときに発生した時間は、この見積もりから除外されます。

> [!div class="mx-imgBorder"]
> ![ダッシュボードのコスト見積もり](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyzing-previous-months-usage"></a>以前の月の使用状況の分析

コストの分析は、以前の月の使用状況を確認して、ラボの調整の決定に役立てるために行われます。  過去のコストの内訳については、[サブスクリプションのコスト分析](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis)に関するページを参照してください。  Azure portal では、上部の検索フィールドに「サブスクリプション」と入力してから、[サブスクリプション] オプションを選択します。  

> [!div class="mx-imgBorder"]
> ![サブスクリプションの検索](./media/cost-management-guide/subscription-search.png)

確認する特定のサブスクリプションを選択します。

> [!div class="mx-imgBorder"]
> ![サブスクリプションの選択](./media/cost-management-guide/subscription-select.png)

 左側のペインで **[Cost Management]** の下にある [コスト分析] を選択します。

 > [!div class="mx-imgBorder"]
> ![サブスクリプションのコスト分析](./media/cost-management-guide/subscription-cost-analysis.png)

このダッシュボードでは、スケジュールに基づいてさまざまな種類のファイルにエクスポートする機能など、詳細なコスト分析を行うことができます。  Cost Management にはさまざまな機能があります。詳細については、[Cost Management の課金の概要](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)に関するページを参照してください。

リソースの種類によるフィルター処理: `microsoft.labservices/labaccounts` には、Lab Services に関連付けられているコストのみが表示されます。

## <a name="understanding-the-usage"></a>使用状況について

コスト分析のサンプルを次に示します。

> [!div class="mx-imgBorder"]
> ![サブスクリプションのコスト分析](./media/cost-management-guide/cost-analysis.png)

既定で、リソース、リソースの種類、場所、リソース グループ名、タグ、コストという 6 つの列があります。  リソース列には、ラボ アカウント、ラボ名、および VM に関する情報が含まれています。  ラボ アカウント/ラボ名/既定値を含む行は、ラボのコストです。これは、2 行目と 3 行目で確認できます。  使用されている VM には、ラボ アカウント/ラボ名/既定値/VM 名の下にコストがあります。  この例では、両方とも "aaalab / dockerlab" で始まる最初の行と 2 番目の行を足すと、"aaalab" ラボ アカウント内のラボ "dockerlab" の総コストが得られます。

Shared Image Gallery 情報を取得するには、リソースの種類を `Microsoft.Compute/Galleries` に変更します。これにより、イメージ ギャラリーの全体的なコストが得られます。  ギャラリーが格納されている場所によっては、Shared Image Gallery はコストに表示されない可能性があります。

> [!NOTE]
> Shared Image Gallery はラボ アカウントに接続されています。  つまり、複数のラボで同じイメージを使用できます。

## <a name="separating-costs"></a>コストの分離

一部の大学では、さまざまなクラスを分離する方法としてラボ アカウントとリソース グループを使用してきました。  各クラスには、独自のラボ アカウントとリソース グループがあります。 [コスト分析] ペインで、クラスの適切なリソース グループ名を使用してリソース グループ名に基づくフィルターを追加すると、そのクラスのコストのみが表示されます。  これにより、コストを表示するときにさまざまなクラス間のより明確な描写が可能になります。  コスト分析の[スケジュールされたエクスポート](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data)機能を使用すると、各クラスのコストを別々のファイルとしてダウンロードできます。

## <a name="managing-costs"></a>コストの管理

クラスの種類によっては、コストを管理して、マシンを使用している学生がいない状態で VM が実行されることを減らす方法があります。

### <a name="auto-shutdown-on-disconnect"></a>切断時に自動シャットダウン

ラボの作成時、ラボの所有者は、ラボの VM が、[VM への RDP 接続が切断されたときにシャットダウン](how-to-enable-shutdown-disconnect.md)されるように設定できます。  この設定により、学生が接続を切断したときに VM を停止するのを忘れるというシナリオが低減されます。

### <a name="quota-vs-scheduled-time"></a>クォータとスケジュールされた時間

[クォータ時間](classroom-labs-concepts.md#quota)と[スケジュールされた時間](classroom-labs-concepts.md#schedules)の違いを理解すると、ラボの所有者は、教授と学生のニーズにより適合するようにラボを構成することができます。  スケジュールされた時間とは、すべての学生の VM が開始されて接続可能になる、設定された時間のことです。  一般的に、スケジュールされた時間は、授業時間のように、1 日のうちの設定された時間に、すべての学生が自分の VM を使用し、教授の指示に従っている状況で使用されます。  欠点は、VM にログインしていない学生がいても、すべての学生の VM が起動され、コストが発生することです。  クォータ時間とは、各学生に割り当てられる時間で、これは各自の判断で使用でき、多くの場合、自主的な研究に使用されます。 VM は、学生が VM を起動するまで起動されません。  

ラボは、クォータ時間、スケジュールされた時間、またはその両方を組み合わせて使用できます。 クラスにスケジュールされた時間が必要ない場合は、VM を最も効果的に使用するために、クォータ時間のみを使用します。

### <a name="scheduled-event---stop-only"></a>スケジュール化されたイベント - 停止のみ

[スケジュール] で、特定の時刻にすべてのマシンを停止する「停止のみ」のイベントの種類を追加できます。  一部のラボ所有者は、毎日午前 0 時に「停止のみ」のイベントを設定して、学生が自分が使用している VM のシャットダウンを忘れたときのコストとクォータ使用量を削減しています。  この種類のイベントの欠点は、学生が VM を使用している場合でも、すべての VM がシャットダウンされることです。

### <a name="other-costs-related-to-labs"></a>ラボに関連するその他のコスト 

Lab Services には含まれないが、ラボ サービスに関連付けることができるコストがあります。  Shared Image Gallery はラボに接続できますが Lab Services のコストの下には表示されず、コストはかかります。  イメージには継承ストレージ コストがあるため、全体的なコストを抑えるためには、使用されていないイメージをすべてギャラリーから削除する必要があります。  ラボは、仮想ネットワーク (VNet) によって他の Azure リソースに接続できます。ラボが削除された場合は、VNet および他のリソースを削除する必要があります。

## <a name="conclusion"></a>まとめ

上記の情報を使用することで、使用コストについて、および提供されているツールを使用して余分なコストを削減する方法について、より深く理解することができます。
