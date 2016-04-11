

## Azure CLI

> [AZURE.NOTE] この記事では、最新バージョンの Azure CLI または Azure PowerShell を使用して、仮想マシン イメージをナビゲートして選択する方法について説明します。前提条件として、リソース マネージャー モードに変更しておく必要があります。Azure CLI を使用している場合、次のように入力してこのモードに入ります`azure config mode arm`。

`azure vm quick-create` と共に使用するため、またはリソース グループのテンプレート ファイルを作成するための、イメージを検索する簡単で素早い方法は、`azure vm image list` コマンドを呼び出して、場所、発行元名 (大文字と小文字は区別されません)、およびプラン (プランを知っている場合) を渡すことです。たとえば、次のリストは、"Canonical" が "UbuntuServer" プランの発行元であることを知っている場合の短い例にすぎません。多数のリストは非常に長くなります。

    azure vm image list westus canonical ubuntuserver
    info:    Executing command vm image list
    warn:    The parameter --sku if specified will be ignored
    + Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
    data:    Publisher  Offer         Sku          Version          Location  Urn
    data:    ---------  ------------  -----------  ---------------  --------  --------------------------------------------------
    data:    canonical  ubuntuserver  12.04-DAILY  12.04.201504201  westus    canonical:ubuntuserver:12.04-DAILY:12.04.201504201
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201302250  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201302250
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201303250  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201303250
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201304150  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201304150
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201305160  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201305160
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201305270  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201305270
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201306030  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201306030
    data:    canonical  ubuntuserver  12.04.2-LTS  12.04.201306240  westus    canonical:ubuntuserver:12.04.2-LTS:12.04.201306240

**Urn** 列は、`azure vm quick-create` に渡すフォームです。

ただし、多くの場合、何が使用可能かをまだ知りません。この場合は、まず `azure vm image list-publishers` を使用して発行元を検出し、場所のプロンプトにはリソース グループに使用するデータ センターの場所を指定して応答することにより、イメージをナビゲートできます。たとえば、以下は米国西部の場所にあるすべてのイメージ発行元をリストしたものです (場所の引数は、標準の場所名を小文字化し、スペースを削除して渡します)

    azure vm image list-publishers
    info:    Executing command vm image list-publishers
    Location: westus
    + Getting virtual machine image publishers (Location: "westus")
    data:    Publisher                                       Location
    data:    ----------------------------------------------  --------
    data:    a10networks                                     westus  
    data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
    data:    alertlogic                                      westus  
    data:    AlertLogic.Extension                            westus  


これらのリストは非常に長くなることがあるため、上記のリスト例は抜粋にすぎません。たとえば、「Canonical 」が、実際に米国西部の場所にあるイメージ発行元であることに気付いたとします。これで、次の例のように、`azure vm image list-offers` を呼び出して、プロンプトが出されたときに場所と発行元を渡すことで、プランを検索できます。

    azure vm image list-offers
    info:    Executing command vm image list-offers
    Location: westus
    Publisher: canonical
    + Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
    data:    Publisher  Offer         Location
    data:    ---------  ------------  --------
    data:    canonical  UbuntuServer  westus  
    info:    vm image list-offers command OK

これで、米国西部のリージョンで、Canonical が Azure で **UbuntuServer** プランを発行していることが分かりました。どんな SKU でしょうか。 それらを取得するには、`azure vm image list-skus` を呼び出して、プロンプトが出されたときに場所、発行元、検出したプランを指定して応答します。

    azure vm image list-skus
    info:    Executing command vm image list-skus
    Location: westus
    Publisher: canonical
    Offer: ubuntuserver
    + Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
    data:    Publisher  Offer         sku          Location
    data:    ---------  ------------  -----------  --------
    data:    canonical  ubuntuserver  12.04-DAILY  westus  
    data:    canonical  ubuntuserver  12.04.2-LTS  westus  
    data:    canonical  ubuntuserver  12.04.3-LTS  westus  
    data:    canonical  ubuntuserver  12.04.4-LTS  westus  
    data:    canonical  ubuntuserver  12.04.5-LTS  westus  
    data:    canonical  ubuntuserver  12.10        westus  
    data:    canonical  ubuntuserver  14.04-beta   westus  
    data:    canonical  ubuntuserver  14.04-DAILY  westus  
    data:    canonical  ubuntuserver  14.04.0-LTS  westus  
    data:    canonical  ubuntuserver  14.04.1-LTS  westus  
    data:    canonical  ubuntuserver  14.04.2-LTS  westus  
    data:    canonical  ubuntuserver  14.10        westus  
    data:    canonical  ubuntuserver  14.10-beta   westus  
    data:    canonical  ubuntuserver  14.10-DAILY  westus  
    data:    canonical  ubuntuserver  15.04        westus  
    data:    canonical  ubuntuserver  15.04-beta   westus  
    data:    canonical  ubuntuserver  15.04-DAILY  westus  
    info:    vm image list-skus command OK

