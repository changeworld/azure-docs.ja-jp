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
ms.openlocfilehash: bfb314348caf1d5bf83c940c0bce79e87d6d2593
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2018
ms.locfileid: "37890809"
---
# <a name="how-to-test-your-azure-data-lake-analytics-code"></a>Azure Data Lake Analytics コードをテストする方法

Azure Data Lake が提供する U-SQL は、あらゆる規模のデータを処理するために、宣言型の SQL と命令型の C# を組み合わせた言語です。 このドキュメントでは、U-SQL および拡張 C# UDO (ユーザー定義演算子) コードのテスト ケースを作成する方法について説明します。

## <a name="test-u-sql-scripts"></a>U-SQL スクリプトのテスト

U-SQL スクリプトは実行可能コードにコンパイルおよび最適化され、クラウド上の複数のマシンにまたがって、またはローカル コンピューター上で実行されます。 コンパイルと最適化のプロセスは、全体で実行するものとして U-SQL 全体を扱います。 すべてのステートメントについて従来の "単体テスト" を行うことはできません。 ただし、U-SQL テスト SDK とローカル実行 SDK を使用することによって、スクリプト レベルのテストを行うことができます。

### <a name="create-test-cases-for-u-sql-script"></a>U-SQL スクリプトのテスト ケースの作成

Azure Data Lake Tools for Visual Studio は、U-SQL スクリプトのテスト ケース作成を支援する優れたエクスペリエンスを提供します。

1.  ソリューション エクスプローラーで U-SQL スクリプトを右クリックして **[Create Unit Test]**(単体テストの作成) を選択します。
2.  新しいテスト プロジェクトを作成するための構成を行うか、または既存のテスト プロジェクトにテスト ケースを挿入します。

    ![Data Lake Tools for Visual Studio における U-SQL テスト プロジェクトの作成](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project.png) 

    ![Data Lake Tools for Visual Studio における U-SQL テスト プロジェクト構成の作成](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png) 

### <a name="manage-test-data-source"></a>テスト データ ソースの管理

U-SQL スクリプトをテストするとき、テスト入力ファイルが必要です。 U-SQL プロジェクトのプロパティで **[Test Data Source]** を構成することによって、これらのテスト データを管理できます。 U-SQL テスト SDK で `Initialize()` インターフェイスを呼び出すと、一時ローカル Data Root フォルダーがテスト プロジェクトの作業ディレクトリ下に作成され、U-SQL スクリプトのテスト ケースを実行する前に、テスト データ ソース フォルダー内のすべてのファイルとサブフォルダー(および、サブフォルダー下のファイル) が一時ローカル データ ルート フォルダーにコピーされます。 テスト データ フォルダーのパスをセミコロンで区切ることによって、テスト データ ソース フォルダーをさらに追加できます。

