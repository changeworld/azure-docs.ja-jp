<properties 
	pageTitle="リソース マネージャーでの Microsoft Azure クロスプラットフォーム コマンドライン インターフェイスの使用" 
	description="リソース マネージャーでの Microsoft Azure クロスプラットフォーム コマンドライン インターフェイスの使用" 
	editor="tysonn" 
	manager="timlt" 
	documentationCenter="" 
	authors="squillace" 
	services=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="command-line-interface" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/05/2015" 
	ms.author="rasquill"/>

#リソース マネージャーでの Azure クロスプラットフォーム コマンドライン インターフェイスの使用

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/powershell-azure-resource-manager.md" title="Windows PowerShell">Windows PowerShell</a><a href="/documentation/articles/xplat-cli-azure-resource-manager.md" title="Cross-Platform CLI" class="current">クロス プラットフォーム CLI</a></div>

この記事では、リソース マネージャーで Azure クロスプラットフォーム コマンドライン インターフェイス (xplat-cli) を使用する方法について説明します。 

>[AZURE.NOTE] リソース マネージャーは、プレビューでは、現在はし、Azure サービス管理と同じレベルの管理機能を提供していません。 
>
> まだインストールされている、xplat-cli を構成している場合を参照してください。[インストールして、Microsoft Azure クロスプラット フォーム コマンド ライン インターフェイスを構成する][xplatsetup]インストールする方法の詳細手順については、構成、および、xplat-cli を使用して。


##リソース マネージャー

リソース マネージャーでは、複数の_リソース_ (データベース サーバー、データベース、Web サイトなどのユーザー管理のエンティティ) を_リソース グループ_と呼ばれる 1 つの論理単位にまとめて管理できます。次の例では、リソース グループは、web サイトと SQL データベース リソースとして含める可能性があります。

リソース マネージャーは使用して、リソース グループ内のリソースへの変更を説明するより宣言的な方法をサポートする *templates*、JSON ドキュメントがあります。テンプレート言語を使用してパラメーターを記述し、値をコマンドの実行時にインラインで入力するか、別の JSON ファイルに保存することもできます。これにより、同じテンプレートに異なるパラメーターを設定して、新しいリソースを簡単に作成できます。たとえば、Website を作成するテンプレートには、サイト名や Website を配置するリージョンなどの共通的なパラメーターを用意できます。

>[AZURE.NOTE] テンプレート言語の詳細については、この時点では説明しません。テンプレート言語については、リファレンス ドキュメントが整ったときに、それらのドキュメントへのリンクを追加してこの記事を更新します。
>
> ただし、使用して、 `azure group template download`をダウンロードして、テンプレート ギャラリーから Microsoft やパートナーが提供するテンプレートを変更するコマンド。

テンプレートを使用してグループを変更または作成すると、_デプロイメント_が作成されてグループに適用されます。

##認証

現在、xplat-cli によってリソース マネージャーでの作業は、職場または学校のアカウントを使用して Microsoft azure 認証を受けることが必要です。Microsoft アカウントや .publishsettings ファイルによってインストールされた証明書では認証できません。

組織アカウントを使用した認証の詳細については、次を参照してください。[インストールして、Microsoft Azure クロスプラット フォーム コマンド ライン インターフェイスを構成する][xplatsetup]です。

##検索して、リソース グループ テンプレートを構成します。

1. 既定では、リソース マネージャー モードが有効でないため、次のコマンドを使用して xplat-cli でリソース マネージャーのコマンドを有効にする必要があります。

		azure config mode arm

	>[AZURE.NOTE] リソース マネージャー モードと Azure サービス管理モードでは、相互に排他的です。つまり、どちらか一方のモードで作成されたリソースは、他方のモードは管理できません。

