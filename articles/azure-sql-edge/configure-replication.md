---
title: Azure SQL Edge (プレビュー) へのレプリケーションを構成する
description: Azure SQL Edge (プレビュー) へのレプリケーションを構成する方法について説明します
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e2b37e0f3ccf5fcebe4723c05d644f2cbb7c1d56
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593991"
---
# <a name="configure-replication-to-azure-sql-edge-preview"></a>Azure SQL Edge (プレビュー) へのレプリケーションを構成する 

Azure SQL Edge インスタンスは、一方向のトランザクション レプリケーションまたはスナップショット レプリケーションのプッシュ サブスクライバーとして構成できます。 Azure SQL Edge インスタンスを、トランザクション レプリケーション構成のパブリッシャーまたはディストリビューターとして機能させることはできません。 マージ レプリケーション、P2P レプリケーション、Oracle パブリッシングは、Azure SQL Edge ではサポートされていません。

## <a name="supported-configurations"></a>**サポートされている構成**:
  
- Azure SQL Edge インスタンスは、パブリッシャーのプッシュ サブスクライバーである必要があります。
- 次のいずれかのパブリッシャーおよびディストリビューターを使用できます:
   - オンプレミスで実行されている SQL Server のインスタンス、または Azure 仮想マシンで実行されている SQL Server のインスタンス。 詳細については、[Azure Virtual Machines 上の SQL Server の概要](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/)に関するページをご覧ください。 SQL Server インスタンスでは、SQL Server 2016 よりも大きいバージョンが使用されている必要があります。
   - Azure SQL Database Managed Instance のインスタンス。 マネージド インスタンスでは、パブリッシャー、ディストリビューター、およびサブスクライバー データベースをホストできます。 詳細については、「[Replication with SQL Database Managed Instance (SQL Database Managed Instance でのレプリケーション)](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/)」を参照してください。

- ディストリビューション データベースとレプリケーション エージェントを Azure SQL Edge インスタンスに配置することはできません。  

> [!NOTE]
> サポートされていないバージョンを使用してレプリケーションを構成しようとすると、エラー番号 MSSQL_REPL20084 (プロセスはサブスクライバーに接続できませんでした) および MSSQL_REPL40532 (ログインによって要求されたサーバー \<name > を開くことができません。 ログインに失敗しました) のエラーが発生する可能性があります。  

Azure SQL Edge のすべての機能を使用するには、最新バージョンの [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) および [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) を使用する必要があります。  

## <a name="remarks"></a>解説

- レプリケーションを構成するには、[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) を使用するか、SQL Server Management Studio または [Azure Database Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio) を使用してパブリッシャーで Transact-SQL ステートメントを実行します
- レプリケーションで Azure SQL Edge インスタンスに接続するために使用できるのは、SQL Server 認証ログインのみです。
- レプリケーション テーブルには主キーが必要です。
- SQL Server 上の単一パブリケーションで、Azure SQL Edge と、SQL Server (オンプレミスおよび Azure 仮想マシンの SQL Server) の両方のサブスクライバーをサポートできます。  
- レプリケーションの管理、監視、およびトラブルシューティングは、オンプレミスの SQL Server から実行する必要があります。  
- Azure SQL Edge へのプッシュ サブスクリプションのみがサポートされています。  
- Azure SQL Edge の **sp_addsubscription** では `@subscriber_type = 0` のみがサポートされています。  
- Azure SQL Edge では、双方向、即時、更新可能、またはピア ツー ピアのレプリケーションはサポートされていません。
- Azure SQL Edge では、SQL Server または Azure SQL Database Managed Instance で使用できる機能のサブセットのみがサポートされています。そのため、サポートされていない機能を 1 つ以上含むデータベース (またはデータベース内のオブジェクト) をレプリケートしようとすると、エラーが発生します。 たとえば、空間データ型のオブジェクトを含むデータベースをレプリケートしようとすると、エラーが発生します。 Azure SQL Edge でサポートされている機能の詳細については、[Azure SQL Edge のサポートされている機能](features.md)に関する記事をご覧ください。

## <a name="scenarios"></a>シナリオ  

### <a name="initializing-reference-data-on-an-edge-instance"></a>Edge インスタンス上の参照データの初期化

レプリケーションが役立つ一般的なシナリオは、時間の経過と共に変化する参照データを含む Edge インスタンスを初期化する必要がある場合です。 たとえば、Edge インスタンス上の ML モデルを、オンプレミスの SQL Server インスタンス上でトレーニングした後に更新する場合です。

1. オンプレミスの SQL Server データベースで、トランザクション レプリケーション パブリケーションを作成します。  
2. オンプレミスの SQL Server で、**新しいサブスクリプション ウィザード**または Transact-SQL ステートメントを使用して、Azure SQL Edge のサブスクリプションに対するプッシュを作成します。  
3. Azure SQL Edge 上のレプリケートされたデータベースを初期化するには、スナップショット エージェントによって生成され、ディストリビューション エージェントによって配布および配信されたスナップショットを使用するか、パブリッシャーからのデータベースのバックアップを使用します。 ここでも、データベースのバックアップに Azure SQL Edge でサポートされていないオブジェクトまたは機能が含まれていた場合、復元操作は失敗します。

## <a name="limitations"></a>制限事項

次のオプションは、Azure SQL Edge サブスクリプションではサポートされていません。

- ファイル グループの関連付けのコピー  
- テーブルのパーティション分割構成のコピー  
- インデックスのパーティション分割構成のコピー  
- ユーザー定義の統計情報のコピー  
- 既定のバインドのコピー  
- ルールのバインドのコピー  
- フルテキスト インデックスのコピー  
- XML XSD のコピー  
- XML インデックスのコピー  
- アクセス許可のコピー  
- 空間インデックスのコピー  
- フィルター処理済みインデックスのコピー  
- データ圧縮属性のコピー  
- スパース列属性のコピー  
- filestream、hierarchyid、または空間データ型のコピー。
- MAX データ型への hierarchyid の変換  
- MAX データ型への spatial の変換  
- 拡張プロパティのコピー  
- アクセス許可のコピー  

## <a name="examples"></a>例

パブリケーションおよびプッシュ サブスクリプションを作成します。 詳細については、次を参照してください。
  
- [パブリケーションを作成する](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- Azure SQL Edge サーバー名または IP (例: **myEdgeinstance,1433**) をサブスクライバーとして使用し、Azure SQL Edge インスタンスのデータベース名 (例: **AdventureWorks**) を宛先データベースとして使用することで、[プッシュ サブスクリプションを作成します](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)。  

## <a name="see-also"></a>関連項目  

- [パブリケーションを作成する](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [プッシュ サブスクリプションを作成する](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [レプリケーションの種類](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [監視 (レプリケーション)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [サブスクリプションを初期化する](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  


