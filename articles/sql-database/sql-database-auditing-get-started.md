---
title: "SQL Database 監査の使用 | Microsoft Docs"
description: "SQL Database 監査の使用"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: 
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: CarlRabeler; ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 7b3c6d29ad7ded2ea65f378a1f807235c688d738
ms.openlocfilehash: 9f51fcdb291021064dc02a71afdf849f1f4a3d85


---
# <a name="get-started-with-sql-database--auditing"></a>SQL Database 監査の使用
Azure SQL Database の監査では、データベース イベントを追跡し、Azure Storage アカウントの監査ログにイベントを書き込みます。

監査により、規定遵守の維持、データベース活動の理解、およびビジネス上の懸念やセキュリティ違犯の疑いを示す差異や異常に対する洞察が容易になります。

監査は、標準準拠を強化し促進しますが、準拠を保証するものではありません。 標準準拠をサポートする Azure プログラムの詳細については、 [Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/compliance/)のページを参照してください。

* [Azure SQL Database の監査の概要]
* [データベースに対する監査を設定する]
* [監査ログとレポートを分析する]

## <a name="a-idsubheading-1aazure-sql-database-auditing-overview"></a><a id="subheading-1"></a>Azure SQL Database の監査の概要
SQL Database 監査により、以下のことが可能になります。

* **保持** 。 監査するデータベース活動のカテゴリを定義できます。
* **レポート** 。 事前に構成したレポートとダッシュボードを使用して、活動とイベントのレポートをすぐに使用できます。
* **分析** 。 疑わしいイベント、異常な活動、および傾向を発見できます。

**監査方法**は 2 つあります。

* **BLOB の監査** - Azure Blob ストレージにログが書き込まれます。 これは新しいほうの監査方法です。**パフォーマンスが高く**、**細かなオブジェクト レベルの監査が可能**であり、**対費用効果が高く**なります。
* **テーブルの監査** - Azure Table Storage にログが書き込まれます。

