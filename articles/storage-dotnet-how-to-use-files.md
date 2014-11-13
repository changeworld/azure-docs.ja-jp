<properties urlDisplayName="File Service" pageTitle="Azure File ストレージを使用する方法 | Microsoft Azure" metaKeywords="Get started Azure file  Azure file share  Azure file shares  Azure file   Azure file storage   Azure file .NET   Azure file C#   Azure file PowerShell" description="Microsoft Azure File ストレージを使用してファイル共有を作成し、ファイルの内容を管理する方法について説明します。サンプルは PowerShell および C# で記述されています。" metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter=".NET" title=".NET で Microsoft Azure File ストレージを使用する方法" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# Azure File ストレージを使用する方法

この概要ガイドでは、Microsoft Azure File ストレージの基本的な使用方法を説明します。最初に PowerShell を使用して新しい Azure File 共有を作成する方法を示します。次に、ディレクトリを追加する方法、ローカル ファイルを共有にアップロードする方法、ディレクトリ内のファイルを一覧表示する方法を示します。その後、Azure の仮想マシンのファイル共有を、一般的な SMB 共有と同じようにマウントする方法を説明します。

Azure の仮想マシンやクラウド サービスだけでなく、内部設置型のアプリケーションから共有内のファイルにアクセスする必要があるユーザーのために、Azure .NET Storage クライアント ライブラリを使用してデスクトップ アプリケーションからファイル共有を利用する方法を説明します。

> [WACOM.NOTE] このガイドの .NET コードの例を実行するには、Azure .NET Storage クライアント ライブラリ 4.x 以降が必要になります。Storage クライアント ライブラリは、[NuGet][NuGet] で入手できます。

## 目次

-   [File ストレージとは][File ストレージとは]
-   [File ストレージの概念][File ストレージの概念]
-   [Azure のストレージ アカウントの作成][Azure のストレージ アカウントの作成]
-   [PowerShell を使用したファイル共有の作成][PowerShell を使用したファイル共有の作成]
-   [Azure の仮想マシンからの共有のマウント][Azure の仮想マシンからの共有のマウント]
-   [File ストレージを利用する内部設置型アプリケーションの作成][File ストレージを利用する内部設置型アプリケーションの作成]
-   [次のステップ][次のステップ]

## <a name="what-is-file-storage"></a><span class="short-header">Azure File ストレージとは</span>Azure File ストレージとは

File ストレージは、標準の SMB 2.1 プロトコルを使用して、アプリケーション用の共有ストレージを提供します。Microsoft Azure の仮想マシンおよびクラウド サービスでは、マウントされている共有を介して、アプリケーション コンポーネント間でファイル データを共有できます。内部設置型のアプリケーションでは、File ストレージ API を介して、共有内のファイル データにアクセスできます。

Azure の仮想マシンまたはクラウド サービスで実行しているアプリケーションでは、デスクトップ アプリケーションで通常の SMB 共有をマウントする場合と同じように、ファイル ストレージ共有をマウントすることで、ファイル データにアクセスできます。File ストレージ共有は、任意の数の Azure 仮想マシンまたはロールが同時にマウントしてアクセスすることができます。

File ストレージ共有は標準の SMB 2.1 ファイル共有であるため、Azure で実行されているアプリケーションは、ファイル I/O API を介して共有内のデータにアクセスできます。そのため、開発者は、既存のコードとスキルを活用して、既存のアプリケーションを移行することができます。IT プロフェッショナルは、Azure アプリケーションの管理の一環として、PowerShell コマンドレットを使用して、ファイル ストレージ共有を作成、マウント、管理できます。このガイドでは、両方の例を紹介します。

File ストレージの一般的な用途には、次のようなものがあります。

-   ファイル共有を利用している内部設置型のアプリケーションを、コストの高い書き換えを行わずに、Azure の仮想マシンやクラウド サービス上で実行できるように移行する
-   共有のアプリケーション設定を構成ファイルなどに格納する
-   ログ、メトリック、クラッシュ ダンプなどの診断データを共有の場所に格納する
-   Azure の仮想マシンまたはクラウド サービスの開発や管理に必要なツールおよびユーティリティを格納する

