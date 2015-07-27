<properties 
	pageTitle="SQL Database 監査の使用 | Azure" 
	description="SQL Database 監査の使用" 
	services="sql-database" 
	documentationCenter="" 
	authors="jeffgoll" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="jeffreyg"/>
 
# SQL Database 監査の使用 
<p>Azure SQL Database 監査は、データベース イベントを追跡し、監査したイベントを Azure Storage アカウントの監査ログに書き込みます監査は通常、Basic、Standard、と Premium の各サービス層で使用できます。

監査により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。

監査ツールは、標準準拠を強化し促進しますが、準拠を保証するものではありません。標準準拠をサポートする Azure プログラムの詳細については、「<a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Microsoft Azure のトラスト センター</a>」を参照してください。

+ [Azure SQL Database の監査の基本] 
+ [データベースに対する監査を設定する]
+ [監査ログとレポートを分析する]

##<a id="subheading-1"></a>Azure SQL Database の監査の基本

以下のセクションでは、Azure プレビュー ポータルを使用した監査の構成について説明します。また、[従来の Azure ポータルを使用してデータベースの監査を設定する]こともできます。

SQL Database 監査により、以下のことが可能になります。

- 選択したイベントの監査証跡の**保持**。監査するデータベース活動のカテゴリを定義できます。
- データベース活動の**レポート**。事前に構成したレポートとダッシュボードを使用して、活動とイベントのレポートをすぐに使用できます。
- レポートの**分析**。疑わしいイベント、異常な活動、および傾向を発見できます。

次のイベント カテゴリの監査を構成できます。

収集された監査ログが次のように分類される**プレーンな SQL** および**パラメーター化された SQL**

- **データへのアクセス**
- **スキーマの変更 (DDL)**
- **データの変更 (DML)**
- **アカウント、ロール、アクセス許可 (DCL)**

**ストアド プロシージャ**、**ログイン**、**トランザクション管理**。

各イベント カテゴリに対し、**成功**および**失敗**した操作の監査が個別に構成されます。

監査対象の活動やイベントの詳細については、<a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">監査ログ形式のリファレンス (doc ファイルのダウンロード)</a> を参照してください。

監査ログは、Azure ストレージ アカウントに格納されます。監査ログの保有期間を定義することができます。

特定のデータベースに対して、または既定のサーバー ポリシーとして、監査ポリシーを定義できます。既定のサーバー監査ポリシーは、特定のオーバーライド データベース監査ポリシーが定義されていないサーバー上のすべてのデータベースに適用されます。

監査をセットアップする前に、["ダウンレベル クライアント"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) を使用しているかどうかを確認します。


##<a id="subheading-2"></a>データベースに対する監査を設定する

1. https://portal.azure.com で <a href="https://portal.azure.com" target="_blank">Azure プレビュー ポータル</a>を起動します。または、https://manage.windowsazure.com/ で<a href= "https://manage.windowsazure.com/" target="_bank">従来の Azure ポータル</a>を起動することもできます。詳しい手順は次のようになります。

2. 監査する SQL Database/SQL Server の構成ブレードに移動します。上部の [**設定**] ボタンをクリックし、[設定] ブレードで [**監査**] を選択します。

	![][1]

3. 監査構成ブレードで、[ストレージの詳細] を選択して [監査ログ ストレージ] ブレードを選択します。ログを保存する Azure ストレージ アカウントを選択し、保持期間を選択します。**ヒント:** すべての監査済みデータベースに同じストレージ アカウントを使用して、事前に構成したレポートのテンプレートを活用します。

	![][2]

4. [**イベントごとのログ記録**] で、[**成功**] および [**失敗**] を、すべてのイベントについて、または個別のイベント カテゴリについて記録します。


5. SQL Database の監査を構成している場合は、[**監査を適用するには、ここをクリックして ...**] をクリックし、[**セキュリティが有効なアクセス**] で [**必須**] を選択します。SQL Server の監査を構成している場合は、2 つのオプションがあります。(a) 手順 #6 の後、サーバーの各 SQL Database に移動してこの手順を適用します。または、(2) [接続文字列でサーバーの FDQN を変更します](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)。


	![][5]

6. **[OK]** をクリックします。



##<a id="subheading-3">監査ログとレポートを分析する</a>

監査ログは、設定時に選択した Azure ストレージ アカウントで、**SQLDBAuditLogs** というプレフィックスを使用してストア テーブルのコレクションに集計されます。<a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure ストレージ エクスプローラー</a>などのツールを使用してログ ファイルを表示できます。

