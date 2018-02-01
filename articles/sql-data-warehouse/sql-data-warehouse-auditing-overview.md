---
title: "Azure SQL Data Warehouse での監査 | Microsoft Docs"
description: "Azure SQL Data Warehouse での監査の概要"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: 0e6af148-b218-4b43-bb5f-907917d20330
ms.service: sql-data-warehouse
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: security
ms.date: 01/16/2018
ms.author: rortloff;barbkess
ms.openlocfilehash: 5400f29d8c7579809ef7b2a084115473df7baa85
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2018
---
# <a name="auditing-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse での監査

SQL Data Warehouse の監査により、Azure Storage アカウントの監査ログにデータベースのイベントを記録できます。 監査により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。 また SQL Data Warehouse の監査を Microsoft Power BI と統合することにより、レポートと分析が容易になります。

監査ツールは、標準準拠を強化し促進しますが、準拠を保証するものではありません。 標準準拠をサポートする Azure プログラムの詳細については、 <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Azure セキュリティ センター</a>のページを参照してください。

## <a id="subheading-1"></a>監査の基本
SQL Data Warehouse データベース監査により、以下のことが可能になります。

* **保持** 。 監査するデータベース活動のカテゴリを定義できます。
* **レポート** 。 事前に構成したレポートとダッシュボードを使用して、活動とイベントのレポートをすぐに使用できます。
* **分析** 。 疑わしいイベント、異常な活動、および傾向を発見できます。

次のイベント カテゴリの監査を構成できます。

収集された監査ログが次のように分類される**プレーンな SQL** および**パラメーター化された SQL**  

* **データへのアクセス**
* **スキーマの変更 (DDL)**
* **データの変更 (DML)**
* **アカウント、ロール、アクセス許可 (DCL)**
* **ストアド プロシージャ**、**ログイン**、**トランザクション管理**。

各イベント カテゴリに対し、**成功**および**失敗**した操作の監査が個別に構成されます。

監査対象の活動やイベントの詳細については、<a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">監査ログ形式のリファレンス (doc ファイルのダウンロード)</a> を参照してください。

監査ログは、Azure ストレージ アカウントに格納されます。 監査ログの保有期間を定義することができます。

特定のデータベースに対して、または既定のサーバー ポリシーとして、監査ポリシーを定義できます。 既定のサーバー監査ポリシーは、特定のオーバーライド データベース監査ポリシーが定義されていないサーバー上のすべてのデータベースに適用されます。

監査をセットアップする前に、 ["ダウンレベル クライアント"](sql-data-warehouse-auditing-downlevel-clients.md)を使用しているかどうかを確認します。

## <a id="subheading-2"></a>データベースに対する監査を設定する
1. <a href="https://portal.azure.com" target="_blank">Azure ポータル</a>を開きます。
2. 監査する SQL Data Warehouse の **[設定]** に移動します。 **[監査と脅威検出]** を選びます。
   
    ![][1]
3. 次に、 **[オン]** ボタンをクリックして監査を有効にします。
   
    ![][3]
4. 監査構成パネルで、**[容量の詳細]** を選んで [監査ログの容量] パネルを開きます。 ログの Azure ストレージ アカウントと、リテンション期間を選びます。 
>[!TIP]
>すべての監査済みデータベースに同じストレージ アカウントを使用して、事前に構成したレポートのテンプレートを活用します。
   
    ![][4]
5. **[OK]** ボタンをクリックして、記憶域の詳細構成を保存します。
6. **[イベントごとのログ記録]** で、**[成功]** および **[失敗]** を、すべてのイベントについて、または個別のイベント カテゴリについて記録します。
7. データベースの監査を構成している場合は、データの監査が正しくキャプチャされるように、クライアントの接続文字列を変更する必要がある場合があります。 ダウンレベル クライアントの接続については、 [接続文字列でのサーバーの FDQN の変更](sql-data-warehouse-auditing-downlevel-clients.md) に関するトピックを参照してください。
8. Click **OK**.

