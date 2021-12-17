---
title: Azure Chaos Studio でサービスダイレクト障害を使用する実験を作成する
description: サービスダイレクト障害を使用する実験を作成する
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: how-to
ms.date: 11/01/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: fd945f5a96228bc30c7d1f801fcc16ca34717cde
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373429"
---
# <a name="create-a-chaos-experiment-that-uses-a-service-direct-fault-to-fail-over-an-azure-cosmos-db-instance"></a>サービスダイレクト障害を使用するカオス実験を作成して、Azure Cosmos DB インスタンスをフェールオーバーします

カオス実験を使用して、制御された環境で障害を引き起こすことで、アプリケーションにこれらの障害に対する回復性があることを確認できます。 このガイドでは、カオス実験と Azure Chaos Studio を使用して、マルチ読み取り、単一書き込みの Azure Comos DB フェールオーバーを実行します。 この実験を行うことで、フェールオーバー イベントが発生したときにデータ損失を防ぐことができます。

これと同じ手順を使用して、サービスダイレクト障害に対する実験を設定して実行できます。 カオス エージェントのインストールが必要なエージェントベースの障害とは異なり、**サービスダイレクト** 障害は、インストルメンテーションを必要とせずに Azure リソースに対して直接実行されます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Azure Cosmos DB アカウント。 Azure Cosmos DB アカウントがない場合は、[こちらの手順に従って作成](../cosmos-db/sql/create-cosmosdb-resources-portal.md)できます。
- Azure Cosmos DB アカウントの読み取りリージョンと書き込みリージョンが 1 つ以上設定されていること。


## <a name="enable-chaos-studio-on-your-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントで Chaos Studio を有効化する

Chaos Studio では、最初にリソースが Chaos Studio にオンボードされていない限り、リソースに対して障害を挿入することはできません。 リソースを Chaos Studio にオンボードするには、リソースに[ターゲットと機能](chaos-studio-targets-capabilities.md)を作成します。 Azure Cosmos DB アカウントには 1 つのターゲットの種類 (サービスダイレクト) と 1 つの機能 (フェールオーバー) のみがありますが、その他のリソースには最大 2 つのターゲットの種類 (サービスダイレクト障害とエージェントベースの障害に各 1 つ) と多くの機能を含めることができます。