事前に構成されたダッシュボード レポート テンプレートが<a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">ダウンロードできる Excel スプレッドシート</a>として用意されているので、ログ データをすぐに分析できます。監査ログでテンプレートを使用するには、Excel 2013 またはそれ以降、および Power Query が必要です (<a href="http://www.microsoft.com/download/details.aspx?id=39379">ここ</a>でダウンロードできます)。

テンプレートでは架空のサンプル データを使用しています。Power Query を設定して Azure ストレージ アカウントから監査ログを直接インポートできます。

レポート テンプレートの使用手順の詳細については、<a href="http://go.microsoft.com/fwlink/?LinkId=506731">方法 (doc のダウンロード)</a> を参照してください。

![][6]


##<a id="subheading-4"></a>従来の Azure ポータルを使用してデータベースの監査を設定する

1. https://manage.windowsazure.com/ で<a href= "https://manage.windowsazure.com/" target="_bank">従来の Azure ポータル</a>を起動します。
 
2.   監査する SQL Database/SQL Server をクリックしてから、[**監査とセキュリティ**] タブをクリックします。

3.   SQL Database の監査を構成している場合は、[**セキュリティが有効なアクセス**] で [**必須**] を選択します。SQL Server の監査を構成している場合は、2 つのオプションがあります。(a) 手順 #7 の後、サーバーの各 SQL Database に移動してこの手順を適用します。または、(2) [接続文字列でサーバーの FDQN を変更します](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)。

4. 監査のセクションで、[**有効にする**] をクリックします。


	![][7]

5. 必要に応じて、[**イベントごとのログ記録**] を編集します。

	![][8]

6. [**ストレージ アカウント**] を選択し、[**保持**] を設定します。

	![][11]

7. **[保存]** をクリックします。




##<a id="subheading-3">運用環境での使用手法</a>
このセクションでは、上のスクリーン キャプチャーについて説明します。<a href="https://portal.azure.com" target="_blank">Azure プレビュー ポータル</a>または<a href= "https://manage.windowsazure.com/" target="_bank">従来の Azure ポータル</a>を使用できます。
 

##<a id="subheading-4"></a>ストレージ キーの再生成

運用環境では、ストレージ キーを最新の情報に定期的に更新することが推奨されます。鍵を最新の情報に更新する場合は、ポリシーを再度保存する必要があります。このプロセスは次のとおりです。


1. 監査構成ブレードで (監査を設定する前セクションの説明を参照してください)、[**ストレージ アクセス キー**] を、[*プライマリ*] から [*セカンダリ*] に切り替え、[**保存**] をクリックします。![][10]
2. ストレージ構成ブレードに移動し、*プライマリ アクセス キー*を**再生成**します。

3. 監査構成ブレードに戻り、[**ストレージ アクセス キー**] を [*セカンダリ*] から [*プライマリ*] に切り替え、[**保存**] をクリックします。

4. ストレージの UI に戻り、*セカンダリ アクセス キー*を**再生成** (次のキー更新サイクルの準備として) します。
  
##<a id="subheading-4"></a>自動化
Azure SQL データベースで監査を構成する際、使用できる PowerShell コマンドレットがいくつかあります。auditing コマンドレットにアクセスにするには、Azure リソース マネージャー モードで PowerShell を実行する必要があります。

> [AZURE.NOTE][Azure リソース マネージャー](https://msdn.microsoft.com/library/dn654592.aspx) モジュールは、現在プレビュー段階にあります。Azure モジュールと同じ管理機能を提供しないことがあります。

Azure リソース マネージャー モードを実行中に、`Get-Command *AzureSql*` を実行して、使用できるコマンドレットの一覧を表示します。







<!--Anchors-->
[Azure SQL Database の監査の基本]: #subheading-1
[データベースに対する監査を設定する]: #subheading-2
[監査ログとレポートを分析する]: #subheading-3
[従来の Azure ポータルを使用してデータベースの監査を設定する]: #subheading-4


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/sql-database-get-started-auditingpreview.png
[2]: ./media/sql-database-auditing-get-started/sql-database-get-started-storageaccount.png
[3]: ./media/sql-database-auditing-get-started/sql-database-auditing-eventtype.png
[5]: ./media/sql-database-auditing-get-started/sql-database-get-started-connectionstring.png
[6]: ./media/sql-database-auditing-get-started/sql-database-auditing-dashboard.png
[7]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-enable.png
[8]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-configure.png
[9]: ./media/sql-database-auditing-get-started/sql-database-auditing-security-enabled-access.png
[10]: ./media/sql-database-auditing-get-started/sql-database-auditing-storage-account.png
[11]: ./media/sql-database-auditing-get-started/sql-database-auditing-classic-portal-configure-storage.png






<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md

 

<!---HONumber=July15_HO3-->