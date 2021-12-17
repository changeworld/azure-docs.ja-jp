---
title: 'クイックスタート: Apache Spark のジョブ定義を使用してデータを変換する'
description: このチュートリアルでは、Azure Synapse Analytics で Apache Spark ジョブ定義を使用してデータを変換するための詳細な手順を説明します。
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/13/2021
ms.openlocfilehash: 2b7392af7a00ec68ad263b7c930842714c94fe1d
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2021
ms.locfileid: "112967719"
---
# <a name="quickstart-transform-data-using-apache-spark-job-definition"></a>クイックスタート: Apache Spark のジョブ定義を使用してデータを変換する

このクイックスタートでは、Azure Synapse Analytics で Apache Spark ジョブ定義を使用してパイプラインを作成します。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* **Azure Synapse ワークスペース**:「[クイックスタート:Synapse ワークスペースを作成する](quickstart-create-workspace.md)」の手順に従い、Azure portal を使用して Synapse ワークスペースを作成します。
* **Apache Spark ジョブ定義**: 「[チュートリアル: Synapse Studio で Apache Spark ジョブ定義を作成する](spark/apache-spark-job-definitions.md)」の手順に従い、Synapse ワークスペースで Apache Spark ジョブ定義を作成します。


### <a name="navigate-to-the-synapse-studio"></a>Synapse Studio に移動する

Azure Synapse ワークスペースが作成された後、Synapse Studio を開く方法は 2 つあります。

* [Azure portal](https://ms.portal.azure.com/#home) で Synapse ワークスペースを開きます。 [はじめに] の下の [Synapse Studio を開く] カードで、 **[開く]** を選択します。
* [Azure Synapse Analytics](https://web.azuresynapse.net/) を開き、ワークスペースにサインインします。

このクイックスタートでは、例として "sampletest" という名前のワークスペースを使用します。 自動的に Synapse Studio のホーム ページに移動します。

![Synapse Studio のホーム ページ](media/quickstart-transform-data-using-spark-job-definition/synapse-studio-home.png)

## <a name="create-a-pipeline-with-an-apache-spark-job-definition"></a>Apache Spark ジョブ定義を使用してパイプラインを作成する

パイプラインには、一連のアクティビティを実行するための論理フローが含まれています。 このセクションでは、Apache Spark ジョブ定義アクティビティを含んだパイプラインを作成します。

1. **[統合]** タブに移動します。パイプライン ヘッダーの横にあるプラス符号のアイコンを選択し、 **[パイプライン]** を選択します。

     ![新しいパイプラインを作成する](media/doc-common-process/new-pipeline.png)

2. パイプラインの **[プロパティ]** 設定ページで、**名前** として「**demo**」と入力します。

3. **[アクティビティ]** ペインの **[Synapse]** で、 **[Spark job definition]\(Spark ジョブ定義\)** をパイプライン キャンバス上にドラッグします。

     ![Spark ジョブ定義をドラッグする](media/quickstart-transform-data-using-spark-job-definition/drag-spark-job-definition.png)


## <a name="set-apache-spark-job-definition-canvas"></a>Apache Spark ジョブ定義キャンバスを設定する

Apache Spark ジョブ定義を作成すると、自動的に Spark ジョブ定義キャンバスに切り替わります。

### <a name="general-settings"></a>全般設定

1. キャンバスで Spark ジョブ定義モジュールを選択します。

2. **[全般]** タブで、 **[名前]** に「**sample**」と入力します。

3. (省略可) 説明を入力することもできます。

4. タイムアウト: アクティビティを実行できる最大時間。 既定値は 7 日です。これは許容される最大時間でもあります。 形式は D.HH:MM:SS です。

5. 再試行: 最大再試行回数。

6. 再試行の間隔: 何秒おきに再試行するか。

7. セキュリティで保護された出力: オンにした場合、アクティビティからの出力がログにキャプチャされません。

8. セキュリティで保護された入力: オンにした場合、アクティビティからの入力がログにキャプチャされません。

     ![Spark ジョブ定義の [全般]](media/quickstart-transform-data-using-spark-job-definition/spark-job-definition-general.png)

### <a name="settings-tab"></a>Settings tab 

このパネルでは、実行する Spark ジョブ定義を参照できます。

* Spark ジョブ定義リストを展開し、既存の Apache Spark ジョブ定義を選択できます。 **[New]\(新規\)** ボタンを選択して新しい Apache Spark ジョブ定義を作成し、実行する Spark ジョブ定義を参照することもできます。

* **[New]\(新規\)** ボタンをクリックしてコマンドライン引数を追加できます。 コマンドライン引数を追加すると、Spark ジョブ定義で設定したコマンドライン引数がオーバーライドされることに注意してください。 <br> *サンプル: `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result`* <br>

     ![Spark ジョブ定義のパイプライン設定](media/quickstart-transform-data-using-spark-job-definition/spark-job-definition-pipline-settings.png)

* **[動的なコンテンツの追加]** ボタンをクリックするか、ショートカット キー <kbd>Alt</kbd>+<kbd>Shift</kbd>+<kbd>D</kbd> を押すことで動的コンテンツを追加できます。 **[動的なコンテンツの追加]** ページでは、式、関数、システム変数を自由に組み合わせて動的なコンテンツを追加できます。

     ![動的なコンテンツの追加](media/quickstart-transform-data-using-spark-job-definition/add-dynamic-content.png)

### <a name="user-properties-tab"></a>[ユーザーのプロパティ] タブ

このパネルでは、Apache Spark ジョブ定義アクティビティのプロパティを追加できます。

![ユーザーのプロパティ](media/quickstart-transform-data-using-spark-job-definition/user-properties.png)

## <a name="next-steps"></a>次のステップ

次の記事に進んで、Azure Synapse Analytics のサポートを確認します。

> [!div class="nextstepaction"]
> [パイプラインとアクティビティ](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
> [マッピング データ フローの概要](../data-factory/concepts-data-flow-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
> [データ フローの式言語](../data-factory/data-flow-expression-functions.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
