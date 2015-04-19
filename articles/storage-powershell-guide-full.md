<properties 
	pageTitle="Azure Storage での Azure PowerShell の使用" 
	description="Azure Storage で Azure PowerShell を使用する方法の説明" 
	services="storage" 
	documentationCenter="na" 
	authors="Selcin" 
	manager="adinah"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/27/2015" 
	ms.author="selcint"/>

# Azure Storage での Azure PowerShell の使用 

Azure Storage 用の Azure PowerShell ガイドへようこそ

[Azure の管理ポータル](http://manage.windowsazure.com/)、[ストレージ クライアント ライブラリ](http://msdn.microsoft.com/library/azure/dn261237.aspx)、[REST API](http://msdn.microsoft.com/library/azure/dd179355.aspx)、[Azure PowerShell](http://msdn.microsoft.com/library/azure/jj156055.aspx) など、複数の開発ツールや管理ツールを使用して、Azure Storage 上でさまざまなタスクを実行できます。 

このガイドでは、Azure PowerShell を使用して Azure Storage 上で一般的なタスクを実行する方法について説明します。Azure PowerShell は、Windows PowerShell から Azure を管理するコマンドレットを提供するモジュールです。タスクベースのコマンドライン シェルとスクリプト言語であり、システム管理に特化して設計されています。PowerShell を使用すると、Azure サービスやアプリケーションの管理を容易に制御して自動化できます。たとえば、このコマンドレットを使用して、Azure の管理ポータルで実行できるタスクと同じタスクを実行できます。 

このガイドは、[Azure Storage](http://azure.microsoft.com/documentation/services/storage/) と [Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx) を使用した経験がある読者を対象としています。また、PowerShell と Azure Storage の使用方法を示すための多くのスクリプトを用意しています。各スクリプトの実行前に、使用する構成に基づいてスクリプト変数を更新する必要があります。

このガイドの最初のセクションでは、Azure Storage と PowerShell について簡単に説明します。詳細と手順については、「[Azure Storage で Azure PowerShell を使用するための前提条件]」から開始してください(#pre)。

## 目次
  
 - [5 分で始める、Azure Storage と PowerShell の使用][]
 - [Azure Storage で Azure PowerShell を使用するための前提条件][] 
 - [Azure でストレージ アカウントを管理する方法][]
	 - [既定の Azure サブスクリプションを設定する方法][]
	 - [新しい Azure ストレージ アカウントを作成する方法][]
	 - [既定の Azure ストレージ アカウントを設定する方法][]
	 - [サブスクリプション内のすべての Azure ストレージ アカウントをリストする方法][]
	 - [Azure ストレージ コンテキストを作成する方法][]
 - [Azure BLOB と BLOB のスナップショットを管理する方法][]
	 - [コンテナーを作成する方法][]
	 - [BLOB をコンテナーにアップロードする方法][]
	 - [BLOB をコンテナーからダウンロードする方法][]
	 - [あるストレージ コンテナーから別のストレージ コンテナーに BLOB をコピーする方法][]
	 - [BLOB を削除する方法][]
	 - [Azure BLOB のスナップショットを管理する方法][]
	 	- [BLOB のスナップショットを作成する方法][]
	 	- [BLOB のスナップショットをリストする方法][]
	 	- [BLOB のスナップショットをコピーする方法][]
 - [Azure テーブルとテーブル エンティティを管理する方法][]
	 - [テーブルを作成する方法][]
	 - [テーブルを取得する方法][]
	 - [テーブルを削除する方法][]
	 - [テーブル エンティティを管理する方法][]
	 	- [テーブル エンティティを追加する方法][]
	 	- [テーブル エンティティを照会する方法][]
	 	- [テーブル エンティティを削除する方法][]
 - [Azure キューとキュー メッセージを管理する方法][]
	 - [キューを作成する方法][]
	 - [キューを取得する方法][]
	 - [キューを削除する方法][]
	 - [キュー メッセージを管理する方法][]
	 	- [メッセージをキューに挿入する方法][]
	 	- [次のメッセージでデキューする方法][]
 - [Azure ファイル共有とファイルを管理する方法][]
 - [ストレージ分析を設定して照会する方法][]
 - [Shared Access Signature (SAS) や格納されているアクセス ポリシーの選択を管理する方法][]
 - [米国政府対応の Azure Storage や Azure China を使用する方法][]
 - [次のステップ][]
 
## <a name="getstart"></a>5 分で始める、Azure Storage と PowerShell の使用
このセクションでは、PowerShell から Azure Storage にアクセスする方法を 5 分で説明します。 

**Azure を初めて使用する場合:** Microsoft Azure サブスクリプションとそのサブスクリプションに関連付けられた Microsoft アカウントを取得します。Azure の購入オプションの詳細については、[無料評価版](http://azure.microsoft.com/pricing/free-trial/)に関するページ、[購入方法](http://azure.microsoft.com/pricing/purchase-options/)に関するページ、[メンバー プラン](http://azure.microsoft.com/pricing/member-offers/) (MSDN、Microsoft Partner Network、BizSpark、その他の Microsoft プログラムのメンバー向け) に関するページをご覧ください。

**Microsoft Azure アカウントとサブスクリプションについて不明な点がある場合:**「[アカウント、サブスクリプション、管理ロールの管理](https://msdn.microsoft.com/library/azure/hh531793.aspx)」をご覧ください。

**Microsoft Azure アカウントとサブスクリプションを作成済みである場合:** 

1.	[Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409) をダウンロードして、ローカル コンピューターにインストールします。
2.	Windows PowerShell Integrated Scripting Environment (ISE) の起動:ローカル コンピューターで、**[スタート]** メニューを表示します。「**管理ツール**」と入力し、クリックして実行します。**[管理ツール]** ウィンドウで、**[Windows PowerShell ISE]** を右クリックし、**[管理者として実行]** をクリックします。 
3.	**Windows PowerShell ISE** で、**[ファイル]**、**[新規作成]** の順にクリックし、新しいスクリプト ファイルを作成します。 
4.	ここでは、Azure Storage にアクセスするための基本的な PowerShell コマンドを示すシンプルなスクリプトを取り上げます。このスクリプトでは、Azure アカウントをローカルの PowerShell 環境に追加するための Azure アカウント資格情報が最初に求められます。次に、既定の Azure サブスクリプションが設定され、Azure で新しいストレージ アカウントが作成されます。さらに、この新しいストレージ アカウントで新しいコンテナーが作成され、既存の画像ファイル (BLOB) がこのコンテナーにアップロードされます。このスクリプトにより、コンテナー内のすべての BLOB がリストされると、ローカル コンピューターに新しい格納先ディレクトリが作成され、画像ファイルがダウンロードされます。
5.	次のコードのセクションで、**#begin** と **#end** の間のスクリプトを選択します。Ctrl キーを押しながら C キーを押し、クリップボードにコピーします。 

    	#begin
    	# Update with the name of your subscription.
    	$SubscriptionName="YourSubscriptionName"
    
    	# Give a name to your new storage account. It must be lowercase! 
    	$StorageAccountName="yourstorageaccountname"
    
    	# Choose "West US" as an example.
    	$Location = "West US"
    
    	# Give a name to your new container.
    	$ContainerName = "imagecontainer"
    
    	# Have an image file and a source directory in your local computer.
    	$ImageToUpload = "C:\Images\HelloWorld.png"
    
    	# A destination directory in your local computer.
    	$DestinationFolder = "C:\DownloadImages"
    
    	# Add your Azure account to the local PowerShell environment.
    	Add-AzureAccount
    
    	# Set a default Azure subscription.
    	Select-AzureSubscription -SubscriptionName $SubscriptionName -Default
    
    	# Create a new storage account.
    	New-AzureStorageAccount -StorageAccountName $StorageAccountName -Location $Location
    
    	# Set a default storage account.
    	Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    
    	# Create a new container.
    	New-AzureStorageContainer -Name $ContainerName -Permission Off
    
    	# Upload a blob into a container.
    	Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload 
    
    	# List all blobs in a container.
    	Get-AzureStorageBlob -Container $ContainerName
    
    	# Download blobs from the container:
    	# Get a reference to a list of all blobs in a container.
    	$blobs = Get-AzureStorageBlob -Container $ContainerName
    
    	# Create the destination directory.
    	New-Item -Path $DestinationFolder -ItemType Directory -Force  
    
    	# Download blobs into the local destination directory.
    	$blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder
    	#end
    
5.	**Windows PowerShell ISE** で、Ctrl キーを押しながら V キーを押し、スクリプトをコピーします。**[ファイル]**、**[保存]** の順にクリックします。**[名前を付けて保存]** ダイアログ ウィンドウで、スクリプト ファイルの名前を "mystoragescript" などとして入力します。**[保存]** をクリックします。

6.	ここで、構成設定に基づいてスクリプト変数を更新する必要があります。たとえば、**$SubscriptionName** 変数を自分のサブスクリプションで更新する必要があります。その他の変数は、スクリプトで指定されたままにすることも、必要に応じて更新することも可能です。

	- **$SubscriptionName:** この変数は、自分のサブスクリプション名で更新する必要があります。次の 3 つの方法のいずれかに従って、サブスクリプションの名前を確認します。
	
		a. **Windows PowerShell ISE** で、**[ファイル]** > **[新規作成]** の順にクリックし、新しいスクリプト ファイルを作成します。次のスクリプトを新しいスクリプト ファイルにコピーし、**[デバッグ]**、**[実行/続行]** の順にクリックします。次のスクリプトでは、Azure アカウントをローカルの PowerShell 環境に追加するための Azure アカウント資格情報が最初に求められ、次に、ローカルの PowerShell セッション接続されているすべてのサブスクリプションが表示されます。このチュートリアルに従って作業する際に使用するサブスクリプションの名前を書き留めます。 
	 
    		Add-AzureAccount 
       		Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName 


		b. Azure で現在サポートされているポータルは、現在の [Azure 管理ポータル](https://manage.windowsazure.com/)と [Azure プレビュー ポータル](https://portal.azure.com/)の 2 つです。現在の [Azure 管理ポータル](https://portal.azure.com/)にサインインした場合は、スクロール ダウンして、ポータルの左側にある **[設定]** をクリックします。**[サブスクリプション]** をクリックします。このガイドで取り上げられているスクリプトを実行する際に使用するサブスクリプションの名前をコピーします。例として次のスクリーンショットをご覧ください。

		![Azure Management Portal][Image1]

		c. [Azure プレビュー ポータル](https://portal.azure.com/)にサインインした場合は、左側の [ハブ] メニューの **[参照]** をクリックします。**[すべて]** をクリックし、**[サブスクリプション]** をクリックします。このガイドのスクリプトを実行する際に使用するサブスクリプションの名前をコピーします。例として次のスクリーンショットをご覧ください。

		![Azure Preview Portal][Image2]
 

	- **$StorageAccountName:** スクリプトの所定の名前を使用するか、ストレージ アカウントの新しい名前を入力します。**重要:** ストレージ アカウントの名前は Azure 内で一意であり、小文字にする必要があります。
	 
	- **$Location:** スクリプトの所定の "West US" を使用するか、その他の Azure の場所 (East US、North Europe など) を選択します。
	  
	- **$ContainerName:** スクリプトの所定の名前を使用するか、コンテナーの新しい名前を入力します。
	
	- **$ImageToUpload:** ローカル コンピューター上の画像へのパスを入力します。たとえば、"C:\Images\HelloWorld.png" などです。
	
	- **$DestinationFolder:** Azure Storage からダウンロードしたファイルを格納するローカル ディレクトリへのパスを入力します。たとえば、"C:\DownloadImages" などです。

7.	"mystoragescript.ps1" ファイル内のスクリプト変数を更新したら、**[ファイル]**、**[保存]** の順にクリックします。次に、**[デバッグ]** をクリックし、**[実行/続行]** をクリックするか **F5** キーを押してスクリプトを実行します。  

スクリプトを実行すると、ダウンロードした画像ファイルを格納するローカルの格納先フォルダーの準備が整います。次のスクリーンショットは、この出力の例を示しています。

![Download Blobs][Image3]


> [AZURE.NOTE] 「5 分で始める、Azure Storage と PowerShell の使用」のセクションでは、Azure Storage で Azure PowerShell を使用する方法について簡単に説明しました。詳細と手順については、以下のセクションを参照することをお勧めします。

## <a name="pre"></a>Azure Storage で Azure PowerShell を使用するための前提条件
このガイドで取り上げている PowerShell コマンドレットを実行するには、Azure サブスクリプションとアカウントが必要です。使用可能な Azure サブスクリプション オプションについては、[Azure の概要](http://azure.microsoft.com/pricing/free-trial/)に関するページをご覧ください。

Azure PowerShell は、Windows PowerShell から Azure を管理するコマンドレットを提供するモジュールです。Azure PowerShell のインストールと設定の詳細については、「[Azure PowerShell のインストールと構成方法](http://azure.microsoft.com/documentation/articles/install-configure-powershell/)」をご覧ください。このガイドを使用する前に、最新の Azure PowerShell モジュールをダウンロードしてインストールするか、アップグレードすることをお勧めします。 

Azure PowerShell コンソール、標準の Windows PowerShell コンソール、または Windows PowerShell Integrated Scripting Environment (ISE) でコマンドレットを実行できます。たとえば、**Azure PowerShell コンソール**を開くには、[スタート] メニューで、「Microsoft Azure PowerShell」と入力して右クリックし、[管理者として実行] をクリックします。**Windows PowerShell ISE** を開くには、[スタート] メニューで、「管理ツール」と入力し、クリックして実行します。[管理ツール] ウィンドウで、[Windows PowerShell ISE] を右クリックし、[管理者として実行] をクリックします。設定オプションと構成オプションの詳細については、「[Azure PowerShell のインストールと構成方法](http://azure.microsoft.com/documentation/articles/install-configure-powershell/)」チュートリアルの Azure PowerShell のインストール方法に関するセクションをご覧ください。

## <a name="manageaccount"></a>Azure でストレージ アカウントを管理する方法
### <a name="setdefsub"></a>既定の Azure サブスクリプションを設定する方法
Azure PowerShell を使用して Azure Storage を管理するには、Azure Active Directory 認証または証明書ベースの認証を使用して、Azure でのクライアント環境を認証する必要があります。詳細については、「[方法:サブスクリプションへの接続](http://azure.microsoft.com/documentation/articles/install-configure-powershell/#Connect)」のセクションを「[Azure PowerShell のインストールと構成方法](http://azure.microsoft.com/documentation/articles/install-configure-powershell/)」チュートリアルで参照する必要があります。このガイドでは、Azure Active Directory 認証を使用します。

1.	Azure PowerShell コンソールまたは Windows PowerShell ISE で、次のコマンドを入力し、Azure アカウントをローカルの PowerShell 環境に追加します。

    `Add-AzureAccount`

2.	[Microsoft Azure へのサインイン] ウィンドウで、アカウントに関連付けられた電子メール アドレスとパスワードを入力します。Azure により資格情報が認証および保存され、ウィンドウが閉じます。

3.	次のコマンドを実行し、ローカルの PowerShell 環境内の Azure アカウントを表示して、自分のアカウントが含まれていることを確認します。
 
	`Get-AzureAccount`
  
4.	次のコマンドレットを実行し、ローカルの PowerShell セッションに接続されているすべてのサブスクリプションを表示して、自分のサブスクリプションが含まれていることを確認します。

	`Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName`
 
5.	既定の Azure サブスクリプションを設定するには、Select-AzureSubscription コマンドレットを実行します。
 
	    $SubscriptionName = 'Your subscription Name'
    	Select-AzureSubscription -SubscriptionName $SubscriptionName -Default

6.	Get-AzureSubscription コマンドレットを実行することで、既定のサブスクリプションの名前を確認します。

	`Get-AzureSubscription -Default`

7.	Azure Storage に使用できるすべての PowerShell コマンドレットを確認するには、次を実行します。

	`Get-Command -Module Azure -Noun *Storage*`

### <a name="createaccount"></a>新しい Azure ストレージ アカウントを作成する方法
Azure Storage を使用するには、ストレージ アカウントが必要です。コンピューターを構成してサブスクリプションに接続できるようにすると、新しい Azure ストレージ アカウントを作成できます。 

1.	Get-AzureLocation コマンドレットを実行し、使用できるデータセンターの場所をすべて見つけます。

    `Get-AzureLocation | format-Table -Property Name, AvailableServices, StorageAccountTypes`

2.	New-AzureStorageAccount コマンドレットを実行し、新しいストレージ アカウントを作成します。次の例では、"West US" というデータセンターに新しいストレージ アカウントを作成します。
  
    	$location = "West US"
	    $StorageAccountName = "yourstorageaccount"
	    New-AzureStorageAccount -StorageAccountName $StorageAccountName -Location $location

詳細については、「[Azure ストレージ アカウントについて](http://azure.microsoft.com/documentation/articles/storage-whatis-account/)」をご覧ください。

> [AZURE.IMPORTANT] ストレージ アカウントの名前は Azure 内で一意であり、小文字でなければなりません。名前付け規則と制限の詳細については、「[Azure ストレージ アカウントについて](http://azure.microsoft.com/documentation/articles/storage-whatis-account/)」のページ、「[コンテナー、BLOB、メタデータの名前付けと参照](http://msdn.microsoft.com/library/azure/dd135715.aspx)」、「[ストレージ アカウントの作成](http://msdn.microsoft.com/library/azure/hh264518.aspx)」をご覧ください。

### <a name="defaultaccount"></a>既定の Azure ストレージ アカウントを設定する方法
サブスクリプションで複数のストレージ アカウントを持つことができます。それらのいずれかのアカウントを選択し、同じ PowerShell セッションのすべてのストレージ コマンドに対する既定のストレージ アカウントとして設定できます。そうすることにより、ストレージ コンテキストを明示的に指定しなくても、Azure PowerShell ストレージ コマンドを実行できます。 

1.	サブスクリプションの既定のストレージ アカウントを設定するために、Set-AzureSubscription コマンドレットを実行します。 

		$SubscriptionName = "Your subscription name" 
     	$StorageAccountName = "yourstorageaccount"  
    	Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName 

2.	次に、Get-AzureSubscription コマンドレットを実行し、ストレージ アカウントが既定のサブスクリプション アカウントに関連付けられていることを確認します。このコマンドは、現在のサブスクリプションにおけるサブスクリプション プロパティを返します。これには、現在のストレージ アカウントが含まれます。

	    Get-AzureSubscription -Current

### <a name="listaccounts"></a>サブスクリプション内のすべての Azure ストレージ アカウントをリストする方法
Azure サブスクリプションごとに最大 100 個のストレージ アカウントを作成できます。制限に関する最新情報については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](http://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/)」をご覧ください。

次のコマンドレットを実行し、現在のサブスクリプションのストレージ アカウントの名前とステータスを確認します。

    Get-AzureStorageAccount | Format-Table -Property StorageAccountName, Location, AccountType, StorageAccountStatus

### <a name="createctx"></a>Azure ストレージ コンテキストを作成する方法
Azure ストレージ コンテキストは、ストレージ資格情報をカプセル化するための PowerShell 内のオブジェクトです。後続のコマンドレットを実行する際にストレージ コンテキストを使用すると、ストレージ アカウントとそのアクセス キー明示的に指定しなくても、要求を認証できます。ストレージ コンテキストは、ストレージ アカウント名とアクセス キーを使用する、共有アクセス署名 (SAS) トークンを使用する、接続文字列または匿名を使用するなどのさまざまな方法で作成できます。詳細については、「[New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx)」をご覧ください。  

次の 3 つの方法のいずれかを使用してストレージ コンテキストを作成します。

- [Get-AzureStorageKey](http://msdn.microsoft.com/library/azure/dn495235.aspx) コマンドレットを実行し、Azure ストレージ アカウントのプライマリ ストレージ アクセス キーを確認します。次に、[New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) コマンドレットを呼び出し、ストレージ コンテキストを作成します。

    	$StorageAccountName = "yourstorageaccount"
    	$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    	$Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary


- Generate a shared access signature token for an Azure storage container and use it to create a storage context:

    	$sasToken = New-AzureStorageContainerSASToken -Container abc -Permission rl
    	$Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -SasToken $sasToken

	詳細については、「[New-AzureStorageContainerSASToken](http://msdn.microsoft.com/library/azure/dn806416.aspx)」と「[共有アクセス署名、パート 1:SAS モデルについて](http://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)」をご覧ください。

- 場合によっては、新しいストレージ コンテキストの作成時にサービスのエンドポイントを指定することもあります。これは、BLOB サービスでストレージ アカウントのカスタム ドメイン名を登録した場合に必要になることがあります。または、アクセスしているストレージ リソースに対して共有アクセス署名を使用します。次に示すように、接続文字列でサービスのエンドポイントを設定し、このエンドポイントを使用して新しいストレージ コンテキストを作成します。

    	$ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint=<blobEndpoint>;QueueEndpoint=<QueueEndpoint>;TableEndpoint=<TableEndpoint>;AccountName=<AccountName>;AccountKey=<AccountKey>"
    	$Ctx = New-AzureStorageContext -ConnectionString $ConnectionString

ストレージ接続文字列を構成する方法の詳細については、[接続文字列の構成](http://msdn.microsoft.com/library/azure/ee758697.aspx)に関するページをご覧ください。

これで、コンピューターを設定できました。また、Azure PowerShell を使用してサブスクリプションとストレージ アカウントを管理する方法を確認できました。次のセクションでは、Azure BLOB と BLOB のスナップショットを管理する方法について確認します。

## <a name="manageblobs"></a>Azure BLOB と BLOB のスナップショットを管理する方法
Azure BLOB ストレージは、HTTP または HTTPS 経由で世界中のどこからでもアクセスできるテキストやバイナリ データなど、大量の非構造化データを格納するためのサービスです。このセクションでは、Azure BLOB ストレージ サービスの概念について理解しているユーザーを対象としています。詳細については、「[.NET から BLOB ストレージを使用する方法](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs)」や「[BLOB サービスの概念](http://msdn.microsoft.com/library/azure/dd179376.aspx)」をご覧ください。

### <a name="container"></a>コンテナーを作成する方法
Azure Storage のすべての BLOB はコンテナーに格納する必要があります。New-AzureStorageContainer コマンドレットを使用して、プライベート コンテナーを作成できます。

    $StorageContainerName = "yourcontainername"
    New-AzureStorageContainer -Name $StorageContainerName -Permission Off

> [AZURE.NOTE] 3 つのレベルの匿名読み取りアクセスがあります。**つまり、Off**、**Blob**、**Container** の 3 つです。BLOB に対する匿名アクセスを許可しない場合は、Permission パラメーターを **Off** に設定します。既定では、新しいコンテナーはプライベートであり、アカウント所有者のみがアクセスできます。BLOB リソースに対する匿名パブリック読み取りアクセスを許可するが、コンテナー メタデータまたはコンテナー内の BLOB の一覧に対するアクセスは許可しない場合は、Permission パラメーターを **BLOB** に設定します。BLOB リソース、コンテナー メタデータ、コンテナー内の BLOB の一覧に対する完全パブリック読み取りアクセスを許可する場合は、Permission パラメーターを **Container** に設定します。詳細については、[BlobContainerPublicAccessType](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storageclient.blobcontainerpublicaccesstype(v=azure.95).aspx 列挙型に関するページをご覧ください。

### <a name="uploadblob"></a>BLOB をコンテナーにアップロードする方法
Azure BLOB Storage では、ブロック BLOB とページ BLOB がサポートされています。詳細については、「[ブロック BLOB とページ BLOB について](http://msdn.microsoft.com/library/azure/ee691964.aspx)」をご覧ください。

BLOB をコンテナーにアップロードする場合は、[Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) コマンドレットを使用します。既定では、このコマンドにより、ローカル ファイルがブロック BLOB にアップロードされます。BLOB の種類を指定する場合は、-BlobType パラメーターを使用します。 

次の例では、[Get-ChildItem](http://technet.microsoft.com/library/hh849800.aspx) コマンドレットを実行して、指定したフォルダー内のすべてのファイルを取得し、パイプライン演算子を使用することでそれらを次のコマンドレットに渡します。[Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) コマンドレットは、ローカル ファイルをコンテナーにアップロードします。

    Get-ChildItem -Path C:\Images\* | Set-AzureStorageBlobContent -Container "yourcontainername"

### <a name="downblob"></a>BLOB をコンテナーからダウンロードする方法
次の例は、BLOB をコンテナーからダウンロードする方法を示しています。この例では、まず、ストレージ アカウント コンテキストを使用して Azure Storage への接続を確立します。このコンテキストには、ストレージ アカウント名とそのプライマリ アクセス キーが含まれます。次に、[Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) コマンドレットを使用して、BLOB の参照を取得します。さらに、[Get-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806418.aspx) コマンドレットを使用して、BLOB をローカルの格納先フォルダーにダウンロードします。 

    #Define the variables.
	ContainerName = "yourcontainername" 
    $DestinationFolder = "C:\DownloadImages" 
    
    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #List all blobs in a container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx
    
    #Download blobs from a container. 
    New-Item -Path $DestinationFolder -ItemType Directory -Force 
    $blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder -Context $Ctx

### <a name="copyblob"></a>あるストレージ コンテナーから別のストレージ コンテナーに BLOB をコピーする方法
BLOB は、ストレージ アカウントとリージョン間にまたがって非同期的にコピーできます。次の例は、2 つの異なるストレージ アカウントで、BLOB をあるストレージ コンテナーから別のストレージ コンテナーにコピーする方法を示しています。この例では、まずソースとコピー先のストレージ アカウントの変数を設定し、次にそれぞれのアカウントのストレージ コンテキストを作成します。さらに、[Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) コマンドレットを使用して、BLOB をソース コンテナーからコピー先のコンテナーにコピーします。この例では、ソースとコピー先のストレージ アカウントとコンテナーが既に存在していると想定しています。 

    #Define the source storage account and context.
    $SourceStorageAccountName = "yoursourcestorageaccount"
    $SourceStorageAccountKey = "Storage key for yoursourcestorageaccount"
    $SrcContainerName = "yoursrccontainername"
    $SourceContext = New-AzureStorageContext -StorageAccountName $SourceStorageAccountName -StorageAccountKey $SourceStorageAccountKey
    
    #Define the destination storage account and context.
    $DestStorageAccountName = "yourdeststorageaccount"
    $DestStorageAccountKey = "Storage key for yourdeststorageaccount"
    $DestContainerName = "destcontainername"
    $DestContext = New-AzureStorageContext -StorageAccountName $DestStorageAccountName -StorageAccountKey $DestStorageAccountKey
    
    #Get a reference to blobs in the source container.
    $blobs = Get-AzureStorageBlob -Container $SrcContainerName -Context $SourceContext
    
    #Copy blobs from one container to another.
    $blobs| Start-AzureStorageBlobCopy -DestContainer $DestContainerName -DestContext $DestContext

この例で実行するのは非同期コピーです。[Get-AzureStorageBlobCopyState](http://msdn.microsoft.com/library/azure/dn806406.aspx) コマンドレットを実行すると、各コピーの状態を監視できます。

### <a name="deleteblob"></a>BLOB を削除する方法
BLOB を削除するには、まず BLOB の参照を取得し、次にその Remove-AzureStorageBlob コマンドレットを呼び出します。次の例では、特定のコンテナー内のすべての BLOB を削除します。この例では、まずストレージ アカウントの変数を設定し、次にストレージ コンテキストを作成します。さらに、[Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) コマンドレットを使用して BLOB の参照を取得し、[Remove-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806399.aspx) コマンドレットを実行して Azure Storage のコンテナーから BLOB を削除します。 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "containername"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Get a reference to all the blobs in the container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx
    
    #Delete blobs in a specified container.
    $blobs| Remove-AzureStorageBlob 

## <a name="manageshots"></a>Azure BLOB のスナップショットを管理する方法
Azure では BLOB のスナップショットを作成できます。スナップショットは、ある時点で作成された読み取り専用の BLOB です。作成されたスナップショットは、読み取り、コピー、削除はできますが、変更はできません。スナップショットを使用すると、BLOB をある時点での表示内容のままバックアップできます。詳細については、「[BLOB のスナップショットの作成](http://msdn.microsoft.com/library/azure/hh488361.aspx)」をご覧ください。

### <a name="createshot"></a>BLOB のスナップショットを作成する方法
BLOB のスナップショットを作成するには、まず BLOB の参照を取得し、次にその ICloudBlob.CreateSnapshot メソッドを呼び出します。次の例では、まずストレージ アカウントの変数を設定し、次にストレージ コンテキストを作成します。さらに、[Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) コマンドレッドを使用して BLOB の参照を取得し、[ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) メソッドを実行してスナップショットを作成します。 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "yourcontainername"
    $BlobName = "yourblobname"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $ContainerName -Blob $BlobName
    
    #Create a snapshot of the blob.
    $snap = $blob.ICloudBlob.CreateSnapshot() 

### <a name="listshot"></a>BLOB のスナップショットをリストする方法
1 個の BLOB に対して必要な数だけスナップショットを作成できます。BLOB に関連付けられたスナップショットをリストして、現在のスナップショットを追跡できます。次の例では、事前定義済みの BLOB を使用し、[Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) コマンドレットを呼び出して、その BLOB のスナップショットをリストします。  

    #Define the blob name.
    $BlobName = "yourblobname"
    
    #List the snapshots of a blob.
    Get-AzureStorageBlob -Context $Ctx -Prefix $BlobName -Container $ContainerName  | Where-Object  { $_.ICloudBlob.IsSnapshot -and $_.Name -eq $BlobName }

### <a name="copyshot"></a>BLOB のスナップショットをコピーする方法
BLOB のスナップショットをコピーして復元できます。詳細と制限については、「[BLOB のスナップショットの作成](http://msdn.microsoft.com/library/azure/hh488361.aspx)」をご覧ください。次の例では、まずストレージ アカウントの変数を設定し、次にストレージ コンテキストを作成します。さらに、コンテナーと BLOB 名変数を定義します。また、[Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) コマンドレットを使用して BLOB の参照を取得し、[ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) メソッドを実行してスナップショットを作成します。さらに、[Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) コマンドレットを実行し、ソース BLOB に対して ICloudBlob オブジェクトを使用して BLOB のスナップショットをコピーします。この例を実行する前に、必ず構成に基づいて変数を更新してください。次の例では、ソースとコピー先のコンテナーとソース BLOB が既に存在していると想定しています。 

    #ストレージ アカウントとコンテキストの定義
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Define the variables.
    $SrcContainerName = "yoursourcecontainername"
    $DestContainerName = "yourdestcontainername"
    $SrcBlobName = "yourblobname"
    $DestBlobName = "CopyBlobName"
    
    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $SrcContainerName -Blob $SrcBlobName
    
    #Create a snapshot of a blob.
    $snap = $blob.ICloudBlob.CreateSnapshot() 
    
    #Copy the snapshot to another container.
    Start-AzureStorageBlobCopy -Context $Ctx -ICloudBlob $snap -DestBlob $DestBlobName -DestContainer $DestContainerName

これで、Azure PowerShell で Azure BLOB と BLOB スナップショットを管理する方法を確認できました。次のセクションでは、テーブル、キュー、ファイルを管理する方法について確認します。

## <a name="managetables"></a>Azure テーブルとテーブル エンティティを管理する方法
Azure Table Storage サービスは NoSQL データストアであり、これを使用することで、構造化された非リレーショナル データの巨大なセットを格納して照会できます。このサービスのメイン コンポーネントは、テーブル、エンティティ、プロパティです。テーブルは、エンティティのコレクションです。エンティティは、プロパティのセットです。各エンティティには、最大 252 個のプロパティを含むことができます。これらはすべて名前と値のペアです。このセクションでは、Azure Table ストレージ サービスの概念について理解しているユーザーを対象としています。詳細については、「[テーブル サービス データ モデルについて](http://msdn.microsoft.com/library/azure/dd179338.aspx)」と「[.NET からテーブル ストレージを使用する方法](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-tables/)」をご覧ください。

以下のサブセクションでは、Azure PowerShell を使用して Azure Table ストレージ サービスを管理する方法について説明します。**テーブルの****作成**、**削除**、**取得**、**テーブル エンティティの追加**、**照会**、**削除**の各シナリオについて説明します。

### <a name="createtable"></a>テーブルを作成する方法
各テーブルは、Azure ストレージ アカウント内に存在します。次の例は、Azure Storage にテーブルを作成する方法を示しています。この例では、まず、ストレージ アカウント コンテキストを使用して Azure Storage への接続を確立します。このコンテキストには、ストレージ アカウント名とそのアクセス キーが含まれます。次に、[New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) コマンドレットを使用して、Azure Storage にテーブルを作成します。 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey 
    
    #Create a new table.
    $tabName = "yourtablename"
    New-AzureStorageTable -Name $tabName -Context $Ctx 

### <a name="gettable"></a>テーブルを取得する方法
あるストレージ アカウント内の 1 個またはすべてのテーブルを照会して取得できます。次の例は、[Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) コマンドレットを使用して特定のテーブルを取得する方法を示しています。

    #Retrieve a table.
    $tabName = "yourtablename"
    Get-AzureStorageTable -Name $tabName -Context $Ctx 

パラメーターなしで Get-AzureStorageTable コマンドレットを呼び出した場合は、ストレージ アカウントのすべてのストレージ テーブルを取得します。

### <a name="remtable"></a>テーブルを削除する方法
[Remove-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806393.aspx) コマンドレットを使用すると、ストレージ アカウントからテーブルを削除できます。  

    #Delete a table.
    $tabName = "yourtablename"
    Remove-AzureStorageTable -Name $tabName -Context $Ctx 

### <a name="mngentity"></a>テーブル エンティティを管理する方法
現在、Azure PowerShell では、テーブル エンティティを直接管理するコマンドレットは用意されていません。テーブル エンティティに対する操作を実行するには、[Azure Storage Client Library for .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx) で指定されているクラスを使用します。 

#### <a name="addentity"></a>テーブル エンティティを追加する方法
テーブルにエンティティを追加するには、まず、エンティティのプロパティを定義するオブジェクトを作成します。1 個のエンティティには、最大 255 個のプロパティを含むことができます。これには 3 個のシステム プロパティも含まれます。**つまり、PartitionKey**、**RowKey**、**Timestamp** の 3 個です。**PartitionKey** と **RowKey** の値を挿入または更新することはユーザーが担当します。一方、サーバーは **Timestamp** の値を管理します。この値は変更できません。**PartitionKey** と **RowKey** が組み合わさって、テーブル内の各エンティティを一意に識別します。 

-	**PartitionKey**: エンティティが格納されるパーティションを決定します。
-	**RowKey**: パーティション内のエンティティを一意に識別します。

1 個のエンティティに対して最大 252 個のカスタム プロパティを定義できます。詳細については、「[テーブル サービス データ モデルについて](http://msdn.microsoft.com/library/azure/dd179338.aspx)」をご覧ください。

次の例は、エンティティをテーブルに追加する方法を示しています。この例では、employee テーブルを取得して、そこに複数のエンティティを追加する方法を取り上げます。まず、ストレージ アカウント コンテキストを使用して Azure Storage への接続を確立します。このコンテキストには、ストレージ アカウント名とそのアクセス キーが含まれます。次に、[Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) コマンドレットを使用して特定のテーブルを取得します。このテーブルが存在しない場合は、Azure Storage にテーブルを作成するために [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) コマンドレットが使用されます。次に、各エンティティのパーティションと行キーを指定することでテーブルにエンティティを追加する、カスタム関数 Add-Entity が定義されます。この Add-Entity 関数は、[Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) クラスで [New-Object](http://technet.microsoft.com/library/hh849885.aspx) コマンドレットを呼び出し、エンティティ オブジェクトを作成します。その後、このエンティティ オブジェクトで [Microsoft.WindowsAzure.Storage.Table.TableOperation.Insert](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.insert.aspx) メソッドを呼び出し、テーブルに追加します。 

    #Function Add-Entity: Adds an employee entity to a table.
    function Add-Entity()
    {
    param(
       $table, 
       [String]$partitionKey, 
       [String]$rowKey,
       [String]$name, 
       [Int]$id
    )
    
      $entity = New-Object Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity $partitionKey, $rowKey
      $entity.Properties.Add("Name", $name)
      $entity.Properties.Add("ID", $id)
    
      $result = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Insert($entity))
    }
    
    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey
    $TableName = "Employees"
    
    #Retrieve the table if it already exists.
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore
    
    #Create a new table if it does not exist.
    if ($table -eq $null)
    {
       $table = New-AzureStorageTable -Name $TableName -Context $Ctx
    }
    
    #Add multiple entities to a table.
    Add-Entity $table "Partition1" "Row1" "Chris" 1 
    Add-Entity $table "Partition1" "Row2" "Jessie" 2 
    Add-Entity $table "Partition2" "Row1" "Christine" 3 
    Add-Entity $table "Partition2" "Row2" "Steven" 4 


#### <a name="queryentity"></a>テーブル エンティティを照会する方法
テーブルを照会するには、[Microsoft.WindowsAzure.Storage.Table.TableQuery](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tablequery.aspx) クラスを使用します。次の例では、このガイドのエンティティの追加方法に関するセクションで指定されたスクリプトを実行済みであると想定しています。この例では、まず、ストレージ コンテキストを使用して Azure Storage への接続を確立します。このコンテキストには、ストレージ アカウント名とそのアクセス キーが含まれます。次に、[Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) コマンドレットを使用して、作成済みの Employees テーブルの取得を試みます。Microsoft.WindowsAzure.Storage.Table.TableQuery クラスで [New-Object](http://technet.microsoft.com/library/hh849885.aspx) コマンドレットを呼び出すと、新しいクエリ オブジェクトが作成されます。そして、文字列フィルターで指定されたとおりの 1 を値とする "ID" 列があるエンティティを見つけます。詳細については、「[テーブルとエンティティのクエリ](http://msdn.microsoft.com/library/azure/dd894031.aspx)」の「フィルター文字列の作成」のセクションをご覧ください。このクエリを実行すると、フィルター条件に一致するすべてのエンティティが返されます。    

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    $TableName = "Employees"
    
    #Get a reference to a table.
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx
    
    #Create a table query.
    $query = New-Object Microsoft.WindowsAzure.Storage.Table.TableQuery
    
    #Define columns to select. 
    $list = New-Object System.Collections.Generic.List[string] 
    $list.Add("RowKey")
    $list.Add("ID")
    $list.Add("Name")
    
    #Set query details.
    $query.FilterString = "ID gt 0"
    $query.SelectColumns = $list
    $query.TakeCount = 20
    
    #Execute the query.
    $entities = $table.CloudTable.ExecuteQuery($query)
    
    #Display entity properties with the table format.
    $entities  | Format-Table PartitionKey, RowKey, @{ Label = "Name"; Expression={$_.Properties["Name"].StringValue}}, @{ Label = "ID"; Expression={$_.Properties["ID"].Int32Value}} -AutoSize 

#### <a name="deleteentity"></a>テーブル エンティティを削除する方法
パーティション キーと行キーを使用してエンティティを削除できます。次の例では、このガイドのエンティティの追加方法に関するセクションで指定されたスクリプトを実行済みであると想定しています。この例では、まず、ストレージ コンテキストを使用して Azure Storage への接続を確立します。このコンテキストには、ストレージ アカウント名とそのアクセス キーが含まれます。次に、[Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) コマンドレットを使用して、作成済みの Employees テーブルの取得を試みます。このテーブルが存在する場合は、[Microsoft.WindowsAzure.Storage.Table.TableOperation.Retrieve](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx) メソッドを呼び出し、パーティションと行キーの値に基づいてエンティティを取得します。次に、このエンティティを [Microsoft.WindowsAzure.Storage.Table.TableOperation.Delete](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.delete.aspx) メソッドに渡します。 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Retrieve the table.
    $TableName = "Employees"
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore

    #If the table exists, start deleting its entities.
    if ($table -ne $null)
    {
       #Together the PartitionKey and RowKey uniquely identify every  
       #entity within a table.
       $tableResult = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Retrieve("Partition2", "Row1"))
       $entity = $tableResult.Result;
    if ($entity -ne $null) 
    {
       #Delete the entity.$table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Delete($entity))
    }
    }

## <a name="managequeues"></a>Azure キューとキュー メッセージを管理する方法
Azure キュー ストレージは、HTTP または HTTPS を使用した認証された呼び出しを介して世界中のどこからでもアクセスできる大量のメッセージを格納するためのサービスです。このセクションでは、Azure Queue ストレージ サービスの概念について理解しているユーザーを対象としています。詳細については、「[.NET からキュー ストレージを使用する方法](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-queues/)」と「[Queue サービスの概念](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-queues/#concepts)」をご覧ください。

このセクションでは、Azure PowerShell を使用して Azure Queue ストレージ サービスを管理する方法を取り上げます。キュー メッセージの**挿入**、**削除**、**キューの作成**、**削除**、**取得**の各シナリオについて説明します。
 
### <a name="createqueue"></a>キューを作成する方法
次の例では、まず、ストレージ アカウント コンテキストを使用して Azure Storage への接続を確立します。このコンテキストには、ストレージ アカウント名とそのアクセス キーが含まれます。次に、[New-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806382.aspx) コマンドレットを呼び出して "queuename" という名前のキューを作成します。 

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    $QueueName = "queuename"
    $Queue = New-AzureStorageQueue -Name $QueueName -Context $Ctx 

Azure Queue サービスでの名前付け規則の詳細については、「[キューとメタデータの名前付け](http://msdn.microsoft.com/library/azure/dd179349.aspx)」をご覧ください。

### <a name="getqueue"></a>キューを取得する方法
あるストレージ アカウント内の特定のキューまたはすべてのキューの一覧を照会して取得できます。次の例は、[Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) コマンドレットを使用して特定のキューを取得する方法を示しています。

    #Retrieve a queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $Ctx 

パラメーターなしで [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) コマンドレットを呼び出した場合は、すべてのキューの一覧を取得します。

### <a name="remqueue"></a>キューを削除する方法
キューとキューに含まれるすべてのメッセージを削除するには、Remove-AzureStorageQueue コマンドレットを呼び出します。次の例は、Remove-AzureStorageQueue コマンドレットを使用して特定のキューを削除する方法を示しています。 

    #Delete a queue.
    $QueueName = "yourqueuename"
    Remove-AzureStorageQueue -Name $QueueName -Context $Ctx 

### <a name="mngqueuemsg"></a>キュー メッセージを管理する方法
現在、Azure PowerShell では、キュー メッセージを直接管理するコマンドレットは用意されていません。キュー メッセージに対する操作を実行するには、[Azure Storage Client Library for .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx) で指定されているクラスを使用します。 

#### <a name="addqueuemsg"></a>メッセージをキューに挿入する方法
既存のキューにメッセージを挿入するには、まず [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) クラスの新しいインスタンスを作成します。次に、[AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) メソッドを呼び出します。CloudQueueMessage は、文字列 (UTF-8 形式) またはバイト配列から作成できます。
 
次の例は、メッセージをキューに追加する方法を示しています。この例では、まず、ストレージ アカウント コンテキストを使用して Azure Storage への接続を確立します。このコンテキストには、ストレージ アカウント名とそのアクセス キーが含まれます。次に、[Get-AzureStorageQueue](https://msdn.microsoft.com/library/azure/dn806377.aspx) コマンドレットを使用して特定のキューを取得します。このキューが存在する場合は、[New-Object](http://technet.microsoft.com/library/hh849885.aspx) コマンドレットを使用して、[Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) クラスのインスタンスが作成されます。その後、このメッセージ オブジェクトで [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) メソッドを呼び出して、キューに追加します。次のコードでは、キューを取得し、メッセージ  'MessageInfo' を挿入します。

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx 
    
    #If the queue exists, add a new message.
    if ($Queue -ne $null)
    {
       # Create a new message using a constructor of the CloudQueueMessage class.
       $QueueMessage = New-Object "Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage" "MessageInfo"
    
       #Add a new message to the queue.
       $Queue.CloudQueue.AddMessage($QueueMessage)
    } 


#### <a name="dequeuemsg"></a>次のメッセージでデキューする方法
コードでは、2 つの手順でキューからメッセージをデキューします。[Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) を呼び出すと、キュー内の次のメッセージを取得します。**GetMessage** から返されたメッセージは、このキューからメッセージを読み取る他のコードから参照できなくなります。また、キューからのメッセージの削除を完了するには、[Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) メソッドを呼び出す必要があります。このようにメッセージを 2 つの手順で削除することで、ハードウェアまたはソフトウェアの問題が原因でコードによるメッセージの処理が失敗した場合に、コードの別のインスタンスで同じメッセージを取得し、もう一度処理することができます。コードでは、メッセージが処理された直後に **DeleteMessage** を呼び出します。

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    
    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx
    
    $InvisibleTimeout = [System.TimeSpan]::FromSeconds(10)
    
    #Get the message object from the queue.
    $QueueMessage = $Queue.CloudQueue.GetMessage($InvisibleTimeout)
    #Delete the message.
    $Queue.CloudQueue.DeleteMessage($QueueMessage) 

## <a name="files"></a>Azure ファイル共有とファイルを管理する方法
Azure File ストレージは、標準の SMB 2.1 プロトコルを使用して、アプリケーション用の共有ストレージを提供します。Microsoft Azure の仮想マシンとクラウド サービスでは、マウントされている共有を介して、アプリケーション コンポーネント間でファイル データを共有できます。内部設置型のアプリケーションでは、File ストレージ API または Azure PowerShell を介して、共有内のファイル データにアクセスできます。

Azure File ストレージの詳細については、「[Azure File ストレージを使用する方法](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/)」と「[ファイル サービス REST API](http://msdn.microsoft.com/library/azure/dn167006.aspx)」をご覧ください。

Azure PowerShell を使用して Azure File ストレージを管理する方法の詳細については、「[PowerShell を使用したファイル共有の作成](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#use-cmdlets)」をご覧ください。

## <a name="stganalytics"></a>ストレージ分析を設定して照会する方法
[Azure Storage Analytics](http://msdn.microsoft.com/library/azure/hh343270.aspx) では、Azure ストレージ アカウントからメトリック (ストレージ メトリック) を収集すること、ストレージ アカウントに送信される要求に関するログ データ (ストレージ ログ) を収集することが可能です。ストレージ メトリックを使用すると、ストレージ アカウントの正常性を監視でき、ストレージ ログを使用すると、ストレージ アカウントに関する問題の診断とトラブルシューティングができます。
既定では、Storage サービスに対してストレージ メトリックは有効になっていません。Azure 管理ポータルか Windows PowerShell を利用するか、ストレージ API にプログラムを作成することで監視を有効にできます。ストレージ ログはサーバー側で発生し、ストレージ アカウント内の成功した要求と失敗した要求の両方について詳細を記録することが可能になります。これらのログを使用すると、読み取り、書き込み、削除の各操作の詳細、失敗した要求の原因を確認できます。

PowerShell を使用し、ストレージ メトリックのデータを有効にして表示する方法については、「[PowerShell を利用してストレージ メトリックを有効にする方法](http://msdn.microsoft.com/library/azure/dn782843.aspx#HowtoenableStorageMetricsusingPowerShell)」をご覧ください。

PowerShell を使用し、ストレージ ログのデータを有効にして取得する方法の詳細については、 
「[PowerShell を使用してストレージ ログを有効にする方法](http://msdn.microsoft.com/library/azure/dn782840.aspx#HowtoenableStorageLoggingusingPowerShell)」と「[ストレージ ログのログ データの検索](http://msdn.microsoft.com/library/azure/dn782840.aspx#FindingyourStorageLogginglogdata)」をご覧ください。
ストレージ メトリックとストレージ ログを使用してストレージの問題のトラブルシューティングを行う方法の詳細については、「[Microsoft Azure ストレージの監視、診断、トラブルシューティング](http://azure.microsoft.com/documentation/articles/storage-monitoring-diagnosing-troubleshooting/)」をご覧ください。

## <a name="sas"></a>Shared Access Signature (SAS) や格納されているアクセス ポリシーの選択を管理する方法
共有アクセス署名は、Azure のストレージを使用するあらゆるアプリケーションのセキュリティ モデルの重要な部分となります。アカウント キーを知らせずに、ストレージ アカウントへの制限付きアクセス許可をクライアントに付与する場合に便利です。既定では、ストレージ アカウントの所有者のみがそのアカウントを使って BLOB、テーブル、キューなどにアクセスできます。サービスやアプリケーションで、アクセス キーを共有せずに他のクライアントでそれらのリソースを使えるようにするには、次の 3 つの方法があります。

- 匿名ユーザーにコンテナーや BLOB への読み込みアクセスを許可するようにコンテナーのアクセス許可を設定します。これはテーブルやキューでは許可されません。
- 共有アクセス署名を使用して、特定の期間のコンテナー、BLOB、キュー、テーブルへの制限付きアクセス権を付与します。
- 保存されたアクセス ポリシーを使用して、コンテナー、BLOB、キュー、テーブルに対する共有アクセス署名の制御を追加します。保存されたアクセス ポリシーによって、開始時刻、有効期限、署名の許可の変更や、署名が発行された後の取り消しが行えるようになります。

共有アクセス署名の形式は、次の 2 つのいずれかです。

- **アドホック SAS**:アドホック SAS を作成すると、開始時刻、有効期限、SAS へのアクセス許可がすべて、SAS URI で指定されます。この種類の SAS は、コンテナー、BLOB、テーブル、キューで作成できます。これは取り消しできません。
- **保存されているアクセス ポリシーによる SAS**:保存されているアクセス ポリシーは、リソース コンテナー (BLOB コンテナー、テーブル、キュー) で定義されており、これを使用して、1 つ以上の共有アクセス署名の制限を管理できます。保存されているアクセス ポリシーに SAS を関連付けると、SAS は、保存されているアクセス ポリシーに定義されている制約 (開始時刻、有効期限、およびアクセス許可) を継承します。この種類の SAS は、取り消しできません。

詳細については、「[共有アクセス署名]」の(storage-dotnet-shared-access-signature-part-1.md)[パート 1:SAS モデルについて](storage-dotnet-shared-access-signature-part-1.md)」と「[Microsoft Azure ストレージ リソースへのアクセスの管理](http://msdn.microsoft.com/library/azure/ee393343.aspx)」をご覧ください。

次のセクションでは、Azure テーブルに対する共有アクセス署名トークンと保存されたアクセス ポリシーを作成する方法を学習します。Azure PowerShell では、コンテナー、BLOB、キューに対して類似するコマンドレットが提供されます。このセクションでスクリプトを実行するには、[Azure PowerShell バージョン 0.8.14](http://go.microsoft.com/?linkid=9811175&clcid=0x409) 以降のバージョンをダウンロードしてください。

### <a name="sub1"></a>ポリシー ベースの共有アクセス署名トークンを作成する方法
New-AzureStorageTableStoredAccessPolicy コマンドレットを使用して新しい保存アクセス ポリシーを作成します。次に、[New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) コマンドレットを呼び出し、Azure ストレージ テーブルに対する新しいポリシー ベースの共有アクセス署名トークンを作成します。

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
    New-AzureStorageTableSASToken -Name $tableName -Policy $policy -Context $Ctx

### <a name="sub2"></a>アドホック (取り消し不可) の共有アクセス署名トークンを作成する方法
次のように [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) コマンドレットを使用して、Azure ストレージ テーブルに対するアドホック (取り消し不可) の共有アクセス署名トークンを作成します。

    New-AzureStorageTableSASToken -Name $tableName -Permission "rqud" -StartTime "2015-01-01" -ExpiryTime "2015-02-01" -Context $Ctx

### <a name="sub3"></a>保存されているアクセス ポリシーを作成する方法
次のように New-AzureStorageTableStoredAccessPolicy コマンドレットを使用して、Azure ストレージ テーブルの新しく保存されているアクセス ポリシーを作成します。

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx

### <a name="sub4"></a>保存されているアクセス ポリシーを更新する方法
次のように Set-AzureStorageTableStoredAccessPolicy コマンドレットを使用して、Azure ストレージ テーブルの既存の保存されているアクセス ポリシーを更新します。

    Set-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Permission "rd" -NoExpiryTime -NoStartTime -Context $Ctx

### <a name="sub5"></a>保存されているアクセス ポリシーを削除する方法
次のように Remove-AzureStorageTableStoredAccessPolicy コマンドレットを使用して、Azure ストレージ テーブルの保存されているアクセス ポリシーを削除します。

    Remove-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Context $Ctx


## <a name="gov"></a>米国政府対応の Azure Storage や Azure China を使用する方法
[米国政府対応の Azure Government](http://azure.microsoft.com/features/gov/)、[グローバル Azure 対応の AzureCloud](https://manage.windowsazure.com)、[中国の 21Vianet が運営する Azure 対応の AzureChinaCloud](http://www.windowsazure.cn/) など、Microsoft Azure ではそれぞれ独立した環境をサポートしています。米国政府対応の新しい Azure 環境や Azure China をデプロイできます。 

AzureChinaCloud で Azure Storage を使用するには、AzureChinaCloud に関連付けられたストレージ コンテキストを作成する必要があります。次の手順に従って開始します。

1.	[Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) コマンドレットを実行し、使用できる Azure 環境を確認します。

    `Get-AzureEnvironment`

2.	Azure China のアカウントを Windows PowerShell に追加します。

    `Add-AzureAccount -Environment AzureChinaCloud`

3.	AzureChinaCloud アカウントのストレージ コンテキストを作成します。

    	$Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureChinaCloud

[U.S. Azure Government](http://azure.microsoft.com/features/gov/) で Azure Storage を使用するには、新しい環境を定義し、その環境で新しいストレージ コンテキストを作成する必要があります。

1. [Add-AzureEnvironment](http://msdn.microsoft.com/library/azure/dn790364.aspx) コマンドレットを呼び出し、プライベート データセンターの新しい Azure 環境を作成します。 

    	Add-AzureEnvironment -Name $EnvironmentName -PublishSettingsFileUrl $publishSettingsFileUrl -ServiceEndpoint $serviceEndpoint -ManagementPortalUrl $managementPortalUrl -StorageEndpoint $storageEndpoint -ActiveDirectoryEndpoint $activeDirectoryEndpoint -ResourceManagerEndpoint $resourceManagerEndpoint -GalleryEndpoint $galleryEndpoint -ActiveDirectoryServiceEndpointResourceId $activeDirectoryServiceEndpointResourceId -GraphEndpoint $graphEndpoint -SubscriptionDataFile $subscriptionDataFile

2. 次に示すように、[New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) コマンドレットを実行し、この新しい環境の新しいストレージ コンテキストを作成します。 
   
	    $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment $EnvironmentName

詳細については、

- 「[Microsoft Azure Government Developer Guide (Microsoft Azure Government 開発者向けガイド)]」をご覧ください(azure-government-developer-guide.md)。 
- [グローバル Azure 対応の AzureCloud と中国の 21Vianet が運営する Azure 対応の AzureChinaCloud の相違点](https://msdn.microsoft.com/library/azure/dn578439.aspx)に関するページ

## <a name="next"></a>次のステップ
このガイドでは、Azure PowerShell を使用して Azure Storage を管理する方法を確認しました。詳細についての関連記事とリソースがあります。

- [Azure ストレージのドキュメント](http://azure.microsoft.com/documentation/services/storage/)
- [Azure Storage の MSDN リファレンス](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- [Azure Storage の PowerShell コマンドレット](http://msdn.microsoft.com/library/azure/dn806401.aspx)
- [Windows PowerShell リファレンス](https://msdn.microsoft.com/library/ms714469.aspx)

[Image1]: ./media/storage-powershell-guide-full/Subscription_currentportal.png
[Image2]: ./media/storage-powershell-guide-full/Subscription_Previewportal.png
[Image3]: ./media/storage-powershell-guide-full/Blobdownload.png


[5 分で始める、Azure Storage と PowerShell の使用]: #getstart
[Azure Storage で Azure PowerShell を使用するための前提条件]: #pre
[Azure でストレージ アカウントを管理する方法]: #manageaccount
[既定の Azure サブスクリプションを設定する方法]: #setdefsub
[新しい Azure ストレージ アカウントを作成する方法]: #createaccount
[既定の Azure ストレージ アカウントを設定する方法]: #defaultaccount
[サブスクリプション内のすべての Azure ストレージ アカウントをリストする方法]: #listaccounts
[Azure ストレージ コンテキストを作成する方法]: #createctx
[Azure BLOB と BLOB のスナップショットを管理する方法]: #manageblobs
[コンテナーを作成する方法]: #container
[BLOB をコンテナーにアップロードする方法]: #uploadblob
[BLOB をコンテナーからダウンロードする方法]: #downblob
[あるストレージ コンテナーから別のストレージ コンテナーに BLOB をコピーする方法]: #copyblob
[BLOB を削除する方法]: #deleteblob
[Azure BLOB のスナップショットを管理する方法]: #manageshots
[BLOB のスナップショットを作成する方法]: #createshot
[BLOB のスナップショットをリストする方法]: #listshot
[BLOB のスナップショットをコピーする方法]: #copyshot
[Azure テーブルとテーブル エンティティを管理する方法]: #managetables
[テーブルを作成する方法]: #createtable
[テーブルを取得する方法]: #gettable
[テーブルを削除する方法]: #remtable
[テーブル エンティティを管理する方法]: #mngentity
[テーブル エンティティを追加する方法]: #addentity
[テーブル エンティティを照会する方法]: #queryentity
[テーブル エンティティを削除する方法]: #deleteentity
[Azure キューとキュー メッセージを管理する方法]: #managequeues
[キューを作成する方法]: #createqueue
[キューを取得する方法]: #getqueue
[キューを削除する方法]: #remqueue
[キュー メッセージを管理する方法]: #mngqueuemsg
[メッセージをキューに挿入する方法]: #addqueuemsg
[次のメッセージでデキューする方法]: #dequeuemsg
[Azure ファイル共有とファイルを管理する方法]: #files
[ストレージ分析を設定して照会する方法]: #stganalytics
[Shared Access Signature (SAS) や格納されているアクセス ポリシーの選択を管理する方法]: #sas
[米国政府対応の Azure Storage や Azure China を使用する方法]: #gov
[次のステップ]: #next

<!--HONumber=47-->
