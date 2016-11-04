---
title: Azure SQL Database のセキュリティのガイドラインと制限事項 | Microsoft Docs
description: Microsoft Azure SQL Database のガイドラインとセキュリティに関連する制限事項について説明します。
services: sql-database
documentationcenter: ''
author: BYHAM
manager: jhubbard
editor: ''
tags: ''

ms.service: sql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 08/02/2016
ms.author: rickbyh

---
# Azure SQL Database のセキュリティのガイドラインと制限事項
このトピックでは、Microsoft Azure SQL Database のガイドラインとセキュリティに関連する制限事項について説明します。Azure SQL Database のセキュリティを管理するときには、以下の点に注意してください。

## 仮想マスター データベースへのアクセス
通常、マスター データベースにアクセスできるのは管理者のみである必要があります。各ユーザー データベースに定期的にアクセスするには、各データベースで作成された包含データベース ユーザー (管理者以外) を使用する必要があります。包含データベース ユーザーを使用する場合は、マスター データベースでログインを作成する必要はありません。詳細については、「[包含データベース ユーザー - データベースの可搬性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」を参照してください。

## ファイアウォール
Azure SQL Server 全体を対象とする SQL Server ファイアウォールは、通常、ポータルで構成され、管理者が使用する IP アドレスのみを許可します。データベースごとに必要な IP アドレス範囲を開くデータベース スコープのファイアウォール規則を作成するには、ユーザー データベースに接続し、[sp\_set\_database\_firewall\_rule](https://msdn.microsoft.com/library/dn270010.aspx) Transact-SQL ステートメントを使用します。

Azure SQL Database サービスは TCP ポート 1433 経由でのみ利用できます。コンピューターから SQL Database にアクセスするには、クライアント コンピューターのファイアウォールで、TCP ポート 1433 での TCP 通信の発信を許可する必要があります。他のアプリケーションで必要ない場合は、TCP ポート 1433 での着信接続をブロックします。

接続処理の一部として、Azure 仮想マシンからの接続は worker ロールごとに異なる別の IP アドレスとポートにリダイレクトされます。ポート番号の範囲は、11000 ～ 11999 です。TCP ポートの詳細については、「[ADO.NET 4.5、SQL Database V12 における 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)」をご覧ください。

## 認証
可能いな限り、Active Directory 認証 (統合セキュリティ) を使用します。詳細については、「[Azure Active Directory 認証を使用して SQL Database に接続する](sql-database-aad-authentication.md)」、および SQL Server オンライン ブックの「[認証モードの選択](https://msdn.microsoft.com/library/ms144284.aspx)」をご覧ください。

スケーラビリティを高めるには、包含データベース ユーザーを使用します。詳細については、「[包含データベース ユーザー - データベースの可搬性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」、「[CREATE USER (TRANSACT-SQL)](https://technet.microsoft.com/library/ms173463.aspx)」、「[包含データベース](https://technet.microsoft.com/library/ff929071.aspx)」をご覧ください。

データベース エンジンは、30 分以上アイドル状態が続くと接続を閉じます。接続する前に、もう一度ログインする必要があります。

SQL Database への継続したアクティブな接続では、少なくとも 10 時間ごとに再認証が必要になります (データベース エンジンによって実行されます)。データベース エンジンは最初に送信されたパスワードを使用して再認証を試みるため、ユーザー入力は必要ありません。パフォーマンス上の理由により、パスワードが SQL Database でリセットされると、接続プールが原因で接続がリセットされた場合でも、接続は再認証されません。これは、オンプレミスの SQL Server とは異なる動作です。接続が最初に承認されて以来パスワードが変更されている場合は、その接続を終了し、新しいパスワードを使用して新しい接続を作成する必要があります。KILL DATABASE CONNECTION 権限を持つユーザーは、[KILL](https://msdn.microsoft.com/library/ms173730.aspx) コマンドを使用して SQL Database への接続を明示的に終了できます。

## ログインとユーザー
SQL Database でのログインとユーザーの管理には、制限があります。

* ``CREATE/ALTER/DROP DATABASE`` ステートメントを実行する場合は、**master** データベースに接続する必要があります。サーバーレベル プリンシパル ログインに対応する master データベースのデータベース ユーザーは、変更または削除できません。
* サーバーレベル プリンシパル ログインの既定の言語は英語 (米国) です。
* **master** データベースにアクセスするには、各ログインは **master** データベースのユーザー アカウントにマップされている必要があります。**master** データベースは、包含データベース ユーザーをサポートしていません。
* サーバーレベル プリンシパル ログインと、**master** データベースの **dbmanager** データベース ロールのメンバーにのみ、``CREATE DATABASE`` と ``DROP DATABASE`` ステートメントを実行する権限があります。
* ``CREATE/ALTER/DROP LOGIN`` ステートメントを実行する場合は、master データベースに接続する必要があります。ただし、ログインの使用はお勧めできません。代わりに、包含データベース ユーザーを使用してください。
* ユーザー データベースに接続するには、接続文字列にそのデータベースの名前を指定する必要があります。
* サーバーレベル プリンシパル ログインと、**master** データベースの **loginmanager** データベース ロールのメンバーにのみ、``CREATE LOGIN``、``ALTER LOGIN``、``DROP LOGIN`` ステートメントを実行する権限があります。
* ADO.NET アプリケーションで ``CREATE/ALTER/DROP LOGIN`` と ``CREATE/ALTER/DROP DATABASE`` ステートメントを実行する場合、パラメーター化コマンドは使用できません。詳細については、「[コマンドとパラメーター](https://msdn.microsoft.com/library/ms254953.aspx)」をご覧ください。
* ``CREATE/ALTER/DROP DATABASE`` と ``CREATE/ALTER/DROP LOGIN`` ステートメントを実行する場合、これらの各ステートメントは、Transact-SQL バッチ内の唯一のステートメントである必要があります。一致しないと、エラーが発生します。たとえば、以下の Transact-SQL は、データベースが存在するかどうかを確認します。存在する場合は、``DROP DATABASE`` ステートメントが呼び出され、データベースが削除されます。``DROP DATABASE`` ステートメントはバッチ内の唯一のステートメントではないので、これを実行すると Transact-SQL はエラーになります。

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

* ``CREATE USER`` ステートメントを ``FOR/FROM LOGIN`` オプションと共に実行する場合、これが Transact-SQL バッチ内の唯一のステートメントである必要があります。
* ``ALTER USER`` ステートメントを ``WITH LOGIN`` オプションと共に実行する場合、これが Transact-SQL バッチ内の唯一のステートメントである必要があります。
* ユーザーに対して ``CREATE/ALTER/DROP`` を実行するには、データベースに対する ``ALTER ANY USER`` 権限が必要です。
* データベース ロールの所有者が、そのデータベース ロールに対して他のデータベース ユーザーの追加または削除を行おうとすると、「**User or role 'Name' does not exist in this database.**」というエラーが発生する場合があります。 このエラーは、所有者からはユーザーが見えないために発生します。この問題を解決するには、そのユーザーに対する ``VIEW DEFINITION`` 権限をロールの所有者に許可します。

ログインとユーザーの詳細については、「[Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)」をご覧ください。

## 関連項目
[Azure SQL Database ファイアウォール](sql-database-firewall-configure.md)

[ファイアウォール設定の構成方法 (Azure SQL Database)](sql-database-configure-firewall-settings.md)

[Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)

[SQL Server Database エンジンと Azure SQL Database のセキュリティ センター](https://msdn.microsoft.com/library/bb510589)

<!---HONumber=AcomDC_0803_2016-->