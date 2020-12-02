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
ms.date: 11/16/2020
ms.openlocfilehash: 0314f5e55a476cd1cd1f8a31bd918e0ebb64c75f
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2020
ms.locfileid: "95254240"
---
# <a name="explore-the-synapse-knowledge-center"></a>Synapse Knowledge Center を探索する

このチュートリアルでは、Synapse Studio Knowledge Center を使用する方法について説明します。

## <a name="getting-to-the-knowledge-center"></a>Knowledge Center へのアクセス

Synapse Studio で Knowledge Center を検索するには、次の 2 つの方法があります。

  1. ページ右上付近にある [Home]\(ホーム\) ハブの **[Learn]\(詳細\)** をクリックします。
  2. 上部のメニュー バーで、 **[?]** 、 **[Knowledge Center]** の順にクリックします。

いずれかの方法を選択して、 **[Knowledge Center]** を開きます。

## <a name="overview"></a>概要

**Knowledge Center** では、次の 3 つを使用できます。
* **[Use samples immediately]\(サンプルをすぐに使用する\)** 。 Synapse の機能の例をすぐに確認したい場合は、このオプションを選択します。
* **[ギャラリーを参照]** 。 このオプションを使用すると、サンプル データ セットをリンクして、SQL スクリプト、ノートブック、パイプラインの形式でサンプル コードを追加できます。
* **[Tour Synapse studio]\(Synapse Studio のツアーを見る\)** 。 このオプションでは、Synapse Studio の基本的な部分についての簡単なツアーを見ることができます。 これは、Synapse Studio をこれまで使用したことがない場合に役立ちます。

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>サーバーレス SQL プールを使用して Blob Storage を探索する

1. **[Knowledge Center]** に移動して、 **[Use samples immediately]\(サンプルをすぐに使用する\)** をクリックします。
1. **[Query data with SQL]\(SQL を使用してデータを照会する\)** を選択します。 
1. **[Use samples immediately]\(サンプルをすぐに使用する\)** をクリックします。
1. これにより、新しい SQL スクリプトが作成されます。
1. 最初のクエリ (28 行目から 32 行目) までスクロールして、クエリ テキストを選択します。
1. [実行] をクリックします。 選択したテキストが実行されます。

## <a name="loading-more-nyc-taxi-data"></a>ニューヨーク市のタクシー データの追加の読み込み
1. **[ナレッジ センター]** に移動し、 **[ギャラリーを参照]** をクリックします。 
1. 上部にある **[SQL スクリプト]** タブを選択します。
1. **[ニューヨークのタクシー データを読み込む]** を選択します。
1. **[入力]** で、 **[既存のプールを選択する]** を選択し、 **[SQLDB1]** を選択します。
1. **[スクリプトを開く]** をクリックします。
1. 新しい SQL スクリプトが表示されます。
1. **[実行]**
1. これにより、すべてのニューヨーク市のタクシー データに対して複数のテーブルが作成され、T-SQL COPY コマンドを使用して読み込まれます。

## <a name="next-steps"></a>次のステップ

* [Azure Synapse Analytics の使用を開始する](get-started.md)
* [ワークスペースを作成する](quickstart-create-workspace.md)
* [サーバーレス SQL プールを使用する](quickstart-sql-on-demand.md)
