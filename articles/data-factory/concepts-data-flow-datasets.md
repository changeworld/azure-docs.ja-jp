---
title: Azure Data Factory の Mapping Data Flow のデータセット
description: Azure Data Factory の Mapping Data Flow には特定のデータセットの互換性があります
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: ad6cfdad519ab3901c58979970ea07439b3106e9
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2019
ms.locfileid: "57726925"
---
# <a name="mapping-data-flow-datasets"></a>Mapping Data Flow のデータセット

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

データセットは、パイプラインで使用しているデータの形状を定義する Data Factory のコンストラクトです。 Data Flow では、行レベルと列レベルのデータにきめ細かいデータセット定義が必要です。 制御フロー パイプラインで使用されるデータセットには、同じ深さのデータの理解が必要ありません。

データ フロー内のデータセットは、ソースとシンクの変換で使用されます。 これらは、基本的なデータ スキーマを定義するために使用されます。 データにスキーマがない場合は、ソースとシンクに対して [Schema Drift]\(スキーマのドリフト\) をオンに設定できます。 データセットからスキーマを定義すると、関連するリンクされたサービスから関連するデータの種類、データ形式、ファイルの場所、および接続情報が得られます。 データセットから取得されたメタデータは、ソース変換でソース "プロジェクション" として表示されます。 データセット内のスキーマは物理的なデータ型とデータ シェイプを表し、ソース変換内のプロジェクションは、定義された名前と型を持つデータのデータ フロー表現を表します。

## <a name="dataset-types"></a>データセットの種類

現在、Data Flow にはデータセットの種類が 4 つあります。

* Azure SQL DB
* Azure SQL DW
* Parquet (ADLS と BLOB から送信)
* 区切りテキスト (ADLS と BLOB から送信)

Data Flow データセットでは、*ソースの種類*と、*リンクされたサービスの接続の種類*が分けられています。 通常、Data Factory では、接続の種類 (BLOB、ADLS など) を選択してから、データセットのファイルの種類を定義します。 Data Flow 内で、ソースの種類を選択します。このソースの種類は、さまざまなリンクされたサービスの接続の種類に関連付けることができます。

![ソース変換のオプション](media/data-flow/dataset1.png "ソース")

## <a name="data-flow-compatible-datasets"></a>Data Flow の互換性のあるデータセット

新しいデータセットを作成すると、パネルの右上に [Data Flow 互換] というラベルの付いたチェックボックスが表示されます。 そのボタンをクリックすると、Data Flow で使用できるデータセットのみがフィルター処理されます。 

## <a name="import-schemas"></a>スキーマをインポートする

Data Flow データセットのスキーマをインポートすると、[Import Schema]\(スキーマのインポート\) ボタンが表示されます。 このボタンをクリックすると、2 つのオプションが表示されます。ソースからのインポートか、ローカル ファイルからのインポートです。 ほとんどの場合、ソースからスキーマを直接インポートします。 ただし、既存のスキーマ ファイル (Parquet ファイルまたはヘッダー付きの CSV) がある場合は、そのローカル ファイルを指定すると、Data Factory でそのスキーマ ファイルに基づいてスキーマが定義されます。

## <a name="delimited-text-dataset"></a>区切りテキスト データセット

区切りテキスト データセットでは、区切り記号の設定によって、1 つの区切り記号 (TSV の "\t"、CSV の ","、'|' など) を処理するか、複数の文字を区切り記号に使用します。 ヘッダー行の切り替えを設定した後、ソース変換に移動してデータ型を自動検出します。

## <a name="next-steps"></a>次の手順

まず、[新しいデータ フローを作成](data-flow-create.md)し、ソース変換を追加します。 その後、ソースのデータセットを構成します。

[コピー アクティビティ](copy-activity-overview.md)を使用して ADF データ ソースからデータを取得し、それを ADLS または BLOB にステージングして、データ フローでアクセスできるようにします。

