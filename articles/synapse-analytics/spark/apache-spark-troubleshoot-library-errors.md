---
title: ライブラリのインストール エラーのトラブルシューティング
description: このチュートリアルでは、ライブラリのインストール エラーをトラブルシューティングする方法の概要を説明します。
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: e812fa47d35889a9cf8c671a4df6034812272a6a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670623"
---
# <a name="troubleshoot-library-installation-errors"></a>ライブラリのインストール エラーのトラブルシューティング 
サード パーティまたはローカル環境でビルドされたコードをアプリケーションで使用できるようにするには、いずれかのサーバーレス Apache Spark プールにライブラリをインストールできます。 requirements.txt ファイルにリストされているパッケージは、プールの開始時に PyPi からダウンロードされます。 この要件ファイルは、その Spark プールから Spark インスタンスが作成されるたびに使用されます。 Spark プールにインストールされたライブラリは、同じプールを使用するすべてのセッションで使用できるようになります。 

場合によっては、インストールしようとしているライブラリが Apache Spark プールに表示されないことがあります。 このケースは、提供された requirements.txt または指定されたライブラリにエラーがある場合によく発生します。 ライブラリのインストール プロセスにエラーがあると、Apache Spark プールは、Synapse 基本ランタイムに指定されたライブラリに戻ります。

このドキュメントの目的は、一般的な問題について説明し、ライブラリのインストール エラーのデバッグに役立ててもらうことです。

## <a name="force-update-your-apache-spark-pool"></a>Apache Spark プールを強制的に更新する
Apache Spark プールでライブラリを更新した場合、プールを再起動するとそれらの変更が取得されます。 アクティブなジョブがある場合、それらのジョブは引き続き Spark プールの元のバージョンで実行されます。

**[Force new settings]\(新しい設定を強制\)** オプションを選択すると、強制的に変更を適用できます。 この設定により、選択した Spark プールの現在のセッションがすべて終了します。 セッションが終了したら、プールが再起動するまで待機する必要があります。 

![Python ライブラリを追加する](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Python ライブラリを追加する")

## <a name="validate-your-permissions"></a>アクセス許可を検証する
ライブラリをインストールおよび更新するには、Azure Synapse Analytics ワークスペースにリンクされているプライマリ Azure Data Lake Storage Gen2 ストレージ アカウントに対して、**ストレージ BLOB データ共同作成者** または **ストレージ BLOB データ所有者** のアクセス許可を持っている必要があります。

これらのアクセス許可があることを検証するには、次のコードを実行します。

```python
from pyspark.sql.types import StructType,StructField, StringType, IntegerType
data2 = [("James","Smith","Joe","4355","M",3000),
    ("Michael","Rose","Edward","40288","F",4000)
  ]

schema = StructType([ \
    StructField("firstname",StringType(),True), \
    StructField("middlename",StringType(),True), \
    StructField("lastname",StringType(),True), \
    StructField("id", StringType(), True), \
    StructField("gender", StringType(), True), \
    StructField("salary", IntegerType(), True) \
  ])
 
df = spark.createDataFrame(data=data2,schema=schema)

df.write.csv("abfss://<<ENTER NAME OF FILE SYSTEM>>@<<ENTER NAME OF PRIMARY STORAGE ACCOUNT>>.dfs.core.windows.net/validate_permissions.csv")

```
エラーが表示された場合は、必要なアクセス許可が不足している可能性があります。 必要なアクセス許可を取得する方法については、次のドキュメントを参照してください: [ストレージ BLOB データ共同作成者またはストレージ BLOB データ所有者のアクセス許可を割り当てる](../../storage/common/storage-auth-aad-rbac-portal.md#assign-an-azure-built-in-role)。

また、パイプラインを実行している場合は、ワークスペース MSI にもストレージ BLOB データ所有者またはストレージ BLOB データ共同作成者のアクセス許可が必要です。 ワークスペース ID にこのアクセス許可を付与する方法については、次を参照してください: [ワークスペースのマネージド ID にアクセス許可を付与する](../security/how-to-grant-workspace-managed-identity-permissions.md)。

## <a name="check-the-requirements-file"></a>要件ファイルを確認する
***requirements.txt*** ファイル (pip freeze コマンドからの出力) を使用して、仮想環境をアップグレードできます。 このファイルは、[pip freeze](https://pip.pypa.io/en/stable/reference/pip_freeze/) のリファレンス ドキュメントで説明されている形式に従います。

次の制限事項に注意してください。
   -  PyPi パッケージ名は、正確なバージョンと共にリストされている必要があります。 
   -  要件ファイルの内容には、余分な空白行または文字を含めないでください。 
   -  [Synapse ランタイム](apache-spark-version-support.md)には、すべてのサーバーレス Apache Spark プールにプレインストールされているライブラリのセットが含まれています。 基本ランタイムにプレインストールされているパッケージをダウングレードすることはできません。 パッケージは追加またはアップグレードのみ可能です。
   -  PySpark、Python、Scala/Java、.NET、または Spark のバージョンの変更はサポートされていません。

次のスニペットは、要件ファイルの必要な形式を示しています。

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

## <a name="validate-wheel-files"></a>Wheel ファイルを検証する
Synapse サーバーレス Apache Spark プールは、Linux ディストリビューションに基づいています。 PyPI から Wheel ファイルを直接ダウンロードしてインストールする場合は、必ず、Linux 上で構築されており、Spark プールと同じ Python バージョンで実行されるバージョンを選択するようにしてください。

>[!IMPORTANT]
>セッションとセッションの間に、カスタム パッケージを追加または変更できます。 ただし、更新されたパッケージを表示するには、プールとセッションが再起動するまで待つ必要があります。

## <a name="check-for-dependency-conflicts"></a>依存関係の競合を確認する
 一般に、Python の依存関係の解決は、管理が難しい場合があります。 依存関係の競合をローカルでデバッグできるように、Synapse ランタイムに基づいて独自の仮想環境を作成し、変更を検証できます。

環境を再作成し、更新を検証するには、次のようにします。
 1. Synapse ランタイムをローカルに再作成するためのテンプレートを[ダウンロード](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml)します。 テンプレートと実際の Synapse 環境の間には若干の違いがある場合があります。
   
 2. [次の手順](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html)を使用して仮想環境を作成します。 この環境を使用すると、指定したライブラリの一覧を使用して、分離された Python インストールを作成できます。 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. ``pip install -r <provide your req.txt file>`` を使用して、指定したパッケージで仮想環境を更新します。 インストールでエラーが発生した場合は、Synapse 基本ランタイムにプレインストールされているものと、指定された要件ファイルに指定されているものとの間に競合が存在する可能性があります。 サーバーレス Apache Spark プール上に更新されたライブラリを取得するには、これらの依存関係の競合を解決する必要があります。

## <a name="next-steps"></a>次のステップ
- 既定のライブラリを確認します: [Apache Spark バージョンのサポート](apache-spark-version-support.md)