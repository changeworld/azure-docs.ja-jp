---
title: シングルテナント Azure Logic Apps のストレージ コストを見積もる
description: Logic Apps ストレージ計算ツールを使用して、ワークフローのストレージ コストを見積もります。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/10/2021
ms.openlocfilehash: 995c620eeafc5a627a9f9d94733c80de47f2928d
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132369400"
---
# <a name="estimate-storage-costs-for-workflows-in-single-tenant-azure-logic-apps"></a>シングルテナント Azure Logic Apps のワークフローのストレージ コストを見積もる

Azure Logic Apps では、すべてのストレージ操作に [Azure Storage](../storage/index.yml) を使用します。 従来の "*マルチテナント*" の Azure Logic Apps では、すべてのストレージの使用状況およびコストがロジック アプリに関連付けられます。 "*シングルテナント*" の Azure Logic Apps では、独自のストレージ アカウントを使用できるようになりました。 これらのストレージ コストは、Azure の請求書に個別に一覧表示されます。 この機能により、ロジック アプリ データの柔軟性と管理のしやすさが向上します。

> [!NOTE]
> この記事は、シングルテナントの Azure Logic Apps 環境のワークフローに該当します。 これらのワークフローは、同じロジック アプリ内に、同じストレージを共有する 1 つのテナントに存在します。 詳細については、「[シングルテナントとマルチテナント、および統合サービス環境](single-tenant-overview-compare.md)」を参照してください。

ストレージのコストは、ワークフローのコンテンツに基づいて変化します。 トリガー、アクション、ペイロードが異なると、ストレージ操作とニーズも異なります。 この記事では、シングルテナント ベースのロジック アプリで独自の Azure Storage アカウントを使用している場合にストレージ コストを見積もる方法について説明します。 最初に、Logic Apps ストレージ計算ツールを使用して、[実行するストレージ操作を見積もる](#estimate-storage-needs)ことができます。 その後、Azure 料金計算ツールでこれらの数値を使用して、[考えられるストレージ コストを見積もる](#estimate-storage-costs)ことができます。

## <a name="prerequisites"></a>前提条件

* Azure アカウントとサブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/)してください。

* シングルテナント ベースの Logic Apps ワークフロー。 ワークフローは [Azure portal を使用する](create-single-tenant-workflows-azure-portal.md)か、[Visual Studio Code を使用して](create-single-tenant-workflows-visual-studio-code.md)作成できます。 ワークフローがまだない場合は、ストレージ計算ツールで小規模、中規模、および大規模なサンプル ワークフローを使用できます。

* ワークフローで使用する Azure ストレージ アカウント。 ストレージ アカウントがない場合は、[ストレージ アカウントを作成します](../storage/common/storage-account-create.md)

## <a name="get-your-workflows-json-code"></a>ワークフローの JSON コードを取得する

見積もるワークフローがある場合は、ワークフローの JSON コードを取得します。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **Logic Apps** サービスにアクセスし、ワークフローを選択します。

1. ロジック アプリのメニューで、 **[開発ツール]** の **[ロジック アプリ コード ビュー]** を選択します。

1. ワークフローの JSON コードをコピーします。

## <a name="estimate-storage-needs"></a>ストレージ ニーズを見積もる