この情報を使用すると、元の呼び出しを先頭で呼び出すことによって、必要なイメージを正確に検索できます。

    azure vm image list westus canonical ubuntuserver 14.04.2-LTS
    info:    Executing command vm image list
    + Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "14.04.2-LTS" Location:"westus")
    data:    Publisher  Offer         Sku          Version          Location  Urn
    data:    ---------  ------------  -----------  ---------------  --------  --------------------------------------------------
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.201503090  westus    canonical:ubuntuserver:14.04.2-LTS:14.04.201503090
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.20150422   westus    canonical:ubuntuserver:14.04.2-LTS:14.04.20150422
    data:    canonical  ubuntuserver  14.04.2-LTS  14.04.201504270  westus    canonical:ubuntuserver:14.04.2-LTS:14.04.201504270
    info:    vm image list command OK

これで、使用するイメージを正確に選択できます。検出した URN 情報を使用して仮想マシンを素早く作成する方法や、その URN 情報のあるテンプレートを使用する方法については、「[Azure リソース マネージャーでの Azure CLI for Mac, Linux, and Windows の使用](../articles/xplat-cli-azure-resource-manager.md)」をご覧ください。

## PowerShell

PowerShell を使用している場合、次のように入力します`Switch-AzureMode AzureResourceManager`。更新と構成の詳細については、「[Using Azure CLI with Resource Manager (リソース マネージャーでの Azure CLI の使用)](../articles/xplat-cli-azure-resource-manager.md)」と「[Using Azure PowerShell with Azure Resource Manager (Azure リソース マネージャーでの Azure PowerShell の使用)](../articles/powershell-azure-resource-manager.md)」をご覧ください。

> [AZURE.NOTE] 1.0 以降の Azure PowerShell モジュールでは、`Switch-AzureMode` コマンドレットが削除されました。このバージョン以降では、以下のコマンドの `Azure` の部分を `AzureRm` に置き換えてください。バージョン 1.0 より前の Azure PowerShell を使用する場合は以下のコマンドを使用できますが、初めに `Switch-AzureMode AzureResourceManager` を実行する必要があります。


Azure リソース マネージャーを使用して新しい仮想マシンを作成するとき、場合によっては、以下のイメージ プロパティの組み合わせによりイメージを指定する必要があります。

- 発行元
- プラン
- SKU

たとえば、これらの値は、**Set-AzureVMSourceImage** PowerShell コマンドレットや、作成する仮想マシンの種類を指定する必要のあるリソース グループ テンプレート ファイルで必要となります。

これらの値を判別する必要がある場合は、以下の方法で、イメージをナビゲートしてそれらの値を判別できます。

1. イメージの発行元を一覧表示する。
2. 指定された発行元について、そのプランを一覧表示する。
3. 指定されたプランについて、その SKU を一覧表示する。

これを PowerShell で行うには、まず Azure PowerShell のリソース マネージャー モードに切り替えます。

	Switch-AzureMode AzureResourceManager

上記の最初のステップで、以下のコマンドによって発行元を一覧表示します。

	$locName="<Azure location, such as West US>"
	Get-AzureVMImagePublisher -Location $locName | Select PublisherName

選択した発行元の名前を入力して、それらのコマンドを実行します。

	$pubName="<publisher>"
	Get-AzureVMImageOffer -Location $locName -Publisher $pubName | Select Offer

選択したプランの名前を入力して、それらのコマンドを実行します。

	$offerName="<offer>"
	Get-AzureVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

**Get AzureVMImageSku** コマンドの表示から、新しいバーチャル マシンのイメージを指定するために必要なすべての情報が得られます。

たとえば次のようになります。

	PS C:\> $locName="West US"
	PS C:\> Get-AzureVMImagePublisher -Location $locName | Select PublisherName

	PublisherName
	-------------
	a10networks
	aiscaler-cache-control-ddos-and-url-rewriting-
	alertlogic
	AlertLogic.Extension
	Barracuda.Azure.ConnectivityAgent
	barracudanetworks
	basho
	boxless
	bssw
	Canonical
	...

"MicrosoftWindowsServer" が発行元の場合:

	PS C:\> $pubName="MicrosoftWindowsServer"
	PS C:\> Get-AzureVMImageOffer -Location $locName -Publisher $pubName | Select Offer

	Offer
	-----
	WindowsServer

"WindowsServer" プランの場合:

	PS C:\> $offerName="WindowsServer"
	PS C:\> Get-AzureVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

	Skus
	----
	2008-R2-SP1
	2012-Datacenter
	2012-R2-Datacenter
	Windows-Server-Technical-Preview

この一覧から選択した SKU の名前をコピーすれば、**Set-AzureVMSourceImage** PowerShell コマンドレットに必要なすべての情報や、イメージの発行元、プラン、SKU を指定する必要のあるリソース グループ テンプレート ファイルに必要なすべての情報が得られます。

### ビデオ チュートリアル

このビデオでは、PowerShell を使用した上記の手順を説明します。

[AZURE.VIDEO virtual-machines-linux-cli-ps-findimage-posh]


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!---HONumber=AcomDC_0330_2016-->