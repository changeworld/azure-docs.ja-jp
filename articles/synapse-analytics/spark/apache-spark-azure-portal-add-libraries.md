---
title: Azure Synapse Analytics で Apache Spark 用のライブラリを追加および管理する
description: Azure Synapse Analytics で、Apache Spark によって使用されるライブラリを追加および管理する方法について説明します。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 83dfd1b4df37018329b5d7a707e9b65fdf0782a6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81426403"
---
# <a name="add-and-manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Azure Synapse Analytics で Apache Spark 用のライブラリを追加および管理する

Apache Spark は、機能を提供するために多くのライブラリに依存しています。 これらのライブラリは、拡張したり、追加のライブラリや古いライブラリの更新されたバージョンで置き換えたりできます。

Python パッケージは Spark プール (プレビュー) レベルで追加でき、.jar ベースのパッケージは Spark ジョブ定義レベルで追加できます。

## <a name="adding-or-updating-python-libraries"></a>Python ライブラリの追加または更新

Azure Synapse Analytics の Apache Spark には、Anacondas の完全インストールと追加のライブラリがあります。 完全なライブラリの一覧については、[Apache Spark のバージョンのサポート](apache-spark-version-support.md)に関するページを参照してください。

Spark インスタンスが起動すると、このインストールをベースとして使用して新しい仮想環境が作成されます。 さらに、*requirements.txt* ファイル (`pip freeze` コマンドの出力) を使用して、仮想環境をアップグレードできます。 このファイルにリストされているインストールまたはアップグレード用のパッケージは、クラスターの起動時に PyPi からダウンロードされます。 この要件ファイルは、その Spark プールから Spark インスタンスが作成されるたびに使用されます。

> [!IMPORTANT]
>
> - インストールするパッケージが大きいか、インストールに時間がかかる場合、これは Spark インスタンスのアップタイムに影響します。
> - インストール時にコンパイラのサポートを必要とするパッケージ (GCC など) はサポートされていません。
> - パッケージのダウングレードはできません。追加またはアップグレードのみが可能です。

### <a name="requirements-format"></a>要件の形式

次のスニペットは、要件ファイルの形式を示しています。 PyPi パッケージ名が正確なバージョンと共にリストされます。 このファイルは、[pip freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/) のリファレンス ドキュメントで説明されている形式に従います。 この例では、特定のバージョンを固定しています。 "より大きい" および "未満" のバージョンをこのファイルで指定することもできます。

```
absl-py==0.7.0

adal==1.2.1

alabaster==0.7.10
```

### <a name="python-library-user-interface"></a>Python ライブラリのユーザー インターフェイス

ライブラリを追加するための UI は、Azure portal の **[Create Apache Spark pool]\(Apache Spark プールの作成\)** ページの **[Additional settings]\(追加設定\)** タブにあります。

ページの **[パッケージ]** セクションにあるファイル セレクターを使用して、環境構成ファイルをアップロードします。

![Python ライブラリを追加する](./media/apache-spark-azure-portal-add-libraries/add-python-libraries.png "Python ライブラリを追加する")

## <a name="next-steps"></a>次のステップ

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark ドキュメント](https://spark.apache.org/docs/2.4.4/)