「[データベースに対する監査を設定する](#subheading-2)」セクションの説明にあるように、さまざまな種類のイベント カテゴリの監査を構成できます。

<!--For each Event Category, auditing of **Success** and **Failure** operations are configured separately.-->

特定のデータベースに対して、または既定のサーバー ポリシーとして、監査ポリシーを定義できます。 既定のサーバー監視ポリシーがサーバー上にある既存と新規作成のすべてのデータベースに適用されます。

## <a name="a-idsubheading-2aset-up-auditing-for-your-database"></a><a id="subheading-2"></a>データベースに対する監査を設定する
以下のセクションでは、Azure ポータルを使用した監査の構成について説明します。

### <a name="a-idsubheading-2-1blob-auditinga"></a><a id="subheading-2-1">BLOB の監査</a>
1. https://portal.azure.com で [Azure Portal](https://portal.azure.com)を起動します。
2. 監査する SQL Database または SQL Server の [設定] ブレードに移動します。 [設定] ブレードで、**[監査と脅威の検出]** を選択します。
   
    <a id="auditing-screenshot"></a>
    ![ナビゲーション ウィンドウ][1]
3. データベース監査構成ブレードで、このデータベースをサーバーの設定に従い監査する場合は、**[サーバーから設定を継承]** チェック ボックスをオンにします。 このオプションをオンにすると、このコンテキストでサーバーの監査設定を表示または変更する **[サーバーの監査設定を表示]** リンクが表示されます。
   
    ![ナビゲーション ウィンドウ][2]
4. (サーバー レベルの監査に加え、あるいはサーバー レベルの監査の代わりに) データベース レベルで BLOB 監査を有効にする場合、**[監査設定をサーバーから継承]** オプションを**オフ**にし、[監査] を**オン**にし、**BLOB** 監査タイプを選択します。
   
   > サーバーの BLOB 監査が有効になっている場合、データベース構成監査とサーバー BLOB 監査が横並びで存在します。  
   > 
   > 
   
    ![ナビゲーション ウィンドウ][3]
5. **[容量の詳細]** を選択して [監査ログの容量] ブレードを開きます。 ログを保存する Azure Storage アカウントと古いログを削除するタイミングを指定するリテンション期間を選択し、一番下にある **[OK]** をクリックします。 **ヒント:** 監査レポートのテンプレートを最大限活用するには、すべての監査済みデータベースに同じストレージ アカウントを使用してください。
   
    <a id="storage-screenshot"></a>
    ![ナビゲーション ウィンドウ][4]
6. 監査対象イベントをカスタマイズする場合、PowerShell または REST API から実行できます。詳細については、「[Automation (PowerShell / REST API)](#subheading-7)」セクションを参照してください。
7. **[Save]**をクリックします。

### <a name="a-idsubheading-2-2table-auditinga"></a><a id="subheading-2-2">テーブルの監査</a>
> [!NOTE]
> **テーブルの監査**をセットアップする前に、["ダウンレベル クライアント"](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) を使用しているかどうかを確認します。 また、ファイアウォールを厳密に設定している場合は、テーブル監査を有効にすると[データベースの IP エンドポイントが変更される](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)ことに注意してください。
> 
> 

1. https://portal.azure.com で [Azure Portal](https://portal.azure.com)を起動します。
2. 監査する SQL Database または SQL Server の [設定] ブレードに移動します。 [設定] ブレードで、**[監査と脅威検出]** を選択します (*[「BLOB の監査」セクションのスクリーンショットを参照してください](#auditing-screenshot)*)。
3. データベース監査構成ブレードで、このデータベースをサーバーの設定に従い監査する場合は、**[サーバーから設定を継承]** チェック ボックスをオンにします。 このオプションをオンにすると、このコンテキストでサーバーの監査設定を表示または変更する **[サーバーの監査設定を表示]** リンクが表示されます。
   
    ![ナビゲーション ウィンドウ][2]
4. サーバーから監査設定を継承しない場合、**[監査設定をサーバーから継承]** オプションを**オフ**にし、監査を**オン**にし、**テーブル**監査タイプを選択します。
   
    ![ナビゲーション ウィンドウ][3-tbl]
5. **[容量の詳細]** を選択して [監査ログの容量] ブレードを開きます。 ログを保存する Azure Storage アカウントと古いログを削除するタイミングを指定するリテンション期間を選択します。 **ヒント:** 監査レポートのテンプレートを最大限活用するには、すべての監査済みデータベースに同じストレージ アカウントを使用してください (*[「BLOB の監査」セクションのスクリーンショットを参照してください](#storage-screenshot)*)。
6. 監査するイベントをカスタマイズするには、**[監査されたイベント]** をクリックします。 **[イベントによるログ]** ブレードで、すべてのイベントのログを記録するには、**[成功]** および **[失敗]** をクリックします。または、イベント カテゴリを個別に選択します。
   
   > 監査対象イベントは PowerShell または REST API からカスタマイズすることもできます。詳細については、「[Automation (PowerShell / REST API)](#subheading-7)」セクションを参照してください。
   > 
   > 
   
    ![ナビゲーション ウィンドウ][5]
7. 監査設定を構成したら、新しい**脅威の検出** (プレビュー) 機能をオンにし、電子メールを構成してセキュリティの警告を受信します。 脅威の検出を使用すると、セキュリティ上の脅威になる可能性がある異常なデータベース アクティビティに対するプロアクティブ アラートを受信できます。 詳細については、 [脅威の検出の概要](sql-database-threat-detection-get-started.md) に関するページを参照してください。
8. **[保存]**をクリックします。

## <a name="a-idsubheading-3aanalyze-audit-logs-and-reports"></a><a id="subheading-3"></a>監査ログとレポートを分析する
監査ログは、設定時に選択した Azure ストレージ アカウントで集計されます。

[Azure ストレージ エクスプローラー](http://storageexplorer.com/)などのツールを使用して監査ログを調査できます。

**テーブル**監査ログと **BLOB** 監査ログの両方の分析について、下の詳細を参照してください。

### <a name="a-idsubheading-3-1blob-auditinga"></a><a id="subheading-3-1">BLOB の監査</a>
BLOB 監査は "**sqldbauditlogs**" という名前のコンテナー内に BLOB ファイルのコレクションとして保存されます。

BLOB 監査ログのストレージ フォルダー階層、BLOB の命名規則、ログ形式の詳細については、「[BLOB 監査ログ形式のリファレンス (doc ファイルのダウンロード)](https://go.microsoft.com/fwlink/?linkid=829599)」を参照してください。

BLOB 監査ログはいくつかの方法で表示できます。

1. Azure Portal - **下の[「テーブルの監査」セクション](#activity-ui)の方法 (1) を参照してください** (Excel ダウンロードはサポートされません)。
2. ポータル経由で、あるいは [Azure ストレージ エクスプローラー](http://storageexplorer.com/)などのツールを利用して Azure Storage BLOB コンテナーからログ ファイルをダウンロードします。
   
    ログ ファイルをローカルでダウンロードすると、ファイルをダブルクリックし、SSMS でログを開き、表示し、分析できます。
   
    他の方法:
   
   * Azure ストレージ エクスプローラーを利用し、**複数のファイルを同時にダウンロード**できます。特定のサブフォルダー (特定の日付のログ ファイルをすべて含むサブフォルダーなど) を右クリックし、"名前を付けて保存" を選択すると、ローカル フォルダーに保存されます。
     
       数個のファイル (あるいは上の説明にあるように丸 1 日分) をダウンロードしたら、それらをローカルで次のように結合できます。
     
       **[SSMS]、[ファイル]、[開く]、[Merge Extended Events (拡張イベントの結合)] の順に開き、結合するすべてのファイルを選択する**
   * プログラムを使用する:
     
     * 拡張イベント リーダー **C# ライブラリ** ([詳細はここを参照](https://blogs.msdn.microsoft.com/extended_events/2011/07/20/introducing-the-extended-events-reader/))
     * **PowerShell** を利用し、拡張イベント ファイルにクエリを実行する ([詳細はここを参照](https://sqlscope.wordpress.com/2014/11/15/reading-extended-event-files-using-client-side-tools-only/))

### <a name="a-idsubheading-3-2table-auditinga"></a><a id="subheading-3-2">テーブルの監査</a>
テーブル監査ログは、**SQLDBAuditLogs** プレフィックスを付け、Azure Storage テーブルのコレクションとして保存されます。

テーブル監査ログ形式の詳細については、[テーブル監査ログ形式のリファレンス (doc ファイルのダウンロード)](http://go.microsoft.com/fwlink/?LinkId=506733) を参照してください。

テーブル監査ログはいくつかの方法で表示できます。

<a id="activity-ui"></a>

1. [Azure Portal](https://portal.azure.com) - **[監査と脅威検出]** ブレードの一番上で **[More (その他)]** をクリックし、**[Explore (探索)]** をクリックします。 **[監査レコード]** ブレードが開きます。ここでログを参照できます。
   
   * [監査レコード] ブレードの上の領域にある **[フィルター]** をクリックすると、特定の日付を表示できます。
   * [監査レコード] ブレードの上の領域にある **[Excel で開く]** をクリックすると、Excel 形式で監査ログをダウンロードし、表示できます。
     
     ![Navigation pane][7]
2. あるいは、事前に構成されたレポート テンプレートが[ダウンロードできる Excel スプレッドシート](http://go.microsoft.com/fwlink/?LinkId=403540)として用意されているので、ログ データをすぐに分析できます。 監査ログでテンプレートを使用するには、Excel 2013 またはそれ以降、および Power Query が必要です ( [ここ](http://www.microsoft.com/download/details.aspx?id=39379)でダウンロードできます)。
   
    Power Query を使用すると、Azure ストレージ アカウントから直接 Excel テンプレートに監査ログをインポートすることもできます。 その後監査レコードを確認し、ログ データを使用しダッシュ ボードとレポートを作成できます。
   
    ![Navigation pane][9]

## <a name="a-idsubheading-5apractices-for-usage-in-production"></a><a id="subheading-5"></a>運用環境での使用手法
<!--The description in this section refers to screen captures above.-->

### <a name="a-idsubheading-6auditing-geo-replicated-databasesa"></a><a id="subheading-6">Geo レプリケーション対応データベースの監査</a>
Geo レプリケーション対応データベースを使用するとき、監査の種類によっては、プライマリ データベースとセカンダリ データベースのいずれかまたは両方に監査を設定できます。

**テーブルの監査** - 「[データベースに対する監査を設定する](#subheading-2-2)」セクションの説明にあるように、2 つのデータベース (プライマリとセカンダリ) のそれぞれに、データベース レベルまたはサーバー レベルで個別のポリシーを構成できます。

**BLOB 監査** - 次の指示に従ってください。

1. **プライマリ データベース** - 「[データベースに対する監査を設定する](#subheading-2-1)」セクションの説明にあるように、サーバーまたはデータベース自体で **BLOB 監査**をオンにします。
2. **セカンダリ データベース** - BLOB 監査のオン/オンは、プライマリ データベース監査設定からのみ切り替えることができます。
   
   * 「[データベースに対する監査を設定する](#subheading-2-1)」セクションの説明にあるように、**プライマリ データベース**で **BLOB 監査**をオンにします。 (**注:** BLOB 監査は、サーバーではなく、データベース自体で有効にする必要があります)。
   * プライマリ データベースで BLOB 監査を有効にすると、セカンダリ データベースでも有効になります。
   * **重要:** 既定では、セカンダリ データベースの**ストレージ設定**はプライマリ データベースのそれと同じになり、**リージョンをまたいだトラフィック**が発生します。 これは、**セカンダリ サーバー**で BLOB 監査を有効にし、セカンダリ サーバーの**ストレージ設定**でローカル ストレージを構成する (セカンダリ データベースのストレージ場所が上書きされ、結果的に各データベースが監査ログをローカル ストレージに保存します) ことで回避できます。  

<br>

### <a name="a-idsubheading-6storage-key-regenerationa"></a><a id="subheading-6">ストレージ キーの再生成</a>
運用環境では、ストレージ キーを最新の情報に定期的に更新することが推奨されます。 キーを最新の情報に更新する場合は、監査ポリシーを再度保存する必要があります。 このプロセスは次のとおりです。

1. [Storage Details (ストレージ詳細)] ブレードで、**[ストレージ アクセス キー]** を *[プライマリ]* から *[セカンダリ]* に切り替え、一番下にある **[OK]** をクリックします。 [監査構成] ブレードの上部の **[保存]** をクリックします。
   
    ![Navigation pane][6]
2. ストレージ構成ブレードに移動し、 **プライマリ アクセス キー** を *再生成*します。
   
    ![Navigation pane][8]
3. [監査の構成] ブレードに戻り、**[ストレージ アクセス キー]** を *[セカンダリ]* から *[プライマリ]* に切り替え、一番下にある **[OK]** をクリックします。 [監査構成] ブレードの上部の **[保存]** をクリックします。
4. [ストレージの構成] ブレードに戻り、*セカンダリ アクセス キー*を**再生成** (次のキー更新サイクルの準備として) します。

## <a name="a-idsubheading-7aautomation-powershell--rest-api"></a><a id="subheading-7"></a>Automation (PowerShell / REST API)
次の自動化ツールを利用し、Azure SQL Database で監査を構成することもできます。

1. **PowerShell コマンドレット**
   
   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]
2. **REST API - BLOB 監査**
   
   * [データベース BLOB 監査ポリシーの作成または更新](https://msdn.microsoft.com/en-us/library/azure/mt695939.aspx)
   * [サーバー BLOB 監査ポリシーの作成または更新](https://msdn.microsoft.com/en-us/library/azure/mt771861.aspx)
   * [データベース BLOB 監査ポリシーの取得](https://msdn.microsoft.com/en-us/library/azure/mt695938.aspx)
   * [サーバー BLOB 監査ポリシーの取得](https://msdn.microsoft.com/en-us/library/azure/mt771860.aspx)
   * [サーバー BLOB 監査操作結果の取得](https://msdn.microsoft.com/en-us/library/azure/mt771862.aspx)
3. **REST API - テーブル監査**
   
   * [データベース監査ポリシーの作成または更新](https://msdn.microsoft.com/en-us/library/azure/mt604471.aspx)
   * [サーバー監査ポリシーの作成または更新](https://msdn.microsoft.com/en-us/library/azure/mt604383.aspx)
   * [データベース監査ポリシーの取得](https://msdn.microsoft.com/en-us/library/azure/mt604381.aspx)
   * [サーバー監査ポリシーの取得](https://msdn.microsoft.com/en-us/library/azure/mt604382.aspx)

<!--Anchors-->
[Azure SQL Database の監査の概要]: #subheading-1
[データベースに対する監査を設定する]: #subheading-2
[監査ログとレポートを分析する]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7


<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[3-tbl]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on_table.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_audited_events.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_storage_key_regeneration.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_activity_log.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_regenerate_key.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_report_template.png


[101]: https://msdn.microsoft.com/en-us/library/azure/mt603731(v=azure.200).aspx
[102]: https://msdn.microsoft.com/en-us/library/azure/mt619329(v=azure.200).aspx
[103]: https://msdn.microsoft.com/en-us/library/azure/mt603796(v=azure.200).aspx
[104]: https://msdn.microsoft.com/en-us/library/azure/mt603574(v=azure.200).aspx
[105]: https://msdn.microsoft.com/en-us/library/azure/mt603531(v=azure.200).aspx
[106]: https://msdn.microsoft.com/en-us/library/azure/mt603794(v=azure.200).aspx
[107]: https://msdn.microsoft.com/en-us/library/azure/mt619353(v=azure.200).aspx



<!--HONumber=Dec16_HO1-->


