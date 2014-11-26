

# Azure での SQL データベースの管理方法

このガイドでは、Azure SQL データベースに関連する論理サーバーとデータベース インスタンスに対して管理タスクを実行する方法を示します。

##SQL データベースとは

SQL データベースは、Azure 上でリレーショナル データベース管理サービスを実現し、SQL Server テクノロジを基盤としています。SQL データベースを使用すると、データベース インスタンスを簡単に準備してデプロイすることができ、分散したデータ センターを活用して、組み込みのデータ保護と自己復旧機能を活かしたエンタープライズ クラスの可用性、拡張性、およびセキュリティを確保できます。

##目次

* [Azure へのサインイン][Azure へのサインイン]
* [SQL データベースの構成][SQL データベースの構成]
* [Management Studio を使用した接続][Management Studio を使用した接続]
* [Azure へのデータベースのデプロイ][Azure へのデータベースのデプロイ]
* [ログインとユーザーの追加][ログインとユーザーの追加]
* [SQL データベース ソリューションの規模の設定][SQL データベース ソリューションの規模の設定]
* [論理サーバーとデータベース インスタンスの監視][論理サーバーとデータベース インスタンスの監視]
* [次のステップ][次のステップ]


<h2><a id="PreReq1"></a>Azure へのサインイン</h2>

SQL データベースは、Azure におけるリレーショナル データのストレージ サービス、アクセス サービス、および管理サービスを提供します。これを使用するには、Azure サブスクリプションが必要です。

