<properties title="Get started with SQL database auditing" pageTitle="SQL Database 監査の使用 | Azure" description="SQL Database 監査の使用" metaKeywords="" services="sql-database" solutions="data-management" documentationCenter="" authors="jeffreyg" videoId="" scriptId="" manager="jeffreyg" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/31/2015" ms.author="jeffreyg" />
 
# SQL Database 監査の使用 
<p> Azure SQL Database 監査は、データベース イベントを追跡し、監査したイベントを Azure Storage アカウントの監査ログに書き込みます監査は、Basic、Standard、および Premium の各サービス層でプレビューとして使用できます。

監査により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違反の疑いを示す差異や異常に関する洞察を得ることができます。 

監査ツールは、標準準拠を強化し促進しますが、準拠を保証するものではありません。標準準拠をサポートする Azure プログラムの詳細については、 <a href="http://azure.microsoft.com/ja-jp/support/trust-center/compliance/" target="_blank">Azure のトラスト センター</a>を参照してください。

+ [Azure SQL Database の監査の基本] 
+ [データベースに対する監査を設定する]
+ [監査ログとレポートを分析する]

##<a id="subheading-1">Azure SQL Database の監査の基本</a>

Azure プレビュー ポータルで監査を設定しますが、データベースの作成に Azure ポータルを使用した場合でも Azure プレビュー ポータルを使用した場合でも違いはありません。SQL Database 監査により、以下のことが可能になります。

- 選択したイベントの監査証跡の**保持**。ログに記録するデータベース活動とイベントのカテゴリを定義します。
- データベース活動の**レポート**。事前に構成したレポートとダッシュボードを使用して、活動とイベントのレポートをすぐに使用できます。
- レポートの**分析**。疑わしいイベント、異常な活動、および傾向を見つけます。

次の活動とイベントを監査できます。

- **データへのアクセス**
- **スキーマの変更 (DDL)**
- **データの変更 (DML)**
- **アカウント、ロール、アクセス許可 (DCL)**
- **セキュリティ例外**

活動と記録するログの詳細については、 <a href="http://go.microsoft.com/fwlink/?LinkId=506733" target="_blank">監査ログ形式のリファレンス「Azure SQL Database Data Security Audit Logs format (Azure SQL Database データ セキュリティ監査ログの形式)」 (doc ファイルでダウンロード) を参照してください</a>。 

監査ログが保存されるストレージ アカウントも選択します。

###セキュリティが有効な接続文字列
監査を設定すると、Azure はデータベースに対しセキュリティが有効な接続文字列を提供します。この接続文字列を使用するクライアント アプリケーションだけが、活動とイベントをログに記録できます。そのため、既存のクライアント アプリケーションが新しい文字列形式を使用するように更新する必要があります。

一般的な接続文字列の形式: <*サーバー名*>.database.windows.net

セキュリティが有効な接続文字列の形式: <*サーバー名>.database.**secure**.windows.net


##<a id="subheading-2"></a>データベースに対する監査を設定する

1. https://portal.azure.com から  <a href="https://portal.azure.com" target="_blank">Azure プレビュー ポータル</a> を開始します。または、 <a href= "https://manage.windowsazure.com/" target="_bank">従来の Azure のポータル </a> https://manage.windowsazure.com/ からも開始できます。詳しい手順は次のようになります。
2. 監査するデータベースの構成ブレードに移動します。**[操作]** のセクションまで画面を下方向にスクロールし、**[監査]** をクリックします。これで監査が有効になり、監査構成ブレードが起動します。

	![][1]

3. 監査構成ブレードで、ログが保存される Azure ストレージ アカウントを選択します。**ヒント:**すべての監査済みデータベースに同じストレージ アカウントを使用して、事前に構成したレポートのテンプレートを活用します。

	![][2]

4. **[監査オプション]** で、**[すべて]** をクリックしてすべてのイベントをログに記録するか、または個別のイベントの種類を選択します。

	![][3]

5. これらの設定を、今後のサーバーのすべてのデータベース、およびまだ監査を設定していないデータベースに適用する場合は、**[この設定をサーバーの既定値として保存する]** をクリックします。同じ手順によって、後でデータベースごとにこの設定をオーバーライドできます。 

6. **[データベース接続文字列を表示する]** をクリックしてから、アプリケーションに対し、該当するセキュリティが有効な接続文字列をコピーするかメモします。活動を監査するすべてのクライアント アプリケーションに対して、この文字列を使用します。

	![][5]

7. **[OK]** をクリックします。



