---
title: クイックスタート - Apache Spark プールを作成する (プレビュー)
description: このガイドの手順に従って、Azure Synapse Analytics ワークスペース用の新しい Apache Spark プールを作成します。
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 1457549fef3a78356c8b1af6be620fdf30ddab46
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420626"
---
# <a name="quickstart-create-a-new-apache-spark-pool-preview"></a>クイック スタート:新しい Apache Spark プールを作成する (プレビュー)

Synapse Analytics には、データの取り込み、変換、モデル化、分析、および提供に役立つさまざまな分析エンジンが用意されています。 Apache Spark プールは、オープンソースのビッグ データ コンピューティング機能を提供します。 お使いの Synapse ワークスペースに Apache Spark プールを作成した後、データを読み込み、モデル化し、処理し、提供して分析情報を得ることができます。

このクイックスタートでは、Azure portal を使用して Synapse ワークスペースに Apache Spark プールを作成する方法を学習します。

> [!IMPORTANT]
> Spark インスタンスの料金は、それを使用しているかどうかに関係なく、分単位で課金されます。 必ず、Spark インスタンスの使用を終了した後にシャットダウンするか、短いタイムアウトを設定してください。 詳しくは、この記事の「**リソースのクリーンアップ**」をご覧ください。

Azure サブスクリプションをお持ちでない場合は、[開始する前に無料アカウントを作成](https:/azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https:/azure.microsoft.com/free/)
- [Synapse Analytics ワークスペース](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure ポータル](https:/portal.azure.com/)

## <a name="create-new-apache-spark-pool"></a>新しい Apache Spark プールを作成する

1. Apache Spark プールを作成する Synapse ワークスペースで、 **[新しい Apache Spark プール]** をクリックします。
![新しい Apache Spark プールを作成するためのコマンドが赤い枠で囲まれた Synapse ワークスペースの概要](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-01.png)
2. **[基本]** タブで、以下の詳細を入力します。

    |設定 | 推奨値 | 説明 |
    | :------ | :-------------- | :---------- |
    | **[Apache Spark pool name]\(Apache Spark プール名\)** | 有効なプール名 | これは、Apache Spark プールの名前です。 |
    | **ノード サイズ** | Small (4 vCPU / 32 GB) (S (4 vCPU/32 GB)) | このクイックスタートのコストを削減するために、最小サイズに設定します |
    | **Autoscale** | Enabled | この既定の設定のままにします |
    | **[Number of nodes]\(ノードの数\)** | 3 - 40 | この既定の設定のままにします |
    ||||

    ![Apache Spark プール作成のフロー - [基本] タブ。](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-02.png)
    > [!IMPORTANT]
    > Apache Spark プールで使用できる名前には、特定の制限があることに注意してください。 名前は、文字または数字のみを含み、15 文字以下である必要があります。さらに、文字で始まり、予約語を含まず、ワークスペース内で一意である必要があります。

3. **[Next: additional settings]\(次へ: 追加設定\)** をクリックして、既定の設定を確認します。 既定の設定は変更しないでください。
![Apache Spark プールの作成フロー - [additional settings]\(追加設定\) タブ。](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03.png)

4. **[Next: tags]\(次へ: タグ\)** をクリックします。 タグは追加しないでください。
![Apache Spark プールの作成フロー - [additional settings]\(追加設定\) タブ。](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03-tags.png)

5. **[Review + create]\(レビュー + 作成\)** をクリックします。

6. 以前に入力した内容に基づいて詳細が正しいことを確認し、 **[作成]** をクリックします。
![Apache Spark プールの作成フロー - 設定確認のタブ。](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-05.png)

7. この時点で、リソースのプロビジョニング フローが開始されます。完了すると、その旨が表示されます ![Apache Spark プールの作成フロー - リソースのプロビジョニング。](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-06.png)

8. プロビジョニングが完了した後にワークスペースに戻ると、新しく作成された Apache Spark プールの新しいエントリが表示されます。
 ![Apache Spark プールの作成フロー - リソースのプロビジョニング。](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-07.png)

9. この時点で、実行されているリソースはなく、Spark の料金は発生していません。この時点で、作成する Spark インスタンスに関するメタデータが作成されています。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次の手順に従って、ワークスペースから Apache Spark プールを削除します。
> [!WARNING]
> Apache Spark プールを削除すると、ワークスペースから分析エンジンが削除されます。 プールに接続することはできなくなります。また、この Apache Spark プールを使用するすべてのクエリ、パイプライン、ノートブックは動作しなくなります。

Apache Spark プールを削除する場合は、次の手順を実行します。

1. ワークスペースの [Apache Spark プール] ブレードに移動します。
2. 削除する Apache プール (この場合は **contosospark**) を選択します
3. **[削除]** を押します。
 ![Apache Spark プールの一覧。最近作成されたプールが選択されています。](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-08.png)
4. 削除を確認し、 **[削除]** を押します。
 ![選択した Apache Spark プールの削除を確認するダイアログ。](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-10.png)
5. プロセスが正常に完了すると、Apache Spark プールはワークスペース リソースの一覧に表示されなくなります。

作成された SQL プールは、データの読み込み、ストリームの処理、レイクからの読み取りなどのために、ワークスペースで使用できるようになります。

## <a name="next-steps"></a>次のステップ

- 「[クイック スタート:Web ツールを使用した Synapse Studio での Apache Spark プールの作成](spark/apache-spark-notebook-create-spark-use-sql.md)。
- 「[クイック スタート:Azure portal を使用した Synapse SQL プールの作成](quickstart-create-sql-pool.md)。
