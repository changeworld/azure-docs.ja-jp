<properties 
	pageTitle="Azure File ストレージを使用する方法 | Microsoft Azure" 
	description="Microsoft Azure File ストレージを使用してファイル共有を作成し、ファイルの内容を管理する方法について説明します。サンプルは PowerShell および C# で記述されています。" 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/06/2015" 
	ms.author="tamram"/>

# Azure File ストレージを使用する方法

## 概要

この概要ガイドでは、Microsoft Azure File ストレージの基本的な使用方法を説明します。最初に PowerShell を使用して新しい Azure File 共有を作成する方法を示します。次に、ディレクトリを追加する方法、ローカル ファイルを共有にアップロードする方法、ディレクトリ内のファイルを一覧表示する方法を示します。その後、Azure の仮想マシンのファイル共有を、一般的な SMB 共有と同じようにマウントする方法を説明します。

Azure の仮想マシンやクラウド サービスだけでなく、内部設置型のアプリケーションから共有内のファイルにアクセスする必要があるユーザーのために、Azure .NET Storage クライアント ライブラリを使用してデスクトップ アプリケーションからファイル共有を利用する方法を説明します。

> [AZURE.NOTE] このガイドの .NET コードの例を実行するには、Azure .NET Storage クライアント ライブラリ 4.x 以降が必要になります。Storage クライアント ライブラリは [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) で入手できます。

## Azure File ストレージとは

File ストレージは、標準の SMB 2.1 プロトコルを使用して、アプリケーション用の共有ストレージを提供します。Microsoft Azure の仮想マシンおよびクラウド サービスでは、マウントされている共有を介して、アプリケーション コンポーネント間でファイル データを共有できます。内部設置型のアプリケーションでは、File ストレージ API を介して、共有内のファイル データにアクセスできます。

Azure の仮想マシンまたはクラウド サービスで実行しているアプリケーションでは、デスクトップ アプリケーションで通常の SMB 共有をマウントする場合と同じように、ファイル ストレージ共有をマウントすることで、ファイル データにアクセスできます。File ストレージ共有は、任意の数の Azure 仮想マシンまたはロールが同時にマウントしてアクセスすることができます。

File ストレージ共有は標準の SMB 2.1 ファイル共有であるため、Azure で実行されているアプリケーションは、ファイル I/O API を介して共有内のデータにアクセスできます。そのため、開発者は、既存のコードとスキルを活用して、既存のアプリケーションを移行することができます。IT プロフェッショナルは、Azure アプリケーションの管理の一環として、PowerShell コマンドレットを使用して、ファイル ストレージ共有を作成、マウント、管理できます。このガイドでは、両方の例を紹介します。

File ストレージの一般的な用途には、次のようなものがあります。

- ファイル共有を利用している内部設置型のアプリケーションを、コストの高い書き換えを行わずに、Azure の仮想マシンやクラウド サービス上で実行できるように移行する
- 共有のアプリケーション設定を構成ファイルなどに格納する
- ログ、メトリック、クラッシュ ダンプなどの診断データを共有の場所に格納する 
- Azure の仮想マシンまたはクラウド サービスの開発や管理に必要なツールおよびユーティリティを格納する

## File ストレージの概念

File ストレージには次の構成要素があります。

![files-concepts][files-concepts]


