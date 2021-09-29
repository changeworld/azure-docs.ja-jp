---
title: Azure App Service に移行するための Web アプリの評価に関するチュートリアル
description: Azure Migrate で Azure App Service の評価を作成する方法について説明します
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 07/28/2021
ms.openlocfilehash: 7d37b3755e79c8117bbec7269e7ef97e8edb7429
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782311"
---
# <a name="tutorial-assess-aspnet-web-apps-for-migration-to-azure-app-service"></a>チュートリアル: Azure App Service に移行するための ASP.NET Web アプリの評価

Azure への移行に取り組む過程では、オンプレミスのワークロードを評価し、クラウドへの対応性を測り、リスクを明らかにして、コストと複雑さを見積もります。
この記事では、Azure App Service への移行の準備として、Azure Migrate: Discovery and assessment ツールを使用して、IIS Web サーバーで実行されている検出された ASP.NET Web アプリを評価する方法について説明します。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * Web アプリ構成データに基づいて評価を実行する。
> * Azure App Service の評価を検討する。

> [!NOTE]
> チュートリアルでは、シナリオを試すための最も簡単な方法を説明し、可能な限り既定のオプションを使用します。 

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。
- このチュートリアルに従って Azure App Service への移行のために Web アプリを評価する前に、Azure Migrate アプライアンスを使用して、評価する Web アプリが検出されていることを確認してください ([こちらのチュートリアルをご覧ください](tutorial-discover-vmware.md))。
- 既存のプロジェクトでこの機能を試す場合は、この記事の[前提条件](how-to-discover-sql-existing-project.md)を完了していることを確認してください。

## <a name="run-an-assessment"></a>評価を実行する

評価を実行するには次のようにします。

1. **[概要]** ページ > **[Servers, databases and web apps]\(サーバー、データベース、Web アプリ\)** で、 **[検出、評価、移行]** をクリックします。
    :::image type="content" source="./media/tutorial-assess-webapps/discover-assess-migrate.png" alt-text="Azure Migrate の [概要] ページ":::
2. **[Azure Migrate: Discovery and assessment]** で、 **[評価]** をクリックし、評価の種類として **Azure App Service** を選択します。
    :::image type="content" source="./media/tutorial-assess-webapps/assess.png" alt-text="評価の種類として Azure App Service を選択するドロップダウン":::
3. **[評価の作成]** では、評価の種類として **Azure App Service** が事前に選択されており、検出ソースが既定で **[Azure Migrate アプライアンスから検出されたサーバー]** に設定されていることを確認できます。
4. **[編集]** をクリックして、評価のプロパティを確認します。
     :::image type="content" source="./media/tutorial-assess-webapps/assess-webapps.png" alt-text="評価のプロパティをカスタマイズできる [編集] ボタン":::
