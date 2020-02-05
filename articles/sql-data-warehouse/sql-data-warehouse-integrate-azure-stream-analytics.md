---
title: Azure Stream Analytics の使用
description: ソリューション開発のための、Azure SQL Data Warehouse での Azure Stream Analytics の使用に関するヒント。
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: integration
ms.date: 03/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a655ada93cd9db9db95295d445c0b4f27d772148
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721202"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Azure Synapse Analytics での Azure Stream Analytics の使用
Azure Stream Analytics は、待機時間の短縮、高可用性、クラウド内のデータのストリーミング データに対する拡張性の高い複雑なイベント処理を実現する、フル マネージドのサービスです。 基本事項については、「 [Azure Stream Analytics の概要](../stream-analytics/stream-analytics-introduction.md)」をお読みください。 その後で、「 [Azure Stream Analytics の使用](../stream-analytics/stream-analytics-real-time-fraud-detection.md) 」チュートリアルに従って、Stream Analytics を使用してエンド ツー エンド ソリューションを作成する方法を知ることができます。

この記事では、データ ウェアハウス データベースを Stream Analytics ジョブの出力シンクとして使用する方法について説明します。

## <a name="prerequisites"></a>前提条件
最初に、「 [Azure Stream Analytics の使用](../stream-analytics/stream-analytics-real-time-fraud-detection.md) 」チュートリアルの次の手順を通読します。  

1. Event Hub 入力の作成
2. イベント ジェネレーター アプリケーションの構成と起動
3. Stream Analytics のジョブの準備
4. ジョブの入力とクエリの指定

次に、Azure SQL Data Warehouse データベースを作成します

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>ジョブの出力の指定:Azure SQL Data Warehouse データベース
### <a name="step-1"></a>手順 1
Stream Analytics ジョブで、ページ上部の **[出力]** をクリックし、 **[追加]** をクリックします。

### <a name="step-2"></a>手順 2.
SQL Database を選択します。

### <a name="step-3"></a>手順 3.
次の値を次のページに入力します。

* *[出力のエイリアス]* :このジョブの出力のフレンドリ名を入力します。
* *サブスクリプション*:
  * SQL Data Warehouse データベースが Stream Analytics ジョブと同じサブスクリプション内に存在する場合は、[現在のサブスクリプションの SQL データベースを使用] を選択します。
  * データベースが別のサブスクリプション内にある場合は、[別のサブスクリプションの SQL データベースを使用] を選択します。
* *データベース*:宛先データベースの名前を指定します。
* *[サーバー名]* : 指定したデータベース用のサーバー名を指定します。 これは、Azure Portal を使って見つけることができます。

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png)

* *User Name*:データベースの書き込みアクセス許可を持つアカウントのユーザー名を指定します。
* *パスワード*:指定したユーザー アカウントのパスワードを入力します。
* *テーブル*:データベース内の対象テーブルの名前を指定します。

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png)

### <a name="step-4"></a>手順 4.
チェック ボタンをクリックして、このジョブ出力を追加し、Stream Analytics がデータベースに適切に接続できることを確認します。

データベースへの接続が成功すると、ポータルに通知が表示されます。 [テスト] をクリックすると、データベースへの接続をテストできます。

## <a name="next-steps"></a>次のステップ
統合の概要については、[その他のサービスの統合](sql-data-warehouse-overview-integrate.md)に関する記事を参照してください。
開発に関するその他のヒントについては、[データ ウェアハウスの設計上の決定とコーディング技法](sql-data-warehouse-overview-develop.md)に関する記事を参照してください。

