<properties
	pageTitle="SQL Database 監査の使用 | Microsoft Azure"
	description="SQL Database 監査の使用"
	services="sql-database"
	documentationCenter=""
	authors="ronitr"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/11/2016"
	ms.author="carlrabeler; ronitr"/>
 
# SQL Database 監査の使用
Azure SQL Database 監査は、データベース イベントを追跡し、監査したイベントを Azure ストレージ アカウントの監査ログに書き込みます監査は通常、Basic、Standard、と Premium の各サービス層で使用できます。

監査により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。

監査ツールは、標準準拠を強化し促進しますが、準拠を保証するものではありません。標準準拠をサポートする Azure プログラムの詳細については、[Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/compliance/)のページを参照してください。

+ [Azure SQL Database の監査の基本]
+ [データベースに対する監査を設定する]
+ [監査ログとレポートを分析する]

##<a id="subheading-1"></a>Azure SQL Database の監査の基本

以下のセクションでは、Azure ポータルを使用した監査の構成について説明します。また、[Azure クラシック ポータルを使用してデータベースの監査を設定する]こともできます。

SQL Database 監査により、以下のことが可能になります。

- 選択したイベントの監査証跡の**保持**。監査するデータベース活動のカテゴリを定義できます。
- データベース活動の**レポート**。事前に構成したレポートとダッシュボードを使用して、活動とイベントのレポートをすぐに使用できます。
- レポートの**分析**。疑わしいイベント、異常な活動、および傾向を発見できます。

> [AZURE.NOTE] プレビューが開始された新しい**脅威の検出**機能を使用すると、セキュリティ上の脅威になる可能性がある異常なデータベース アクティビティのプロアクティブ アラートを受信できるようになります。[脅威の検出] は [監査の構成] ブレードでオンにして構成することができます。詳細については、[脅威の検出の概要](sql-database-threat-detection-get-started.md)に関するページを参照してください。

次のイベント カテゴリの監査を構成できます。

収集された監査ログが次のように分類される**プレーンな SQL** および**パラメーター化された SQL**

- **データへのアクセス**
- **スキーマの変更 (DDL)**
- **データの変更 (DML)**
- **アカウント、ロール、アクセス許可 (DCL)**
- **ストアド プロシージャ**、**ログイン**、**トランザクション管理**。

各イベント カテゴリに対し、**成功**および**失敗**した操作の監査が個別に構成されます。

