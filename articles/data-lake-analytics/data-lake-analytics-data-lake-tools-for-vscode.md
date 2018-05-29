---
title: 'Azure Data Lake Tools: Azure Data Lake Tools for Visual Studio Code の使用 | Microsoft Docs'
description: 'Azure Data Lake Tools for Visual Studio Code を使用して U-SQL スクリプトを作成、テスト、実行する方法について説明します。 '
Keywords: VScode,Azure Data Lake Tools,Local run,Local debug,Local Debug,preview file,upload to storage path,download,upload
services: data-lake-analytics
documentationcenter: ''
author: Jejiang
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2018
ms.author: jejiang
ms.openlocfilehash: f35aa14286874d7c152509a69bd171b95b19e22b
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "34011273"
---
# <a name="use-azure-data-lake-tools-for-visual-studio-code"></a>Azure Data Lake Tools for Visual Studio Code の使用

Azure Data Lake Tools for Visual Studio Code (VS Code) を使用した U-SQL スクリプトの作成、テスト、および実行について説明します。 この情報は、次のビデオにも含まれています。

<a href="https://channel9.msdn.com/Series/AzureDataLake/Azure-Data-Lake-Tools-for-VSCode?term=ADL%20Tools%20for%20VSCode"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>前提条件

Azure Data Lake Tools for VSCode では、Windows、Linux、および MacOS がサポートされます。  

- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)。

