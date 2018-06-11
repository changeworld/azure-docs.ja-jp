---
title: Azure Data Lake Tools for Visual Studio Code の使用
description: Azure Data Lake Tools for Visual Studio Code を使用して U-SQL スクリプトを作成、テスト、実行する方法について説明します。
services: data-lake-analytics
author: Jejiang
ms.author: jejiang
manager: kfile
editor: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: 4f5d51a2a34e89223f51f456f2c730835b1e2451
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2018
ms.locfileid: "34735189"
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

## <a name="install-azure-data-lake-tools"></a>Azure Data Lake Tools をインストールする

前提条件の各項目のインストールが完了したら、Azure Data Lake Tools for VS Code をインストールできます。

**Azure Data Lake Tools をインストールするには**

1. Visual Studio Code を開きます。
2. 左側のウィンドウで **[拡張機能]** をクリックします。 検索ボックスに「**Azure Data Lake**」と入力します。
3. **[Azure Data Lake Tools]** の横にある **[インストール]** をクリックします。 数秒後、**[インストール]** ボタンが **[再読み込み]** に変わります。
4. **[再度読み込む]** をクリックして、**Azure Data Lake Tools** 拡張機能をアクティブにします。
5. **[ウィンドウの再読み込み]** をクリックして確認します。 **[拡張機能]** ウィンドウに Azure Data Lake Tools が表示されます。

    ![Data Lake Tools for Visual Studio Code の [拡張機能] ウィンドウ](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

 
## <a name="activate-azure-data-lake-tools"></a>Azure Data Lake Tools をアクティブ化する
新しい .usql ファイルを作成するか、既存の .usql ファイルを開いて拡張機能をアクティブにしてください。 


## <a name="work-with-u-sql"></a>U-SQL の操作

U-SQL を操作するには、U-SQL ファイルまたはフォルダーを開く必要があります。

**サンプル スクリプトを開くには**

コマンド パレットを開いて (Ctrl + Shift + P)、「**ADL: Open Sample Script**」と入力します。 このサンプルの別のインスタンスが開きます。 このインスタンスのスクリプトは編集、構成、送信することもできます。

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


## <a name="work-with-code-behind-file-csharp-python-and-r"></a>分離コード ファイルの操作: CSharp、Python、および R

Azure Data Lake Tool では複数のカスタム コードがサポートされています。手順については、[VSCode での Python、R、および CSharp を使用した Azure Data Lake Analytics の U-SQL の開発](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)に関するページをご覧ください。

## <a name="work-with-assemblies"></a>アセンブリの操作

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


## <a name="u-sql-local-run-and-local-debug-for-windows-users"></a>Windows ユーザーの U-SQL のローカル実行とローカル デバッグ
U-SQL ローカル実行は、Data Lake Analytics にコードを発行する前に、ローカル データをテストし、スクリプトをローカルで検証するものです。 作成したコードを Data Lake Analytics に送信する前に、ローカル デバッグ機能によって次のタスクを実行することができます。 
- C# 分離コードをデバッグする。 
- コードをステップ実行する。 
- スクリプトをローカルで検証する。

ローカル実行とローカル デバッグの手順については、「[Visual Studio Code を使用した U-SQL ローカル実行とローカル デバッグ](data-lake-tools-for-vscode-local-run-and-debug.md)」を参照してください。


## <a name="connect-to-azure"></a>Azure への接続

Data Lake Analytics で U-SQL スクリプトをコンパイルして実行するには、まず Azure アカウントに接続する必要があります。

<b id="sign-in-by-command"> コマンドを使用して Azure に接続するには</b>

1.  Ctrl + Shift + P キーを押してコマンド パレットを開きます。 
2.  「**ADL: Login**」と入力します。 右下にログイン情報が表示されます。

    ![Data Lake Tools for Visual Studio Code のコマンド パレット](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)

    ![Data Lake Tools for Visual Studio Code のデバイス ログイン情報](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)

3.  **[Copy & Open]\(コピーして開く\)** をクリックし、URL として https://aka.ms/devicelogin を指定してログイン Web ページを開きます。 テキスト ボックスにコードを貼り付け、**[続行]** を選択します。

    ![Data Lake Tools for Visual Studio Code のログイン時に貼り付けるコード](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )  
     
4.  手順に従って Web ページからサインインします。 接続すると、**VS Code** ウィンドウの左下隅のステータス バーに Azure アカウント名が表示されます。 

    > [!NOTE] 
    >- Data Lake Tool では、以前にサインインしていてまだログアウトしていない場合、次回は自動でサインインされます。
    >- アカウントで 2 要素認証が有効になっている場合は、PIN ではなく電話認証の使用をお勧めします。


サインアウトするには、**ADL: Logout** コマンドを入力します。

**エクスプローラーから Azure に接続するには**

エクスプローラーからサインインするには、**[AZURE DATALAKE]** を展開し、**[Azure へのサインイン...]** をクリックし、「[**コマンドを使用して Azure に接続する**](#sign-in-by-command)」の手順 3 と手順 4 に従います。

![エクスプローラーから Azure に接続する](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-sign-in-from-explorer.png )  

エクスプローラーからサインアウトすることはできません。 サインアウトするには、「[**コマンドを使用して Azure に接続するには**](#sign-in-by-command)」を参照してください。


## <a name="create-extract-script"></a>EXTRACT スクリプトを作成する 
**[ADL: Create EXTRACT Script]** (ADL: EXTRACT スクリプトの作成) コマンドを使用するか、**AZURE DATALAKE** エクスプローラーから、.csv ファイル、.tsv ファイル、または .txt ファイル用の EXTRACT スクリプトを作成できます。

**コマンドを使用して EXTRACT スクリプトを作成するには**

1. Ctrl + Shift + P を押してコマンド パレットを開き、「**ADL: Create EXTRACT Script**」(ADL: EXTRACT の作成) と入力します。
2. Azure ストレージ ファイルの完全なパスを指定し、**Enter** キーを押します。
3. 1 つのアカウントを選択します。
4. .txt ファイルの場合は、ファイルを抽出するための区切り記号を選択します。 

    ![EXTRACT スクリプトの作成プロセス](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-process.png)

入力に基づいて EXTRACT スクリプトが生成されます。 列を検出できないスクリプトの場合は、次の 2 つのオプションのいずれかを選択します。 それ以外の場合は、1 つのスクリプトのみが生成されます。

![EXTRACT スクリプトの作成結果](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-result.png)

**エクスプローラーから EXTRACT スクリプトを作成するには**

EXTRACT スクリプトを作成する別の方法は、Data Lake Storage または Blob Storage 内の .csv ファイル、.tsv ファイル、または .txt ファイルの右クリック メニューを使用することです。

![コンテキスト メニューから EXTRACT スクリプトを作成する](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu.png)

## <a name="integrate-with-azure-data-lake-analytics-through-command"></a>コマンドを使用して Azure Data Lake Analytics と統合する

Azure Data Lake Analytics リソースにアクセスして、アカウントの表示、メタデータへのアクセス、分析ジョブの表示を実行できます。 

**Azure サブスクリプション内の Azure Data Lake Analytics アカウントを一覧表示するには**

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2. 「**ADL: List Accounts**」と入力します。 **[出力]** ウィンドウにアカウントが表示されます。

**Azure Data Lake Analytics メタデータにアクセスするには**

1.  Ctrl + Shift + P キーを押し、「**ADL: List Tables**」と入力します。
2.  いずれかの Data Lake Analytics アカウントを選択します。
3.  いずれかの Data Lake Analytics データベースを選択します。
4.  いずれかのスキーマを選択します。 テーブルの一覧が表示されます。

**Azure Data Lake Analytics ジョブを表示するには**
1.  コマンド パレットを開いて (Ctrl + Shift + P)、**[ADL: Show Job]** を選択します。 
2.  Data Lake Analytics アカウントまたはローカル アカウントを選択します。 
3.  アカウントのジョブの一覧が表示されるまで待機します。
4.  ジョブ リストからジョブを選択すると、Data Lake Tools によって右側のウィンドウにジョブ ビューが開かれ、VS Code **OUTPUT** に情報が表示されます。

    ![Data Lake Tools for Visual Studio Code の IntelliSense オブジェクト型](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-storage-through-command"></a>コマンドを使用して Azure Data Lake Storage と統合する

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


## <a name="integrate-with-azure-data-lake-analytics-from-explorer"></a>エクスプローラーから Azure Data Lake Analytics と統合する

- ログインすると、**[AZURE DATALAKE]** の左側のパネルに、現在の Azure アカウントに含まれるサブスクリプションがすべて一覧表示されます。 

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

## <a name="integrate-with-azure-data-lake-storage-from-explorer"></a>エクスプローラーから Azure Data Lake Storage と統合する

**[Data Lake Store]** に移動します

 - フォルダー ノードの右クリック コンテキスト メニューでは、**[更新]**、**[削除]**、**[アップロード]**、**[フォルダーをアップロード]**、**[相対パスのコピー]**、**[完全パスのコピー]** を実行できます。

   ![DataLake エクスプローラー](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- ファイル ノードの右クリック コンテキスト メニューでは、**[プレビュー]**、**[ダウンロード]**、**[削除]**、**[EXTRACT スクリプトの作成]** (CSV、TSV、および TXT ファイルでのみ使用可能)、**[相対パスのコピー]** および **[完全パスのコピー]** を実行できます。

    ![DataLake エクスプローラー - 抽出](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-explorer"></a>エクスプローラーから Azure Blob Storage と統合する

**[Blob ストレージ]** に移動します

- BLOB コンテナー ノードの右クリック コンテキスト メニューでは、**[更新]**、**[BLOB コンテナーの削除]**、および **[BLOB のアップロード]** を実行できます。

    ![[Blob ストレージ] の BLOB コンテナー ノード](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- フォルダー ノードの右クリック コンテキスト メニューでは、**[更新]** と **[BLOB のアップロード]** を実行できます。

    ![[Blob ストレージ] のフォルダー ノード](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- ファイル ノードの右クリック コンテキスト メニューでは、**[プレビュー/編集]**、**[ダウンロード]**、**[削除]**、**[EXTRACT スクリプトの作成]** (CSV、TSV、および TXT ファイルでのみ使用可能)、**[相対パスのコピー]** および **[完全パスのコピー]** を実行できます。

    ![コンテキスト メニューから EXTRACT スクリプトを作成する](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-adl-storage-explorer-in-portal"></a>ポータルで ADL ストレージ エクスプローラーを開く
1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2. 「**Open Web Azure Storage Explorer**」と入力するか、スクリプト エディターで相対パスまたは完全なパスを右クリックして、**[Open Web Azure Storage Explorer]\(Web Azure Storage Explorer を開く\)** を選択します。
3. Data Lake Analytics アカウントを選択します。

Data Lake Tools によって、Azure Portal に Azure ストレージ パスが表示されます。 そのパスを見つけて Web からファイルをプレビューすることができます。

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

   > [!NOTE]
   > Microsoft では、Azure Data Lake Tools for Visual Studio バージョン 2.3.3000.4 以降にアップグレードすることをお勧めしています。 以前のバージョンはダウンロードできなくなり、非推奨になっています。  
   
## <a name="next-steps"></a>次の手順
- [VSCode で Azure Data Lake Analytics の Python、R、および CSharp を使用して U-SQL を開発する](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Visual Studio Code を使用した U-SQL ローカル実行とローカル デバッグ](data-lake-tools-for-vscode-local-run-and-debug.md)
- [チュートリアル: Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
- [チュートリアル: Data Lake Tools for Visual Studio を使用して U-SQL スクリプトを開発する](data-lake-analytics-data-lake-tools-get-started.md)
