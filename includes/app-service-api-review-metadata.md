## API アプリのメタデータの確認

Web API プロジェクトを API アプリとしてデプロイできるようにするメタデータは、*apiapp.json* ファイルと *Metadata* フォルダーに格納されています。

![](./media/app-service-api-review-metadata/metadatainse.png)

*apiapp.json* ファイルの内容は、既定では、次の例のようになります。

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "ContactsList",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "ContactsList",
		    "summary": "",
		    "author": "",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    }
		}

このチュートリアルでは、既定値をそのまま使用できます。このチュートリアルの「[API アプリのメタデータ](#api-app-metadata)」セクションで、このメタデータをカスタマイズする方法について説明します。
<!--HONumber=54-->