## <a name="file-storage-concepts"></a><span class="short-header">File ストレージの概念</span>File ストレージの概念

File ストレージには次の構成要素があります。

![files-concepts][files-concepts]

-   **ストレージ アカウント:**Azure のストレージにアクセスする場合には必ず、
    ストレージ アカウントを使用します。ストレージ アカウントの容量の詳細については、[Azure のストレージの拡張性とパフォーマンスのターゲットに関するページ][Azure のストレージの拡張性とパフォーマンスのターゲットに関するページ]を参照してください。

-   **共有:** File ストレージ共有は、Azure 内の SMB 2.1 ファイル共有です。
    ディレクトリとファイルはすべて親の共有に作成する必要があります。アカウントに含まれる共有の数と、
    共有に格納できるファイル数には制限がなく、
    ストレージ アカウントの容量の上限まで増やすことができます。

-   **ディレクトリ:** ディレクトリの階層 (オプション)。

-   **ファイル:** 共有内のファイル。ファイルのサイズの上限は 1 TB です。

-   **URL 形式:** ファイルは次の URL 形式でアドレス指定
    されます。
    <https://>`<storage account>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`

    次の例の URL を使用すると、上の図のいずれかのファイルを
    アドレス指定できます。
    `http://acmecorp.file.core.windows.net/cloudfiles/diagnostics/log.txt`

共有、ディレクトリ、およびファイルの詳しい命名方法については、「[Naming and Referencing Shares, Directories, Files, and Metadata (共有、ディレクトリ、ファイル、およびメタデータの命名と参照)][Naming and Referencing Shares, Directories, Files, and Metadata (共有、ディレクトリ、ファイル、およびメタデータの命名と参照)]」を参照してください。

## <a name="create-account"></a><span class="short-header">Azure の Storage アカウントの作成</span>Azure の Storage アカウントの作成

Azure File ストレージは現在プレビュー段階にあります。このプレビュー機能の利用を申し込むには、[Microsoft Azure プレビューのページ][Microsoft Azure プレビューのページ]で、**Azure Files** の利用を申し込んでください。申し込みが承認されると、File ストレージ プレビューを利用できるようになったという通知が送られます。その後で、File ストレージにアクセスするストレージ アカウントを作成できます。

> [WACOM.NOTE] File ストレージは現在、新規のストレージ アカウントでのみ利用できます。File ストレージ アカウントへのアクセスがサブスクリプションに許可された後で、このガイドで使用する新規ストレージ アカウントを作成してください。

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="use-cmdlets"></a><span class="short-header">PowerShell を使用したファイル共有の作成</span>PowerShell を使用したファイル共有の作成

### Azure Storage 用の PowerShell コマンドレットのインストール

PowerShell の使用を準備するために、Azure PowerShell コマンドレットをダウンロードしてインストールします。インストール先とインストール方法については、「[Azure PowerShell のインストールおよび構成方法][Azure PowerShell のインストールおよび構成方法]」を参照してください。

> [WACOM.NOTE] File サービス用の PowerShell コマンドレットは、最新の Azure PowerShell モジュール (Version 0.8.5 以降) でのみ提供されています。最新の Azure PowerShell モジュールをダウンロードしてインストールするか、アップグレードすることをお勧めします。

**[スタート]** をクリックし、「**Windows Azure PowerShell**」と入力して、Azure PowerShell ウィンドウを開きます。Azure PowerShell ウィンドウに自動的に Azure Powershell モジュールが読み込まれます。

### ストレージ アカウントおよびキーのコンテキストの作成

次に、ストレージ アカウント コンテキストを作成します。コンテキストによってアカウント名とアカウント キーがカプセル化されます。次の例の `account-name` と `account-key` は、実際のアカウント名とキーに置き換えてください。

    # create a context for account and key
    $ctx=New-AzureStorageContext account-name account-key

### 新しいファイル共有の作成

次に、新しいファイル共有を作成します。この例では `sampleshare` という名前を付けています。

    # create a new share
    $s = New-AzureStorageShare sampleshare -Context $ctx

