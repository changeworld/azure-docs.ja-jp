<properties
			pageTitle="PowerShell と .NET で Azure File ストレージを使用する方法 | Microsoft Azure"
            description="Azure File ストレージを使用して、クラウド ファイル共有を作成し、ファイルの内容を管理する方法について説明します。File ストレージを使用することで、企業は SMB ファイル共有に依存するアプリケーションを Azure に移行できます。再起動時にファイル共有に再接続するためには、仮想マシンのストレージ アカウントの資格情報を保持してください。"
            services="storage"
            documentationCenter=".net"
            authors="tamram"
            manager="adinah"
            editor="" />

<tags ms.service="storage"
      ms.workload="storage"
      ms.tgt_pltfrm="na"
      ms.devlang="dotnet"
      ms.topic="hero-article"
      ms.date="07/06/2015"
      ms.author="tamram" />

# PowerShell と .NET で Azure File ストレージを使用する方法

## 概要

Azure File サービスは、標準の SMB 2.1 プロトコルを使用して、ファイル共有を公開します。Azure 内で稼働するアプリケーションは、ReadFile や WriteFile などの使い慣れた標準のファイル システム API を使用して VM 間でファイルを容易に共有できます。また、REST インターフェイスを介して同時にファイルにアクセスできるため、さまざまなハイブリッド シナリオを開くことができます。最後に、Azure File は、BLOB、テーブル、およびキュー サービスと同じテクノロジ上に構築されます。つまり Azure File では、既存の可用性、持続性、スケーラビリティ、およびプラットフォームに組み込まれている geo 冗長性を利用できます。

## このチュートリアルについて

この概要チュートリアルでは、Microsoft Azure File ストレージの基本的な使用方法を説明します。このチュートリアルでは、次のことを行います。

- PowerShell を使用して、新しい Azure File 共有の作成、ディレクトリの追加、共有へのローカル ファイルのアップロード、ディレクトリ内のファイルの一覧表示の方法を示します。
- Azure の仮想マシンのファイル共有を、SMB 共有と同じようにマウントします。

Azure の仮想マシンやクラウド サービスだけでなく、内部設置型のアプリケーションから共有内のファイルにアクセスする必要があるユーザーのために、Azure .NET Storage クライアント ライブラリを使用してデスクトップ アプリケーションからファイル共有を利用する方法を説明します。

> [AZURE.NOTE]このガイドの .NET コードの例を実行するには、Azure .NET Storage クライアント ライブラリ 4.x 以降が必要になります。Storage クライアント ライブラリは、[NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) で入手できます。

[AZURE.INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]


## Azure のストレージ アカウントの作成

Azure File ストレージは現在プレビュー段階にあります。このプレビュー機能の利用を申し込むには、[Microsoft Azure プレビューのページ](/services/preview/)で、**Azure Files** の利用を申し込んでください。申し込みが承認されると、File ストレージ プレビューを利用できるようになったという通知が送られます。その後で、File ストレージにアクセスするストレージ アカウントを作成できます。

> [AZURE.NOTE]File ストレージは現在、新規のストレージ アカウントでのみ利用できます。File ストレージ アカウントへのアクセスがサブスクリプションに許可された後で、このガイドで使用する新規ストレージ アカウントを作成してください。
>
> Azure File ストレージでは、現在、共有アクセス署名をサポートしていません。

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## PowerShell を使用したファイル共有の作成

次に、Azure PowerShell を使用して、ファイル共有を作成します。作成されたファイル共有は、SMB 2.1 をサポートしている任意のファイル システムからマウントできます。

### Azure Storage 用の PowerShell コマンドレットのインストール

PowerShell の使用を準備するために、Azure PowerShell コマンドレットをダウンロードしてインストールします。インストール先とインストール方法については、「[Azure PowerShell のインストールおよび構成方法](../install-configure-powershell.md)」を参照してください。

> [AZURE.NOTE]File サービス用の PowerShell コマンドレットは、最新の Azure PowerShell モジュール (Version 0.8.5 以降) でのみ提供されています。最新の Azure PowerShell モジュールをダウンロードしてインストールするか、アップグレードすることをお勧めします。

**[スタート]** をクリックし、「**Azure PowerShell**」と入力して、Azure PowerShell ウィンドウを開きます。Azure PowerShell ウィンドウに自動的に Azure Powershell モジュールが読み込まれます。

### ストレージ アカウントおよびキーのコンテキストの作成

次に、ストレージ アカウント コンテキストを作成します。コンテキストによってストレージ アカウント名とアカウント キーがカプセル化されます。Azure ポータルからアカウント キーをコピーする手順については、「[ストレージ アクセス キーの表示、コピー、および再生成](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)」を参照してください。

