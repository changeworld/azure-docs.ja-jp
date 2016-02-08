<properties
   pageTitle="Azure AD Connect sync: Generic SQL Connector | Microsoft Azure"
   description="この記事では、Microsoft の Generic SQL コネクタを構成する方法について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="01/21/2016"
   ms.author="andkjell"/>

# Generic SQL コネクタに関するテクニカル リファレンス

この記事では Generic SQL コネクタについて説明します。この記事は次の製品に適用されます。

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   4\.1.3461.0 以降の修正プログラム [KB2870703](https://support.microsoft.com/kb/2870703) を使用する必要があります。

MIM2016 および FIM2010R2 の場合、コネクタは [Microsoft ダウンロード センター](http://go.microsoft.com/fwlink/?LinkId=717495)からダウンロードして入手できます。

## Generic SQL コネクタの概要

Generic SQL コネクタを使用すると、ODBC 接続を提供するデータベース システムに、同期サービスを統合することができます。

包括的な観点から見た場合、コネクタの現在のリリースでサポートされている機能は次のとおりです。

| 機能 | サポート |
| --- | --- |
| 接続先のデータ ソース | コネクタは、すべての 64 ビット ODBC ドライバーでサポートされています。次のデータ ソースでテスト済みです。<li>Microsoft SQL Server および SQL Azure</li><li>IBM DB2 10.x</li><li>IBM DB2 9.x</li><li>Oracle 10 および 11 g</li><li>MySQL 5.x</li>
| シナリオ | <li>オブジェクトのライフサイクル管理</li><li>パスワード管理</li> |
| 操作 | <li>フル インポートと差分インポート、エクスポート</li><li>エクスポートの場合: 追加、削除、更新、および置換</li><li>パスワードの設定、パスワードの変更</li>
| スキーマ | <li>オブジェクトと属性の動的な検出</li>

### 前提条件

コネクタを使用する前に、前述した修正プログラムに加えて次のものが同期サーバーにインストールされていることを確認してください。

- Microsoft .NET 4.5.2 Framework 以降
- 64 ビット ODBC クライアント ドライバー

### 接続先データ ソースでのアクセス許可

Generic SQL コネクタでサポートされているタスクのどれかを作成または実行するには、次のものが必要です。

- db\_datareader
- db\_datawriter

### ポートとプロトコル

ODBC ドライバーが動作するために必要なポートについては、データベース ベンダーのドキュメントを参照してください。

## 新しいコネクタの作成

Generic SQL を作成するには、**[同期サービス]** で **[管理エージェント]** を選択し、**[作成]** を選択します。**Generic SQL (Microsoft)** コネクタを選択します。

![コネクタの作成](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### 接続

コネクタでは、接続のために ODBC DSN ファイルを使用します。**[管理ツール]** の [スタート] メニューで見つかった **[ODBC データ ソース]** を使用して DSN ファイルを作成します。管理ツールで、**ファイル DSN** を作成してコネクタに提供できるようにします。

![コネクタの作成](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

新しい Generic SQL コネクタを作成すると、[接続] 画面が最初に表示されます。まず次の情報を指定する必要があります。

- DSN ファイル パス
- 認証
    - ユーザー名
    - パスワード

データベースは、以下に説明する認証方法のいずれかをサポートする必要があります。

- **Windows 認証**: 認証データベースは、Windows 資格情報を使用してユーザーを検証します。この場合、同期サービスで使用するサービス アカウントが使用されます。このアカウントには、データベースへのアクセス許可が必要です。
- **SQL 認証**: 認証データベースでは、[接続] 画面で定義されたユーザー名/パスワードを使用してデータベースに接続します。DSN ファイルにユーザー名/パスワードを格納する場合、[接続] 画面で指定する資格情報には優先順位があります。
- **Azure SQL Database 認証**: 詳細については、「[Azure Active Directory 認証を使用して SQL Database に接続する](sql-database-aad-authentication.md)」を参照してください。

**DN はアンカー**: このオプションを選択した場合、DN はまた、アンカー属性として使用されます。これは、単純な実装に使用できますが次の制限事項があります。

-	コネクタでは、オブジェクトの種類を 1 つしかサポートしません。したがって、参照属性は、同じオブジェクトの種類しか参照できません。

**エクスポートの種類: オブジェクトの置換**: エクスポート中には、一部の属性しか変更されていなくても、オブジェクト全体がすべての属性を伴ってエクスポートされ、既存のオブジェクトに置き換わります。

### スキーマ 1 (オブジェクトの種類の検出)

このページでは、データベース内のさまざまなオブジェクトの種類をコネクタで検索する方法を構成します。

オブジェクトの種類はすべてパーティションとして表示され、**[パーティションの構成と階層]** でさらに構成できます。

![スキーマ1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**オブジェクトの種類の検出方法**: コネクタでは、次に示すオブジェクトの種類の検出方法をサポートしています。

- **固定値**: コンマ区切りの形式でオブジェクトの種類のリストを指定します。例: User,Group,Department。![スキーマ1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
- **テーブル/ビュー/ストアド プロシージャ**: テーブル/ビュー/ストアド プロシージャの名前を指定し、次にオブジェクトの種類の一覧を指定する列名を指定します。ストアド プロシージャを使用する場合は、さらに、そのパラメーターを **[Name]:[Direction]:[Value]** の形式で指定します。各パラメーターを個別の行に指定します (新しい行を取得するには、Ctrl + Enter キーを使用します)。![スキーマ1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
- **SQL クエリ**: このオプションでは、オブジェクトの種類を含んでいる 1 つの列を返す SQL クエリを指定できます。例: `SELECT [Column Name] FROM TABLENAME`返される列は、文字列型 (varchar) でなければなりません。

### スキーマ 2 (属性の型を検出する)

このページでは、属性の名前と型を検出する方法を構成します。前のページで検出されたすべてのオブジェクトについて構成オプションが一覧表示されます。

![スキーマ2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**属性の型の検出方法**: コネクタでは、[スキーマの 1] 画面で検出されたすべてのオブジェクトで属性の型を検出する以下の方法をサポートしています。

- **テーブル/ビュー/ストアド プロシージャ**: 属性の名前を検索する場合に使用する必要があるテーブル/ビュー/ストアド プロシージャの名前を指定します。ストアド プロシージャを使用する場合は、さらに、そのパラメーターを **[Name]:[Direction]:[Value]** の形式で指定します。各パラメーターを個別の行に指定します (新しい行を取得するには、Ctrl + Enter キーを使用します)。複数値の属性内で属性名を検出するには、テーブルまたはビューのコンマ区切りのリストを指定します。親テーブルと子テーブルの列名が同じである場合、複数値のシナリオはサポートされません。
- **SQL クエリ**: このオプションでは、属性名を含んでいる 1 つの列を返す SQL クエリを指定できます。例: `SELECT [Column Name] FROM TABLENAME`返される列は、文字列型 (varchar) でなければなりません。

### スキーマ 3 (アンカーと DN を定義する)

このページでは、検出されたオブジェクトの種類ごとにアンカーと DN 属性を構成することができます。複数の属性を選択することで、アンカーを一意にすることができます。

![スキーマ3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

- 複数値の属性とブール型の属性は表示されません。
- [接続] ページで **[DN はアンカー]** が選択されていない限り、DN とアンカーに対して同じ属性を使用することはできません。
- [接続] ページで **[DN はアンカー]** が選択されている場合、このページでは DN 属性のみが必要です。DN 属性は、アンカー属性としても使用されます。![スキーマ3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### スキーマ4 (属性の型、参照、および方向を定義する)

このページでは、属性ごとに属性の型 (整数、参照、文字列、バイナリ、またはブール) と方向を構成することができます。**[スキーマ 2]** ページからの属性がすべて (複数値の属性も含む)、一覧表示されます。

![スキーマ4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

- **DataType**: 属性の型を、同期エンジンで認識されている種類にマップするために使用します。既定では SQL スキーマで検出された型と同じ型を使用しますが、DateTime 型および参照型は簡単に検出されません。そのような場合は、**DateTime** または **参照** を指定する必要があります。
- **方向**: 属性の方向は Import、Export、または ImportExport に設定することができます。ImportExport が既定値です。![スキーマ4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

注:

- 属性の型がコネクタで検出不可能な型である場合、コネクタは文字列データ型を使用します。
- **入れ子になったテーブル**は、1 列のデータベース テーブルと見なすことができます。Oracle では、入れ子になったテーブルの行を任意の順序で格納します。ただし、入れ子になったテーブルを取得して PL/SQL 変数に入れる場合、行には 1 から始まる連続した添字が与えられます。これにより個々の行への配列様式のアクセスが可能になります。
- **VARRYS** はコネクタでサポートされていません。

### スキーマ 5 (参照属性のパーティションを定義する)

このページでは、すべての参照属性について、属性が参照するパーティション (すなわち、オブジェクトの種類) を構成します。

![スキーマ5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

**[DN はアンカー]** を使用している場合、参照元のオブジェクトの種類と同じ種類を使用する必要があります。別のオブジェクトの種類を参照することはできません。

### グローバル パラメーター

[グローバル パラメーター] ページでは、差分インポート、日付/時刻形式、およびパスワード方法を構成します。

![グローバルパラメーター1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)

Generic SQL コネクタでは、差分インポートの次の方法をサポートしています。

- **トリガー**: 「[トリガーを使用した差分ビューの生成](https://technet.microsoft.com/library/cc708665.aspx)」を参照してください。
- **透かし**: これは一般的な手法であり、任意のデータベースで使用できます。データベース ベンダーに基づいて透かしクエリがあらかじめ設定されています。使用するすべてのテーブル/ビューに、透かし列が存在する必要があります。この方法では、テーブルとその依存 (複数値または子) テーブルへの挿入および更新を追跡する必要があります。同期サービスとデータベース サーバーとの間で時間を同期させる必要があります。そうしないと、差分インポートの一部のエントリが省略される場合があります。制限事項:
    - 透かし手法では、削除されたオブジェクトはサポートしていません。
- **スナップショット** (Microsoft SQL Server でのみ動作)。「[Generating Delta Views Using Snapshots (スナップショットを使用した差分ビューの生成)](https://technet.microsoft.com/library/cc720640.aspx)」を参照してください。
- **変更の追跡** (Microsoft SQL Server でのみ動作)。「[変更の追跡について](https://msdn.microsoft.com/library/bb933875.aspx)」を参照してください。制限事項:
    - アンカー & DN 属性は、テーブル内の選択されたオブジェクトのプライマリ キーの一部を成している必要があります。
    - [変更の追跡] を使用したインポートおよびエクスポート中に、SQL クエリはサポートされていません。

**追加パラメーター**: データベース サーバーが置かれている場所を示すデータベース サーバーのタイム ゾーンを指定します。この値は、日付属性と時刻属性のさまざまな形式をサポートするために使用されます。

コネクタでは常に、日付と時刻を UTC 形式で格納します。日付と時刻を正しく変換できるようにするには、データベース サーバーのタイム ゾーンと、使用する形式を指定する必要があります。形式は .Net 形式で表現する必要があります。

エクスポート時には、日時の属性のすべてを UTC 時刻形式でコネクタに指定する必要があります。

![グローバルパラメーター2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**パスワード構成**: コネクタは、パスワード同期機能を備え、パスワードの設定および変更をサポートしています。

このコネクタでは、2 つの方法でパスワード同期をサポートしています。

- **ストアド プロシージャ**: この方法では、パスワードの設定および変更に対応するために 2 つのストアド プロシージャが必要です。次の例に示すように、[Set Password SP パラメーター] と [Change Password SP パラメーター] に、パスワードの追加操作およびパスワードの変更操作に必要なすべてのパラメーターを入力します。![グローバルパラメーター3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
- **パスワード拡張**: この方法ではパスワード拡張 DLL が必要です ([IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx) インターフェイスを実装する拡張 DLL 名を指定する必要があります)。コネクタが実行時に DLL を読み込むことができるように、拡張フォルダーにパスワード拡張アセンブリを配置する必要があります。![グローバルパラメーター4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

また、**[拡張の構成]** ページでパスワードの管理を有効にする必要があります。![グローバルパラメーター5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### パーティションと階層の構成

パーティションと階層のページでは、すべてのオブジェクトの種類を選択します。各オブジェクトの種類は、それぞれ独自のパーティション内にあります。

![パーティション1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

**[接続]** ページまたは **[グローバル パラメーター]** ページで定義されている値を上書きすることもできます。

![パーティション2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### アンカーの構成

アンカーは既に定義されているので、このページは読み取り専用です。選択したアンカー属性は常にオブジェクトの種類に付加され、それがオブジェクトの種類全体の中で確実に一意のままであるようにします。

![アンカー](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## 実行ステップ パラメーターの構成

次の手順は、コネクタの実行プロファイルで構成します。これらの構成によって、データのインポートおよびエクスポートの実際の作業が実行されます。

### フル インポートと差分インポート

Generic SQL コネクタでは、次の方法を使用したフル インポートと差分インポートをサポートしています。

- テーブル
- 表示
- ストアド プロシージャ
- SQL クエリ

![実行手順1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**テーブル/ビュー**

オブジェクトの複数値の属性をインポートするには、**[複数値のテーブル/ビューの名前]** にコンマ区切りのテーブル/ビュー名を指定し、**[結合条件]** に親テーブルとのそれぞれの結合条件を指定します。

例: Employee オブジェクトとそのすべての複数値属性をインポートします。Employee という名前のテーブル (メイン テーブル) と Department という名前のテーブル (複数値テーブル) があります。以下の手順を実行します。

- **[テーブル/ビュー/SP]** に「**Employee**」と入力します。
- **[複数値のテーブル/ビューの名前]** に「Department」と入力します。
- **[結合条件]** に Employee と Department の結合条件を入力します。たとえば、`Employee.DEPTID=Department.DepartmentID` のようになります。![実行手順2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**ストアド プロシージャ**

![実行手順3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

- データの量が大きい場合は、ストアド プロシージャを使用して、改ページ調整を実装することをお勧めします。
- ストアド プロシージャで改ページ調整をサポートするには、開始インデックスと終了インデックスを指定する必要があります。「[Efficiently Paging Through Large Amounts of Data (大量データの効率的なページング)](https://msdn.microsoft.com/library/bb445504.aspx)」を参照してください。
- @StartIndex と @EndIndex は実行時に、**[手順の構成]** ページで構成したそれぞれのページ サイズ値に置き換えられます。　例: コネクタが最初のページを取得し、このページのサイズが 500 に設定されているとします。このような状況では、@StartIndex は 1 で、@EndIndex は 500 と見なされます。これらの値は、コネクタが後続のページを取得するに従って大きくなり、@StartIndex 値と @EndIndex 値が変更されます。
- パラメーター化されたストアド プロシージャを実行するには、`[Name]:[Direction]:[Value]` 形式でパラメーターを指定します。各パラメーターを個別の行に入力します (新しい行を取得するには、Ctrl + Enter キーを使用します)。
- Generic SQL コネクタでは、分散環境からのインポート操作もサポートしています (たとえば、Microsoft SQL Server 内のリンク サーバー)。この場合、情報はリンク サーバー内のテーブルから取得され、テーブルは次の形式で提供されます。`[ServerName].[Database].[Schema].[TableName]`
    - 分散環境では、コネクタは Microsoft リンク サーバーのみをサポートします。
- Generic SQL コネクタでは、実行手順の情報とスキーマ検出との間で構造 (エイリアス名とデータ型の両方) が類似しているオブジェクトのみをサポートします。スキーマで選択したオブジェクトと、実行手順で指定した情報が異なる場合、SQL コネクタではそのようなシナリオをサポートできません。

**SQL クエリ**

![実行手順4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![実行手順5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

- 複数の結果セット クエリはサポートされていません。
- SQL クエリでは、改ページ調整をサポートしており、改ページ調整をサポートする変数として開始インデックスと終了インデックスを提供しています。

### 差分インポート

![実行手順6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

差分インポートの場合は、フル インポートのようなサポートされている他の方法と共に追加の構成が必要です。

- 方法としてトリガーまたはスナップショットを選択して差分変更を追跡する場合、ユーザーは **[履歴テーブルまたはスナップショットのデータベース名**] ボックスで履歴テーブルまたはスナップショットのデータベースを指定します。
- ユーザーはまた、履歴テーブルと親テーブルとの間の結合条件を指定する必要があります。例: `Employee.ID=History.EmployeeID`
- 履歴テーブルから親テーブルに対するトランザクションを追跡する場合、ユーザーは操作情報 (追加/更新/削除など) を含んでいる列名を指定する必要があります。
- 透かしで差分変更を追跡する場合、ユーザーは操作情報を含んでいる列名を **[透かし列名]** に指定する必要があります。これにより、コネクタは、差分インポートの実行中に、この列を検討することができます。
- 変更の種類については、**変更の種類の属性**列が必要です。この列により、プライマリ テーブルまたは複数値のテーブル内で発生した変更が、差分ビュー内の変更の種類にマップされます。この列には、属性レベルの変更に対しては Modify\_Attribute という変更の種類を含め、オブジェクト レベルの変更に対しては Add、Modify、または Delete という変更の種類を含めることができます。変更の種類が既定値の Add、Modify、または Delete 以外の種類である場合、ユーザーはこのオプションを使用してそれらの値を定義できます。

### エクスポート

![実行手順7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

Generic SQL コネクタでは、次に示す 4 つのサポートされた方法を使用したエクスポートをサポートしています。

- テーブル
- 表示
- ストアド プロシージャ
- SQL クエリ

**テーブル/ビュー**

ユーザーが [テーブル/ビュー] オプションを選択した場合、それぞれのクエリが生成され、エクスポートが実行されます。

**ストアド プロシージャ**

![実行手順8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

ユーザーが [ストアド プロシージャ] オプションを選択した場合、エクスポートには、挿入/更新/削除の各種操作を実行する 3 種類のストアド プロシージャが必要です。

- **Add SP Name (SP 名前の追加)**。この SP は、それぞれのテーブルで挿入を行うためにコネクタに任意のオブジェクトが渡された場合に実行されます。
- **Update SP Name (SP 名の更新)**。この SP は、それぞれのテーブルで更新を行うためにコネクタに任意のオブジェクトが渡された場合に実行されます。
- **Delete SP Name (SP 名の削除)**。この SP は、それぞれのテーブルで削除を行うためにコネクタに任意のオブジェクトが渡された場合に実行されます。
- ストアド プロシージャへのパラメーター値として使用されるスキーマから選択した属性です。例: EmployeeName: INPUT: @EmployeeName (コネクタのスキーマで EmployeeName が選択されると、コネクタはエクスポートの実行中にそれぞれの値を置換します)
- パラメーター化されたストアド プロシージャを実行するには、`[Name]:[Direction]:[Value]` 形式ですべてのパラメーターを指定します。各パラメーターを個別の行に入力します (新しい行を取得するには、Ctrl + Enter キーを使用します)。

**SQL クエリ**

![実行手順9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

ユーザーが [SQL クエリ] オプションを選択した場合、エクスポートには、挿入/更新/削除の各種操作を実行する 3 種類のクエリが必要です。

- **挿入クエリ**: このクエリは、それぞれのテーブルで挿入を行うためにコネクタに任意のオブジェクトが渡された場合に実行されます。
- **更新クエリ**: このクエリは、それぞれのテーブルで更新を行うためにコネクタに任意のオブジェクトが渡された場合に実行されます。
- **削除クエリ**: このクエリは、それぞれのテーブルで削除を行うためにコネクタに任意のオブジェクトが渡された場合に実行されます。
- クエリへのパラメーター値として使用されるスキーマから選択された属性です。例: `Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## トラブルシューティング

-	コネクタのトラブルシューティングを行うためにログ記録を有効にする方法については、「[How to Enable ETW Tracing for Connectors (コネクタの ETW トレースを有効にする方法)](http://go.microsoft.com/fwlink/?LinkId=335731)」を参照してください。

<!---HONumber=AcomDC_0128_2016-->