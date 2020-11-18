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
ms.date: 10/07/2020
ms.openlocfilehash: f7b96bcebb2106e52c62426ca2b64f9305e09141
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515411"
---
# <a name="creating-a-synapse-workspace"></a>Synapse ワークスペースの作成

このチュートリアルでは、Synapse ワークスペース、専用 SQL プール、サーバーレス Apache Spark プールを作成する方法について説明します。 

## <a name="prerequisites"></a>前提条件

このチュートリアルの手順を完了するには、**所有者** ロールが割り当てられているリソース グループにアクセスできる必要があります。 このリソース グループで Synapse ワークスペースを作成します。

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Azure portal で Synapse ワークスペースを作成する

1. [Azure portal](https://portal.azure.com) を開き、上部で **Synapse** を検索します。
1. 検索結果の **[サービス]** で、 **[Azure Synapse Analytics (ワークスペース プレビュー)]** を選択します。
1. **[追加]** を選択してワークスペースを作成します。
1. **[基本]** で、使用する **[サブスクリプション]** 、 **[リソース グループ]** 、 **[リージョン]** を入力し、ワークスペース名を選択します。 このチュートリアルでは、**myworkspace** を使用します。
1. **[Data Lake Storage Gen 2 の選択]** に移動します。 
1. **[新規作成]** をクリックし、「**contosolake**」という名前を付けます。
1. **[ファイル システム]** をクリックし、**users** という名前を付けます。 これにより、**users** というコンテナーが作成されます。
1. ワークスペースでは、このストレージ アカウントを Spark テーブルおよび Spark アプリケーション ログの "プライマリ" ストレージ アカウントとして使用します。
1. **[確認と作成]**  >  **[作成]** の順に選択します。 ワークスペースの準備は数分で完了します。

## <a name="open-synapse-studio"></a>Synapse Studio を開く

Azure Synapse ワークスペースが作成された後、Synapse Studio を開く方法は 2 つあります。

* [Azure portal](https://portal.azure.com) で Synapse ワークスペースを開きます。 **[概要]** セクションの上部にある **[Synapse Studio の起動]** を選択します。
* `https://web.azuresynapse.net` にアクセスし、ワークスペースにサインインします。

## <a name="create-a-dedicated-sql-pool"></a>専用 SQL プールを作成する

1. Synapse Studio の左側のペインで、 **[管理]**  >  **[SQL プール]** を選択します。
1. **[新規]** を選択し、次の設定を入力します。

    |設定 | 推奨値 | 
    |---|---|---|
    |**SQL プール名**| **SQLDB1**|
    |**パフォーマンス レベル**|**DW100C**|
    |||

1. **[確認と作成]**  >  **[作成]** の順に選択します。 専用 SQL プールの準備は数分で完了します。 専用 SQL プールは、**SQLDB1** とも呼ばれる専用 SQL プール データベースに関連付けられます。

専用 SQL プールがアクティブである限り、課金対象のリソースが消費されます。 コストを削減するために、後でプールを一時停止できます。

## <a name="create-a-serverless-apache-spark-pool"></a>サーバーレス Apache Spark プールを作成する

1. Synapse Studio の左側のペインで、 **[管理]**  >  **[Apache Spark プール]** を選択します。
1. **[新規]** を選択し、次の設定を入力します。

    |設定 | 推奨値 | 
    |---|---|---|
    |**[Apache Spark pool name]\(Apache Spark プール名\)**|**Spark1**
    |**ノード サイズ**| **Small**|
    |**[Number of nodes]\(ノードの数\)**| 最小数を 3 に、最大数を 3 に設定します|

1. **[確認と作成]**  >  **[作成]** の順に選択します。 Apache Spark プールの準備は数秒で完了します。

Azure Synapse で Spark アクティビティを実行する際は、使用する Spark プールを指定します。 そのプールから Azure Synapse に対して、使用する Spark リソースの数が指示されます。 支払いの対象となるのは、使用したリソースの分だけです。 プールの使用を能動的に停止すると、リソースは自動的にタイムアウトし、リサイクルされます。

> [!NOTE]
> Spark データベースは Spark プールから独立して作成されます。 ワークスペースには必ず、**既定** と呼ばれる Spark データベースが存在します。 追加の Spark データベースを作成することができます。

## <a name="the-serverless-sql-pool"></a>サーバーレス SQL プール

各ワークスペースには、**組み込み** と呼ばれる、事前構築されたプールが付属しています。 このプールを削除することはできません。 サーバーレス SQL プールを使用すると、Azure Synapse でサーバーレス SQL プールを作成したり、その管理に配慮したりすることなく、SQL を操作できます。 専用 SQL プールとは異なり、サーバーレス SQL プールの課金は、クエリの実行に使用されたリソースの数ではなく、クエリを実行するためにスキャンされたデータの量に基づきます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [専用 SQL プールを使用して分析する](get-started-analyze-sql-pool.md)
