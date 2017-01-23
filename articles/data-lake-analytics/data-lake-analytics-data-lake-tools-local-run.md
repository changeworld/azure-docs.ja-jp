---
title: "ローカル実行と Azure Data Lake U-SQL SDK を使用した U-SQL ジョブのテストおよびデバッグ | Microsoft Docs"
description: "Azure Data Lake Tools for Visual Studio と Azure Data Lake U-SQL SDK を使用して、ローカル ワークステーション上の U-SQL ジョブをテストおよびデバッグする方法について説明します。"
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: c43fb7b513136bb3ea964419d68f58585bba48fd
ms.openlocfilehash: 348971e07169ae9c155753c7943124e96e6e9597


---
# <a name="test-and-debug-u-sql-jobs-by-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>ローカル実行と Azure Data Lake U-SQL SDK を使用した U-SQL ジョブのテストおよびデバッグ

Azure Data Lake Tools for Visual Studio と Azure Data Lake U-SQL SDK を使用して、U-SQL ジョブを、Azure Data Lake サービスで実行するときと同じように、ワークステーションで実行することができます。 この 2 つのローカル実行機能により、U-SQL ジョブのテストとデバッグにかかる時間を節約できます。

前提条件:

- Azure Data Lake Analytics アカウント。 [Azure Data Lake Analytics の使用開始](data-lake-analytics-get-started-portal.md)に関するページを参照してください。
- Azure Data Lake Tools for Visual Studio。 [Data Lake Tools for Visual Studio を使用した U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)に関するページを参照してください。
- U-SQL スクリプトの開発経験。 [Azure Data Lake Analytics の使用開始](data-lake-analytics-get-started-portal.md)に関するページを参照してください。


## <a name="understand-the-data-root-folder-and-the-file-path"></a>データルート フォルダーとファイル パスについて

ローカル実行と U-SQL SDK は、どちらもデータルート フォルダーを必要とします。 データルート フォルダーは、ローカル コンピューティング アカウント用の "ローカル ストア" で、 Data Lake Analytics アカウントの Azure Data Lake Store アカウントに相当します。 別のデータルート フォルダーへの切り換えは、別のストア アカウントへの切り換えと同じです。 さまざまなデータルート フォルダーで通常共有されるデータにアクセスする必要がある場合は、スクリプト内で絶対パスを使用す必要があります。 または、このデータルート フォルダー内に、共有データを指すファイル システムのシンボリック リンクを作成します (たとえば、NTFS で **mklink** を作成します)。

データルート フォルダーは、以下の目的で使用されます。

- データベース、テーブル、テーブル値関数 (TVF)、アセンブリなどのメタデータを格納する。
- U-SQL で相対パスとして定義されている入力および出力パスを検索する。 相対パスを使用することにより、U-SQL プロジェクトを Azure にデプロイしやすくなります。

U-SQL スクリプトでは、相対パスとローカル絶対パスの両方を使用できます。 相対パスは、指定したデータルート フォルダー パスを基準としています。 サーバー側と互換性のあるスクリプトにするため、パスの区切り文字として "/" を使用することをお勧めします。 以下に相対パスとそれに相当する絶対パスの例をいくつか示します。 次の例では、"C:\LocalRunDataRoot" がデータルート フォルダーです。

|相対パス|絶対パス|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

## <a name="use-local-run-from-visual-studio"></a>Visual Studio からのローカル実行の使用

Data Lake Tools for Visual Studio により、Visual Studio で U-SQL ローカル実行機能が使用できます。 この機能を使用すると、以下の操作を行うことができます。

- C# アセンブリと共に U-SQL スクリプトをローカルで実行する。
- C# アセンブリをローカルでデバッグする。
- サーバー エクスプローラーの U-SQL カタログ (ローカル データベース、アセンブリ、スキーマ、テーブル) を作成、表示、削除する。 ローカル カタログは、サーバー エクスプローラーから確認することもできます。

    ![Data Lake Tools for Visual Studio ローカル実行のローカル カタログ](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-local-catalog.png)

