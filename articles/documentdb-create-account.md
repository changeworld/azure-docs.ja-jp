<properties title="How to Create a DocumentDB Account" pageTitle="データベース アカウントの作成 | Azure" description="ドキュメントに DocumentDB NoSQL、データベース アカウントを作成し、Azure プレビュー ポータルでアカウントの設定を選択する方法について説明します。"  metaKeywords="NoSQL, DocumentDB, database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="mimig" manager="jhubbard" editor="monicar" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/09/2014" ms.author="mimig" />

#How to create a DocumentDB account (DocumentDB アカウントの作成方法)
Microsoft Azure DocumentDB を使用するには、DocumentDB アカウントを作成する必要があります。このトピックでは、Azure プレビュー管理ポータルで DocumentDB アカウントを作成する方法について説明します。  


1.	[プレビュー管理ポータル](https://portal.azure.com/#gallery/Microsoft.DocumentDB)にサインインします。
2.	[新規]、[DocumentDB アカウント] の順にクリックします。  
	![][1]  

	または、スタートボードから Azure Marketplace を参照し、[データ + 分析] カテゴリを選択し、**[DocumentDB]** を選択します。次に、**[作成]** をクリックします。  

	![][2]   

3. **[新しい DocumentDB (プレビュー)]** ブレードで、DocumentDB アカウントに対して必要な構成を指定します。 
 
	![][3] 


	- **[ID]** ボックスに、DocumentDB を識別する名前を入力します。この値は、URI 内のホスト名になります。ID に含めることができるのは英小文字、数字、および '-' 文字のみで、文字数は 3 ～ 50 文字にする必要があります。選択したエンドポイント名に documents.azure.com が追加され、これが DocumentDB アカウント エンドポイントになります。

	- DocumentDB プレビューで単一の標準料金レベルがサポートされているため、**[料金レベル]** レンズはロックされています。詳細については、「[DocumentDB の料金](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409)」を参照してください。

	- **[省略可能な構成]**レンズは、DocumentDB アカウントに割り当てられる初期の容量を指定するときに使用されます。DocumentDB では、容量単位を利用して、占有データベース ストレージとスループットが各容量単位に含まれる、DocumentDB アカウントを拡張できます。既定では、1 つの容量単位がプロビジョニングされます。DocumentDB アカウントで利用可能な容量単位の数は、[プレビュー管理ポータル](https://portal.azure.com/#gallery/Microsoft.DocumentDB)でいつでも調整できます。DocumentDB アカウントの容量およびスループットの詳細については、「[DocumentDB の容量とパフォーマンスの管理][documentdb-manage]」という記事を参照してください。

	- **[リソース グループ]** で、DocumentDB アカウントのリソース グループを選択または作成します。既定では、新しいリソース グループが作成されます。ただし、DocumentDB アカウントを追加する既存のリソース グループを選択することもできます。詳細については、「[リソース グループを使用した Azure リソースの管理](http://azure.microsoft.com/ja-jp/documentation/articles/azure-preview-portal-using-resource-groups/)」を参照してください。

	- **[サブスクリプション]** で、DocumentDB アカウントに使用する Azure サブスクリプションを選択します。アカウントのサブスクリプションが 1 つである場合は、そのアカウントが自動的に選択されます。*
 
	- **[場所]** を使用して、DocumentDB アカウントがホストされる場所を指定します。   

3.	新しい DocumentDB アカウントのオプションを構成したら、**[作成]** をクリックします。DocumentDB アカウントが作成されるまで数分かかる場合があります。状態を確認するには、スタートボードで進行状況を監視してください。  
	![][4]  
  
	または、通知ハブから進行状況を監視できます。  

	![][5]  

	![][6]

4.	DocumentDB アカウントが作成されたら、既定の設定で使用するための準備が整います。

	*DocumentDB アカウントの既定の一貫性は Session に設定されることに注意してください。既定の一貫性の設定は、[プレビュー管理ポータル](https://portal.azure.com/#gallery/Microsoft.DocumentDB)を使用して調整できます。*  
	![][7]  

5.	**[参照]** ブレードから、既存の DocumentDB アカウントにアクセスすることもできます。  
	![][8]

##<a id="NextSteps"></a>次のステップ
Azure DocumentDB を導入するには、次のリソースを参照してください。

-	[DocumentDB のリソース モデルと概念](/documentation/articles/documentdb-resources/)
-	[DocumentDB リソースとの対話](/documentation/articles/documentdb-interactions-with-resources/)
-	[DocumentDB アカウントの作成方法](/documentation/articles/documentdb-create-account/)
-	[DocumentDB アカウントの使用](/documentation/articles/documentdb-get-started/)

DocumentDB について詳しくは、[azure.com](http://go.microsoft.com/fwlink/p/?LinkID=402319) の Azure DocumentDB 資料を参照してください。

[方法:DocumentDB アカウントを作成する]: #Howto
[次のステップ]: #NextSteps
[documentdb-manage]:../documentdb-manage/

<!--Image references-->
[1]: ./media/documentdb-create-account/ca1.png
[2]: ./media/documentdb-create-account/ca2.png
[3]: ./media/documentdb-create-account/ca3.png
[4]: ./media/documentdb-create-account/ca4.png
[5]: ./media/documentdb-create-account/ca5.png
[6]: ./media/documentdb-create-account/ca6.png
[7]: ./media/documentdb-create-account/ca7.png
[8]: ./media/documentdb-create-account/ca8.png

<!--HONumber=35.2-->
