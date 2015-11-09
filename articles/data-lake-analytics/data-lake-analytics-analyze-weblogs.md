<properties 
   pageTitle="Azure Data Lake Analytics を使用して Web サイトのログを分析する | Azure" 
   description="Data Lake Analytics を使用して Web サイトのログを分析する方法について説明します。" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/28/2015"
   ms.author="jgao"/>

# チュートリアル: Azure Data Lake Analytics を使用して Web サイトのログを分析する

Data Lake Analytics を使用して Web サイトのログを分析する方法について、特に、Web サイトへのアクセスを試みたときにエラーが発生した参照元の特定に重点を置いて説明します。

>[AZURE.NOTE]アプリケーションが動作していることだけを確認する場合は、「[Azure Data Lake Analytics の対話型チュートリアルの使用](data-lake-analytics-use-interactive-tutorials.md)」を参照すると時間を短縮できます。このチュートリアルでは、同じシナリオと同じコードを使用します。このチュートリアルは、Data Lake Analytics アプリケーションの作成と実行の作業全体を開発者に体験してもらうことを目的としています。

## 前提条件:

- **Visual Studio 2015、Visual Studio 2013 Update 4、または Visual Studio 2012 (Visual C++ インストール済み)**。 
- **Microsoft Azure SDK for .NET Version 2.5 以上**。[Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx) を使用してインストールします。
- **[Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs)**。 

	Data Lake Tools for Visual Studio をインストールすると、Visual Studio に **[Data Lake]** メニューが表示されます。
	
	![U-SQL Visual Studio menu](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)

- **Data Lake Analytics と Data Lake Tools for Visual Studio の基本的な知識**。作業を開始するには、以下のリンク先を参照してください。
 
	- [Azure プレビュー ポータルで Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)。
	- [Data Lake Tools for Visual Studio を使用して U-SQL スクリプトを開発する](data-lake-analytics-data-lake-tools-get-started.md)。

