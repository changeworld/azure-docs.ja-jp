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
	ms.date="02/23/2015" 
	ms.author="jeffreyg"/>
 
#SQL Database 監査の使用 
<p> Azure SQL Database 監査は、データベース イベントを追跡し、監査したイベントを Azure Storage アカウントの監査ログに書き込みます監査は通常、Basic、Standard、と Premium の各サービス層で使用できます。

監査により、規定遵守の維持、データベース活動の理解、ビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常の兆候を容易に検知できるようになります。 

監査ツールは、標準準拠を強化し促進しますが、準拠を保証するものではありません。標準準拠をサポートする Azure プログラムの詳細については、 <a href="http://azure.microsoft.com/support/trust-center/compliance/" target="_blank">Azure のトラスト センター</a>をご覧ください。

+ [Azure SQL Database の監査の基本] 
+ [データベースに対する監査を設定する]
+ [監査ログとレポートを分析する]

##<a id="subheading-1"></a>Azure SQL Database の監査の基本

Azure プレビュー ポータルで監査を設定しますが、データベースの作成に Azure ポータルを使用した場合でも Azure プレビュー ポータルを使用した場合でも違いはありません。SQL Database 監査により、以下のことが可能になります。

- 選択したイベントの監査証跡の**保持**。ログに記録するデータベース活動とイベントのカテゴリを定義します。
- データベース活動の**レポート**。事前に構成したレポートとダッシュボードを使用して、活動とイベントのレポートをすぐに使用できます。
- レポートの**分析**。疑わしいイベント、異常な活動、傾向を見つけます。

次の活動とイベントを監査できます。

- **データへのアクセス**
- **スキーマの変更 (DDL)**
- **データの変更 (DML)**
- **アカウント、ロール、アクセス許可 (DCL)**
- **セキュリティ例外**

活動と記録するログの詳細については、 <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">監査ログ形式のリファレンス「Azure SQL Database Data Security Audit Logs format (Azure SQL Database データ セキュリティ監査ログの形式)」 (doc ファイルでダウンロード) をご覧ください</a>。 

監査ログが保存されるストレージ アカウントも選択します。

###セキュリティが有効な接続文字列
監査を設定すると、Azure はデータベースに対しセキュリティが有効な接続文字列を提供します。この接続文字列を使用するクライアント アプリケーションだけが、活動とイベントをログに記録できます。そのため、既存のクライアント アプリケーションが新しい文字列形式を使用するように更新する必要があります。

一般的な接続文字列の形式:<*サーバー名*>.database.windows.net

セキュリティが有効な接続文字列の形式:<*サーバー名*>.database.**secure**.windows.net


##<a id="subheading-2"></a>データベースに対する監査を設定する

1. https://portal.azure.com から <a href="https://portal.azure.com" target="_blank">Azure プレビュー ポータル</a> を開始します。または、 <a href= "https://manage.windowsazure.com/" target="_bank">従来の Azure のポータル</a> https://manage.windowsazure.com/ からも開始できます。詳しい手順は次のようになります。
2. 監査するデータベースの構成ブレードに移動します。**[操作]** のセクションまで画面を下方向にスクロールし、**[監査]** をクリックします。これで監査が有効になり、監査構成ブレードが起動します。

	![][1]

3. 監査構成ブレードで、ログが保存される Azure ストレージ アカウントを選択します。**ヒント:**すべての監査済みデータベースに同じストレージ アカウントを使用して、事前に構成したレポートのテンプレートを活用します。

	![][2]

4. **[監査オプション]** で、**[すべて]** をクリックしてすべてのイベントをログに記録するか、または個別のイベントの種類を選択します。

	![][3]

5. これらの設定を、今後のサーバーのすべてのデータベース、まだ監査を設定していないデータベースに適用する場合は、**[この設定をサーバーの既定値として保存する]** を選択します。同じ手順によって、後でデータベースごとにこの設定をオーバーライドできます。 

6. **[データベース接続文字列を表示する]** をクリックしてから、アプリケーションに対して該当するセキュリティが有効な接続文字列をコピーするか、メモします。活動を監査するすべてのクライアント アプリケーションに対して、この文字列を使用します。

	![][5]

7. **[OK]** をクリックします。



##<a id="subheading-3">監査ログとレポートを分析する</a>

監査ログは、設定時に選択した Azure ストレージ アカウントで **AuditLogs** という名前の単一の Azure ストア テーブルに集計されます。ログ ファイルは、 <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure Storage Explorer</a>などのツールを使用して表示できます。

