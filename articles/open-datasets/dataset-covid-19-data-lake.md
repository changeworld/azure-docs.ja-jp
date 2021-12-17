---
title: COVID-19 データ レイク
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で COVID-19 データ レイクを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: e43ea946511efaa313204ca26bf7e05f1b3f923a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038865"
---
# <a name="covid-19-data-lake"></a>COVID-19 データ レイク

COVID-19 データ レイクには、さまざまなソースからの COVID-19 関連のデータセットが含まれています。 これは、検査と患者の結果の追跡データ、ソーシャル ディスタンス ポリシー、病床数、流動性などをカバーしています。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

COVID-19 Data Lake は、米国東部リージョンの Azure Data Lake Storage でホストされます。 各データセットの変更されたバージョンは、csv、json、json-lines、parquet の各形式で入手できます。 生データも取り込まれているため、利用できます。

結合を簡略化するため、ISO 3166 下位コードが存在しない場合は追加されます。 列名は、小文字とアンダースコア区切り文字で設定しなおされています。 データセットは毎日更新され、編集済み、および生のファイルの履歴コピーも提供されます。

## <a name="datasets"></a>データセット

| データセット                                                                 | 説明                                                                                                                                                                                                                                             |
|--------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Bing COVID-19 Data](dataset-bing-covid-19.md)                                                       | Bing COVID-19 データには、すべてのリージョンからの確定、重症、回復した症例が含まれ、毎日更新されます。                                                                                                                                                      |
| [COVID Tracking Project](dataset-covid-tracking.md)                                                | COVID Tracking Project データセットは、米国のすべての州と準州からの最新の検査数、感染者数、入院患者数、患者のアウトカムを提供しています。                                                                                     |
| [European Centre for Disease Prevention and Control (ECDC) Covid-19 Cases](dataset-ecdc-covid-cases.md) | 欧州疾病予防管理センター (ECDC) が提供する、COVID-19 の世界の感染者数の地理的な分布に関し提供されている最新の公開データです。 各行/エントリには、日あたり、国または地域あたりの新規感染者数が含まれています。 |
| [Oxford COVID-19 Government Response Tracker](dataset-oxford-covid-government-response-tracker.md)                              | Oxford Covid-19 Government Response Tracker (OxCGRT) データセットには、どの政府がどの措置をどのタイミングで実施しているかについてのシステム的な情報が含まれています。                                                                                              |

---

## <a name="next-steps"></a>次の手順

[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットを表示します。