---
title: Azure Data Lake Analytics コードをテストする方法 | Microsoft Docs
description: Azure Data Lake Analytics 用の U-SQL および拡張 C# コードのテスト ケースを追加する方法について説明します。
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: fa5c113541452a93c25adc7c14bdaa6994434c71
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365862"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Azure Data Lake Analytics コードのテスト

Azure Data Lake は、あらゆる規模のデータを処理するために、宣言型の SQL と命令型の C# を組み合わせた U-SQL 言語を提供します。 このドキュメントでは、U-SQL および拡張 C# UDO (ユーザー定義演算子) コードのテスト ケースを作成する方法について説明します。

## <a name="test-u-sql-scripts"></a>U-SQL スクリプトのテスト

U-SQL スクリプトは実行可能コードにコンパイルおよび最適化され、クラウド上の複数のマシンにまたがって、またはローカル コンピューター上で実行されます。 コンパイルと最適化のプロセスは、U-SQL スクリプト全体を全体として扱います。 すべてのステートメントについて従来の "単体テスト" を行うことはできません。 ただし、U-SQL テスト SDK とローカル実行 SDK を使用することによって、スクリプトレベルのテストを行うことができます。

### <a name="create-test-cases-for-u-sql-script"></a>U-SQL スクリプトのテスト ケースの作成

Azure Data Lake Tools for Visual Studio では、U-SQL スクリプトのテスト ケースを作成できます。

1.  ソリューション エクスプローラーで U-SQL スクリプトを右クリックし、**[Create Unit Test]\(単体テストの作成\)** を選択します。
2.  新しいテスト プロジェクトを作成するか、既存のテスト プロジェクトにテスト ケースを挿入します。

    ![Data Lake Tools for Visual Studio - U-SQL テスト プロジェクトを作成する](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project.png) 

    ![Data Lake Tools for Visual Studio - U-SQL テスト プロジェクトの構成を作成する](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png) 

### <a name="manage-the-test-data-source"></a>テスト データ ソースの管理

U-SQL スクリプトをテストするときは、テスト入力ファイルが必要です。 U-SQL プロジェクトのプロパティで **[Test Data Source]\(テスト データ ソース\)** を構成することによって、これらのテスト データを管理できます。 

U-SQL テスト SDK で `Initialize()` インターフェイスを呼び出すと、一時ローカル データ ルートフォルダーがテスト プロジェクトの作業ディレクトリ下に作成され、U-SQL スクリプトのテスト ケースを実行する前に、テスト データ ソース フォルダー内のすべてのファイルとサブフォルダー(および、サブフォルダー下のファイル) が一時ローカル データ ルート フォルダーにコピーされます。 テスト データ フォルダーのパスをセミコロンで区切ることによって、テスト データ ソース フォルダーをさらに追加できます。

