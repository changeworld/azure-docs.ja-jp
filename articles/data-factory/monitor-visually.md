---
title: Azure Data Factory を視覚的に監視する | Microsoft Docs
description: Azure Data Factory を視覚的に監視する方法について説明します
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: shlo
ms.openlocfilehash: 4b3828e1857d17a128de346449d5cf2041709e50
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041077"
---
# <a name="visually-monitor-azure-data-factories"></a>Azure Data Factory を視覚的に監視する
Azure Data Factory は、データドリブン型のワークフローをクラウドに作成することでデータの移動と変換を制御し、自動化することができるクラウドベースのデータ統合サービスです。 Azure Data Factory を使えば、データ主導型のワークフロー (パイプライン) を作成し、スケジューリングできます。具体的には、各種データ ストアからデータを取り込む、そのデータを各種コンピューティング サービス (Azure HDInsight Hadoop、Spark、Azure Data Lake Analytics、Azure Machine Learning など) で処理/変換する、データ ストア (Azure SQL Data Warehouse など) に出力データを公開して、それを利用するビジネス インテリジェンス (BI) アプリケーションに提供するという一連の処理を行えるワークフローです。
このクイック スタートでは、Data Factory v2 パイプラインを視覚的に監視する方法について説明します。コードの記述は一切必要ありません。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="monitor-data-factory-v2-pipelines"></a>Data Factory v2 パイプラインを監視する

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
2. [Azure Portal](https://portal.azure.com/) にログインします。
3. Azure Portal で、作成した Data Factory ブレードに移動し、[Monitor & Manage]\(監視と管理\) タイルをクリックします。 これで ADF v2 の視覚的な監視エクスペリエンスが起動します。

## <a name="list-view-monitoring"></a>リスト ビューの監視

単純なリスト ビュー インターフェイスでパイプラインとアクティビティの実行を監視します。 すべての実行はローカル ブラウザーのタイム ゾーンで表示されます。 タイム ゾーンは変更できます。すべての日時フィールドは選択したタイム ゾーンにスナップされます。  

#### <a name="monitoring-pipeline-runs"></a>パイプラインの実行の監視
リスト ビューには、Data Factory v2 パイプラインの各パイプラインの実行が表示されます。 次のような列があります。

| **列名** | **説明** |
| --- | --- |
| Pipeline Name (パイプライン名) | パイプラインの名前。 |
| アクション | アクティビティ実行の表示に使用できる単一のアクション。 |
| Run Start (実行の開始) | パイプラインの実行の開始日時 (MM/DD/YYYY, HH:MM:SS AM/PM) |
| duration | 実行期間 (HH:MM:SS) |
| Triggered By (トリガー元) | 手動トリガー、スケジュール トリガー |
| Status | 失敗、成功、進行中 |
| parameters | パイプラインの実行パラメーター (名前、値のペア) |
| エラー | パイプラインの実行エラー (発生した場合) |
| Run ID (実行 ID) | パイプラインの実行 ID |

![パイプラインの実行を監視する](media/monitor-visually/pipeline-runs.png)

#### <a name="monitoring-activity-runs"></a>アクティビティ実行の監視
リスト ビューには、各パイプラインの実行に対応するアクティビティの実行が表示されます。 **[アクション]** 列の **[アクティビティの実行]** アイコンをクリックして、各パイプラインの実行のアクティビティの実行を表示します。 次のような列があります。

| **列名** | **説明** |
| --- | --- |
| Activity Name (アクティビティ名) | パイプライン内のアクティビティの名前。 |
| Activity Type (アクティビティの種類) | アクティビティの種類 (Copy、HDInsightSpark、HDInsightHive など) |
| Run Start (実行の開始) | アクティビティの実行の開始日時 (MM/DD/YYYY, HH:MM:SS AM/PM) |
| duration | 実行期間 (HH:MM:SS) |
| Status | 失敗、成功、進行中 |
| 入力 | アクティビティの入力を記述する JSON 配列 |
| 出力 | アクティビティの出力を記述する JSON 配列 |
| エラー | アクティビティの実行エラー (発生した場合) |

![アクティビティの実行を監視する](media/monitor-visually/activity-runs.png)

> [!IMPORTANT]
> パイプラインとアクティビティの実行一覧を更新するには、上部の **[更新]** アイコンをクリックする必要があります。 現在、自動更新はサポートされていません。
>

![更新](media/monitor-visually/refresh.png)

## <a name="features"></a>機能

#### <a name="select-a-data-factory-to-monitor"></a>監視するデータ ファクトリの選択
左上の **[Data Factory]** アイコンにマウス ポインターを移動します。 [矢印] アイコンをクリックすると、監視できる Azure サブスクリプションと Data Factory のリストが表示されます。

![データ ファクトリの選択](media/monitor-visually/select-datafactory.png)

#### <a name="rich-ordering-and-filtering"></a>高度な並べ替えとフィルター処理

パイプラインの実行順序は、[Run Start]\(実行の開始\) で昇順または降順で並べ替えます。パイプラインの実行は次の列でフィルターします。

| **列名** | **説明** |
| --- | --- |
| Pipeline Name (パイプライン名) | パイプラインの名前。 オプションには [過去 24 時間]、[先週]、[過去 30 日間] のクイック フィルターがあります。または、カスタムの日時を選択します。 |
| Run Start (実行の開始) | パイプラインの実行の開始日時 |
| Run Status (ジョブの状態) | 状態 (成功、失敗、進行中) で実行をフィルターします |

![filter](media/monitor-visually/filter.png)

#### <a name="addremove-columns-in-list-view"></a>リスト ビューへの列の追加と削除
リスト ビューの見出しを右クリックし、リスト ビューに表示する列を選択します。

![列](media/monitor-visually/columns.png)

#### <a name="reorder-column-widths-in-list-view"></a>リスト ビューの列幅を変更する
リスト ビューの列幅を増減するには、列見出しにマウス ポインターを移動します。

#### <a name="user-properties"></a>ユーザー プロパティ

監視可能なエンティティになるように、任意のパイプラインのアクティビティ プロパティをユーザー プロパティとして昇格できます。 たとえば、パイプラインのコピー アクティビティの **Source** プロパティと **Destination** プロパティをユーザー プロパティとして昇格できます。 また、**[自動生成]** を選択して、コピー アクティビティの **Source** および **Destination** ユーザー プロパティを生成することもできます。

![ユーザー プロパティの作成](media/monitor-visually/monitor-user-properties-image1.png)

> [!NOTE]
> ユーザー プロパティとして昇格できるのは、最大 5 つのパイプライン アクティビティ プロパティのみです。

作成したユーザー プロパティは、監視リスト ビューで監視することができます。 コピー アクティビティのソースがテーブル名の場合、アクティビティの実行リスト ビューでソースのテーブル名を列として監視できます。

![ユーザー プロパティを持たないアクティビティの実行リスト](media/monitor-visually/monitor-user-properties-image2.png)

![アクティビティ実行の一覧にユーザー プロパティの列を追加する](media/monitor-visually/monitor-user-properties-image3.png)

![アクティビティ実行の一覧とユーザー プロパティの列](media/monitor-visually/monitor-user-properties-image4.png)

#### <a name="guided-tours"></a>ガイド ツアー
左下の [情報] アイコンをクリックし、[Guided Tours]\(ガイド ツアー\) をクリックすると、パイプラインとアクティビティの実行を監視する方法に関する手順が表示されます。

![ガイド ツアー](media/monitor-visually/guided-tours.png)

#### <a name="feedback"></a>フィードバック
[フィードバック] アイコンをクリックして、さまざまな機能や、発生している問題についてフィードバックをお寄せください。

![フィードバック](media/monitor-visually/feedback.png)

## <a name="next-steps"></a>次の手順

パイプラインの監視と管理の詳細については、「[Azure Data Factory をプログラムで監視する](https://docs.microsoft.com/azure/data-factory/monitor-programmatically)」を参照してください。
