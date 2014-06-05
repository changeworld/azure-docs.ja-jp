<properties linkid="script-xplat-intro" urlDisplayName="Microsoft Azure クロスプラットフォーム コマンドライン インターフェイス" pageTitle="リソース マネージャーでの Microsoft Azure クロスプラットフォーム コマンドライン インターフェイスの使用" title="リソース マネージャーでの Microsoft Azure クロスプラットフォーム コマンドライン インターフェイスの使用" metaKeywords="Windows Azure クロスプラットフォーム コマンドライン インターフェイス リソース マネージャー, Windows Azure コマンドライン リソース マネージャー, Azure コマンドライン リソース マネージャー, Azure CLI リソース マネージャー" description="リソース マネージャーで Microsoft Azure クロスプラットフォーム コマンドライン インターフェイスを使用します" metaCanonical="http://www.windowsazure.com/ja-jp/script/xplat-cli-intro" umbracoNaviHide="0" disqusComments="1" editor="mollybos" manager="paulettm" documentationCenter="" solutions="" authors="larryfr" services="" />

#リソース マネージャーでの Azure クロスプラットフォーム コマンドライン インターフェイスの使用

Microsoft では先ごろ、Microsoft Azure の新しい管理方法として、リソース マネージャーのプレビュー版を公開しました。この記事では、リソース マネージャーで Azure クロスプラットフォーム コマンドライン インターフェイス (xplat-cli) を使用する方法について説明します。

>[WACOM.NOTE]リソース マネージャーは、現在プレビュー版であり、Azure サービス管理と同じレベルの管理機能は提供されていません。

>[WACOM.NOTE]xplat-cli をまだインストールおよび構成していない場合、xplat-cli のインストール、構成、および使用方法については、[Install and Configure the Microsoft Azure Cross-Platform Command-Line Interface (Microsoft Azure クロスプラットフォーム コマンドライン インターフェイスのインストールおよび構成)][xplatsetup]を参照してください。

##リソース マネージャー

リソース マネージャーでは、複数の_リソース_ (データベース サーバー、データベース、Web サイトなどのユーザー管理のエンティティ) を_リソース グループ_と呼ばれる 1 つの論理単位にまとめて管理できます。たとえば、1 つのリソース グループに、Web サイトと SQL データベースというリソースを含めることができます。

リソース マネージャーでは、リソース グループ内のリソースの変更をより宣言的な方法で記述できるように、JSON ドキュメント形式の*テンプレート*が使用されます。テンプレート言語を使用してパラメーターを記述し、値をコマンドの実行時にインラインで入力するか、別の JSON ファイルに保存することもできます。これにより、同じテンプレートに異なるパラメーターを設定して、新しいリソースを簡単に作成できます。たとえば、Web サイトを作成するテンプレートには、サイト名や Web サイトを配置するリージョンなどの共通的なパラメーターを用意できます。

>[WACOM.NOTE]現時点では、この記事でテンプレート言語の詳細については説明しません。テンプレート言語については、リファレンス ドキュメントが整ったときに、それらのドキュメントへのリンクを追加してこの記事を更新します。
>
> ただし、`azure group template download` コマンドを使用して、テンプレート ギャラリーから Microsoft やパートナーが提供するテンプレートをダウンロードし、それを変更することができます。

テンプレートを使用してグループを変更または作成すると、_展開_ が作成されてグループに適用されます。

##認証

現在、xplat-cli によってリソース マネージャーを使用するには、組織アカウントを使用して Microsoft Azure で認証する必要があります。Microsoft アカウントや .publishsettings ファイルによってインストールされた証明書では認証できません。

組織アカウントを使用した認証の詳細については、[Install and Configure the Microsoft Azure Cross-Platform Command-Line Interface (Azure クロスプラットフォーム コマンドライン インターフェイスのインストールと構成)][xplatsetup]を参照してください。

##グループとテンプレートの操作

1. リソース マネージャーは現在プレビュー版であり、リソース マネージャーを使用するための xplat-cli コマンドは、既定で無効です。コマンドを有効にするには、次のコマンドを使用します。

		azure config mode arm

	>[WACOM.NOTE]リソース マネージャー モードと Azure サービス管理モードは互いに排他的です。つまり、どちらか一方のモードで作成されたリソースは、他方のモードは管理できません。

