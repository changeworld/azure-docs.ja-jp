---
title: Azure Analysis Services でサポートされるデータ ソース | Microsoft Docs
description: Azure Analysis Services のデータ モデルでサポートされるデータ ソースについて説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f6ad95eb45cc208fe2289cb2095214f98a0b250b
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442391"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Azure Analysis Services でサポートされるデータ ソース

Visual Studio のデータ ファイル指定ウィザードまたはインポート ウィザードで表示されるデータ ソースとコネクタは、Azure Analysis Services と SQL Server Analysis Services の両方に対するものです。 ただし、表示されているすべてのデータ ソースとコネクタが、Azure Analysis Services でサポートされているわけではありません。 接続できるデータ ソースの種類は、モデルの互換性レベル、使用可能なデータ コネクタ、認証の種類、プロバイダー、オンプレミスのデータ ゲートウェイのサポートなど、さまざまな要因に依存します。 

## <a name="azure-data-sources"></a>Azure データ ソース

|データソース  |メモリ内  |DirectQuery  |
|---------|---------|---------|
|Azure SQL Database     |   [はい]      |    [はい]      |
|Azure SQL Data Warehouse     |   [はい]      |   [はい]       |
|Azure Blob Storage*     |   [はい]       |    いいえ       |
|Azure Table Storage*    |   [はい]       |    いいえ       |
|Azure Cosmos DB*     |  [はい]        |  いいえ         |
|Azure Data Lake Store*     |   [はい]       |    いいえ       |
|Azure HDInsight HDFS*     |     [はい]     |   いいえ        |
|Azure HDInsight Spark*     |   [はい]       |   いいえ        |
||||

\* 表形式 1400 モデルのみ。

**プロバイダー**   
Azure データ ソースに接続するメモリ内モデルおよび DirectQuery モデルは、.NET Framework SQL Server 用データ プロバイダーを使います。

## <a name="on-premises-data-sources"></a>オンプレミス データ ソース

Azure AS サーバーからオンプレミスのデータ ソースに接続するには、オンプレミスのゲートウェイが必要です。 ゲートウェイを使うときは、64 ビットのプロバイダーが必要です。

### <a name="in-memory-and-directquery"></a>メモリ内と DirectQuery

|データソース | メモリ内のプロバイダー | DirectQuery プロバイダー |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0、Microsoft OLE DB Provider for SQL Server、.NET Framework SQL Server 用データ プロバイダー | SQL Server 用の .NET Framework データ プロバイダー |
| SQL Server Data Warehouse |SQL Server Native Client 11.0、Microsoft OLE DB Provider for SQL Server、.NET Framework SQL Server 用データ プロバイダー | SQL Server 用の .NET Framework データ プロバイダー |
| Oracle |Microsoft OLE DB Provider for Oracle、Oracle Data Provider for .NET |Oracle Data Provider for .NET | |
| Teradata |OLE DB Provider for Teradata、Teradata Data Provider for .NET |Teradata Data Provider for .NET | |
| | | |

### <a name="in-memory-only"></a>メモリ内のみ

|データソース  |  
|---------|---------|
|Access データベース     |  
|Active Directory*     |  
|Analysis Services     |  
|Analytics Platform System     |  
|Dynamics CRM*     |  
|Excel ブック     |  
|Exchange*     |  
|フォルダー*     |
|IBM Informix* (ベータ) |
|JSON ドキュメント*     |  
|バイナリからの行*     | 
|MySQL Database     | 
|OData フィード*     |  
|ODBC クエリ     | 
|OLE DB     |   
|Postgre SQL データベース*    | 
|Salesforce オブジェクト* |  
|Salesforce レポート* |
|SAP HANA*    |  
|SAP Business Warehouse*    |  
|SharePoint*     |   
|Sybase データベース     |  
|XML テーブル*    |  
|||
 
\* 表形式 1400 モデルのみ。

## <a name="specifying-a-different-provider"></a>別のプロバイダーを指定する

Azure Analysis Services のデータ モデルでは、特定のデータ ソースに接続するときに異なるデータ プロバイダーが必要になる場合があります。 場合によっては、SQL Server Native Client (SQLNCLI11) などのネイティブ プロバイダーを使ってデータ ソースに接続する表形式モデルがエラーを返すことがあります。 SQLOLEDB 以外のネイティブ プロバイダーを使っている場合、"**プロバイダー 'SQLNCLI11.1' が登録されていません**" というエラー メッセージが表示されることがあります。 または、オンプレミスのデータ ソースに接続する直接クエリ モデルがあり、ネイティブ プロバイダーを使っている場合は、"**Error creating OLE DB row set.Incorrect syntax near 'LIMIT'**" (OLE DB 行セットの作成でエラーが発生しました。'LIMIT' の周辺に正しくない構文があります。) というエラー メッセージが表示される場合があります。

オンプレミスの SQL Server Analysis Services 表形式モデルを Azure Analysis Services に移行する場合は、プロバイダーの変更が必要になることがあります。

**プロバイダーを指定するには**

1. SSDT > **表形式モデル エクスプローラー** > **[データ ソース]** で、データ ソース接続を右クリックし、**[データ ソースの編集]** をクリックします。
2. **[接続の編集]** で、**[詳細]** をクリックして詳細プロパティ ウィンドウを開きます。
3. **[詳細プロパティの設定]** > **[プロバイダー]** で、適切なプロバイダーを選びます。

## <a name="impersonation"></a>権限の借用
場合によっては、異なる権限借用アカウントの指定が必要になることがあります。 権限借用アカウントは、Visual Studio (SSDT) または SSMS で指定できます。

オンプレミスのデータ ソースの場合:

* SQL 認証を使っている場合、権限借用にはサービス アカウントを使う必要があります。
* Windows 認証を使っている場合は、Windows ユーザーとパスワードを設定します。 SQL Server では、特定の権限借用アカウントでの Windows 認証は、メモリ内データ モデルに対してのみサポートされます。

クラウド データ ソースの場合:

* SQL 認証を使っている場合、権限借用にはサービス アカウントを使う必要があります。

## <a name="next-steps"></a>次の手順
[オンプレミス ゲートウェイ](analysis-services-gateway.md)   
[サーバーの管理](analysis-services-manage.md)   

