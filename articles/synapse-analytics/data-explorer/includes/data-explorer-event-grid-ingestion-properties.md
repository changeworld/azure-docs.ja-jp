---
ms.topic: include
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 73cd54f16e29564fda5f9e41d0469d59ea99526d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477841"
---
|**プロパティ** | **プロパティの説明**|
|---|---|
| `rawSizeBytes` | 未加工の (圧縮されていない) データのサイズ。 Avro/ORC/Parquet の場合、これは形式固有の圧縮が適用される前のサイズです。 このプロパティを圧縮されていないデータ サイズ (バイト単位) に設定して、元のデータ サイズを指定します。|
| `kustoTable` |  既存のターゲット テーブルの名前。 [`Data Connection`] ブレードでの [`Table`] の設定をオーバーライドします。 |
| `kustoDataFormat` |  データ形式。 [`Data Connection`] ブレードでの [`Data format`] の設定をオーバーライドします。 |
| `kustoIngestionMappingReference` |  使用する既存のインジェスト マッピングの名前。 [`Data Connection`] ブレードでの [`Column mapping`] の設定をオーバーライドします。|
| `kustoIgnoreFirstRecord` | `true` に設定した場合、Kusto で BLOB の最初の行が無視されます。 表形式のデータ (CSV、TSV など) でヘッダーを無視するために使用します。 |
| `kustoExtentTags` | 結果のエクステントに添付される[タグ](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context)を表す文字列。 |
| `kustoCreationTime` |  ISO 8601 の文字列として書式設定されている、BLOB の [$IngestionTime](/azure/data-explorer/kusto/query/ingestiontimefunction?context=/azure/synapse-analytics/context/context?pivots=azuredataexplorer) をオーバーライドします。 バックフィルに使用します。 |
