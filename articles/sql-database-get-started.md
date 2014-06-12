<properties linkid="manage-services-getting-started-with-sqldbs" urlDisplayName="作成およびプロビジョニングの方法" pageTitle="SQL データベースの概要 - Azure" metaKeywords="" description="Azure での SQL データベースの作成および管理を開始します。" metaCanonical="" services="sql-database" documentationCenter="" title="Azure SQL データベースの概要" authors=""  solutions="" writer="" manager="" editor=""  />





#Azure SQL データベースの概要

このチュートリアルでは、Azure の管理ポータルを使用して、Azure SQL データベース管理の基礎を学習します。データベース管理に慣れていない場合は、このレッスンを通じて基本的なスキルを約 30 分で学ぶことができます。
 

このチュートリアルは、SQL Server または Azure SQL データベースを使用した経験がない読者を対象に作成されています。このチュートリアルを完了すると、Azure にサンプル データベースが作成され、管理ポータルを使用して基本的な管理タスクを実行する方法を理解できます。

Azure にサンプル データベースを作成して準備し、Excel などのアプリケーションを使用してシステム データとユーザー データを照会します。


##目次##

* [ステップ 1.: Azure のアカウントを作成する](#Subscribe)
* [ステップ 2.: Azure に接続してデータベースを作成する](#Connect)
* [ステップ 3.: ファイアウォールを構成する](#ConfigFirewall)
* [ステップ 4.: Transact-SQL スクリプトを使用してデータとスキーマを追加する](#AddData)
* [ステップ 5.: スキーマを作成する](#createschema)
* [ステップ 6.: データを挿入する](#insertData)
* [ステップ 7.: SQL データベースの管理ポータルでサンプル データとシステム データを照会する](#QueryDBSysData)
* [ステップ 8.: データベース ログインを作成してアクセス許可を割り当てる](#DBLogin)
* [ステップ 9.: 他のアプリケーションから接続する](#ClientConnection)
* [ステップ 10.: SQL データ同期を構成する](#ConfigureDataSync)


<h2><a id="Subscribe"></a>ステップ 1.: Azure のアカウントを作成する</h2>

1. Web ブラウザーを開いて、[http://www.windowsazure.com](http://www.windowsazure.com) にアクセスします。
無料アカウントを取得するには、右上にある [無料評価版] をクリックして、表示される手順に従います。

2. アカウントが作成されました。これで使用開始する準備が整いました。


<h2><a id="Connect"></a>ステップ 2.: Azure に接続してデータベースを作成する</h2>


1. [管理ポータル](http://manage.windowsazure.com)にサインインします。次のようなナビゲーション ウィンドウが表示されます。

	![Image1](./media/sql-database-get-started/1NavPaneDBSelected_SQLTut.png)

2. ページの下部にある **[新規]** をクリックします。**[新規]** をクリックすると、作成できる項目が一覧に表示されます。

3. **[SQL データベース]**、**[カスタム作成]** の順にクリックします。

	![Image2](./media/sql-database-get-started/2MainPageCustomCreateDB_SQLTut.png)

	このオプションを選択すると、自分を管理者として新しいサーバーを同時に作成できます。システム管理者として、このチュートリアルで後で実行する SQL データベースの管理ポータルへの接続など、さまざまなタスクを実行できます。

4. **[カスタム作成]** をクリックすると、[データベースの設定] ページが表示されます。このページで、サーバーに空のデータベースを作成するのに必要な基本情報を指定します。テーブルとデータの追加は、後の手順で行います。

	[データベースの設定] ページで、次のように指定します。

	![Image3](./media/sql-database-get-started/3DatabaseSettings_SQLTut.PNG)

	* データベース名として「**School**」と入力します。

	* エディション、最大サイズ、照合順序は既定の設定を使用します。

	* **[新しい SQL データベース サーバー]** をクリックします。新しいサーバーを選択すると、2 ページ目が追加され、このページで Administrator アカウントとリージョンを設定します。

	* 指定が完了したら、矢印をクリックして、次のページに進みます。


5. [サーバーの設定] ページで、次のように指定します。

	![Image4](./media/sql-database-get-started/4ServerSettings_SQLTut.PNG)

	* 空白を含まない 1 語で管理者名を入力します。SQL データベースでは、暗号化された接続による SQL 認証を使用してユーザー ID を検証します。管理者アクセス許可を持つ新しい SQL Server 認証のログインが、入力した名前を使用して作成されます。管理者名には、Windows ユーザーまたは Windows Live ID は使用できません。Windows 認証は SQL データベースではサポートされません。

	* 大文字、小文字、および数字または記号を組み合わせた 9 文字以上のパスワードを指定します。

	* リージョンを選択します。リージョンによって、サーバーの Geo が決まります。リージョンは簡単に切り替えることができないので、このサーバーに最適なリージョンを選択してください。自分またはユーザーに最も近い場所を選択します。Azure アプリケーションとデータベースを同じリージョンに置くことで、送信帯域幅コストおよびデータ遅延を削減できます。

	* **[Azure サービスにサーバーへのアクセスを許可します]** チェック ボックスをオンのままにしておいてください。そうすれば、SQL データベースの管理ポータル、Office 365 の Excel、または Azure SQL レポートを使用してこのデータベースに接続できます。

	* 終了したら、ページの下部にあるチェックマークをクリックします。

	サーバー名を指定しなかったことに注意してください。SQL データベース サーバーは世界各地からアクセスできる必要があるため、サーバーが作成されたときに SQL データベースによって適切な DNS エントリが構成されます。他の DNS エントリと衝突しないように名前が生成されます。SQL データベース サーバーの名前は変更できません。

	次のステップでは、コンピューターで実行中のアプリケーションから接続して、SQL データベース サーバー上のデータベースにアクセスできるようにファイアウォールを構成します。



<h2><a id="ConfigFirewall"></a>ステップ 3.: ファイアウォールを構成する</h2>

ファイアウォールを構成して接続が許可されるようにするために、サーバーのページで情報を入力します。

**注:** SQL データベース サービスは、TDS プロトコルの使用する TCP ポート 1433 だけで使用できます。そのため、ネットワークとローカル コンピューターのファイアウォールがポート 1433 で発信 TCP 通信を許可するようにします。詳細については、「[SQL Database Firewall (SQL データベースのファイアウォール)](http://social.technet.microsoft.com/wiki/contents/articles/2677.sql-azure-firewall-en-us.aspx)」を参照してください。


1. 左のナビゲーション ウィンドウで、**[SQL データベース]** をクリックします。

2. ページの上部にある **[サーバー]** をクリックします。次に、作成したサーバーをクリックします。右側に白い矢印が表示されます。矢印をクリックして、サーバーのページを開きます。

	![Image5](./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG)

3. サーバーのページで **[構成]** をクリックしてファイアウォールの構成設定を開き、次のようにルールを指定します。

	![Image6](./media/sql-database-get-started/6DBConfigFirewall_SQLTut.png)


	* 現在のクライアント IP アドレスをコピーします。これは使用するルーターまたはプロキシ サーバーがリッスンしている IP アドレスです。SQL データベースが現在の接続で使用されている IP アドレスを検出すると、このデバイスからの接続要求を受け入れるためのファイアウォール ルールを作成できます。
	
	* 開始範囲と終了範囲の両方に IP アドレスを貼り付けます。その後、この範囲が狭すぎることを示す接続エラーが発生した場合は、このルールを編集して範囲を広げることができます。

	* 使用するコンピューターや会社の名前など、ファイアウォール ルールの名前を入力します。

	* チェックマークをクリックして、ルールを保存します。

	ルールを保存すると、ページは次のスクリーンショットに似た表示になります。

	![Image7](./media/sql-database-get-started/7DBConfigFirewallSAVE_SQLTut.png)


4. ページの下部にある **[保存]** をクリックして、手順を完了します。**[保存]** が表示されない場合は、ブラウザーのページを更新します。

これで、Azure 上の SQL データベース サーバー、サーバーへのアクセスを許可するファイアウォール ルール、データベース オブジェクト、および管理者ログインが用意できました。次は、SQL データベースの管理ポータルでクエリ ウィンドウを使用して、Transact-SQL スクリプトを実行し、定義済みのデータベースを作成します。

スキルが向上するにつれて、プログラムによる手法や SQL Server Data Tools のデザイナーなど、データベースを作成する別の方法にも興味がわいてきます。ローカル サーバーで実行される既存の SQL Server データベースが既にある場合は、設定した Azure サーバーにそのデータベースを簡単に移行することができます。方法については、このチュートリアルの末尾にあるリンクを参照してください。



<h2><a id="AddData"></a>ステップ 4.: Transact-SQL スクリプトを使用してデータとスキーマを追加する</h2>

この手順では、2 つのスクリプトを実行します。最初のスクリプトは、テーブル、列、リレーションシップを定義するスキーマを作成します。2 つ目のスクリプトは、データを追加します。それぞれの手順は、別々の接続で独立して実行されます。以前に SQL Server でデータベースを構築したことがある場合は、SQL データベースでは CREATE コマンドと INSERT コマンドを別々のバッチで実行する必要があるという点が異なることに注意してください。SQL データベースでは、転送中のデータに対する攻撃を最小限に抑えるために、この要件が課されています。

** 注:** スキーマとデータの値はこの [MSDN の記事](http://msdn.microsoft.com/ja-jp/library/windowsazure/ee621790.aspx "MSDN article")からとられていて、SQL データベースで動作するように変更されています。

1. ホーム ページに移動します。[管理ポータル](http://manage.windowsazure.com)で、ホーム ページの項目の一覧に **School** データベースが表示されます。

	![Image8](./media/sql-database-get-started/8MainPageHome_SQLTut.png)


2. **[School]** をクリックします。右側に白い矢印が表示されます。矢印をクリックして、データベースのページを開きます。

	![Image9](./media/sql-database-get-started/9DBListSchool_SQLTut.png)

3. ページの下部にある **[管理]** をクリックします。表示されていない場合は、ブラウザーのウィンドウを更新します。これにより、SQL データベースの管理ポータルが開きます。このポータルは Azure の管理ポータルとは別物です。このポータルを使用して、Transact-SQL コマンドとクエリを実行します。
	
	![Image10](./media/sql-database-get-started/10DBPortalManageButton_SQLTut.png)


4. 管理者のログイン名とパスワードを入力します。これは、サーバーの作成時に指定した管理者ログインです。

	![Image11](./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.png)


5. SQL データベースの管理ポータルで **[新しいクエリ]** をクリックします。ワークスペースに空のクエリ ウィンドウが開きます。次のステップでは、このウィンドウを使用して、空のデータベースに構造とデータを追加する一連の定義済みスクリプトをコピーします。

	![Image12](./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG)
	 

<h2><a id="createschema"></a>ステップ 5.: スキーマを作成する</h2>

この手順では、以下のスクリプトを使用してスキーマを作成します。スクリプトは、まず、同じ名前の既存のテーブルがないかチェックして名前の衝突がないことを確認し、[CREATE TABLE](http://msdn.microsoft.com/ja-jp/library/windowsazure/ee336258.aspx) ステートメントを使用してテーブルを作成します。さらに、このスクリプトは [ALTER TABLE](http://msdn.microsoft.com/ja-jp/library/windowsazure/ee336286.aspx) ステートメントを使用してプライマリ キーとテーブルのリレーションシップを指定します。

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



<h2><a id="insertData"></a>ステップ 6.: データを挿入する</h2>

新しいクエリ ウィンドウを開いて、以下のスクリプトを貼り付けます。スクリプトを実行してデータを挿入します。このスクリプトは [INSERT](http://msdn.microsoft.com/ja-jp/library/windowsazure/ee336284.aspx) ステートメントを使用して各列に値を追加しています。

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
	-- Insert data into the Department table.
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (1, 'Engineering', 350000.00, '2007-09-01', 2);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (2, 'English', 120000.00, '2007-09-01', 6);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (4, 'Economics', 200000.00, '2007-09-01', 4);
	INSERT INTO dbo.Department (DepartmentID, [Name], Budget, StartDate, Administrator)
	VALUES (7, 'Mathematics', 250000.00, '2007-09-01', 3);
	GO
	-- Insert data into the Course table.
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (1050, 'Chemistry', 4, 1);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (1061, 'Physics', 4, 1);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (1045, 'Calculus', 4, 7);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (2030, 'Poetry', 2, 2);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (2021, 'Composition', 3, 2);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (2042, 'Literature', 4, 2);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (4022, 'Microeconomics', 3, 4);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (4041, 'Macroeconomics', 3, 4);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (4061, 'Quantitative', 2, 4);
	INSERT INTO dbo.Course (CourseID, Title, Credits, DepartmentID)
	VALUES (3141, 'Trigonometry', 4, 7);
	GO
	-- Insert data into the OnlineCourse table.
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (2030, 'http://www.fineartschool.net/Poetry');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (2021, 'http://www.fineartschool.net/Composition');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (4041, 'http://www.fineartschool.net/Macroeconomics');
	INSERT INTO dbo.OnlineCourse (CourseID, URL)
	VALUES (3141, 'http://www.fineartschool.net/Trigonometry');
	--Insert data into OnsiteCourse table.
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (1050, '123 Smith', 'MTWH', '11:30');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (1061, '234 Smith', 'TWHF', '13:15');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (1045, '121 Smith','MWHF', '15:30');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (4061, '22 Williams', 'TH', '11:15');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (2042, '225 Adams', 'MTWH', '11:00');
	INSERT INTO dbo.OnsiteCourse (CourseID, Location, Days, [Time])
	VALUES (4022, '23 Williams', 'MWF', '9:00');
	-- Insert data into the CourseInstructor table.
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (1050, 1);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (1061, 31);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (1045, 5);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (2030, 4);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (2021, 27);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (2042, 25);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (4022, 18);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (4041, 32);
	INSERT INTO dbo.CourseInstructor(CourseID, PersonID)
	VALUES (4061, 34);
	GO
	--Insert data into the OfficeAssignment table.
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (1, '17 Smith');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (4, '29 Adams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (5, '37 Williams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (18, '143 Smith');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (25, '57 Adams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (27, '271 Williams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (31, '131 Smith');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (32, '203 Williams');
	INSERT INTO dbo.OfficeAssignment(InstructorID, Location)
	VALUES (34, '213 Smith');
	-- Insert data into the StudentGrade table.
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 2, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2030, 2, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 3, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2030, 3, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 6, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2042, 6, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 7, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2042, 7, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2021, 8, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (2042, 8, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 9, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 10, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 11, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 12, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 12, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 14, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 13, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 13, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 14, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 15, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 16, 2);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 17, null);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 19, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 20, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 21, 2);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 22, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4041, 22, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4061, 22, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (4022, 23, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1045, 23, 1.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 24, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 25, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1050, 26, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 26, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 27, 3);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1045, 28, 2.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1050, 28, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 29, 4);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1050, 30, 3.5);
	INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
	VALUES (1061, 30, 4);
	GO
</pre></div>


<h2><a id="QueryDBSysData"></a>ステップ 7.: SQL データベースの管理ポータルでサンプル データとシステム データを照会する</h2>

作業をチェックするために、入力したデータを返すクエリを実行します。また、組み込みストアド プロシージャを実行して、SQL データベース サーバーで実行されているデータベースの情報を示すデータ管理ビューを表示することもできます。

<h4><a id="QueryDB"></a>サンプル データのクエリ</h4>

新しいクエリ ウィンドウで、以下の Transact-SQL スクリプトをコピーして実行し、追加したデータの一部を取得します。


<div style="width:auto; height:auto; overflow:auto"><pre>
	SELECT
		Course.Title as "Course Title"
  		,Department.Name as "Department"
  		,Person.LastName as "Instructor"
  		,OnsiteCourse.Location as "Location"
  		,OnsiteCourse.Days as "Days"
  		,OnsiteCourse.Time as "Time"
	FROM
 	 Course
 	 INNER JOIN Department
  	  ON Course.DepartmentID = Department.DepartmentID
 	 INNER JOIN CourseInstructor
 	   ON Course.CourseID = CourseInstructor.CourseID
 	 INNER JOIN Person
 	   ON CourseInstructor.PersonID = Person.PersonID
 	 INNER JOIN OnsiteCourse
		ON OnsiteCourse.CourseID = CourseInstructor.CourseID;
</pre></div>

下図のような結果セットが表示されます。

![Image13](./media/sql-database-get-started/13DBQueryResults_SQLTut.PNG)


<h4><a id="QuerySys"></a>システム データのクエリ</h4>

システム ビューと組み込みストアド プロシージャを使用してサーバーから情報を取得することもできます。このチュートリアルでは、いくつかのコマンドだけを試します。

次のコマンドを実行して、サーバーでどのデータベースが使用できるか確認します。

	SELECT * FROM sys.databases  

このコマンドを実行して、現在サーバーに接続中のユーザーの一覧を返します。

	SELECT user_name(),suser_sname()

このストアド プロシージャを実行して、**School** データベースにあるすべてのオブジェクトの一覧を返します。

	EXEC SP_help

**School** データベースに対するポータル接続は閉じないでください。少し後にまた必要になります。



<h2><a id="DBLogin"></a>ステップ 8.: データベース ログインを作成してアクセス許可を割り当てる</h2>

SQL データベースでは、Transact-SQL を使用してログインを作成しアクセス許可を付与することができます。このレッスンでは、Transact-SQL を使用して 3 つの作業を実行します。SQL Server 認証ログインの作成、データベース ユーザーの作成、およびロール メンバーシップを介したアクセス許可の付与です。

SQL Server 認証ログインはサーバー接続時に使用されます。SQL データベース サーバー上のデータベースにアクセスするユーザーは全員、SQL Server 認証のログイン名とパスワードを指定してサーバーに接続します。

ログインを作成するには、まず **master** データベースに接続する必要があります。

<h4><a id="CreateLogin"></a>SQL Server 認証ログインを作成する</h4>

1. [管理ポータル](http://manage.windowsazure.com)で、**[SQL データベース]**、**[サーバー]** の順にクリックし、サーバーを選択し、白い矢印をクリックして、サーバーのページを開きます。

	![Image5](./media/sql-database-get-started/5DBPortalDatabasesServers_SQLTut.PNG)

2. [クイック スタート] ページで、**[サーバーの管理]** をクリックして、SQL データベースの管理ポータルに対する新しい接続を開きます。

3. 管理者の名前とパスワードを入力します。これは、サーバーの作成時に指定した管理者ログインです。

	![Image20](./media/sql-database-get-started/11ManageDatabaseLogin_SQLTut.png)


4. 新しいブラウザー ウィンドウに、SQL データベース管理ポータルが表示されます。上部にある **[データベースの選択]** をクリックし、**[master]** をクリックします。

	![Image14](./media/sql-database-get-started/14DBPortalConnectMaster_SQLTut.PNG)

5. 下図のようなエラーがページに表示された場合は、無視してください。**[新しいクエリ]** をクリックします。**master** データベースに対して Transact-SQL コマンドを実行できるクエリ ウィンドウが開きます。

	![Image15](./media/sql-database-get-started/15DBPortalConnectMasterErr_SQLTut.PNG)

6. 次のコマンドをコピーしてクエリ ウィンドウに貼り付けます。

        CREATE LOGIN SQLDBLogin WITH password='Password1';

7. コマンドを実行して、SQLDBLogin という名前の新しい SQL Server ログインを作成します。


<h4><a id="CreateDBuser"></a>データベース ユーザーを作成してアクセス許可を割り当てる</h4>

SQL Server 認証ログインを作成した後、次のステップは、ログインに関連付けられたデータベースとアクセス許可レベルを割り当てることです。そのために、各データベースに**データベース ユーザー**を作成します。

1. **School** データベースに接続する SQL データベース管理ポータルのページに戻ります。ブラウザー ウィンドウを閉じた場合は、先の「Transact-SQL スクリプトを使用してデータとスキーマを追加する」の手順を使用して **School** データベースに対する新しい接続を開始します。

	SQL データベース管理ポータルのページでは、左上隅に **School** データベースの名前が表示されます。

	![Image12](./media/sql-database-get-started/12DBPortalNewQuery_SQLTut.PNG)

2. **[新しいクエリ]** をクリックして新しいクエリ ウィンドウを開き、次のステートメントをコピーします。

	    CREATE USER SQLDBUser FROM LOGIN SQLDBLogin;

3. スクリプトを実行します。このスクリプトは、ログインに基づいて新しいデータベース ユーザーを作成します。

	次に、db_datareader ロールを使用してアクセス許可を割り当てます。このロールに割り当てられたデータベース ユーザーは、データベースのすべてのユーザー テーブルからすべてのデータを読み取ることができます。

4. 新しいクエリ ウィンドウを開いて、以下のステートメントを入力して実行します。このステートメントは組み込みストアド プロシージャを実行して、作成した新しいユーザーに db_datareader ロールを割り当てます。

        EXEC sp_addrolemember 'db_datareader', 'SQLDBUser';

	これで、**School** データベースに対する読み取り専用アクセス許可を持つ新しい SQL Server 認証ログインが準備されました。この手順を使用して、データに対するアクセス レベルが異なる別の SQL Server 認証ログインを作成することもできます。




<h2><a id="ClientConnection"></a>ステップ 9.: 他のアプリケーションから接続する</h2>

動作するデータベースが作成できたので、Excel のブックからこのデータベースに接続できます。

<h4>Excel から接続する</h4>


コンピューターに Excel 2010 がインストールされている場合、以下の手順に従ってサンプル データベースに接続できます。

1. Excel で、[データ] タブの **[その他のデータ ソース]** をクリックし、**[SQL Server]** をクリックします。

2. データ接続ウィザードで、SQL データベース サーバーの完全修飾ドメイン名、および、データベースにアクセスするアクセス許可を持つ SQL Server 認証ログインを入力します。

  サーバー名は、**[データベース]** ページの **[クイック リンク]** に表示されます。サーバー名は、Azure の管理ポータルの [SQL データベース] の [サーバー] ページで、[ダッシュボード] の **[URL の管理]** にも表示されます。

  サーバー名は、一連の英字と数字の後に .database.windows.net が付きます。データベース接続ウィザードではこの名前を指定します。名前を指定するときは、http:// または https:// プレフィックスを付けないでください。

  SQL Server 認証ログインを入力します。テスト目的の場合、サーバーのセットアップ時に作成した管理者ログインを使用できます。通常のデータ アクセスの場合は、先ほど作成したようなデータベース ユーザー ログインを使用します。

	![Image16](./media/sql-database-get-started/16ExcelConnect_SQLTut.png)


3. 次のページで、**[School]** データベースを選択し、**[Course]** を選択します。**[完了]** をクリックします。

	![Image17](./media/sql-database-get-started/17ExcelSelect_SQLTut.png)

4. [データのインポート] ダイアログ ボックスが表示され、データのインポート方法と場所を選択します。既定のオプションを選択したままで、**[OK]** をクリックします。

	![Image19](./media/sql-database-get-started/19ExcelImport_SQLTut.png)


5. ワークシートに、次のようなテーブルが表示されます。
	
	![Image18](./media/sql-database-get-started/18ExcelTable_SQLTut.PNG)

Excel だけを使用すると、一度に 1 つのテーブルしかインポートできません。もっと優れた方法は、PowerPivot for Excel アドインを使用して、複数のテーブルを 1 つのデータセットとしてインポートし操作することです。PowerPivot の操作方法はこのチュートリアルの範囲を超えていますが、詳細な情報はこの [Microsoft Web サイト](http://www.microsoft.com/ja-jp/bi/powerpivot.aspx)で入手できます。

<h2><a id="ConfigureDataSync"></a>ステップ 10.: SQL データ同期を構成する</h2>

<h4>SQL データ同期</h4>

SQL データベース インスタンスを作成したので、SQL データ同期を活用して、複数の場所にある価値の高いデータを同期することができます。

SQL データ同期は、コードやスクリプトを作成することなく、選択したデータを定期的にまたは随時、同期できる SQL データベースの機能です。SQL データ同期は、SQL データベース インスタンス間の同期、または SQL データベースと SQL Server インスタンスを含むハイブリッド トポロジの同期をサポートしています。

SQL データ同期の詳細については、[SQL データ同期の概要](http://go.microsoft.com/fwlink/?LinkId=274959)ページを参照してください。

<h2><a id="NextSteps"></a>次のステップ</h2>

これで SQL データベースと管理ポータルに慣れたので、SQL Server データベース管理者が使用する他のツールや技法も試すことができます。

新しいデータベースを積極的に管理するには、SQL Server Management Studio をインストールして使用することを検討してください。Management Studio は、Azure 上で動作しているものも含め、SQL Server データベースを管理する主なデータベース管理ツールです。Management Studio を使用すると、将来のためにクエリを保存し、新しいテーブルとストアド プロシージャを追加し、構文チェッカーや Intellisense、テンプレートを含む豊富なスクリプト作成環境で Transact-SQL のスキルを磨くことができます。手始めに、「[Managing SQL Databases Using SQL Server Management Studio (SQL Server Management Studio を使用した SQL データベースの管理)](http://www.windowsazure.com/ja-jp/develop/net/common-tasks/sql-azure-management/)」の指示に従ってください。

Transact-SQL クエリとデータ定義言語を自在に操作できることが、データベース管理者には不可欠です。Transact-SQL の経験がない場合は、「[チュートリアル: Transact-SQL ステートメントの作成](http://msdn.microsoft.com/ja-jp/library/ms365303.aspx)」から始めて、基本的なスキルを習得してください。

内部設置型データベースを SQL データベースに移動する方法は他にもあります。既存のデータベースがある場合、または、実習のためにサンプル データベースをダウンロードした場合は、次の方法を試してください。

* [Migrating Databases to SQL Database (データベースの SQL データベースへの移行)](http://msdn.microsoft.com/ja-jp/library/windowsazure/ee730904.aspx)
* [Copying Databases in SQL Database (データベースの SQL データベースへのコピー)](http://msdn.microsoft.com/ja-jp/library/windowsazure/ff951624.aspx)


