1. [Logic Apps ストレージ計算ツール](https://logicapps.azure.com/calculator)にアクセスします。

   :::image type="content" source="./media/estimate-storage-costs/storage-calculator.png" alt-text="入力インターフェイスを示す Logic Apps ストレージ計算ツールのスクリーンショット。" lightbox="./media/estimate-storage-costs/storage-calculator.png":::

1. シングルテナント ベースのロジック アプリ ワークフローの JSON コードを入力、アップロード、または選択します。

   * 前のセクションでコードをコピーした場合は、 **[Paste or upload workflow.json]\(workflow.json の貼り付けまたはアップロード\)** ボックスに貼り付けます。 
   * **workflow.json** ファイルをローカルに保存している場合は、 **[Select workflow]\(ワークフローの選択\)** で **[ファイルの参照]** を選択します。 ファイルを選択し、 **[開く]** を選択します。
   * まだワークフローがない場合は、 **[Select workflow]\(ワークフローの選択\)** でサンプル ワークフローを 1 つ選択します。

1. **[詳細オプション]** のオプションを確認します。 これらの設定はワークフローの種類によって異なり、次のものが含まれる場合があります。

   * ループの実行回数を入力するオプション。
   * ペイロードが 32 KB を超えるすべてのアクションを選択するためのオプション。

1. **[Monthly runs]\(毎月の実行\)** については、ワークフローを毎月実行する回数を入力します。

1. **[計算]** を選択し、計算が実行されるまで待機します。

1. **[Storage Operation Breakdown and Calculation Steps]\(ストレージ操作の内訳と計算手順\)** で、 **[操作数]** の見積もりを確認します。

    2 つのテーブルでは、実行別および月別の推定操作数を確認できます。 次の操作が表示されます。

    * **[Blob (read)]\(BLOB (読み取り)\)** 。Azure Blob Storage 読み取り操作の場合。
    * **[Blob (write)]\(BLOB (書き込み)\)** 。Azure Blob Storage 書き込み操作の場合。
    * **[Queue]\(キュー\)** Azure キュー Queue クラス 2 操作の場合。
    * **[Tables]\(テーブル\)** 。Azure Table Storage 操作の場合。

    各操作には、最小、最大、および "最善の推測" のカウント数があります。 個々のシナリオに基づいて[ストレージ操作コストを見積もる](#estimate-storage-costs)ために使用する最も関連性の高い数値を選択します。 推奨としては、正確性のために "最善推定" カウントを使用してください。 ただし、最大カウントを使用して、コスト見積もりにバッファーを含めるようにすることもできます。

    :::image type="content" source="./media/estimate-storage-costs/storage-calculator-results.png" alt-text="推定される操作による出力を示す、Logic Apps ストレージ計算ツールのスクリーンショット。" lightbox="./media/estimate-storage-costs/storage-calculator-results.png":::

## <a name="estimate-storage-costs"></a>ストレージ コストを見積もる

[ロジック アプリ ワークフローのストレージ ニーズを計算した](#estimate-storage-needs)後、考えられる毎月のストレージ コストを見積もることができます。 次のストレージ操作の種類の価格を見積もることができます。

* [BLOB ストレージの読み取りと書き込みの操作](#estimate-blob-storage-operations-costs)
* [キュー ストレージの操作](#estimate-queue-operations-costs)
* [テーブル ストレージの操作](#estimate-table-operations-costs)

### <a name="estimate-blob-storage-operations-costs"></a>BLOB ストレージの操作コストを見積もる

ロジック アプリの BLOB ストレージ操作の毎月のコストを見積もるには、次のようにします。

1. [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)に移動します。

1. **[製品]** タブで、 **[ストレージ]** &gt; **[ストレージ アカウント]** を選択します。 または、**検索バー** の検索ボックスに「**ストレージ アカウント**」と入力し、タイルを選択します。

   :::image type="content" source="./media/estimate-storage-costs/pricing-calculator-storage-tile.png" alt-text="Azure 料金計算ツールのスクリーンショット。ストレージ アカウント ビューを追加するタイルが表示されています。" lightbox="./media/estimate-storage-costs/pricing-calculator-storage-tile.png":::

1. **[Storage Accounts added]\(追加されるストレージ アカウント\)** 通知で、 **[表示]** を選択し、計算ツールの **[ストレージ アカウント]** セクションを表示します。 または、 **[ストレージ アカウント]** セクションに手動でアクセスします。

1. **[リージョン]** で、ロジック アプリのリージョンを選択します。

1. **[Type]\(種類\)** で、 **[ブロック BLOB ストレージ]** を選択します。

1. **[パフォーマンス レベル]** で、パフォーマンス レベルを選択します。

1. **[冗長性]** で、冗長性レベルを選択します。

1. 必要に応じて、その他の設定を調整します。

1. **[書き込み操作]** で、Logic Apps ストレージ計算ツールからの **[Blob (write)]\(BLOB (書き込み)\)** 操作数を "*10,000 で除算*" します。 この手順は、計算ツールがストレージ操作に対してトランザクション単位で動作するため必要です。

1. **[読み取り操作]** で、Logic Apps ストレージ計算ツールからの **[Blob (read)]\(BLOB (読み取り)\)** 操作数を "*10,000 で除算*" します。 この手順は、計算ツールがストレージ操作に対してトランザクション単位で動作するため必要です。

1. 推定される BLOB ストレージの操作コストを確認します。

### <a name="estimate-queue-operations-costs"></a>キューの操作コストを見積もる

ロジック アプリのキュー操作の毎月のコストを見積もるには、次のようにします。

1. [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)に移動します。

1. **[製品]** タブで、 **[ストレージ]** &gt; **[ストレージ アカウント]** を選択します。 または、**検索バー** の検索ボックスに「**ストレージ アカウント**」と入力し、タイルを選択します。

   :::image type="content" source="./media/estimate-storage-costs/pricing-calculator-storage-tile.png" alt-text="Azure 料金計算ツールのスクリーンショット。ストレージ アカウント ビューを追加するタイルが表示されています。" lightbox="./media/estimate-storage-costs/pricing-calculator-storage-tile.png":::

1. **[Storage Accounts added]\(追加されるストレージ アカウント\)** 通知で、 **[表示]** を選択し、計算ツールの **[ストレージ アカウント]** セクションを表示します。 または、 **[ストレージ アカウント]** セクションに手動でアクセスします。

1. **[リージョン]** で、ロジック アプリのリージョンを選択します。

1. **[Type]\(種類\)** で **[Queue Storage]** を選択します。

1. **Storage Account Type\(ストレージ アカウントの種類\)** で、ストレージ アカウントの種類を選択します。

1. **[冗長性]** で、冗長性レベルを選択します。

1. **[Queue Class 2 operations]\(Queue クラス 2 操作\)** に、Logic Apps ストレージ計算ツールの **キュー** 操作数を "*10,000 で割った*" 値を入力します。 この手順は、計算ツールがキュー操作に対してトランザクション単位で動作するため必要です。

1. 推定されるキューの操作コストを確認します。

### <a name="estimate-table-operations-costs"></a>テーブル操作のコストを見積もる

ロジック アプリのテーブル ストレージ操作の毎月のコストを見積もるには、次のようにします。

1. [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)に移動します。

1. **[製品]** タブで、 **[ストレージ]** &gt; **[ストレージ アカウント]** を選択します。 または、**検索バー** の検索ボックスに「**ストレージ アカウント**」と入力し、タイルを選択します。

   :::image type="content" source="./media/estimate-storage-costs/pricing-calculator-storage-tile.png" alt-text="Azure 料金計算ツールのスクリーンショット。ストレージ アカウント ビューを追加するタイルが表示されています。" lightbox="./media/estimate-storage-costs/pricing-calculator-storage-tile.png":::

1. **[Storage Accounts added]\(追加されるストレージ アカウント\)** 通知で、 **[表示]** を選択し、計算ツールの **[ストレージ アカウント]** セクションを表示します。 または、 **[ストレージ アカウント]** セクションに手動でアクセスします。

1. **[リージョン]** で、ロジック アプリのリージョンを選択します。

1. **[Type]\(種類\)** で **[Table Storage]\(テーブル ストレージ\)** を選択します。

1. **[Tier]\(レベル\)** で、パフォーマンス レベルを選択します。

1. **[冗長性]** で、冗長性レベルを選択します。

1. **[Storage transactions]\(ストレージ トランザクション\)** に、Logic Apps ストレージ計算ツールの **テーブル** 操作数を "*10,000 で割った*" 値を入力します。 この手順は、計算ツールがキュー操作に対してトランザクション単位で動作するため必要です。

1. 推定されるテーブル ストレージの操作コストを確認します。

## <a name="next-step"></a>次のステップ

> [!div class="nextstepaction"]
> [Logic Apps のコストを計画および管理する](plan-manage-costs.md)