1. Web ブラウザーを開いて、[http://www.windowsazure.com](http://www.windowsazure.com) にアクセスします。無料アカウントを取得するには、右上にある [無料評価版] をクリックして、表示される手順に従います。

2. アカウントが作成されました。これで使用開始する準備が整いました。


<h2><a id="PreReq2"></a>SQL データベースの作成と構成</h2>

次に、論理サーバーを作成し構成する手順を説明します。新しい Azure (プレビュー) 管理ポータルの改訂されたワークフローでは、まずデータベースを作成し、次にサーバーを作成します。

このガイドでは、最初にサーバーを作成します。アップロードする既存の SQL Server データベースがある場合は、この方が便利なことがあります。

<h3><a id="createsrvr"></a>論理サーバーの作成</h3>

1. [http://www.windowsazure.com](http://www.windowsazure.com) でサインインします。

2. **[SQL データベース]** をクリックし、SQL データベースのホーム ページで **[サーバー]** をクリックします。

4. ページの下部にある **[追加]** をクリックします。

5. [サーバーの設定] で、空白を含まない 1 語で管理者名を入力します。

	SQL データベースでは、暗号化された接続を経由して SQL 認証を使用します。sysadmin 固定のサーバー ロールに割り当てられた新しい SQL Server 認証のログインが、入力した名前を使用して作成されます。

	ログインには、電子メール アドレス、Windows ユーザー アカウント、または Windows Live ID は使用できません。クレーム認証と Windows 認証のどちらも SQL データベースではサポートされません。

6. 大文字、小文字、および数字または記号を組み合わせた 9 文字以上の強力なパスワードを指定します。

7. リージョンを選択します。リージョンによって、サーバーのジオ (主要地域)が決まります。リージョンは簡単に切り替えることができないので、このサーバーに最適なリージョンを選択してください。また、最も近い場所を選択してください。Azure アプリケーションとデータベースを同じリージョンに置くことで、送信帯域幅コストおよびデータ遅延を削減できます。

8. **[Azure サービスにサーバーへのアクセスを許可します]** オプションを選択したままにしてください。そうすれば、SQL データベースの管理ポータル、ストレージ サービス、Azure のその他のサービスを使用してこのデータベースに接続できます。

9.  終了したら、ページの下部にあるチェックマークをクリックします。

サーバー名を指定しなかったことに注意してください。SQL データベースでは、DNS エントリが重複しないようにサーバー名は自動的に生成されます。サーバー名は、10 文字の英数字文字列です。SQL データベース サーバーの名前は変更できません。

次の手順では、ネットワークで実行中のアプリケーションからの接続によるアクセスが許可されるようにファイアウォールを構成します。

<h3><a id="configFWLogical"></a>論理サーバーのファイアウォールの構成</h3>

1. **[SQL データベース]** をクリックし、**[サーバー]** をクリックして、作成したサーバーをクリックします。

2. **[構成]** をクリックします。

3. 現在のクライアント IP アドレスをコピーします。ネットワークから接続している場合、これは使用するルーターまたはプロキシ サーバーがリッスンしている IP アドレスです。SQL データベースが現在の接続で使用されている IP アドレスを検出すると、このデバイスからの接続要求を受け入れるためのファイアウォール ルールを作成できます。

4. 範囲の開始値と終了値の両方にこの IP アドレスを貼り付けます。その後、この範囲が狭すぎることを示す接続エラーが発生した場合は、このルールを編集して範囲を広げることができます。

	動的に割り当てられた IP アドレスをクライアント コンピューターで使用する場合は、ネットワーク上でコンピューターに割り当てられた IP アドレスを含む範囲を指定する必要があります。狭い範囲から始めて、必要になった場合にのみ範囲を広げます。

5. 使用するコンピューターや会社の名前など、ファイアウォール ルールの名前を入力します。

6.  チェックマークをクリックして、ルールを保存します。

7. ページの下部にある **[保存]** をクリックして、手順を完了します。**[保存]** が表示されない場合は、ブラウザーのページを更新します。

これで、論理サーバー、IP アドレスからの受信接続を許可するファイアウォール ルール、および管理者ログインが用意できました。次の手順では、ローカル コンピューターに切り替えて、残りの構成手順を完了します。

**注:** 作成した論理サーバーは一時的で、データ センターの物理サーバー上で動的にホストされます。サーバーを削除する場合は、あらかじめこれは回復不可能な操作であることを知っておく必要があります。後でサーバーにアップロードするデータベースは必ずバックアップしておいてください。



<h2><a id="PreReq3"></a>Management Studio を使用した接続</h2>

Management Studio は、単一のワークスペースで複数の SQL Server インスタンスと複数のサーバーを管理できる管理ツールです。内部設置型の SQL Server インスタンスが既に存在する場合は、タスクをサイド バイ サイドで実行するために、内部設置型インスタンスと Azure 上の論理サーバーの両方に対して接続を開くことができます。

Management Studio には、構文チェック機能や、再利用の目的でスクリプトと名前付きクエリを保存する機能など、現時点では管理ポータルで使用できない機能が含まれています。SQL データベースは、単純な表形式のデータ ストリーム (TDS) エンドポイントです。Management Studio を含め、TDS に対して使用できるどのツールも、SQL データベースを操作するときに有効に使用できます。内部設置型サーバーを対象にして作成したスクリプトは、SQL データベース論理サーバーで実行されます。

この手順では、Management Studio を使用して、Azure 上の論理サーバーに接続します。この手順では SQL Server Management Studio Version 2008 R2 または 2012 が必要です。Management Studio のダウンロードまたは接続に関するヘルプが必要な場合は、このサイトの「[SQL Server Management Studio を使用した SQL データベースの管理][SQL Server Management Studio を使用した SQL データベースの管理]」を参照してください。

接続する前に、ローカル システムのポート 1433 で送信要求を許可するファイアウォールの例外を作成する必要が生じることがあります。既定でセキュリティにより保護されたコンピューターでは通常、ポート 1433 が開かれていません。

<h3><a id="configFWOnPremise"></a>内部設置型サーバーのファイアウォールの構成</h3>

1. セキュリティが強化された Windows ファイアウォールで、新しい送信の規則を作成します。

2. **[ポート]** をクリックし、TCP 1433 を指定して、**[接続を許可する]** を選択し、**[パブリック]** プロファイルが選択されていることを確認します。

3. *WindowsAzureSQLDatabase (tcp-out) port 1433* など、わかりやすい名前を指定します。


<h3><a id="logical"></a>論理サーバーに接続する</h3>

1. Management Studio の [サーバーに接続する] で、データベース エンジンが選択されていることを確認し、*サーバー名*.database.widnows.net という形式で論理サーバー名を入力します。

   サーバー ダッシュ ボードの [URL の管理] で、管理ポータルで使用されている完全修飾サーバー名を取得することもできます。

2. [認証] で **[SQL Server 認証]** を選択し、論理サーバーを構成したときに作成した管理者ログインを入力します。

3.  **[オプション]** をクリックします。

4. [データベースへの接続] で **master** を指定します。


<h3><a id="premise"></a>内部設置型サーバーに接続する</h3>

1. Management Studio の [サーバーに接続する] で、データベース エンジンが選択されていることを確認し、*サーバー名*\\*instancename* という形式でローカル インスタンスの名前を入力します。サーバーがローカルであり、既定のインスタンスを使用している場合は、「*localhost*」と入力します。

2. [認証] で **[Windows 認証]** を選択し、sysadmin ロールのメンバーである Windows アカウントを入力します。


<h2><a id="HowTo1"></a>Azure へのデータベースのデプロイ</h2>

内部設置型の SQL Server データベースを Azure に移行する多数のアプローチがあります。このタスクでは、データベースの SQL データベースへのデプロイ ウィザードを使用して、サンプル データベースをアップロードします。

School サンプル データベースはシンブルで便利です。オブジェクトはすべて SQL データベースと互換性があり、移行のためにデータベースを変更または準備する必要がありません。新しい管理者として、まずシンプルなデータベースのデプロイを試して手順を学んだ後で、自分のデータベースを使用してください。

**注:** 内部設置型データベースを Azure に移行するにあたっての準備方法の詳細な作業手順については、SQL データベース移行ガイドを参照してください。また、Azure トレーニング キットをダウンロードすることも検討してください。内部設置型データベースを移行する別の手法を示す演習が含まれています。

<h3><a id="CreateDB"></a>内部設置型サーバーに school データベースを作成する</h3>

このデータベースを作成するスクリプトは、[SQL データベース管理の概要に関するページ][SQL データベース管理の概要に関するページ]にあります。このガイドでは、Management Studio でこれらのスクリプトを実行して、school データベースの内部設置型バージョンを作成します。

1. Management Studio で、内部設置型サーバーに接続します。**[データベース]** を右クリックし、**[新しいデータベース]** をクリックして、「*school*」と入力します。

2. *[school]* を右クリックし、**[新しいクエリ]** をクリックします。

3. チュートリアルからスキーマ作成スクリプトをコピーして実行します。

<div style="width:auto; height:300px; overflow:auto"><pre>
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

次に、データ挿入スクリプトをコピーして実行します。

<div style="width:auto; height:300px; overflow:auto"><pre>
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

   これで、Azure にエクスポートできる内部設置型データベースが準備できました。次は、ウィザードを実行して .bacpac ファイルを作成し、そのファイルを Azure に読み込み、SQL データベースにインポートします。


<h3><a id="DeployDB"></a>SQL データベースにデプロイする</h3>

1.  Management Studio で、移行するデータベースがある内部設置型 SQL Server インスタンスに接続します。

2.  作成した school データベースを右クリックして、**[タスク]** をポイントし、**[データベースの SQL データベースへのデプロイ]** をクリックします。

3.  [デプロイの設定] で、「*school*」のようなデータベースの名前を入力します。

4. **[接続]** をクリックします。

5. [サーバー名] ボックスに、10 文字のサーバー名の後に .databases.windows.net を付けて入力します。

6.  [認証] ボックスで、**[SQL Server 認証]** を選択します。

7.  SQL データベース論理サーバーの作成時に準備した管理者ログイン名とパスワードを入力します。

8.  **[オプション]** をクリックします。

9.  [接続プロパティ] の [データベースへの接続] で、「**master**」と入力します。

10.  **[接続]** をクリックします。これで接続指定が完了し、ウィザードに戻ります。


11.  **[次へ]** をクリックし、**[完了]** をクリックしてウィザードを実行します。

<h3><a id="VerifyDBMigration"></a>データベース デプロイを確認する</h3>

1.  Management Studio で、論理サーバーに接続します。接続が既に開いている場合は、その接続を閉じ、新しい接続を開きます。既存の接続では、接続が確立された時点で実行されていたデータベースのみが表示されます。

   論理サーバーに接続する方法の詳細については、このドキュメントの「 [Management Studio を使用した接続][Management Studio を使用した接続]」を参照してください。

2.  データベース フォルダーを展開します。一覧に school データベースが表示されます。

3.  school データベースを右クリックして、**[新しいクエリ]** をクリックします。

4.  以下のクエリを実行して、データがアクセス可能であることを確認します。

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


<h2><a id="HowTo2"></a>ログインとユーザーの追加</h2>

データベースをデプロイした後に、ログインを構成し、アクセス許可を割り当てる必要があります。次の手順では、2 つのスクリプトを実行します。

最初のスクリプトでは、master データベースに接続してログインを作成するスクリプトを実行します。ログインは、読み取り操作と書き込み操作をサポートするため、および OLE_LINK1OLE_LINK2 "sa" アクセス許可なしでシステム クエリを実行する機能など、OLE_LINK1OLE_LINK2 操作タスクを委任するために使用されます。

作成するログインは、SQL Server 認証ログインであることが必要です。Windows ユーザーの ID を使用する、または ID を要求するスクリプトが既に存在している場合は、そのスクリプトは SQL データベースで動作しません。

2 つ目のスクリプトは、データベース ユーザーにアクセス許可を割り当てます。このスクリプトでは、既に Azure に読み込まれているデータベースに接続します。

<h3><a id="CreateLogins"></a>ログインを作成する</h3>

1. Management Studio で、Azure 上の論理サーバーに接続し、データベース フォルダーを開き、**master** を右クリックして、**[新しいクエリ]** をクリックします。

2.  次の Transact SQL ステートメントを使用して、ログインを作成します。パスワードを、有効なパスワードに置き換えます。それぞれを個別に選択し、**[実行]** をクリックします。残りのログインに対して、同じ手順を繰り返します。

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on master, execute each line separately
    -- use this login to manage other logins on this server
    CREATE LOGIN sqladmin WITH password='&lt;ProvidePassword&gt;'; 
    CREATE USER sqladmin FROM LOGIN sqladmin;
    EXEC sp_addrolemember 'loginmanager', 'sqladmin';

    -- use this login to create or copy a database
    CREATE LOGIN sqlops WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlops FROM LOGIN sqlops;
    EXEC sp_addrolemember 'dbmanager', 'sqlops';
</pre></div>


<h3><a id="CreateDBUsers"></a>データベース ユーザーを作成する</h3>

1.  データベース フォルダーを展開し、**school** を右クリックして、**[新しいクエリ]** をクリックします。

2.  次の Transact SQL ステートメントを使用し、データベース ユーザーを追加します。パスワードを、有効なパスワードに置き換えます。

<div style="width:auto; height:auto; overflow:auto"><pre>
    -- run on a regular database, execute each line separately
    -- use this login for read operations
    CREATE LOGIN sqlreader WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlreader FROM LOGIN sqlreader;
    EXEC sp_addrolemember 'db_datareader', 'sqlreader';

    -- use this login for write operations
    CREATE LOGIN sqlwriter WITH password='&lt;ProvidePassword&gt;';
    CREATE USER sqlwriter FROM LOGIN sqlwriter;
    EXEC sp_addrolemember 'db_datawriter', 'sqlwriter';

    -- grant DMV permissions on the school database to 'sqlops'
    GRANT VIEW DATABASE STATE to 'sqlops';
</pre></div>

<h3><a id="ViewLogins"></a>ログインを表示およびテストする</h3>

1.  [新しいクエリ] ウィンドウで **master** に接続し、次のステートメントを実行します。

        SELECT * from sys.sql_logins;

2.  Management Studio で **school** データベースを右クリックして、**[新しいクエリ]** をクリックします。

3. [クエリ] メニューの **[接続]** をポイントし、**[接続の変更]** をクリックします。

4.  *sqlreader* としてログインします。

5.  次のステートメントをコピーし、実行してみます。オブジェクトが存在しないことを示すエラーが表示されます。

        INSERT INTO dbo.StudentGrade (CourseID, StudentID, Grade)
        VALUES (1061, 30, 9);

6.  2 番目のクエリ ウィンドウを開き、接続コンテキストを *sqlwriter* に変更します。今度は、同じクエリが正常に実行されます。

ここまでで、複数のログインを作成してテストしました。詳細については、「[SQL データベースにおけるデータベースとログインの管理][SQL データベースにおけるデータベースとログインの管理]」および「[動的管理ビューを使用した SQL データベースの監視][動的管理ビューを使用した SQL データベースの監視]」を参照してください。


<h2><a id="HowTo3"></a>論理サーバーとデータベース インスタンスの監視</h2>

ログインの監査、トレースの実行、パフォーマンス カウンターの使用など、内部設置型サーバーで使い慣れている人も多い監視ツールや技法は、SQL データベースでは利用できません。Azure では、データ管理ビュー (DMV) を使用して、データの容量、クエリの問題、および現在の接続を監視します。

詳細については、「[動的管理ビューを使用した SQL データベースの監視][動的管理ビューを使用した SQL データベースの監視]」を参照してください。


<h2><a id="HowTo4"></a>SQL データベース ソリューションの規模の設定</h2>

Azure の場合、データベースの拡張性はスケール アウトと同義です。データ センターにある複数のコモディティ サーバーにワークロードが再配分されます。スケール アウトは、データ容量またはパフォーマンスの問題に対処する戦略の 1 つです。急成長している大規模なデータベースには、アクセスするユーザー数の多少にかかわらず、いずれ、スケール アウト戦略が必要になります。

Azure 上でのスケール アウトは、フェデレーションによって実現するのが最善です。SQL データベースのフェデレーションは、水平的シャーディングに基づいています。つまり、1 つ以上のテーブルを行単位で分割し、複数のフェデレーション メンバーに分配します。

フェデレーションは、あらゆる拡張性の問題に対する唯一の回答ではありません。データまたはアプリケーションの要件の特徴によっては、もっと単純な手法が適していることもあります。以下に、簡単なものから順番に、ソリューション候補を挙げます。

**データベースのサイズを拡大する**

データベースは固定サイズで作成され、エディションごとに最大サイズが決まっています。Web Edition の場合、データベースのサイズは最大 5 GB に拡大できます。Business Edition の場合、データベースの最大サイズは 150 GB です。データ容量を増やす最も明瞭な方法は、エディションおよび最大サイズの変更です。

     ALTER DATABASE school MODIFY (EDITION = 'Business', MAXSIZE=10GB);

**データベースを複数使用してユーザーを配分する**

利用できる状況は限られますが、データベースのコピーを作成し、各データベースにログインとユーザーを配分することもできます。フェデレーションという選択肢が登場する前は、これが負荷を分散する一般的な手法でした。この手法は、データベースを短期的に使用し、後で内部設置型のプライマリ データベースにマージする場合、および、読み取り専用データを提供するソリューションの場合に有効です。

**フェデレーションを使用する**

SQL データベースのフェデレーションは、拡張性とパフォーマンスを高めるために使用されます。データベース内の 1 つ以上のテーブルを行単位で分割し、複数のデータベース (フェデレーション メンバー) に分配します。この種の OLE_LINK3OLE_LINK4 水平パーティション分割は、多くの場合、OLE_LINK3OLE_LINK4 "シャーディング" と呼ばれます。これが便利なのは、主として、拡張性とパフォーマンスを実現する必要がある場合、または容量を管理する必要がある場合です。

フェデレーションは Business Edition でサポートされています。詳細については、「[Azure SQL データベースでのフェデレーション][Azure SQL データベースでのフェデレーション]」および「[SQL Database Federations のチュートリアル - DBA][SQL Database Federations のチュートリアル - DBA]」を参照してください。

**その他の形態のストレージを検討する**

Azure では、テーブル ストレージや BLOB ストレージも含めて、複数の形態のデータ ストレージがサポートされていることを思い出してください。リレーショナル機能が必要でない場合は、テーブルまたは BLOB ストレージの方が経済的なこともあります。

<h2><a id="NextSteps"></a>次のステップ</h2>

これで、SQL データベース管理の基本を学習できました。さらに複雑な管理タスクを実行する方法については、次のリンク先を参照してください。

* MSDN の「[SQL データベース][SQL データベース]」を参照
* 「[SQL Database TechNet WIKI][SQL Database TechNet WIKI]」を参照



[Azure へのサインイン]: #PreReq1
[SQL データベースの構成]: #PreReq2
[Management Studio を使用した接続]: #PreReq3
[Azure へのデータベースのデプロイ]: #HowTo1
[ログインとユーザーの追加]: #HowTo2
[論理サーバーとデータベース インスタンスの監視]: #HowTo3
[SQL データベース ソリューションの規模の設定]: #HowTo4
[次のステップ]: #NextSteps

[SQL データベース]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg619386

[SQL Database TechNet WIKI]: http://social.technet.microsoft.com/wiki/contents/articles/2267.sql-azure-technet-wiki-articles-index-ja-jp.aspx

[SQL データベースの使用方法に関するページ]: http://www.windowsazure.com/ja-jp/develop/net/how-to-guides/sql-azure/
[Azure SQL データベースでのフェデレーション]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh597452.aspx
[SQL Database Federations のチュートリアル - DBA]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh778416.aspx
[SQL Server Management Studio を使用した SQL データベースの管理]: http://www.windowsazure.com/ja-jp/develop/net/common-tasks/sql-azure-management/
[動的管理ビューを使用した SQL データベースの監視]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ff394114.aspx
[Azure ストレージのジオ (主要地域) レプリケーションの概要に関するページ]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/introducing-geo-replication-for-windows-azure-storage.aspx
[Azure サブスクリプションのストレージ アカウントを作成する方法]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433066.aspx
[Azure SDK のダウンロードに関するページ]: http://www.microsoft.com/ja-jp/download/details.aspx?id=15658
[Azure 管理ツールに関するページ]: http://wapmmc.codeplex.com/
[SQL データベース管理の概要に関するページ]: http://www.windowsazure.com/ja-jp/manage/tutorials/sql-azure-management/  
[SQL データベースにおけるデータベースとログインの管理]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee336235.aspx
[BLOB ストレージ サービスの使用方法に関するページ]: https://www.windowsazure.com/ja-jp/develop/net/how-to-guides/blob-storage/
[DAC SQL Database Import Export Service Client v 1.5 に関するページ]: http://sqldacexamples.codeplex.com/releases/view/85948


[SQL データベース用の Adventure Works]: http://msftdbprodsamples.codeplex.com/releases/view/37304


