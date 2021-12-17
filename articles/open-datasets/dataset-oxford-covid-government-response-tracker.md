---
title: Oxford COVID-19 Government Response Tracker
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で Oxford COVID-19 Government Response Tracker データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: a39c8915027cddeb168f5bb0c63f915492ece398
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114444616"
---
# <a name="oxford-covid-19-government-response-tracker"></a>Oxford COVID-19 Government Response Tracker

[Oxford Covid-19 Government Response Tracker (OxCGRT) データセット](https://github.com/OxCGRT/covid-policy-tracker/)には、どの政府がどの措置をどのタイミングで実施しているかについてのシステム的な情報が含まれています。

この情報により、意思決定者や市民は、政府の対応について一貫した方法で理解することができ、パンデミックと闘うための取り組みの支援となります。 OxCGRT は政府が実施したさまざまな共通政策の対応についての情報をシステム的に収集し、政府の行動の範囲を反映するスケールでこれらの政策を記録し、それらのスコアを一連の政策指標に集計します。


[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="datasets"></a>データセット

データセットの編集版は、CSV、JSON、JSON-Lines、Parquet 形式で入手でき、毎日更新されます。
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.csv
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.json
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.jsonl
- https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet

すべての編集済みバージョンには iso_country コードと読み込み時間が追加されており、小文字の列名とアンダースコアの区切り文字が使用されています。

生データ: https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/covid_policy_tracker/latest/CovidPolicyTracker.csv

以前のバージョンの編集済み、および生データ: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/ https://pandemicdatalake.blob.core.windows.net/public/raw/covid-19/covid_policy_tracker/

## <a name="data-volume"></a>データ ボリューム

2020 年 6 月 8 日時点で、27,919 行 (CSV 4.9 MB、JSON 20.9 MB、JSONL 20.8 MB、Parquet 133.0 KB) が含まれています。

## <a name="data-source"></a>データ ソース

このデータ ソースは Thomas Hale、Sam Webster、Anna Petherick、Toby Phillips、Beatriz Kira に帰属します。 (2020). Oxford COVID-19 Government Response Tracker. [Blavatnik School of Government](https://www.bsg.ox.ac.uk/). 生データは、[最新の OxCGRT の csv ファイル](https://github.com/OxCGRT/covid-policy-tracker/blob/master/data/OxCGRT_latest.csv)から毎日取り込まれます。 収集方法など、このデータセットの詳細については、[Government Tracker Response のサイト](https://www.bsg.ox.ac.uk/research/research-projects/covid-19-government-response-tracker)を参照してください。

## <a name="data-quality"></a>データ品質
OxCGRT はデータの正確性と適時性については保証しません。 詳細については、[データ品質に関する声明](https://github.com/OxCGRT/covid-policy-tracker#data-quality)を参照してください。

## <a name="license-and-use-rights-attribution"></a>ライセンスと使用権の帰属

このデータは、[Creative Commons の属性 4.0 国際ライセンス](https://github.com/OxCGRT/covid-policy-tracker/blob/master/LICENSE.txt)でライセンス供与されています。

引用: Thomas Hale、Sam Webster、Anna Petherick、Toby Phillips、Beatriz Kira。 (2020). Oxford COVID-19 Government Response Tracker. Blavatnik School of Government.

## <a name="contact"></a>Contact

COVID-19 Data Lake のこのデータセットまたはその他のデータセットに関する質問またはフィードバックについては、askcovid19dl@microsoft.com にお問い合わせください。

## <a name="columns"></a>[列]

| Name                                  | データ型 | 一意 | 値 (サンプル)            | 説明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|---------------------------------------|-----------|--------|----------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| c1_flag                               | boolean   | 3      | True                       | 地理的な範囲に対するバイナリ フラグ。 0 - 対象を指定 1 - 全般 空白 - データなし                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| c1_school_closing                     | double    | 5      | 3.0 2.0                    | 学校や大学の閉鎖に関する記録。 0 - 措置なし 1 - 閉鎖を推奨 2 - 閉鎖を要求 (一部のレベルまたはカテゴリのみ。例: 高校のみ、公立学校のみ) 3 - すべてのレベルで閉鎖を要求 空白 - データなし                                                                                                                                                                                                                                                                                                 |
| c2_flag                               | boolean   | 3      | True                       | 地理的な範囲に対するバイナリ フラグ。 0 - 対象を指定; 1 - 全般; 空白 - データなし                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| c2_workplace_closing                  | double    | 5      | 2.0 1.0                    | 職場の閉鎖に関する記録。 0 - 措置なし 1 - 閉鎖の推奨 (または在宅勤務を推奨) 2 - 一部の部門やカテゴリの従業員に対する閉鎖 (または在宅勤務) の要求 3 - 必要不可欠な職場 (例: 食料品店や医師) 以外のすべての閉鎖 (または在宅勤務) の要求 空白 - データなし                                                                                                                                                                                                                          |
| c3_cancel_public_events               | double    | 4      | 2.0 1.0                    | パブリック イベントのキャンセルの記録。 0 - 措置なし 1 - キャンセルを推奨 2 - キャンセルを要求 空白 - データなし                                                                                                                                                                                                                                                                                                                                                                                                                    |
| c3_flag                               | boolean   | 3      | True                       | 地理的な範囲に対するバイナリ フラグ。 0 - 対象を指定 1 - 全般 空白 - データなし                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| c4_flag                               | boolean   | 3      | True                       | 地理的な範囲に対するバイナリ フラグ 0 - 対象を指定 1 - 全般 空白 - データなし                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| c4_restrictions_on_gatherings         | double    | 6      | 4.0 3.0                    | プライベートな集会の制限に関する記録。 0 - 制限なし 1 - 大規模な集会に対する制限 (制限は 1,000 人以上) 2 - 101 人から 1,000 人の集会の制限 3 - 11 人から 100 人の集会の制限 4 - 10 人以下の集会の制限 空白 - データなし                                                                                                                                                                                                                 |
| c5_close_public_transport             | double    | 4      | 1.0 2.0                    | 公共交通機関の閉鎖の記録 0 - 措置なし 1 - 閉鎖を推奨 (または利用できる交通機関の数、ルート、手段を大幅に削減) 2 - 閉鎖の要求 (またはほとんどの市民の利用を禁止) 空白 - データなし                                                                                                                                                                                                                                                                                                          |
| c5_flag                               | boolean   | 3      | True                       | 地理的な範囲に対するバイナリ フラグ 0 - 対象を指定 1 - 全般 空白 - データなし                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| c6_flag                               | boolean   | 3      | True                       | 地理的な範囲に対するバイナリ フラグ 0 - 対象を指定 1 - 全般 空白 - データなし                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| c6_stay_at_home_requirements          | double    | 5      | 1.0 2.0                    | "屋内退避勧告" および自宅待機命令の記録 0 - 措置なし 1 - 自宅から出ないことを推奨 2 - 軽い運動、食料品の買い物、'必要不可欠な' 移動を除き、自宅から出ないことを要求 3 - 最低限の例外を除き、自宅から出ないことを要求 (例: 週に一度の外出を許可、または一度に 1 名のみの外出を許可など) 空白 - データなし                                                                                                                                                |
| c7_flag                               | boolean   | 3      | True                       | 地理的な範囲に対するバイナリ フラグ 0 - 対象を指定 1 - 全般 空白 - データなし                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| c7_restrictions_on_internal_movement  | double    | 4      | 2.0 1.0                    | 都市/地域間の移動制限の記録 0 - 措置なし 1 - 地域/都市間の移動を行わないよう推奨 2 - 内部での移動の制限を実施 空白 - データなし                                                                                                                                                                                                                                                                                                                                      |
| c8_international_travel_controls      | double    | 6      | 3.0 4.0                    | 国際的な移動の制限に関する記録。 注: これは市民ではなく、外国人の旅行者に対する政策を記録します 0 - 制限なし 1 - 到着時の検診 2 - 一部またはすべての地域からの到着に対する検疫 3 - 一部の地域からの到着を禁止 4 - すべての地域の禁止または全面的な国境の閉鎖 空白 - データなし                                                                                                                                                                                                                           |
| confirmedcases                        | smallint  | 18,238 | 1 2                        |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| confirmeddeaths                       | smallint  | 14,906 | 1 2                        |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| countrycode                           | string    | 186    | USA BRA                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| countryname                           | string    | 186    | United States Brazil       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| date                                  | date      | 478    | 2020-08-25 2021-03-30      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| e1_flag                               | boolean   | 3      | True                       | セクター別の範囲に対するバイナリ フラグ 0 - フォーマル セクターの労働者のみ 1 - インフォーマル セクターの労働者も含む 空白 - データなし                                                                                                                                                                                                                                                                                                                                                                                                          |
| e1_income_support                     | double    | 4      | 1.0 2.0                    | 失業したまたは働くことのできない人に対して政府が現金の直接給付を提供しているかどうかの記録。 注: 明示的に賃金/給与にリンクされている場合は、企業への支払いのみを含みます 0 - 所得補助なし 1 - 政府が逸失賃金の 50 % 未満を補助 (または一時金の場合は平均給与の 50 % 未満) 2 - 政府が逸失賃金の 50 % 以上を補助 (または一時金の場合は平均給与の 50 % 以上) 空白 - データなし                                                                        |
| e2_debt/contract_relief               | double    | 4      | 1.0 2.0                    |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| e3_fiscal_measures                    | double    | 819    | -0.01 3.0                  | 発表された経済政策の支出 注: 以前発表された財政出動の支出の記録の金額 (米国ドル) に追加された金額のみの記録です。E4、H4、H5 に "含まれない" あらゆる支出や税金免除を含みます 0 - その日の新規支出なし 空白 - データなし                                                                                                                                                                                                                                                               |
| e4_international_support              | double    | 113    | -0.02 5000000.0            | 他国への Covid-19 関連の支援に関する発表済みのオファー 注: 以前発表された支出の記録の金額 (米国ドル) に追加された金額のみの記録 0 - その日の新規支出なし 空白 - データなし                                                                                                                                                                                                                                                                                                                 |
| h1_flag                               | boolean   | 3      | True                       | 地理的な範囲に対するバイナリ フラグ 0 - 対象を指定 1 - 全般 空白 - データなし                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| h1_public_information_campaigns       | double    | 4      | 2.0 1.0                    | 広告キャンペーンの有無の記録 0 - Covid-19 の広告キャンペーンなし 1 - Covid-19 に関する公的機関による強い警告 2- 調整された広告キャンペーン (例: 従来型およびソーシャル メディア全体) 空白 - データなし                                                                                                                                                                                                                                                                                         |
| h2_testing_policy                     | double    | 5      | 2.0 1.0                    | 検査を受けることができる人に関する政府の政策の記録 注: これは、免疫 (抗体検査) の検査ではなく、現在の感染 (PCR 検査) の検査に関する政策を記録します 0 - 検査の政策なし 1 - (a) 症状のある人かつ (b) 特定の条件に一致する人 (例: エッセンシャル ワーカー、入院中の人、既知の感染者と接触があった人、海外からの帰国者) 2 - Covid-19 の症状があるすべての人の検査 3 - 公的検査が実施されている (例: 無症状の人に対する "ドライブ スルー" 検査) 空白 - データなし |
| h3_contact_tracing                    | double    | 4      | 2.0 1.0                    | 陽性判定後の接触者追跡に関する政府の政策の記録 注: Covid-19 にさらされた可能性のあるすべての人を特定する政策を選んでいます。任意の Bluetooth アプリはこれを満たさない可能性があります 0 - 接触者追跡なし 1 - 限定的な接触者追跡、すべての感染事例に対して行われるわけではない 2 - 総合的な接触者追跡、特定されたすべての感染事例に対して行われる                                                                                                                                                          |
| h4_emergency_investment_in_healthcare | double    | 462    | 35.0 562.0                 | 医療システムでの発表済みの短期支出 (病院、マスクなど) 注: 以前発表された支出の記録の金額 (米国ドル) に追加された金額のみの記録 0 - その日の新規支出なし 空白 - データなし                                                                                                                                                                                                                                                                                                         |
| h5_investment_in_vaccines             | double    | 133    | 1.0 191.0                  | Covid-19 ワクチン開発における発表済みの公共部門の支出 注: 以前発表された支出の記録の金額 (米国ドル) に追加された金額のみの記録 0 - その日の新規支出なし 空白 - データなし                                                                                                                                                                                                                                                                                                                            |
| iso_country                           | string    | 186    | US BR                      | ISO 3166 国または地域コード                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| load_date                             | timestamp | 1      | 2021-04-26 00:06:25.157000 | データが外部ソースから読み込まれた日時                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| stringencyindex                       | double    | 188    | 11.11 60.19                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| stringencyindexfordisplay             | double    | 188    | 11.11 60.19                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="preview"></a>プレビュー

| countryname | countrycode | date       | c1_school_closing | c2_workplace_closing | c3_cancel_public_events | c4_restrictions_on_gatherings | c5_close_public_transport | c6_stay_at_home_requirements | c7_restrictions_on_internal_movement | c8_international_travel_controls | e1_income_support | e2_debt/contract_relief | e3_fiscal_measures | e4_international_support | h1_public_information_campaigns | h2_testing_policy | h3_contact_tracing | h4_emergency_investment_in_healthcare | h5_investment_in_vaccines | m1_wildcard | stringencyindex | stringencyindexfordisplay | iso_country | load_date             |
|-------------|-------------|------------|-------------------|----------------------|-------------------------|-------------------------------|---------------------------|------------------------------|--------------------------------------|----------------------------------|-------------------|-------------------------|--------------------|--------------------------|---------------------------------|-------------------|--------------------|---------------------------------------|---------------------------|-------------|-----------------|---------------------------|-------------|-----------------------|
| アルバ       | ABW         | 2020-01-01 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| アルバ       | ABW         | 2020-01-02 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| アルバ       | ABW         | 2020-01-03 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| アルバ       | ABW         | 2020-01-04 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| アルバ       | ABW         | 2020-01-05 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| アルバ       | ABW         | 2020-01-06 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| アルバ       | ABW         | 2020-01-07 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| アルバ       | ABW         | 2020-01-08 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| アルバ       | ABW         | 2020-01-09 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |
| アルバ       | ABW         | 2020-01-10 | 0                 | 0                    | 0                       | 0                             | 0                         | 0                            | 0                                    | 0                                | 0                 | 0                       | 0                  | 0                        | 0                               | 0                 | 0                  | 0                                     | 0                         | null        | 0               | 0                         | AW          | 4/26/2021 12:06:25 AM |

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=oxford-covid-19-government-response-tracker -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=oxford-covid-19-government-response-tracker)** 。

## <a name="this-notebook-documents-the-urls-and-sample-code-to-access-the-oxford-covid-19-government-response-tracker-oxcgrt-dataset"></a>このノートブックには、Oxford Covid-19 Government Response Tracker (OxCGRT) データセットにアクセスするための URL とサンプル コードが文書化されています

Azure Blob Storage でホストされているさまざまなファイル形式の URL:

CSV: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.csv 

JSON: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.json

JSONL: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.jsonl 

Parquet: https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet

Pandas の組み込みのダウンロード機能を使用して、データセット ファイルを http URL からダウンロードします。 Pandas には、次のようなさまざまなファイル形式のリーダーがあります。

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_parquet.html

https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html


まず、データセット ファイルを pandas のデータフレームに読み込んで、いくつかのサンプル行を表示することから始めます

```python
import pandas as pd
import numpy as np
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_parquet("https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet")
df.head(10)
```

さまざまなフィールドのデータ型を確認し、更新された列が datetime 形式であることを確認してみましょう

```python
df.dtypes
```

このデータセットには、多数の国のデータが含まれています。 データが含まれている国を確認してみましょう。

まず、各国の最新のデータを見ることから始めます。

```python
df.groupby('countryname').first().filter(['confirmedcases ', 'confirmeddeaths','h5_investment_in_vaccines',
    'c6_stay_at_home_requirements','h4_emergency_investment_in_healthcare','c4_restrictions_on_gatherings', 'load_date'])
```

次に、`confirmedcases`、`confirmeddeaths` などの列が最新のデータと一致することを確認するために、いくつかの集計を行い ます。 上記の表の最後の日付の感染者と死亡の数値が、`confirmedcases` および `confirmeddeaths` の集計と一致していることを確認してください。


```python
df.groupby('countryname').agg({'countryname': 'count','confirmedcases': 'sum','confirmeddeaths': 'sum',
                               'h5_investment_in_vaccines': 'count', 'c6_stay_at_home_requirements':'sum'})
```

いくつかの国に基本的な視覚化を適用してみましょう

```python
import plotly.graph_objects as go
import plotly.express as px
import matplotlib.pyplot as plt

df.loc[: , ['countryname', 'confirmedcases', 
'confirmeddeaths']].groupby(['countryname']).max().sort_values(by='confirmedcases', 
                                           ascending=False).reset_index()[:15].style.background_gradient(cmap='rainbow')
```

```python
df_US = df.groupby(df.date).agg({'confirmedcases': 'sum','confirmeddeaths':'sum'}).reset_index()

df_US.plot(kind='line',x='date',y="confirmedcases",grid=True)
df_US.plot(kind='line',x='date',y="confirmeddeaths",grid=True)

```

<!-- nbend -->

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

---

### <a name="azure-databricks"></a>Azure Databricks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=oxford-covid-19-government-response-tracker -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureDatabricks&package=pyspark&registryId=oxford-covid-19-government-response-tracker)** 。

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet"
blob_sas_token = r""
```

```python
# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)
```

```python
# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')
```

```python
# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

<!-- nbend -->

---

### <a name="azure-synapse"></a>Azure Synapse

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

このプラットフォームとパッケージの組み合わせでは、サンプルは利用できません。

# <a name="pyspark"></a>[pyspark](#tab/pyspark)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=oxford-covid-19-government-response-tracker -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureSynapse&package=pyspark&registryId=oxford-covid-19-government-response-tracker)** 。

```python
# Azure storage access info
blob_account_name = "pandemicdatalake"
blob_container_name = "public"
blob_relative_path = "curated/covid-19/covid_policy_tracker/latest/covid_policy_tracker.parquet"
blob_sas_token = r""
```

```python
# Allow SPARK to read from Blob remotely
wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
spark.conf.set(
  'fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),
  blob_sas_token)
print('Remote blob path: ' + wasbs_path)
```

```python
# SPARK read parquet, note that it won't load any data yet by now
df = spark.read.parquet(wasbs_path)
print('Register the DataFrame as a SQL temporary view: source')
df.createOrReplaceTempView('source')
```

```python
# Display top 10 rows
print('Displaying top 10 rows: ')
display(spark.sql('SELECT * FROM source LIMIT 10'))
```

<!-- nbend -->

---

## <a name="next-steps"></a>次のステップ

[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットを表示します。