MacOS および Linux の場合 :
- [.NET Core SDK 2.0](https://www.microsoft.com/net/download/core)。 
- [Mono 5.2.x](http://www.mono-project.com/download/)。

## <a name="install-data-lake-tools"></a>Data Lake Tools のインストール

前提条件の各項目のインストールが完了したら、Data Lake Tools for VS Code をインストールできます。

**Data Lake Tools をインストールするには**

1. Visual Studio Code を開きます。
2. 左側のウィンドウで **[拡張機能]** をクリックします。 検索ボックスに「**Azure Data Lake**」と入力します。
3. **[Azure Data Lake Tools]** の横にある **[インストール]** をクリックします。 数秒後、**[インストール]** ボタンが **[再読み込み]** に変わります。
4. **[再度読み込む]** をクリックして、**Azure Data Lake Tools** の拡張機能をアクティブにします。
5. **[ウィンドウの再読み込み]** をクリックして確認します。 **[拡張機能]** ウィンドウに Azure Data Lake Tools が表示されます。

    ![Data Lake Tools for Visual Studio Code の [拡張機能] ウィンドウ](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

 
## <a name="activate-azure-data-lake-tools"></a>Azure Data Lake Tools をアクティブ化する
新しい .usql ファイルを作成するか、既存の .usql ファイルを開いて拡張機能をアクティブにしてください。 

## <a name="open-the-sample-script"></a>サンプル スクリプトを開く
コマンド パレットを開いて (Ctrl + Shift + P)、「**ADL: Open Sample Script**」と入力します。 このサンプルの別のインスタンスが開きます。 このインスタンスのスクリプトは編集、構成、送信することもできます。

## <a name="work-with-u-sql"></a>U-SQL の操作

U-SQL を操作するには、U-SQL ファイルまたはフォルダーを開く必要があります。

**U-SQL プロジェクトのフォルダーを開くには**

1. Visual Studio Code で **[ファイル]** メニューを選択し、**[フォルダーを開く]** を選択します。
2. フォルダーを指定し、**[フォルダーの選択]** を選択します。
3. **[ファイル]** メニューを選択し、**[New]\(新規作成\)** を選択します。 Untitled-1 ファイルがプロジェクトに追加されます。
4. 次のコードを Untitled-1 ファイルに入力します。

        @departments  = 
            SELECT * FROM 
                (VALUES
                    (31,    "Sales"),
                    (33,    "Engineering"), 
                    (34,    "Clerical"),
                    (35,    "Marketing")
                ) AS 
                      D( DepID, DepName );
         
        OUTPUT @departments
            TO "/Output/departments.csv"
        USING Outputters.Csv();

    このスクリプトにより、いくつかのデータを含む departments.csv ファイルが /output フォルダーに作成されます。

5. 開いたフォルダーに **myUSQL.usql** という名前でファイルを保存します。

**U-SQL スクリプトをコンパイルするには**

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。 
2. 「**ADL: Compile Script**」と入力します。 **[出力]** ウィンドウにコンパイル結果が表示されます。 スクリプト ファイルを右クリックし、**[ADL: Compile Script]** を選択して U-SQL ジョブをコンパイルすることもできます。 コンパイル結果は **[出力]** ウィンドウに表示されます。
 
**U-SQL スクリプトを送信するには**

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。 
2. 「**ADL: Submit Job**」と入力します。  スクリプト ファイルを右クリックし、**[ADL: Submit Job]** を選択することもできます。 

 U-SQL ジョブの送信後、VS Code の **[出力]** ウィンドウに送信ログが表示されます。 ジョブ ビューが右側のウィンドウに表示されます。 送信が成功した場合は、ジョブの URL も表示されます。 ジョブの URL を Web ブラウザーで開くと、リアルタイムのジョブの状態を追跡できます。 ジョブ ビューの [概要] タブでは、ジョブの詳細を確認できます。 主な機能には、スクリプトの再送信、スクリプトの複製、ポータルでのオープンなどがあります。 ジョブ ビューの [データ] タブでは、入力ファイル、出力ファイル、リソースを参照できます。 ファイルはローカル コンピューターにダウンロードできます。

   ![Data Lake Tools for Visual Studio Code の構成ファイル](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-summary.png)

   ![Data Lake Tools for Visual Studio Code の構成ファイル](./media/data-lake-analytics-data-lake-tools-for-vscode/job-view-data.png)

**既定のコンテキストの設定**

 この設定を適用する既定のコンテキストをすべてのスクリプト ファイルに設定することができます (ファイルのパラメーターを個々に設定していない場合)。

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。 
2. 「**ADL: Set Default Context**」と入力します。
3. あるいは、スクリプト エディターを右クリックし、**[ADL: Set Default Context]** を選択して、必要なアカウント、データベース、およびスキーマを選択します。 この設定は xxx_settings.json 構成ファイルに保存されます。

    ![Data Lake Tools for Visual Studio Code の構成ファイル](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-sequence.png)

**スクリプト パラメーターの設定**

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。 
2. 「**ADL: Set Script Parameters**」と入力します。
3. xxx_settings.json ファイルが開き、以下のプロパティが表示されます。

  - アカウント: U-SQL ジョブのコンパイルと実行に必要な、ご使用の Azure サブスクリプションの Data Lake Analytics アカウント。このため、U-SQL ジョブをコンパイルおよび実行する前にコンピューター アカウントを構成する必要があります。
    - データベース: 自分のアカウント内のデータベース。 既定値は **master** です。
    - スキーマ: データベース内のスキーマ。 既定値は **dbo** です。
    - オプション設定:
        - 優先順位: 優先順位の範囲は 1 ～ 1,000 で、1 が最も高い優先順位です。 既定値は **1,000** です。
        - 並列処理: 並列処理の範囲は 1 ～ 150 です。 既定値は、Azure Data Lake Analytics アカウントで許可される並列処理の最大数です。 

        ![Data Lake Tools for Visual Studio Code の構成ファイル](./media/data-lake-analytics-data-lake-tools-for-vscode/default-context-setting.png)
      
        > [!NOTE] 
        > 構成の保存後、アカウント、データベース、スキーマ情報が、対応する .usql ファイルの左下隅にあるステータス バーに表示されます (既定のコンテキストを設定していない場合)。

**Git Ignore の設定**

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。 
2. 「**ADL: Set Git Ignore**」と入力します。

    - VSCode 作業フォルダーに **.gitIgnore** ファイルが存在しない場合、このフォルダーに **.gitIgnore** という名前のファイルが作成されます。 既定では、このファイルには 4 つの項目 (**usqlCodeBehindReference**、**usqlCodeBehindGenerated**、**.cache**、**obj**) が追加されます。 必要に応じてさらに更新できます。
    - VSCode 作業フォルダーに **.gitIgnore** ファイルが既に存在する場合、この **.gitIgnore** ファイルに 4 項目 (**usqlCodeBehindReference**、**usqlCodeBehindGenerated**、**.cache**、**obj**) が記載されていなければ、これらの項目がツールによってファイルに追加されます。

    ![Data Lake Tools for Visual Studio Code の構成ファイル](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-gitignore.png)

## <a name="use-python-r-and-csharp-code-behind-file"></a>Python、R、CSharp の分離コード ファイルの使用
Azure Data Lake Tool では複数のカスタム コードがサポートされています。手順については、[VSCode での Python、R、および CSharp を使用した Azure Data Lake Analytics の U-SQL の開発](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)に関するページをご覧ください。

## <a name="use-assemblies"></a>アセンブリの使用

アセンブリの開発に関する情報については、「[Azure Data Lake Analytics ジョブの U-SQL アセンブリの開発](data-lake-analytics-u-sql-develop-assemblies.md)」を参照してください。

Data Lake Analytics カタログには、Data Lake Tools を使用してカスタム コード アセンブリを登録できます。

**アセンブリを登録するには**

**ADL: Register Assembly** コマンドまたは **ADL: Register Assembly (Advanced)** コマンドを使用してアセンブリを登録できます。

**ADL: Register Assembly コマンドを使用して登録するには**
1.  Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2.  「**ADL: Register Assembly**」と入力します。 
3.  ローカル アセンブリのパスを指定します。 
4.  Data Lake Analytics アカウントを選択します。
5.  データベースを選択します。

結果: ブラウザーにポータルが表示され、アセンブリの登録プロセスが表示されます。  

**ADL: Register Assembly** コマンドをトリガーする他の便利な方法として、エクスプローラーで .dll ファイルを右クリックする方法があります。 

**ADL: Register Assembly (Advanced) コマンドを使用して登録するには**
1.  Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2.  「**ADL: Register Assembly (Advanced)**」と入力します。 
3.  ローカル アセンブリのパスを指定します。 
4.  JSON ファイルが表示されます。 アセンブリの依存関係とリソース パラメーターを確認して、必要な場合は編集します。 手順が **[出力]** ウィンドウに表示されます。 アセンブリの登録に進むには、JSON ファイルを保存 (Ctrl + S) します。

    ![Data Lake Tools for Visual Studio Code の分離コード](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
    
   >[!NOTE]
   >- アセンブリの依存関係: Azure Data Lake Tools は、DLL が依存関係を備えているかどうかを自動的に検出します。 検出された依存関係は、その後 JSON ファイルに表示されます。 
   >- リソース: DLL リソース (.txt、.png、.csv など) をアセンブリの登録の一部としてアップロードできます。 

**ADL: Register Assembly (Advanced)** コマンドをトリガーする別の方法として、エクスプローラーで .dll ファイルを右クリックする方法があります。 

次の U-SQL コードは、アセンブリを呼び出す方法を示しています。 このサンプルでは、アセンブリ名は *test* です。


        REFERENCE ASSEMBLY [test];

        @a = 
            EXTRACT 
                Iid int,
            Starts DateTime,
            Region string,
            Query string,
            DwellTime int,
            Results string,
            ClickedUrls string 
            FROM @"Sample/SearchLog.txt" 
            USING Extractors.Tsv();

        @d =
            SELECT DISTINCT Region 
            FROM @a;

        @d1 = 
            PROCESS @d
            PRODUCE 
                Region string,
            Mkt string
            USING new USQLApplication_codebehind.MyProcessor();

        OUTPUT @d1 
            TO @"Sample/SearchLogtest.txt" 
            USING Outputters.Tsv();


## <a name="connect-to-azure"></a>Azure への接続

Data Lake Analytics で U-SQL スクリプトをコンパイルして実行するには、まず Azure アカウントに接続する必要があります。

**Azure に接続するには**

1.  Ctrl + Shift + P キーを押してコマンド パレットを開きます。 
2.  「**ADL: Login**」と入力します。 上部の領域にログイン情報が表示されます。

    ![Data Lake Tools for Visual Studio Code のコマンド パレット](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![Data Lake Tools for Visual Studio Code デバイス ログイン情報](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3.  **[Copy & Open]\(コピーして開く\)** をクリックし、URL として https://aka.ms/devicelogin を指定してログイン Web ページを開きます。 テキスト ボックスにコード **G567LX42V** を貼り付けて、**[続行]** を選択します。

   ![Data Lake Tools for Visual Studio Code のログイン時に貼り付けるコード](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  手順に従って Web ページからサインインします。 接続すると、**VS Code** ウィンドウの左下隅のステータス バーに Azure アカウント名が表示されます。 

    > [!NOTE] 
    >- Data Lake Tool では、以前にサインインしていてまだログアウトしていない場合、次回は自動でサインインされます。
    >- アカウントで 2 要素認証が有効になっている場合は、PIN ではなく電話認証の使用をお勧めします。


サインアウトするには、**ADL: Logout** コマンドを入力します。

## <a name="list-your-data-lake-analytics-accounts"></a>Data Lake Analytics アカウントの一覧表示

接続をテストするには、Data Lake Analytics アカウントの一覧を取得します。

**Azure サブスクリプション内の Data Lake Analytics アカウントを一覧表示するには**

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2. 「**ADL: List Accounts**」と入力します。 **[出力]** ウィンドウにアカウントが表示されます。


## <a name="access-the-data-lake-analytics-catalog"></a>Data Lake Analytics カタログへのアクセス

Azure に接続した後は、以下の手順に従って U-SQL カタログにアクセスできます。

**Azure Data Lake Analytics メタデータにアクセスするには**

1.  Ctrl + Shift + P キーを押し、「**ADL: List Tables**」と入力します。
2.  いずれかの Data Lake Analytics アカウントを選択します。
3.  いずれかの Data Lake Analytics データベースを選択します。
4.  いずれかのスキーマを選択します。 テーブルの一覧が表示されます。

## <a name="view-data-lake-analytics-jobs"></a>Data Lake Analytics ジョブの表示

**Data Lake Analytics ジョブを表示するには**
1.  コマンド パレットを開いて (Ctrl + Shift + P)、**[ADL: Show Job]** を選択します。 
2.  Data Lake Analytics アカウントまたはローカル アカウントを選択します。 
3.  アカウントのジョブの一覧が表示されるまで待機します。
4.  ジョブ リストからジョブを選択すると、Data Lake Tools によって右側のウィンドウにジョブ ビューが開かれ、VS Code **OUTPUT** に情報が表示されます。

    ![Data Lake Tools for Visual Studio Code の IntelliSense オブジェクト型](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-integration"></a>Azure Data Lake Storage の統合

Azure Data Lake Storage 関連コマンドを使用して次のことを実行できます。
 - Azure Data Lake Storage のリソースを閲覧する。 ([ストレージ パスの一覧表示](#list-the-storage-path)) 
 - Azure Data Lake Storage ファイルをプレビューする。 ([ストレージ ファイルのプレビュー](#preview-the-storage-file)) 
 - VS Code で Azure Data Lake Storage に直接ファイルをアップロードする。 ([ファイルまたはフォルダーのアップロード](#upload-file-or-folder))
 - VS Code で Azure Data Lake Storage から直接ファイルをダウンロードする。 ([ファイルのダウンロード](#download-file))

### <a name="list-the-storage-path"></a>ストレージ パスの一覧表示 

**コマンド パレットを使用してストレージ パスを一覧表示するには**

スクリプト エディターを右クリックし、**[ADL: List Path]\(ADL: パスの一覧表示\)** を選択します。

一覧でフォルダーを選択するか、**[Enter Path]** または **[Browse from Root]** をクリックします (例として、[Enter a path] を使用します)。 -> お使いの**ADLA アカウント**を選択します。 -> ストレージ フォルダーのパス (/output/ など) へ移動するか、パスを入力します。 -> コマンド パレットに、エントリに基づくパス情報が一覧表示されます。

![Data Lake Tools for Visual Studio Code のストレージ パスの結果一覧](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

相対パスの一覧を表示するさらに便利な方法は、右クリック コンテキスト メニューを使用することです。

**右クリックを使用してストレージ パスを一覧表示するには**

パス文字列を右クリックし、**[List Path]\(パスの一覧表示\)** を選択して続行します。

![Data Lake Tools for Visual Studio Code の右クリック コンテキスト メニュー](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)


### <a name="preview-the-storage-file"></a>ストレージ ファイルのプレビュー

スクリプト エディターを右クリックし、**[ADL: Preview File]\(ADL: ファイルのプレビュー\)** を選択します。

お使いの **ADLA アカウント**を選択します。 -> Azure ストレージ ファイルのパス (/output/SearchLog.txt など) を入力します。 -> 結果: VSCode でファイルが開きます。

   ![Data Lake Tools for Visual Studio Code のファイル結果をプレビュー](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

ファイルをプレビューする別の方法として、スクリプト エディターでファイルの完全なパスまたは相対パスの右クリック メニューを使用する方法があります。 

### <a name="upload-file-or-folder"></a>ファイルまたはフォルダーのアップロード

1. スクリプト エディターを右クリックし、**[ファイルのアップロード]** または **[フォルダーをアップロード]** を選択します。

2. 1 つ以上のファイル ("ファイルのアップロード" を選択した場合) またはフォルダー全体 ("フォルダーをアップロード" を選択した場合) を選択し、**[アップロード]** をクリックします。 -> 一覧でストレージ フォルダーを選択するか、**[パスの入力]** または **[Browse from Root]\(ルートから参照\)** をクリックします (例として、"パスの入力" を使用します)。 -> お使いの**ADLA アカウント**を選択します。 -> ストレージ フォルダーのパス (/output/ など) へ移動するか、パスを入力します。 -> **[Choose Current Folder]** をクリックして、アップロード先を指定します

   ![Data Lake Tools for Visual Studio Code のアップロード状態](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)    


   ファイルをストレージにアップロードする別の方法として、スクリプト エディターでファイルの完全なパスまたは相対パスの右クリック メニューを使用する方法があります。

同時に、[アップロード状態](#check-storage-tasks-status)を監視できます。


### <a name="download-file"></a>ファイルのダウンロード 
**[ADL: Download File]\(ADL: ファイルのダウンロード\)** または **[ADL: Download File (Advanced)]\(ADL: ファイルのダウンロード (詳細)\)** コマンドを入力して、ファイルをダウンロードできます。

**[ADL: Download File (Advanced)]\(ADL: ファイルのダウンロード (詳細)\) を使用してファイルをダウンロードするには**
1. スクリプト エディターを右クリックし、**[Download File (Advanced)]\(ファイルのダウンロード (詳細)\)** を選択します。
2. VS Code に、JSON ファイルが表示されます。 ファイルのパスを入力して、複数のファイルを同時にダウンロードできます。 手順が **[出力]** ウィンドウに表示されます。 ファイルのダウンロードを続行するには、Ctrl + S キーを押して JSON ファイルを保存します。

    ![Data Lake Tools for Visual Studio Code で config を使用してファイルをダウンロードする](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

3.  結果: **[出力]** ウィンドウに、ファイル アップロードの状態が表示されます。

    ![Data Lake Tools for Visual Studio Code で複数のファイル結果をダウンロードする](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)     

同時に、[ダウンロード状態](#check-storage-tasks-status)を監視できます。

**[ADL: Download File]\(ADL: ファイルのダウンロード\) を使用してファイルをダウンロードするには**

1. スクリプト エディターを右クリックし、**[ファイルのダウンロード]** を選択してから、**[フォルダーの選択]** ダイアログでダウンロード先フォルダーを選択します。

2. 一覧でフォルダーを選択するか、**[Enter Path]** または **[Browse from Root]** をクリックします (例として、[Enter a path] を使用します)。 -> お使いの**ADLA アカウント**を選択します。 -> ストレージ フォルダーのパス (/output/ など) へ移動するか、パスを入力します。 -> ダウンロードするファイルを選択します。

   ![Data Lake Tools for Visual Studio Code のダウンロード状態](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png) 

   
   ストレージ ファイルをダウンロードする別の方法として、スクリプト エディターでファイルの完全なパスまたは相対パスの右クリック メニューを使用する方法があります。

同時に、[ダウンロード状態](#check-storage-tasks-status)を監視できます。

### <a name="check-storage-tasks-status"></a>ストレージ タスクの状態の確認
ダウンロードおよびアップロードが完了したときに、ステータス バーの下部に状態が表示されます。
1. ステータス バーの下部をクリックすると、ダウンロードおよびアップロードの状態が **[出力]** パネルに表示されます。

   ![Data Lake Tools for Visual Studio Code でのストレージ状態の確認](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="vscode-explorer-integration-with-azure-data-lake"></a>VSCode エクスプローラーと Azure Data Lake の統合

**Azure の統合** 

- Azure にログインしていない場合はいつでも、**[AZURE DATALAKE]** を展開して **[Azure にサインイン]** をクリックすることで、Azure に ログインできます。 ログインすると、**[AZURE DATALAKE]** の左側のパネルに、現在の Azure アカウントに含まれるサブスクリプションがすべて一覧表示されます。 

   ![DataLake エクスプローラー](./media/data-lake-analytics-data-lake-tools-for-vscode/sign-in-datalake-explorer.png)

   ![DataLake エクスプローラー](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

**ADLA メタデータ ナビゲーション**

- Azure サブスクリプションを展開すると、U-SQL データベース ノードで U-SQL データベースに移動し、**スキーマ**、**資格情報**、**アセンブリ**、**テーブル**、**インデックス**などを確認することができます。

**ADLA メタデータ エンティティの管理**

- **[U-SQL データベース]** を展開し、対応するノードで右クリックしてコンテキスト メニューから **[Script to Create]\(作成スクリプト\)** を選択して、データベース、スキーマ、テーブル、テーブル タイプ、インデックス、統計を新規作成できます。 開かれたスクリプト ページで、必要に応じてスクリプトを編集し、右クリックしてコンテキスト メニューから **[ADL: ジョブの送信]** を選択して、ジョブを送信します。 作成が完了したら、コンテキスト メニューから **[更新]** をクリックすると、新しく作成したアイテムが表示されます。 右クリックしてコンテキスト メニューから **[削除]** を選択して、アイテムを削除することもできます。

   ![DataLake エクスプローラーでの新規アイテム メニューの作成](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

   ![DataLake エクスプローラーでの新規アイテム スクリプトの作成](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

**ADLA アセンブリの登録**

 - **[アセンブリ]** ノードを右クリックして、対応するデータベースに**アセンブリを登録**できます。

    ![DataLake エクスプローラー](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

**ADLS 統合** 

**[Data Lake Store]** に移動します

 - フォルダー ノードの右クリック コンテキスト メニューでは、**[更新]**、**[削除]**、**[アップロード]**、**[Upload Folder]\(フォルダーのアップロード\)**、**[相対パスのコピー]**、**[完全パスのコピー]** を行うことができます。

   ![DataLake エクスプローラー](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

 - ファイル ノードの右クリック コンテキスト メニューでは、**[ダウンロード]**、**[プレビュー]**、**[削除]**、**[相対パスのコピー]**、**[完全パスのコピー]** を行うことができます。 

   ![DataLake エクスプローラー](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-download-preview-file.png)

**WASB の統合**

**[Blob ストレージ]** に移動します

- BLOB コンテナー ノードの右クリック コンテキスト メニューでは、**[更新]**、**[BLOB コンテナーの削除]**、**[BLOB のアップロード]** を行うことができます。

    ![[Blob ストレージ] の BLOB コンテナー ノード](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- フォルダー ノードの右クリック コンテキスト メニューでは、**[更新]**、**[BLOB のアップロード]** を行うことができます。

    ![[Blob ストレージ] のフォルダー ノード](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- ファイル ノードの右クリック コンテキスト メニューでは、**[Preview/Edit]\(プレビュー/編集\)**、**[ダウンロード]**、**[削除]**、**[相対パスのコピー]**、**[完全パスのコピー]** を行うことができます。

    ![[Blob ストレージ] のファイル ノード](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-file-node.png)

## <a name="open-adl-storage-explorer-in-portal"></a>ポータルで ADL ストレージ エクスプローラーを開く
1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2. 「**Open Web Azure Storage Explorer**」と入力するか、スクリプト エディターで相対パスまたは完全なパスを右クリックして、**[Open Web Azure Storage Explorer]\(Web Azure Storage Explorer を開く\)** を選択します。
3. Data Lake Analytics アカウントを選択します。

Data Lake Tools によって、Azure Portal に Azure ストレージ パスが表示されます。 そのパスを見つけて Web からファイルをプレビューすることができます。

## <a name="local-run-and-local-debug-for-windows-users"></a>Windows ユーザーのローカル実行とローカル デバッグ
U-SQL ローカル実行は、Data Lake Analytics にコードを発行する前に、ローカル データをテストし、スクリプトをローカルで検証するものです。 作成したコードを Data Lake Analytics に送信する前に、ローカル デバッグ機能によって次のタスクを実行することができます。 
- C# 分離コードをデバッグする。 
- コードをステップ実行する。 
- スクリプトをローカルで検証する。

ローカル実行とローカル デバッグの手順については、「[Visual Studio Code を使用した U-SQL ローカル実行とローカル デバッグ](data-lake-tools-for-vscode-local-run-and-debug.md)」を参照してください。

## <a name="additional-features"></a>その他の機能

Data Lake Tools for VS Code では、以下の各機能がサポートされています。

-   IntelliSense オートコンプリート: キーワードやメソッド、変数などの要素の付近にポップアップ ウィンドウで入力候補が表示されます。 以下に示すさまざまなオブジェクト型が、異なるアイコンで表されます。

    - Scala データ型
    - 複合データ型
    - 組み込みの UDT
    - .NET コレクションおよびクラス
    - C# 式
    - 組み込みの C# UDF、UDO、UDAAG 
    - U-SQL 関数
    - U-SQL ウィンドウ化関数
 
    ![Data Lake Tools for Visual Studio Code の IntelliSense オブジェクト型](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Data Lake Analytics メタデータに基づく IntelliSense オートコンプリート: Data Lake Tools によって Data Lake Analytics メタデータ情報がローカルにダウンロードされます。 IntelliSense 機能により、Data Lake Analytics メタデータからデータベース、スキーマ、テーブル、ビュー、テーブル値関数、プロシージャ、C# アセンブリなどのオブジェクトが自動的に入力されます。
 
    ![Data Lake Tools for Visual Studio Code の IntelliSense メタデータ](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   IntelliSense エラー マーカー: Data Lake Tools によって U-SQL と C# の編集エラーが下線で示されます。 
-   構文の強調表示: Data Lake Tools では、変数、キーワード、データ型、関数などの各種の要素を区別するために異なる色が使用されます。 

    ![Data Lake Tools for Visual Studio Code の構文の強調表示](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

   >[!NOTE]
   >2018 月 5 月 25 日に施行される新しい一般データ保護規則 (GDPR) に備えて、お使いの Azure Data Lake Tools for Visual Studio Code をバージョン 0.2.13 以降にアップグレードすることをお勧めします。 このバージョンには、最新のデータ保護要件に基づいた変更が含まれています。 以前のバージョンはダウンロードできません。また、推奨されていません。 
 
   
## <a name="next-steps"></a>次の手順
- [VSCode で Azure Data Lake Analytics の Python、R、および CSharp を使用して U-SQL を開発する](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Visual Studio Code を使用した U-SQL ローカル実行とローカル デバッグ](data-lake-tools-for-vscode-local-run-and-debug.md)
- [チュートリアル: Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
- [チュートリアル: Data Lake Tools for Visual Studio を使用して U-SQL スクリプトを開発する](data-lake-analytics-data-lake-tools-get-started.md)
- [Azure Data Lake Analytics ジョブの U-SQL アセンブリを開発する](data-lake-analytics-u-sql-develop-assemblies.md)