![Data Lake Tools for Visual Studio - プロジェクトのテスト データ ソースを構成する](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-the-database-environment-for-testing"></a>テスト用データベース環境の管理

ストアド プロシージャの呼び出し時など、U-SQL スクリプトで U-SQL データベース オブジェクトを使用または照会する場合、U-SQL テスト ケースを実行する前にデータベース環境を初期化する必要があります。 U-SQL テスト SDK の `Initialize()` インターフェイスは、U-SQL プロジェクトによって参照されているすべてのデータベースを、テスト プロジェクトの作業ディレクトリ内の一時ローカル データ ルート フォルダーにデプロイするのに役立ちます。 

[U-SQL プロジェクトの U-SQL データベース プロジェクト参照を管理する方法](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)も確認してください。

### <a name="verify-test-results"></a>テスト結果の検証

`Run()` インターフェイスは、ジョブの実行結果を返します。 0 は成功を意味し、1 は失敗を意味します。 C# Assert 関数を使用して出力を検証することもできます。 

### <a name="run-test-cases-in-visual-studio"></a>Visual Studio でのテスト ケースの実行

U-SQL スクリプトのテスト プロジェクトは、C# 単体テスト フレームワークの上に構築されます。 プロジェクトをビルドした後、**テスト エクスプローラーの [プレイリスト]** からすべてのテスト ケースを実行できます。 または、.cs ファイルを右クリックし、**[テストの実行]** を選択します。

## <a name="test-c-udos"></a>C# UDO のテスト

### <a name="create-test-cases-for-c-udos"></a>C# UDO のテスト ケースの作成

C# 単体テスト フレームワークを使用して C# UDO (ユーザー定義演算子) をテストすることができます。 UDO をテストするときは、対応する **IRowset** オブジェクトを入力として準備する必要があります。

IRowset オブジェクトを作成する方法は、2 つあります。

- ファイルからデータを読み込んで IRowset を作成する:

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- データ コレクションからのデータを使用して IRowset を作成する:

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>テスト結果の検証

UDO 関数を呼び出した後、C# assert 関数を使用したスキーマおよび Rowset 値検証によって結果を検証することができます。 Visual Studio で **[ファイル] > [新規作成] > [プロジェクト]** の順に選択して、U-SQL C# UDO 単体テスト サンプル プロジェクトのサンプル コードを使用できます。

### <a name="run-test-cases-in-visual-studio"></a>Visual Studio でのテスト ケースの実行

テスト プロジェクトをビルドした後、**テスト エクスプローラーの [プレイリスト]** からすべてのテスト ケースを実行するか、.cs ファイルを右クリックして **[テストの実行]** を選択することができます。

## <a name="run-test-cases-in-visual-studio-team-service"></a>Visual Studio Team Service でのテスト ケースの実行

**U-SQL スクリプト テスト プロジェクト**と **C# UDO テスト プロジェクト**はどちらも、C# 単体テスト プロジェクトを継承します。 Visual Studio Team Services の [Visual Studio テスト タスク](https://docs.microsoft.com/vsts/pipelines/test/getting-started-with-continuous-testing?view=vsts)で、これらのテスト ケースを実行できます。 

### <a name="run-u-sql-test-cases-in-visual-studio-team-service"></a>Visual Studio Team Service での U-SQL テスト ケースの実行

U-SQL テストの場合は必ず、ビルド マシン上に `CPPSDK` をロードし、次に `CPPSDK` のパスを USqlScriptTestRunner(cppSdkFolderFullPath: \@"") に渡してください。

**CPPSDK とは**

CPPSDK は、Microsoft Visual C++ 14 および Windows SDK 10.0.10240.0 を含むパッケージです。 これは、U-SQL ランタイムに必要な環境です。 このパッケージは、Azure Data Lake Tools for Visual Studio のインストール フォルダー下の次の場所から入手できます。

- Visual Studio 2015: `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- Visual Studio 2017: `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\SDK\ScopeCppSDK`

**Visual Studio Team Services ビルド エージェントで CPPSDK を準備する**

CPPSDK の依存関係を Visual Studio Team Services で準備する最も一般的な方法は次のとおりです。

1.  CPPSDK ライブラリを含むフォルダーを zip 圧縮します。
2.  ソース管理システムに .zip ファイルをチェックインします  (zip ファイルにより、".gitignore" によって一部のファイルが無視されることなく、CPPSDK フォルダー下のすべてのライブラリを確実にチェックインできます)。   
3.  zip ファイルをビルド パイプラインに解凍します。
4.  ビルド コンピューター上で、`USqlScriptTestRunner` がこの解凍したフォルダーを指すようにします。

### <a name="run-c-udo-test-cases-in-visual-studio-team-services"></a>Visual Studio Team Services での C# UDO テスト ケースの実行

C# UDO テストの場合、UDO に必要な次のアセンブリを必ず参照してください。 [Nuget パッケージ Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/) によってこれらを参照する場合は必ず、ビルド パイプラインで NuGet Restore タスクを追加してください。

* Microsoft.Analytics.Interfaces
* Microsoft.Analytics.Types
* Microsoft.Analytics.UnitTest

## <a name="next-steps"></a>次の手順

- [Azure Data Lake Analytics の CI/CD パイプラインを設定する方法](data-lake-analytics-cicd-overview.md)
- [ローカル コンピューターで U-SQL スクリプトを実行する](data-lake-analytics-data-lake-tools-local-run.md)
- [U-SQL データベース プロジェクトを使用して U-SQL データベースを開発する](data-lake-analytics-data-lake-tools-develop-usql-database.md)

