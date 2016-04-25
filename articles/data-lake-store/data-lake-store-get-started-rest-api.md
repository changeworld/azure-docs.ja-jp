<properties 
   pageTitle="REST API で Data Lake Store の使用を開始する | Microsoft Azure" 
   description="WebHDFS REST API を使用して Data Lake Store に対する操作を実行する" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/08/2016"
   ms.author="nitinme"/>

# REST API で Azure Data Lake Store の使用を開始する

> [AZURE.SELECTOR]
- [ポータル](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.JS](data-lake-store-manage-use-nodejs.md)

この記事では、WebHDFS REST API と Data Lake Store REST API を使用して、アカウントの管理と、Azure Data Lake Store に対するファイル システム操作を実行する方法について説明します。Azure Data Lake Store では、アカウント管理操作用の独自の REST API を公開しています。ただし、Data Lake Store は、HDFS および Hadoop エコシステムと互換性があるため、WebHDFS REST API を使用したファイル システム操作もサポートしています。

>[AZURE.NOTE] Data Lake Store の REST API サポートの詳細については、「[Azure Data Lake Store REST API リファレンス](https://msdn.microsoft.com/library/mt693424.aspx)」を参照してください。

## 前提条件

- **Azure サブスクリプション**。[Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
- Data Lake Store のパブリック プレビューに対して、**Azure サブスクリプションを有効にする**。[手順](data-lake-store-get-started-portal.md#signup)を参照してください。
- **Azure Active Directory アプリケーションを作成する**「[ポータルを利用し、Active Directory のアプリケーションとサービス プリンシパルを作成する](../resource-group-create-service-principal-portal.md)」を参照してください。アプリケーションの作成が完了したら、アプリケーションに関連する次の値を取得します。
	- アプリケーションのクライアント ID とテナント ID を取得する
	- 認証キーを作成する
	- 委任されたアクセス許可を設定する

	これらの値を取得する方法の手順については、上に示したリンクで確認できます。
- **Azure Active Directory アプリケーションをロールに割り当てます**。ロールは、Azure Active Directory アプリケーションにアクセス許可を付与するスコープのレベルにあります。たとえば、サブスクリプション レベルまたはリソース グループのレベルで、アプリケーションを割り当てることができます。手順については、「[アプリケーションをロールに割り当てる](../resource-group-create-service-principal-portal.md#assign-application-to-role)」を参照してください。
- [cURL](http://curl.haxx.se/)。この記事では、cURL を使用して、Data Lake Store アカウントに対して REST API 呼び出しを行う方法を説明します。

## Azure Active Directory を使用して認証する方法

Azure Active Directory を使用した認証方法には 2 つあります。

* **対話型** - アプリケーションがユーザーにログインするように求めます。詳細については、「[Authorization code grant flow (認証コードの付与フロー)](https://msdn.microsoft.com/library/azure/dn645542.aspx)」を参照してください。

* **非対話型** - アプリケーションが独自の資格情報を指定します。詳細については、「[Service to service calls using credentials (資格情報によるサービス間の呼び出し)](https://msdn.microsoft.com/library/azure/dn645543.aspx)」を参照してください。

この記事では、**非対話型**のアプローチを使用します。このアプローチの場合は、次に示すように POST 要求を発行する必要があります。

	curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

この要求の出力には、後で REST API 呼び出しと共に渡される認証トークン (以下の出力の `access-token` で示される) が取り込まれます。この認証トークンをテキスト ファイルに保存します。この記事の後半で必要となります。

	{"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

## Data Lake Store アカウントを作成する

この操作は、[ここ](https://msdn.microsoft.com/library/mt694078.aspx)で定義されている REST API をベースにしています。

次の cURL コマンドを使用します。**<yourstorename>** をご自分の Data Lake Store 名に置換します。

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@C:\temp\input.json

上記のコマンドで、<`REDACTED`> を、以前に取得した承認トークンに置換します。このコマンドの要求ペイロードは、上記の `-d` パラメーターで指定した **input.json** ファイルに含まれています。input.json ファイルの内容は、次のようになります。

	{
	"location": "eastus2",
	"tags": {
		"department": "finance"
		},
	"properties": {}
	}	

## Data Lake Store アカウントでフォルダーを作成する

この操作は、[ここ](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory)で定義されている WebHDFS REST API 呼び出しをベースにしています。

次の cURL コマンドを使用します。**<yourstorename>** をご自分の Data Lake Store 名に置換します。

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

上記のコマンドで、<`REDACTED`> を、以前に取得した承認トークンに置換します。このコマンドを実行すると、Data Lake Store アカウントのルート フォルダーの下に **mytempdir** という名前のディレクトリが作成されます。

操作が正常に完了すると、次のような応答が表示されます。

	{"boolean":true}

## Data Lake Store アカウントのフォルダーを一覧する

この操作は、[ここ](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory)で定義されている WebHDFS REST API 呼び出しをベースにしています。

次の cURL コマンドを使用します。**<yourstorename>** をご自分の Data Lake Store 名に置換します。

	curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

上記のコマンドで、<`REDACTED`> を、以前に取得した承認トークンに置換します。

操作が正常に完了すると、次のような応答が表示されます。

	{
	"FileStatuses": {
		"FileStatus": [{
			"length": 0,
			"pathSuffix": "mytempdir",
			"type": "DIRECTORY",
			"blockSize": 268435456,
			"accessTime": 1458324719512,
			"modificationTime": 1458324719512,
			"replication": 0,
			"permission": "777",
			"owner": "NotSupportYet",
			"group": "NotSupportYet"
		}]
	}
	}

## Data Lake Store アカウントにデータをアップロードする

この操作は、[ここ](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File)で定義されている WebHDFS REST API 呼び出しをベースにしています。

WebHDFS REST API を使用したデータのアップロードは、次に説明するように 2 段階のプロセスとなります。

1. アップロードするファイル データを送信することなく、HTTP PUT 要求を送信します。次のコマンドで、**<yourstorename>** をご自分の Data Lake Store 名に置換します。

		curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE

	このコマンドの出力には、次に示すように、一時的なリダイレクト URL が含まれます。

		HTTP/1.1 100 Continue

		HTTP/1.1 307 Temporary Redirect
		...
		...
		Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
		...
		...

2. 次に、応答内の **Location** プロパティに関して一覧された URL に対して別の HTTP PUT 要求を送信する必要があります。**<yourstorename>** をご自分の Data Lake Store 名に置換します。

		curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true

	出力は次のようになります。

		HTTP/1.1 100 Continue

		HTTP/1.1 201 Created
		...
		...

## Data Lake Store アカウントからデータを読み取る

この操作は、[ここ](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File)で定義されている WebHDFS REST API 呼び出しをベースにしています。

Data Lake Store アカウントからのデータの読み取りは、2 段階のプロセスとなります。

* まず、エンドポイント `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN` に対して GET 要求を送信します。これにより、次の GET 要求を送信する場所が返されます。
* 次に、エンドポイント `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true` に対して GET 要求を送信します。これにより、ファイルの内容が表示されます。

ただし、1 番目の手順と 2 番目の手順の間に入力パラメーターの違いはないため、`-L` パラメーターを使用して最初の要求を送信できます。`-L` オプションは、基本的に 2 つの要求を 1 つの要求に結合し、新しい場所で cURL により要求をやり直します。最後に、次のように、すべての要求呼び出しの出力が表示されます。**<yourstorename>** をご自分の Data Lake Store 名に置換します。

	curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

次のような出力が表示されます。

	HTTP/1.1 307 Temporary Redirect
	...
	Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
	...
	
	HTTP/1.1 200 OK
	...
	
	Hello, Data Lake Store user!

## Data Lake Store アカウントのファイルの名前を変更する

この操作は、[ここ](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory)で定義されている WebHDFS REST API 呼び出しをベースにしています。

ファイルの名前を変更するには、次の cURL コマンドを使用します。**<yourstorename>** をご自分の Data Lake Store 名に置換します。

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

次のような出力が表示されます。

	HTTP/1.1 200 OK
	...
	
	{"boolean":true}

## Data Lake Store アカウントからファイルを削除する

この操作は、[ここ](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory)で定義されている WebHDFS REST API 呼び出しをベースにしています。

次の cURL コマンドを使用して、ファイルを削除します。**<yourstorename>** をご自分の Data Lake Store 名に置換します。

	curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

出力次のように表示されます。

	HTTP/1.1 200 OK
	...
	
	{"boolean":true}

## Data Lake Store アカウントを削除する

この操作は、[ここ](https://msdn.microsoft.com/library/mt694075.aspx)で定義されている REST API をベースにしています。

Data Lake Store アカウントを削除するには、次の cURL コマンドを使用します。**<yourstorename>** をご自分の Data Lake Store 名に置換します。

	curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

出力次のように表示されます。

	HTTP/1.1 200 OK
	...
	...

## 関連項目

- [Azure Data Lake Store と互換性のあるオープン ソースのビッグ データ アプリケーション](data-lake-store-compatible-oss-other-applications.md)
 

<!---HONumber=AcomDC_0413_2016-->