2. テンプレートを使用する場合、独自のテンプレートを作成するか、テンプレート ギャラリーのテンプレートを使用します。この場合は、使用して、テンプレート ギャラリーから 1 つです。ギャラリーから使用できるテンプレートを一覧表示するには、次のコマンドを使用します。(何千ものテンプレートが使用可能なために、結果をページ分割または使用することを確認する**grep**または**findstr**[windows]または興味深いテンプレートを見つけるに、お気に入りの文字列検索コマンド。または、使用、 **- json**オプションを選択し、簡単に検索するための JSON 形式で全体の一覧をダウンロードします。次の例はという名前のテンプレートを使用して**Microsoft.WebSiteSQLDatabase.0.2.6 プレビュー**.)	

		azure group template list

	応答には、パブリッシャーとテンプレート名、一覧し、(が複数存在するまで) は、次のように表示されます。

		data:    Publisher               Name
		data:    ----------------------------------------------------------------------------
		data:    Microsoft               Microsoft.WebSite.0.1.0-preview1
		data:    Microsoft               Microsoft.PHPStarterKit.0.1.0-preview1
		data:    Microsoft               Microsoft.HTML5EmptySite.0.1.0-preview1
		data:    Microsoft               Microsoft.ASPNETEmptySite.0.1.0-preview1
		data:    Microsoft               Microsoft.WebSiteMySQLDatabase.0.1.0-preview1

3. Azure の web サイトを作成するテンプレートの詳細を表示するには、次のコマンドに使用します。

		azure group template show Microsoft.WebSiteSQLDatabase.0.2.6-プレビュー

	これにより、テンプレートに関する説明が表示されます。 

4. テンプレートを選択すると (**azure グループ テンプレート Microsoft.WebSiteSQLDatabase.0.2.6 プレビューを表示する**)、次のコマンドでダウンロードすることができます。

		azure group template download Microsoft.WebSiteSQLDatabase.0.2.6-preview

	ダウンロードしたテンプレートは、個別の要件に合わせてカスタマイズできます。たとえば、テンプレートに別のリソースを追加できます。

	>[AZURE.NOTE] 使用して、テンプレートを変更する場合、 `azure group template validate`コマンドを作成または既存のリソース グループの変更に使用する前に、テンプレートを検証します。

5. 使用するためのリソース グループ テンプレートを構成するのには、テンプレート ファイルをテキスト エディターで開きます。注、**パラメーター**上部にある JSON のコレクション。ここには、テンプレートで記述されているリソースを作成するために、入力が必要なパラメーターの一覧が表示されています。これらのパラメーターには、**sku** のように既定値が設定されているパラメーターもあれば、**siteName** のように値の型を単純に指定するパラメーターもあります。 
	
	テンプレートを使用する際は、コマンドライン パラメーターの一部としてパラメーターを指定するか、パラメーター値が設定されたファイルを指定します。どちらの場合も、パラメーターは、JSON 形式である必要があり、これらのキーは、既定値はありませんが、独自の値を指定する必要があります。

	たとえばのパラメーターを含むファイルを作成するため、 **Microsoft.WebSiteSQLDatabase.0.2.6 プレビュー**テンプレート、使用するには、次のデータ ファイルを作成するという名前**params.json**です。以降の下の値を置き換えます**_My_**など**_MyWebSite_** 、独自の値を使用します。**siteLocation** には、最寄りの Azure リージョン (**North Europe** や **South Central US** など) を指定します。(この例では**米国西部**)

		{
		  "siteName": {
		    "value": "MyWebSite"
		  },
		  "hostingPlanName": {
		    "value": "MyHostingPlan"
		  },
		  "siteLocation": {
		    "value": "West US"
		  },
		  "serverName": {
		    "value": "MySQLServer"
		  },
		  "serverLocation": {
		    "value": "West US"
		  },
		  "administratorLogin": {
		    "value": "MySQLAdmin"
		  },
		  "administratorLoginPassword": {
		    "value": "MySQLAdminPassword"
		  },
		  "databaseName": {
		    "value": "MySQLDB"
		  }
		}


1. 保存した後、 **params.json**ファイルで、テンプレートに基づく新しいリソース グループを作成するには、次のコマンドを使用します。'-E' パラメーターを指定、 **params.json** 、前の手順で作成したファイルです。置換、 **MyGroupName**を使用するグループの名前を持つと**MyDataCenter**で、 **siteLocation**で指定された値、 **params.json**テンプレート パラメーターのファイルです。

		azure group create MyGroupName "West US" -f Microsoft.WebSiteSQLDatabase.0.2.6-preview.json -d MyDeployment -e params.json

	>[AZURE.NOTE] このコマンドは、グループ内のリソースに適用された、展開する前に、展開のアップロードが完了したら、[ok] を返します。展開の状態を確認するには、次のコマンドを使用します。
	>
	> `azure group deployment show MyGroupName MyDeployment`
	> 
	> **ProvisioningState** 、デプロイの状態を示しています。
	> 
	> 構成が適切でないことがわかった場合、または実行時間の長いデプロイを停止する必要がある場合は、次のコマンドを使用します。
	> 
	> `azure group deployment stop MyGroupName MyDeployment`
	> 
	> デプロイ名を指定しない場合は、テンプレート ファイル名に基づいてデプロイ名が自動的に作成されます。出力の一部として返されます、 `azure group create`コマンド。

