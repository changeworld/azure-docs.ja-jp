---
title: Databricks Notebook でデータを変換する - Azure | Microsoft Docs
description: Databricks Notebook を実行してデータを処理または変換する方法を説明します。
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
ms.openlocfilehash: fbf713b2d52469ae12fc284e0a3d7e3bc369daeb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620505"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Databricks Notebook を実行してデータを変換する

[Data Factory パイプライン](concepts-pipelines-activities.md)の Azure Databricks Notebook アクティビティは、Azure Databricks ワークスペースで Databricks Notebook を実行します。 この記事は、データ変換とサポートされる変換アクティビティの概要を説明する、 [データ変換アクティビティ](transform-data.md) に関する記事に基づいています。 Azure Databricks は、Apache Spark を実行するための管理されたプラットフォームです。

## <a name="databricks-notebook-activity-definition"></a>Databricks Notebook アクティビティの定義

Databricks Notebook アクティビティのサンプルの JSON 定義を次に示します。

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            }
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Databricks Notebook アクティビティのプロパティ

次の表で、JSON 定義で使用される JSON プロパティについて説明します。

|プロパティ|説明|必須|
|---|---|---|
|name|パイプラインのアクティビティの名前。|[はい]|
|description |アクティビティの動作を説明するテキスト。|いいえ |
|型|Databricks Notebook アクティビティでは、アクティビティの種類は DatabricksNotebook です。|[はい]|
|linkedServiceName|Databricks Notebook が実行されている Databricks リンク サービスの名前です。 このリンクされたサービスの詳細については、[計算のリンクされたサービス](compute-linked-services.md)に関する記事をご覧ください。|[はい]|
|notebookPath|Databricks ワークスペースで実行するノートブックの絶対パスです。 このパスはスラッシュで始まる必要があります。|[はい]|
|baseParameters|キーと値ペアの配列です。 基本パラメーターは、各アクティビティの実行に使うことができます。 指定されていないパラメーターをノートブックが受け取った場合は、ノートブックの既定値が使われます。 パラメーターについて詳しくは、[Databricks Notebook](https://docs.databricks.com/api/latest/jobs.html#jobsparampair) に関する記事をご覧ください。|いいえ |
