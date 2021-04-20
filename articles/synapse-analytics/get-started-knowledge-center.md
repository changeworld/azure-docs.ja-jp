---
title: チュートリアル:Synapse Knowledge Center の探索を開始する
description: このチュートリアルでは、Synapse Knowledge Center を使用する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/04/2021
ms.openlocfilehash: 92d1534b5dd0233ce88b81d605c6502b67a121df
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307896"
---
# <a name="explore-the-synapse-knowledge-center"></a>Synapse Knowledge Center を探索する

このチュートリアルでは、Synapse Studio Knowledge Center を使用する方法について説明します。

## <a name="introduction-to-the-knowledge-center"></a>Knowledge Center の概要

Synapse Studio で Knowledge Center を検索するには、次の 2 つの方法があります。

  1. ページ右上付近にある [Home]\(ホーム\) ハブの **[Learn]\(詳細\)** をクリックします。
  2. 上部のメニュー バーで、 **[?]** 、 **[Knowledge Center]** の順にクリックします。

いずれかの方法を選択して、 **[Knowledge Center]** を開きます。

表示されると、 **[Knowledge Center]** で次の 3 つの操作を実行できることがわかります。
* **[Use samples immediately]\(サンプルをすぐに使用する\)** 。 Synapse の機能の例をすぐに確認したい場合は、このオプションを選択します。
* **[ギャラリーを参照]** 。 このオプションを使用すると、サンプル データ セットをリンクして、SQL スクリプト、ノートブック、パイプラインの形式でサンプル コードを追加できます。
* **[Tour Synapse studio]\(Synapse Studio のツアーを見る\)** 。 このオプションでは、Synapse Studio の基本的な部分についての簡単なツアーを見ることができます。 これは、Synapse Studio をこれまで使用したことがない場合に役立ちます。

## <a name="exploring-use-samples-immediately"></a>探索: [Use samples immediately]\(サンプルをすぐに使用する\)

このセクションには 3 つの項目があります。
* [Explore sample data with Spark]\(Spark を使用してサンプル データを探索する\)
* [Query data with SQL]\(SQL を使用してデータを照会する\)
* [Create external table with SQL]\(SQL を使用して外部テーブルを作成する\)

1. **[Knowledge Center]** で、 **[Use samples immediately]\(サンプルをすぐに使用する\)** をクリックします。
1. **[Query data with SQL]\(SQL を使用してデータを照会する\)** を選択します。
1. **[Use sample]\(サンプルを使用する\)** をクリックします。
1. 新しいサンプル SQL スクリプトが開きます。
1. 最初のクエリ (28 行目から 32 行目) までスクロールして、クエリ テキストを選択します。
1. [実行] をクリックします。 選択したコードだけが実行されます。

## <a name="loading-more-nyc-taxi-data"></a>ニューヨーク市のタクシー データの追加の読み込み

1. **[Knowledge Center]** に移動し、 **[ギャラリーを参照]** をクリックします。
1. 上部にある **[SQL スクリプト]** タブを選択します。
1. **[Load the New York Taxicab dataset]\(ニューヨークのタクシー データセットを読み込む\)** データ インジェスト サンプルを選択し、 **[続行]** をクリックします。
1. **[SQL プール]** で、 **[既存のプールを選択する]** を選択して **[SQLPOOL1]** を選択し、前に作成した **SQLPOOL1** データベースを選択します。
1. **[スクリプトを開く]** をクリックします。
1. 新しいサンプル SQL スクリプトが開きます。
1. **[実行]**
1. これにより、すべてのニューヨーク市のタクシー データに対して複数のテーブルが作成され、T-SQL COPY コマンドを使用して読み込まれます。 前のクイック スタートの手順でこれらのテーブルを作成済みの場合は、存在しないテーブルの CREATE と COPY を実行するコードだけを選択して実行します。

    > [!NOTE] 
    > 専用 SQL プール (以前の SQL DW) に対する SQL スクリプトのサンプル ギャラリーを使用する場合、使用できるのは、既存の専用 SQL プール (以前の SQL DW) だけです。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [管理者を追加する](get-started-add-admin.md)

