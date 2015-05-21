*apiapp.json* ファイル内のほとんどのプロパティと、*Metadata* フォルダー内のファイルは、Azure Marketplace で API アプリ パッケージがどのように表示されるかに影響します。以降のセクションでは、Marketplace から API アプリをインストールするのではなく、直接コードをデプロイしたときに、どのプロパティとファイルが API アプリに影響するかを説明します。

### API アプリ ID 

`id` プロパティを使用して、API アプリの名前を決定します。次に例を示します。

		"id": "ContactsList",

![](./media/app-service-api-direct-deploy-metadata/apiappname.png)

### 名前空間

`namespace` プロパティには、Azure Active Directory テナントのドメインを設定します。ドメインを確認するには、ブラウザーで [Azure クラシック ポータル](https://manage.windowsazure.com/)を開き、**\[Active Directory\]** を参照して、**\[ドメイン\]** タブを選択します。次に例を示します。

		"namespace": "contoso.onmicrosoft.com",

### 動的 Swagger API の定義

動的 [Swagger](http://swagger.io/) API の定義に URL エンドポイントを指定するには、GET 要求で Swagger 2.0 API の定義を返す API アプリによって公開された API の相対 URL を `endpoints.apiDefinition` プロパティに格納します。次に例を示します。

		"endpoints": {
		    "apiDefinition": "/swagger/docs/v1"
		}

### 静的 Swagger API の定義

静的 [Swagger](http://swagger.io/) 2.0 API の定義ファイルを指定するには、*Metadata* フォルダーにファイルを保存し、そのファイルに *apiDefinition.swagger.json* という名前を付けます。

![](./media/app-service-api-direct-deploy-metadata/apidefinmetadata.png)

*apiapp.json* ファイルから `endpoints.apiDefinition` を除外するか、その値を null に設定します。`endpoints.apiDefinition` URL と *apiDefinition.swagger.json* ファイルの両方を含めた場合は、URL が優先され、ファイルは無視されます。
 
### API アプリのその他のメタデータ

*apiapp.json* ファイルおよび *Metadata* フォルダーの詳細については、「[API アプリ パッケージの作成](app-service-api-create-package.md)」を参照してください。


<!--HONumber=52-->