- **Data Lake Analytics アカウント**。 [Azure Data Lake Analytics アカウントの作成](data-lake-analytics-get-started-portal.md#create_adl_analytics_account)に関する記述を参照してください。

	Data Lake Tools では、Data Lake Analytics アカウントの作成がサポートされていません。そのため、Azure プレビュー ポータル、Azure PowerShell、.NET SDK、または Azure CLI を使用して作成する必要があります。 
- **Data Lake Analytics アカウントへのサンプル データのアップロード**。 [既定の Data Lake ストレージ アカウントへの SearchLog.tsv のアップロード](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account)に関する記述を参照してください。

    Data Lake Analytics ジョブを実行するには、いくつかのデータが必要です。Data Lake Tools でデータのアップロードがサポートされていても、このチュートリアルに従いやすくするため、サンプル データのアップロードにはポータルを使用します。
 
## Azure への接続  
 

U-SQL スクリプトをビルドしてテストするには、まず、Azure に接続する必要があります。

**Data Lake Analytics に接続するには**

1. Visual Studio を開きます。
2. **[Data Lake]** メニューの **[オプションと設定]** をクリックします。
4. **[サインイン]** をクリックします。他のユーザーがサインインしている場合は、**[ユーザーの変更]** をクリックし、指示に従います。
5. **[OK]** をクリックして、[オプションと設定] ダイアログ ボックスを閉じます。

**Data Lake Analytics アカウントを参照するには**

1. Visual Studio で、**Ctrl + Alt + S** キーを押して、**サーバー エクスプローラー**を開きます。
2. **サーバー エクスプローラー**で、**[Azure]**、**[Data Lake Analytics]** の順に展開します。Data Lake Analytics アカウントが複数ある場合は、そのリストが表示されます。Visual Studio で Data Lake Analytics アカウントを作成することはできません。アカウントを作成する場合は、「[Azure プレビュー ポータルで Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)」または「[Azure PowerShell で Azure Data Lake Analytics の使用を開始する](data-lake-get-started-powershell.md)」を参照してください。

## U-SQL アプリケーションの作成 

U SQL アプリケーションの大部分は、U-SQL スクリプトです。U-SQL の詳細については、[U-SQL の使用](data-lake-analytics-u-sql-get-started.md)に関するページを参照してください。

アプリケーションにはユーザー定義演算子を追加できます。詳細については、[Data Lake Analytics ジョブの U-SQL ユーザー定義演算子の作成](data-lake-analytics-u-sql-user-defined-operators.md)に関するページを参照してください。
 
**Data Lake Analytics ジョブを作成して送信するには**

1. **[ファイル]** メニューの **[新規作成]** をクリックし、**[プロジェクト]** をクリックします。
2. プロジェクトの種類として、[U-SQL プロジェクト] を選択します。

	![new U-SQL Visual Studio project](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)
	
3. **[OK]** をクリックします。Visual Studio で、Script.usql ファイルを含むソリューションが作成されます。
4. 次のスクリプトを Script.usql ファイルに入力します。

        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;
        
        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema. 
        DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
        CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
        RETURNS @result TABLE
        (
            s_date DateTime,
            s_time string,
            s_sitename string,
            cs_method string, 
            cs_uristem string,
            cs_uriquery string,
            s_port int,
            cs_username string, 
            c_ip string,
            cs_useragent string,
            cs_cookie string,
            cs_referer string, 
            cs_host string,
            sc_status int,
            sc_substatus int,
            sc_win32status int, 
            sc_bytes int,
            cs_bytes int,
            s_timetaken int
        )
        AS
        BEGIN
        
            @result = EXTRACT
                s_date DateTime,
                s_time string,
                s_sitename string,
                cs_method string,
                cs_uristem string,
                cs_uriquery string,
                s_port int,
                cs_username string,
                c_ip string,
                cs_useragent string,
                cs_cookie string,
                cs_referer string,
                cs_host string,
                sc_status int,
                sc_substatus int,
                sc_win32status int,
                sc_bytes int,
                cs_bytes int,
                s_timetaken int
            FROM @"/Samples/Data/WebLog.log"
            USING Extractors.Text(delimiter:' ');
        
        // Create a table for storing referrers and status 
        DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
        @weblog = SampleDBTutorials.dbo.WeblogsView();
        CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
        (
            INDEX idx1
            CLUSTERED(Year ASC)
            PARTITIONED BY HASH(Year)
        ) AS 
        
        SELECT s_date.Year AS Year,
            s_date.Month AS Month,
            s_date.Day AS Day,
            cs_referer,
            sc_status,
            COUNT(DISTINCT c_ip) AS cnt
        FROM @weblog
        GROUP BY s_date,
                cs_referer, 
                sc_status;
        
        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;
        
        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();

    U-SQL を理解するには、[Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)に関するページを参照してください。
       
5. **[送信]** ボタンの横に、Analytics アカウントを指定します。
5. **ソリューション エクスプローラー**で、**Script.usql** を右クリックして **[スクリプトのビルド]** をクリックします。[出力] ウィンドウで結果を確認します。
6. **ソリューション エクスプローラー**で、**Script.usql** を右クリックして **[スクリプトの送信]** をクリックします。
7. **[Analytics アカウント]** にジョブを実行するアカウントが設定されていることを確認し、**[送信]** をクリックします。送信が完了すると、Data Lake Tools for Visual Studio の [結果] ウィンドウに送信結果とジョブのリンクが示されます。
8. ジョブが正常に完了するまで待機します。ジョブが失敗した場合は、ソース ファイルが存在しない可能性があります。このチュートリアルの「前提条件」を参照してください。トラブルシューティングの詳細については、[Azure Data Lake Analytics ジョブの監視とトラブルシューティング](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)に関するページを参照してください。

    ジョブが完了すると、次の画面が表示されます。
    
    ![data lake analytics analyze weblogs website logs](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)

**ジョブの出力を表示するには**

1. **サーバー エクスプローラー**で、**[Azure]**、**[Data Lake Analytics]**、ご使用の Data Lake Analytics アカウント、**[ストレージ アカウント]** の順に展開し、既定の Data Lake ストレージ アカウントを右クリックして **[エクスプローラー]** をクリックします。 
2.  **[サンプル]** をダブルクリックしてフォルダーを開き、**[出力]** をダブルクリックします。
3.  **UnsuccessfulResponsees.log** をダブルクリックします。
4.  ジョブのグラフ ビューで出力ファイルをダブルクリックして、出力ファイルに直接移動することもできます。
        
## 関連項目

さまざまなツールを使用して Data Lake Analytics の使用を開始する方法については、以下を参照してください。

- [Azure プレビュー ポータルで Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
- [Azure PowerShell で Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-powershell.md)
- [.NET SDK で Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-net-sdk.md)

開発に関するその他のトピックも参照してください。

- [Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)
- [Azure Data Lake Analytics U-SQL 言語の使用を開始する](data-lake-analytics-u-sql-get-started.md)
- [Data Lake Analytics ジョブの U-SQL ユーザー定義演算子を作成する](data-lake-analytics-u-sql-user-defined-operators.md)

<!---HONumber=Nov15_HO1-->