3. グループを表示するには、次のコマンドを使用します。

		azure group show MyGroupName

	このコマンドにより、グループ内のリソースに関する情報が返されます。複数のグループがある場合を使用して、 `azure group list`グループの名前の一覧を取得し、使用するコマンド `azure group show`、特定のグループの詳細を表示します。

##リソースの操作

テンプレート言語を使用すると、グループ全体の構成の変更を宣言できますが、場合によっては、特定のリソースのみの操作が必要になることがあります。これを使用して行える、 `azure resource`コマンド。

> [AZURE.NOTE] 使用する場合、 `azure resource`以外の場合にコマンド、 `list`コマンドを使用して使用しているリソースの API バージョンを指定する必要があります、'-パラメーターの o ' です。使用する API バージョンが不明な場合は、テンプレート ファイルでリソースの **apiVersion** フィールドを確認してください。

1. グループ内のすべてのリソースの一覧を表示するには、次のコマンドを使用します。

		azure resource list MyGroupName

1. グループ内の個別のリソース (Web サイトなど) を表示するには、次のコマンドを使用します。

		azure resource show MyGroupName MyWebSiteName Microsoft.Web/sites -o "2014-04-01"

	**Microsoft.Web/sites** パラメーターに注意してください。このパラメーターは、情報を要求するリソースの種類を示します。前の手順でダウンロードしたテンプレート ファイルを確認すると、テンプレートに記述されている Web サイトのリソースで、種類の定義にこの同じ値が使用されていることがわかります。

	このコマンドを実行すると、Web サイトに関する情報が表示されます。たとえば、**ホスト名**フィールドには、web サイトの URL が含まれている必要があります。この情報をブラウザーで使用すると、Web サイトが実行されていることを確認できます。

2. 使用する際に多くの場合、リソースの詳細の表示、ときに、'-json' パラメーターが、出力がいくつかの値は、入れ子になった構造体、またはコレクションは読みやすくします。次のコマンドを実行すると、show コマンドの結果を JSON ドキュメントとして表示した場合の例が示されます。

		azure resource show MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01" --json

	>[AZURE.NOTE] 使用してファイルに、JSON データを保存する、 &gt;文字を出力をファイルにパイプします。次に例を示します。
	>
	> `azure resource show MyGroupName MyWebSite Micrsoft.Web/sites --json > myfile.json`

3. 既存のリソースを削除するには、次のコマンドを使用します。

		azure resource delete MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01"

##ログの記録

グループに対して実行される操作をログに記録された情報を表示する、 `azure group log show`コマンド。既定では、グループに対して実行された直前の操作が表示されます。すべての操作を表示するには、任意指定の `--all` パラメーターを使用します。最後のデプロイメントについて表示するには、`--last-deployment` を使用します。特定のデプロイメントについて表示するには、`--deployment` とデプロイメント名を指定します。次の例は、グループに対して実行されたすべての操作のログを返します 'MyGroup'です。

	azure group log show mygroup --all

##次のステップ

* Azure クロス プラットフォーム コマンド ライン インターフェイスの使用に関する詳細については、次を参照してください。[インストールして、Microsoft Azure クロスプラット フォーム コマンド ライン インターフェイスを構成する][xplatsetup]です。
* Windows Azure PowerShell を使用してリソース マネージャーでの操作方法の詳細については、次を参照してください[リソース マネージャーで Windows PowerShell を使い始める][psrm。]

[signuporg]: http://www.windowsazure.com/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/library/jj573650#createAzureTenant
[portal]: https://manage.windowsazure.com/
[xplatsetup]: /documentation/articles/xplat-cli/
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

<!--HONumber=47-->
