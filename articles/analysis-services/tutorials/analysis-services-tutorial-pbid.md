---
title: チュートリアル - Power BI Desktop を使用して Azure Analysis Services に接続する | Microsoft Docs
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: 4096b9f77ba841abfcb4f29f2827aefacf22103f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446590"
---
# <a name="tutorial-connect-with-power-bi-desktop"></a>チュートリアル: Power BI Desktop を使用して接続する

このチュートリアルでは、Power BI Desktop を使用して、サーバー上の adventureworks サンプル モデル データベースに接続します。 作成したタスクで、モデルへの一般的なユーザー接続とモデル データからの基本的なレポートの作成をシミュレートします。

> [!div class="checklist"]
> * ポータルからサーバー名を取得する
> * Power BI Desktop を使用して接続する
> * 基本的なレポートを作成する

## <a name="prerequisites"></a>前提条件

- [adventureworks サンプル モデル データベースをサーバーに追加](../analysis-services-create-sample-model.md)します。
- adventureworks サンプル モデル データベースへの[*読み取り*](../analysis-services-server-admins.md)アクセス許可を付与します。
- [最新の Power BI Desktop をインストールします](https://powerbi.microsoft.com/desktop)。

## <a name="log-in-to-the-azure-portal"></a>Azure Portal にログインする
このチュートリアルでは、ポータルにログインしてサーバー名のみを取得します。 通常、ユーザーはサーバー管理者からサーバー名を入手します。

[ポータル](https://portal.azure.com/)にログインします。

## <a name="get-server-name"></a>サーバー名を取得する
Power BI Desktop からサーバーに接続するには、まずサーバー名が必要です。 ポータルからサーバー名を取得できます。

**Azure Portal** でサーバーを選び、**[概要]** > **[サーバー名]** のサーバー名をコピーします。
   
   ![Azure でサーバー名を取得する](./media/analysis-services-tutorial-pbid/aas-copy-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Power BI Desktop での接続

1. Power BI Desktop で、**[データの取得]** > **[Azure]** > **[Azure Analysis Services データベース]** の順にクリックします。

   ![[データの取得] で接続する](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aasserver.png)

2. **[サーバー]** にサーバー名を貼り付け、**[データベース]** に「**adventureworks**」と入力して **[OK]** をクリックします。

   ![サーバー名とモデル データベースを指定する](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aas-servername.png)

3. メッセージが表示されたら、ログイン資格情報を入力します。 入力するアカウントには、少なくとも adventureworks サンプル モデル データベースの読み取りアクセス許可が必要です。

    Power BI Desktop で adventureworks モデルが開き、[レポート] ビューに空のレポートが表示されます。 **[フィールド]** リストには、非表示にされているものを除くすべてのモデル オブジェクトが表示されます。 右下隅に接続ステータスが表示されます。

4. **[視覚化]** の **[集合横棒グラフ]** を選択し、**[書式]** (ペイント ローラー アイコン) をクリックし、**[データ ラベル]** を有効にします。 

   ![視覚化](./media/analysis-services-tutorial-pbid/aas-pbid-visualizations-report.png)

5. **[フィールド]** > **[Internet Sales]** テーブルで、**[Internet Sales Total]** と **[Margin]** のメジャーを選択します。 **[Product Category]** テーブルで **[Product Category Name]** を選択します。

   ![完成したレポート](./media/analysis-services-tutorial-pbid/aas-pbid-complete-report.png)

    数分間かけてさまざまな視覚化を作成し、データとメトリックをスライスして、adventureworks サンプル モデルを調べてください。 レポートに満足したら、忘れずに保存します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になった場合は、レポートを保存しないでおきます。保存済みの場合は、ファイルを削除します。

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Power BI Desktop を使用してサーバー上のデータ モデルに接続し、基本的なレポートを作成する方法について説明しました。 データ モデルの作成方法に慣れていない場合は、[Adventure Works Internet Sales 表形式データ モデリング チュートリアル](aas-adventure-works-tutorial.md)に関するページを参照してください。