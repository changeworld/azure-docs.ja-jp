<properties 
	pageTitle="MongoDB のプロトコル対応の DocumentDB アカウントを作成する | Microsoft Azure" 
	description="現在プレビューとして提供されている MongoDB のプロトコル対応の DocumentDB アカウントを作成する方法について説明します。" 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/23/2016" 
	ms.author="mimig"/>

# Azure ポータルを使用して MongoDB のプロトコル対応の DocumentDB アカウントを作成する方法

MongoDB のプロトコル対応の Azure DocumentDB アカウントを作成するには、次の要件を満たす必要があります。

- Azure アカウントを入手する。まだ持っていない場合は、[無料 Azure アカウント](https://azure.microsoft.com/free/)を取得できます。

## アカウントの作成  

MongoDB のプロトコル対応の DocumentDB アカウントを作成するには、次の手順を実行します。

1. 新しいウィンドウで、[Azure ポータル](https://portal.azure.com)にサインインします。
2. **[新規]**、**[データ + ストレージ]**、**[すべて表示]** の順にクリックし、**[データ + ストレージ]** カテゴリから "DocumentDB プロトコル" を探します。**[DocumentDB - MongoDB のプロトコル サポート]** をクリックします。

	![Screen shot of the Marketplace and Data + Storage search blades, highlighting DocumentDB - Protocol Support for MongoDB, Mongo database](./media/documentdb-create-mongodb-account/marketplacegallery2.png)

3. **[データ + ストレージ]** カテゴリの **[ストレージ]** で **[さらに表示]** をクリックし、**[さらに読み込む]** を何度かクリックして **[DocumentDB - MongoDB のプロトコル サポート]** を表示します。**[DocumentDB - MongoDB のプロトコル サポート]** をクリックします。

	![Screen shot of the Marketplace and Data + Storage blades, highlighting DocumentDB - Protocol Support for MongoDB, Mongo database](./media/documentdb-create-mongodb-account/marketplacegallery1.png)

4. **[DocumentDB - MongoDB のプロトコル サポート (プレビュー)]** ブレードで **[作成]** をクリックして、プレビューのサインアップ プロセスを開始します。

	![The DocumentDB - Protocol Support for MongoDB blade in the Azure portal](./media/documentdb-create-mongodb-account/marketplacegallery3.png)

5. **[DocumentDB アカウント]** ブレードで **[プレビューにサインアップ]** をクリックします。情報を確認し、**[OK]** をクリックします。

	![The Sign up to preview today blade for DocumentDB - Protocol Support for MongoDB in the Azure portal](./media/documentdb-create-mongodb-account/registerforpreview.png)

6.  プレビューの使用条件に同意して [作成] ブレードに戻ります。**[DocumentDB アカウント]** ブレードで、アカウントに必要な構成を指定します。

	![Screen shot of the New DocumentDB with protocol support for MongoDB blade](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.png)


	- **[ID]** ボックスに、アカウントを識別する名前を入力します。**ID** が検証されると、緑色のチェック マークが **[ID]** ボックスに表示されます。この **ID** の値は、URI 内のホスト名になります。**ID** に含めることができるのは英小文字、数字、および "-" のみで、文字数は 3 ～ 50 文字にする必要があります。選択したエンドポイント名に *documents.azure.com* が追加され、これがアカウントのエンドポイントになります。

	- **[サブスクリプション]** で、アカウントに使用する Azure サブスクリプションを選択します。アカウントのサブスクリプションが 1 つである場合は、既定ではそのアカウントが選択されます。

	- **[リソース グループ]** で、アカウントのリソース グループを選択または作成します。既定では、Azure サブスクリプションの既存のリソース グループが選択されます。ただし、アカウントを追加する新しいリソース グループの作成を選択することもできます。詳細については、「[Azure ポータルを使用した Azure リソースの管理](resource-group-portal.md)」を参照してください。

	- **[場所]** を使用して、アカウントのホストとなる地理的場所を指定します。
   
	- **[ダッシュボードにピン留めする]** をオンにします。

7.	新しいアカウント オプションを構成したら、**[作成]** をクリックします。アカウントの作成には数分かかる場合があります。状態を確認するには、スタート画面で進行状況を監視してください。![スタート画面の [作成中] タイルのスクリーン ショット (オンライン データベース クリエーター)](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-3.png)

	または、通知ハブから進行状況を監視できます。

	![迅速なデータベースの作成 - DocumentDB アカウントを作成中であることを示す通知ハブのスクリーンショット](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-4.png)

	![DocumentDB アカウントが正常に作成され、リソース グループにデプロイされたことを示す通知ハブのスクリーンショット - オンライン データベース クリエーターの通知](./media/documentdb-create-mongodb-account/create-nosql-db-databases-json-tutorial-5.png)

8.	アカウントは、その作成後から既定の設定で使用できる状態になっています。

	![Screen shot of the default account blade](./media/documentdb-create-mongodb-account/defaultaccountblades.png)
	

## 次のステップ


- MongoDB のプロトコル対応の DocumentDB アカウントに[接続](documentdb-connect-mongodb-account.md)する方法を確認します。

 

<!---HONumber=AcomDC_0928_2016-->