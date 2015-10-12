<properties
	pageTitle="Mac、Linux、Windows 用の CLI | Microsoft Azure"
	description="Azure リソース マネージャーでの、Mac、Linux、Windows 用 Microsoft Azure CLI の使用"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services="virtual-machines"/>

<tags ms.service="virtual-machines" ms.workload="infrastructure-services"" ms.tgt_pltfrm="command-line-interface" ms.devlang="na" ms.topic="article" ms.date="06/09/2015" ms.author="danlep"/>

# Azure リソース マネージャーでの、Mac、Linux、および Windows 用 Azure CLI の使用

> [AZURE.SELECTOR]
- [Azure PowerShell](../powershell-azure-resource-manager.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]この記事では、リソース マネージャー デプロイ モデルを使用したリソースの管理について説明します。

この記事では、Azure リソース マネージャー モードを使用し、Mac、Linux、Windows 用 Azure CLI を使用して、Azure VM を作成、管理、削除する方法について説明します。

>[AZURE.NOTE]コマンドラインで Azure リソース作成し、管理するには、Azure アカウントが必要 ([無料試用版はこちら](http://azure.microsoft.com/pricing/free-trial/)) です。[Azure CLI をインストールして](../xplat-cli-install.md)、[ログオンし、アカウントに関連付けられている Azure のリソースを使用する必要があります](../xplat-cli-connect.md)。これらの操作を完了した場合は、使用する準備が整いました。

## Azure リソース

Azure リソース マネージャーを使用すると、複数の_リソース_ (仮想マシン、データベース サーバー、データベース、Web サイトなどのユーザー管理のエンティティ) を_リソース グループ_と呼ばれる 1 つの論理単位にまとめて管理できます。asm モードと同じように、これらのリソースをコマンドラインで強制的に作成、管理、削除できます。

Azure リソース マネージャー モードを使用して、JSON *テンプレート*にリソースのデプロイ可能なグループの構造とリレーションシップを記述することで、_宣言型_の方法で Azure のリソースを管理することもできます。テンプレートを使用してパラメーターを記述し、値をコマンドの実行時にインラインで入力するか、別の JSON azuredeploy-parameters.json ファイルに保存することもできます。これにより、同じテンプレートに異なるパラメーターを設定して、新しいリソースを簡単に作成できます。たとえば、Web サイトを作成するテンプレートには、サイト名や Web サイトを配置するリージョンなどの共通的なパラメーターを用意できます。

テンプレートを使用してグループを変更または作成すると、_デプロイ_が作成されてグループに適用されます。Azure リソース マネージャーの詳細については、「[Azure リソース マネージャーの概要](../resource-group-overview.md)」をご覧ください。

## 認証

Azure CLI を使用して Azure リソース マネージャーを操作するには、仕事か学校のアカウントを使用して Microsoft Azure で認証する必要があります。.publishsettings ファイルによってインストールされた証明書では認証できません。

職場か学校のアカウントを使用した認証の詳細については、「[Azure CLI から Azure サブスクリプションへの接続する](../xplat-cli-connect.md)」をご覧ください。

> [AZURE.NOTE]Azure Active Directory によって管理される職場か学校のアカウントを使用しているため、Azure のロールベースのアクセス制御 (RBAC) を使用して、Azure リソースへのアクセスと使用状況を管理することもできます。詳細については、「[リソースへのアクセスの管理と監査](../resource-group-rbac.md)」をご覧ください。

## Azure リソース マネージャー モードの設定

Azure リソース マネージャー モードは既定で無効になっているため、次のコマンドを使って Azure CLI リソース マネージャー コマンドを有効にする必要があります。

	azure config mode arm

>[AZURE.NOTE]Azure リソース マネージャー モードと Azure サービス管理モードは互いに排他的です。つまり、どちらか一方のモードで作成されたリソースは、他方のモードは管理できません。

## 場所を見つける

ほとんどの Azure リソース マネージャー コマンドには、リソースを作成し、検索するための有効な場所が必要です。次のコマンドを使用して、利用可能なすべての場所を検索することができます。

	azure location list

これにより、「米国西部」や「米国東部」などのリージョンに固有の場所が表示されます。

## リソース グループの作成

リソース グループは、ネットワーク、ストレージ、その他のリソースの論理グループです。Azure リソース マネージャー モードのほとんどすべてのコマンドにはリソース グループが必要です。次のコマンドを使用して、たとえば _testrg_ という名前のリソース グループを作成することができます。

	azure group create -n "testrg" -l "West US"

その後、このグループへのリソースの追加を開始し、これを使用して新しい仮想マシンを構成することができます。

## 仮想マシンの作成

Azure リソース マネージャー モードで仮想マシンを作成する方法は 2 つあります。

1. 個々の Azure CLI コマンドを使用する。
2. リソース グループ テンプレートを使用する。

これらのメソッドのいずれかを開始する前に、少なくとも 1 つのリソース グループを作成することを確認します。

### 個々の Azure CLI コマンドの使用

これは、ニーズに従って仮想マシンを構成し、作成する基本的な方法です。Azure リソース マネージャー モードでは、**vm create** コマンドを使用する前に、ネットワークのようないくつか必須のリソースを構成する必要があります。

>[AZURE.NOTE]サブスクリプションのコマンド ラインでリソースを初めて作成する場合は、特定のリソース プロバイダーを登録するように求められることがあります。このような場合は、プロバイダーを登録し、失敗したコマンドをもう一度やり直すのが簡単です。次に例を示します。
>
> `azure provider register Microsoft.Storage`
>
> 次のコマンドを実行して、サブスクリプションに登録されているプロバイダーの一覧を確認できます。
>
> `azure provider list`


#### パブリック IP リソースの作成

新しい仮想マシンに SSH 接続して、意味のある作業をするためには、パブリック IP を作成する必要があります。パブリック IP を作成するは簡単です。コマンドには、リソース グループ、パブリック IP リソースの名前、場所がその順序で必要です。

	azure network public-ip create "testrg" "testip" "westus"

#### ネットワーク インターフェイス カードのリソースの作成

ネットワーク インターフェイス カード、つまり NIC は、サブネットと仮想ネットワークを最初に作成する必要があります。**network vnet create** コマンドを使用して、特定の場所に仮想ネットワークの作成し、リソース グループを作成します。

	azure network vnet create "testrg" "testvnet" "westus"

**network vnet subnet create** コマンドを使用して、この仮想ネットワーク内にサブネットを作成することができます。

	azure network vnet subnet create "testrg" "testvnet" "testsubnet"

**network nic create** コマンドとこれらのリソースを使用して、NIC を作成することができます。

	azure network nic create "testrg" "testnic" "westus" -k "testsubnet" -m "testvnet" -p "testip"

>[AZURE.NOTE]オプションですが、NIC を 使用する IP にバインドして、後でこの NIC を使用して、作成された仮想マシンに SSH 接続するために、パブリック IP 名をパラメーターとして **network nic create** に渡すことは非常に重要です。

**network** コマンドの詳細については、コマンドラインのヘルプか、「[Azure リソース管理での Azure CLI の使用](azure-cli-arm-commands.md)」をご覧ください。

#### オペレーティング システム イメージの検索

現時点では、イメージの発行元に基づくオペレーティング システムのみを検索できます。つまり、目的の場所の OS イメージの発行元の一覧を検索するには、次のコマンドを実行する必要があります。

	azure vm image list-publishers "westus"

次のコマンドを実行して、リストから発行元を選択し、選択した発行元のイメージの一覧を検索します。

	azure vm image list "westus" "CoreOS"

最後に、次の例のように表示される一覧から OS イメージを選択します。

	info:    Executing command **vm image list**
	warn:    The parameters --offer and --sku if specified will be ignored
	+ Getting virtual machine image offers (Publisher: "Canonical" Location: "westus")
	data:    Publisher  Offer        Sku          Version          Location  Urn

	data:    ---------  -----------  -----------  ---------------  --------- ----------------------------------------
	data:    CoreOS     CoreOS       Alpha        475.1.0          westus    CoreOS:CoreOS:Alpha:475.1.0
	data:    CoreOS     CoreOS       Alpha        490.0.0          westus    CoreOS:CoreOS:Alpha:490.0.0

仮想マシンにロードするイメージの URN 名を保存します。この名前は記事の後半で使用します。

#### 仮想マシンの作成

**vm create** コマンドを実行し、必要な情報を渡して仮想マシンを作成する準備ができました。オプションですが、NIC は、この情報を既に持っているため、この段階でパブリック IP アドレスを渡すこともできます。コマンドは、次の例のように表示されます。_testvm_ は、_testrg_ リソース グループで作成された仮想マシンの名前となります。

	azure-cli@0.8.0:/# azure vm create "testrg" "testvm" "westus" "Linux" -Q "CoreOS:CoreOS:Alpha:660.0.0" -u "azureuser" -p "Pass1234!" -N "testnic"
	info:    Executing command vm create
	+ Looking up the VM "testvm"
	info:    Using the VM Size "Standard_A1"
	info:    The [OS, Data] Disk or image configuration requires storage account
	+ Retrieving storage accounts
	info:    Could not find any storage accounts in the region "westus", trying to create new one
	+ Creating storage account "cli02f696bbfda6d83414300" in "westus"
	+ Looking up the storage account cli02f696bbfda6d83414300
	+ Looking up the NIC "testnic"
	+ Creating VM "testvm"
	info:    vm create command OK

次のコマンドを実行して、この仮想マシンを開始できます。

	azure vm start "testrg" "testvm"

次に、**ssh username@ipaddress** コマンドを使用して SSH 接続します。パブリック IP リソースの IP アドレスをすばやく調べるには、次のコマンドを使用します。

	azure network public-ip show "testrg" "testip"

**vm** コマンドを使用すると、この仮想マシンの管理が簡単になります。詳細については、「[Azure リソース管理での Azure CLI の使用](azure-cli-arm-commands.md)」を参照してください。

### 仮想マシンの簡易作成ショートカット

新しい**仮想マシンの簡易作成** ショートカットにより、VM の命令型の手順が大幅に縮小されました。これは、単純な仮想マシンの作成を試したい場合や、ネットワークの構成について配慮しない場合に便利です。これは対話型コマンドであり、実行前に OS イメージ の URN のみを確認する必要があります。

	azure-cli@0.8.0:/# azure vm quick-create
	info:  Executing command vm quick-create
	Resource group name: CLIRG
	Virtual machine name: myqvm
	Location name: westus
	Operating system Type [Windows, Linux]: Linux
	ImageURN (format: "publisherName:offer:skus:version"): CoreOS:CoreOS:Alpha:660.0.0
	User name: azureuser
	Password: ********
	Confirm password: ********

Azure CLI は既定の VM サイズで仮想マシンを作成します。また、ストレージ アカウント、NIC、仮想ネットワークとサブネット、パブリック IP を作成します。起動した後は、パブリック IP を使用して、仮想マシンに SSH 接続できます。

### リソース グループ テンプレートの使用

#### リソース グループ テンプレートの検索と構成

1. テンプレートを使用する場合、独自のテンプレートを作成するか、テンプレート ギャラリーのテンプレートを使用するか、[GitHub](https://github.com/azurermtemplates/azurermtemplates) で取得可能なテンプレートを使用します。最初に、テンプレート ギャラリーの CoreOS.CoreOSStable.0.2.40-preview という名前のテンプレートを使用してみましょう。ギャラリーから使用できるテンプレートを一覧表示するには、次のコマンドを使用します。利用可能なテンプレートは何千とあるため、ページを分割するか **grep** や **findstr** (Windows の場合) や任意の文字列検索コマンドを使用して興味のあるテンプレートを検索してください。または、**--json** オプションを使用して JSON 形式のすべてのリストをダウンロードすると簡単に検索できます。

		azure group template list

	これにより、テンプレートの発行元と名前が次の例のように表示されます (さらに数多くの項目が表示されます)。

		data:    Publisher               Name
		data:    ----------------------------------------------------------------------------
		data:    CoreOS                  CoreOS.CoreOSStable.0.2.40-preview
		data:    CoreOS                  CoreOS.CoreOSAlpha.0.2.39-preview
		data:    SUSE                    SUSE.SUSELinuxEnterpriseServer12.2.0.36-preview
		data:    SUSE                    SUSE.SUSELinuxEnterpriseServer11SP3PremiumImage0.2.54-preview

2. テンプレートの詳細を表示するには、次のコマンドに使用します。

		azure group template show CoreOS.CoreOSStable.0.2.40-preview

	これにより、テンプレートに関する説明が表示されます。使用するテンプレートは、Linux 仮想マシンを作成します。

3. テンプレートをダウンロードするには、テンプレートを選択して、次のコマンドを使用します。

		azure group template download CoreOS.CoreOSStable.0.2.40-preview

	ダウンロードしたテンプレートは、個別の要件に合わせてカスタマイズできます。たとえば、テンプレートに別のリソースを追加できます。

	>[AZURE.NOTE]テンプレートを変更する場合は、リソース グループの作成や既存のリソース グループの変更に使用する前に、`azure group template validate` コマンドを使用してテンプレートを検証してください。

4. ご使用状況にあわせてリソース グループ テンプレートを構成するには、テキスト エディターでテンプレート ファイルを開きます。最上部近くの **parameters** JSON コレクションをご確認ください。ここには、テンプレートで記述されているリソースを作成するために、入力が必要なパラメーターの一覧が表示されています。パラメーターは既定値を持っているものもあれば、値の型か許可されている値の範囲を指定するものもあります。

	テンプレートを使用する際は、コマンドライン パラメーターの一部としてパラメーターを指定するか、パラメーター値が設定されたファイルを指定します。テンプレートが特定のデプロイに緊密にバインドされ、簡単には再利用できなくなりますが、テンプレートの **parameters** セクション内に **value** フィールドを直接、記述することもできます。いずれの場合も、パラメーターは JSON 形式で記述する必要があります。また既定値を持たないそれらのキーで、ご利用の値を使う必要があります。

	たとえば、CoreOS.CoreOSStable.0.2.40-preview テンプレートのパラメーターが格納されたファイルを作成するには、次のデータを使用して、params.json という名前のファイルを作成します。独自の値でこのサンプルで使用する値を置き換えます。**Location** には、最寄りの Azure リージョン (**North Europe** や **South Central US** など) を指定します。(この例では、**West US** を使用しています。)

		{
		  "newStorageAccountName": {
			"value": "testStorage"
		  },
		  "newDomainName": {
			"value": "testDomain"
		  },
		  "newVirtualNetworkName": {
			"value": "testVNet"
		  },
		  "vnetAddressSpace": {
			"value": "10.0.0.0/11"
		  },
		  "hostName": {
			"value": "testHost"
		  },
		  "userName": {
			"value": "azureUser"
		  },
		  "password": {
			"value": "Pass1234!"
		  },
		  "location": {
			"value": "West US"
		  },
		  "hardwareSize": {
			"value": "Medium"
		  }
	    }

5. params.json ファイルを保存した後、次のコマンドを使用して、テンプレートに基づく新しいリソース グループを作成します。`-e` パラメーターには、前の手順で作成した params.json ファイルを指定します。**testRG** を使用するグループ名と置き換え、**testDeploy** をデプロイ名と置き換えます。場所は、params.json テンプレート パラメーター ファイルで指定された場所と同じ必要があります。

		azure group create "testRG" "West US" -f CoreOS.CoreOSStable.0.2.40-preview.json -d "testDeploy" -e params.json

	このコマンドでは、デプロイがアップロードされると "OK" と表示されますが、その時点ではまだグループのリソースにデプロイが適用されていません。デプロイの状態を確認するには、次のコマンドを使用します。

		azure group deployment show "testRG" "testDeploy"

	**ProvisioningState** に、デプロイの状態が表示されます。

	デプロイが成功した場合は、次の例のような出力が表示されます。

		azure-cli@0.8.0:/# azure group deployment show testRG testDeploy
		info:    Executing command group deployment show
		+ Getting deployments
		data:    DeploymentName     : testDeploy
		data:    ResourceGroupName  : testRG
		data:    ProvisioningState  : Running
		data:    Timestamp          : 2015-04-27T07:49:18.5237635Z
		data:    Mode               : Incremental
		data:    Name                   Type          Value
		data:    ---------------------  ------------  ----------------
		data:    newStorageAccountName  String        testStorage
		data:    newDomainName          String        testDomain
		data:    newVirtualNetworkName  String        testVNet
		data:    vnetAddressSpace       String        10.0.0.0/11
		data:    hostName               String        testHost
		data:    userName               String        azureUser
		data:    password               SecureString  undefined
		data:    location               String        West US
		data:    hardwareSize           String        Medium
		info:    group deployment show command OK

	>[AZURE.NOTE]構成が適切でないことがわかった場合、または実行時間の長いデプロイを停止する必要がある場合は、次のコマンドを使用します。
	>
	> `azure group deployment stop "testRG" "testDeploy"`
	>
	> デプロイ名を指定しない場合は、テンプレート ファイル名に基づいてデプロイ名が自動的に作成されます。作成されたデプロイ名は、`azure group create` コマンドの出力の一部として返されます。

6. グループを表示するには、次のコマンドを使用します。

		azure group show "testRG"

	このコマンドにより、グループ内のリソースに関する情報が返されます。複数のグループがある場合は、`azure group list` コマンドでグループ名の一覧を表示した後、`azure group show` コマンドを使用して特定のグループの詳細を表示します。

7. テンプレート ライブラリからダウンロードする代わりに、GitHub から直接、最新のテンプレートを使用することもできます。[GitHub.com](http://www.github.com) を開いて、AzureRmTemplates を検索します。AzureRmTemplates リポジトリを選択し、たとえば、_101-simple-vm-from-image_ のような任意のテンプレートを検索します。テンプレートをクリックすると、他のファイルに azuredeploy.json が含まれているテンプレートが表示されます。これは、**--template-url** オプションを使用して、コマンドで使用するテンプレートです。 _raw_ モードを開き、ブラウザーのアドレス バーに表示される URL をコピーします。次の例のようにコマンドを使用して、テンプレート ライブラリからダウンロードする代わりに、この URL を使用して、デプロイを直接作成することができます。

		azure group deployment create "testDeploy" -g "testResourceGroup" --template-uri https://raw/githubusercontent.com/azurermtemplates/azurermtemplates/master/101-simple-vm-from-image/azuredeploy.json

	> [AZURE.NOTE]_raw_ モードで、json テンプレートを開くことが重要です。ブラウザーのアドレス バーに表示される URL は、通常モードで表示される URL とは異なります。GitHub でファイルを表示するときに_未加工_モードでファイルを開くには、右上にある **[未加工]** をクリックします。

#### リソースの操作

テンプレート言語を使用すると、グループ全体の構成の変更を宣言できますが、場合によっては、特定のリソースのみの操作が必要になることがあります。このような場合には、`azure resource` コマンドを使用します。

> [AZURE.NOTE]`list` コマンド以外の `azure resource` コマンドを使用する場合、操作するリソースの API バージョンを `-o` パラメーターによって指定する必要があります。使用する API バージョンが不明な場合は、テンプレート ファイルでリソースの **apiVersion** フィールドを確認してください。

1. グループ内のすべてのリソースの一覧を表示するには、次のコマンドを使用します。

		azure resource list "testRG"

2. グループ内の個別のリソースを表示するには、次のコマンドを使用します。

		azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01"

	**Microsoft.ClassicCompute/virtualMachines** パラメーターに注意してください。このパラメーターは、情報を要求するリソースの種類を示します。前の手順でダウンロードしたテンプレート ファイルを確認すると、テンプレートに記述されている仮想マシンのリソースで、種類の定義にこの同じ値が使用されていることがわかります。

	このコマンドを実行すると、仮想マシンに関する情報が表示されます。

3. リソースに関する情報では、一部の値がネスト構造、つまりコレクションであるため、詳細を表示する際に `--json` パラメーターを使用すると出力が見やすくなります。次の例のコマンドを実行すると、**show** コマンドの結果を JSON ドキュメントとして表示した場合の例が示されます。

		azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01" --json

	>[AZURE.NOTE]&gt; 文字を使用して出力をファイルにパイプすると、JSON データをファイルに保存できます。次に例を示します。
	>
	> `azure resource show "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01" --json > myfile.json`

4. 既存のリソースを削除するには、次のコマンドを使用します。

		azure resource delete "testRG" "testHost" Microsoft.ClassicCompute/virtualMachines -o "2014-06-01"

## ログの記録

グループに実行された操作に関してログに記録された情報を表示するには、`azure group log show` コマンドを使用します。既定では、グループに対して実行された直前の操作が表示されます。すべての操作を表示するには、任意指定の `--all` パラメーターを使用します。最後のデプロイについて表示するには、`--last-deployment` を使用します。特定のデプロイについて表示するには、`--deployment` とデプロイ名を指定します。次の例では、MyGroup グループに対して実行されたすべての操作のログが表示されます。

	azure group log show mygroup --all

## 次のステップ

* Azure コマンド ライン インターフェイス (Azure CLI) の詳細については、「[Azure CLI のインストール][clisetup]」を参照してください。
* Azure リソース マネージャーで Azure PowerShell を使用する方法の詳細については、「[Azure リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)」を参照してください。
* Azure ポータルから Azure リソース マネージャーを使用する方法の詳細については、「[リソース グループを使用した Azure リソースの管理][psrm]」を参照してください。

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[portal]: https://manage.windowsazure.com/
[clisetup]: ../xplat-cli-install.md
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

<!---HONumber=Oct15_HO1-->