これで、File ストレージにファイル共有が作成されました。次に、ディレクトリとファイルを追加します。

### ファイル共有でのディレクトリの作成

次に、共有内にディレクトリを作成します。次の例では、ディレクトリに `sampledir` という名前を付けています。

    # create a directory in the share
    New-AzureStorageDirectory -Share $s -Path sampledir

### ディレクトリへのローカル ファイルのアップロード

次に、ディレクトリにローカル ファイルをアップロードします。次の例では、`C:\temp\samplefile.txt` からファイルをアップロードします。ファイル パスを編集して、ローカル マシン上の有効なファイルを指定してください。

    # upload a local file to the new directory
    Set-AzureStorageFileContent -Share $s -Source C:\temp\samplefile.txt -Path sampledir

### ディレクトリ内のファイルの一覧表示

ディレクトリ内のファイルを一覧表示して、ファイルを確認できます。このコマンドを実行するとサブディレクトリも表示されますが、この例ではサブディレクトリがないため、ファイルのみが一覧表示されます。

    # list files in the new directory
    Get-AzureStorageFile -Share $s -Path sampledir

## <a name="mount-share"></a><span class="short-header">Azure の仮想マシンからの共有のマウント</span>Azure の仮想マシンからの共有のマウント

Azure のファイル共有をマウントする方法を示すために、ここでは Azure の仮想マシンを作成し、リモート接続して、共有をマウントします。

1.  最初に、「[Windows Server を実行する仮想マシンの作成][Windows Server を実行する仮想マシンの作成]」の説明に従って、Azure の仮想マシンを新規作成します。
2.  次に、「[Windows Server を実行する仮想マシンにログオンする方法][Windows Server を実行する仮想マシンにログオンする方法]」の説明に従って仮想マシンにリモート接続します。
3.  仮想マシンで PowerShell ウィンドウを開きます。

### ストレージ アカウントの資格情報を仮想マシンに適用

ファイル共有をマウントする前に、ストレージ アカウントの資格情報を仮想マシンにも適用します。そうすることで、仮想マシンが再起動したときに Windows が自動的にファイル共有に再接続します。アカウントの資格情報を仮想マシンに適用するには、仮想マシンの PowerShell ウィンドウで `cmdkey` コマンドを実行します。`<storage-account>` を実際のストレージ アカウントの名前に置き換え、`<account-key>` を実際のストレージ アカウント キーに置き換えてください。

    cmdkey /add:<storage-account>.file.core.windows.net /user:<storage-account> /pass:<account-key>

これで、仮想マシンの再起動時に Windows が自動的にファイル共有に再接続するようになります。共有に再接続したことを確認するには、PowerShell ウィンドウ内から `net use` コマンドを実行します。

### 適用された資格情報を使用したファイル共有のマウント

仮想マシンにリモート接続した後で、`net use` コマンドを実行してファイル共有をマウントできます。次の構文を使用します。`<storage-account>` を実際のストレージ アカウントの名前に置き換え、`<share-name>` を実際の File ストレージ共有の名前に置き換えてください。

    net use z: \<storage-account>.file.core.windows.netnet use z: \\<storage-account>.file.core.windows.net\<share-name>lt;share-name>

> [WACOM.NOTE] 前の手順でストレージ アカウントの資格情報を適用したため、`net use` コマンドで資格情報を指定する必要はありません。資格情報をまだ適用していない場合は、`net use` コマンドのパラメーターで資格情報を指定してください。`<storage-account>` を実際のストレージ アカウントの名前に置き換え、`<share-name>` を実際の File ストレージ共有の名前に置き換え、さらに `<account-key>` を実際のストレージ アカウント キーに置き換えてください。

    net use z: \<storage-account>.file.core.windows.netnet use z: \\<storage-account>.file.core.windows.net\<share-name> /u:<storage-account> <account-key>lt;share-name> /u:<storage-account> <account-key>

これで、通常のドライブと同じように仮想マシンの中から File ストレージ共有を利用できるようになります。コマンド プロンプトから標準のファイル コマンドを発行したり、マウントした共有とその内容をエクスプローラーで表示したりできます。.NET Framework の [System.IO 名前空間][System.IO 名前空間]の API など、標準の Windows ファイル I/O API を使用してファイル共有にアクセスするコードを仮想マシン内で実行することもできます。

