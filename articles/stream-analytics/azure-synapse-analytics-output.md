---
title: Azure Stream Analytics からの Azure Synapse Analytics 出力
description: この記事では、Azure Stream Analytics の出力としての Azure Synapse Analytics について説明します。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 6c61f378dd9121c727fc245d177e11921a8a8e26
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110094386"
---
# <a name="azure-synapse-analytics-output-from-azure-stream-analytics"></a>Azure Stream Analytics からの Azure Synapse Analytics 出力

[Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics) は、エンタープライズ データ ウェアハウスとビッグ データ分析が統合された制限のない分析サービスです。 

Azure Stream Analytics ジョブを使用すると、Azure Synapse Analytics 内の専用 SQL プール テーブルに出力でき、最大 200 MB/秒のスループット レートを処理できます。これにより、レポートやダッシュボードなどのワークロードに対して、最も要求の厳しいリアルタイム分析とホットパス データ処理がサポートされます。  

Stream Analytics ジョブに出力として専用 SQL プール テーブルを追加するには、事前にそれが存在している必要があります。 テーブルのスキーマを、使用するジョブの出力内のフィールドとその型と一致させる必要があります。 

> [!NOTE] 
> Azure Synapse Analytics を出力として使用するためには、ストレージ アカウントが出力レベルではなくジョブ レベルで設定されていることを確認してください。 ストレージ アカウントの設定を変更するには、Stream Analytics ジョブの **[構成]** メニューから **[ストレージ アカウントの設定]** に移動します。 必ず、テーブルをサポートするストレージ アカウントの種類 (General Purpose V2 および General Purpose V1) を使用してください。 必ず Standard レベルを選択してください。 Premium レベルは、このシナリオではサポートされません。

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

* [マネージド ID を使用して Azure Stream Analytics ジョブから Azure SQL Database または Azure Synapse Analytics にアクセスする (プレビュー)](sql-database-output-managed-identity.md)
* [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)