2. テンプレートを使用する場合、独自のテンプレートを作成するか、テンプレート ギャラリーのテンプレートを使用します。ギャラリーから使用できるテンプレートを一覧表示するには、次のコマンドを使用します。

		azure group template list

	これにより、テンプレートの発行元と名前が次のように表示されます。

		data:    Publisher               Name
		data:    ----------------------------------------------------------------------------
		data:    Microsoft               Microsoft.WebSite.0.1.0-preview1
		data:    Microsoft               Microsoft.PHPStarterKit.0.1.0-preview1
		data:    Microsoft               Microsoft.HTML5EmptySite.0.1.0-preview1
		data:    Microsoft               Microsoft.ASPNETEmptySite.0.1.0-preview1
		data:    Microsoft               Microsoft.WebSiteMySQLDatabase.0.1.0-preview1

3. Azure の Web サイトを作成するためのテンプレートの詳細を表示するには、次のコマンドを使用します。

		azure group template show Microsoft.WebSiteSQLDatabase.0.1.0-preview1

	これにより、テンプレートに関する説明が表示されます。

4. テンプレートをダウンロードするには、テンプレートを選択して、次のコマンドを使用します。

		azure group template download Microsoft.WebSiteSQLDatabase.0.1.0-preview1

	ダウンロードしたテンプレートは、個別の要件に合わせてカスタマイズできます。たとえば、テンプレートに別のリソースを追加できます。

	>[WACOM.NOTE]テンプレートを変更した場合は、リソース グループの作成または既存のリソース グループの変更に使用する前に、`azure group template validate` コマンドを使用してテンプレートを検証してください。

