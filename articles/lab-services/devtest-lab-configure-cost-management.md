---
title: Azure DevTest Labs で、ラボの月間推定コスト傾向を表示する | Microsoft Docs
description: Azure DevTest Labs の月間推定コスト傾向グラフについて説明します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 13535dae82ef2c8896dad7d6221553d15e4e6a95
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38573813"
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Azure DevTest Labs で、ラボの月間推定コスト傾向を表示する
DevTest ラボのコスト管理機能は、ラボのコストを追跡するのに役立ちます。 この記事では、 **月間推定コスト傾向** グラフを使用して、現在のカレンダー月の現時点までの推定コストおよび現在のカレンダー月の月末の予測コストを表示する方法を示します。 この記事では、支出の目標としきい値を設定し、それに達した場合に DevTest Labs をトリガーして結果をレポートすることで、ラボのコスト管理を改善する方法について説明します。

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>月間推定コスト傾向グラフを表示する
月間推定コスト傾向グラフを表示するには、次の手順に従います: 

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
1. 必要に応じて、**[All Services]\(その他のサービス\)** を選択し、一覧から **[DevTest Labs]** を選択します。 (お使いのラボは、**[すべてのリソース]** の [ダッシュボード] に既に表示されている場合があります)。
1. ラボの一覧で目的のラボを選択します。  
1. ラボの **[概要]** で、**[Configuration and policies]\(構成とポリシー\)** を選択します。   
1. 左側の **[コスト管理]** で **[コスト傾向]** を選択します。

   次のスクリーン ショットはコスト グラフの例を示しています。 
   
    ![コスト グラフ](./media/devtest-lab-configure-cost-management/graph.png)

**[推定コスト]** 値は現在のカレンダー月のこれまでの推定コストです。 **[予測コスト]** は現在のカレンダー月全体の推定コストで、過去 5 日間のラボのコストを使用して計算されます。

コストの金額の端数は切り上げられます。 例:  

* 5.01 は切り上げて 6 になります。 
* 5.50 は切り上げて 6 になります。
* 5.99 は切り上げて 6 になります。

グラフの上に示されているとおり、グラフに既定で表示されるコストは、[従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)プラン料金を使用した*推定*コストです。 [ラボのコスト目標を管理する](#managing-cost-targets-for-your-lab)ことで、グラフに表示する独自の支出目標を設定することもできます。

また、コスト計算には次のものは含まれて *いません* 。

* CSP サブスクリプションと Dreamspark サブスクリプションは現在サポートされません。Azure DevTest Labs は、[Azure 課金 API](../billing/billing-usage-rate-card-overview.md) を使用してラボ コストを計算しますが、CSP サブスクリプションも Dreamspark サブスクリプションもサポートしていないためです。
* プラン料金。 現時点では、Microsoft または Microsoft パートナーと交渉したオファー料金 (サブスクリプションの下に表示される金額) は使用できません。 従量課金制の料金のみが使用されます。
* 税金
* 割引
* 請求通貨。 現時点では、ラボのコストは USD 通貨でのみ表示されます。

## <a name="managing-cost-targets-for-your-lab"></a>ラボのコスト目標を管理する
DevTest Labs を使用して支出目標を設定することで、コストの管理を改善できます。設定した目標は、月間推定コスト傾向グラフに表示されます。 また、DevTest Labs では、指定した目標の支出やしきい値に達したときに通知を送信することもできます。 

1. ラボの **[概要]** ウィンドウで、**[構成とポリシー]** を選択します。
1. 左側の **[コスト管理]** で **[コスト傾向]** を選択します。

    ![[ターゲットの管理] ボタン](./media/devtest-lab-configure-cost-management/cost-trend.png)

1. **[コスト傾向]** ウィンドウで **[ターゲットの管理]** ボタンを選択します。

    ![[ターゲットの管理] ボタン](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)

1. [ターゲットの管理] ウィンドウで、目的の支出目標としきい値を指定します。 選択した各しきい値を、コスト傾向グラフや webhook 通知でレポートするかどうかも設定できます。

    ![[ターゲットの管理] ウィンドウ](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - コスト目標を追跡する期間を選択します。
      - **毎月**: コスト目標は月 1 回追跡されます。
      - **固定**: コスト目標は、[開始日] フィールドと [終了日] フィールドで指定した日付範囲の間追跡されます。 通常、この期間はプロジェクトの実行予定期間に一致させます。
   - **目標コスト**を指定します。 たとえば、このラボで定義した期間に支出が予定されている金額を指定します。
   - レポートされるしきい値を有効にするか無効にするかを選択します。指定した**目標コスト**の最大 125% まで、25% 刻みで増やして設定できます。
      - **通知**: しきい値に達すると、指定した webhook URL により通知されます。
      - **グラフ上のプロット**: しきい値に達すると、「[月間推定コスト傾向グラフを表示する](#viewing-the-monthly-estimated-cost-trend-chart)」での説明のとおり、結果がコスト傾向グラフ上にプロットされ、確認できるようになります。
   - しきい値に達したときに**通知**されるように選択した場合、webhook の URL を指定する必要があります。 [コストの統合] 領域で、**[ここをクリックして統合を追加します]** をクリックします。

      [通知の構成] ウィンドウで webhook の URL を入力し、**[OK]** を選択します。

       ![[通知の構成] ウィンドウ](./media/devtest-lab-configure-cost-management/configure-notification.png)

      - **[通知]** を指定した場合、webhook の URL を設定する必要があります。
      - 同様に、webhook の URL を設定した場合は、[コストのしきい値] ウィンドウで **[通知]** を **[オン]** に設定する必要があります。
      - このウィンドウで入力する前に、webhook を作成しておく必要があります。  

      webhook の詳細については、「[webhook または API Azure Function を作成する](../azure-functions/functions-create-a-web-hook-or-api-function.md)」を参照してください。 
 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>関連するブログ記事
* [Two more things to keep your cost on track in DevTest Labs (DevTest Labs でコストを正常に保つためのさらに 2 つのこと)](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Why Cost Thresholds? (なぜコストのしきい値か?)](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>次の手順
次に試す操作をいくつか以下に示します。

* [ラボ ポリシーを定義する](devtest-lab-set-lab-policy.md) - ラボとその VM の使用方法を制御するために使用されるさまざまなポリシーを設定する方法を確認します。 
* [カスタム イメージを作成する](devtest-lab-create-template.md) - VM を作成する場合は、ベースとしてカスタム イメージまたは Marketplace イメージを指定します。 この記事では、VHD ファイルからカスタム イメージを作成する方法について説明します。
* [Marketplace イメージを構成する](devtest-lab-configure-marketplace-images.md) - DevTest Labs では、Azure Marketplace イメージを基にした VM を作成することができます。 この記事では、ラボで VM を作成する際に使用できるようにする Azure Marketplace イメージ (該当するものがある場合) を指定する方法について説明します。
* [ラボで VM を作成する](devtest-lab-add-vm.md) - 基本イメージ (カスタムまたは Marketplace) から VM を作成する方法と、VM でアーティファクトを操作する方法について説明します。