事前に構成されたダッシュボード レポート テンプレートが <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">ダウンロードできる Excel スプレッドシートとして用意されているので、</a> ログ データをすぐに分析できます。監査ログでテンプレートを使用するには、Excel 2013 またはそれ以降、Power Query が必要です ( <a href="http://www.microsoft.com/download/details.aspx?id=39379">ここ</a>をクリックしてください。 

テンプレートでは架空のサンプル データを使用しています。Power Query を設定して Azure ストレージ アカウントから監査ログを直接インポートできます。 

レポート テンプレートの使用手順の詳細については、 <a href="http://go.microsoft.com/fwlink/?LinkId=506731">「Azure SQL DB - Auditing How to use the interactive Reports Excel Workbook Template (Azure SQL DB - 監査、対話式レポート Excel ワークブック テンプレートの使用方法)」(doc 形式でダウンロード) をご覧ください</a>。

![][6]


##<a id="subheading-4"></a>従来の Azure ポータルを使用してデータベースの監査を設定する

1. https://manage.windowsazure.com/ から <a href= "https://manage.windowsazure.com/" target="_bank">従来の Azure のポータル</a> https://manage.windowsazure.com/ からも開始できます。 
2. 監査するデータベースをクリックしてから **[監査とセキュリティのプレビュー]** タブをクリックします。
3. 監査のセクションで、[有効にする] をクリックします。

![][7]

4. 必要に応じて **[イベントの種類]** を編集します。

![][8]

5. **[ストレージ アカウント]** を選択します。
6. **[保存]** をクリックします。
7. 接続文字列に対し、**[セキュリティで保護された接続文字列を表示します]** をクリックします。


##<a id="subheading-3">運用環境での使用手法</a>
このセクションでは、上のスクリーン キャプチャーについて説明します。使用できるのは <a href="https://portal.azure.com" target="_blank">Azure プレビュー ポータル</a> または <a href= "https://manage.windowsazure.com/" target="_bank">従来の Azure のポータル</a> です。
 

##<a id="subheading-4"></a>セキュリティが有効なアクセス

運用環境では、通常、すべてのアプリケーションやツールのすべてのトラフィックを監査する必要があることがほとんどです。このため、**[セキュリティが有効なアクセス]** を [ *Optional*] から [ *Required*] に変更してポリシーを保存します。いったん [ *Required*] と構成すると、元の接続文字列から Database にアクセスするオプションはなくなりますが、セキュリティが有効な接続文字列からのみ接続できます。


![][9]


##<a id="subheading-4"></a>ストレージ キーの再生成

運用環境では、ストレージ キーを最新の情報に定期的に更新することが推奨されます。監査サービスでは、ストレージ アカウント キーを保持しません。保存時、書き込み専用共有アクセス署名 (SAS) キーが監査テーブルに対して生成されます (顧客のみこの監査ログの読み取りができます)。このため、鍵を最新の情報に更新する場合は、ポリシーを再度保存する必要があります。このプロセスは次のとおりです。


1. 監査構成ブレードで (監査の設定に関する前のセクションの説明をご覧ください)、**[ストレージ アクセス キー]** を、[ *Primary*] から [ *Secondary*] に切り替え、**[保存]** をクリックします。
![][10]
2. ストレージ構成ブレードに移動し、[ *Primary Access Key*] を**再生成**します。

3. 監査構成ブレードに戻り、**[ストレージ アクセス キー]** を [ *Secondary*] から [ *Primary*] に切り替え、**[保存]** をクリックします。

4. ストレージの UI に戻り、[ *Secondary Access Key*] を**再生成** (次のキー更新サイクルの準備として) します。
  
##<a id="subheading-4"></a>Automation
Azure SQL データベースで監査を構成する際、使用できる PowerShell コマンドレットがいくつかあります。auditing コマンドレットにアクセスにするには、Azure リソース マネージャー モードで PowerShell を実行する必要があります。

> [AZURE.NOTE] AzureResourceManager モジュールは、現在プレビュー段階にあります。Azure モジュールと同じ管理機能を提供しないことがあります。

 [Azure リソース マネージャー](https://msdn.microsoft.com/library/dn654592.aspx) モードは、Switch-AzureMode コマンドレット (`Switch-AzureMode AzureResourceManager`) を実行してアクセスします。Azure リソース マネージャー モードを実行中に、`Get-Command *AzureSql*` を実行して、使用できるコマンドレットの一覧を表示します。







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






<!--Link references-->
[別の azure.microsoft.com ドキュメントのトピックへのリンク 1]: ../virtual-machines-windows-tutorial/
[別の azure.microsoft.com ドキュメントのトピックへのリンク 2]: ../web-sites-custom-domain-name/
[別の azure.microsoft.com ドキュメントのトピックへのリンク 3]: ../storage-whatis-account/


<!--HONumber=47-->
