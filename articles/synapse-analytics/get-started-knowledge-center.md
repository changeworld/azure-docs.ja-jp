---
title: 'チュートリアル: Synapse ナレッジ センターの探索を開始する'
description: このチュートリアルでは、Synapse ナレッジ センターを使用する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/04/2021
ms.openlocfilehash: ba6023ccc1582650e74da483aeefc48d2a305bef
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2021
ms.locfileid: "111554754"
---
# <a name="explore-the-synapse-knowledge-center"></a>Synapse Knowledge Center を探索する

このチュートリアルでは、Synapse Studio の **ナレッジ センター** を使用する方法について説明します。

## <a name="finding-the-knowledge-center"></a>ナレッジ センターへのアクセス

Synapse Studio で **ナレッジ センター** を見つけるには、次の 2 つの方法があります。

  1. ページ右上付近にある [Home]\(ホーム\) ハブの **[Learn]\(詳細\)** をクリックします。
  2. 上部のメニュー バーで、 **[?]** 、 **[Knowledge center]\(ナレッジ センター\)** の順にクリックします。

いずれかの方法を選択して、**ナレッジ センター** を開きます。

## <a name="exploring-the-knowledge-center"></a>Knowledge Center の探索

表示されると、 **[Knowledge Center]** で次の 3 つの操作を実行できることがわかります。
* **[Use samples immediately]\(サンプルをすぐに使用する\)** 。 Synapse の機能の例をすぐに確認したい場合は、このオプションを選択します。
* **[ギャラリーを参照]** 。 このオプションを使用すると、サンプル データ セットをリンクして、SQL スクリプト、ノートブック、パイプラインの形式でサンプル コードを追加できます。
* **[Tour Synapse studio]\(Synapse Studio のツアーを見る\)** 。 このオプションでは、Synapse Studio の基本的な部分についての簡単なツアーを見ることができます。 これは、Synapse Studio をこれまで使用したことがない場合に役立ちます。

## <a name="use-samples-immediately-three-samples-to-help-you-get-started-fast"></a>サンプルをすぐに使用する: すばやく開始するのに役立つ 3 つのサンプル

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

## <a name="gallery-a-collection-of-sample-datasets-and-sample-code"></a>ギャラリー: サンプル データセットとサンプル コードのコレクション

1. **[Knowledge center]\(ナレッジ センター\)** に移動し、 **[Browse gallery]\(ギャラリーを参照\)** をクリックします。
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

