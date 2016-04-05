<properties
   pageTitle="Azure Data Catalog データ カタログの概要"
   description="Azure Data Catalog のシナリオと機能のエンド ツー エンドのチュートリアルです。"
   documentationCenter=""
   services="data-catalog"
   authors="dvana"
   manager="mblythe"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="03/03/2016"
   ms.author="derrickv"/>

# Azure Data Catalog の概要

この記事は、**Azure Data Catalog** の機能とエンド ツー エンドのシナリオを紹介します。サービスにサインアップしたら、以下の手順に従って、データ カタログを作成し、データソースの登録、注釈の設定、検出を行います。

## チュートリアルの前提条件

このチュートリアルを始める前に、次の項目を用意する必要があります。

- **Azure サブスクリプション** - サブスクリプションがない場合は、無料試用版アカウントを数分で作成することができます。詳細については、「[1 か月間の無料試用版](https://azure.microsoft.com/pricing/free-trial/)」を参照してください。
- **Azure Active Directory** - Azure Data Catalog では、ID およびアクセス管理に [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) を使用します。
- **データ ソース** - Azure Data Catalog には、データ ソースを検出する機能があります。このチュートリアルでは、Adventure Works サンプル データベースを使用しますが、馴染みがあり、自分のロールに関連するデータを操作したいと考える場合は、任意のサポートされているデータ ソースも使用できます。サポートされているデータ ソースの一覧については、「[Azure Data Catalog でサポートされるデータ ソース](data-catalog-dsr.md)」を参照してください。

それでは、Adventure Works サンプル データベースのインストールを開始しましょう。

## 演習 1: Adventure Works サンプル データベースのインストール

この演習では、以降の演習で使用する、SQL Server データベース エンジンの Adventure Works サンプルをインストールします。

### Adventure Works 2014 OLTP データベースのインストール

Adventure Works データベースは、製品、販売、および購入を含む、架空の自転車メーカー (Adventure Works Cycles) の標準オンライン トランザクション処理シナリオをサポートします。このチュートリアルでは、製品に関する情報を **Azure Data Catalog** に登録します。

Adventure Works サンプル データベースのインストール方法を次に示します。

Adventure Works サンプル データベースをインストールするには、CodePlex の [Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) にある AdventureWorks2014 バックアップを復元します。データベースを復元する 1 つの方法として、SQL Server Management Studio で T-SQL スクリプトを実行します。

**T-SQL スクリプトを使用して Adventure Works サンプル データベースをインストールする**

1.	C:\\DataCatalog\_GetStarted という名前のフォルダーを作成します。別のフォルダー名を使用する場合は、後述の T-SQL スクリプトのパスを必ず変更してください。
2.	[Adventure Works 2014 Full Database Backup.zip](https://msftdbprodsamples.codeplex.com/downloads/get/880661) をダウンロードします。
3.	Adventure Works 2014 Full Database Backup.zip を C:\\DataCatalog\_GetStarted に展開します。下のスクリプトでは、バックアップ ファイルが C:\\DataCatalog\_GetStarted\\Adventure Works 2014 Full Database Backup\\AdventureWorks2014.bak にあることを前提としています。
4.	**SQL Server Management Studio** で、**標準**ツール バーの **[新しいクエリ]** をクリックします。
5.	クエリ ウィンドウで、次の T-SQL コードを実行します。

**次のスクリプトを実行して Adventure Works 2014 データベースをインストールする**

    USE [master]
    GO
    -- NOTE: This script is for sample purposes only. The default backup file path for this script is C:\DataCatalog_GetStarted. To run this script, create the default file path or change the file path, and copy AdventureWorks2014.bak into the path.

    -- IMPORTANT: In a production application, restore a SQL database to the data folder for your SQL Server instance.

    RESTORE DATABASE AdventureWorks2014
    	-- AdventureWorks2014.bak file location
    	FROM disk = 'C:\DataCatalog_GetStarted\Adventure Works 2014 Full Database Backup\AdventureWorks2014.bak'

    	-- AdventureWorks2014.mdf database location
    	WITH MOVE 'AdventureWorks2014_data' TO 'C:\DataCatalog_GetStarted\AdventureWorks2014.mdf',

    	-- AdventureWorks2014.ldf log location
    	MOVE 'AdventureWorks2014_Log' TO 'C:\DataCatalog_GetStarted\AdventureWorks2014.ldf'
    ,REPLACE
    GO

T-SQL スクリプトを実行する代わりに、SQL Server Management Studio を使用してデータベースを復元することもできます。「[データベースのバックアップの復元 (SQL Server Management Studio)](http://msdn.microsoft.com/library/ms177429.aspx)」を参照してください。

この演習では、残りの演習で使用される Adventure Works サンプル データベースをインストールしました。次の演習では、Adventure Works サンプル データベース内のテーブルから **Azure Data Catalog** データ ソースを登録する方法について説明します。

## 演習 2: データ ソースの登録

この演習では、**Azure Data Catalog** 登録ツールを使用して、Adventure Works データ ソースをカタログに登録します。登録は、データ ソースおよびそれに格納されている資産から、名前、型、および場所などの重要な構造メタデータを抽出し、そのメタデータをカタログにコピーするプロセスです。データ ソースとそれらのデータはそのままの場所に残りますが、カタログによって、それらが簡単に検出および理解できるようにするために、メタデータが使われます。

### ここにデータ ソースを登録する方法を示します

1.	https://azure.microsoft.com/services/data-catalog にアクセスし、**[はじめに]** をクリックします。
2.	**Azure Data Catalog** ポータルにログインし、**[データの発行]** をクリックします。

    ![](media/data-catalog-get-started/data-catalog-publish-data.png)

3.	[**アプリケーションの起動**] をクリックします。

    ![](media/data-catalog-get-started/data-catalog-launch-application.png)

4. [**ようこそ**] ページで、[**サインイン**] をクリックし、資格情報を入力します。
5. **[Microsoft Azure Data Catalog]** ページで、**[SQL Server]** をダブルクリックするか、**[SQL Server]** をクリックして **[次へ]** をクリックします。

    ![](media/data-catalog-get-started/data-catalog-data-sources.png)

6.	AdventureWorks2014 の SQL Server 接続のプロパティを入力し (以下の例を参照)、**[接続]** をクリックします。

    ![](media/data-catalog-get-started/data-catalog-sql-server-connection.png)

7.	次のページで、データ ソースのメタデータを登録します。この例では、AdventureWorks Production 名前空間から **Production/Product** オブジェクトを登録します。これを実行する方法を次に示します。

    a.**[サーバー階層]** のツリーで、**[Production]** をクリックします。

    ![](media/data-catalog-get-started/data-catalog-server-hierarchy.png)

    b.Ctrl キーを押しながら、[Product]、[ProductCategory]、[ProductDescription]、および [ProductPhoto] をクリックします。

    c.選択項目の移動矢印 (**>**) をクリックします。これにより、選択した Product オブジェクトすべてが **[登録されるオブジェクト]** の一覧に移動します。

    ![](media/data-catalog-get-started/data-catalog-available-objects.png)

    d.[**タグの追加**] に、説明の photo を入力します。これにより、これらのデータ資産に検索タグが追加されます。タグは、ユーザーが登録されているデータ ソースを見つけるために役立つ優れた方法です。

    ![](media/data-catalog-get-started/data-catalog-objects-register.png)

    e.**省略可能**: [**プレビューを含める**] ことができ、[**データ ソース エキスパートを追加**] することができます。

    f.[**登録**] をクリックします。**Azure Data Catalog** では、選択したオブジェクトが登録されます。この演習では、Adventure Works から選択したオブジェクトが登録されます。

    g.登録されているデータ ソース オブジェクトを表示するには、[**ポータルの表示**] をクリックします。**Azure Data Catalog** ポータルでは、データ ソース オブジェクトを **[タイル]** または **[リスト]** で表示できます。

    ![](media/data-catalog-get-started/data-catalog-view-portal.png)

この演習では、組織全体でユーザーがオブジェクトを簡単に検出できるように、Adventure Works サンプル データベースからオブジェクトを登録しました。次の演習では、登録されているデータ資産を検出する方法を学習します。

## 演習 3: 登録されているデータ資産の検出

この演習では、**Azure Data Catalog** ポータルを使用して、登録されているデータ資産を検出し、それらのメタデータを表示します。**Azure Data Catalog** は、単純なキーワード検索、対話型フィルター、および "パワー" ユーザー向けの詳細検索構文など、データ資産を検出するための多数のツールを備えています。

### 次に、登録されているデータ資産を検出する方法を示します

**Azure Data Catalog** には、簡単ながら強力な検索構文が備わっており、これを使用すると、ユーザーが必要とするデータを返すクエリを簡単に構築できます。**Azure Data Catalog** の検索の詳細については、「[Data Catalog Search syntax reference (Data Catalog の検索構文のリファレンス)](https://msdn.microsoft.com/library/azure/mt267594.aspx)」を参照してください。

**Azure Data Catalog** には次の検索オプションがあります。

- キーワード検索
- フィルター
- 詳細検索

表示するデータ資産を絞り込むこともできます。**Azure Data Catalog** には次の表示オプションがあります。

- プロパティの表示
- 列の表示
- プレビューの表示

この例では、キーワード検索を使用します。**Azure Data Catalog** の検索には、いくつかのクエリ手法があります。この例では、**グループ化**検索クエリを使用します。

**クエリ手法**

|手法|最初の起動時にドメインに参加しているマシンになるように VM をプロビジョニングするには、|例
|---|---|---
|プロパティ スコープ|指定したプロパティの検索語句と一致する場合にのみデータ ソースを返します。|name:product
|論理演算子|このページのブール演算子に関するセクションで説明されているように、ブール演算子を使用して検索の範囲を広げたり狭めたりします。|finance NOT corporate
|かっこを使用したグループ化|かっこを使用してクエリの一部をグループ化し、論理的に分離します。特にブール演算子と組み合わせて使用します。|name:product AND (tags:illustration OR tags:photo)
|比較演算子|数値データ型および日付データ型を持つプロパティについて、等値演算子以外の比較演算子を使用します。|creationTime:>11/05/14

この例では、名前が product に等しく、タグが illustration に等しいか、またはタグが photo に等しいデータ資産の**グループ化**検索を実行します。

1. https://azure.microsoft.com/services/data-catalog にアクセスし、**[はじめに]** をクリックして、**Azure Data Catalog** ポータルにログインします。
2. **[Data Catalog の検索]** ボックスに、**グループ化**クエリ「(tags:description OR tags:photo)」と入力します。
3. 検索アイコンをクリックするか、Enter キーを押します。**Azure Data Catalog** によって、この検索クエリに対応するデータ資産が表示されます。

    ![](media/data-catalog-get-started/data-catalog-search-box.png)

この演習では、**Azure Data Catalog** ポータルを使用して、カタログに登録されている Adventure Works データ資産を検出し、表示しました。

<a name="annotating"/>
## 演習 4: 登録されているデータ ソースへの注釈の設定

この演習では、**Azure Data Catalog** ポータルを使用して、カタログに以前に登録されたデータ資産に注釈を設定します。指定する注釈は、登録時にデータ ソースから抽出された構造型メタデータを補完、拡張して、データ資産をさらに検出および理解しやすくします。各 **Data Catalog** ユーザーは独自の注釈を指定できるため、データに関して、ある観点を持つすべてのユーザーがそれを簡単に共有できます。

### 次に、データ資産に注釈を設定する方法を示します

1. https://azure.microsoft.com/services/data-catalog にアクセスし、**[はじめに]** をクリックして、**Azure Data Catalog** ポータルにログインします。
2. [**探索**] をクリックします。
3. 1 つまたは複数の**データ資産**を選択します。この例では、[**ProductPhoto**] を選択し、「Product photos for marketing materials」と入力します。
4. **説明**を入力すると、他のユーザーが、選択したデータ資産を検出したり、そのデータ資産を使用する理由と方法を理解したりするのに役立ちます。たとえば、「Product images」と入力します。さらに、タグとビュー列を追加することもできます。
5. これで、カタログに追加したわかりやすいメタデータを使用して、データ資産を検出するための検索とフィルター処理を試行できます。

    ![](media/data-catalog-get-started/data-catalog-annotate.png)

この演習では、登録されているデータ資産にわかりやすい情報を追加し、カタログ ユーザーが理解できる用語を使用してデータ ソースを検出できるようにしました。

## 演習 5: メタデータのクラウドソーシング

この演習では、別のユーザーと協力して、カタログ内のデータ資産にメタデータを追加します。**Azure Data Catalog** の注釈のクラウドソーシング アプローチにより、すべてのユーザーがタグ、説明、およびその他のメタデータを追加できるため、データ資産とその使用に関してある観点を持つ任意のユーザーは、その観点を他のユーザーがキャプチャし、使用できるようにすることができます。

> [AZURE.NOTE] 別のユーザーがこのチュートリアルで練習していなくても、心配しないでください。 データ カタログにアクセスするすべてのユーザーが、選択に応じて、独自の観点を追加できます。このメタデータのクラウドソーシング アプローチにより、カタログの内容と、カタログのメタデータの豊かさは時間とともに増加します。

### 次に、データ資産に関するメタデータをクラウドソーシングする方法を示します

同僚に、先述の「[登録されているデータ ソースへの注釈の設定](#annotating)」の演習を繰り返すように求めます。同僚が ProductPhoto など、データ資産に説明を追加すると、複数の注釈が表示されます。

![](media/data-catalog-get-started/data-catalog-crowdsource.png)

この演習では、**Azure Data Catalog** のメタデータのクラウドソーシングのための機能について学習しました。カタログ ユーザーは検出したデータ資産に注釈を設定できます。

## 演習 6: データ ソースへの接続

この演習では、**Azure Data Catalog** ポータルで、Microsoft Excel を使用してデータ ソースに接続します。

> [AZURE.NOTE] **Azure Data Catalog** は、ユーザーに実際のデータ ソースへのアクセス権を付与するのではなく、単にユーザーがそれらを検出し、理解しやすくするだけであることに注意することが重要です。ユーザーがデータ ソースに接続すると、選択したクライアント アプリケーションが、ユーザーの Windows 資格情報を使用するか、または必要に応じて、ユーザーに資格情報の入力を求めます。ユーザーに以前にデータ ソースへのアクセス権が付与されていない場合、データ ソースに接続する前に、アクセス権を付与される必要があります。

### 次に、Excel からデータ ソースに接続する方法を示します

1. https://azure.microsoft.com/services/data-catalog にアクセスし、**[はじめに]** をクリックして、**Azure Data Catalog** ポータルにログインします。
2. [**探索**] をクリックします。
3. データ資産を選択します。この例では ProductCategory を選択します。
4. [**開く**] > [**Excel**] を選択します。

    ![](media/data-catalog-get-started/data-catalog-connect1.png)

5. [**Microsoft Excel のセキュリティに関する通知**] ウィンドウで、[**有効にする**] をクリックします。
6. **ProductCategory.odc** ファイルを開きます。
7. データ ソースが Excel で開きます。

    ![](media/data-catalog-get-started/data-catalog-connect2.png)

この演習では、**Azure Data Catalog** を使用して検出されたデータ ソースに接続しました。**Azure Data Catalog** ポータルでは、ユーザーがその [**開く**] メニューに統合されたクライアント アプリケーションを使用して、直接接続することも、選択した任意のアプリケーションを使用し、資産のメタデータに含まれる接続の場所情報を使用して、接続することもできます。

## 演習 7: データ ソースのメタデータの削除

この演習では、**Azure Data Catalog** ポータルを使用して、登録されているデータ資産からプレビュー データを削除し、カタログからデータ資産を削除します。

> [AZURE.NOTE] カタログの既定の動作は、すべてのユーザーが任意のデータ ソースを登録でき、すべてのユーザーが登録されている任意のデータ資産を削除できることです。**Azure Data Catalog の Standard Edition** に含まれる管理機能では、資産の所有権の取得、資産を検出できるユーザーの制限、および資産を削除できるユーザーの制限のための追加のオプションがあります。

**Azure Data Catalog** では、個別の資産を削除したり、複数の資産を削除したりできます。

### 次に、複数のデータ資産を削除する方法を示します

1. https://azure.microsoft.com/services/data-catalog にアクセスし、**[はじめに]** をクリックして、**Azure Data Catalog** ポータルにログインします。
2. [**探索**] をクリックします。
3. 1 つまたは複数のデータ資産を選択します。
4. [**削除**] をクリックします。

この演習では、登録されているデータ資産をカタログから削除しました。

## 演習 8: 登録されているデータ ソースの管理

この演習では、**Azure Data Catalog** の管理機能を使用して、データ資産の所有権を取得し、ユーザーが検出できる資産とユーザーによるそれらの資産の管理方法を制御します。

> [AZURE.NOTE] この演習で説明している管理機能は、**Azure Data Catalog の Standard Edition** でのみ使用できます。**Free Edition** では使用できません。**Azure Data Catalog** では、データ資産の所有権を取得し、共同所有者をデータ資産に追加し、データ資産の可視性を設定できます。

### 次に、データ資産の所有権を取得し、可視性を制限する方法を示します

1. https://azure.microsoft.com/services/data-catalog にアクセスし、**[はじめに]** をクリックして、**Azure Data Catalog** ポータルにログインします。
2. [**探索**] をクリックします。
3. 1 つまたは複数のデータ資産を選択します。
4. [**プロパティ**] パネルの [**管理**] セクションで、[**所有権の取得**] をクリックします。
5. 可視性を制限するには、[**所有者とこれらのユーザー**] をクリックします。

    ![](media/data-catalog-get-started/data-catalog-ownership.png)

この演習では、**Azure Data Catalog** の管理機能について学習し、選択したデータ資産の可視性を制限しました。

## 概要

このチュートリアルでは、**Azure Data Catalog** のエンタープライズ データ ソースの登録、注釈、検出、および管理を含む重要な機能について学習しました。これでチュートリアルは完了したので、使用を開始できます。自分やチームが依存するデータ ソースを登録し、カタログを使用する同僚を招待することによって、今すぐ始めることができます。

<!---HONumber=AcomDC_0330_2016-->