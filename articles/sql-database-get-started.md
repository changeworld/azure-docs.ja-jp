<properties urlDisplayName="How to create and provision" pageTitle="SQL データベースの概要 - Azure" metaKeywords="" description="Get started creating and managing SQL Databases in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="Getting Started with Azure SQL Database" authors="jeffryg"  solutions="" writer="" manager="jeffreyg" editor="tysonn"  />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/04/2014" ms.author="jeffreyg" />


#Microsoft Azure SQL データベースの概要

このチュートリアルでは、Azure の管理ポータルを使用して、Microsoft Azure SQL データベース管理の基礎を学習します。データベース管理に慣れていない場合は、このレッスンを通じて基本的なスキルを約 30 分で学ぶことができます。 

このチュートリアルは、SQL Server または Azure SQL データベースを使用した経験がない読者を対象に作成されています。このチュートリアルを完了すると、Azure にサンプル データベースが作成され、管理ポータルを使用して基本的な管理タスクを実行する方法を理解できます。

Azure にサンプル データベースを作成して準備し、Excel を使用してシステム データとユーザー データを照会します。


##目次##

* [手順 1: Microsoft Azure アカウントを作成する](#Subscribe)
* [手順 2: Azure に接続してデータベースを作成する](#Subscribe)
* [手順 3: ファイアウォールを構成する](#ConfigFirewall)
* [手順 4: Transact-SQL スクリプトを使用してデータとスキーマを追加する](#AddData)
* [手順 5: スキーマを作成する](#createschema)
* [手順 6: データを挿入する](#insertData)
* [手順 7: SQL Database の管理ポータルでサンプル データとシステム データを照会する](#QueryDBSysData)
* [手順 8: データベース ログインを作成してアクセス許可を割り当てる](#DBLogin)
* [手順 9: 他のアプリケーションから接続する](#ClientConnection)


<h2 id="Subscribe">手順 1: Microsoft Azure アカウントを作成する</h2>

1. Web ブラウザーを開いて、[http://azure.microsoft.com](http://azure.microsoft.com) にアクセスします。
無料アカウントを取得するには、右上にある [無料評価版] をクリックして、表示される手順に従います。

2. アカウントが作成されました。これで使用開始する準備が整いました。


<h2 id="Connect">手順 2: Azure に接続してデータベースを作成する</h2>


1. [管理ポータル](http://manage.windowsazure.com)にサインインします。次のようなナビゲーション ウィンドウが表示されます。

	![Navigation pane][Image1]

2. ページの下部にある **[新規]** をクリックします。**[新規]** をクリックすると、作成できる項目が一覧表示されます。

3. **[SQL データベース]**、**[カスタム作成]** の順にクリックします。 

	![Navigation pane][Image2]

このオプションを選択すると、自分を管理者として新しいサーバーと SQL データベースを同時に作成できます。システム管理者として、このチュートリアルで後で実行する SQL データベースの管理ポータルへの接続など、さまざまなタスクを実行できます。  

4.  **[カスタム作成]** をクリックすると、[データベースの設定] ページが表示されます。このページで、サーバーに空の SQL データベースを作成するのに必要な基本情報を指定します。テーブルとデータの追加は、後の手順で行います。 

    [データベースの設定] ページで、次のように指定します。

	![Navigation pane][Image3]

* データベース名として「**School**」と入力します。 

* エディション、最大サイズ、照合順序は既定の設定を使用します。 

* **[新しい SQL データベース サーバー]** をクリックします。新しいサーバーを選択すると、2 ページ目が追加され、このページで Administrator アカウントとリージョンを設定します。 

* 完了したら、矢印をクリックして次のページに進みます。


7. [サーバーの設定] ページで、次のように指定します。 

	![Navigation pane][Image4]

* 空白を含まない 1 語で管理者名を入力します。SQL データベースでは、暗号化された接続による SQL 認証を使用してユーザー ID を検証します。管理者アクセス許可を持つ新しい SQL Server 認証のログインが、入力した名前を使用して作成されます。管理者名には、Windows ユーザーまたは Live ID ユーザー名は使用できません。Windows 認証は SQL データベースではサポートされません。

* 大文字、小文字、および数字または記号を組み合わせた 9 文字以上のパスワードを指定します。パスワードの複雑さの詳細については、ヘルプ バブルを使用してください。

* リージョンを選択します。リージョンによって、サーバーの Geo が決まります。リージョンは簡単に切り替えることができないので、このサーバーに最適なリージョンを選択してください。また、最も近い場所を選択してください。Azure アプリケーションとデータベースを同じリージョンに置くことで、送信帯域幅コストおよびデータ遅延を削減できます。

* **[Azure サービスにサーバーへのアクセスを許可します]** チェック ボックスをオンのままにしておいてください。そうすると、SQL Database の管理ポータル、Office 365 の Excel、または Azure SQL レポートを使用してこのデータベースに接続できます。

* 終了したら、ページの下部にあるチェックマークをクリックします。

サーバー名を指定しなかったことに注意してください。SQL データベース サーバーは世界各地からアクセスできる必要があるため、サーバーが作成されたときに SQL データベースによって適切な DNS エントリが構成されます。他の DNS エントリと衝突しないように名前が生成されます。SQL データベース サーバーの名前は変更できません。

先ほど作成した **School** データベースをホストするサーバーの名前を確認するには、左側のナビゲート ウィンドウで **[SQL データベース]** をクリックし、**[SQL データベース]** リスト ビューで **School** データベースをクリックします。**[クイック スタート]** ページを下にスクロールして、サーバーの名前を確認します。

次のステップでは、コンピューターで実行中のアプリケーションから接続して、SQL データベース サーバー上のデータベースにアクセスできるようにファイアウォールを構成します。



<h2 id="ConfigFirewall">手順 3: ファイアウォールを構成する</h2>

ファイアウォールを構成して接続が許可されるようにするために、サーバーのページで情報を入力します。

**注: **SQL Database サービスは、TDS プロトコルが使用する TCP ポート 1433 だけで使用できます。そのため、ネットワークとローカル コンピューターのファイアウォールがポート 1433 で発信 TCP 通信を許可するようにします。詳細については、[SQL Database のファイアウォールに関する記事](http://social.technet.microsoft.com/wiki/contents/articles/2677.sql-azure-firewall-ja-jp.aspx)を参照してください。


1. 左のナビゲーション ウィンドウで、**[SQL データベース]** をクリックします。

2. ページの上部にある **[サーバー]** をクリックします。次に、作成したサーバーをクリックし、サーバーのページを開きます。

3. サーバーのページで、**[構成]** をクリックして **[使用できる IP アドレス]** 設定を開き、**[使用できる IP アドレスに追加します]** リンクをクリックします。こうすると新しいファイアウォール ルールが作成され、デバイスがリッスンしているルーターやプロキシ サーバーからの接続要求が許可されるようになります。

4. 追加のファイアウォール ルールを作成するには、ルール名と IP の範囲の開始および終了の値を指定します。

5. このサーバーと他の Azure サービスとのやり取りを有効にするには、**[はい]** をクリックして **[Microsoft Azure サービス]** オプションを開きます。 

7. 変更内容を保存するには、ページの下部にある **[保存]** をクリックします。

6. ルールを保存すると、ページは次のスクリーンショットに似た表示になります。

	![Navigation pane][Image7]

これで、Azure 上の SQL データベース サーバー、サーバーへのアクセスを許可するファイアウォール ルール、データベース オブジェクト、および管理者ログインが用意できました。ただし、照会を行う作業データベースがまだありません。それには、データベースにスキーマと実際のデータを設定する必要があります。

このチュートリアルでは簡単に利用できるツールのみを使用するため、SQL データベースの管理ポータルでクエリ ウィンドウを使用して、Transact-SQL スクリプトを実行し、定義済みのデータベースをビルドします。

スキルが向上するにつれて、プログラムによる手法や SQL Server Data Tools のデザイン サーフェイスなど、データベースを作成する別の方法にも興味がわいてきます。ローカル サーバーで実行される既存の SQL Server データベースが既にある場合は、設定した Azure サーバーにそのデータベースを簡単に移行することができます。方法については、このチュートリアルの末尾にあるリンクを参照してください。 



<h2 id="AddData">手順 4.Transact-SQL スクリプトを使用してデータとスキーマを追加する</h2>

この手順では、2 つのスクリプトを実行します。最初のスクリプトは、テーブル、列、リレーションシップを定義するスキーマを作成します。2 つ目のスクリプトは、データを追加します。それぞれの手順は、別々の接続で独立して実行されます。以前に SQL Server でデータベースを構築したことがある場合は、SQL データベースでは CREATE コマンドと INSERT コマンドを別々のバッチで実行する必要があるという点が異なることに注意してください。SQL データベースでは、転送中のデータに対する攻撃を最小限に抑えるために、この要件が課されています。 

**注: **スキーマおよびデータ値は、この [MSDN の記事](http://msdn.microsoft.com/ja-jp/library/windowsazure/ee621790.aspx "MSDN article") のものを使用し、SQL Database で動作するように変更されています。

1. ホーム ページに移動します。[管理ポータル](http://manage.windowsazure.com)で、ホーム ページの項目の一覧に **School** データベースが表示されます。

	![Navigation pane][Image8]

2. **[School]** をクリックして選択し、ページの下部にある **[管理]** をクリックします。これにより、SQL データベースの管理ポータルが開きます。このポータルは Azure の管理ポータルとは別物です。このポータルを使用して、Transact-SQL コマンドとクエリを実行します。

3. **School** データベースにログインするには、管理者のログイン名とパスワードを入力します。これは、サーバーの作成時に指定した管理者ログインです。

4. SQL Database の管理ポータルで、リボンの **[新しいクエリ]** をクリックします。ワークスペースに空のクエリ ウィンドウが開きます。次のステップでは、このウィンドウを使用して、空のデータベースに構造とデータを追加する一連の定義済みスクリプトをコピーします。



<h2 id="createschema">手順 5.スキーマを作成する</h2>

この手順では、以下のスクリプトを使用してスキーマを作成します。スクリプトは、まず、同じ名前の既存のテーブルがないかチェックして名前の衝突がないことを確認し、[CREATE TABLE](http://msdn.microsoft.com/ja-jp/library/windowsazure/ee336258.aspx)  ステートメントを使用してテーブルを作成します。さらに、このスクリプトは、[ALTER TABLE] (http://msdn.microsoft.com/ja-jp/library/windowsazure/ee336286.aspx) ステートメントを使用してプライマリ キーとテーブルのリレーションシップを指定します。

スクリプトをコピーしてクエリ ウィンドウに貼り付けます。ウィンドウの上部にある **[実行]** をクリックして、スクリプトを実行します。

<div style="width:auto; height:600px; overflow:auto"><pre>
	-- Create the Department table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[Department]') 
		AND type in (N'U'))
   	BEGIN
  	CREATE TABLE [dbo].[Department](
		[DepartmentID] [int] NOT NULL,
		[Name] [nvarchar](50) NOT NULL,
		[Budget] [money] NOT NULL,
		[StartDate] [datetime] NOT NULL,
		[Administrator] [int] NULL,
     CONSTRAINT [PK_Department] PRIMARY KEY CLUSTERED 
    (
	[DepartmentID] ASC
    )WITH (IGNORE_DUP_KEY = OFF)
    )
    END;
	GO

	-- Create the Person table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[Person]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[Person](
		[PersonID] [int] IDENTITY(1,1) NOT NULL,
		[LastName] [nvarchar](50) NOT NULL,
		[FirstName] [nvarchar](50) NOT NULL,
		[HireDate] [datetime] NULL,
		[EnrollmentDate] [datetime] NULL,
 	 CONSTRAINT [PK_School.Student] PRIMARY KEY CLUSTERED 	
	(
	[PersonID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	) 
	END;
	GO

	-- Create the OnsiteCourse table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[OnsiteCourse](
		[CourseID] [int] NOT NULL,
		[Location] [nvarchar](50) NOT NULL,
		[Days] [nvarchar](50) NOT NULL,
		[Time] [smalldatetime] NOT NULL,
 	 CONSTRAINT [PK_OnsiteCourse] PRIMARY KEY CLUSTERED 
	(
		[CourseID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	) 
	END;
	GO

	-- Create the OnlineCourse table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[OnlineCourse]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[OnlineCourse](
		[CourseID] [int] NOT NULL,
		[URL] [nvarchar](100) NOT NULL,
 	 CONSTRAINT [PK_OnlineCourse] PRIMARY KEY CLUSTERED 
	(
		[CourseID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	) 
	END;
	GO

	--Create the StudentGrade table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[StudentGrade]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[StudentGrade](
		[EnrollmentID] [int] IDENTITY(1,1) NOT NULL,
		[CourseID] [int] NOT NULL,
		[StudentID] [int] NOT NULL,
		[Grade] [decimal](3, 2) NULL,
	 CONSTRAINT [PK_StudentGrade] PRIMARY KEY CLUSTERED 
	(
		[EnrollmentID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	) 
	END;
	GO

	-- Create the CourseInstructor table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[CourseInstructor]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[CourseInstructor](
		[CourseID] [int] NOT NULL,
		[PersonID] [int] NOT NULL,
	 CONSTRAINT [PK_CourseInstructor] PRIMARY KEY CLUSTERED 
	(
		[CourseID] ASC,
		[PersonID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	) 
	END;
	GO

	-- Create the Course table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[Course]') 
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[Course](
		[CourseID] [int] NOT NULL,
		[Title] [nvarchar](100) NOT NULL,
		[Credits] [int] NOT NULL,
		[DepartmentID] [int] NOT NULL,
 	 CONSTRAINT [PK_School.Course] PRIMARY KEY CLUSTERED 
	(
		[CourseID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	)
	END;
	GO

	-- Create the OfficeAssignment table.
	IF NOT EXISTS (SELECT * FROM sys.objects 
		WHERE object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]')
		AND type in (N'U'))
	BEGIN
	CREATE TABLE [dbo].[OfficeAssignment](
		[InstructorID] [int] NOT NULL,
		[Location] [nvarchar](50) NOT NULL,
		[Timestamp] [timestamp] NOT NULL,
 	 CONSTRAINT [PK_OfficeAssignment] PRIMARY KEY CLUSTERED 
	(
		[InstructorID] ASC
	)WITH (IGNORE_DUP_KEY = OFF)
	)
	END;
	GO

	-- Define the relationship between OnsiteCourse and Course.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnsiteCourse_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[OnsiteCourse]'))
	ALTER TABLE [dbo].[OnsiteCourse]  WITH CHECK ADD  
       CONSTRAINT [FK_OnsiteCourse_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[OnsiteCourse] CHECK 
       CONSTRAINT [FK_OnsiteCourse_Course];
	GO

	-- Define the relationship between OnlineCourse and Course.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_OnlineCourse_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[OnlineCourse]'))
	ALTER TABLE [dbo].[OnlineCourse]  WITH CHECK ADD  
       CONSTRAINT [FK_OnlineCourse_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[OnlineCourse] CHECK 
       CONSTRAINT [FK_OnlineCourse_Course];
	GO
	-- Define the relationship between StudentGrade and Course.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Course]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))
	ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
       CONSTRAINT [FK_StudentGrade_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[StudentGrade] CHECK 
       CONSTRAINT [FK_StudentGrade_Course];
	GO

	--Define the relationship between StudentGrade and Student.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_StudentGrade_Student]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[StudentGrade]'))	
	ALTER TABLE [dbo].[StudentGrade]  WITH CHECK ADD  
       CONSTRAINT [FK_StudentGrade_Student] FOREIGN KEY([StudentID])
	REFERENCES [dbo].[Person] ([PersonID]);
	GO
	ALTER TABLE [dbo].[StudentGrade] CHECK 
       CONSTRAINT [FK_StudentGrade_Student];
	GO

	-- Define the relationship between CourseInstructor and Course.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
  	 WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Course]')
  	 AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
	ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  
  	 CONSTRAINT [FK_CourseInstructor_Course] FOREIGN KEY([CourseID])
	REFERENCES [dbo].[Course] ([CourseID]);
	GO
	ALTER TABLE [dbo].[CourseInstructor] CHECK 
 	  CONSTRAINT [FK_CourseInstructor_Course];
	GO

	-- Define the relationship between CourseInstructor and Person.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
 	  WHERE object_id = OBJECT_ID(N'[dbo].[FK_CourseInstructor_Person]')
	   AND parent_object_id = OBJECT_ID(N'[dbo].[CourseInstructor]'))
	ALTER TABLE [dbo].[CourseInstructor]  WITH CHECK ADD  
 	  CONSTRAINT [FK_CourseInstructor_Person] FOREIGN KEY([PersonID])
	REFERENCES [dbo].[Person] ([PersonID]);
	GO
	ALTER TABLE [dbo].[CourseInstructor] CHECK 
  	 CONSTRAINT [FK_CourseInstructor_Person];
	GO

	-- Define the relationship between Course and Department.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
       WHERE object_id = OBJECT_ID(N'[dbo].[FK_Course_Department]')
       AND parent_object_id = OBJECT_ID(N'[dbo].[Course]'))
	ALTER TABLE [dbo].[Course]  WITH CHECK ADD  
       CONSTRAINT [FK_Course_Department] FOREIGN KEY([DepartmentID])
	REFERENCES [dbo].[Department] ([DepartmentID]);
	GO
	ALTER TABLE [dbo].[Course] CHECK CONSTRAINT [FK_Course_Department];
	GO

	--Define the relationship between OfficeAssignment and Person.
	IF NOT EXISTS (SELECT * FROM sys.foreign_keys 
	  WHERE object_id = OBJECT_ID(N'[dbo].[FK_OfficeAssignment_Person]')
 	  AND parent_object_id = OBJECT_ID(N'[dbo].[OfficeAssignment]'))
	ALTER TABLE [dbo].[OfficeAssignment]  WITH CHECK ADD  
 	  CONSTRAINT [FK_OfficeAssignment_Person] FOREIGN KEY([InstructorID])
	REFERENCES [dbo].[Person] ([PersonID]);
	GO
	ALTER TABLE [dbo].[OfficeAssignment] CHECK 
   	 CONSTRAINT [FK_OfficeAssignment_Person];
	GO
</pre></div>



<h2 id="insertData">手順 6.データを挿入する</h2>

新しいクエリ ウィンドウを開いて、以下のスクリプトを貼り付けます。スクリプトを実行してデータを挿入します。このスクリプトは、[INSERT](http://msdn.microsoft.com/ja-jp/library/windowsazure/ee336284.aspx)  ステートメントを使用して各列に値を追加します。

<div style="width:auto; height:600px; overflow:auto"><pre>
	-- Insert data into the Person table.
	SET IDENTITY_INSERT dbo.Person ON;
	GO
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (1, 'Abercrombie', 'Kim', '1995-03-11', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (2, 'Barzdukas', 'Gytis', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (3, 'Justice', 'Peggy', null, '2001-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (4, 'Fakhouri', 'Fadi', '2002-08-06', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (5, 'Harui', 'Roger', '1998-07-01', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (6, 'Li', 'Yan', null, '2002-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (7, 'Norman', 'Laura', null, '2003-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (8, 'Olivotto', 'Nino', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (9, 'Tang', 'Wayne', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (10, 'Alonso', 'Meredith', null, '2002-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (11, 'Lopez', 'Sophia', null, '2004-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (12, 'Browning', 'Meredith', null, '2000-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (13, 'Anand', 'Arturo', null, '2003-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (14, 'Walker', 'Alexandra', null, '2000-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (15, 'Powell', 'Carson', null, '2004-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (16, 'Jai', 'Damien', null, '2001-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (17, 'Carlson', 'Robyn', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (18, 'Zheng', 'Roger', '2004-02-12', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (19, 'Bryant', 'Carson', null, '2001-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (20, 'Suarez', 'Robyn', null, '2004-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (21, 'Holt', 'Roger', null, '2004-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (22, 'Alexander', 'Carson', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (23, 'Morgan', 'Isaiah', null, '2001-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (24, 'Martin', 'Randall', null, '2005-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (25, 'Kapoor', 'Candace', '2001-01-15', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (26, 'Rogers', 'Cody', null, '2002-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (27, 'Serrano', 'Stacy', '1999-06-01', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (28, 'White', 'Anthony', null, '2001-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (29, 'Griffin', 'Rachel', null, '2004-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (30, 'Shan', 'Alicia', null, '2003-09-01');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (31, 'Stewart', 'Jasmine', '1997-10-12', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (32, 'Xu', 'Kristen', '2001-7-23', null);
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (33, 'Gao', 'Erica', null, '2003-01-30');
	INSERT INTO dbo.Person (PersonID, LastName, FirstName, HireDate, EnrollmentDate)
	VALUES (34, 'Van Houten', 'Roger', '2000-12-07', null);
	GO
	SET IDENTITY_INSERT dbo.Person OFF;
	GO
	
</pre></div>


<h2 id="QueryDBSysData">手順 7.SQL データベースの管理ポータルでサンプル データとシステム データを照会する</h2>

作業をチェックするために、入力したデータを返すクエリを実行します。また、組み込みストアド プロシージャを実行して、SQL データベース サーバーで実行されているデータベースの情報を示すデータ管理ビューを表示することもできます。

<h4 id="QueryDB">サンプル データのクエリ</h4>

新しいクエリ ウィンドウで、以下の Transact-SQL スクリプトをコピーして実行し、追加したデータの一部を取得します。


<div style="width:auto; height:auto; overflow:auto"><pre>
	SELECT * From Person
</pre></div>

Person テーブルから、PersonID、LastName、FirstName、HireDate、EnrollmentDate を含む 34 行の結果セットが表示されます。


<h4 id="QuerySys">システム データのクエリ</h4>

システム ビューと組み込みストアド プロシージャを使用してサーバーから情報を取得することもできます。このチュートリアルでは、いくつかのコマンドだけを試します。

次のコマンドを実行して、サーバーでどのデータベースが使用できるか確認します。 

	SELECT * FROM sys.databases  

このコマンドを実行して、現在サーバーに接続中のユーザーの一覧を返します。

	SELECT user_name(),suser_sname()

このストアド プロシージャを実行して、**School** データベースにあるすべてのオブジェクトの一覧を返します。

	EXEC SP_help

**School** データベースに対するポータル接続は閉じないでください。少し後にまた必要になります。



<h2 id="DBLogin">手順 8.データベース ログインを作成してアクセス許可を割り当てる</h2>

SQL データベースでは、Transact-SQL を使用してログインを作成しアクセス許可を付与することができます。このレッスンでは、Transact-SQL を使用して 3 つの作業を実行します。


1. SQL Server 認証ログインを作成する
2. データベース ユーザーを作成し、
3. ロール メンバーシップを介してアクセス許可を付与する

SQL Server 認証ログインはサーバー接続時に使用されます。SQL データベース サーバー上のデータベースにアクセスするユーザーは全員、SQL Server 認証のログイン名とパスワードを指定してサーバーに接続します。 

ログインを作成するには、まず **master** データベースに接続する必要があります。

<h4 id="CreateLogin">SQL Server 認証ログインを作成する</h4>

1. [管理ポータル](http://manage.windowsazure.com)で、**[SQL データベース]** を選択し、**[サーバー]** をクリックしてサーバーを選び、白い矢印をクリックして
サーバーのページを開きます。

2. [クイック スタート] ページで、**[サーバーの管理]** をクリックして、SQL Database の管理ポータルに対する新しい接続を開きます。 

3. 接続するデータベースの **master** を指定し、次に、ユーザー名とパスワードを使用してログインします。これは、サーバーの作成時に指定した管理者ログインです。

4. 新しいブラウザー ウィンドウに、SQL Database の管理ポータルが表示され、**master** に接続します。

5. 下図のようなエラーがページに表示された場合は、無視してください。**[新しいクエリ]** をクリックします。**master** データベースに対して Transact-SQL コマンドを実行できるクエリ ウィンドウが開きます。

	![Navigation pane][Image15]

6. 次のコマンドをコピーしてクエリ ウィンドウに貼り付けます。

        CREATE LOGIN SQLDBLogin WITH password='Password1';

7. コマンドを実行して、SQLDBLogin という名前の新しい SQL Server ログインを作成します。


<h4 id="CreateDBuser">データベース ユーザーを作成してアクセス許可を割り当てる</h4>

SQL Server 認証ログインを作成した後、次のステップは、ログインに関連付けられたデータベースとアクセス許可レベルを割り当てることです。そのために、各データベースに**データベース ユーザー**を作成します。

1. **School** データベースに接続する SQL Database の管理ポータルのページに戻ります。ブラウザー ウィンドウを閉じた場合は、先の「Transact-SQL スクリプトを使用してデータとスキーマを追加する」の手順を使用して **School** データベースに対する新しい接続を開始します。 

	SQL Database の管理ポータルのページでは、左上隅に **School** データベースの名前が表示されます。

	![Navigation pane][Image12]

2. **[新しいクエリ]** をクリックして新しいクエリ ウィンドウを開き、次のステートメントをコピーします。 

	    CREATE USER SQLDBUser FROM LOGIN SQLDBLogin;

3. スクリプトを実行します。このスクリプトは、ログインに基づいて新しいデータベース ユーザーを作成します。

   次に、db_datareader ロールを使用してアクセス許可を割り当てます。このロールに割り当てられたデータベース ユーザーは、データベースのすべてのユーザー テーブルからすべてのデータを読み取ることができます。 

4. 新しいクエリ ウィンドウを開いて、以下のステートメントを入力して実行します。このステートメントは組み込みストアド プロシージャを実行して、作成した新しいユーザーに db_datareader ロールを割り当てます。 

        EXEC sp_addrolemember 'db_datareader', 'SQLDBUser';

これで、**School** データベースに対する読み取り専用アクセス許可を持つ新しい SQL Server 認証ログインが準備されました。この手順を使用して、データに対するアクセス レベルが異なる別の SQL Server 認証ログインを作成することもできます。


<h2 id="ClientConnection">手順 9.他のアプリケーションから接続する</h2>

ADO.NET を使用して Microsoft Azure SQL Database に接続することができます。内部設置型の接続とは異なり、接続が終了したり、新しい接続が一時的にブロックされる可能性のある、Throttling または他のサービス障害について考慮しておく必要があります。このような状況を一時障害と呼びます。一時障害を管理するには、再試行戦略を実装してください。Azure SQL Database に接続すると、[一時障害処理アプリケーション ブロック](http://go.microsoft.com/fwlink/?LinkId=519356)の使用に関するドキュメント (2013 年 4 月、Enterprise Library の一部) に記載されている、一時障害の条件を特定する検出戦略を確認することができます。

<h4>サンプル: C# コンソール アプリケーション</h4>


	static void Main(string[] args)
    {
        //NOTE: Use appropriate exception handling in a production application.

        //Replace
        //  builder["Server"]: {servername} = Your Azure SQL Database server name
        //  builder["User ID"]: {username}@{servername} = Your Azure SQL Database user name and server name
        //  builder["Password"]: {password} = Your Azure SQL Database password

        System.Data.SqlClient.SqlConnectionStringBuilder builder = new System.Data.SqlClient.SqlConnectionStringBuilder();
        builder["Server"] = "{servername}";
        builder["User ID"] = "{username}@{servername}";
        builder["Password"] = "{password}";

        builder["Database"] = "AdventureWorks2012";
        builder["Trusted_Connection"] = false;
        builder["Integrated Security"] = false;
        builder["Encrypt"] = true;

        //1. Define an Exponential Backoff retry strategy for Azure SQL Database throttling (ExponentialBackoff Class). An exponential back-off strategy will gracefully back off the load on the service.
        int retryCount = 4;
        int minBackoffDelayMilliseconds = 2000;
        int maxBackoffDelayMilliseconds = 8000;
        int deltaBackoffMilliseconds = 2000;

        ExponentialBackoff exponentialBackoffStrategy = 
          new ExponentialBackoff("exponentialBackoffStrategy",
              retryCount,
              TimeSpan.FromMilliseconds(minBackoffDelayMilliseconds), 
              TimeSpan.FromMilliseconds(maxBackoffDelayMilliseconds),
              TimeSpan.FromMilliseconds(deltaBackoffMilliseconds));

        //2. Set a default strategy to Exponential Backoff.
        RetryManager manager = new RetryManager(new List<RetryStrategy>
        {  
            exponentialBackoffStrategy 
        }, "exponentialBackoffStrategy");

        //3. Set a default Retry Manager. A RetryManager provides retry functionality, or if you are using declarative configuration, you can invoke the RetryPolicyFactory.CreateDefault
            RetryManager.SetDefault(manager);

        //4. Define a default SQL Connection retry policy and SQL Command retry policy. A policy provides a retry mechanism for unreliable actions and transient conditions.
        RetryPolicy retryConnectionPolicy = manager.GetDefaultSqlConnectionRetryPolicy();
        RetryPolicy retryCommandPolicy = manager.GetDefaultSqlCommandRetryPolicy();

        //5. Create a function that will retry the connection using a ReliableSqlConnection.
        retryConnectionPolicy.ExecuteAction(() =>
        {
            using (ReliableSqlConnection connection = new ReliableSqlConnection(builder.ConnectionString))
            {
                connection.Open();

                IDbCommand command = connection.CreateCommand();
                command.CommandText = "SELECT Name FROM Production.Product";

                //6. Create a function that will retry the command calling ExecuteCommand() from the ReliableSqlConnection
                retryCommandPolicy.ExecuteAction(() =>
                {
                    using (IDataReader reader = connection.ExecuteCommand<IDataReader>(command))
                    {
                        while (reader.Read())
                        {
                            string name = reader.GetString(0);

                            Console.WriteLine(name);
                        }
                    }
                });                  
            }
        });

        Console.ReadLine();
    }



<h2 id="NextSteps">次のステップ</h2>

これで SQL データベースと管理ポータルに慣れたので、SQL Server データベース管理者が使用する他のツールや技法も試すことができます。

新しいデータベースを積極的に管理するには、SQL Server Management Studio をインストールして使用することを検討してください。Management Studio は、Azure 上で動作しているものも含め、SQL Server データベースを管理する主なデータベース管理ツールです。Management Studio を使用すると、将来のためにクエリを保存し、新しいテーブルとストアド プロシージャを追加し、構文チェッカーや Intellisense、テンプレートを含む豊富なスクリプト作成環境で Transact-SQL のスキルを磨くことができます。手始めに、「[Azure: Microsoft のクラウド プラットフォーム](http://www.azure.microsoft.com/ja-jp/documentation/articles/sql-database-manage-azure-ssms/)」の手順に従ってください。

Transact-SQL クエリとデータ定義言語を自在に操作できることが、データベース管理者には不可欠です。Transact-SQL に熟練していない場合は、まず、「[チュートリアル: Transact-SQL ステートメントの作成](http://msdn.microsoft.com/ja-jp/library/ms365303.aspx) 」を参照して、基本的なスキルを学習してください。

内部設置型データベースを SQL データベースに移動する方法は他にもあります。既存のデータベースがある場合、または、実習のためにサンプル データベースをダウンロードした場合は、次の方法を試してください。

* [Azure SQL データベースへのデータベース移行](http://msdn.microsoft.com/ja-jp/library/windowsazure/ee730904.aspx)
* [Azure SQL データベースでのデータベースのコピー](http://msdn.microsoft.com/ja-jp/library/windowsazure/ff951624.aspx)
* [Azure 仮想マシンへの SQL Server データベースの配置](http://msdn.microsoft.com/ja-jp/library/dn195938(v=sql.120).aspx)



[Image1]: ./media/sql-database-get-started/1NavPaneDBSelected_SQLTut.png
[Image2]: ./media/sql-database-get-started/2MainPageCustomCreateDB_SQLTut.png
[Image3]: ./media/sql-database-get-started/3DatabaseSettings_SQLTut.PNG
[Image4]: ./media/sql-database-get-started/4ServerSettings_SQLTut.PNG
[Image5]: ./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG
[Image6]: ./media/sql-database-get-started/6DBConfigFirewall_SQLTut.PNG
[Image7]: ./media/sql-database-get-started/7DBConfigFirewallSAVE_SQLTut.png
[Image8]: ./media/sql-database-get-started/20MainPageHome_SQLTut.PNG
[Image9]: ./media/sql-database-get-started/9dblistschool_SQLTut.PNG
[Image10]: ./media/sql-database-get-started/10dbportalmanagebutton_SQLTut.PNG
[Image11]: ./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.PNG
[Image12]: ./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG
[Image13]: ./media/sql-database-get-started/13DBQueryResults_SQLTut.PNG
[Image14]: ./media/sql-database-get-started/14DBPortalConnectMaster_SQLTut.PNG
[Image15]: ./media/sql-database-get-started/15DBPortalConnectMasterErr_SQLTut.PNG
[Image16]: ./media/sql-database-get-started/16ExcelConnect_SQLTut.png
[Image17]: ./media/sql-database-get-started/17ExcelSelect_SQLTut.PNG
[Image18]: ./media/sql-database-get-started/18ExcelTable_SQLTut.PNG
[Image19]: ./media/sql-database-get-started/19ExcelImport_SQLTut.png
[Image20]: ./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.PNG


<!--HONumber=35.1-->