## <a id="subheading-3"></a>監査ログとレポートを分析する
監査ログは、設定時に選択した Azure ストレージ アカウントで、 **SQLDBAuditLogs** というプレフィックスを使用してストア テーブルのコレクションに集計されます。 <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure ストレージ エクスプローラー</a>などのツールを使用してログ ファイルを表示できます。

事前に構成されたダッシュボード レポート テンプレートが<a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">ダウンロードできる Excel スプレッドシート</a>として用意されているので、ログ データをすぐに分析できます。 監査ログでテンプレートを使用するには、Excel 2013 またはそれ以降、および Power Query が必要です (<a href="http://www.microsoft.com/download/details.aspx?id=39379">ここ</a>でダウンロードできます)。

テンプレートでは架空のサンプル データを使用しています。Power Query を設定して Azure ストレージ アカウントから監査ログを直接インポートできます。

## <a id="subheading-4"></a>ストレージ キーの再生成
運用環境では、ストレージ キーを最新の情報に定期的に更新することが推奨されます。 鍵を最新の情報に更新する場合は、ポリシーを保存する必要があります。 このプロセスは次のとおりです。

1. 監査構成パネル (前の監査設定セクションで説明されています) で、**[ストレージ アクセス キー]** を *[プライマリ]* から *[セカンダリ]* に変更して、**[保存]** をクリックします。

   ![][4]
2. ストレージ構成パネルに移動し、"*プライマリ アクセス キー*" を**再生成**します。
3. 監査構成パネルに戻り、 
4. **[ストレージ アクセス キー]** を *[セカンダリ]* から *[プライマリ]* に切り替えて、**[保存]** をクリックします。
4. ストレージの UI に戻り、 **セカンダリ アクセス キー** を *再生成* (次のキー更新サイクルの準備として) します。

## <a id="subheading-5"></a>Automation (PowerShell / REST API)
次の自動化ツールを利用し、Azure SQL Data Warehouse で監査を構成することもできます。

* **PowerShell コマンドレット**:

   * [Get-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/get-azurermsqldatabaseauditingpolicy)
   * [Get-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Get-AzureRMSqlServerAuditingPolicy)
   * [Remove-AzureRMSqlDatabaseAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlDatabaseAuditing)
   * [Remove-AzureRMSqlServerAuditing](/powershell/module/azurerm.sql/Remove-AzureRMSqlServerAuditing)
   * [Set-AzureRMSqlDatabaseAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlDatabaseAuditingPolicy)
   * [Set-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Set-AzureRMSqlServerAuditingPolicy)
   * [Use-AzureRMSqlServerAuditingPolicy](/powershell/module/azurerm.sql/Use-AzureRMSqlServerAuditingPolicy)


## <a name="downlevel-clients-support-for-auditing-and-dynamic-data-masking"></a>監査と動的データ マスキングのためのダウンレベル クライアントのサポート
監査は TDS リダイレクションに対応する SQL クライアントと連動します。

TDS 7.4 を実装するクライアントもリダイレクトをサポートします。 この例外には一部のリダイレクション機能に対応していない JDBC 4.0 とリダイレクションが実装されていない Tedious for Node.JS があります。

TDS バージョン 7.3 以前をサポートする "ダウンレベル クライアント" の場合、次のようにして接続文字列のサーバー FQDN を変更します。

- 接続文字列の元のサーバー FQDN: <*server name*>.database.windows.net
- 接続文字列の変更後のサーバー FQDN: <*server name*>.database.**secure**.windows.net

「ダウンレベル クライアント」には次が含まれます。

* .NET 4.0 以前
* ODBC 10.0 以前
* JDBC (JDBC は TDS 7.4 対応ですが、一部の TDS リダイレクション機能に対応していません。)
* Tedious (Node.JS 用)

**注釈:** 上のサーバー FDQN 変更は SQL サーバー レベル監査ポリシーの適用にも役に立ちます。データベースごとの構成が必要ありません (一時的な軽減)。     




<!--Anchors-->
[Database Auditing basics]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3


<!--Image references-->
[1]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing.png
[2]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-inherit.png
[3]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-enable.png
[4]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-storage-account.png
[5]: ./media/sql-data-warehouse-auditing-overview/sql-data-warehouse-auditing-dashboard.png