##<a id="subheading-3">監査ログとレポートを分析する</a>

監査ログは、設定時に選択した Azure ストレージ アカウントで **AuditLogs** という名前の単一の Azure ストア テーブルに集計されます。ログ ファイルは、 <a href="http://azurestorageexplorer.codeplex.com/" target="_blank">Azure Storage Explorer</a>などのツールを使用して表示できます。

事前に構成されたダッシュボード レポート テンプレートが <a href="http://go.microsoft.com/fwlink/?LinkId=403540" target="_blank">ダウンロードできる Excel スプレッドシートとして用意されているので、</a> ログ データをすぐに分析できます。監査ログでテンプレートを使用するには、Excel 2013 またはそれ以降、および Power Query が必要です ( <a href="http://www.microsoft.com/ja-jp/download/details.aspx?id=39379">ここ</a>でダウンロードできます)。 

テンプレートでは架空のサンプル データを使用しています。Power Query を設定して Azure ストレージ アカウントから監査ログを直接インポートできます。 

レポート テンプレートの使用手順の詳細については、 <a href="http://go.microsoft.com/fwlink/?LinkId=506731">「Azure SQL DB - Auditing How to use the interactive Reports Excel Workbook Template (Azure SQL DB - 監査、対話式レポート Excel ワークブック テンプレートの使用方法)」(doc 形式でダウンロード) を参照してください</a>。

![][6]


##<a id="subheading-4"></a>従来の Azure ポータルを使用してデータベースの監査を設定する

1. https://manage.windowsazure.com/ から <a href= "https://manage.windowsazure.com/" target="_bank">従来の Azure のポータル</a> を開始します。 
2. 監査するデータベースをクリックしてから **[監査およびセキュリティのプレビュー]** タブをクリックします。
3. 監査のセクションで、[有効にする] をクリックします。

![][7]

4. 必要に応じて **[イベントの種類]** を編集します。

![][8]

5. **[ストレージ アカウント]** を選択します。
6. **[保存]** をクリックします。
7. 接続文字列に対し、**[セキュリティで保護された接続文字列を表示します]** をクリックします。


##<a id="subheading-3">運用環境での使用手法</a>
このセクションでは、上のスクリーン キャプチャーについて説明します。使用できるのは  <a href="https://portal.azure.com" target="_blank">Azure プレビュー ポータル</a> または <a href= "https://manage.windowsazure.com/" target="_bank">従来の Azure のポータル</a> です。
 

##<a id="subheading-4"></a>セキュリティが有効なアクセス

運用環境では、通常、すべてのアプリケーションやツールのすべてのトラフィックを監査する必要があることがほとんどです。このため、**セキュリティが有効なアクセス**を *[オプション]* から *[必須]* に変更してポリシーを保存します。いったん *[必須]* と構成すると、元の接続文字列から Database にアクセスするオプションはなくなりますが、セキュリティが有効な接続文字列からのみ接続できます。


![][9]


##<a id="subheading-4"></a>ストレージ キーの再生成

運用環境では、ストレージ キーを最新の情報に定期的に更新することが推奨されます。監査サービスでは、ストレージ アカウント キーを保持しません。保存時、書き込み専用共有アクセス署名 (SAS) キーが監査テーブルに対して生成されます (顧客のみこの監査ログの読み取りができます)。このため、鍵を最新の情報に更新する場合は、ポリシーを再度保存する必要があります。このプロセスは次のとおりです。


1. 監査構成ブレードで (監査を設定する前セクションの説明を参照してください)、**[ストレージ アクセス キー]** を、*[プライマリ]* から *[セカンダリ]* に切り替え、**[保存]** をクリックします。
![][10]
2. ストレージ構成ブレードに移動し、*プライマリ アクセス キー*を**再生成**します。

3. 監査構成ブレードに戻り、**[ストレージ アクセス キー]** を *[セカンダリ]* から *[プライマリ]* に切り替え、**[保存]** をクリックします。

4. ストレージの UI に戻り、*セカンダリ アクセス キー*を**再生成** (次のキー更新サイクルの準備として) します。
  
##<a id="subheading-4"></a>Automation
PowerShell の場合は、 <a href="https://github.com/Azure/azure-powershell" target="_blank">PowerShell SDK を参照してください</a>。

REST API については、 <a href="http://download.microsoft.com/download/D/8/D/D8D90BA1-977F-466B-A839-7823FF37FD02/04-Azure SQL DB Auditing REST API.docx">Azure SQL Database セキュリティの REST API に関するページを参照してください。</a>





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


<!--HONumber=35_1-->
