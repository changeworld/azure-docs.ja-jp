---
title: 小売業界のデータ モデル向け ETL テンプレート
description: このチュートリアルでは、Adventure Works サンプル データを使用して、小売業向けの業界データ モデル テンプレートを使用するための手順について説明します。
author: kromerm
ms.author: aamerril
ms.service: synapse-analytics
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 10/18/2021
ms.openlocfilehash: 99f91c76addbacb64290c54435b5b3c760eabc5c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132704790"
---
# <a name="adventureworks-template-documentation"></a>AdventureWorks テンプレート ドキュメント

このドキュメントでは、Microsoft の AdventureWorks パイプライン テンプレートを設定して使用し、Azure Synapse Analytics と Retail データベース テンプレートを使用して AdventureWorks データセットの探索をすぐに開始する方法について説明します。

## <a name="overview"></a>概要
AdventureWorks は、Microsoft アプリケーションのデモに使用される架空のスポーツ用品小売業者です。 このケースでは、Synapse Pipelines を使用して小売データを Retail データベース テンプレートにマップし、Azure Synapse 内でさらなる分析を行う方法の例として使用しています。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* **Azure Synapse ワークスペース**。 [Azure Synapse ワークスペースをまだ持っていない場合は作成します](../storage/common/storage-account-create.md)。

## <a name="find-the-template"></a>テンプレートを見つける

テンプレートを見つけるには、次の手順を実行します。

1. Synapse ワークスペースに移動します。 ホームページから **[Learn]** を選択し、**[ギャラリーを参照]** を選択します。 Synapse ギャラリーが開きます。 ワークスペースにインストールするデータセット、スクリプト、パイプラインなどを検索することができます。 

1. **[パイプライン]** を選択し、「AdventureWorks」というキーワードで検索結果をフィルター処理します。

1. **AdventureWorks** テンプレートを選択し、**[続行]** を選択します。

これらの手順を実行すると、テンプレートの概要ページが表示されます。

## <a name="configure-the-template"></a>テンプレートを構成する
このテンプレートは最小限の構成で済むように設計されています。 テンプレートの概要ページからパイプラインの初期開始構成のプレビューを確認し、**[パイプラインを開く]** をクリックして、自分のワークスペースにリソースを作成することができます。 テンプレート内の 31 個のリソースがすべて作成されたという通知が表示され、コミットまたは発行前にこれらを確認することができます。 テンプレートには以下のコンポーネントがあります。

* 17 個のパイプライン: これらは、データがターゲット テーブルに正しく読み込まれるようにスケジュールされており、ソース テーブルごとの 1 つのパイプラインに加え、スケジュールされたものが含まれています。
* 14 個のデータ フロー: ソース システムからデータを読み込み、ターゲット データベースに配置するためのロジックが含まれています。

AdventureWorks データセットを別のデータベースに読み込んだ場合は、そのデータセットを指すようにデータフロー ソースを更新することができます。 それ以外の場合は、次の手順で、テンプレートに定義されているスキーマと一致するソース DB とターゲット DB を作成します。


## <a name="dataset-and-sourcetarget-models"></a>データセット、ソース モデル、ターゲット モデル
Excel 形式の AdventureWorks データセットは、この [GitHub サイト](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/AdventureWorks%20Data.zip)からダウンロードできます。 さらに、[ソース データベースとターゲット データベースの両方のスキーマ定義](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/AdventureWorksSchemas.xlsx)にアクセスできます。 Synapse のデータベース デザイナーを使用して、先ほどダウンロードした Excel のスキーマを使用してソース データベースとターゲット データベースを再作成します。 データベース デザイナーの詳細については、この[ドキュメント](../synapse-analytics/database-designer/concepts-database-templates.md)を参照してください。

データベースを作成したら、ワークスペース DB のソースとシンクの設定のドロップダウンを編集して、データ フローが正しいテーブルを指していることを確認します。 データをソース モデルに読み込むには、サンプル データセットに用意されている CSV ファイルを、テーブルに指定されている正しいフォルダーに配置します。 それが完了したら、後はパイプラインを実行するだけです。

## <a name="troubleshoot-the-pipelines"></a>パイプラインのトラブルシューティング
パイプラインが正常に実行されなかった場合にエラーを確認するには、主にいくつかの点があります。

* データセット スキーマ。 CSV ファイルのデータ設定が正確であることを確認します。 行ヘッダーを含めた場合は、データベース テーブルの [ヘッダーの表示] オプションがオンになっていることを確認します。
* データ フロー ソース。 スキーマ例に指定されているものと異なる列またはテーブル名を使用した場合は、データ フローのステップを実行して、列が正しくマップされていることを確認する必要があります。
* データ フロー シンク。 ターゲット データベース上のスキーマとデータ形式の構成は、データ フロー テンプレートと一致させる必要があります。 前述のように、何か変更があった場合は、それらの項目をそろえる必要があります。

## <a name="next-steps"></a>次のステップ

* [データ フローのマッピング](concepts-data-flow-overview.md)の詳細を確認します。
* [パイプライン テンプレート](solution-templates-introduction.md)の詳細を確認します。