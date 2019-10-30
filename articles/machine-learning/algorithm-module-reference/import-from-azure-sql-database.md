---
title: Azure SQL Database からのインポート
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning ビジュアル インターフェイスの Import Data (データのインポート) モジュールを使用して、Azure SQL Database からデータを取得する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 374776088d93813f39122f2018b00466c55d2b6e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2019
ms.locfileid: "72694259"
---
# <a name="import-from-azure-sql-database"></a>Azure SQL Database からのインポート

この記事では、Azure Machine Learning ビジュアル インターフェイスの [Import Data (データのインポート)](import-data.md) モジュールを使用して、Azure SQL Database からデータを取得する方法について説明します。  

データベースからデータをインポートするには、サーバー名とデータベース名の両方を指定し、テーブル、ビュー、またはクエリを定義する SQL ステートメントを指定する必要があります。  

一般に、Azure データベースにデータを格納すると、Azure でテーブルや BLOB を使用するよりもコストが高くなります。 また、サブスクリプションの種類によっては、データベースに格納できるデータ量に制限がある場合もあります。 ただし、Azure SQL Database に対するトランザクション料金は発生しないため、このオプションは、データ参照テーブルやデータ ディクショナリなど、頻繁に使用される少量の情報に高速でアクセスするのに最適です。

Azure データベースにデータを格納することは、データを読み取る前にフィルター処理できるようにする必要がある場合や、レポートを作成するために予測やメトリックをデータベースに保存したい場合にも適しています。

## <a name="how-to-import-data-from-azure-sql-database"></a>Azure SQL Database からデータをインポートする方法

1. [Import Data](import-data.md) (データのインポート) モジュールをパイプラインに追加します。 このモジュールは、ビジュアル インターフェイスの [Data Input and Output]\(データの入力と出力\) カテゴリにあります。

1. **[Data source]\(データ ソース\)** として **[Azure SQL Database]** を選択します。

1. Azure SQL Database に固有の次のオプションを設定します。

    **データベース サーバー名**: Azure によって生成されるサーバー名を入力します。 通常、`<generated_identifier>.database.windows.net` の形式になります。

    **データベース名**: 指定したサーバー上の既存のデータベースの名前を入力します。

    **サーバー ユーザー アカウント名**: データベースのアクセス許可を持つアカウントのユーザー名を入力します。

    **Server ユーザー アカウントのパスワード**: 指定したユーザー アカウントのパスワードを入力します。

    **データベース クエリ**:読み取るデータを記述する SQL ステートメントを入力するか、貼り付けます。 Visual Studio サーバー エクスプローラーや SQL Server Data Tools などのツールを使用して、常に SQL ステートメントを検証し、クエリ結果を事前に確認してください。

1. Azure Machine Learning に読み込んだデータセットが、パイプラインの実行間で変更されないと予想される場合は、 **[Use cached results]\(キャッシュされた結果を使用する\)** オプションを選択します。

    これを選択すると、モジュール パラメーターに他の変更がない場合、パイプラインは、モジュールが最初に実行されたときにデータを読み込み、その後は、データセットのキャッシュ バージョンを使用します。

    パイプラインを繰り返すたびにデータセットを再読み込みする場合は、このオプションを選択解除します。 データセットは、[Import Data (データのインポート)](import-data.md) でパラメーターが変更されるたびにソースから再読み込みされます。

1. パイプラインを実行します。

    [Import Data (データのインポート)](import-data.md) でデータがビジュアル インターフェイスに読み込まれると、ソース データベースで使用されているデータ型によっては、暗黙的な型変換が実行されることがあります。

## <a name="results"></a>結果

インポートが完了したら、出力データセットをクリックし、 **[Visualize]\(視覚化\)** を選択して、データが正常にインポートされたかどうかを確認します。

必要に応じて、ビジュアル インターフェイスのツールを使用して、データセットとそのメタデータを変更できます。

- [Edit Metadata (メタデータの編集)](edit-metadata.md) を使用して、列名を変更したり、列を別のデータ型に変換したり、ラベルや特徴として使用する列を指定したりします。

- 列のサブセットを選択するには、[Select Columns in Dataset (データセット内の列の選択)](select-columns-in-dataset.md) を使用します。

- [Partition and Sample (パーティションとサンプル)](partition-and-sample.md) を使用して、条件によってデータセットを分割したり、上位 n 行を取得したりします。

## <a name="next-steps"></a>次の手順

Azure Machine Learning service で[使用できる一連のモジュール](module-reference.md)を参照してください。 
