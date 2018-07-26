---
title: Databricks Jar でデータを変換する - Azure | Microsoft Docs
description: Databricks Jar を実行してデータを処理または変換する方法を説明します。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: 8a7e409bc664fd56fbb9b80678832a626f301e5b
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075571"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Azure Databricks で Jar アクティビティを実行してデータを変換する

[Data Factory パイプライン](concepts-pipelines-activities.md) の Azure Databricks Jar アクティビティは、Azure Databricks クラスターで Spark Jar を実行します。 この記事は、データ変換とサポートされる変換アクティビティの概要を説明する、 [データ変換アクティビティ](transform-data.md) に関する記事に基づいています。 Azure Databricks は、Apache Spark を実行するための管理されたプラットフォームです。

## <a name="databricks-jar-activity-definition"></a>Databricks Jar アクティビティの定義

Databricks Jar アクティビティのサンプルの JSON 定義を次に示します。

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Databricks Jar アクティビティのプロパティ

次の表で、JSON 定義で使用される JSON プロパティについて説明します。

|プロパティ|説明|必須|
|:--|---|:-:|
|name|パイプラインのアクティビティの名前。|[はい]|
|description|アクティビティの動作を説明するテキスト。|いいえ |
|type|Databricks Jar アクティビティでは、アクティビティの種類は DatabricksSparkJar です。|[はい]|
|linkedServiceName|Jar アクティビティが実行されている Databricks リンク サービスの名前です。 このリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。|[はい]|
|mainClassName|実行される main メソッドを含むクラスのフル ネーム。 このクラスは、ライブラリとして提供される JAR に含まれている必要があります。|[はい]|
|parameters|main メソッドに渡されるパラメーター。  文字列の配列です。|いいえ |
|libraries|ジョブを実行するクラスターにインストールされるライブラリのリスト。 <文字列, オブジェクト> の配列を指定できます。|はい (mainClassName メソッドを少なくとも 1 つ含む)|

## <a name="supported-libraries-for-databricks-activities"></a>databricks アクティビティでサポートされるライブラリ

前述の Databricks アクティビティ定義では、*jar*、*egg*、*maven*、*pypi*、*cran* というライブラリの種類を指定しています。

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "http://cran.us.r-project.org"
            }
        }
    ]
}

```

ライブラリの種類の詳細については、[Databricks のドキュメント](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary)を参照してください。

## <a name="how-to-upload-a-library-in-databricks"></a>Databricks でライブラリをアップロードする方法

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Databricks ワークスペース UI の使用](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)。

UI を使用して追加されたライブラリの dbfs パスを取得するには、[Databricks CLI (インストール)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli) を使用します。 

UI を使用する場合、通常、Jar ライブラリは dbfs:/FileStore/jars に保存されます。 CLI *databricks fs ls dbfs:/FileStore/jars* を使用してすべてを一覧表示することができます。 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Databricks CLI を使用したライブラリのコピー](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

例: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*