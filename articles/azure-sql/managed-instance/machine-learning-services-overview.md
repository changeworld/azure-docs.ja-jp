---
title: Azure SQL Managed Instance の Machine Learning Services
description: この記事では、Azure SQL Managed Instance の概要または Machine Learning Services について説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 03/17/2021
ms.openlocfilehash: 94495144c64b3770995a5f67e9129b3ba86e741e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599563"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance の Machine Learning Services

Machine Learning Services は Azure Python Managed Instance の機能であり、データベース内の機械学習を提供し、Python スクリプトと R スクリプトの両方をサポートしています。 その機能には、高パフォーマンスの予測分析と機械学習のための Microsoft Python および R のパッケージが含まれています。 ストアド プロシージャ、Python または R ステートメントを含む T-SQL、または T-SQL を含む Python または R コードにより、スクリプトでリレーショナル データを使用できます。

## <a name="what-is-machine-learning-services"></a>Machine Learning Services とは

Azure SQL Managed Instance の Machine Learning Services を使用すると、Python および R スクリプトをデータベース内で実行できます。 この機能を使用して、データの準備とクリーンアップ、特徴エンジニアリング、およびデータベース内での機械学習モデルのトレーニング、評価、およびデプロイを行うことができます。 この機能により、データが存在する場所でスクリプトが実行され、ネットワークを介して別のサーバーにデータが転送されなくなります。

Azure SQL Managed Instance で R および Python をサポートする Machine Learning Services を使用すると、次のことを行うことができます。

- **R および Python スクリプトを実行してデータ準備と汎用データ処理を行う** - 自作の R および Python スクリプトをデータが存在する Azure SQL Managed Instance に取り込むことができるようになりました。R および Python スクリプトを実行するためにデータを他のサーバーに移動する必要はありません。 データ移動の必要性と、遅延、セキュリティ、コンプライアンスに関連する関連問題を排除できます。

- **データベースで機械学習モデルをトレーニングする** - 任意のオープン ソース アルゴリズムを使用してモデルをトレーニングできます。 データベースから引き出されたサンプル データセットに依存するのではなく、トレーニングをデータセット全体に簡単にスケーリングできます。

- **モデルとスクリプトをストアド プロシージャの運用環境にデプロイする** - スクリプトとトレーニング済みモデルは、T-SQL ストアド プロシージャに埋め込むだけで運用できます。 Azure SQL Managed Instance に接続するアプリでは、ストアド プロシージャを呼び出すだけで、このようなモデルの予測とインテリジェンスを活用できます。 また、ネイティブの T-SQL PREDICT 関数を使用して、モデルを操作可能にして、高度な並行リアルタイム スコアリング シナリオで高速スコアリングを行うこともできます。

Python と R のベース ディストリビューションは Machine Learning Services に含まれています。 Python 用の Microsoft パッケージ [revoscalepy](/sql/machine-learning/python/ref-py-revoscalepy) および [microsoftml](/sql/machine-learning/python/ref-py-microsoftml) と、R 用の [RevoScaleR](/sql/machine-learning/r/ref-r-revoscaler)、[MicrosoftML](/sql/machine-learning/r/ref-r-microsoftml)、[olapR](/sql/machine-learning/r/ref-r-olapr)、および [sqlrutils](/sql/machine-learning/r/ref-r-sqlrutils) に加え、PyTorch、TensorFlow、scikit-learn などのオープンソースのパッケージとフレームワークをインストールおよび使用できます。

## <a name="how-to-enable-machine-learning-services"></a>Machine Learning Services を有効にする方法

次の SQL コマンドで拡張性を有効にすることで、Azure SQL Managed Instance で Machine Learning Services を有効にできます (SQL Managed Instance は再起動され、数秒使用できなくなります)。

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

このコマンドが SQL Managed Instance リソースに与える影響の詳細については、「[リソース管理](machine-learning-services-differences.md#resource-governance)」を参照してください。

### <a name="enable-machine-learning-services-in-a-failover-group"></a>フェールオーバー グループで Machine Learning Services を有効にする

[フェールオーバー グループ](failover-group-add-instance-tutorial.md)では、システム データベースはセカンダリ インスタンスにレプリケートされません (詳細については、「[フェールオーバー グループの制限](../database/auto-failover-group-overview.md#limitations-of-failover-groups)」を参照してください)。

使用している Managed Instance がフェールオーバー グループの一部である場合は、次の手順を実行します。

- `sp_configure` および `RECONFIGURE` コマンドをフェールオーバー グループの各インスタンスで実行して、Machine Learning Services を有効にします。

- R/Python ライブラリをマスター データベースではなくユーザー データベースにインストールします。

## <a name="next-steps"></a>次のステップ

- [SQL Server Machine Learning Services との重要な違い](machine-learning-services-differences.md)に関する記事を参照してください。
- Machine Learning Services で Python を使用する方法については、[Python スクリプトの実行](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)に関するページを参照してください。
- Machine Learning Services で R を使用する方法については、[R スクリプトの実行](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)に関するページを参照してください。
- 他の SQL プラットフォームの機械学習に関する詳細については、[SQL 機械学習のドキュメント](/sql/machine-learning/index)を参照してください。