![Data Lake Tools for Visual Studio プロジェクトのテスト データ ソースの構成](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-database-environment-for-test"></a>テスト用データベース環境の管理

ストアド プロシージャの呼び出しなど、U-SQL スクリプトで U-SQL データベース オブジェクトを使用またはクエリする場合、U-SQL テスト ケースを実行する前にデータベース環境を初期化する必要があります。 U-SQL テスト SDK の `Initialize()` インターフェイスは、U-SQL プロジェクトによって参照されているすべてのデータベースを、テスト プロジェクトの作業ディレクトリ内の一時ローカル Data Root フォルダーにデプロイするのに役立ちます。 

[U-SQL プロジェクトの U-SQL データベース プロジェクト参照を管理する方法](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)も確認してください。

### <a name="verify-test-results"></a>テスト結果の検証

`Run()` インターフェイスから返されるジョブ実行結果で、0 は成功、1 は失敗を意味します。 C# Assert 関数を使用して出力を検証することもできます。 

### <a name="execute-test-cases-in-visual-studio"></a>Visual Studio でのテスト ケースの実行

U-SQL スクリプトのテスト プロジェクトは、C# 単体テスト フレームワークの上にビルドされます。 プロジェクトをビルドした後、**テスト エクスプローラーの [プレイリスト]** からすべてのテスト ケースを実行するか、または .cs ファイルを右クリックして **[テストの実行]** を選択することができます。

## <a name="test-c-udos"></a>C# UDO のテスト

### <a name="create-test-cases-for-c-udos"></a>C# UDO のテスト ケースの作成

C# 単体テスト フレームワークを使用して C# UDO (ユーザー定義演算子) をテストすることができます。 UDO をテストするときは、対応する **IRowset** オブジェクトを入力として準備する必要があります。

IRowset を作成する方法は 2 つあります。

1.  ファイルからデータを読み込んで IRowset を作成する

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

2.  データ コレクションからのデータを使用して IRowset を作成する

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

UDO 関数を呼び出した後、C# Assert 関数を使用したスキーマおよび Rowset 値検証によって結果を検証することができます。 サンプル コードは U-SQL C# UDO Unit Test Sample Project にあり、Visual Studio で **[ファイル] > [新規作成] > [プロジェクト]** からアクセスできます。

### <a name="execute-test-cases-in-visual-studio"></a>Visual Studio でのテスト ケースの実行

テスト プロジェクトをビルドした後、**テスト エクスプローラーの [プレイリスト]** からすべてのテスト ケースを実行するか、または .cs ファイルを右クリックして **[テストの実行]** を選択することができます。

## <a name="run-test-cases-in-visual-studio-team-service"></a>Visual Studio Team Service でのテスト ケースの実行

**U-SQL スクリプト テスト プロジェクト**と **C# UDO テスト プロジェクト**はどちらも、C# 単体テスト プロジェクトを継承します。 Visual Studio Team Service の [Visual Studio Test タスク](https://docs.microsoft.com/vsts/pipelines/test/getting-started-with-continuous-testing?view=vsts)で、これらのテスト ケースを実行できます。 

### <a name="run-u-sql-test-cases-in-visual-studio-team-service"></a>Visual Studio Team Service での U-SQL テスト ケースの実行

U-SQL テストの場合は必ず、ビルド コンピューター上に `CPPSDK` をロードし、`CPPSDK` のパスを USqlScriptTestRunner(cppSdkFolderFullPath: @"") に渡してください。

**CPPSDK とは**

CPPSDK は、Microsoft Visual C++ 14 と、U-SQL ランタイムに必要な環境である Windows SDK 10.0.10240.0 をインクルードするパッケージです。 このパッケージは、Azure Data Lake Tools for Visual Studio のインストール フォルダー下の次の場所から入手できます。

- Visual Studio 2015: `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- Visual Studio 2017: `C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\SDK\ScopeCppSDK`

**Visual Studio Team Service ビルド エージェントで CPPSDK を準備する方法**

この CPPSDK 依存関係を Visual Studio Team Service で準備する一般的な方法は次のとおりです。

1.  CPPSDK ライブラリを含むフォルダーを zip 化します。
2.  ソース管理システムに zip ファイルをチェック インします。 (zip ファイルにより、CPPSDK フォルダー下のすべてのライブラリを確実にチェック インできます。この方法を使用しないと、一部のファイルが ".gitignore" によって無視されます)
3.  zip ファイルをビルド パイプラインに解凍します。
4.  ビルド コンピューター上で、`USqlScriptTestRunner` がこの解凍したフォルダーを指すようにします。

### <a name="run-c-udo-test-cases-in-visual-studio-team-service"></a>Visual Studio Team Service での C# UDO テスト ケースの実行

C# UDO テストの場合、UDO に必要な次のアセンブリを必ず参照してください。 [Nuget パッケージ Microsoft.Azure.DataLake.USQL.Interfaces](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/) によってこれらを参照する場合は必ず、ビルド パイプラインで NuGet Restore タスクを追加してください。

* Microsoft.Analytics.Interfaces
* Microsoft.Analytics.Types
* Microsoft.Analytics.UnitTest

## <a name="next-steps"></a>次の手順

- [Azure Data Lake Analytics の CI/CD パイプラインを設定する方法](data-lake-analytics-cicd-overview.md)
- [ローカル コンピューターで U-SQL スクリプトを実行する](data-lake-analytics-data-lake-tools-local-run.md)
- [U-SQL データベース プロジェクトを使用して U-SQL データベースを開発する](data-lake-analytics-data-lake-tools-develop-usql-database.md)

