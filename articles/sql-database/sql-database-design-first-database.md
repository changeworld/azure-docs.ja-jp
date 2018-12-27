---
title: 'チュートリアル: SSMS を使用した最初の Azure SQL データベースの設計 | Microsoft Docs'
description: SQL Server Management Studio を使用して、最初の Azure SQL データベースを設計する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 9fa36b9b87a8e9591b0c863826cd2278a29ba28e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956059"
---
# <a name="tutorial-design-your-first-azure-sql-database-using-ssms"></a>チュートリアル: SSMS を使用した最初の Azure SQL データベースの設計

Azure SQL データベースは、Microsoft Cloud (Azure) のリレーショナルなサービスとしてのデータベース (DBaaS) です。 このチュートリアルでは、Azure Portal および [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) を使用して以下の操作を行う方法を学習します。

> [!div class="checklist"]
> * Azure Portal でデータベースを作成する*
> * Azure Portal でサーバーレベルのファイアウォール規則を設定する
> * SSMS を使用してデータベースに接続する
> * SSMS のテーブルを作成する
> * BCP を使用してデータを一括で読み込む
> * SSMS を使用してデータを照会する

*Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/)を作成してください。

> [!NOTE]
> このチュートリアルでは、[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)を使用していますが、[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)を選択することもできます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下がインストールされていることを確認してください。

- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (最新バージョン)
- [BCP と SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (最新バージョン)

## <a name="sign-in-to-the-azure-portal"></a>Azure ポータルにサインインします。

[Azure Portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-blank-database"></a>空のデータベースの作成

Azure SQL データベースは、定義済みの一連の[コンピューティング リソースとストレージ リソース](sql-database-service-tiers-dtu.md)を使って作成されます。 データベースは、[Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)内と [Azure SQL データベース論理サーバー](sql-database-features.md)内に作成されます。

空の SQL Database を作成するには、次の手順に従います。

1. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。

1. **[新規]** ページで、[Azure Marketplace] セクションで **[データベース]** を、**[おすすめ]** セクションで **[SQL Database]** をクリックします。

   ![空のデータベースを作成](./media/sql-database-design-first-database/create-empty-database.png)

   1. 前の画像で示されているように、**[SQL Database]** のフォームに次の情報を入力します。

      | Setting       | 推奨値 | 説明 |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **データベース名** | *yourDatabase* | 有効なデータベース名については、「[データベース識別子](/sql/relational-databases/databases/database-identifiers)」を参照してください。 |
      | **サブスクリプション** | *yourSubscription*  | サブスクリプションの詳細については、[サブスクリプション](https://account.windowsazure.com/Subscriptions)に関するページを参照してください。 |
      | **[リソース グループ]** | *yourResourceGroup* | 有効なリソース グループ名については、[名前付け規則と制限](/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 |
      | **[ソースの選択]** | 空のデータベース | 空のデータベースを作成するように指定します。 |

   1. **[サーバー]** をクリックして、既存のサーバーを使用するか、お客様のデータベース用に新しいサーバーを作成して構成します。 サーバーを選択するか、または **[新しいサーバーの作成]** をクリックして **[新しいサーバー]** フォームに次の情報を入力します。

      | Setting       | 推奨値 | 説明 |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **サーバー名** | グローバルに一意の名前 | 有効なサーバー名については、[名前付け規則と制限](/azure/architecture/best-practices/naming-conventions)に関するページを参照してください。 |
      | **サーバー管理者ログイン** | 有効な名前 | 有効なログイン名については、「[データベース識別子](/sql/relational-databases/databases/database-identifiers)」を参照してください。 |
      | **パスワード** | 有効なパスワード | パスワードには 8 文字以上が含まれていること、また、大文字、小文字、数字、英数字以外の文字のうち、3 つのカテゴリの文字が使用されていることが必要です。 |
      | **場所** | 有効な場所 | リージョンについては、「[Azure リージョン](https://azure.microsoft.com/regions/)」を参照してください。 |

      ![データベース サーバーの作成](./media/sql-database-design-first-database/create-database-server.png)

      **[選択]** をクリックします。

   1. **[価格レベル]** をクリックして、サービス レベル、DTU または仮想コア数、およびストレージの容量を指定します。 DTU または仮想コア数とストレージに関して、サービス レベルごとに利用できるオプションを確認してください。 既定では、**Standard** [DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)が選択されますが、[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)を選択することもできます。

      > [!IMPORTANT]
      > 現在、1 TB を超える Premium レベルのストレージは、英国北部、米国中西部、英国南部 2、中国東部、US DoD 中部、ドイツ中部、US DoD 東部、US Gov 南西部、US Gov 中南部、ドイツ北東部、中国北部、US Gov 東部を除くすべてのリージョンで使用できます。 それ以外のリージョンでは、Premium レベルのストレージの最大容量は 1 TB です。 [P11 ～ P15 の現時点での制限]( sql-database-dtu-resource-limits-single-databases.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb)に関するページを参照してください。

      サービス レベル、DTU 数、ストレージの容量を選択したら、**[適用]** をクリックします。

   1. 空のデータベースの**照合順序**を入力します (このチュートリアルでは既定値を使用)。 照合順序の詳細については、「[Collations (照合順序)](/sql/t-sql/statements/collations)」を参照してください。

1. これで **SQL Database** フォームの入力が完了したので、**[作成]** をクリックして、データベースをプロビジョニングします。 この手順には数分かかることがあります。

1. ツール バーの **[通知]** をクリックして、デプロイ プロセスを監視します。

     ![通知](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-firewall-rule"></a>ファイアウォール規則の作成

SQL データベース サービスでは、サーバーレベルでファイアウォールが作成されます。 ファイアウォールは、外部のアプリケーションとツールがサーバーやサーバー上のデータベースに接続することを防止します。 お客様のデータベースに外部から接続できるようにするには、まず、お客様の IP アドレスに対するルールをファイアウォールに追加する必要があります。 以下の手順に従って、[SQL データベース サーバー レベルのファイアウォール規則](sql-database-firewall-configure.md)を作成してください。

> [!NOTE]
> SQL データベースの通信は、ポート 1433 で行われます。 企業ネットワーク内から接続しようとしても、ポート 1433 での送信トラフィックがネットワークのファイアウォールで禁止されている場合があります。 その場合、お客様の管理者によってポート 1433 が開放されない限り、お客様の Azure SQL Database サーバーに接続することはできません。

1. デプロイが完了したら、左側のメニューから **[SQL データベース]** をクリックし、**SQL データベース** ページで、*yourDatabase* をクリックします。 お客様のデータベースの概要ページが開くと、完全修飾**サーバー名** (*yourserver.database.windows.net* など) や追加の構成オプションが表示されます。

1. この完全修飾サーバー名をコピーします。これは、SQL Server Management Studio からお客様のサーバーとデータベースに接続するために使用します。

   ![サーバー名](./media/sql-database-design-first-database/server-name.png)

1. ツール バーで **[Set server firewall]\(サーバー ファイアウォールの設定\)** をクリックします。 SQL データベース サーバーの **[ファイアウォール設定]** ページが開きます。

   ![サーバーのファイアウォール規則](./media/sql-database-design-first-database/server-firewall-rule.png)

   1. ツール バーの **[クライアント IP の追加]** をクリックし、現在の IP アドレスをファイアウォール規則に追加します。 ファイアウォール規則は、単一の IP アドレスまたは IP アドレスの範囲に対して、ポート 1433 を開くことができます。

   1. **[Save]** をクリックします。 論理サーバーでポート 1433 を開いている現在の IP アドレスに対して、サーバーレベルのファイアウォール規則が作成されます。

   1. **[OK]** をクリックし、**[ファイアウォール設定]** ページを閉じます。

これでお客様の IP アドレスがファイアウォールを通過できるようになりました。 SQL Server Management Studio や他の任意のツールを使用して SQL データベース サーバーとそのデータベースに接続することができます。 必ず、お客様が先ほど作成したサーバー管理者アカウントを使用してください。

> [!IMPORTANT]
> 既定では、すべての Azure サービスで、SQL データベース ファイアウォール経由のアクセスが有効になります。 すべての Azure サービスに対して無効にするには、このページの **[オフ]** をクリックします。

## <a name="connect-to-the-database"></a>データベースへの接続

[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) を使用して、お客様の Azure SQL データベース サーバーに対する接続を確立します。

1. SQL Server Management Studio を開きます。

1. **[サーバーへの接続]** ダイアログ ボックスで、次の情報を入力します。

   | Setting       | 推奨値 | 説明 |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **サーバーの種類** | データベース エンジン | この値は必須です。 |
   | **サーバー名** | 完全修飾サーバー名 | たとえば、*yourserver.database.windows.net* などです。 |
   | **認証** | パブリック | このチュートリアルで構成した認証の種類は "SQL 認証" のみです。 |
   | **ログイン** | サーバー管理者アカウント | サーバーの作成時に指定したアカウントです。 |
   | **パスワード** | サーバー管理者アカウントのパスワード | お客様がサーバーを作成したときに指定したパスワードです。 |

   ![[サーバーへの接続]](./media/sql-database-design-first-database/connect.png)

   1. **[サーバーへの接続]** ダイアログ ボックスの **[オプション]** をクリックします。 **[データベースへの接続]** セクションに「*yourDatabase*」と入力して、このデータベースに接続します。

      ![サーバー上のデータベースに接続](./media/sql-database-design-first-database/options-connect-to-db.png)  

   1. **[接続]** をクリックします。 SSMS で**オブジェクト エクスプローラー** ウィンドウが開きます。

1. **オブジェクト エクスプローラー**で、**Databases**、*yourDatabase* の順に展開して、サンプル データベース内のオブジェクトを表示します。

   ![データベース オブジェクト](./media/sql-database-design-first-database/connected.png)  

## <a name="create-tables-in-the-database"></a>データベースのテーブルを作成する

[Transact-SQL](/sql/t-sql/language-reference) を用いた大学の生徒管理システムを構成する、4 つのテーブルのデータベース スキーマを作成します。

- Person
- コース
- 生徒
- クレジット

次の図は、これらのテーブルの相互関係を示しています。 テーブルの一部は、他のテーブル内の列を参照します。 たとえば *Student* テーブルは、*Person* テーブルの *PersonId* 列を参照します。 このチュートリアルのテーブルの相互関係を把握するため、図を詳しく確認します。 効果的なデータベース テーブル作成方法の詳細は、[効果的なデータベース テーブルの作成](https://msdn.microsoft.com/library/cc505842.aspx)を参照してください。 データ型の選択については、[データ型](/sql/t-sql/data-types/data-types-transact-sql)を参照してください。

> [!NOTE]
> [SQL Server Management Studio のテーブル デザイナー](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools)を使用して、テーブルを作成および設計することも可能です。

![テーブルのリレーションシップ](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. **オブジェクト エクスプローラー**で *yourDatabase* を右クリックし、**[新しいクエリ]** を選択します。 データベースに接続された空のクエリ ウィンドウが開きます。

1. クエリ ウィンドウで次のクエリを実行し、データベース内の 4 つのテーブルを作成します。

   ```sql
   -- Create Person table
   CREATE TABLE Person
   (
       PersonId INT IDENTITY PRIMARY KEY,
       FirstName NVARCHAR(128) NOT NULL,
       MiddelInitial NVARCHAR(10),
       LastName NVARCHAR(128) NOT NULL,
       DateOfBirth DATE NOT NULL
   )

   -- Create Student table
   CREATE TABLE Student
   (
       StudentId INT IDENTITY PRIMARY KEY,
       PersonId INT REFERENCES Person (PersonId),
       Email NVARCHAR(256)
   )

   -- Create Course table
   CREATE TABLE Course
   (
       CourseId INT IDENTITY PRIMARY KEY,
       Name NVARCHAR(50) NOT NULL,
       Teacher NVARCHAR(256) NOT NULL
   )

   -- Create Credit table
   CREATE TABLE Credit
   (
       StudentId INT REFERENCES Student (StudentId),
       CourseId INT REFERENCES Course (CourseId),
       Grade DECIMAL(5,2) CHECK (Grade <= 100.00),
       Attempt TINYINT,
       CONSTRAINT [UQ_studentgrades] UNIQUE CLUSTERED
       (
           StudentId, CourseId, Grade, Attempt
       )
   )
   ```

   ![テーブルの作成](./media/sql-database-design-first-database/create-tables.png)

1. **オブジェクト エクスプローラー**で、*yourDatabase* の **Tables** ノードを展開すると、お客様が作成したテーブルが表示されます。

   ![作成済み SSMS テーブル](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>テーブルにデータを読み込む

1. お客様の Downloads フォルダーに *sampleData* という名前のフォルダーを作成し、お客様のデータベース用のサンプル データを格納します。

1. 次のリンクを右クリックし、それらを *sampleData* フォルダーに保存します。

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

1. コマンド プロンプト ウィンドウを開き、*sampleData* フォルダーに移動します。

1. 次のコマンドを実行して、サンプル データをテーブルに挿入します。*server*、*database*、*user*、*password* の各値は、お客様の環境の値に置き換えてください。
  
   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

これで、先ほど作成したテーブルにサンプル データが読み込まれました。

## <a name="query-data"></a>データのクエリを実行する

データベース テーブルから情報を取得するには、次のクエリを実行します。 SQL クエリの記述について詳しくは、[SQL クエリの記述](https://technet.microsoft.com/library/bb264565.aspx)に関するページを参照してください。 最初のクエリでは 4 つのテーブルをすべて結合し、"Dominick Pope" の指導を受けた生徒のうち、成績が 75% を超えている生徒を検索します。 次のクエリでは 4 つのテーブルをすべて結合し、"Noe Coleman" がこれまでに登録したコースを検索します。

1. SQL Server Management Studio のクエリ ウィンドウで、次のクエリを実行します。

   ```sql
   -- Find the students taught by Dominick Pope who have a grade higher than 75%
   SELECT  person.FirstName, person.LastName, course.Name, credit.Grade
   FROM  Person AS person
       INNER JOIN Student AS student ON person.PersonId = student.PersonId
       INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
       INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope'
       AND Grade > 75
   ```

1. クエリ ウィンドウで次のクエリを実行します。

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled
   SELECT  course.Name, course.Teacher, credit.Grade
   FROM  Course AS course
       INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
       INNER JOIN Student AS student ON student.StudentId = credit.StudentId
       INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
       AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、多数の基本的なデータベース タスクについて学習しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * データベースを作成する
> * ファイアウォール規則の設定
> * [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) を使用した、データベースへの接続
> * テーブルの作成
> * データの一括読み込み
> * データのクエリ実行

次のチュートリアルでは、Visual Studio と C# を使用したデータベースの設計について学びます。

> [!div class="nextstepaction"]
> [C# と ADO.NET で Azure SQL データベースを設計し、接続する](sql-database-design-first-database-csharp.md)