Data Lake Tools インストーラーによって C:\LocalRunRoot フォルダーが作成され、既定のデータルート フォルダーとして使用されます。 ローカル実行の並列処理の既定値は 1 です。

### <a name="to-configure-local-run-in-visual-studio"></a>Visual Studio でローカル実行を構成するには

1. Visual Studio を開きます。
2. **[サーバー エクスプローラー]** を開きます。
3. **[Azure]**  >  **[Data Lake Analytics]** の順に展開します。
4. **[Data Lake]** メニューをクリックし、**[オプションと設定]** をクリックします。
5. 左側のツリーで **[Azure Data Lake]** を展開し、**[全般]** を展開します。

    ![Data Lake Tools for Visual Studio ローカル実行の設定の構成](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-configure.png)

ローカル実行を実行するには、Visual Studio U-SQL プロジェクトが必要です。 この部分は、Azure から U-SQL スクリプトを実行する場合と異なります。

### <a name="to-run-a-u-sql-script-locally"></a>U-SQL スクリプトをローカルで実行するには
1. Visual Studio で、U-SQL プロジェクトを開きます。   
2. ソリューション エクスプローラーで U-SQL スクリプトを右クリックし、**[Submit Script (スクリプトの送信)]** をクリックします。
3. スクリプトをローカルで実行する Analytics アカウントとして **[(Local) ((ローカル))]** を選択します。
スクリプト ウィンドウの上部にある **[(Local) ((ローカル))]** アカウントをクリックしてから、**[送信]** をクリックすることもできます (または Ctrl + F5 キーボード ショートカットを使用します)。

    ![Data Lake Tools for Visual Studio ローカル実行のジョブの送信](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-submit-job.png)



## <a name="use-local-run-from-the-data-lake-u-sql-sdk"></a>Data Lake U-SQL SDK からのローカル実行の使用

Visual Studio を使用してローカルで U-SQL スクリプトを実行するだけでなく、Azure Data Lake U-SQL SDK を使用して、コマンド ラインとプログラミング インターフェイスによってローカルで U-SQL スクリプトを実行できます。 これにより、U-SQL ローカル テストを拡張することができます。

### <a name="install-the-sdk"></a>SDK のインストール

Data Lake U-SQL SDK には、以下の依存関係が必要です。

