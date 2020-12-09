---
title: Azure Stream Analytics からの Azure Synapse Analytics 出力
description: この記事では、Azure Stream Analytics の出力としての Azure Synapse Analytics について説明します。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 63d03559b2a30020ed2e8bd1cd030855b8fb20b7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445096"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics"></a>Azure Stream Analytics からの Azure Synapse Analytics 出力

[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) は、エンタープライズ データ ウェアハウスとビッグ データ分析が統合された制限のない分析サービスです。 

Azure Stream Analytics ジョブを使用すると、Azure Synapse Analytics 内の専用 SQL プール テーブルに出力でき、最大 200 MB/秒のスループット レートを処理できます。これにより、レポートやダッシュボードなどのワークロードに対して、最も要求の厳しいリアルタイム分析とホットパス データ処理がサポートされます。  

Stream Analytics ジョブに出力として専用 SQL プール テーブルを追加するには、事前にそれが存在している必要があります。 テーブルのスキーマを、使用するジョブの出力内のフィールドとその型と一致させる必要があります。 

Azure Synapse を出力として使用するには、ストレージ アカウントが構成されていることを確認する必要があります。 [ストレージ アカウントの設定] に移動し、ストレージ アカウントを構成します。 テーブルをサポートする次のストレージ アカウントの種類のみを使用できます: 汎用 V2 と汎用 V1。 Standard レベルのみ選択してください。 Premium レベルはサポートされていません。

## <a name="output-configuration"></a>出力の構成

次の表に、Azure Synapse Analytics 出力を作成するためのプロパティの名前とその説明を示します。

|プロパティ名|説明|
|-|-|
|出力エイリアス |クエリの出力をこのデータベースに出力するためにクエリで使用されるわかりやすい名前です。 |
|データベース |出力を送信する専用 SQL プールの名前。 |
|サーバー名 |Azure Synapse サーバーの名前。  |
|ユーザー名 |データベースに書き込むためのアクセス権が割り当てられているユーザー名です。 Stream Analytics では、SQL 認証のみがサポートされます。 |
|Password |データベースに接続するためのパスワード。 |
|テーブル  | 出力の書き込み先のテーブル名です。 テーブル名は、大文字と小文字が区別されます。 このテーブルのスキーマは、ご自分のジョブの出力によって生成されるフィールドの数とその型に正確に一致する必要があります。|

## <a name="next-steps"></a>次のステップ

* [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)
* [クイック スタート:Azure CLI を使用して Azure Stream Analytics ジョブを作成する](quick-create-azure-cli.md)
* [クイック スタート: ARM テンプレートを使用して Azure Stream Analytics ジョブを作成する](quick-create-azure-resource-manager.md)
* [クイック スタート: Azure PowerShell を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-powershell.md)
* [クイック スタート:Visual Studio を使用して Azure Stream Analytics ジョブを作成する](stream-analytics-quick-create-vs.md)
* [クイック スタート: Visual Studio Code で Azure Stream Analytics ジョブを作成する](quick-create-visual-studio-code.md)