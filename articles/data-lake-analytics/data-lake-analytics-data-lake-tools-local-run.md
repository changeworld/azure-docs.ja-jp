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
ms.author: yanacai
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: ffa31e7eee7642c29a846658b999828434347316
ms.lasthandoff: 03/03/2017


---
# <a name="test-and-debug-u-sql-jobs-by-using-local-run-and-the-azure-data-lake-u-sql-sdk"></a>ローカル実行と Azure Data Lake U-SQL SDK を使用した U-SQL ジョブのテストおよびデバッグ

Azure Data Lake Tools for Visual Studio と Azure Data Lake U-SQL SDK を使用して、U-SQL ジョブを、Azure Data Lake サービスで実行するときと同じように、ワークステーションで実行することができます。 この&2; つのローカル実行機能により、U-SQL ジョブのテストとデバッグにかかる時間を節約できます。

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

詳細については、「[Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md)」を参照してください。




## <a name="next-steps"></a>次のステップ

* Data Lake Analytics の概要については、「 [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)」を参照してください。
* U-SQL アプリケーションの開発を開始する場合は、「 [チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」をご覧ください。
* U-SQL の詳細については、「 [Azure Data Lake Analytics U-SQL 言語の使用](data-lake-analytics-u-sql-get-started.md)」を参照してください。
* 管理タスクについては、「 [Azure Portal を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-portal.md)」をご覧ください。
* 診断情報のログについては、「[Azure Data Lake Analytics の診断ログへのアクセス](data-lake-analytics-diagnostic-logs.md)」を参照してください。
* より複雑なクエリを表示する場合は、[Azure Data Lake Analytics を使用した Web サイトのログの分析](data-lake-analytics-analyze-weblogs.md)に関するページをご覧ください。
* ジョブの詳細を表示する方法については、「[Azure Data Lake Analytics ジョブに対するジョブ ブラウザーとジョブ ビューの使用](data-lake-analytics-data-lake-tools-view-jobs.md)」を参照してください。
* 頂点実行ビューの使用方法については、「[Data Lake Tools for Visual Studio での頂点実行ビューの使用](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)」を参照してください。

