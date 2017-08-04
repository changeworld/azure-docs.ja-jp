---
title: "Azure Data Lake Tools - Azure Data Lake Tools for Visual Studio Code の使用 | Microsoft Docs"
description: "Azure Data Lake Tools for Visual Studio Code を使用して U-SQL スクリプトを作成、テスト、実行する方法について説明します。 "
Keywords: "VScode,Azure Data Lake Tools,ローカル実行,ローカル デバッグ,ストレージ ファイルのプレビュー,ストレージ パスにアップロード"
services: data-lake-analytics
documentationcenter: 
author: jejiang
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/14/2017
ms.author: jejiang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 0ed3d7a0057eb446b3e1d16019ac74c641ae3138
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="use-the-azure-data-lake-tools-for-visual-studio-code"></a>Azure Data Lake Tools for Visual Studio Code の使用

Azure Data Lake Tools for Visual Studio Code (VSCode) を使用して U-SQL スクリプトを作成、テスト、実行する方法について説明します。  この情報は、次のビデオにも含まれています。

<a href="https://www.youtube.com/watch?v=J_gWuyFnaGA&feature=youtu.be"><img src="./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-video.png"></a>

## <a name="prerequisites"></a>前提条件

Data Lake Tools は、VSCode でサポートされている Windows、Linux、MacOS などのプラットフォームにインストールできます。 プラットフォームごとの前提条件については、以下を参照してください。