- [Microsoft .NET Framework 4.6 以降](https://www.microsoft.com/en-us/download/details.aspx?id=17851)。
- Microsoft Visual C++ 14 および Windows SDK 10.0.10240.0 以降。 これを取得する方法は 2 つあります。

    - [Visual Studio Community エディション](https://developer.microsoft.com/downloads/vs-thankyou)をインストールします。 たとえば C:\Program Files (x86)\Windows Kits\10\. のように、Program Files フォルダー内に \Windows Kits\10 フォルダーが作成されます。また、\Windows Kits\10\Lib でも Windows 10 SDK バージョンを確認できます。 このフォルダーが表示されない場合は、Visual Studio を再インストールし、インストール中に必ず Windows 10 SDK を選択してください。 U-SQL ローカル コンパイラ スクリプトでは、これらの依存関係は自動的に検出されます。

    ![Data Lake Tools for Visual Studio ローカル実行の Windows 10 SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

    - [Data Lake Tools for Visual Studio](http://aka.ms/adltoolsvs) をインストールします。 事前にパッケージ化された Visual C++ および Windows SDK ファイルが、C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK で見つかります。 この場合、U-SQL ローカル コンパイラでは依存関係が自動検出されないため、 CppSDK パスを指定する必要があります。 このファイルは別の場所にコピーすることも、そのまま使用することもできます。 その後、環境変数 **SCOPE_CPP_SDK** をそのディレクトリに設定するか、ローカル実行ヘルパー アプリケーションのコマンド ラインで引数 **-CppSDK** にそのディレクトリを指定します。

SDK のインストール後、以下の構成手順を実行する必要があります。

- **SCOPE_CPP_SDK** 環境変数を設定します。

    Data Lake Tools for Visual Studio のインストールによって Microsoft Visual C++ と Windows SDK がインストールされている場合は、次のフォルダーがあることを確認します。

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    このディレクトリを指す **SCOPE_CPP_SDK** という新しい環境変数を定義します。 または、フォルダーを他の場所にコピーし、**SCOPE_CPP_SDK** をそのフォルダーとして指定します。

    コマンド ラインを使用している場合は、環境変数を設定するだけでなく、**-CppSDK** 引数を指定できます。 この引数により、既定の CppSDK 環境変数が上書きされます。

- **LOCALRUN_DATAROOT** 環境変数を設定します。

    データ ルートを指す **LOCALRUN_DATAROOT** という新しい環境変数を定義します。

    コマンド ラインを使用している場合は、環境変数を設定するだけでなく、データルート パスで **-DataRoot** 引数を指定できます。 この引数により、既定のデータルート環境変数が上書きされます。 この引数を、実行しているすべてのコマンド ラインに追加して、すべての操作に対する既定のデータルート環境変数を上書きすることができます。

### <a name="use-the-sdk-from-the-command-line"></a>コマンド ラインからの SDK の使用

#### <a name="command-line-interface-of-the-helper-application"></a>ヘルパー アプリケーションのコマンド ライン インターフェイス

SDK では、LocalRunHelper.exe はコマンド ライン ヘルパー アプリケーションであり、よく使用されるローカル実行機能の大部分へのインターフェイスを提供します。 コマンドと引数スイッチの両方で大文字と小文字が区別される点に注意してください。 これを呼び出すには、次のようにします。

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

引数を指定せずに、または **help** スイッチを使用して LocalRunHelper.exe を実行すると、次のようなヘルプ情報が表示されます。

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

ヘルプ情報には、以下の項目が含まれます。

-  **Command**: コマンド名が表示されます。  
-  **Required Argument**: 指定する必要のある引数が一覧表示されます。  
-  **Optional Argument**: 既定値を持つ省略可能な引数が一覧表示されます。  省略可能なブール引数はパラメーターを持たず、これらの引数が指定されている場合は既定値の反対の値を意味します。

#### <a name="return-value-and-logging"></a>戻り値とログ記録

ヘルパー アプリケーションは、**0** (成功) または **-1** (失敗) を返します。 既定では、すべてのメッセージが現在のコンソールに送信されます。 ただし、ほとんどのコマンドでは、出力をログ ファイルにリダイレクトする省略可能な **-MessageOut path_to_log_file** 引数がサポートされています。


### <a name="sdk-usage-samples"></a>SDK の使用例

#### <a name="compile-and-run"></a>コンパイルと実行

**run** コマンドは、スクリプトをコンパイルしてからその結果を実行するために使用します。 コマンド ライン引数は、**compile** と **run** のコマンド ライン引数を組み合わせたものです。

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

次に例を示します。

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

**compile** と **run** を組み合わせた方法に加え、コンパイルと、コンパイル済みの実行可能ファイルの実行を個別に行う方法もあります。

#### <a name="compile-a-u-sql-script"></a>U-SQL スクリプトのコンパイル

**compile** コマンドは、U-SQL スクリプトを実行可能ファイルにコンパイルするために使用します。

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

コンパイルの省略可能な引数を次に示します。

|引数|Description|
|--------|-----------|
|-CppSDK (既定値 "")|CppSDK ディレクトリ。|
|-DataRoot (既定値 "")|データおよびメタデータのデータ ルート。 既定値は **LOCALRUN_DATAROOT** 環境変数です。|
|-MessageOut (既定値 "")|ファイルにダンプされるコンソール上のメッセージ。|
|-Shallow (既定値 "False")|簡易コンパイル。 スクリプトと戻り値の構文チェックのみを行います。|
|-WorkDir (既定値 "D:\localrun\t\ScopeWorkDir")|コンパイラの使用および出力用のディレクトリ。 詳細については、付録の「作業ディレクトリ」を参照してください。|

アセンブリと分離コード用の省略可能な引数を以下に示します。

|引数|Description|
|--------|-----------|
|-CodeBehind (既定値 "False")|スクリプトに .cs 分離コードが含まれることを示します。この分離コードは、ユーザー定義オブジェクト (UDO) として自動的にコンパイルされ、登録されます|
|-References (既定値 "")|分離コードの追加参照アセンブリまたはデータ ファイルへのパスのリスト。";" で区切られています|
|-UseDatabase (既定値 "master")|分離コードの一時アセンブリ登録に使用するデータベース|
|-UdoRedirect (既定値 "False")|UDO アセンブリのリダイレクト構成。この構成は、UDO が呼び出されたときに、まずコンパイル済みの出力ディレクトリの依存アセンブリをプローブするよう .NET ランタイムに指示します|

使用例をいくつか以下に示します。

U-SQL スクリプトのコンパイル:

    LocalRunHelper compile -Script d:\test\test1.usql

U-SQL スクリプトをコンパイルし、データルート フォルダーを設定します。 これにより環境変数の設定が上書きされることに注意してください。

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

U-SQL スクリプトをコンパイルし、作業ディレクトリ、参照アセンブリ、およびデータベースを設定します。

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>コンパイル結果の実行

**execute** コマンドは、コンパイル結果を実行するために使用されます。   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

省略可能な引数を次に示します。

|引数|説明|
|--------|-----------|
|-DataRoot (既定値 "")|メタデータ実行のデータ ルート。 既定値は **LOCALRUN_DATAROOT** 環境変数です。|
|-MessageOut (既定値 "")|ファイルにダンプされるコンソール上のメッセージ。|
|-Parallel (既定値 "1")|生成されたローカル実行ステップが、指定された並列処理レベルで実行されることを示します。|
|-Verbose (既定値 "False")|ランタイムから詳細な出力が表示されることを示します。|

使用例を次に示します。

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5

## <a name="use-the-sdk-with-programming-interfaces"></a>プログラミング インターフェイスでの SDK の使用

プログラミング インターフェイスはすべて Microsoft.Analytics.LocalRun アセンブリ内にあります。 このインターフェイスを使って、U-SQL SDK および C# テスト フレームワークの機能を統合し、U-SQL スクリプトのローカル テストを拡張できます。 インターフェイスの詳細については、付録を参照してください。

## <a name="appendix"></a>付録

### <a name="working-directory"></a>作業ディレクトリ

U-SQL スクリプトをローカルで実行する場合は、コンパイル時に作業ディレクトリが作成されます。 この作業ディレクトリには、コンパイル出力だけでなく、ローカル実行に必要なランタイム ファイルがシャドウ コピーされます。 コマンド ラインに **-WorkDir** 引数が指定されていない場合、現在のディレクトリ内に既定の作業ディレクトリ ScopeWorkDir が作成されます。 作業ディレクトリ内のファイルは以下のとおりです。

|ディレクトリ/ファイル|定義|Description|
|--------------|----------|-----------|
|ScopeWorkDir|作業ディレクトリ|ルート フォルダー|
|C6A101DDCB470506|ランタイム バージョンのハッシュ文字列|ローカル実行に必要なランタイム ファイルのシャドウ コピー|
|\.\Script_66AE4909AA0ED06C|スクリプト名 + スクリプト パスのハッシュ文字列|コンパイル出力と実行ステップのログ記録|
|\.\.\\_\_script\_\_.abr|コンパイラの出力|代数ファイル|
|\.\.\\_\_ScopeCodeGen\_\_.*|コンパイラの出力|生成されたマネージ コード|
|\.\.\\_\_ScopeCodeGenEngine\_\_.*|コンパイラの出力|生成されたネイティブ コード|
|\.\.\referenced_assemblies|アセンブリ参照|参照アセンブリ ファイル|
|\.\.\deployed_resources|リソースのデプロイ|リソースのデプロイ ファイル|
|\.\.\xxxxxxxx.xxx[1..n]_*.*|実行ログ|実行ステップのログ|

### <a name="programming-interfaces-for-the-azure-data-lake-u-sql-sdk"></a>Azure Data Lake U-SQL SDK のプログラミング インターフェイス

プログラミング インターフェイスはすべて Microsoft.Analytics.LocalRun アセンブリ内にあります。

#### <a name="microsoftanalyticslocalrunconfiguration"></a>Microsoft.Analytics.LocalRun.Configuration
Microsoft.Analytics.LocalRun.Configuration は、コンパイルの構成パラメーター クラスです。

**コンストラクター**

public Configuration(string rootPath)

|パラメーター|型|Description|
|---------|----|-----------|
|rootPath|System.String|作業コンテキストの現在のディレクトリへのパス。 WorkingDirectory が設定されていない場合、既定の作業ディレクトリは rootPath + ScopeWorkDir となります。|

**プロパティ**

|名前|Description|
|----|-----------|
|CppSDK|CppSDK の場所 (システムの既定の構成でない場合)。 |
|DataDirectory|テーブル、アセンブリ、および入出力データの保存場所。 既定値は ScopeWorkDir\DataDir です。 |
|GenerateUdoRedirect|アセンブリ読み込みリダイレクトのオーバーライド構成を生成するかどうかを示します。|
|WorkingDirectory|コンパイラの作業ディレクトリ。 既定値は ScopeWorkDir です。|


#### <a name="microsoftanalyticslocalrunlocalcompiler"></a>Microsoft.Analytics.LocalRun.LocalCompiler
Microsoft.Analytics.LocalRun.LocalCompiler は U-SQL ローカル コンパイラ クラスです。

**コンストラクター**

public LocalCompiler(Configuration configuration)

|パラメーター|型|説明|
|---------|----|-----------|
|構成|Microsoft.Analytics.LocalRun.Configuration||

**メソッド**

public bool Compile(string script, string filePath, bool shallow, out CommonCompileResult result)

|パラメーター|型|Description|
|---------|----|-----------|
|script (スクリプト)|System.String|入力スクリプトの文字列。|
|filePath|System.String|スクリプト ファイルのパス。 null に設定され、既定値が使用されます。|
|shallow|System.Boolean|簡易コンパイル (構文の検証のみ) または完全コンパイル。|
|result|Microsoft.Cosmos.ClientTools.Shared.CommonCompileResult|詳細なコンパイル結果。|
|戻り値|System.Boolean|True: コンパイル時に重大なエラーなし。 <br>False: コンパイル時に重大なエラーあり。|

#### <a name="microsoftanalyticslocalrunlocalrunner--idisposable"></a>Microsoft.Analytics.LocalRun.LocalRunner : IDisposable
Microsoft.Analytics.LocalRun.LocalRunner : IDisposable は U-SQL ローカル ランナー クラスです。

**コンストラクター**

public LocalRunner(string algebraFilePath, string dataRoot, Action<string> postMessage = null)

|パラメーター|型|説明|
|---------|----|-----------|
|algebraFilePath|System.String|代数ファイルへのパス|
|dataRoot|System.String|DataRoot へのパス|
|postMessage (省略可能)|System.Action<String>|進行状況のログ記録ハンドラー|

public LocalRunner(string algebraFilePath, string dataRoot, string cachePath, string runtimePath, string tempPath, string logPath, Action<Object, ExecutionStatusBase. ExecutionEventArgs> execEventHandler, Object eventContext, Action<string> postMessage = null)

|パラメーター|型|説明|
|---------|----|-----------|
|algebraFilePath|System.String|代数ファイルへのパス。|
|dataRoot|System.String|DataRoot へのパス。|
|cachePath|System.String|コンパイル結果のディレクトリへのパス。 null に設定され、既定値が使用されます。この場所に代数ファイルがあります。|
|runtimePath|System.String|シャドウ コピーされたランタイムのディレクトリへのパス。 null に設定され、既定値が使用されます。この場所に cachePath の親ディレクトリがあります。|
|tempPath|System.String|内部使用のみの一時ストレージのパス。 null に設定されます。|
|logPath|System.String|実行ログの書き込み先のパス。 null に設定され、cachePath が既定値が使用されます。|
|execEventHandler|System. Action<Object, ExecutionStatusBase.ExecutionEventArgs>|実行状態変更のイベント通知ハンドラー。|
|eventContext|System. オブジェクト|イベント ハンドラーへのコンテキスト。|
|postMessage (省略可能)|System.Action<String>|進行状況のログ記録ハンドラー。|

**プロパティ**

|名前|Description|
|----|-----------|
|AlgebraPath|代数ファイルへのパス。|
|CachePath|生成されたバイナリが置かれるコンパイラ結果キャッシュのパス。|
|CompletedSteps|完了したステップの数。|
|DataRoot|メタデータの DataRoot。|
|LastErrorMessage|(ExecutionStatusBase から継承)。|
|LogPath|ログ ファイルの保存先。 存在しない場合はセッターによってディレクトリが作成されます。 既に作成されているログのパスがクリーニングされることはありません。|
|OutputHeader|テキスト形式の出力内のダンプ スキーマ ヘッダー。|
|並列処理|並列処理。 既定値は論理プロセッサ数 1 です。 開始後にこの値を変更すると、例外が発生します。|
|進捗状況|0 ～ 100% の範囲内で示される実行の進行状況。|
|RuntimePath|ランタイム ファイルの場所。 コンパイラによってシャドウ コピーされた場合、この場所は CachePath の 1 つ上のディレクトリにします。|
|状態|実行の状態。 <br><br>enum ExecutionStatusBase.ExecutionStatus <br>{ <br>Initialized, // Initialized. <br>Running,     // It is running.  WaitOne only checks the event in this state. <br>Success,     // It finished successfully. <br>Error,       // It failed. <br>}|
|TotalSteps|実行するステップの総数。 頂点 DAG の構築後にのみ、有効な値が使用可能になります。|
|詳細|実行時の詳細。|

**メソッド**

|メソッド|Description|
|------|-----------|
|Cancel()|実行中の代数を取り消します。 <br><br>戻り値の型はブール値です。 <br><br>戻り値が False: エラーが原因でキャンセルできませんでした。詳細については LastErrorMessage を確認してください。|
|Start()|代数の実行を開始します。 <br><br>戻り値の型はブール値です。 <br><br>戻り値が False: エラーが原因で開始できませんでした。詳細については LastErrorMessage を確認してください。|
|WaitOne() <br>WaitOne(Int32) <br>WaitOne(TimeSpan) <br>WaitOne(Int32, Boolean) <br>WaitOne(TimeSpan, Boolean)|完了するまで待ちます。 WaitHandle.WaitOne を参照してください。|
|Dispose()||


## <a name="next-steps"></a>次のステップ

* Data Lake Analytics の概要については、「 [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。
* U-SQL アプリケーションの開発を開始する場合は、「 [チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」をご覧ください。
* U-SQL の詳細については、「 [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)」を参照してください。
* 管理タスクについては、「 [Azure Portal を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)」をご覧ください。
* 診断情報のログについては、「[Azure Data Lake Analytics の診断ログへのアクセス](data-lake-analytics-diagnostic-logs.md)」を参照してください。
* より複雑なクエリを表示する場合は、[Azure Data Lake Analytics を使用した Web サイトのログの分析](data-lake-analytics-analyze-weblogs.md)に関するページをご覧ください。
* ジョブの詳細を表示する方法については、「[Azure Data Lake Analytics ジョブに対するジョブ ブラウザーとジョブ ビューの使用](data-lake-analytics-data-lake-tools-view-jobs.md)」を参照してください。
* 頂点実行ビューの使用方法については、「[Data Lake Tools for Visual Studio での頂点実行ビューの使用](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)」を参照してください。



<!--HONumber=Dec16_HO1-->


