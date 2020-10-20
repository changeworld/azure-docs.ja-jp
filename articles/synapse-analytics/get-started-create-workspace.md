---
title: チュートリアル:Synapse ワークスペース作成の概要
description: このチュートリアルでは、Synapse ワークスペース、SQL プール、Apache Spark プールを作成する方法について説明します。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 10/07/2020
ms.openlocfilehash: d74c3e42317b954a510f3276db38c0dcdf5e2362
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91850381"
---
# <a name="creating-a-synapse-workspace"></a>Synapse ワークスペースの作成

このチュートリアルでは、Synapse ワークスペース、SQL プール、Apache Spark プールを作成する方法について説明します。 

## <a name="prerequisites"></a>前提条件

このチュートリアルの手順をすべて完了するには、**所有者**ロールが割り当てられているリソース グループにアクセスできる必要があります。 このリソース グループで Synapse ワークスペースを作成します。

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Azure portal で Synapse ワークスペースを作成する

1. [Azure portal](https://portal.azure.com) を開き、上部で **Synapse** を検索します。
1. 検索結果の **[サービス]** で、 **[Azure Synapse Analytics (ワークスペース プレビュー)]** を選択します。
1. **[追加]** を選択してワークスペースを作成します。
1. **[基本]** で、使用する **[サブスクリプション]** 、 **[リソース グループ]** 、 **[リージョン]** を入力し、ワークスペース名を選択します。 このチュートリアルでは、**myworkspace** を使用します。
1. ワークスペースを作成するには、ADLSGEN2 アカウントと、そのアカウント内のコンテナーが必要です。 最も簡単な方法は、新しいものを作成することです。 既存のものを再利用する場合は、追加の構成を行う必要があります。 
    1. Synapse ワークスペースは、Spark ログと Spark テーブルのデータを格納するための既定の場所として、このコンテナーを使用します。
1. オプション 1: 新しい ADLSGEN2 アカウントの作成 
    1. **[Data Lake Storage Gen 2 の選択]** に移動します。 
    1. **[新規作成]** をクリックし、「**contosolake**」という名前を付けます。
    1. **[ファイル システム]** をクリックし、**users** という名前を付けます。 これにより、**users** というコンテナーが作成されます。
1. オプション 2: 既存の ADLSGEN2 アカウントの使用。 このドキュメントの下部にある「**ADLSGEN2 ストレージ アカウントの準備**」の手順を参照してください。
1. Azure Synapse ワークスペースは、このストレージ アカウントを "プライマリ" ストレージ アカウントとして使用し、ワークスペース データを格納するコンテナーを使用します。 このワークスペースでは、データが Apache Spark テーブルに格納されます。 **/synapse/workspacename** というフォルダーに Spark アプリケーション ログが格納されます。
1. **[確認と作成]**  >  **[作成]** の順に選択します。 ワークスペースの準備は数分で完了します。

## <a name="open-synapse-studio"></a>Synapse Studio を開く

Azure Synapse ワークスペースが作成された後、Synapse Studio を開く方法は 2 つあります。

* [Azure portal](https://portal.azure.com) で Synapse ワークスペースを開きます。 **[概要]** セクションの上部にある **[Synapse Studio の起動]** を選択します。
* `https://web.azuresynapse.net` にアクセスし、ワークスペースにサインインします。

## <a name="create-a-sql-pool"></a>SQL プールを作成する

1. Synapse Studio の左側のペインで、 **[管理]**  >  **[SQL プール]** を選択します。
1. **[新規]** を選択し、次の設定を入力します。

    |設定 | 推奨値 | 
    |---|---|---|
    |**SQL プール名**| **SQLDB1**|
    |**パフォーマンス レベル**|**DW100C**|
    |||

1. **[確認と作成]**  >  **[作成]** の順に選択します。 SQL プールの準備は数分で完了します。 SQL プールは、**SQLDB1** とも呼ばれる SQL プール データベースに関連付けられます。

SQL プールがアクティブである限り、課金対象のリソースが消費されます。 コストを削減するために、後でプールを一時停止できます。

## <a name="create-an-apache-spark-pool"></a>Apache Spark プールを作成する

1. Synapse Studio の左側のペインで、 **[管理]**  >  **[Apache Spark プール]** を選択します。
1. **[新規]** を選択し、次の設定を入力します。

    |設定 | 推奨値 | 
    |---|---|---|
    |**[Apache Spark pool name]\(Apache Spark プール名\)**|**Spark1**
    |**ノード サイズ**| **Small**|
    |**[Number of nodes]\(ノードの数\)**| 最小数を 3 に、最大数を 3 に設定します|

1. **[確認と作成]**  >  **[作成]** の順に選択します。 Apache Spark プールの準備は数秒で完了します。

> [!NOTE]
> 名前に反して、Apache Spark プールは SQL プールに似ていません。 これは、Azure Synapse ワークスペースに対して Spark との対話方法を指示するために使用する基本的なメタデータにすぎません。

メタデータであるため、Spark プールを開始したり停止したりすることはできません。

Azure Synapse で Spark アクティビティを実行する際は、使用する Spark プールを指定します。 そのプールから Azure Synapse に対して、使用する Spark リソースの数が指示されます。 支払いの対象となるのは、使用したリソースの分だけです。 プールの使用を能動的に停止すると、リソースは自動的にタイムアウトし、リサイクルされます。

> [!NOTE]
> Spark データベースは Spark プールから独立して作成されます。 ワークスペースには必ず、**既定**と呼ばれる Spark データベースが存在します。 追加の Spark データベースを作成することができます。

## <a name="the-sql-on-demand-pool"></a>SQL オンデマンド プール

各ワークスペースには、**SQL オンデマンド**というあらかじめ構築されたプールが付属しています。 このプールを削除することはできません。 SQL オンデマンド プールを使用すると、Azure Synapse で SQL プールを作成したり、その管理を検討したりすることなく、SQL を操作できます。

他の種類のプールとは異なり、SQL オンデマンドの課金は、クエリの実行に使用されたリソースの数ではなく、クエリを実行するためにスキャンされたデータの量に基づきます。

* SQL オンデマンドには、SQL オンデマンド プールとは別に存在する独自の SQL オンデマンド データベースもあります。
* ワークスペースには必ず、**SQL オンデマンド**という名前の SQL オンデマンド プールが 1 つだけあります。

## <a name="preparing-a-adlsgen2-storage-account"></a>ADLSGEN2 ストレージ アカウントの準備

### <a name="perform-the-following-steps-before-you-create-your-workspace"></a>ワークスペースを作成する前に、次の手順を実行します

1. [Azure Portal](https://portal.azure.com)を開きます。
1. 既存のストレージ アカウントに移動します
1. 左ペインで **[アクセス制御 (IAM)]** を選択します。 
1. 以下のロールを割り当てるか、それらが既に割り当てられていることを確認します。
    * **所有者**ロールに自分自身を割り当てます。
    * **ストレージ BLOB データ所有者**ロールに自分自身を割り当てます。
1. 左ペインで **[コンテナー]** を選択し、コンテナーを作成します。
1. コンテナーには名前を付けることができます。 このドキュメントでは、「**users**」という名前を使用します。
1. 既定の設定である **[パブリック アクセス レベル]** をそのまま使用し、 **[作成]** を選択します。

### <a name="perform-the-following-steps-after-you-create-your-workspace"></a>ワークスペースの作成後に、次の手順を実行します

ワークスペースからストレージ アカウントへのアクセスを構成します。 Azure Synapse ワークスペースのマネージド ID には、既にストレージ アカウントへのアクセス権がある可能性があります。 確認するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) を開き、ワークスペース用に選択したプライマリ ストレージ アカウントを開きます。
1. 左ペインから **[アクセス制御 (IAM)]** を選択します。
1. 以下のロールを割り当てるか、それらが既に割り当てられていることを確認します。 ここでは、ワークスペース ID とワークスペース名に同じ名前を使用します。
    * ストレージ アカウントの **[ストレージ BLOB データ共同作成者]** ロールに、ワークスペース ID として **myworkspace** を割り当てます。
    * ワークスペース名として **myworkspace** を割り当てます。
1. **[保存]** を選択します。


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [SQL プールを使用して分析する](get-started-analyze-sql-pool.md)
