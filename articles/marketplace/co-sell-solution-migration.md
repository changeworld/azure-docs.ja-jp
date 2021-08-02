---
title: OCP GTM から Microsoft AppSource のパートナー センターへ共同販売ソリューションを移行する
description: OCP GTM から Microsoft AppSource のパートナー センターへ共同販売ソリューションを移行する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.reviewer: stmummer
ms.date: 05/18/2021
ms.openlocfilehash: d4eb677b0f55a8ccd2cda33e6026e01238deb9b3
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111971533"
---
# <a name="migration-of-co-sell-solutions-from-ocp-gtm-to-the-commercial-marketplace"></a>OCP GTM からコマーシャル マーケットプレースへの共同販売ソリューションの移行

Microsoft は、発行エクスペリエンスを移動します。 [コマーシャル マーケットプレース](overview.md)では、オファーの作成と管理を、お客様が既に Microsoft との関係を管理しているパートナー センターに一元化することによって、Microsoft の 3 つのチャネルを通じて共同販売するための簡素化されたオファー発行を提供します。

コマーシャル マーケットプレースに登録されている Microsoft パートナーは、次のことを実行できます。

- Microsoft Direct の顧客、パートナー、販売者の各チャネルにわたってオファーを一元的に公開し、共同販売を行う。
- オファーの機能に合致する何百万人ものクラウド顧客に見てもらえるように、オファーが正しいオンライン ストア ([Microsoft AppSource](https://appsource.microsoft.com) または [Azure Marketplace](https://azure.microsoft.com)) にあることを確認する。
- ビジネス目標に合致したオファーで共同販売を行うために独自の発行エクスペリエンスを推進する。
- 既に Microsoft の関係と共同販売機会を管理しているパートナー センター内でオファーの発行を調整する。
- [Marketplace 報奨](marketplace-rewards.md)のロックを解除する。

## <a name="prerequisites-to-continue-co-selling-with-microsoft"></a>Microsoft との共同販売を継続するための前提条件

Microsoft Partner Network のアクティブなメンバーシップを持っており、パートナー センターのコマーシャル マーケットプレースに登録していることを確認します。

- [無料](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)で Microsoft Partner Network に参加します。 パートナーは、ビジネスを成長させるために専用のリソース、プログラム、ツール、および接続にアクセスできます。
- コマーシャル マーケットプレースにアカウントをお持ちでない場合はMicrosoft との共同販売を継続し、完全な発行エクスペリエンスにアクセスするために、[今すぐご登録](create-account.md)ください。

## <a name="publishing-updates-for-attaining-co-sell-ready-status"></a>Co-Sell-Ready 状態を達成するための発行の更新

お客様のソリューションを Microsoft の販売者やパートナーが検出できるようにするには、[Co-Sell Ready (共同販売準備完了) の要件](./co-sell-overview.md)を満たす必要があります。 Microsoft 販売者がインセンティブを受け取るためには、ソリューションが、[Incentive-Eligible (インセンティブ対象) 要件](./co-sell-overview.md)を満たしている必要があります。 パートナー センターの [共同販売] タブでこれらの要件を完了します (この記事で後述する[こちらのイメージ](#action-2-merge)を参照してください)。

> [!NOTE]
> コマーシャル マーケットプレースでは、ソリューションは発行エクスペリエンス全体で「オファー」と呼ばれています。

コマーシャル マーケットプレースに登録したら、OCP GTM からソリューションを移行する準備をします。 組織向け OCP GTM ソリューションの単一ビューを提供するデジタル移行インターフェイスが用意されています。 このインターフェイスから、ソリューションを既にコマーシャル マーケットプレースで公開されている類似オファーとマージしたり、破棄したりするアクションを行なえるようになります。

ソリューションを OCP GTM からインポートする前に、こちらの手順を実行してください。

1. 会社の[発行元リスト](https://partner.microsoft.com/dashboard/account/v3/publishers/list)にアクセスします。 これには、発行アクセス権を持つアカウント所有者、マネージャー、および開発者が含まれています。 パートナー センターのユーザー ロールの詳細については、[こちら](user-roles.md)を参照してください。
2. リストに記載されているいずれかの連絡先に連絡し、"*マネージャー*" または "*開発者*" としてコマーシャル マーケットプレースに [ユーザーを追加](https://partner.microsoft.com/dashboard/account/usermanagement)するように依頼します。これは、ソリューションを編集したり発行したりできるのはこれらのロールだけだからです。
3. 開発者と協力して、ソリューションを OCP GTM アカウントからコマーシャル マーケットプレースに移動します。
4. 次のうちどれを実行するかを決定します。
    1. パートナー センターへ移行したい OCP GTM のソリューションがある場合 - "*照会パイプライン、資料、共同販売ステータス、インセンティブを維持するには*" - 選択できるシナリオが 2 つあります。
        - コマーシャル マーケットプレースに類似オファーがない場合
            - 手順 1. [オファーの作成](#step-1-similar-offer-does-not-exist-in-commercial-marketplace-please-follow-these-steps)
            - 手順 2. [コマーシャル マーケットプレイスで公開する](#scenario-2-similar-offer-exists-in-commercial-marketplace-please-follow-these-steps)
            - 手順 3. [OCP GTM GTM GTM のソリューションと新しく公開されたマーケットプレース オファーのマージを要求する](#begin-the-migration-of-your-solutions-from-ocp-gtm)。
        - コマーシャル マーケットプレースに類似オファーが既にある場合
            - 手順 1. [OCP GTM のソリューションと公開済みマーケットプレース オファーのマージを要求する](#begin-the-migration-of-your-solutions-from-ocp-gtm)
    1. OCP GTM のソリューションに関連性がなくなった場合は、このソリューションを破棄します。

> [!IMPORTANT]
> 2021 年 5 月 17 日に、下の移行オプションは削除されました。  以前に [移行] オプションを使用し、コマーシャル マーケットプレースにドラフト オファーとして移行されたオファーがある場合は、コマーシャル マーケットプレースで公開プロセスを完了することが重要です。  
> - 詳細については、「[オファーをレビューして Microsoft コマーシャル マーケットプレースへ公開する方法 | Microsoft Docs](./review-publish-offer.md)」を参照してください。
> - [コマーシャル マーケットプレースのトレーニング デッキでの公開](https://assetsprod.microsoft.com/mpn/en-us/guidance-and-permissions-needed-to-publish-an-offer-and-co-sell-with-ms.pptm) 
:::image type="content" source="media/co-sell-migrate/ready-to-migrate.png" alt-text="このソリューションをコマーシャル マーケットプレースに移行したい":::

## <a name="begin-the-migration-of-your-solutions-from-ocp-gtm"></a>OCP GTM からソリューションの移行を開始する

1. [こちら](https://partner.microsoft.com/solutions/migration#)から移行を開始します。
2. **[概要]** ページを選択し、**こちらをクリックして開始します**。

    :::image type="content" source="media/co-sell-migrate/welcome-overveiw.png" alt-text="概要ページが表示される":::

3. 移行を開始するには、 **[ソリューション]** タブを選択します。これにより、MPN ID に関連付けられているすべてのソリューションが表示されます。

    :::image type="content" source="media/co-sell-migrate/solutions-tab.png" alt-text="パートナー センターの [概要] ページ、[ソリューション] タブ。":::

    > [!NOTE]
    > コマーシャル マーケットプレースへの移行を保留中のソリューションがない場合は、このタブに記載されます。 Microsoft との共同販売を継続するには、移行したソリューションがコマーシャル マーケットプレースに発行されていることを確認します。

    ソリューションの状態の詳細については、ツール ヒントを確認してください。 アクションを保留中のすべてのソリューションは、**アクション** の下に一覧表示されています。<a name="beginmigration"></a>

4. 移行するソリューションに対して **[移行の開始]** (上の図を参照) を選択してから、次のいずれかのオプションを選択します。

    :::image type="content" source="media/co-sell-migrate/migration-options.png" alt-text="移行のための 3 つのオプション。":::

### <a name="merge-solution"></a>ソリューションのマージ

OCP GTM ソリューションを既に公開されていてコマーシャル マーケットプレースでライブのオファーにマージすることの価値は何でしょうか。

- 現在 OCP GTM のオファーを使用して共同販売を行っているパートナーは、ソリューションをコマーシャル マーケットプレースに移動することが推奨されます。それによって、その機能を利用できるようになり、公開作業も単純化できます。

OCP GTM のソリューションでパートナー センターに移行したいものがある場合は 2 つのシナリオから選べます。 このマージ プロセスの終了後も、オファーは照会パイプライン、資料、共同販売ステータス、インセンティブを保持します。

#### <a name="step-1-similar-offer-does-not-exist-in-commercial-marketplace-please-follow-these-steps"></a>手順 1: 類似オファーがコマーシャル マーケットプレースに存在しない場合は、次の手順に従ってください

OCP GTM のソリューションとマージするオファーがコマーシャル マーケットプレースにまだない場合は、まずコマーシャル マーケットプレースでオファーを作成して公開する必要があります (これにより、共同販売ステータス、インセンティブ、紹介パイプラインが保持されます)。

1. コマーシャル マーケットプレースでドラフト オファーを作成します

   1. **[+New Offer]\(+新しいオファー\)** を選択します

        :::image type="content" source="media/co-sell-migrate/new-offer.png" alt-text="新しいオファーの表示":::

   2. 各タブで必要な情報を入力します。• **[詳細情報]** のリンクとヒントで、要件と詳細について説明されます。
         • 必要な場合、 **[CSP を通して再販する]** ページ (下の左側ナビゲーション メニュー) に入力してクラウド ソリューション プロバイダー (CSP) プログラムを通じて再販します。

        :::image type="content" source="media/co-sell-migrate/offer-setup-nav.png" alt-text="[オファーのセットアップ] ページが表示され、概要オプションが強調表示されています。":::
   3. **[下書きの保存]** を選択します。
        - オファーを発行する前に指定する必要がある情報の詳細な指示については、[発行に関するガイド](./publisher-guide-by-offer-type.md)のページを参照してください。
        - オファーの種類に対応する記事に示されている適格性の要件を確認し、オファーの選択と構成を確定する。
        - オンライン ショップごとの公開パターン (ソリューションをオファーの種類や構成にマップする方法など) を確認します。
        - [オファーの掲載のベスト プラクティス - Microsoft コマーシャル マーケットプレース | Microsoft Docs](./gtm-offer-listing-best-practices.md)

    > [!TIP]
    > **[Microsoft と共同販売する]** タブにはデータを "*入力しない*" ことをお勧めします。時間を節約するために、マージ プロセス中に OCP GTM の既存の資料を使用してこのデータは自動的に設定されます。

    マージが完了したら、[Microsoft と共同販売する] タブに戻り、必要に応じて更新を行うことができます。 詳細については、「[コマーシャル マーケットプレース プランの共同販売を構成する](./co-sell-configure.md)」を参照してください。
1. 完了したら、 **[レビューと公開]** を選択します。

     :::image type="content" source="media/co-sell-migrate/co-sell-with-ms.png" alt-text="[Microsoft と共同販売する] ページが表示され、オプションが強調表示されている":::
1. 送信されたすべての情報を確認したら、 **[Publish]\(発行\)** を選択して、認定レビューのためにドラフト オファーを送信します。 [認定フェーズの詳細をご覧ください](./review-publish-offer.md)。:::image type="content" source="media/co-sell-migrate/review-and-publish.png" alt-text="[レビューと公開] ページが表示されます。":::
1. [概要] タブで、送信の状態を追跡します。

    :::image type="content" source="media/co-sell-migrate/offer-overview-tab.png" alt-text="概要タブが表示されます":::
1. 認定レビューが完了すると、通知されます。 アクション可能なフィードバックが提供されている場合は、それに対処してから、 **[Publish]\(発行\)** を選択して再認定を開始します。

1. オファーが認定に合格したら、提供されているリンクを使用してオファーをプレビューし、必要に応じて最終的な調整を行います。 準備ができたら、 **[一般公開する]** (上のボタンを参照) を選択して、関連するコマーシャル マーケットプレースのストアフロントにオファーを発行します。

1. **下のシナリオ 2 に進み、マージ プロセスを完了します。**

#### <a name="scenario-2-similar-offer-exists-in-commercial-marketplace-please-follow-these-steps"></a>手順 2: 類似オファーがコマーシャル マーケットプレースに存在する場合は、次の手順に従ってください

ソリューションが既に公開され、コマーシャル マーケットプレースでライブであり、OCP GTM のソリューションとコマーシャル マーケットプレースのオファーを 1 つのオファーにマージする必要がある場合は、このオプションを選択します。 これにより、重複するオファーの作成が回避されます。

1. 既存のオファーを特定します。
   1. **[This is similar to an existing offer in commercial marketplace]\(これはコマーシャル マーケットプレースの既存のオファーと似ている\)** を選択します ([上](#beginmigration)の「**移行を開始する**」イメージを参照してください)。
   1. **[アクション 1]** タブには、OCP GTM ソリューションを関連付けることができるライブ コマーシャル マーケットプレース オファーが表示されています。 ライブ オファーがある場合は、一覧からそれを選択します。 選択するオファーの一覧がない場合は、Microsoft AppSource または Azure Marketplace から顧客向けのアドレス (URL) を入力します。
   1. **[続行]** をクリックします。
        [![マージ プロセスの [アクション1] タブ。](media/co-sell-migrate/action-1-merge.png)](media/co-sell-migrate/action-1-merge.png#lightbox)
1. マージを要求します。
    1. **[アクション 2]** タブには、OCP GTM ソリューションと、特定したものとのマージを要求するための手順が表示されます。 マージを要求するには、 **[Save & Contact support]\(保存してサポートに連絡\)** を選択します。これにより、ブラウザーでパートナーのサポート ページが開きます。 
    <a id="action-2-merge"></a>
        [![マージ プロセスの [アクション2] タブ。](media/co-sell-migrate/action-2-merge.png)](media/co-sell-migrate/action-2-merge.png#lightbox)
    1. **[問題の詳細を指定]** を選択し、次を入力します。
        1. プランの種類
        1. プラン名
        1. OCP ソリューション ID
        1. 既存のリストの URL
    1. **[Submit]\(送信\)** をクリックします。 パートナー サポート チームが 2 営業日以内にお客様に連絡します。
    1. パートナー サポートはお客様と協力して、このオファーが正常にマージされ、ライブ オファーとして発行されるようにします。

### <a name="discard-this-solution"></a>このソリューションを破棄する

ソリューションの実行可能性と関連性を評価して、コマーシャル マーケットプレースでのあなたのプレゼンスと見つけやすさを、お客様のニーズを満たす高品質のオファーによって最適化することをお勧めします。

OCP GTM ソリューション内のソリューションがもう関連しなくなった場合は、このオプションを選択します。 破棄を確認するように求められます。後で取り消すこともできます。

1. **[I want to discard this solution]\(このソリューションを破棄する\)** ([上記](#beginmigration)の **[Action required]\(アクションが必要\)** イメージを参照) を選択し、 **[続行]** を選択します。
2. **[破棄]** を選択します。

    :::image type="content" source="media/co-sell-migrate/migration-discard.png" alt-text="破棄を確認します。":::

3. 破棄を元に戻すには、 **[Undo Discard]\(破棄を元に戻す\)** を選択します。

    :::image type="content" source="media/co-sell-migrate/migration-discard-undo.png" alt-text="[Undo Discard]\(破棄を元に戻す\) リンク。":::

### <a name="additional-help"></a>追加のヘルプ

 追加のヘルプが必要な場合は、 **[ヘルプの取得]** タブを選択して、パートナー サポート チームに連絡してください。

:::image type="content" source="media/co-sell-migrate/get-support-link.png" alt-text="[Get Help]\(ヘルプの取得\) タブのサポート リンク。":::

## <a name="next-steps"></a>次のステップ

- [CSP パートナーによる再販](cloud-solution-providers.md)
- [コマーシャル マーケットプレース プランの共同販売を構成する](./co-sell-configure.md)
- これらの [FAQ](https://partner.microsoft.com/resources/detail/co-sell-requirements-publish-commercial-marketplace-faq-pdf) (PDF) を表示する