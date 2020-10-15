---
title: チュートリアル:Synapse Knowledge Center の探索を開始する
description: このチュートリアルでは、Synapse Knowledge Center を使用する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: 47b8c45e22569cc758d00fb8534f409ecebf58ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91299933"
---
# <a name="explore-the-synapse-knowledge-center"></a>Synapse Knowledge Center を探索する

このチュートリアルでは、Synapse Studio Knowledge Center を使用する方法について説明します。

## <a name="getting-to-the-knowledge-center"></a>Knowledge Center へのアクセス

Synapse Studio で Knowledge Center を検索するには、次の 2 つの方法があります。

  1. ホーム ハブで、[便利なリンク] の下にある **[Knowledge Center]** という最初のリンクをクリックします。
  2. 上部のメニュー バーで、 **[?]** 、 **[Knowledge Center]** の順にクリックします。

いずれかの方法を選択して、 **[Knowledge Center]** を開きます。

## <a name="overview"></a>概要

**Knowledge Center** では、次の 3 つを使用できます。
* **[Use samples immediately]\(サンプルをすぐに使用する\)** 。 このオプションは、実行中の分析を、可能な限り迅速に表示できるように最適化されています。 Synapse の機能の例をすぐに確認したい場合は、このオプションを選択します。
* **[Browser available sample]\(使用可能なサンプルを参照する\)** 。 このオプションを使用すると、サンプル データ セットをリンクして、SQL スクリプト、ノートブック、パイプラインの形式でサンプル コードを追加できます。
* **[Tour Synapse studio]\(Synapse Studio のツアーを見る\)** 。 このオプションでは、Synapse Studio の基本的な部分についての簡単なツアーを見ることができます。 これは、Synapse Studio をこれまで使用したことがない場合に役立ちます。

## <a name="exploring-blob-storage-with-sql-on-demand"></a>SQL オンデマンドを使用して BLOB ストレージを探索する

1. **[Knowledge Center]** で、 **[Use samples immediately]\(サンプルをすぐに使用する\)** をクリックします。
1. **[Query data with SQL]\(SQL を使用してデータを照会する\)** を選択します。 
1. **[Use samples immediately]\(サンプルをすぐに使用する\)** をクリックします。
1. これにより、新しい SQL スクリプトが作成されます。
1. 最初のクエリ (28 行目から 32 行目) までスクロールして、クエリ テキストを選択します。
1. [実行] をクリックします。 選択したテキストが実行されます。

## <a name="loading-more-nyc-taxi-data"></a>ニューヨーク市のタクシー データの追加の読み込み
1. **[Knowledge Center]** で、 **[Browse available sample]\(使用可能なサンプルを参照する\)** をクリックします。 
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
* [SQL オンデマンドを使用する](quickstart-sql-on-demand.md)