さらに、ロールにリモート接続することで、Azure クラウド サービスで実行されるロールからファイル共有をマウントすることもできます。

## <a name="create-console-app"></a><span class="short-header">File ストレージを利用する内部設置型アプリケーションの作成</span>File ストレージを利用する内部設置型アプリケーションの作成

これまで説明したように、仮想マシンや Azure 内で実行されるクラウド サービスの中から File ストレージ共有をマウントすることができます。ただし、内部設置型のアプリケーションから File ストレージ共有をマウントすることはできません。内部設置型のアプリケーションから共有データにアクセスするには、File ストレージ API を使用する必要があります。この例では、[Azure .NET Storage クライアント ライブラリ][Azure .NET Storage クライアント ライブラリ]を使用してファイル共有を利用する方法を示します。

内部設置型のアプリケーションから API を使用する方法を示すために、ここではデスクトップ上で動作する簡単なコンソール アプリケーションを作成します。

### コンソール アプリケーションの作成とアセンブリの取得

Visual Studio で新しいコンソール アプリケーションを作成して Azure Storage NuGet パッケージをインストールするには、次の手順を実行します。

1.  Visual Studio で、**[ファイル]**、**[新しいプロジェクト]** の順にクリックした後、**[Windows]** をクリックし、Visual C# テンプレートの一覧から **[コンソール アプリケーション]** をクリックします。
2.  コンソール アプリケーションの名前を入力して、**[OK]** をクリックします。
3.  プロジェクトが作成されたら、ソリューション エクスプローラーでプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。"WindowsAzure.Storage" をオンライン検索し、**[インストール]** をクリックして Azure Storage のパッケージと依存関係をインストールします。

### ストレージ アカウントの資格情報を app.config ファイルに保存

次に、プロジェクトの app.config ファイルに資格情報を保存します。次の例のようになるように app.config ファイルを編集します。ここで、`myaccount` は実際のストレージ アカウント名に置き換え、`mykey` は実際のストレージ アカウント キーに置き換えてください。

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <startup> 
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
        </startup>
        <appSettings>
            <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey" />
        </appSettings>
    </configuration>

> [WACOM.NOTE] 最新バージョンの Azure ストレージ エミュレーターでは、File ストレージがサポートされません。接続文字列は、Azure Files プレビュー機能にアクセスできる、クラウド内の Azure ストレージ アカウントを対象とする必要があります。

### 名前空間宣言の追加

ソリューション エクスプローラーで program.cs ファイルを開き、このファイルに次の名前空間宣言を追加します。

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.File;

### プログラムによる接続文字列の取得

`Microsoft.WindowsAzure.CloudConfigurationManager` クラスまたは `System.Configuration.ConfigurationManager` クラスを使用して、app.config ファイルから保存済みの資格情報を取得することができます。この例では、`CloudConfigurationManager` クラスを使用して資格情報を取得し、それを `CloudStorageAccount` クラスによってカプセル化する方法を示します。program.cs の `Main()` メソッドに、次のコードを追加します。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

### プログラムによる File ストレージ共有へのアクセス

次に、前に示したコードの後で、`Main()` メソッドに次のコードを追加し、接続文字列を取得します。このコードは、前の手順で作成したファイルへの参照を取得し、その内容をコンソール ウィンドウに出力します。

    //Create a CloudFileClient object for credentialed access to File storage.
    CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

    //Get a reference to the file share we created previously.
    CloudFileShare share = fileClient.GetShareReference("sampleshare");

    //Ensure that the share exists.
    if (share.Exists())
    {
        //Get a reference to the root directory for the share.
        CloudFileDirectory rootDir = share.GetRootDirectoryReference();

        //Get a reference to the sampledir directory we created previously.
        CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("sampledir");

        //Ensure that the directory exists.
        if (sampleDir.Exists())
        {
            //Get a reference to the file we created previously.
            CloudFile file = sampleDir.GetFileReference("samplefile.txt");

            //Ensure that the file exists.
            if (file.Exists())
            {
                //Write the contents of the file to the console window.
                Console.WriteLine(file.DownloadTextAsync().Result);
            }
        }
    }