5. Azure App Service 評価のプロパティに含まれるものは次のとおりです。

    **プロパティ** | **詳細**
    --- | ---
    **ターゲットの場所** | 移行先となる Azure リージョン。 Azure App Service の構成とコストに関する推奨事項は、ユーザーが指定した場所に基づいています。
    **分離が必要** | Standard プランと比較してより高速なプロセッサ、SSD ストレージ、コア比 2 倍のメモリを備えた Dv2 シリーズ VM を使用して、Azure データセンター内のプライベートな専用環境で Web アプリを実行したい場合は、[はい] を選択します。
    **予約インスタンス** | 予約インスタンスを指定します。これにより、評価でのコスト見積もりで考慮されます。<br/><br/> 予約インスタンス オプションを選択した場合、[割引 (%)] を指定することはできません。
    **プラン** | 自分が登録されている [Azure プラン](https://azure.microsoft.com/support/legal/offer-details/)。 評価によって、そのプランのコストが見積もられます。
    **通貨** | アカウントの請求通貨。
    **割引率 (%)** | Azure プランに適用される任意のサブスクリプション固有の割引。 既定の設定は 0% です。
    **EA サブスクリプション** | Enterprise Agreement (EA) サブスクリプションをコスト見積もりに使用することを指定します。 このサブスクリプションに適用される割引が考慮されます。 <br/><br/> 予約インスタンスの設定、および割引率 (%) のプロパティは、既定の設定のままにします。

    :::image type="content" source="./media/tutorial-assess-webapps/webapps-assessment-properties.png" alt-text="App Service 評価のプロパティ":::

1. **[評価の作成]** で、[次へ] をクリックします。
1. **[評価するサーバーの選択]**  >  **[評価名]** で、評価の名前を指定します。
1. **[グループの選択または作成]** で **[新規作成]** を選択し、グループ名を指定します。
1. アプライアンスを選択し、グループに追加するサーバーを選択します。 それから、[次へ] をクリックします。
1. **[評価の確認と作成]** で評価の詳細を確認したら、 [評価の作成] をクリックしてグループを作成し、評価を実行します。
1. 評価が作成された後、 **[Servers, databases and web apps]\(サーバー、データベース、Web アプリ\)**  >  **[Azure Migrate: Discovery and assessment]** タイルに移動して、タイルの上部にある [更新] オプションをクリックしてタイル データを更新します。 データが更新されるまで待ちます。
    :::image type="content" source="./media/tutorial-assess-webapps/tile-refresh.png" alt-text="検出および評価ツールのデータの更新":::
1. Azure App Service 評価の横にある数字をクリックします。
    :::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-navigation.png" alt-text="作成された評価へのナビゲーション":::
1. 表示する評価の名前をクリックします。

## <a name="review-an-assessment"></a>評価を確認する

**評価を表示するには**:

1. **[Servers, databases and web apps]\(サーバー、データベース、Web アプリ\)**  >  **[Azure Migrate: Discovery and assessment]** で、Azure App Service 評価の横にある数字をクリックします。
2. 表示する評価の名前をクリックします。
:::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-summary.png" alt-text="App Service 評価の概要":::
3. 評価の概要を確認します。 評価のプロパティを編集することも、評価を再計算することもできます。

#### <a name="azure-app-service-readiness"></a>Azure App Service の対応性

これは、評価された Web アプリの分布を示しています。 Azure App Service に移行する前に修正が可能な、移行に関する問題や警告について詳細を把握するためにドリルダウンできます。 [詳細についてはこちらをご覧ください。](concepts-azure-webapps-assessment-calculation.md)また、Azure App Service に移行するために推奨されている App Service SKU およびプランを確認することもできます。

#### <a name="azure-app-service-cost-details"></a>Azure App Service のコストの詳細

[App Service プラン](../app-service/overview-hosting-plans.md)では、使用するコンピューティング リソースに対して[課金](https://azure.microsoft.com/pricing/details/app-service/windows/)されます。

### <a name="review-readiness"></a>対応性を確認する

1. **[Azure App Service の対応性]** をクリックします。
    :::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-readiness.png" alt-text="Azure App Service の対応性の詳細":::
1. 評価された Web アプリについて、表の Azure App Service の対応性の列を確認してください。
    1. 互換性の問題が見つからなかった場合は、ターゲットのデプロイの種類に対する対応性が **[対応]** とマークされます。
    1. 機能低下やサポートされていない機能など、ターゲットとする特定のデプロイの種類への移行をブロックしない、重大でない互換性の問題がある場合は、対応性は **[条件付きで対応]** (ハイパーリンク付き) とマークされ、**警告** の詳細と推奨される修復のガイダンスが表示されます。
    1. ターゲットとする特定のデプロイの種類への移行を妨げる可能性がある互換性の問題がある場合は、対応性は **[準備不完了]** とマークされ、**問題** の詳細と推奨される修復のガイダンスが表示されます。
    1. 検出がまだ進行中であるか、Web アプリの検出で何らかの問題がある場合、対応性は **[不明]** としてマークされます。これは、評価でその Web アプリに対する対応性を計算できなかったためです。
1. 下のマトリックスに従って特定された、Web アプリの推奨される SKU を確認します。

    **分離が必要** | **予約インスタンス** | **App Service プラン/SKU**
    --- | --- | ---
    はい  | はい | I1
    はい  | いいえ  | I1
    いいえ  | はい | P1v3
    いいえ  | いいえ | P1v2

    **Azure App Service の対応性** | **App Service SKU の決定** | **コスト見積もりの決定**
    --- | --- | ---
    Ready  | はい | はい
    条件付きで対応  | はい  | はい
    準備不完了  | いいえ | いいえ
    Unknown  | いいえ | いいえ

1. 表の App Service プランのハイパーリンクをクリックすると、コンピューティング リソースなどの App Service プランの詳細と、その同じプランの一部である他の Web アプリが表示されます。

### <a name="review-cost-estimates"></a>コスト見積もりを確認する

評価の概要には、App Service で Web アプリをホストするための月額コストの見積もりが表示されます。 App Service では、Web アプリごとにではなく、App Service プランごとに料金をお支払いいただきます。 1 つまたは複数のアプリを同じコンピューティング リソース (または、同じ App Service プラン) で実行するように構成することができます。 この App Service プランに入れたアプリは、App Service プランで定義されたとおりにこれらのコンピューティング リソースで実行されます。
コストを最適化するため、Azure Migrate の評価では、複数の Web アプリをそれぞれに推奨されている App Service プランに割り当てます。 各プランのインスタンスに割り当てられる Web アプリの数は、次の表のようになります。

**App Service プラン** | **App Service プランごとの Web アプリ**
--- | ---
I1  | 8
P1v2  | 8
P1v3  | 16

:::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-cost.png" alt-text="コスト詳細":::

## <a name="next-steps"></a>次のステップ

Azure App Service の評価の計算方法の[詳細を確認](concepts-azure-webapps-assessment-calculation.md)します。