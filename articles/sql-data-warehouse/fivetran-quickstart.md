---
title: Azure SQL Data Warehouse での Fivetran クイック スタート | Microsoft Docs
description: Fivetran と Azure SQL Data Warehouse の使用をすぐに開始します。
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 8e738becfe356908af5baffc0ebf225916b2616e
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2018
ms.locfileid: "49355294"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Fivetran と Azure SQL Data Warehouse の使用をすぐに開始する

このクイックスタートでは、SQL Data Warehouse の既存のインスタンスが既にあることを前提とします。

## <a name="setup-connection"></a>接続のセットアップ

1. Azure SQL Data Warehouse に接続するための完全修飾サーバー名およびデータベース名を検索します。

   [ポータルからサーバー名とデータベース名を検索する方法](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connect-overview)

2. セットアップ ウィザードで、データベースに直接接続するか、SSH トンネルを介して接続するかを決定します。

   データベースに直接接続する場合は、アクセスを許可するファイアウォール ルールを作成する必要があります。 この方法は、最も簡単かつ最も安全な方法です。

   SSH トンネル経由で接続する場合、Fivetran はデータベースへの SSH トンネルを提供するネットワーク内の別のサーバーに接続されます。 この方法は、データベースが仮想ネットワーク上のアクセスできないサブネット内にある場合に必要です。

3. "52.0.2.4" IP アドレスをサーバー レベルのファイアウォールに追加して、Fivetran から Azure SQL Data Warehouse への着信接続を許可します。

   [サーバー レベルのファイアウォールを追加する方法](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal#create-a-server-level-firewall-rule)

## <a name="setup-user-credentials"></a>ユーザー資格情報の設定

SQL Server Management Studio または任意のツールをサーバー管理者ユーザーとして使用して Azure SQL Data Warehouse に接続し、次の SQL コマンドを実行して Fivetran のユーザーを作成します。

マスター データベースの場合: ` CREATE LOGIN fivetran WITH PASSWORD = '<password>'; `

SQL Data Warehouse データベースの場合:

```
CREATE USER fivetran_user_without_login without login;
CREATE USER fivetran FOR LOGIN fivetran;
GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
```

ユーザー fivetran が作成されたら、ウェハウスに次のアクセス許可を付与します。

```
GRANT CONTROL to fivetran;
```

列ストア インデックスの作成のメモリ要件に応じて、作成したユーザーに適切なリソース クラスを追加します。 たとえば、Marketo や Salesforce などの統合は、大量の列/大量のデータがあるため、より高いリソース クラスが必要で、列ストア インデックスを作成するためにより多くのメモリが必要です。

静的リソース クラスの使用をお勧めします。 使用するパフォーマンス レベルに関係なくユーザーに 200 MB を割り当てるリソース クラス `staticrc20` から開始できます。 現在のリソース クラスを使用して列ストア インデックスの作成に失敗した場合は、リソース クラスを上げる必要があります。

```
EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
```

詳しくは、[メモリとコンカレンシーの制限](https://docs.microsoft.com/azure/sql-data-warehouse/memory-and-concurrency-limits#data-warehouse-limits)と[リソース クラス](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression#ways-to-allocate-more-memory)のドキュメントをご覧ください

PolyBase を使用して Blob Storage からファイルを読み込み中に使用されるデータベース スコープ資格情報を作成するには、CONTROL アクセス許可が必要です。

資格情報を入力して Azure SQL Data Warehouse にアクセスします

1. ホスト (自分のサーバー名)
2. ポート
3. Database
4. ユーザー (ユーザー名は `fivetran@<server_name>` にする必要があります。`<server_name>` は azure のホスト uri の一部です: `<server_name>.database.windows.net`)
5. パスワード