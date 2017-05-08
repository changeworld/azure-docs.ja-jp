---
title: "最初の Azure SQL Database の設計 | Microsoft Docs"
description: "最初の Azure SQL Database を設計する方法について説明します。"
services: sql-database
documentationcenter: 
author: janeng
manager: jstrauss
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 03/30/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 0d02954829ebac9275c014f7dac7e1ec423b0fc1
ms.lasthandoff: 04/21/2017


---

# <a name="design-your-first-azure-sql-database"></a>最初の Azure SQL Database の設計

このチュートリアルでは、生徒の成績とコース登録を追跡するための大学データベースを構築します。 このチュートリアルでは、[Azure ポータル](https://portal.azure.com/)と [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) を用いた、Azure SQL Database 論理サーバー上での Azure SQL データベース作成、データベースへのテーブル追加、テーブルへのデータ読み込み、データベース照会の方法を説明します。 また過去の特定時点にデータベースを復元する SQL Database の [ポイントインタイム リストア](sql-database-recovery-using-backups.md#point-in-time-restore)機能の使用方法も示します。

このチュートリアルを実行するには、最新バージョンの [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) をインストールしておく必要があります。 

## <a name="log-in-to-the-azure-portal"></a>Azure ポータルにログインする

[Azure ポータル](https://portal.azure.com/)にログインします。

## <a name="create-a-blank-sql-database-in-azure"></a>Azure で空の SQL データベースを作成する

Azure SQL データベースは、定義済みの一連の[コンピューティング リソースとストレージ リソース](sql-database-service-tiers.md)を使って作成されます。 データベースは、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)内と [Azure SQL Database 論理サーバー](sql-database-features.md)内に作成されます。 

空の SQL Database を作成するには、次の手順に従います。 

1. Azure Portal の左上隅にある **[新規]** ボタンをクリックします。

2. **[新規]** ページで **[データベース]** を選択し、**[データベース]** ページで **[SQL Database]** を選択します。 

    ![空のデータベースを作成](./media/sql-database-design-first-database/create-empty-database.png)

3. 前の画像で示されているように、[SQL Database] のフォームに次の情報を入力します。     

   - [データベース名]: **mySampleDatabase**
   - [リソース グループ]: **myResourceGroup**
   - [ソース]: **空のデータベース**

4. **[サーバー]** をクリックして、新しいデータベース用の新しいサーバーを作成して構成します。 **[新しいサーバー]** のフォームに、グローバルに一意なサーバー名を指定し、サーバー管理者ログインの名前を指定して、任意のパスワードを指定します。 

    ![データベース サーバーの作成](./media//sql-database-design-first-database/create-database-server.png)
5. **[選択]**をクリックします。

6. **[価格レベル]** をクリックして、新しいデータベースのサービス レベルとパフォーマンス レベルを指定します。 このチュートリアルでは、**20 DTU** と **250** GB のストレージを選択します。

    ![データベースの作成 -s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

7. **[適用]**をクリックします。  

8. **[作成]** をクリックしてデータベースをプロビジョニングします。 プロビジョニングの完了には 1 分 30 秒程度かかります。 

9. ツール バーの **[通知]** をクリックして、デプロイ プロセスを監視します。

    ![通知](./media/sql-database-get-started-portal/notification.png)


## <a name="create-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則を作成する

Azure SQL Database は、ファイアウォールによって保護されます。 既定では、サーバーとサーバー内部のデータベースに対する接続はすべて拒否されます。 クライアントの IP アドレスからの接続を許可できるよう、次の手順に従って [SQL Database サーバー レベルのファイアウォール規則](sql-database-firewall-configure.md)を作成します。 

1. デプロイが完了したら、左側のメニューから **[SQL データベース]** をクリックし、**[SQL データベース]** ページで、新しいデータベース **mySampleDatabase** をクリックします。 このデータベースの概要ページが開くと、完全修飾サーバー名 (**mynewserver-20170313.database.windows.net** など) や追加の構成オプションが表示されます。

      ![サーバーのファイアウォール規則](./media/sql-database-design-first-database/server-firewall-rule.png) 

2. 前の画像に示されているように、ツール バーの **[サーバー ファイアウォールの設定]** をクリックします。 SQL Database サーバーの **[ファイアウォール設定]** ページが開きます。 

3. ツールバーの **[クライアント IP の追加]** をクリックし、**[保存]** をクリックします。 現在の IP アドレスに対してサーバーレベルのファイアウォール規則が作成されます。

      ![サーバーのファイアウォール規則の設定](./media/sql-database-design-first-database/server-firewall-rule-set.png) 

4. **[OK]** をクリックしてから、**[X]** をクリックして **[ファイアウォール設定]** ページを閉じます。

これで、SQL Server Management Studio やその他の任意のツールを使用してデータベースとそのサーバーに接続できるようになりました。

> [!NOTE]
> SQL Database の通信は、ポート 1433 上で行われます。 企業ネットワーク内から接続しようとしても、ポート 1433 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、会社の IT 部門によってポート 1433 が開放されない限り、Azure SQL Database サーバーに接続することはできません。
>

## <a name="get-connection-information"></a>接続情報の取得

Azure Portal で、Azure SQL Database サーバーの完全修飾サーバー名を取得します。 その完全修飾サーバー名は、SQL Server Management Studio でのサーバーへの接続に使用します。

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. 左側のメニューから **[SQL データベース]** を選択し、**[SQL データベース]** ページで目的のデータベースをクリックします。 
3. そのデータベースの Azure Portal ページの **[要点]** ウィンドウで、**サーバー名**を見つけてコピーします。

    ![接続情報](./media/sql-database-connect-query-ssms/connection-information.png) 

## <a name="connect-to-your-database-using-sql-server-management-studio"></a>SQL Server Management Studio を使用してデータベースに接続する

[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) を使用して、Azure SQL Database サーバーに対する接続を確立します。

1. SQL Server Management Studio を開きます。

2. **[サーバーへの接続]** ダイアログ ボックスで、次の情報を入力します。
   - **[サーバーの種類]**: データベース エンジンを指定します
   - **[サーバー名]**: 完全修飾サーバー名を入力します (**mynewserver20170313.database.windows.net** など)
   - **[認証]**: SQL Server 認証を指定します
   - **[ログイン]**: サーバー管理者アカウントを入力します
   - **[パスワード]**: サーバー管理者アカウントのパスワードを入力します


   <img src="./media/sql-database-connect-query-ssms/connect.png" alt="connect to server" style="width: 780px;" />

3. **[サーバーへの接続]** ダイアログ ボックスの **[オプション]** をクリックします。 **[データベースへの接続]** セクションに「**mySampleDatabase**」と入力して、このデータベースに接続します。

   ![サーバー上のデータベースに接続](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. **[接続]**をクリックします。 SSMS でオブジェクト エクスプローラー ウィンドウが開きます。 

5. オブジェクト エクスプローラーで、**Databases** フォルダー、**mySampleDatabase** フォルダーの順に展開して、サンプル データベース内のオブジェクトを表示します。

   ![データベース オブジェクト](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="create-tables-in-the-database"></a>データベースのテーブルを作成する 

[Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) を用いた大学の生徒管理システムを構成する、4 つのテーブルのデータベース スキーマを作成します。

- Person
- コース
- 生徒
- 大学の生徒管理システムを構成するクレジット

次の図は、これらのテーブルの相互関係を示しています。 テーブルの一部は、他のテーブル内の列を参照します。 たとえば Student テーブルは、**Person** テーブルの **PersonId** 列を参照します。 このチュートリアルのテーブルの相互関係を把握するため、図を詳しく確認します。 効果的なデータベース テーブル作成方法の詳細は、[効果的なデータベース テーブルの作成](https://msdn.microsoft.com/library/cc505842.aspx)を参照してください。 データ型の選択については、[データ型](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql)を参照してください。

> [!NOTE]
> [SQL Server Management Studio のテーブル デザイナー](https://msdn.microsoft.com/library/hh272695.aspx)を使用して、テーブルを作成および設計することも可能です。 

![テーブルのリレーションシップ](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. オブジェクト エクスプローラーで **mySampleDatabase** を右クリックし、**[新しいクエリ]** をクリックします。 データベースに接続された空のクエリ ウィンドウが開きます。

2. クエリ ウィンドウで次のクエリを実行し、データベース内の 4 つのテーブルを作成します。 

   ```sql 
   -- Create Person table

    CREATE TABLE Person
    (
      PersonId      INT IDENTITY PRIMARY KEY,
      FirstName     NVARCHAR(128) NOT NULL,
      MiddelInitial NVARCHAR(10),
      LastName      NVARCHAR(128) NOT NULL,
      DateOfBirth   DATE NOT NULL
    )
   
   -- Create Student table
 
    CREATE TABLE Student
    (
      StudentId INT IDENTITY PRIMARY KEY,
      PersonId  INT REFERENCES Person (PersonId),
      Email     NVARCHAR(256)
    )
    
   -- Create Course table
 
    CREATE TABLE Course
    (
      CourseId  INT IDENTITY PRIMARY KEY,
      Name      NVARCHAR(50) NOT NULL,
      Teacher   NVARCHAR(256) NOT NULL
    ) 

   -- Create Credit table
 
    CREATE TABLE Credit
    (
      StudentId   INT REFERENCES Student (StudentId),
      CourseId    INT REFERENCES Course (CourseId),
      Grade       DECIMAL(5,2) CHECK (Grade <= 100.00),
      Attempt     TINYINT,
      CONSTRAINT  [UQ_studentgrades] UNIQUE CLUSTERED
      (
        StudentId, CourseId, Grade, Attempt
      )
    )
   ```

![テーブルの作成](./media/sql-database-design-first-database/create-tables.png)

3. SQL Server Management Studio オブジェクト エクスプローラーで 'テーブル' ノードを展開し、作成したテーブルを表示します。

   ![作成済み SSMS テーブル](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>テーブルにデータを読み込む

1. Downloads フォルダーに **SampleTableData** という名前のフォルダーを作成し 、データベースのサンプル データを格納します。 

2. 次のリンクを右クリックし、データを **SampleTableData** フォルダーに保存します。 

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. コマンド プロンプト ウィンドウを開き、SampleTableData フォルダーに移動します。

4. 次のコマンドを実行し、テーブルにサンプル データを挿入することで、**ServerName**、**DatabaseName**、**UserName**、**Password** の値を環境値に置き換えます。
  
   ```bcp
   bcp Course in SampleCourseData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

これで、先ほど作成したテーブルにサンプル データが読み込まれました。

## <a name="query-the-tables"></a>テーブルを照会する

データベース テーブルから情報を取得するには、次のクエリを実行します。 SQL クエリの記述に関する詳細は、[SQL クエリの記述](https://technet.microsoft.com/library/bb264565.aspx)を参照してください。 最初のクエリでは 4 つのテーブルをすべて結合し、'Dominick Pope' のクラスで 75% 以上の成績の生徒をすべて検索し ます。 次のクエリでは 4 つのテーブルをすべて結合し、'Noe Coleman' がこれまでに登録したコースをすべて検索します。

1. SQL Server Management Studio のクエリ ウィンドウで、次のクエリを実行します。

   ```sql 
   -- Find the students taught by Dominick Pope who have a grade higher than 75%

    SELECT  person.FirstName,
        person.LastName,
        course.Name,
        credit.Grade
    FROM  Person AS person
        INNER JOIN Student AS student ON person.PersonId = student.PersonId
        INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
        INNER JOIN Course AS course ON credit.CourseId = course.courseId
    WHERE course.Teacher = 'Dominick Pope' 
        AND Grade > 75
   ```

2. SQL Server Management Studio のクエリ ウィンドウで、次のクエリを実行します。

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled

    SELECT  course.Name,
        course.Teacher,
        credit.Grade
    FROM  Course AS course
        INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
        INNER JOIN Student AS student ON student.StudentId = credit.StudentId
        INNER JOIN Person AS person ON person.PersonId = student.PersonId
    WHERE person.FirstName = 'Noe'
        AND person.LastName = 'Coleman'
   ```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>データベースを以前の状態に復元する 

テーブルを誤って削除した場合を想定してください。 データの復元は容易なことではありません。 Azure SQL Database では、過去最長 35 日間における任意の時点に戻り、新しいデータベースに過去のデータを復元することができます。 このデータベースを用いることで、削除済みデータの復元が可能です。 次の手順を実行して、テーブルが追加される前の状態にサンプル データベースを復元します。

1. データベースの SQL Database ページで、ツール バーの **[復元]** をクリックします。 **[復元]** ページが開きます。

   ![復元](./media/sql-database-design-first-database/restore.png)

2. **[復元]** フォームに必要な情報を入力します。
    * [データベース名]: データベース名を指定します 
    * 特定の時点: [復元] フォームの **[特定の時点]** タブを選択します。 
    * 復元ポイント: データベースを変更する前の時間を選択します。
    * 対象サーバー: データベースを復元するときは、この値を変更することはできません。 
    * エラスティック データベース プール: **[なし]** を選択します。  
    * 価格レベル: **20 DTU** および **250** GB のストレージを選択します。

   ![復元ポイント](./media/sql-database-design-first-database/restore-point.png)

3. **[OK]** をクリックして、[テーブルが追加される前の状態にデータベースを復元](sql-database-recovery-using-backups.md#point-in-time-restore)します。 異なる時点にデータベースを復元すると、[サービス レベル](sql-database-service-tiers.md)の保有期間内であれば、指定した時点の元データベースと同じサーバー内に、データベースが複製されます。

## <a name="next-steps"></a>次のステップ 

一般的なタスクの PowerShell サンプルについては、[SQL Database の PowerShell サンプル](sql-database-powershell-samples.md)に関するページをご覧ください。

