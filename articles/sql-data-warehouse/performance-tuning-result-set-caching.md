---
title: 結果セットのキャッシュを使用したパフォーマンスのチューニング |Microsoft Docs
description: 機能の概要
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.openlocfilehash: f6323501fc0078677c4c0e2cd0e43a15583df29b
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72513987"
---
# <a name="performance-tuning-with-result-set-caching"></a>結果セットのキャッシュを使用したパフォーマンスのチューニング  
結果セットのキャッシュが有効にされている場合、Azure SQL Data Warehouse では、繰り返し使用するためにクエリ結果がユーザー データベースに自動的にキャッシュされます。  これにより、以降のクエリ実行で永続キャッシュから直接結果を取得できるため、再計算は必要ありません。   結果セットのキャッシュにより、クエリのパフォーマンスが向上し、コンピューティング リソースの使用量が減少します。  さらに、キャッシュされた結果セットを使用するクエリはコンカレンシー スロットをまったく使用しないため、既存のコンカレンシー制限にはカウントされません。 セキュリティのため、キャッシュされた結果にアクセスできるのは、そのユーザーがキャッシュされた結果を作成したユーザーと同じデータ アクセス許可を持っている場合のみです。  

## <a name="key-commands"></a>主なコマンド
[ユーザー データベースに対する結果セットのキャッシュをオン/オフにする](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[セッションに対する結果セットのキャッシュをオン/オフにする](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[キャッシュされた結果セットのサイズを確認する](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[キャッシュをクリーンアップする](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>キャッシュされないもの  

結果セットのキャッシュがデータベースに対してオンになると、キャッシュがいっぱいになるまで、すべてのクエリに対して結果がキャッシュされます。ただし、次のクエリは除きます。
- DateTime.Now() などの非決定論的関数を使用するクエリ
- ユーザー定義関数を使用したクエリ
- 64 KB を超える行サイズのデータを返すクエリ

大規模な結果セット (たとえば、100 万行を超えるもの) を含むクエリでは、結果キャッシュが作成される最初の実行時にパフォーマンスが低下する可能性があります。

行レベルのセキュリティは、結果セットのキャッシュではサポートされていません。  

## <a name="when-cached-results-are-used"></a>キャッシュされた結果が使用される場合

次のすべての要件が満たされている場合は、キャッシュされた結果セットがクエリで再利用されます。
- クエリを実行しているユーザーに、クエリで参照されているすべてのテーブルへのアクセス権がある。
- 新しいクエリと、結果セットのキャッシュを生成した前のクエリが完全に一致している。
- キャッシュされた結果セットが生成されたテーブル内でデータおよびスキーマの変更がない。

次のコマンドを実行して、クエリが結果キャッシュ ヒットで実行されたのか、結果キャッシュ ミスで実行されたのかを確認します。 キャッシュ ヒットがあった場合、result_cache_hit は 1 を返します。

```sql
SELECT request_id, command, result_cache_hit FROM sys.pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>キャッシュされた結果の管理 

結果セットのキャッシュの最大サイズは、データベースあたり 1 TB です。  基になるクエリ データが変更されると、キャッシュされた結果は自動的に無効になります。  

キャッシュの削除は、次のスケジュールに従って Azure SQL Data Warehouse で自動的に管理されます。 
- 結果セットが使用されていない場合、または無効になっている場合は、48 時間ごと。 
- 結果セットのキャッシュが最大サイズに近づいた場合。

ユーザーは、次のいずれかのオプションを使用して、結果セットのキャッシュ全体を手動で空にすることができます。 
- データベースの結果セットのキャッシュ機能をオフにする 
- データベースに接続しているときに DBCC DROPRESULTSETCACHE を実行する

データベースを一時停止しても、キャッシュされた結果セットは空になりません。  

## <a name="next-steps"></a>次の手順
開発に関するその他のヒントについては、「 [SQL Data Warehouse development overview (SQL Data Warehouse の開発の概要)](sql-data-warehouse-overview-develop.md)」をご覧ください。 