- Windows

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx)。
    - [Java SE Runtime Environment Version 8 Update 77 以降](https://java.com/download/manual.jsp)。 java.exe のパスをシステム環境変数パスに追加する必要があります。  手順については、「[how do I set or change the Path system variable? (Path システム変数を設定または変更するにはどうすればよいですか?)]( https://www.java.com/download/help/path.xml)」を参照してください。 このパスは、C:\Program Files\Java\jdk1.8.0_77\jre\bin のようなパスです。
    - [.NET Core SDK 1.0.3 または .NET Core 1.1 ランタイム](https://www.microsoft.com/net/download)。
    
- Linux (Ubuntu 14.04 LTS を推奨)

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx)。 次のコマンドを使用してインストールします。

        sudo dpkg -i code_<version_number>_amd64.deb

    - [Mono 4.2.x](http://www.mono-project.com/docs/getting-started/install/linux/)。 

        - 次のコマンドを実行して deb パッケージ ソースを更新します。

                sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF
                echo "deb http://download.mono-project.com/repo/debian wheezy/snapshots 4.2.4.4/main" | sudo tee /etc/apt/sources.list.d/mono-xamarin.list
                sudo apt-get update

        - 次のコマンドを実行して Mono をインストールします。

                sudo apt-get install mono-complete

            > [!NOTE] 
            > Mono 4.6 はサポートされていません。  バージョン 4.6 を完全にアンインストールしてから 4.2.x をインストールする必要があります。  

        - [Java SE Runtime Environment Version 8 Update 77 以降](https://java.com/download/manual.jsp)。 手順は[こちら]( https://java.com/en/download/help/linux_x64_install.xml)に記載されています。
        - [.NET Core SDK 1.0.3 または .NET Core 1.1 ランタイム](https://www.microsoft.com/net/download)。
- MacOS

    - [Visual Studio Code]( https://www.visualstudio.com/products/code-vs.aspx)。
    - [Mono 4.2.4](http://download.mono-project.com/archive/4.2.4/macos-10-x86/)。 
    - [Java SE Runtime Environment Version 8 Update 77 以降](https://java.com/download/manual.jsp)。 手順は[こちら](https://java.com/en/download/help/mac_install.xml)に記載されています。
    - [.NET Core SDK 1.0.3 または .NET Core 1.1 ランタイム](https://www.microsoft.com/net/download)。

## <a name="install-the-data-lake-tools"></a>Data Lake Tools のインストール

前提条件の各項目のインストールが完了したら、Data Lake Tools for VSCode をインストールできます。

**Data Lake Tools をインストールするには**

1. **Visual Studio Code** を開きます。
2. **Ctrl + P** キーを押し、次のコマンドを入力します。
```
ext install usql-vscode-ext
```
Visual Studio Code 拡張機能の一覧が表示されます。 そのうちの 1 つが **Azure Data Lake Tool** です。

3. **[Azure Data Lake Tool]** の横にある **[インストール]** をクリックします。 数秒後、[インストール] ボタンが [再度読み込む] に変わります。
4. **[再度読み込む]** をクリックして拡張機能をアクティブにします。
5. **[OK]** をクリックして確定します。 [拡張機能] ウィンドウに Azure Data Lake Tools が表示されます。
    ![Data Lake Tools for Visual Studio Code のインストール](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extensions.png)

## <a name="activate-azure-data-lake-tools"></a>Azure Data Lake Tools をアクティブ化する
新しい USQL ファイルを作成するか、既存の .USQL ファイルを開いて拡張機能を有効にしてください。 

## <a name="connect-to-azure"></a>Azure への接続

Azure Data Lake Analytics で U-SQL スクリプトをコンパイルして実行するには、まず Azure アカウントに接続する必要があります。

**Azure に接続するには**

1.  **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。 
2.  「**ADL: Login**」と入力します。 ログイン情報が出力ウィンドウに表示されます。

    ![Data Lake Tools for Visual Studio Code のコマンド パレット](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login.png)
    ![Data Lake Tools for Visual Studio Code のログイン情報](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-login-info.png)
3. Ctrl キーを押しながらログイン URL「https://aka.ms/devicelogin」をクリックして、ログイン Web ページを開きます。 コード「G567LX42V」をコピーし、下のテキスト ボックスに貼り付けてから、[続行] をクリックし次に進みます。

   ![Data Lake Tools for Visual Studio Code のログイン時に貼り付けるコード](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-extension-login-paste-code.png )   
4.  手順に従って Web ページからサインインします。 接続すると、VSCode ウィンドウの左下にあるステータス バーに Azure アカウント名が表示されます。 

    > [!NOTE] 
    > アカウントで 2 要素認証が有効になっている場合は、PIN ではなく電話認証を使用することをお勧めします。

サインオフするには、**ADL: Logout** コマンドを使用します。

## <a name="list-data-lake-analytics-accounts"></a>Data Lake Analytics アカウントを一覧表示します。

接続をテストするには、Data Lake Analytics アカウントを一覧表示します。

**Azure サブスクリプション内の Data Lake Analytics アカウントを一覧表示するには**

1. **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。
2. 「**ADL: List Accounts**」と入力します。  **[出力]** ウィンドウにアカウントが表示されます。

## <a name="open-sample-script"></a>サンプル スクリプトを開く

コマンド パレット (**Ctrl + Shift + P**) を使用して、**[ADL: サンプル スクリプトを開く]** を選択します。 このサンプルの別のインスタンスが開きます。 このインスタンスのスクリプトは編集、構成、送信することもできます。

## <a name="work-with-u-sql"></a>U-SQL の操作

U-SQL を操作するには、U-SQL ファイルまたはフォルダーを開く必要があります。

**U-SQL プロジェクトのフォルダーを開くには**

1. Visual Studio Code で **[ファイル]** メニューをクリックし、**[フォルダーを開く]** をクリックします。
2. フォルダーを指定し、**[フォルダーの選択]** をクリックします。
3. **[ファイル]** メニューをクリックし、**[新規ファイル]** をクリックします。 **Untilted-1** ファイルがプロジェクトに追加されます。
4. 次のコードをコピーして、Untitled-1 ファイルに貼り付けます。

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
            TO “/Output/departments.csv”

    このスクリプトにより、いくつかのデータを含む departments.csv ファイルを /output フォルダー内に作成されます。

5. 開いたフォルダーに **myUSQL.usql** という名前でファイルを保存します。 **adltools_settings.json** 構成ファイルもプロジェクトに追加される点に注意してください。
4. **adltools_settings.json** を開き、以下のプロパティで構成します。

    - アカウント: Azure サブスクリプション内の Data Lake Analytics アカウント。
    - データベース: 自分のアカウント内のデータベース。 既定値は master です。
    - スキーマ: データベース内のスキーマ。 既定値は dbo です。
    - オプション設定:
        - 優先順位: 優先順位の範囲は 1 ～ 1,000 で、1 が最も高い優先順位です。 既定値は 1,000 です。
        - 並列処理: 並列処理の範囲は 1 ～ 150 です。 既定値は、ADLA アカウントで許可される並列処理の最大数です。 
        
        > [!NOTE] 
        > 設定値が有効でない場合は、既定値が使用されます。

    ![Data Lake Tools for Visual Studio Code の構成ファイル](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-configuration-file.png)

    U-SQL ジョブのコンパイルと実行には、コンピューティング用の Data Lake Analytics アカウントが必要です。  U-SQL ジョブのコンパイルと実行を行う前に、このコンピューター アカウントを構成する必要があります。
    
    構成が保存されると、アカウント | データベース | スキーマ情報は、対応する USQL ファイルの左下にあるステータス バーに表示されます。 
 
 

ファイルを開く方法とは異なり、フォルダーを開く方法では以下の操作が可能です。

- 分離コード ファイルを使用する。  単一ファイル モードでは、分離コードがサポートされていません。
- 構成ファイルを使用する。 フォルダーを開くと、作業フォルダ―内のスクリプトで 1 つの構成ファイルが共有されます。


U-SQL スクリプトのコンパイルは、Data Lake Analytics サービスによってリモートで行われます。  コンパイル コマンドを発行すると、U-SQL スクリプトが Data Lake Analytics アカウントに送信されます。 コンパイル結果は後で Visual Studio Code が受信します。 このようにリモートでコンパイルを行うため、Visual Studio Code では、Data Lake Analytics アカウントに接続するための情報が構成ファイル内に必要です。

**U-SQL スクリプトをコンパイルするには**

1. **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。 
2. 「**ADL: Compile Script**」と入力します。 コンパイル結果が出力ウィンドウに表示されます。 スクリプト ファイルを右クリックし、**[ADL: Compile Script]** をクリックして U-SQL ジョブをコンパイルすることもできます。 コンパイル結果は出力ウィンドウに表示されます。
 

**U-SQL スクリプトを送信するには**

1. **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。 
2. 「**ADL: Submit Job**」と入力します。  スクリプト ファイルを右クリックし、**[ADL: Submit Job]** をクリックして U-SQL ジョブを送信することもできます。 

U-SQL ジョブを送信すると、送信ログが VSCode の出力ウィンドウに表示されます。 送信が成功した場合は、ジョブの URL も表示されます。 ジョブの URL を Web ブラウザーで開くと、リアルタイムのジョブの状態を追跡できます。

ジョブの詳細の出力を有効にするには、**vscode for u-sql_settings.json** ファイルで "jobInformationOutputPath" を設定します。
 
## <a name="use-code-behind-file"></a>分離コード ファイルの使用

分離コード ファイルは、1 つの U-SQL スクリプトに関連付けられている CSharp ファイルです。 分離コード ファイルでは、スクリプト専用の UDO/UDA/UDT/UDF を定義できます。 UDO/UDA/UDT/UDF は、アセンブリを先に登録しなくても、スクリプト内で直接使用できます。 分離コード ファイルは、対になる U-SQL スクリプト ファイルと同じフォルダーに置かれます。 スクリプトに xxx.usql という名前が付けられた場合、分離コードの名前は xxx.usql.cs となります。 分離コード ファイルを手動で削除すると、関連付けられている U-SQL スクリプトの分離コード機能が無効になります。 U-SQL スクリプト用のカスタム コードの記述方法の詳細については、「[Writing and Using Custom Code in U-SQL – User-Defined Functions (U-SQL でのカスタム コードの記述と使用 - ユーザー定義関数)]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/)」を参照してください。

分離コードをサポートするには、作業フォルダーを開く必要があります。 

**分離コード ファイルを生成するには**

1. ソース ファイルを開きます。 
2. **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。
3. 「**ADL: Generate Code Behind**」と入力します。  分離コード ファイルが同じフォルダーに作成されます。 

スクリプト ファイルを右クリックし、**[ADL: Generate Code Behind]** をクリックして、分離コード ファイルを生成することもできます。 

分離コードを含む U-SQL スクリプトのコンパイルと送信は、単独の U-SQL スクリプトの場合と同様です。

以下の 2 つのスクリーンショットは、分離コード ファイルとそれに関連付けられている U-SQL スクリプト ファイルを示したものです。
 
![Data Lake Tools for Visual Studio Code の分離コード](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind.png)

![Data Lake Tools for Visual Studio Code の分離コード](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-behind-call.png) 

## <a name="use-assemblies"></a>アセンブリの使用

アセンブリの開発に関する情報については、「[Develop U-SQL assemblies for Azure Data Lake Analytics jobs (Azure Data Lake Analytics ジョブの U-SQL アセンブリの開発)](data-lake-analytics-u-sql-develop-assemblies.md)」を参照してください。

Data Lake Tools を使用すると、カスタム コード アセンブリを Data Lake Analytics カタログに登録できます。

**アセンブリを登録するには**

「**ADL: Register Assembly**」 または「**ADL: Register Assembly through Configuration**」を使用してアセンブリを登録することができます。

**ADL: Register Assembly**
1.  **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。
2.  「**ADL: Register Assembly**」と入力します。 
3.  ローカル アセンブリのパスを指定します。 
4.  Data Lake Analytics アカウントを選択します。
5.  データベースを選択します。

結果: ポータルがブラウザーで開かれ、アセンブリの登録プロセスが表示されます。  

**ADL: Register Assembly** コマンドをトリガーする他の便利な方法として、エクスプローラーで dll ファイルを右クリックする方法があります。 

**ADL: Register Assembly through Configuration**
1.  **Ctrl + Shift + P** キーを押してコマンド パレットを開きます。
2.  「**ADL: Register Assembly through Configuration**」と入力します。 
3.  ローカル アセンブリのパスを指定します。 
4.  Json ファイルが表示されます。 アセンブリの依存関係とリソース パラメーターを確認して、必要な場合は編集します。 手順が出力ウィンドウに表示されます。 Json ファイルを保存 (**Ctrl + S**) し、アセンブリの登録を続行します。

![Data Lake Tools for Visual Studio Code の分離コード](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-register-assembly-advance.png)
>[!NOTE]
>- アセンブリの依存関係: Azure Data Lake Tools auto は、DLL が依存関係を持つかどうかを自動的に検出します。 依存関係が検出された場合は、Json ファイルに表示されます。 
>- リソース: DLL のリソース (txt、png、csv など) をアセンブリの登録の一部としてアップロードできます。 

**ADL: Register Assembly through Configuration** コマンドをトリガーするさらに便利な方法として、エクスプローラーで dll ファイルを右クリックする方法があります。 

次の U-SQL コードは、アセンブリを呼び出す方法を示しています。 このサンプルでは、アセンブリ名は *test* です。

```
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
```


## <a name="access-data-lake-analytics-catalog"></a>Data Lake Analytics カタログへのアクセス

Azure に接続した後は、以下の手順に従って U-SQL カタログにアクセスできます。

**Azure Data Lake Analytics メタデータにアクセスするには**

1.  **Ctrl + Shift + P** キーを押し、「**ADL: List Tables**」と入力します。
2.  いずれかの Data Lake Analytics アカウントをクリックします。
3.  いずれかの Data Lake Analytics データベースをクリックします。
4.  いずれかのスキーマをクリックします。 テーブルが表示されます。

## <a name="show-data-lake-analytics-jobs"></a>Data Lake Analytics ジョブを表示する

コマンド パレット (**Ctrl + Shift + P**) を使用して、**[ADL: ジョブの表示]** を選択します。 

1.  ADLA またはローカル アカウントを選択します。 
2.  アカウントのジョブの一覧が表示されるまで待機します。
3.  ジョブの一覧からジョブを選択すると、ADL Tools によってポータルにジョブの詳細が表示され、VSCode に JobInfo ファイルが表示されます。

![Data Lake Tools for Visual Studio Code の IntelliSense オブジェクト型](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="azure-data-lake-storage-adls-integration"></a>Azure Data Lake Storage (ADLS) の統合

VSCode で ADLS 関連コマンドを使用して、ADLS リソースのナビゲーションや ADLS ファイルのプレビューを行い、ADLS にファイルを直接アップロードすることができます。  
### <a name="list-storage-path"></a>ストレージ パスのリスト

コマンド パレット (**Ctrl + Shift + P**) を使用して、**[ADL: List Storage Path] \(ADL: ストレージ パスのリスト\)** を選択します。
1.  コマンド パレットを開き、コマンドを入力します。

    ![Data Lake Tools for Visual Studio Code のストレージ パスのリスト](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-storage.png)

2.  ストレージ パスをリストに加える方法を選択します。 ここでは、例として **[Enter a path] (パスを入力)** を使用しています。

    ![Data Lake Tools for Visual Studio Code のストレージ パス リスト選択方法](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)

    > [!NOTE]
    >- Vscode は最後にアクセスしたすべてのADLAアカウントのパスを保持します。 例： /tt/ss.
    >- Browser from root path: ADLA アカウントまたはローカルを選択して、ルート パスをリストに加えます。
    >- Enter a path: ADLA アカウントまたはローカルを選択して、指定したパスをリストに加えます。
    
3. ローカルまたは ADLA アカウントからアカウントを選択します。

    ![Data Lake Tools for Visual Studio Code で [その他] を選択](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4.  [その他] をクリックし、さらに ADLA アカウントを一覧表示して、ADLA アカウントを選択します。

    ![Data Lake Tools for Visual Studio Code のアカウントの選択](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

5.  Azure ストレージ パスを入力する 例: /output

       ![Data Lake Tools for Visual Studio Code のストレージ パスの入力](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-path.png)

6.  結果: コマンド パレットには、入力内容に基づくパス情報が一覧表示されます。

    ![Data Lake Tools for Visual Studio Code のストレージ パスの結果一覧](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-path.png)

相対パスの一覧を表示する便利な別の方法は、右クリック コンテキスト メニューを使用することです。

1.  パス文字列を右クリックし、[List Storage Path]\(ストレージ パスのリスト\) を選択します。

       ![Data Lake Tools for Visual Studio Code の右クリック コンテキスト メニュー](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)
2. コマンド パレットに、選択した相対パスが表示されます。

   ![Data Lake Tools for Visual Studio Code の選択したパスのリスト](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-relative-path.png)

3.  ローカルまたは ADLA アカウントからアカウントを選択します。

       ![Data Lake Tools for Visual Studio Code のアカウントの選択](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

4.  結果: コマンド パレットに、現在のパスのフォルダーおよびファイルが一覧表示されます。

       ![Data Lake Tools for Visual Studio Code の現在のパスのリスト](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-current.png)

### <a name="preview-storage-file"></a>ストレージ ファイルのプレビュー

コマンド パレット (**Ctrl + Shift + P**) を使用して、**[ADL: Preview Storage File] \(ADL: ストレージ ファイルのプレビュー\)** を選択します。
1.  コマンド パレットを開き、コマンドを入力します。

       ![Data Lake Tools for Visual Studio Code のファイルをプレビュー](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-preview.png)

2.  ローカルまたは ADLA からアカウントを選択します。

       ![Data Lake Tools for Visual Studio Code のアカウントのリスト](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  [その他] をクリックし、さらに ADLA アカウントを一覧表示して、ADLA アカウントを選択します。

       ![Data Lake Tools for Visual Studio Code のアカウントの選択](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-select-adla-account.png)

4.  Azure ストレージ パスまたはファイルを入力します。 例: /output/SearchLog.txt

       ![Data Lake Tools for Visual Studio Code のストレージ パスとファイルを入力](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

5.  結果: コマンド パレットには、入力内容に基づくパス情報が一覧表示されます。

       ![Data Lake Tools for Visual Studio Code のファイル結果をプレビュー](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

ファイルのプレビューを表示する便利な別の方法は、右クリック コンテキスト メニューを使用することです。

1.  プレビューするファイルのパスを右クリックします。

       ![Data Lake Tools for Visual Studio Code の右クリック コンテキスト メニュー](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-preview.png)

2.  ローカルまたは ADLA アカウントからアカウントを選択します。

       ![Data Lake Tools for Visual Studio Code のアカウントの選択](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

3.  結果: VSCode に、ファイルのプレビュー結果が表示されます。

       ![Data Lake Tools for Visual Studio Code のファイル結果をプレビュー](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-preview-results.png)

### <a name="upload-file"></a>ファイルのアップロード 

**ADL: Upload File** または **ADL: Upload File through Configuration** のコマンドを使用してファイルをアップロードすることができます。

**ADL: Upload File**
1.  Ctrl + Shift + P キーを押してコマンド パレットを開くか、スクリプト エディターを右クリックして、「**Upload File**」と入力します。
2. アップロードするローカル パスを入力します。

    ![Data Lake Tools for Visual Studio Code でのローカル パスの入力](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-input-local-path.png)

3. ストレージ パスをリストに加える方法を選択します。 ここでは、例として **[Enter a path] (パスを入力)** を使用しています。

    ![Data Lake Tools for Visual Studio Code のストレージ パスのリスト](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account-selectoneway.png)
    >[!NOTE]
    >- Vscode は最後にアクセスしたすべてのADLAアカウントのパスを保持します。 例： /tt/ss.
    >- Browser from root path: ADLA アカウントまたはローカルを選択して、ルート パスをリストに加えます。
    >- Enter a path: ADLA アカウントまたはローカルを選択して、指定したパスをリストに加えます。

4. ローカルまたは ADLA アカウントからアカウントを選択します。

    ![Data Lake Tools for Visual Studio Code の右クリック ストレージ](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-list-account.png)

5.  Azure ストレージ パスを入力する 例: /output/

       ![Data Lake Tools for Visual Studio Code のストレージ パスの入力](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-input-preview-file.png)

6. Azure ストレージ パスを入力してリストに加えます。 **[Choose current folder] (現在のフォルダーを選択)** を選択します。

    ![Data Lake Tools for Visual Studio Code のフォルダーの選択](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-choose-current-folder.png)

7.  結果: 出力ウィンドウに、ファイル アップロードのステータスが表示されます。

       ![Data Lake Tools for Visual Studio Code のアップロード ステータス](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)    

**ADL: Upload File through Configuration**
1.  Ctrl + Shift + P キーを押してコマンド パレットを開くか、スクリプト エディターを右クリックして、「**Upload File through Configuration**」と入力します。
2.  VSCode に、json ファイルが表示されます。 ファイルのパスを入力し、同時に複数のファイルをアップロードできます。 手順が出力ウィンドウに表示されます。 Json ファイルを保存 (**Ctrl + S**) し、ファイルのアップロードを続行します。

       ![Data Lake Tools for Visual Studio Code の入力ファイル](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-file.png)

3.  結果: 出力ウィンドウに、ファイル アップロードのステータスが表示されます。

       ![Data Lake Tools for Visual Studio Code のアップロード ステータス](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-upload-status.png)     

ファイルをストレージにアップロードする別の便利な方法として、スクリプト エディターでファイルのフルパスまたはファイルの相対パスの右クリック メニューを使用する方法があります。 ローカル ファイル パスを入力し、アカウントを選択すると、出力ウィンドウにアップロード ステータスが表示されます。 

### <a name="open-web-azure-storage-explorer"></a>Web Azure ストレージ エクスプローラーを開く
コマンド **[ADL: Open Web Azure Storage Explorer] (Web Azure ストレージ エクスプローラーを開く)** を使用するか、右クリック メニューで、**Web Azure ストレージ エクスプローラー**を開くことができます。

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2. 「**Open Web Azure Storage Explorer**」と入力するか、スクリプト エディターで相対パスまたは完全なパスを右クリックして、**[Open Web Azure Storage Explorer] (Web Azure ストレージ エクスプローラーを開く)** を選択します。
3. Data Lake Analytics アカウントを選択します。

ADL Tools によってポータルに Azure ストレージ パスが開かれます。 パスにアクセスして Web でファイルのプレビューを確認できます。

### <a name="local-run-and-local-debug-windows-users"></a>Windows ユーザーのローカル実行とローカル デバッグ
U-SQL Local Run は ADLA にコードをパブリッシュする前に、ローカル データ のテスト、ローカルでのスクリプト検証をするために導入されました。 ローカル デバッグ機能を使用すると、C# 分離コードのデバッグ、コードの実行、ローカルでのスクリプトの検証を ADLA に送信する前に行うことができます。 手順については、「[U-SQL local run and local debug with Visual Studio Code (Visual Studio Code を使用した U-SQL ローカル実行とローカル デバッグ)](data-lake-tools-for-vscode-local-run-and-debug.md)」をご覧ください。

## <a name="additional-features"></a>その他の機能

Data Lake Tools for VSCode では、以下の各機能がサポートされています。

-   IntelliSense オートコンプリート。 キーワード、メソッド、変数などの候補がポップアップ表示されます。以下に示すさまざまなオブジェクト型が、異なるアイコンで表されます。

    - Scala データ型
    - 複合データ型
    - 組み込みの UDT
    - .NET コレクションおよびクラス
    - C# 式
    - 組み込みの C# UDF、UDO、UDAAG 
    - U-SQL 関数
    - U-SQL ウィンドウ化関数
 
    ![Data Lake Tools for Visual Studio Code の IntelliSense オブジェクト型](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)
 
-   Data Lake Analytics メタデータでの IntelliSense オートコンプリート。 Data Lake Tools によって Data Lake Analytics メタデータ情報がローカルにダウンロードされます。  IntelliSense 機能により、Data Lake Analytics メタデータからデータベース、スキーマ、テーブル、ビュー、TVF、プロシージャ、C# アセンブリなどのオブジェクトが自動的に入力されます。
 
    ![Data Lake Tools for Visual Studio Code の IntelliSense メタデータ](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

-   IntelliSense エラー マーカー。 Data Lake Tools によって U-SQL と C# の編集エラーが下線で示されます。 
-   構文の強調表示。 Data Lake Tools では、変数、キーワード、データ型、関数などを区別するために異なる色が使用されます。 

    ![Data Lake Tools for Visual Studio Code の構文の強調表示](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>次のステップ

- Visual Studio Code を使用した U-SQL ローカル実行とローカル デバッグについては、「[U-SQL local run and local debug with Visual Studio Code (Visual Studio Code を使用した U-SQL ローカル実行とローカル デバッグ)](data-lake-tools-for-vscode-local-run-and-debug.md)」をご覧ください。
- Data Lake Analytics の概要については、[Azure Data Lake Analytics の使用開始に関するチュートリアル](data-lake-analytics-get-started-portal.md)のページを参照してください。
- Data Lake Tools for Visual Studio の使用方法については、「[チュートリアル: Data Lake Tools for Visual Studio を使用する U-SQL スクリプトの開発](data-lake-analytics-data-lake-tools-get-started.md)」を参照してください。
- アセンブリの開発に関する情報については、「[Develop U-SQL assemblies for Azure Data Lake Analytics jobs (Azure Data Lake Analytics ジョブの U-SQL アセンブリの開発)](data-lake-analytics-u-sql-develop-assemblies.md)」を参照してください。