次の例の `storage-account-name` と `storage-account-key` は、実際のストレージ アカウント名とキーに置き換えてください。

	# create a context for account and key
	$ctx=New-AzureStorageContext storage-account-name storage-account-key

### 新しいファイル共有の作成

次に、新しいファイル共有を作成します。この例では `logs` という名前を付けています。

	# create a new share
	$s = New-AzureStorageShare logs -Context $ctx

これで、File ストレージにファイル共有が作成されました。次に、ディレクトリとファイルを追加します。

> [AZURE.IMPORTANT]ファイル共有の名前はすべて小文字にする必要があります。ファイル共有とファイルの名前付けの詳細については、「[共有、ディレクトリ、ファイル、およびメタデータの名前付けおよび参照](https://msdn.microsoft.com/library/azure/dn167011.aspx)」を参照してください。

### ファイル共有でのディレクトリの作成

次に、共有内にディレクトリを作成します。次の例では、ディレクトリに `CustomLogs` という名前を付けています。

    # create a directory in the share
    New-AzureStorageDirectory -Share $s -Path CustomLogs

### ディレクトリへのローカル ファイルのアップロード

次に、ディレクトリにローカル ファイルをアップロードします。次の例では、`C:\temp\Log1.txt` からファイルをアップロードします。ファイル パスを編集して、ローカル マシン上の有効なファイルを指定してください。

    # upload a local file to the new directory
    Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs

### ディレクトリ内のファイルの一覧表示

ディレクトリ内のファイルを一覧表示して、ファイルを確認できます。このコマンドを実行するとサブディレクトリも表示されますが、この例ではサブディレクトリがないため、ファイルのみが一覧表示されます。

	# list files in the new directory
	Get-AzureStorageFile -Share $s -Path CustomLogs

## Windows を実行する Azure の仮想マシンからの共有のマウント

Azure のファイル共有をマウントする方法を示すために、ここでは Windows を実行する Azure の仮想マシンを作成します。このマシンにリモート接続して、共有をマウントします。

1. 最初に、「[Windows Server を実行する仮想マシンの作成](../virtual-machines-windows-tutorial.md)」の説明に従って、Azure の仮想マシンを新規作成します。
2. 次に、「[Windows Server を実行する仮想マシンにログオンする方法](../virtual-machines-log-on-windows-server.md)」の説明に従って仮想マシンにリモート接続します。
3. 仮想マシンで PowerShell ウィンドウを開きます。

### ストレージ アカウントの資格情報を仮想マシンに適用

ファイル共有をマウントする前に、ストレージ アカウントの資格情報を仮想マシンにも適用します。そうすることで、仮想マシンが再起動したときに Windows が自動的にファイル共有に再接続します。アカウントの資格情報を適用するには、仮想マシンの PowerShell ウィンドウで `cmdkey` コマンドを実行します。`<storage-account-name>` を実際のストレージ アカウントの名前に置き換え、`<storage-account-key>` を実際のストレージ アカウント キーに置き換えてください。

	cmdkey /add:<storage-account-name>.file.core.windows.net /user:<storage-account-name> /pass:<storage-account-key>

これで、仮想マシンの再起動時に Windows が自動的にファイル共有に再接続するようになります。共有に再接続したことを確認するには、PowerShell ウィンドウから `net use` コマンドを実行します。

### 適用された資格情報を使用したファイル共有のマウント

仮想マシンにリモート接続した後で、`net use` コマンドを実行してファイル共有をマウントできます。その場合は次の構文を使用します。`<storage-account-name>` を実際のストレージ アカウントの名前に置き換え、`<share-name>` を実際の File ストレージ共有に置き換えてください。

    net use <drive-letter>: \<storage-account-name>.file.core.windows.net<share-name>

	example :
	net use z: \\samples.file.core.windows.net\logs

> [AZURE.NOTE]前の手順でストレージ アカウントの資格情報を適用したため、`net use` コマンドで資格情報を指定する必要はありません。資格情報をまだ適用していない場合は、`net use` コマンドのパラメーターで資格情報を指定してください。

    net use <drive-letter>: \<storage-account-name>.file.core.windows.net<share-name> /u:<storage-account-name> <storage-account-key>

	example :
	net use z: \\samples.file.core.windows.net\logs /u:samples <storage-account-key>

これで、他のドライブの場合と同じように仮想マシンから File ストレージ共有を利用できるようになります。コマンド プロンプトから標準のファイル コマンドを発行したり、マウントした共有とその内容をエクスプローラーで表示したりできます。.NET Framework の [System.IO 名前空間] (http://msdn.microsoft.com/library/gg145019(v=vs.110).aspx) の API など、標準の Windows ファイル I/O API を使用してファイル共有にアクセスするコードを仮想マシン内で実行することもできます。

さらに、ロールにリモート接続することで、Azure クラウド サービスで実行されるロールからファイル共有をマウントすることもできます。

## File ストレージを利用するオンプレミスのアプリケーションの作成

これまで説明したように、仮想マシンや Azure で実行されるクラウド サービスからファイル共有をマウントすることができます。ただし、オンプレミスのアプリケーションからファイル共有をマウントすることはできません。内部設置型のアプリケーションから共有データにアクセスするには、File ストレージ API を使用する必要があります。この例では、[Azure .NET Storage クライアント ライブラリ](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)を使用してファイル共有を利用する方法を示します。

内部設置型のアプリケーションから API を使用する方法を示すために、ここではデスクトップ上で動作する簡単なコンソール アプリケーションを作成します。

### コンソール アプリケーションの作成とアセンブリの取得

Visual Studio で新しいコンソール アプリケーションを作成して Azure Storage NuGet パッケージをインストールするには、次の手順を実行します。

1. Visual Studio で、**[ファイル]**、**[新しいプロジェクト]** の順にクリックした後、**[Windows]** をクリックし、Visual C# テンプレートの一覧から **[コンソール アプリケーション]** をクリックします。
2. コンソール アプリケーションの名前を入力して、**[OK]** をクリックします。
3. プロジェクトが作成されたら、ソリューション エクスプローラーでプロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。"WindowsAzure.Storage" をオンライン検索し、**[インストール]** をクリックして Azure Storage のパッケージと依存関係をインストールします。

### ストレージ アカウントの資格情報を app.config ファイルに保存

次に、プロジェクトの app.config ファイルに資格情報を保存します。次の例のようになるように app.config ファイルを編集します。ここで、`myaccount` は実際のストレージ アカウント名に置き換え、`mykey` は実際のストレージ アカウント キーに置き換えてください。

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	    <startup>
	        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	    </startup>
	    <appSettings>
	        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
	    </appSettings>
	</configuration>

> [AZURE.NOTE]最新バージョンの Azure ストレージ エミュレーターでは、File ストレージがサポートされません。接続文字列は、Azure Files プレビュー機能にアクセスできる、クラウド内の Azure ストレージ アカウントを対象とする必要があります。


### 名前空間宣言の追加

ソリューション エクスプローラーで program.cs ファイルを開き、このファイルに次の名前空間宣言を追加します。

	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.File;

### プログラムによる接続文字列の取得

`Microsoft.WindowsAzure.CloudConfigurationManager` クラスまたは `System.Configuration.ConfigurationManager ` クラスを使用して、app.config ファイルから保存済みの資格情報を取得することができます。この例では、`CloudConfigurationManager` クラスを使用して資格情報を取得し、それを `CloudStorageAccount` クラスによってカプセル化する方法を示します。program.cs の `Main()` メソッドに、次のコードを追加します。

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    	CloudConfigurationManager.GetSetting("StorageConnectionString"));

### プログラムによる File ストレージ共有へのアクセス

次に、前に示したコードの後で、`Main()` メソッドに次のコードを追加し、接続文字列を取得します。このコードは、前の手順で作成したファイルへの参照を取得し、その内容をコンソール ウィンドウに出力します。

	//Create a CloudFileClient object for credentialed access to File storage.
	CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

	//Get a reference to the file share we created previously.
	CloudFileShare share = fileClient.GetShareReference("logs");

	//Ensure that the share exists.
	if (share.Exists())
	{
	    //Get a reference to the root directory for the share.
	    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

	    //Get a reference to the directory we created previously.
	    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

	    //Ensure that the directory exists.
	    if (sampleDir.Exists())
	    {
	        //Get a reference to the file we created previously.
	        CloudFile file = sampleDir.GetFileReference("Log1.txt");

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

Linux に Azure File 共有をマウントする方法については、チャンネル 9 の「[Azure Files プレビュー経由での Linux の共有ストレージ - パート 1](http://channel9.msdn.com/Blogs/Open/Shared-storage-on-Linux-via-Azure-Files-Preview-Part-1)」を参照してください。

## 次のステップ

Azure File ストレージの詳細については、次のリンクを参照してください。

### リファレンス

- [.NET 用ストレージ クライアント ライブラリ リファレンス](https://msdn.microsoft.com/library/azure/dn261237.aspx)
- [File サービスの REST API リファレンス](http://msdn.microsoft.com/library/azure/dn167006.aspx)

### ブログ記事

- [Microsoft Azure File サービスの概要](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [Microsoft Azure Files への接続の維持](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
 

<!---HONumber=July15_HO5-->