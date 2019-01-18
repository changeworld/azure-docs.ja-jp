---
title: Azure SQL Database の接続とクエリに関するクイック スタート | Microsoft Docs
description: Azure SQL Database の各種クイック スタートでは、Azure SQL データベースに接続してクエリを実行する方法を紹介しています。
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 11/01/2018
ms.openlocfilehash: 613b4cf2b08269259a4608a6960b815777cd0ae9
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/19/2018
ms.locfileid: "53608036"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>クイック スタート:Azure SQL Database の接続とクエリ

次のドキュメントは、Azure SQL データベースに接続してクエリを実行する例を紹介した Azure ページへのリンク集です。 トランスポート レベルのセキュリティについての推奨事項も提供されています。

## <a name="quickstarts"></a>クイック スタート

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|このクイック スタートでは、SSMS を使って Azure SQL データベースに接続し Transact-SQL ステートメントを使ってデータベース内のデータを照会、挿入、更新、削除する方法について説明します。|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|このクイック スタートでは、Azure Data Studio を使用して Azure SQL データベースに接続した後、Transact-SQL (T-SQL) ステートメントを使用して Azure Data Studio チュートリアルで使用される TutorialDB を作成する方法を示します。|
|[Azure Portal](sql-database-connect-query-portal.md)|このクイック スタートでは、クエリ エディターを使用して SQL Database に接続し、Transact-SQL ステートメントを使用してデータベース内のデータを照会、挿入、更新、および削除する方法について説明します。|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|このクイック スタートでは、Visual Studio Code を使って Azure SQL データベースに接続し、Transact-SQL ステートメントを使ってデータベース内のデータを照会、挿入、更新、削除する方法について説明します。|
|[Visual Studio での .NET](sql-database-connect-query-dotnet-visual-studio.md)|このクイック スタートでは、Azure SQL データベースに接続して Transact-SQL ステートメントでデータを照会する C# プログラムを Visual Studio と .NET Framework を使って作成する方法について説明します。|
|[.NET core](sql-database-connect-query-dotnet-core.md)|このクイック スタートでは、Azure SQL Database に接続して Transact-SQL ステートメントでデータを照会する C# プログラムを Windows/Linux/macOS 上の .NET Core を使って作成する方法について説明します。|
|[Go](sql-database-connect-query-go.md)|このクイック スタートでは、Go を使用して Azure SQL データベースに接続する方法を紹介します。 Transact-SQL ステートメントを使用してデータを照会および変更する方法についても説明します。|
|[Java](sql-database-connect-query-java.md)|このクイック スタートでは、Java を使って Azure SQL データベースに接続した後、Transact-SQL ステートメントを使ってデータを照会する方法について説明します。|
|[Node.js](sql-database-connect-query-nodejs.md)|このクイック スタートでは、Azure SQL データベースに接続して Transact-SQL ステートメントでデータを照会するプログラムを Node.js を使って作成する方法について説明します。|
|[PHP](sql-database-connect-query-php.md)|このクイック スタートでは、Azure SQL データベースに接続して Transact-SQL ステートメントでデータを照会するプログラムを PHP を使って作成する方法について説明します。|
|[Python](sql-database-connect-query-python.md)|このクイック スタートでは、Python を使って Azure SQL データベースに接続した後、Transact-SQL ステートメントを使ってデータを照会する方法について説明します。 |
|[Ruby](sql-database-connect-query-ruby.md)|このクイック スタートでは、Azure SQL データベースに接続して Transact-SQL ステートメントでデータを照会するプログラムを Ruby を使って作成する方法について説明します。|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>SQL Database の接続に関する TLS の考慮事項
トランスポート層セキュリティ (TLS) は、Azure SQL Database への接続のために Microsoft が提供またはサポートしているすべてのドライバーによって使われます。 特別な構成は必要ありません。 SQL Server または Azure SQL Database へのすべての接続について、すべてのアプリケーションで次の構成またはそれと同等の構成を設定することをお勧めします。

 - **Encrypt = On**
 - **TrustServerCertificate = Off**

一部のシステムでは、これらの構成キーワードとは異なりますが同等のキーワードが使われています。 これらの構成を指定すると、サーバーから受信した TLS 証明書の ID をクライアント ドライバーが確認することが保証されます。

また、Payment Card Industry - データ セキュリティ基準 (PCI-DSS) に準拠する必要がある場合は、クライアントにおいて TLS 1.1 および 1.0 を無効にすることをお勧めします。

Microsoft 以外のドライバーは、既定では TLS を使用しない場合があります。 これは Azure SQL Database に接続するときに問題になる可能性があります。 埋め込みドライバーを使用するアプリケーションでは、これらの接続設定を制御できない場合があります。 このようなドライバーおよびアプリケーションについては、機密データを操作するシステムで使う前に、そのセキュリティを調べることをお勧めします。

## <a name="next-steps"></a>次の手順

接続アーキテクチャについては、「[Azure SQL Database 接続アーキテクチャ](sql-database-connectivity-architecture.md)」をご覧ください。