監査対象の活動やイベントの詳細については、[監査ログ形式のリファレンス (doc ファイルのダウンロード)](http://go.microsoft.com/fwlink/?LinkId=506733) を参照してください。

監査ログは、Azure ストレージ アカウントに格納されます。古いログを削除するまでの監査ログのリテンション期間の定義できます。

特定のデータベースに対して、または既定のサーバー ポリシーとして、監査ポリシーを定義できます。既定のサーバー監査ポリシーは、特定のオーバーライド データベース監査ポリシーが定義されていないサーバー上のすべてのデータベースに適用されます。

監査をセットアップする前に、["ダウンレベル クライアント"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) を使用しているかどうかを確認します。


##<a id="subheading-2"></a>データベースに対する監査を設定する

1. https://portal.azure.com で [Azure ポータル](https://portal.azure.com)を開きます。または、https://manage.windowsazure.com/ で [Azure クラシック ポータル](https://manage.windowsazure.com/)を起動することもできます。詳しい手順は次のようになります。

2. 監査する SQL Database または SQL Server の [設定] ブレードに移動します。[設定] ブレードで、**[監査と脅威の検出]** を選択します。

	![Navigation pane][1]

3. [監査の構成] ブレードで [監査] を [**オン**] にします。

4. **[容量の詳細]** を選択して [監査ログの容量] ブレードを開きます。ログを保存する Azure ストレージ アカウントを選択し、リテンション期間を選択します。**ヒント:** 監査レポートのテンプレートを最大限活用するには、すべての監査済みデータベースに同じストレージ アカウントを使用してください。

	![ナビゲーション ウィンドウ][2]

5. 監査するイベントをカスタマイズするには、[**監査されたイベント**] をクリックします。**[イベントによるログ]** ブレードで、すべてのイベントのログを記録するには、**[成功]** および **[失敗]** をクリックします。または、イベント カテゴリを個別に選択します。


6. このデータベースをサーバーの設定に従い監査する場合は、[**監査設定をサーバーから継承**] チェック ボックスをオンにします。このオプションをオンにすると、このコンテキストでサーバーの監査設定を表示または変更するリンクが表示されます。

	![Navigation pane][3]

7. 監査設定を構成したら [脅威の検出] を [**オン**] にして、セキュリティの警告を受信する電子メールを構成します。詳細については、「[脅威の検出の概要](sql-database-threat-detection-get-started.md)」ページを参照してください。

8. **[保存]** をクリックします。



##<a id="subheading-3"></a>監査ログとレポートを分析する

監査ログは、設定時に選択した Azure ストレージ アカウントで、**SQLDBAuditLogs** というプレフィックスを使用してストア テーブルのコレクションに集計されます。[Azure ストレージ エクスプローラー](http://azurestorageexplorer.codeplex.com/)などのツールを使用してログ ファイルを表示できます。

事前に構成されたレポート テンプレートが[ダウンロードできる Excel スプレッドシート](http://go.microsoft.com/fwlink/?LinkId=403540)として用意されているので、ログ データをすぐに分析できます。監査ログでテンプレートを使用するには、Excel 2013 またはそれ以降、および Power Query が必要です ([ここ](http://www.microsoft.com/download/details.aspx?id=39379)でダウンロードできます)。

Power Query を使用すると、Azure ストレージ アカウントから直接 Excel テンプレートに、監査ログをインポートできます。その後監査レコードを確認し、ログ データを使用しダッシュ ボードとレポートを作成できます。


![Navigation Pane][4]


##<a id="subheading-4"></a>Azure クラシック ポータルを使用してデータベースの監査を設定する

1. https://manage.windowsazure.com/ で [Azure クラシック ポータル](https://manage.windowsazure.com/)を開きます。

2. 監査する SQL Database/SQL Server をクリックしてから、[**監査とセキュリティ**] タブをクリックします。

3. [監査] を [**有効**] に設定します。

	![Navigation pane][5]

4. 必要に応じて監査するイベントをカスタマイズするために [**イベントごとのログ記録**] を編集します。

	![ナビゲーション ウィンドウ][6]

5. [**ストレージ アカウント**] を選択し、[**保持**] を設定します。

	![ナビゲーション ウィンドウ][7]

6. **[保存]** をクリックします。




##<a id="subheading-5">運用環境での使用手法</a>
このセクションでは、上のスクリーン キャプチャーについて説明します。[Azure ポータル](https://portal.azure.com)または [Azure クラシック ポータル](https://manage.windowsazure.com/)を使用できます。


##<a id="subheading-6"></a>ストレージ キーの再生成

運用環境では、ストレージ キーを最新の情報に定期的に更新することが推奨されます。キーを最新の情報に更新する場合は、監査ポリシーを再度保存する必要があります。このプロセスは次のとおりです。


1. [監査の構成] ブレードで、**[ストレージ アクセス キー]** を *[プライマリ]* から *[セカンダリ]* に切り替え、**[保存]** をクリックします。

	![][8]

2. ストレージ構成ブレードに移動し、*プライマリ アクセス キー*を**再生成**します。

3. [監査の構成] ブレードに戻り、**[ストレージ アクセス キー]** を *[セカンダリ]* から *[プライマリ]* に切り替え、**[保存]** をクリックします。

4. ストレージの UI に戻り、*セカンダリ アクセス キー*を**再生成** (次のキー更新サイクルの準備として) します。
  
##<a id="subheading-7"></a>Automation
Azure SQL Database で監査を構成する際、使用できる PowerShell コマンドレットがいくつかあります。

- [Get-AzureRMSqlDatabaseAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603731.aspx)
- [Get-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt619329.aspx)
- [Remove-AzureRMSqlDatabaseAuditing](https://msdn.microsoft.com/library/azure/mt603796.aspx)
- [Remove-AzureRMSqlServerAuditing](https://msdn.microsoft.com/library/azure/mt603574.aspx)
- [Set-AzureRMSqlDatabaseAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603531.aspx)
- [Set-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt603794.aspx)
- [Use-AzureRMSqlServerAuditingPolicy](https://msdn.microsoft.com/library/azure/mt619353.aspx)




<!--Anchors-->
[Azure SQL Database の監査の基本]: #subheading-1
[データベースに対する監査を設定する]: #subheading-2
[監査ログとレポートを分析する]: #subheading-3
[Azure クラシック ポータルを使用してデータベースの監査を設定する]: #subheading-4
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation]: #subheading-7


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_storage_account.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_inherit_from_server.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_report_template.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_classic_portal_enable.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_classic_portal_events.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_classic_portal_storage.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_storage_key_rotation.png


 

<!---HONumber=AcomDC_0413_2016-->