-   **ストレージ アカウント:** Azure のストレージにアクセスする場合には必ず、ストレージ アカウントを使用します。ストレージ アカウントの容量の詳細については、「[Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット](http://msdn.microsoft.com/library/azure/dn249410.aspx)」を参照してください。

-   **共有:** File ストレージ共有は、Azure 内の SMB 2.1 ファイル共有です。
    ディレクトリとファイルはすべて親の共有に作成する必要があります。アカウントに含まれる共有の数と、共有に格納できるファイル数には制限がなく、ストレージ アカウントの容量の上限まで増やすことができます。

-   **ディレクトリ:** ディレクトリの階層 (オプション)。

-	**ファイル:** 共有内のファイル。最大 1 TB までのファイルを使用できます。

-   **URL 形式:** ファイルは、次の URL を使用してアドレスを指定してアクセスできます。
    format:   
    https://`<storage
    account>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`  
    
    次の例の URL を使用すると、上の図のいずれかのファイルをアドレス指定できます。:  
    `http://acmecorp.file.core.windows.net/cloudfiles/diagnostics/log.txt`



共有、ディレクトリ、およびファイルの詳しい命名方法については、「[共有、ディレクトリ、ファイル、およびメタデータの名前付けおよび参照](http://msdn.microsoft.com/library/azure/dn167011.aspx)」を参照してください。

## Azure のストレージ アカウントの作成

Azure File ストレージは現在プレビュー段階にあります。このプレビュー機能の利用を申し込むには、[Microsoft Azure プレビューのページ](/services/preview/) で、**Azure Files** の利用を申し込んでください。申し込みが承認されると、File ストレージ プレビューを利用できるようになったという通知が送られます。その後で、File ストレージにアクセスするストレージ アカウントを作成できます。

> [AZURE.NOTE] File ストレージは現在、新規のストレージ アカウントでのみ利用できます。File ストレージ アカウントへのアクセスがサブスクリプションに許可された後で、このガイドで使用する新規ストレージ アカウントを作成してください。

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

## PowerShell を使用したファイル共有の作成

次に、Azure PowerShell を使用して、ファイル共有を作成します。作成されたファイル共有は、SMB 2.1 をサポートしている任意のファイル システムからマウントできます。 

### Azure Storage 用の PowerShell コマンドレットのインストール

PowerShell の使用を準備するために、Azure PowerShell コマンドレットをダウンロードしてインストールします。インストール先とインストール方法については、[Azure PowerShell のインストールおよび構成方法](install-configure-powershell.md)に関するページ を参照してください。

> [AZURE.NOTE] File サービス用の PowerShell コマンドレットは、最新の Azure PowerShell モジュール (Version 0.8.5 以降) でのみ提供されています。最新の Azure PowerShell モジュールをダウンロードしてインストールするか、アップグレードすることをお勧めします。

**[スタート]** をクリックし、「**Azure PowerShell**」と入力して、Azure PowerShell ウィンドウを開きます。Azure PowerShell ウィンドウに自動的に Azure Powershell モジュールが読み込まれます。

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

次に、ディレクトリにローカル ファイルをアップロードします。次の例では `C:\temp\samplefile.txt` からファイルをアップロードします。ファイル パスを編集して、ローカル マシン上の有効なファイルを指定してください。 
    
    # upload a local file to the new directory
    Set-AzureStorageFileContent -Share $s -Source C:\temp\samplefile.txt -Path sampledir

### ディレクトリ内のファイルの一覧表示

ディレクトリ内のファイルを一覧表示して、ファイルを確認できます。このコマンドを実行するとサブディレクトリも表示されますが、この例ではサブディレクトリがないため、ファイルのみが一覧表示されます。  

    # list files in the new directory
    Get-AzureStorageFile -Share $s -Path sampledir

## Windows を実行する Azure の仮想マシンからの共有のマウント

Azure のファイル共有をマウントする方法を示すために、ここでは Windows を実行する Azure の仮想マシンを作成します。このマシンにリモート接続して、共有をマウントします。 

1. 最初に、[Windows Server を実行する仮想マシンの作成](virtual-machines-windows-tutorial.md)の説明に従って、Azure の仮想マシンを新規作成します。
2. 次に、[Windows Server を実行する仮想マシンにログオンする方法](virtual-machines-log-on-windows-server.md)の説明に従って仮想マシンにリモート接続します。
3. 仮想マシンで PowerShell ウィンドウを開きます。 

### ストレージ アカウントの資格情報を仮想マシンに適用

ファイル共有をマウントする前に、ストレージ アカウントの資格情報を仮想マシンにも適用します。そうすることで、仮想マシンが再起動したときに Windows が自動的にファイル共有に再接続します。アカウントの資格情報を仮想マシンに適用するには、仮想マシンの PowerShell ウィンドウで `cmdkey` コマンドを実行します。`<storage-account>` を実際のストレージ アカウントの名前に置き換え、`<account-key>` を実際のストレージ アカウント キーに置き換えてください。

	cmdkey /add:<storage-account>.file.core.windows.net /user:<storage-account> /pass:<account-key>

これで、仮想マシンの再起動時に Windows が自動的にファイル共有に再接続するようになります。共有に再接続したことを確認するには、PowerShell ウィンドウから `net use` コマンドを実行します。

### 適用された資格情報を使用したファイル共有のマウント

仮想マシンにリモート接続した後で、 `net use` コマンドを実行してファイル共有をマウントできます。次の構文を使用します。`<storage-account>` を実際のストレージ アカウントの名前に置き換え、`<share-name>` を実際の File ストレージ共有の名前に置き換えてください。

	net use z: \\<storage-account>.file.core.windows.net\<share-name>

> [AZURE.NOTE] 前の手順でストレージ アカウントの資格情報を適用したため、 `net use` コマンドで資格情報を指定する必要はありません。資格情報をまだ適用していない場合は、 `net use` コマンドのパラメーターで資格情報を指定してください。`<storage-account>` を実際のストレージ アカウントの名前に置き換え、`<share-name>` を実際の File ストレージ共有の名前に置き換え、さらに `<account-key>` を実際のストレージ アカウント キーに置き換えてください。
	   
	net use z: \\<storage-account>.file.core.windows.net\<share-name> /u:<storage-account> <account-key>

これで、通常のドライブと同じように仮想マシンから File ストレージ共有を利用できるようになります。コマンド プロンプトから標準のファイル コマンドを発行したり、マウントした共有とその内容をエクスプローラーで表示したりできます。.NET Framework の [System.IO 名前空間](http://msdn.microsoft.com/library/gg145019(v=vs.110).aspx)の API など、標準の Windows ファイル I/O API を使用してファイル共有にアクセスするコードを仮想マシン内で実行することもできます。 

さらに、ロールにリモート接続することで、Azure クラウド サービスで実行されるロールからファイル共有をマウントすることもできます。

## File ストレージを利用するオンプレミスのアプリケーションの作成

これまで説明したように、仮想マシンや Azure で実行されるクラウド サービスからファイル共有をマウントすることができます。ただし、オンプレミスのアプリケーションからファイル共有をマウントすることはできません。内部設置型のアプリケーションから共有データにアクセスするには、File ストレージ API を使用する必要があります。この例では、[Azure .NET Storage クライアント ライブラリ](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)を使用してファイル共有を利用する方法を示します。 

内部設置型のアプリケーションから API を使用する方法を示すために、ここではデスクトップ上で動作する簡単なコンソール アプリケーションを作成します。

### コンソール アプリケーションの作成とアセンブリの取得

Visual Studio で新しいコンソール アプリケーションを作成して Azure Storage NuGet パッケージをインストールするには、次の手順を実行します。

1. Visual Studio で、**[ファイル]**、**[新しいプロジェクト]** の順にクリックした後、**[Windows]** をクリックし、Visual C# テンプレートの一覧から **[コンソール アプリケーション]** をクリックします。
2. コンソール アプリケーションの名前を入力して、**[OK]** をクリックします。
3. プロジェクトが作成されたら、ソリューション エクスプローラーでプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。"WindowsAzure.Storage" をオンライン検索し、**[インストール]** をクリックして、Azure Storage パッケージと依存関係をインストールします。

### ストレージ アカウントの資格情報を app.config ファイルに保存

次に、プロジェクトの app.config ファイルに資格情報を保存します。次の例のようになるように app.config ファイルを編集します。ここで、 `myaccount` は実際のストレージ アカウント名に置き換え、 `mykey` は実際のストレージ アカウント キーに置き換えてください。

    <?xml version="1.0" encoding="utf-8" ?>
	<configuration>
		<startup> 
			<supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		</startup>
		<appSettings>
			<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey" />
		</appSettings>
	</configuration>

> [AZURE.NOTE] 最新バージョンの Azure ストレージ エミュレーターでは、File ストレージがサポートされません。接続文字列は、Azure Files プレビュー機能にアクセスできる、クラウド内の Azure ストレージ アカウントを対象とする必要があります。


### 名前空間宣言の追加
ソリューション エクスプローラーで program.cs ファイルを開き、このファイルに次の名前空間宣言を追加します。

    using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.File;

### プログラムによる接続文字列の取得
 `Microsoft.WindowsAzure.CloudConfigurationManager` クラスまたは `System.Configuration.ConfigurationManager ` クラスを使用して、app.config ファイルから保存済みの資格情報を取得することができます。この例では、`CloudConfigurationManager` クラスを使用して資格情報を取得し、それを `CloudStorageAccount` クラスによってカプセル化する方法を示します。rogram.cs の  `Main()` メソッドに、次のコードを追加します。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

### プログラムによる File ストレージ共有へのアクセス

次に、前に示したコードの後で、 `Main()` メソッドに次のコードを追加し、接続文字列を取得します。このコードは、前の手順で作成したファイルへの参照を取得し、その内容をコンソール ウィンドウに出力します。

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

## Linux を実行する Azure の仮想マシンからの共有のマウント

Azure の仮想マシンを作成するときに、ディスク イメージ ギャラリーから Ubuntu イメージを指定して SMB 2.1 をサポートできます。SMB 2.1 をサポートするどの Linux ディストリビューションでも Azure File 共有をマウントできます。 

Linux に Azure File 共有をマウントする方法については、チャンネル 9 の [Azure Files プレビュー経由での Linux の共有ストレージ - パート 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1) のブログを参照してください。

## 次のステップ

これで、File ストレージの基本を学習できました。さらに複雑なストレージ タスクを実行する方法については、
次のリンク先を参照してください。
<ul>
<li>利用可能な API の詳細については、File サービスのリファレンス ドキュメントを参照してください。
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">.NET 用ストレージ クライアント ライブラリ リファレンス</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/azure/dn167006.aspx">File サービスの REST API リファレンス</a></li>
  </ul>
</li>
<li>File サービスに関連する Azure Storage チームのブログ記事を参照してください。
  <ul>
    <li><a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx">Introducing Microsoft Azure File Service (Microsoft Azure File サービスの概要)</a>
    </li>
    <li><a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx">Persisting connections to Microsoft Azure Files (Microsoft Azure Files への接続の維持)</a></li>
  </ul>
</li><li>Azure でデータを格納するための追加のオプションについては、他の機能ガイドも参照してください。
  <ul>
    <li>非構造化データの格納には、<a href="/documentation/articles/storage-dotnet-how-to-use-blobs/">BLOB ストレージ</a>を使用します。</li>
    <li>構造化データの格納には、<a href="/documentation/articles/storage-dotnet-how-to-use-tables/">テーブル ストレージ</a>を使用します。</li>
    <li>メッセージを確実に格納するには、<a href="/documentation/articles/storage-dotnet-how-to-use-queues/">キュー ストレージ</a>を使用します。</li>
    <li>リレーショナル データの格納には、<a href="/documentation/articles/sql-database-dotnet-how-to-use/">SQL データベース</a>を使用します。</li>
  </ul>
</li>
</ul>

[files-concepts]: ./media/storage-dotnet-how-to-use-files/files-concepts.png


<!--HONumber=49--> 

<!--HONumber=49-->