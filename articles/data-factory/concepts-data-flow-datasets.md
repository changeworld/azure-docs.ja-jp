---
title: Azure Data Factory の Mapping Data Flow のデータセット
description: Azure Data Factory の Mapping Data Flow には特定のデータセットの互換性があります
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/14/2019
ms.openlocfilehash: 36ca5e07adf79de77ac4ab4149ff8e96a1dece8d
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408750"
---
# <a name="mapping-data-flow-datasets"></a>Mapping Data Flow のデータセット

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

データセットは、パイプラインで使用しているデータの形状を定義する Data Factory のコンストラクトです。 Data Flow では、行レベルと列レベルのデータにきめ細かいデータセット定義が必要です。 制御フロー パイプラインで使用されるデータセットには、同じ深さのデータの理解が必要ありません。

Data Flow ソースおよびシンク変換のデータセットは、基本データ スキーマの定義に使用されます。 データにスキーマがない場合は、ソースとシンクに対して [Schema Drift]\(スキーマのドリフト\) をオンに設定できます。 データセットからスキーマを定義すると、関連するリンクされたサービスから関連するデータの種類、データ形式、ファイルの場所、および接続情報が得られます。

## <a name="dataset-types"></a>データセットの種類

現在、Data Flow にはデータセットの種類が 4 つあります。

* Azure SQL DB
* Azure SQL DW
* Parquet
* 区切りテキスト

Data Flow データセットでは、ソースの "*種類*" と、リンクされたサービスの接続の種類が分けられています。 通常、Data Factory では、接続の種類 (BLOB、ADLS など) を選択してから、データセットのファイルの種類を定義します。 Data Flow 内で、ソースの種類を選択します。このソースの種類は、さまざまなリンクされたサービスの接続の種類に関連付けることができます。

![ソース変換のオプション](media/data-flow/dataset1.png "ソース")

## <a name="data-flow-compatible-datasets"></a>Data Flow の互換性のあるデータセット

新しいデータセットを作成すると、パネルの右上に [Data Flow 互換] というラベルの付いたチェックボックスが表示されます。 そのボタンをクリックすると、Data Flow で使用できるデータセットのみがフィルター処理されます。 

## <a name="import-schemas"></a>スキーマをインポートする

Data Flow データセットのスキーマをインポートすると、[Import Schema]\(スキーマのインポート\) ボタンが表示されます。 このボタンをクリックすると、2 つのオプションが表示されます。ソースからのインポートか、ローカル ファイルからのインポートです。 ほとんどの場合、ソースからスキーマを直接インポートします。 ただし、既存のスキーマ ファイル (Parquet ファイルまたはヘッダー付きの CSV) がある場合は、そのローカル ファイルを指定すると、Data Factory でそのスキーマ ファイルに基づいてスキーマが定義されます。

