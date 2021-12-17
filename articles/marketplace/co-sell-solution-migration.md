---
title: OCP GTM から Microsoft AppSource のパートナー センターへ共同販売ソリューションを移行する
description: OCP GTM からパートナー センター (Azure Marketplace) へ共同販売ソリューションを移行します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.reviewer: stmummer
ms.date: 09/27/2021
ms.openlocfilehash: d80063eeb46938d3f8006f777a3209bc34d7cfcb
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083193"
---
# <a name="migration-of-co-sell-solutions-from-ocp-gtm-to-the-commercial-marketplace"></a>OCP GTM からコマーシャル マーケットプレースへの共同販売ソリューションの移行

Microsoft は、発行エクスペリエンスを移動します。 <bpt id="p1">[</bpt>コマーシャル マーケットプレース<ept id="p1">](overview.md)</ept>では、オファーの作成と管理を、お客様が既に Microsoft との関係を管理しているパートナー センターに一元化することによって、Microsoft の 3 つのチャネルを通じて共同販売するための簡素化されたオファー発行を提供します。

コマーシャル マーケットプレースに登録されている Microsoft パートナーは、次のことを実行できます。

- Microsoft Direct の顧客、パートナー、販売者の各チャネルにわたってオファーを一元的に公開し、共同販売を行う。
- オファーの機能に合致する何百万人ものクラウド顧客に見てもらえるように、オファーが正しいオンライン ストア (<bpt id="p1">[</bpt>Microsoft AppSource<ept id="p1">](https://appsource.microsoft.com)</ept> または <bpt id="p2">[</bpt>Azure Marketplace<ept id="p2">](https://azure.microsoft.com)</ept>) にあることを確認する。
- ビジネス目標に合致したオファーで共同販売を行うために独自の発行エクスペリエンスを推進する。
- 既に Microsoft の関係と共同販売機会を管理しているパートナー センター内でオファーの発行を調整する。
- <bpt id="p1">[</bpt>Marketplace 報奨<ept id="p1">](marketplace-rewards.md)</ept>のロックを解除する。

## <a name="prerequisites-to-continue-co-selling-with-microsoft"></a>Microsoft との共同販売を継続するための前提条件

Microsoft Partner Network のアクティブなメンバーシップを持っており、パートナー センターのコマーシャル マーケットプレースに登録していることを確認します。

- <bpt id="p1">[</bpt>無料<ept id="p1">](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)</ept>で Microsoft Partner Network に参加します。 パートナーは、ビジネスを成長させるために専用のリソース、プログラム、ツール、および接続にアクセスできます。
- コマーシャル マーケットプレースにアカウントをお持ちでない場合はMicrosoft との共同販売を継続し、完全な発行エクスペリエンスにアクセスするために、<bpt id="p1">[</bpt>今すぐご登録<ept id="p1">](create-account.md)</ept>ください。

## <a name="publishing-updates-for-attaining-co-sell-ready-status"></a>Co-Sell-Ready 状態を達成するための発行の更新

お客様のソリューションを Microsoft の販売者やパートナーが検出できるようにするには、<bpt id="p1">[</bpt>Co-Sell Ready (共同販売準備完了) の要件<ept id="p1">](./co-sell-overview.md)</ept>を満たす必要があります。 Microsoft 販売者が共同販売インセンティブになるには、ソリューションが、<bpt id="p1">[</bpt>インセンティブ対象要件<ept id="p1">](./co-sell-overview.md)</ept>を満たしている必要があります。 パートナー センターの [共同販売] タブでこれらの要件を完了します (この記事で後述する<bpt id="p1">[</bpt>こちらのイメージ<ept id="p1">](#action-2-merge)</ept>を参照してください)。

> [!NOTE]
> コマーシャル マーケットプレースでは、ソリューションは発行エクスペリエンス全体で「オファー」と呼ばれています。

コマーシャル マーケットプレースに登録したら、OCP GTM からソリューションを移行する準備をします。 組織向け OCP GTM ソリューションの単一ビューを提供するデジタル移行インターフェイスが用意されています。 このインターフェイスから、ソリューションを既にコマーシャル マーケットプレースで公開されている類似オファーとマージしたり、破棄したりするアクションを行なえるようになります。

ソリューションを OCP GTM からインポートする前に、こちらの手順を実行してください。

1. 会社の<bpt id="p1">[</bpt>発行元リスト<ept id="p1">](https://go.microsoft.com/fwlink/?linkid=2165704)</ept>にアクセスします。 これには、発行アクセス権を持つアカウント所有者、マネージャー、および開発者が含まれています。 パートナー センターのユーザー ロールの詳細については、<bpt id="p1">[</bpt>こちら<ept id="p1">](user-roles.md)</ept>を参照してください。
2. リストに記載されているいずれかの連絡先に連絡し、"<bpt id="p2">*</bpt>マネージャー<ept id="p2">*</ept>" または "<bpt id="p3">*</bpt>開発者<ept id="p3">*</ept>" としてコマーシャル マーケットプレースに<bpt id="p1">[</bpt>ユーザーを追加<ept id="p1">](https://go.microsoft.com/fwlink/?linkid=2166003)</ept>するように依頼します。これは、ソリューションを編集したり発行したりできるのはこれらのロールだけだからです。
3. 開発者と協力して、ソリューションを OCP GTM アカウントからコマーシャル マーケットプレースに移動します。
4. 次のうちどれを実行するかを決定します。
    1. パートナー センターへ移行したい OCP GTM のソリューションがある場合 - "<bpt id="p1">*</bpt>照会パイプライン、資料、共同販売ステータス、インセンティブを維持するには<ept id="p1">*</ept>" - 選択できるシナリオが 2 つあります。
        - コマーシャル マーケットプレースに類似オファーがない場合
            - 手順 1. <bpt id="p1">[</bpt>オファーの作成<ept id="p1">](#step-1-similar-offer-does-not-exist-in-commercial-marketplace-please-follow-these-steps)</ept>
            - 手順 2. <bpt id="p1">[</bpt>コマーシャル マーケットプレイスで公開する<ept id="p1">](#scenario-2-similar-offer-exists-in-commercial-marketplace-please-follow-these-steps)</ept>
            - 手順 3. <bpt id="p1">[</bpt>OCP GTM GTM GTM のソリューションと新しく公開されたマーケットプレース オファーのマージを要求する<ept id="p1">](#begin-the-migration-of-your-solutions-from-ocp-gtm)</ept>。
        - コマーシャル マーケットプレースに類似オファーが既にある場合
            - 手順 1. <bpt id="p1">[</bpt>OCP GTM のソリューションと公開済みマーケットプレース オファーのマージを要求する<ept id="p1">](#begin-the-migration-of-your-solutions-from-ocp-gtm)</ept>
    1. OCP GTM のソリューションに関連性がなくなった場合は、このソリューションを破棄します。

> [!IMPORTANT]
> 2021 年 5 月 17 日に、下の移行オプションは削除されました。  以前に [移行] オプションを使用し、コマーシャル マーケットプレースにドラフト オファーとして移行されたオファーがある場合は、コマーシャル マーケットプレースで公開プロセスを完了することが重要です。  
> - 詳細については、「<bpt id="p1">[</bpt>オファーをレビューして Microsoft コマーシャル マーケットプレースへ公開する方法 | Microsoft Docs<ept id="p1">](./review-publish-offer.md)</ept>」を参照してください。
> - <bpt id="p1">[</bpt>コマーシャル マーケットプレースのトレーニング デッキでの公開<ept id="p1">](https://assetsprod.microsoft.com/mpn/en-us/guidance-and-permissions-needed-to-publish-an-offer-and-co-sell-with-ms.pptm)</ept> <ph id="ph1">&#xD;
</ph><bpt id="p2">:::image type="content" source="media/co-sell-migrate/ready-to-migrate.png" alt-text="</bpt>このソリューションをコマーシャル マーケットプレースに移行したい<ept id=&quot;p2&quot;>":::</ept>

## <a name="begin-the-migration-of-your-solutions-from-ocp-gtm"></a>OCP GTM からソリューションの移行を開始する

1. <bpt id="p1">[</bpt>こちら<ept id="p1">](https://go.microsoft.com/fwlink/?linkid=2165807)</ept>から移行を開始します。
1. <bpt id="p1">**</bpt>[概要]<ept id="p1">**</ept> ページを選択し、<bpt id="p2">**</bpt>こちらをクリックして開始します<ept id="p2">**</ept>。

    <bpt id="p1">:::image type="content" source="media/co-sell-migrate/welcome-overveiw.png" alt-text="</bpt>概要ページが表示される<ept id=&quot;p1&quot;>":::</ept>

1. 移行を開始するには、 <bpt id="p1">**</bpt>[ソリューション]<ept id="p1">**</ept> タブを選択します。これにより、MPN ID に関連付けられているすべてのソリューションが表示されます。

    <bpt id="p1">:::image type="content" source="media/co-sell-migrate/solutions-tab.png" alt-text="</bpt>パートナー センターの [概要] ページ、[ソリューション] タブ。<ept id=&quot;p1&quot;>":::</ept>

    > [!NOTE]
    > コマーシャル マーケットプレースへの移行を保留中のソリューションがない場合は、このタブに記載されます。 Microsoft との共同販売を継続するには、移行したソリューションがコマーシャル マーケットプレースに発行されていることを確認します。

    ソリューションの状態の詳細については、ツール ヒントを確認してください。 アクションを保留中のすべてのソリューションは、<bpt id="p1">**</bpt>アクション<ept id="p1">**</ept>の下に一覧表示されています。<bpt id="p2"><a name="beginmigration"></bpt><ept id="p2"></a></ept>

1. 移行するソリューションに対して <bpt id="p1">**</bpt>[移行の開始]<ept id="p1">**</ept> (上の図を参照) を選択してから、次のいずれかのオプションを選択します。

    <bpt id="p1">:::image type="content" source="media/co-sell-migrate/migration-options.png" alt-text="</bpt>移行のための 3 つのオプション。<ept id=&quot;p1&quot;>":::</ept>

### <a name="merge-solution"></a>ソリューションのマージ

OCP GTM ソリューションを既に公開されていてコマーシャル マーケットプレースでライブのオファーにマージすることの価値は何でしょうか。

- 現在 OCP GTM のオファーを使用して共同販売を行っているパートナーは、ソリューションをコマーシャル マーケットプレースに移動することが推奨されます。それによって、その機能を利用できるようになり、公開作業も単純化できます。

OCP GTM のソリューションでパートナー センターに移行したいものがある場合は 2 つのシナリオから選べます。 このマージ プロセスの終了後も、オファーは照会パイプライン、資料、共同販売ステータス、インセンティブを保持します。

#### <a name="step-1-similar-offer-does-not-exist-in-commercial-marketplace-please-follow-these-steps"></a>手順 1: 類似オファーがコマーシャル マーケットプレースに存在しない場合は、次の手順に従ってください

#### <a name="workspaces-view"></a>[ワークスペース ビュー](#tab/workspaces-view)

OCP GTM のソリューションとマージするオファーがコマーシャル マーケットプレースにまだない場合は、まずコマーシャル マーケットプレースでオファーを作成して公開する必要があります (これにより、共同販売ステータス、インセンティブ、紹介パイプラインが保持されます)。

1. コマーシャル マーケットプレースでドラフト オファーを作成します

   1. 商業マーケットプレースの [概要] ページで、 **[+ 新しいプラン]** を選択し、目的のプランの種類を選択します。

        [ ![Marketplace の [プラン] ページにある [新しいプラン] ボタンのスクリーンショット。](media/co-sell-migrate/new-offer-workspaces.png) ](media/co-sell-migrate/new-offer-workspaces.png#lightbox)

   2. 各タブに必要な情報を入力します。
      - 要件と詳細については、 <bpt id="p1">**</bpt>[Learn more]\(詳細情報\)<ept id="p1">**</ept> リンクとヒントを参照してください。
      - 必要な場合、クラウド ソリューション プロバイダー (CSP) プログラムを通じて再販するには、(下記の左側ナビゲーション メニューにある) <bpt id="p1">**</bpt>[CSP を通じた再販]<ept id="p1">**</ept> ページに入力します。

        [ ![[Offer Setup]\(プランの設定\) ページが表示され、概要のオプションが強調表示されています。](media/co-sell-migrate/offer-setup-nav-workspaces.png) ](media/co-sell-migrate/offer-setup-nav-workspaces.png#lightbox)

   3. <bpt id="p1">**</bpt>[下書きの保存]<ept id="p1">**</ept> を選択します。
        - オファーを発行する前に指定する必要がある情報の詳細な指示については、<bpt id="p1">[</bpt>発行に関するガイド<ept id="p1">](./publisher-guide-by-offer-type.md)</ept>のページを参照してください。
        - オファーの種類に対応する記事に示されている適格性の要件を確認し、オファーの選択と構成を確定する。
        - オンライン ショップごとの公開パターン (ソリューションをオファーの種類や構成にマップする方法など) を確認します。
        - <bpt id="p1">[</bpt>オファーの掲載のベスト プラクティス - Microsoft コマーシャル マーケットプレース | Microsoft Docs<ept id="p1">](./gtm-offer-listing-best-practices.md)</ept>

    > [!TIP]
    > <bpt id="p2">**</bpt>[Microsoft と共同販売する]<ept id="p2">**</ept> タブにはデータを "<bpt id="p1">*</bpt>入力しない<ept id="p1">*</ept>" ことをお勧めします。時間を節約するために、マージ プロセス中に OCP GTM の既存の資料を使用してこのデータは自動的に設定されます。

    マージが完了したら、[Microsoft と共同販売する] タブに戻り、必要に応じて更新を行うことができます。 詳細については、「<bpt id="p1">[</bpt>コマーシャル マーケットプレース プランの共同販売を構成する<ept id="p1">](./co-sell-configure.md)</ept>」を参照してください。
1. 完了したら、 <bpt id="p1">**</bpt>[レビューと公開]<ept id="p1">**</ept> を選択します。

    [ ![[Co-Sell with Microsoft]\(Microsoft と共同販売する\) ページが表示され、オプションが強調表示されています。](media/co-sell-migrate/co-sell-with-ms-workspaces.png) ](media/co-sell-migrate/co-sell-with-ms-workspaces.png#lightbox)

1. 送信されたすべての情報を確認したら、 <bpt id="p1">**</bpt>[Publish]\(発行\)<ept id="p1">**</ept> を選択して、認定レビューのためにドラフト オファーを送信します。 [認定フェーズに関する詳細情報を参照してください](./review-publish-offer.md)。

    [ ![[Review and Publish]\(確認して公開\) ページが表示されます。](media/co-sell-migrate/review-and-publish-workspaces.png) ](media/co-sell-migrate/review-and-publish-workspaces.png#lightbox)

1. [概要] タブで、送信の状態を追跡します。

    [ ![パートナー センターでのプランの公開状態を示しています。[Go live]\(公開\) ボタンが表示されています。[Automated validation]\(自動検証\) の下に、[View validation report]\(検証レポートの表示\) リンクも表示されています。](./media/review-publish-offer/publish-status-saas.png) ](./media/review-publish-offer/publish-status-saas.png#lightbox)

1. 認定レビューが完了すると、通知されます。 アクション可能なフィードバックが提供されている場合は、それに対処してから、 <bpt id="p1">**</bpt>[Publish]\(発行\)<ept id="p1">**</ept> を選択して再認定を開始します。

1. オファーが認定に合格したら、提供されているリンクを使用してオファーをプレビューし、必要に応じて最終的な調整を行います。 準備ができたら、 <bpt id="p1">**</bpt>[一般公開する]<ept id="p1">**</ept> (上のボタンを参照) を選択して、関連するコマーシャル マーケットプレースのストアフロントにオファーを発行します。

1. <bpt id="p1">**</bpt>下のシナリオ 2 に進み、マージ プロセスを完了します。<ept id="p1">**</ept>

#### <a name="current-view"></a><bpt id="p1">[</bpt>現在のビュー<ept id="p1">](#tab/current-view)</ept>

OCP GTM のソリューションとマージするオファーがコマーシャル マーケットプレースにまだない場合は、まずコマーシャル マーケットプレースでオファーを作成して公開する必要があります (これにより、共同販売ステータス、インセンティブ、紹介パイプラインが保持されます)。

1. コマーシャル マーケットプレースでドラフト オファーを作成します

   1. <bpt id="p1">**</bpt>[+ New Offer]\(+ 新しいオファー\)<ept id="p1">**</ept> を選択します

        <bpt id="p1">:::image type="content" source="media/co-sell-migrate/new-offer.png" alt-text="</bpt>新しいオファーの表示<ept id=&quot;p1&quot;>":::</ept>

   2. 各タブで必要な情報を入力します。• <bpt id="p1">**</bpt>[詳細情報]<ept id="p1">**</ept> のリンクとヒントで、要件と詳細について説明されます。
         • 必要な場合、 <bpt id="p1">**</bpt>[CSP を通して再販する]<ept id="p1">**</ept> ページ (下の左側ナビゲーション メニュー) に入力してクラウド ソリューション プロバイダー (CSP) プログラムを通じて再販します。

        <bpt id="p1">:::image type="content" source="media/co-sell-migrate/offer-setup-nav.png" alt-text="</bpt>[オファーのセットアップ] ページが表示され、概要オプションが強調表示されています。<ept id=&quot;p1&quot;>":::</ept>
   3. <bpt id="p1">**</bpt>[下書きの保存]<ept id="p1">**</ept> を選択します。
        - オファーを発行する前に指定する必要がある情報の詳細な指示については、<bpt id="p1">[</bpt>発行に関するガイド<ept id="p1">](./publisher-guide-by-offer-type.md)</ept>のページを参照してください。
        - オファーの種類に対応する記事に示されている適格性の要件を確認し、オファーの選択と構成を確定する。
        - オンライン ショップごとの公開パターン (ソリューションをオファーの種類や構成にマップする方法など) を確認します。
        - <bpt id="p1">[</bpt>オファーの掲載のベスト プラクティス - Microsoft コマーシャル マーケットプレース | Microsoft Docs<ept id="p1">](./gtm-offer-listing-best-practices.md)</ept>

    > [!TIP]
    > <bpt id="p2">**</bpt>[Microsoft と共同販売する]<ept id="p2">**</ept> タブにはデータを "<bpt id="p1">*</bpt>入力しない<ept id="p1">*</ept>" ことをお勧めします。時間を節約するために、マージ プロセス中に OCP GTM の既存の資料を使用してこのデータは自動的に設定されます。

    マージが完了したら、[Microsoft と共同販売する] タブに戻り、必要に応じて更新を行うことができます。 詳細については、「<bpt id="p1">[</bpt>コマーシャル マーケットプレース プランの共同販売を構成する<ept id="p1">](./co-sell-configure.md)</ept>」を参照してください。
1. 完了したら、 <bpt id="p1">**</bpt>[レビューと公開]<ept id="p1">**</ept> を選択します。

     <bpt id="p1">:::image type="content" source="media/co-sell-migrate/co-sell-with-ms.png" alt-text="</bpt>[Microsoft と共同販売する] ページが表示され、オプションが強調表示されている<ept id=&quot;p1&quot;>":::</ept>
1. 送信されたすべての情報を確認したら、 <bpt id="p1">**</bpt>[Publish]\(発行\)<ept id="p1">**</ept> を選択して、認定レビューのためにドラフト オファーを送信します。 <bpt id="p1">[</bpt>認定フェーズの詳細をご覧ください<ept id="p1">](./review-publish-offer.md)</ept>。<bpt id="p2">:::image type="content" source="media/co-sell-migrate/review-and-publish.png" alt-text="</bpt>[レビューと公開] ページが表示されます。<ept id=&quot;p2&quot;>":::</ept>
1. [概要] タブで、送信の状態を追跡します。

    <bpt id="p1">:::image type="content" source="media/co-sell-migrate/offer-overview-tab.png" alt-text="</bpt>概要タブが表示されます<ept id=&quot;p1&quot;>":::</ept>
1. 認定レビューが完了すると、通知されます。 アクション可能なフィードバックが提供されている場合は、それに対処してから、 <bpt id="p1">**</bpt>[Publish]\(発行\)<ept id="p1">**</ept> を選択して再認定を開始します。

1. オファーが認定に合格したら、提供されているリンクを使用してオファーをプレビューし、必要に応じて最終的な調整を行います。 準備ができたら、 <bpt id="p1">**</bpt>[一般公開する]<ept id="p1">**</ept> (上のボタンを参照) を選択して、関連するコマーシャル マーケットプレースのストアフロントにオファーを発行します。

1. <bpt id="p1">**</bpt>下のシナリオ 2 に進み、マージ プロセスを完了します。<ept id="p1">**</ept>

---

#### <a name="scenario-2-similar-offer-exists-in-commercial-marketplace-please-follow-these-steps"></a>手順 2: 類似オファーがコマーシャル マーケットプレースに存在する場合は、次の手順に従ってください

ソリューションが既に公開され、コマーシャル マーケットプレースでライブであり、OCP GTM のソリューションとコマーシャル マーケットプレースのオファーを 1 つのオファーにマージする必要がある場合は、このオプションを選択します。 これにより、重複するオファーの作成が回避されます。

1. 既存のオファーを特定します。
   1. <bpt id="p1">**</bpt>[This is similar to an existing offer in commercial marketplace]\(これはコマーシャル マーケットプレースの既存のオファーと似ている\)<ept id="p1">**</ept> を選択します (<bpt id="p3">[</bpt>上<ept id="p3">](#beginmigration)</ept>の「<bpt id="p2">**</bpt>移行を開始する<ept id="p2">**</ept>」イメージを参照してください)。
   1. <bpt id="p1">**</bpt>[アクション 1]<ept id="p1">**</ept> タブには、OCP GTM ソリューションを関連付けることができるライブ コマーシャル マーケットプレース オファーが表示されています。 ライブ オファーがある場合は、一覧からそれを選択します。 選択するオファーの一覧がない場合は、Microsoft AppSource または Azure Marketplace から顧客向けのアドレス (URL) を入力します。
   1. <bpt id="p1">**</bpt>[続行]<ept id="p1">**</ept> をクリックします。
        <bpt id="p1">[</bpt><ph id="ph1">![</ph>マージ プロセスの [アクション1] タブ。<ept id="p1">](media/co-sell-migrate/action-1-merge.png)](media/co-sell-migrate/action-1-merge.png#lightbox)</ept>
1. マージを要求します。
    1. <bpt id="p1">**</bpt>[アクション 2]<ept id="p1">**</ept> タブには、OCP GTM ソリューションと、特定したものとのマージを要求するための手順が表示されます。 マージを要求するには、 <bpt id="p1">**</bpt>[Save & Contact support]\(保存してサポートに連絡\)<ept id="p1">**</ept> を選択します。これにより、ブラウザーでパートナーのサポート ページが開きます。 
    <a id="action-2-merge"></a>
        <bpt id="p1">[</bpt><ph id="ph1">![</ph>マージ プロセスの [アクション2] タブ。<ept id="p1">](media/co-sell-migrate/action-2-merge.png)](media/co-sell-migrate/action-2-merge.png#lightbox)</ept>
    1. <bpt id="p1">**</bpt>[問題の詳細を指定]<ept id="p1">**</ept> を選択し、次を入力します。
        1. プランの種類
        1. プラン名
        1. OCP ソリューション ID
        1. 既存のリストの URL
    1. <bpt id="p1">**</bpt>[Submit]\(送信\)<ept id="p1">**</ept> をクリックします。 パートナー サポート チームが 2 営業日以内にお客様に連絡します。
    1. パートナー サポートはお客様と協力して、このオファーが正常にマージされ、ライブ オファーとして発行されるようにします。

### <a name="discard-this-solution"></a>このソリューションを破棄する

ソリューションの実行可能性と関連性を評価して、コマーシャル マーケットプレースでのあなたのプレゼンスと見つけやすさを、お客様のニーズを満たす高品質のオファーによって最適化することをお勧めします。

OCP GTM ソリューション内のソリューションがもう関連しなくなった場合は、このオプションを選択します。 破棄を確認するように求められます。後で取り消すこともできます。

1. <bpt id="p1">**</bpt>[I want to discard this solution]\(このソリューションを破棄する\)<ept id="p1">**</ept> (<bpt id="p3">[</bpt>上記<ept id="p3">](#beginmigration)</ept>の <bpt id="p2">**</bpt>[Action required]\(アクションが必要\)<ept id="p2">**</ept> イメージを参照) を選択し、 <bpt id="p4">**</bpt>[続行]<ept id="p4">**</ept> を選択します。
2. <bpt id="p1">**</bpt>[破棄]<ept id="p1">**</ept> を選択します。

    <bpt id="p1">:::image type="content" source="media/co-sell-migrate/migration-discard.png" alt-text="</bpt>破棄を確認します。<ept id=&quot;p1&quot;>":::</ept>

3. 破棄を元に戻すには、 <bpt id="p1">**</bpt>[Undo Discard]\(破棄を元に戻す\)<ept id="p1">**</ept> を選択します。

    <bpt id="p1">:::image type="content" source="media/co-sell-migrate/migration-discard-undo.png" alt-text="</bpt>[Undo Discard]\(破棄を元に戻す\) リンク。<ept id=&quot;p1&quot;>":::</ept>

### <a name="additional-help"></a>追加のヘルプ

 追加のヘルプが必要な場合は、 <bpt id="p1">**</bpt>[ヘルプの取得]<ept id="p1">**</ept> タブを選択して、パートナー サポート チームに連絡してください。

<bpt id="p1">:::image type="content" source="media/co-sell-migrate/get-support-link.png" alt-text="</bpt>[Get Help]\(ヘルプの取得\) タブのサポート リンク。<ept id=&quot;p1&quot;>":::</ept>

## <a name="next-steps"></a>次のステップ

- <bpt id="p1">[</bpt>CSP パートナーによる再販<ept id="p1">](cloud-solution-providers.md)</ept>
- <bpt id="p1">[</bpt>コマーシャル マーケットプレース プランの共同販売を構成する<ept id="p1">](./co-sell-configure.md)</ept>
- これらの <bpt id="p1">[</bpt>FAQ<ept id="p1">](https://partner.microsoft.com/resources/detail/co-sell-requirements-publish-commercial-marketplace-faq-pdf)</ept> (PDF) を表示する