1. [Azure Portal](https://portal.azure.com)を開きます。
2. 検索バーで **Chaos Studio (プレビュー)** を検索します。
3. **[ターゲット]** をクリックし、Azure Cosmos DB アカウントに移動します。
![Azure portal の [ターゲット] ビュー](images/tutorial-service-direct-targets.png)
4. Azure Cosmos DB アカウントの横にあるチェックボックスをオンにして **[ターゲットを有効にする]** をクリックし、ドロップダウン メニューから **[サービスダイレクト ターゲットを有効にする]** をクリックします。
![Azure portal でのターゲットの有効化](images/tutorial-service-direct-targets-enable.png)
5. 選択したリソースが正常に有効化されたことを示す通知が表示されます。
![ターゲットが正常に有効化されたことを示す通知](images/tutorial-service-direct-targets-enable-confirm.png)

これで、Azure Cosmos DB アカウントが Chaos Studio に正常にオンボードしました。 また、 **[ターゲット]** ビューで、このリソースで有効になっている機能を管理できます。 リソースの横にある **[アクションの管理]** リンクをクリックすると、そのリソースに対して有効になっている機能が表示されます。

## <a name="create-an-experiment"></a>実験の作成
これで Azure Cosmos DB アカウントがオンボードされたので、実験を作成できます。 カオス実験には、ターゲット リソースに対して実行するアクションを、順番に実行するステップと、並行して実行する分岐に分けて定義します。

1. Chaos Studio ナビゲーションの **[実験]** タブをクリックします。 このビューでは、すべてのカオス実験を表示および管理できます。 **[実験の追加]** 
![Azure portal の実験ビュー](images/tutorial-service-direct-add.png) をクリックします
2. **[サブスクリプション]** 、 **[リソース グループ]** 、カオス実験をデプロイする **[場所]** に情報を入力します。 実験に **名前** を付けます。 **[次へ: 実験デザイナー >]** 
![基本的な実験の詳細の追加](images/tutorial-service-direct-add-basics.png) をクリックします
3. これで、Chaos Studio 実験デザイナーが表示されます。 実験デザイナーでは、ステップ、分岐、障害などを追加して実験を構築することができます。 **ステップ** と **分岐** にフレンドリ名を付け、 **[障害の追加]** をクリックします。
![実験デザイナー](images/tutorial-service-direct-add-designer.png)
4. ドロップダウンから **[Cosmos DB フェールオーバー]** を選択して、 **[期間]** に障害が継続する時間を分単位で入力し、 **[readRegion]** に Azure Cosmos DB アカウントの読み取りリージョンを入力します。 **[次へ: ターゲット リソース >]** 
![障害のプロパティ](images/tutorial-service-direct-add-fault.png) をクリックします
5. ご使用の Azure Cosmos DB アカウントを選択して、 **[次へ]** 
![ターゲットの追加](images/tutorial-service-direct-add-target.png) をクリックします
6. 実験が正しく表示されていることを確認し、 **[レビューと作成]** 、 **[作成]** 
![実験を確認して作成する](images/tutorial-service-direct-add-review.png) の順にクリックします。

## <a name="give-experiment-permission-to-your-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントに実験アクセス許可を付与する
カオス実験を作成すると、ターゲット リソースに対して障害を実行するシステム割り当てマネージド ID が Chaos Studio によって作成されます。 実験を正常に実行するには、この ID にターゲット リソースへの[適切なアクセス許可](chaos-studio-fault-providers.md)を付与する必要があります。

1. Azure Cosmos DB アカウントに移動し、 **[アクセス制御 (IAM)]** をクリックします。
![Azure Cosmos DB の概要ページ](images/tutorial-service-direct-access-resource.png)
2. **[追加]** 、 **[ロールの割り当ての追加]** の順にクリックします。
![アクセス制御の概要](images/tutorial-service-direct-access-iam.png)
3. **Cosmos DB オペレーター** を検索して、ロールを選択します。 **[次へ]** 
![Azure Cosmos DB オペレーター ロールを割り当てる](images/tutorial-service-direct-access-role.png) をクリックします
4. **[メンバーの選択]** をクリックし、実験名を検索します。 実験を選択し、 **[選択]** をクリックします。 同じテナント内に同じ名前の実験が複数ある場合、実験名はランダムな文字が追加されて切り詰められます。
![ロールへの実験の追加](images/tutorial-service-direct-access-experiment.png)
5. **[レビューと割り当て]** をクリックし、もう一度 **[レビューと割り当て]** をクリックします。

## <a name="run-your-experiment"></a>実験を実行する
これで、実験を実行する準備が整いました。 影響を確認するには、Azure Cosmos DB アカウントの概要を開き、別のブラウザー タブで **データをグローバルにレプリケートする** ことをお勧めします。実験中に定期的に更新すると、リージョンのスワップが表示されます。

1. **[実験]** ビューで、ご自分の実験をクリックし、 **[開始]** 、 **[OK]** の順にクリックします。
2. **[状態]** が **[実行中]** に変わったら、 **[履歴]** の下にある最新の実行の **[詳細]** をクリックして、実行中の実験の詳細を確認します。

## <a name="next-steps"></a>次のステップ
これで、Azure Cosmos DB サービスダイレクト実験を実行したので、次のことを行う準備ができました。
- [エージェントベースの障害を使用する実験を作成する](chaos-studio-tutorial-agent-based-portal.md)
- [実験を管理する](chaos-studio-run-experiment.md)