5. テキスト エディターでテンプレート ファイルを開きます。最上部近くの **parameters** コレクションを確認してください。ここには、テンプレートで記述されているリソースを作成するために、入力が必要なパラメーターの一覧が表示されています。これらのパラメーターには、**sku** のように既定値が設定されているパラメーターもあれば、**siteName** のように値の型を単純に指定するパラメーターもあります。テンプレートを使用する際は、コマンドライン パラメーターの一部としてパラメーターを指定するか、パラメーター値が設定されたファイルを指定します。いずれの場合も、パラメーターは JSON 形式で記述する必要があります。

	Microsoft.WebSiteSQLDatabase.0.1.0-preview1 テンプレートのパラメーターが格納されたファイルを作成するには、次のデータを使用して、**params.json** という名前のファイルを作成します。**MyWebSite** などの **My** で始まる値を独自の値と置き換えます。**siteLocation** には、最寄りの Azure リージョン (**North Europe** や **South Central US** など) を指定します。

		{
		  "siteName": {
		    "value": "MyWebSite"
		  },
		  "hostingPlanName": {
		    "value": "MyHostingPlan"
		  },
		  "siteLocation": {
		    "value": "North Europe"
		  },
		  "serverName": {
		    "value": "MySQLServer"
		  },
		  "serverLocation": {
		    "value": "North Europe"
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

1. **params.json** ファイルを保存した後、次のコマンドを使用して、テンプレートに基づく新しいリソース グループを作成します。`-e` パラメーターには、前の手順で作成した **params.json** ファイルを指定します。

		azure group create MyGroupName "MyDataCenter" -y Microsoft.WebSiteSQLDatabase.0.1.0-preview1 -d MyDeployment -e params.json

	**MyGroupName** を使用するグループ名と置き換え、**MyDataCenter** をテンプレートで指定した **siteLocation** の値と置き換えます。

	>[WACOM.NOTE]このコマンドでは、展開がアップロードされると "OK" と表示されますが、その時点ではまだグループのリソースに展開が適用されていません。展開の状態を確認するには、次のコマンドを使用します。
	> 
	> `azure group deployment show MyGroupName MyDeployment`
	> 
	> **ProvisioningState** に、展開の状態が表示されます。
	> 
	> 構成が適切でないことがわかった場合、または実行時間の長い展開を停止する必要がある場合は、次のコマンドを使用します。
	> 
	> `azure group deployment stop MyGroupName MyDeployment`
	> 
	> 展開名を指定しない場合は、テンプレート ファイル名に基づいて展開名が自動的に作成されます。作成された展開名は、`azure group create` コマンドの出力の一部として返されます。

3. グループを表示するには、次のコマンドを使用します。

		azure group show MyGroupName

	このコマンドにより、グループ内のリソースに関する情報が返されます。複数のグループがある場合は、`azure group list` コマンドでグループ名の一覧を表示した後、`azure group show` コマンドを使用して特定のグループの詳細を表示します。

##リソースの操作

テンプレート言語を使用すると、グループ全体の構成の変更を宣言できますが、場合によっては、特定のリソースのみの操作が必要になることがあります。このような場合には、`azure resource` コマンドを使用します。

> [WACOM.NOTE]`list` コマンド以外の `azure resource` コマンドを使用する場合、操作するリソースの API バージョンを `-o` パラメーターによって指定する必要があります。使用する API バージョンが不明な場合は、テンプレート ファイルでリソースの **apiVersion** フィールドを確認してください。

1. グループ内のすべてのリソースの一覧を表示するには、次のコマンドを使用します。

		azure resource list MyGroupName

1. グループ内の個別のリソース (Web サイトなど) を表示するには、次のコマンドを使用します。

		azure resource show MyGroupName MyWebSiteName Microsoft.Web/sites -o "2014-04-01"

	**Microsoft.Web/sites** パラメーターに注意してください。このパラメーターは、情報を要求するリソースの種類を示します。前の手順でダウンロードしたテンプレート ファイルを確認すると、テンプレートに記述されている Web サイトのリソースで、種類の定義にこの同じ値が使用されていることがわかります。

	このコマンドを実行すると、Web サイトに関する情報が表示されます。たとえば、**hostNames** フィールドには、Web サイトの URL が示されます。この情報をブラウザーで使用すると、Web サイトが実行されていることを確認できます。

2. リソースに関する情報では、一部の値がネスト構造、つまりコレクションであるため、詳細を表示する際に`- json` パラメーターを使用すると出力が見やすくなります。次のコマンドを実行すると、show コマンドの結果を JSON ドキュメントとして表示した場合の例が示されます。

		azure resource show MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01" --json

	>[WACOM.NOTE]&gt; 文字を使用して出力をファイルにパイプすると、JSON データをファイルに保存できます。次に例を示します。
	> 
	> `azure resource show MyGroupName MyWebSite Micrsoft.Web/sites --json > myfile.json`

3. 既存のリソースを削除するには、次のコマンドを使用します。

		azure resource delete MyGroupName MyWebSite Microsoft.Web/sites -o "2014-04-01"

##ログの記録

グループに実行された操作に関してログに記録された情報を表示するには、`azure group log show` コマンドを使用します。既定では、グループに対して実行された直前の操作が表示されます。すべての操作を表示するには、任意指定の `--all` パラメーターを使用します。最後の展開について表示するには、`--last-deployment` を使用します。特定の展開について表示するには、`--deployment` と展開名を指定します。次の例では、'MyGroup' グループに対して実行されたすべての操作のログが表示されます。

	azure group log show mygroup --all

##次のステップ

* Azure クロスプラットフォーム コマンドライン インターフェイスの使用方法については、[Install and Configure the Microsoft Azure Cross-Platform Command-Line Interface (Azure クロスプラットフォーム コマンドライン インターフェイスのインストールと構成)][xplatsetup]を参照してください。
*リソース マネージャーで Windows Azure PowerShell を使用する方法の詳細については、[Getting Started using Windows PowerShell with Resource Manager (リソース マネージャーでの Windows PowerShell の使用方法の概要)][psrm]を参照してください。

[signuporg]: http://www.windowsazure.com/ja-jp/documentation/articles/sign-up-organization/
[adtenant]: http://technet.microsoft.com/ja-jp/library/jj573650#createAzureTenant
[portal]: https://manage.windowsazure.com/
[xplatsetup]: /ja-jp/documentation/articles/xplat-cli/
[psrm]: http://go.microsoft.com/fwlink/?LinkId=394760

