<properties
   pageTitle="Azure SQL Database のセキュリティのガイドラインと制限事項 | Microsoft Azure"
   description="Microsoft Azure SQL Database のガイドラインとセキュリティに関連する制限事項について説明します。"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jeffreyg"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="11/24/2015"
   ms.author="rickbyh"/>

# Azure SQL Database のセキュリティのガイドラインと制限事項

このトピックでは、Microsoft Azure SQL Database のガイドラインとセキュリティに関連する制限事項について説明します。Azure SQL Database のセキュリティを管理するときには、以下の点に注意してください。

## ファイアウォール

Azure SQL Database サービスは TCP ポート 1433 経由でのみ利用できます。コンピューターから SQL Database にアクセスするには、ファイアウォールで TCP ポート 1433 での TCP 通信の発信を許可する必要があります。接続処理の一部として、Azure 仮想マシンからの接続はワーカー ロールごとに異なる別の IP アドレスとポートにリダイレクトされます。ポート番号の範囲は、11000 ～ 11999 です。

最初に Azure SQL Database サーバーに接続する前に、[Azure ポータル](https://portal.azure.com)または [Azure Platform Management Portal](https://manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/All/dashboard) を使用して、Azure SQL Database ファイアウォールを構成する必要があります。コンピューターまたは Azure から Azure SQL Database サーバーへの接続試行を有効にする、サーバーレベルのファイアウォール設定を作成する必要があります。また、Azure SQL Database サーバー内の特定のデータベースへのアクセスを制御する場合は、それぞれのデータベースに対するデータベースレベルのファイアウォール ルールを作成します。詳細については、「[Azure SQL Database ファイアウォール](sql-database-firewall-configure.md)」を参照してください。

## 接続の暗号化と証明書の検証

SQL Database とアプリケーション間のすべての通信は、常に暗号化 (SSL) を必要とします。クライアント アプリケーションが接続時に証明書を検証しない場合、SQL Database への接続は "man in the middle" 攻撃を受けやすくなります。

アプリケーション コードやツールで証明書を検証するには、暗号化された接続を明示的に要求し、サーバー証明書は信頼しないようにします。アプリケーション コードやツールが、暗号化された接続を要求しない場合でも、暗号化された接続を受け付けることはできます。ただし、サーバー証明書は検証されず、"man in the middle" 攻撃を受けやすくなります。

ADO.NET アプリケーション コードで証明書を検証するには、データベース接続文字列で ``Encrypt=True`` と ``TrustServerCertificate=False`` を設定します。詳細については、「[方法: ADO.NET を使用して Azure SQL Database に接続する](https://msdn.microsoft.com/library/azure/ee336243.aspx)」をご覧ください。

SQL Server Management Studio も、証明書の検証をサポートしています。**[サーバーへの接続]** ダイアログ ボックスで、**[接続プロパティ]** タブの **[暗号化接続]** をクリックします。

> [AZURE.NOTE]SQL Server Management Studio は、SQL Server 2008 R2 より前のバージョンでは SQL Database への接続をサポートしていません。

SQLCMD は SQL Server 2008 以降で SQL Database をサポートしていますが、SQL Server 2008 R2 より前のバージョンでは証明書の検証をサポートしていません。SQL Server 2008 R2 以降の SQLCMD で証明書を検証するには、``-N`` コマンドライン オプションを使用し、``-C`` オプションは使用しないでください。-N オプションを使用することで、SQLCMD は暗号化された接続を要求するようになります。``-C`` オプションを使用しないことで、SQLCMD はサーバー証明書を暗黙的に信頼しなくなり、証明書の検証が強制されるようになります。

補足的な技術情報については、TechNet Wiki サイトにある [Azure SQL Database での接続セキュリティ](http://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#comment-4847)に関する記事をご覧ください。

## 認証

Active Directory 認証 (統合セキュリティ) は、SQL Database V12 のプレビューで使用できます。詳細については、「[Azure Active Directory 認証を使用して SQL Database に接続する](sql-database-aad-authentication.md)」を参照してください。プレビューを使用しない場合、ユーザーは SQL Database に接続するたびに、資格情報 (ログイン名とパスワード) を入力する必要があります。SQL Server 認証の詳細については、SQL Server オンライン ブックの「[認証モードの選択](https://msdn.microsoft.com/library/ms144284.aspx)」を参照してください。

[SQL Database V12](sql-database-v12-whats-new.md) では、ユーザーは包含データベース ユーザーを使用して、データベースを認証できます。詳細については、「[包含データベース ユーザー - データベースの可搬性を確保する](https://msdn.microsoft.com/library/ff929188.aspx)」、「[CREATE USER (Transact-SQL)](https://technet.microsoft.com/library/ms173463.aspx)」、「[包含データベース](https://technet.microsoft.com/library/ff929071.aspx)」をご覧ください。

> [AZURE.NOTE]Microsoft は、包含データベース ユーザーを使用してスケーラビリティを高めることをお勧めします。

データベース エンジンは、30 分以上アイドル状態が続くと接続を閉じます。接続する前に、もう一度ログインする必要があります。

SQL Database への継続したアクティブな接続では、少なくとも 10 時間ごとに再認証が必要になります (データベース エンジンによって実行されます)。データベース エンジンは最初に送信されたパスワードを使用して再認証を試みるため、ユーザー入力は必要ありません。パフォーマンス上の理由により、パスワードが SQL Database でリセットされると、接続プールが原因で接続がリセットされた場合でも、接続は再認証されません。これは、オンプレミスの SQL Server とは異なる動作です。接続が最初に承認されて以来パスワードが変更されている場合は、その接続を終了し、新しいパスワードを使用して新しい接続を作成する必要があります。KILL DATABASE CONNECTION 権限を持つユーザーは、[KILL](https://msdn.microsoft.com/library/ms173730.aspx) コマンドを使用して SQL Database への接続を明示的に終了できます。

## ログインとユーザー

SQL Database でのログインとユーザーの管理には、制限があります。

サーバーレベル プリンシパル ログインには、次の制限が適用されます。

- サーバーレベル プリンシパル ログインに対応する master データベースのデータベース ユーザーは、変更または削除できません。 
- サーバーレベル プリンシパル ログインは、**master** データベースの 2 つのデータベース ロール **dbmanager** と **loginmanager** のメンバーではありませんが、この 2 つのロールに許可されているすべての権限を持っています。

> [AZURE.NOTE]このログインは、サーバーのプロビジョニング中に作成され、SQL Server のインスタンスの **sa** ログインと似ています。

すべてのログインに、次の制限が適用されます。

- 既定の言語は、英語 (米国)です。
- **master** データベースにアクセスするには、各ログインは **master** データベースのユーザー アカウントにマップされている必要があります。**master** データベースは、包含データベース ユーザーをサポートしていません。
- 接続文字列でデータベースを指定しない場合は、既定で **master** データベースに接続されます。
- ``CREATE/ALTER/DROP LOGIN`` と ``CREATE/ALTER/DROP DATABASE`` ステートメントを実行する場合は、**master** データベースに接続する必要があります。 
- ADO.NET アプリケーションで ``CREATE/ALTER/DROP LOGIN`` と ``CREATE/ALTER/DROP DATABASE`` ステートメントを実行する場合、パラメーター化コマンドは使用できません。詳細については、「[コマンドとパラメーター](https://msdn.microsoft.com/library/ms254953.aspx)」をご覧ください。
- ``CREATE/ALTER/DROP DATABASE`` と ``CREATE/ALTER/DROP LOGIN`` ステートメントを実行する場合、これらの各ステートメントは、Transact-SQL バッチ内の唯一のステートメントである必要があります。一致しないと、エラーが発生します。たとえば、以下の Transact-SQL は、データベースが存在するかどうかを確認します。存在する場合は、``DROP DATABASE`` ステートメントが呼び出され、データベースが削除されます。``DROP DATABASE`` ステートメントはバッチ内の唯一のステートメントではないので、これを実行すると Transact-SQL はエラーになります。

```
IF EXISTS (SELECT [name]
           FROM   [sys].[databases]
           WHERE  [name] = N'database_name')
     DROP DATABASE [database_name];
GO
```

- ``CREATE USER`` ステートメントを ``FOR/FROM LOGIN`` オプションと共に実行する場合、これが Transact-SQL バッチ内の唯一のステートメントである必要があります。
- ``ALTER USER`` ステートメントを ``WITH LOGIN`` オプションと共に実行する場合、これが Transact-SQL バッチ内の唯一のステートメントである必要があります。
- サーバーレベル プリンシパル ログインと、**master** データベースの **dbmanager** データベース ロールのメンバーにのみ、``CREATE DATABASE`` と ``DROP DATABASE`` ステートメントを実行する権限があります。
- サーバーレベル プリンシパル ログインと、**master** データベースの **loginmanager** データベース ロールのメンバーにのみ、``CREATE LOGIN``、``ALTER LOGIN``、``DROP LOGIN`` ステートメントを実行する権限があります。
- ユーザーに対して ``CREATE/ALTER/DROP`` を実行するには、データベースに対する ``ALTER ANY USER`` 権限が必要です。
- データベース ロールの所有者が、そのデータベース ロールに対して他のデータベース ユーザーの追加または削除を行おうとすると、「**User or role 'Name' does not exist in this database.**」というエラーが発生する場合があります。 このエラーは、所有者からはユーザーが見えないために発生します。この問題を解決するには、そのユーザーに対する ``VIEW DEFINITION`` 権限をロールの所有者に許可します。 

ログインとユーザーの詳細については、「[Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)」をご覧ください。

## セキュリティのベスト プラクティス

Azure SQL Database アプリケーションのセキュリティの脅威に対する脆弱性を低くするには、以下の点を考慮します。

- 常に最新の更新プログラムを使用します。SQL Database に接続するときには、セキュリティの脆弱性を防ぐために、常に最新バージョンのツールとライブラリを使用します。
- TCP ポート 1433 で受信接続をブロックします。アプリケーションが SQL Database と通信するために必要なのは、TCP ポート 1433 での発信接続のみです。そのコンピューターの他のアプリケーションも受信通信を必要としない場合は、ファイアウォールが TCP ポート 1433 の受信接続のブロックを続けることを確認してください。
- インジェクションの脆弱性を防ぎます。アプリケーションに SQL インジェクションの脆弱性がないようにするには、可能な限りパラメーター化クエリを使用します。また、コードを徹底的に見直し、アプリケーションをデプロイする前に侵入テストを実行します。


## 関連項目

[Azure SQL Database ファイアウォール](sql-database-firewall-configure.md)

[ファイアウォール設定の構成方法 (Azure SQL Database)](sql-database-configure-firewall-settings.md)

[Azure SQL Database におけるデータベースとログインの管理](sql-database-manage-logins.md)

[SQL Server データベース エンジンと Azure SQL Database のセキュリティ センター](https://msdn.microsoft.com/library/bb510589)

<!---HONumber=AcomDC_1125_2015-->