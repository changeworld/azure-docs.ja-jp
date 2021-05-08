---
title: 'クイックスタート: 概要 - Synapse ワークスペースを作成する'
description: このチュートリアルでは、Synapse ワークスペース、専用 SQL プール、サーバーレス Apache Spark プールを作成する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: b22954edf4f3a5a935c470326aa43bd24ee2d708
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366064"
---
# <a name="creating-a-synapse-workspace"></a>Synapse ワークスペースの作成

このチュートリアルでは、Synapse ワークスペース、専用 SQL プール、サーバーレス Apache Spark プールを作成する方法について説明します。 

## <a name="prerequisites"></a>前提条件

このチュートリアルの手順を完了するには、**所有者** ロールが割り当てられているリソース グループにアクセスできる必要があります。 このリソース グループで Synapse ワークスペースを作成します。

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Azure portal で Synapse ワークスペースを作成する

### <a name="start-the-process"></a>プロセスを開始する
1. [Azure portal](https://portal.azure.com) を開き、Enter キーを押さずに検索バーに「**Synapse**」と入力します。
1. 検索結果の **[サービス]** で、 **[Azure Synapse Analytics]** を選択します。
1. **[追加]** を選択してワークスペースを作成します。

## <a name="basics-tab--project-details"></a>[基本] タブ > [プロジェクトの詳細]
以下のフィールドを設定します。

1. **[サブスクリプション]** - 任意のサブスクリプションを選択します。
1. **[リソース グループ]** - 任意のリソース グループを使用します。
1. **[Managed Resource group]\(マネージド リソース グループ\)** - 空白のままにします。

## <a name="basics-tab--workspace-details"></a>[基本] タブ > [ワークスペースの詳細]
以下のフィールドを設定します。

1. **[ワークスペース名]** - グローバルに一意の任意の名前を選択します。 このチュートリアルでは、**myworkspace** を使用します。
1. **[リージョン]** - 任意のリージョンを選択します。

**[Data Lake Storage Gen 2 の選択]** で、次の操作を行います。

1. **[アカウント名]** で、 **[新規作成]** を選択し、新しいストレージ アカウントに **contosolake** などの名前を付けます。この名前は一意である必要があります。
1. **[File system name]\(ファイル システム名\)** で、 **[新規作成]** を選択し、**users** という名前を付けます。 これにより、**users** というストレージ コンテナーが作成されます。 ワークスペースでは、このストレージ アカウントを Spark テーブルおよび Spark アプリケーション ログの "プライマリ" ストレージ アカウントとして使用します。
1. "Data Lake Storage Gen2 アカウントのストレージ BLOB データ共同作成者ロールを自分に割り当てる" ためのボックスをオンにします。 

## <a name="completing-the-process"></a>プロセスを完了する
**[確認と作成]**  >  **[作成]** の順に選択します。 ワークスペースの準備は数分で完了します。

> [!NOTE]
> 既存の専用 SQL プール (以前の SQL DW) のワークスペース機能を有効にするには、[専用の SQL プール (以前の SQL DW) 用のワークスペースを有効にする方法](./sql-data-warehouse/workspace-connected-create.md)に関するページを参照してください。


## <a name="open-synapse-studio"></a>Synapse Studio を開く

Azure Synapse ワークスペースが作成された後、Synapse Studio を開く方法は 2 つあります。

* [Azure portal](https://portal.azure.com) で Synapse ワークスペースを開き、Synapse ワークスペースの **[概要]** セクションで、[Synapse Studio の起動] ボックスの **[開く]** を選択します。
* `https://web.azuresynapse.net` にアクセスし、ワークスペースにサインインします。

## <a name="place-sample-data-into-the-primary-storage-account"></a>プライマリ ストレージ アカウントにサンプル データを配置する
このファースト ステップ ガイドの多くの例では、ニューヨーク市のタクシー データの 100,000 行の小さなサンプル データセットを使用します。 まず、ワークスペース用に作成したプライマリ ストレージ アカウントにこれを配置します。

* このファイルをお使いのコンピューターにダウンロードします (https://azuresynapsestorage.blob.core.windows.net/sampledata/NYCTaxiSmall/NYCTripSmall.parquet ) 
* Synapse Studio で、[データ] ハブに移動します。 
* **[リンク済み]** を選択します。
* **[Azure Data Lake Storae Gen2]** カテゴリの下に、**myworkspace ( プライマリ - contosolake )** のような名前の項目が表示されます。
* **[users (Primary)]\(ユーザー (プライマリ)\)** という名前のコンテナーを選択します。
* **[アップロード]** を選択し、ダウンロードした `NYCTripSmall.parquet` ファイルを選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [サーバーレス SQL プールを使用して分析する](get-started-analyze-sql-on-demand.md)