コンソール アプリケーションを実行して、出力結果を確認します。

## <a name="next-steps"></a><span class="short-header">次のステップ</span>次のステップ

これで、File ストレージの基本を学習できました。
さらに詳しい情報については、次のリンク先を参照してください。

-   利用可能な API の詳細については、File サービスのリファレンス ドキュメントを参照してください。
    -   [.NET 用ストレージ クライアント ライブラリ リファレンス][Azure .NET Storage クライアント ライブラリ]
    -   [File サービスの REST API リファレンス][File サービスの REST API リファレンス]

-   File サービスに関連する Azure Storage チームのブログ記事を参照してください。
    -   [Introducing Microsoft Azure File Service (Microsoft Azure File サービスの概要)][Introducing Microsoft Azure File Service (Microsoft Azure File サービスの概要)]
    -   [Persisting connections to Microsoft Azure Files (Microsoft Azure Files への接続の維持)][Persisting connections to Microsoft Azure Files (Microsoft Azure Files への接続の維持)]

-   Azure でデータを格納するための追加のオプションについては、他の機能ガイドも
    参照してください。
    -   非構造化データの格納には、[BLOB ストレージ][BLOB ストレージ]を使用します。
    -   構造化データの格納には、[テーブル ストレージ][テーブル ストレージ]を使用します。
    -   メッセージを確実に格納するには、[キュー ストレージ][キュー ストレージ]を使用します。
    -   リレーショナル データの格納には、[SQL データベース][SQL データベース]を使用します。

</p>

  [NuGet]: https://www.nuget.org/packages/WindowsAzure.Storage/
  [File ストレージとは]: #what-is-file-storage
  [File ストレージの概念]: #file-storage-concepts
  [Azure のストレージ アカウントの作成]: #create-account
  [PowerShell を使用したファイル共有の作成]: #use-cmdlets
  [Azure の仮想マシンからの共有のマウント]: #mount-share
  [File ストレージを利用する内部設置型アプリケーションの作成]: #create-console-app
  [次のステップ]: #next-steps
  [files-concepts]: ./media/storage-dotnet-how-to-use-files/files-concepts.png
  [Azure のストレージの拡張性とパフォーマンスのターゲットに関するページ]: http://msdn.microsoft.com/ja-jp/library/dn249410.aspx
  [Naming and Referencing Shares, Directories, Files, and Metadata (共有、ディレクトリ、ファイル、およびメタデータの命名と参照)]: http://msdn.microsoft.com/ja-jp/library/azure/dn167011.aspx
  [Microsoft Azure プレビューのページ]: /ja-jp/services/preview/
  [Azure PowerShell のインストールおよび構成方法]: /ja-jp/documentation/articles/install-configure-powershell/
  [Windows Server を実行する仮想マシンの作成]: /ja-jp/documentation/articles/virtual-machines-windows-tutorial/
  [Windows Server を実行する仮想マシンにログオンする方法]: /ja-jp/documentation/articles/virtual-machines-log-on-windows-server/
  [System.IO 名前空間]: http://msdn.microsoft.com/ja-jp/library/gg145019(v=vs.110).aspx
  [Azure .NET Storage クライアント ライブラリ]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [File サービスの REST API リファレンス]: http://msdn.microsoft.com/ja-jp/library/azure/dn167006.aspx
  [Introducing Microsoft Azure File Service (Microsoft Azure File サービスの概要)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx
  [Persisting connections to Microsoft Azure Files (Microsoft Azure Files への接続の維持)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx
  [BLOB ストレージ]: /ja-jp/documentation/articles/storage-dotnet-how-to-use-blobs/
  [テーブル ストレージ]: /ja-jp/documentation/articles/storage-dotnet-how-to-use-tables/
  [キュー ストレージ]: /ja-jp/documentation/articles/storage-dotnet-how-to-use-queues/
  [SQL データベース]: /ja-jp/documentation/articles/sql-database-dotnet-how-to-use/
