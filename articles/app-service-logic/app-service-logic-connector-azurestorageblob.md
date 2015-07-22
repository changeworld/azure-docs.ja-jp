<properties 
   pageTitle="Azure Storage BLOB コネクタ" 
   description="Azure Storage BLOB コネクタの使用" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="05/11/2015"
   ms.author="rajram"/>
   
#Azure Storage BLOB コネクタ

##概要
Azure Storage BLOB コネクタを使用すると、BLOB コンテナーから BLOB のアップロード、ダウンロード、削除を行うことができます。

##新しい Azure Storage BLOB コネクタの作成
Azure Storage コネクタを新しく作成するには、次の手順に従います。
<ul>
<li>Azure ポータルを起動します。
<li>[+ 新規] \(ページの下部)、[Web + モバイル]、[Azure Marketplace] の順にクリックして、Azure Marketplace を開きます。
</ul>

![Azure Marketplace の起動][1]<br>
<ul>
<li>API Apps をクリックします。
<li><i>BLOB</i> を検索し、[Azure Storage BLOB コネクタ] を選択します。
</ul>

![Azure Storage BLOB コネクタの選択][2]
<br>
<ul>
<li>[作成] をクリックします。
<li>[Azure Storage BLOB コネクタ] ブレードが開いたら、次のデータを指定します。
</ul>

![Azure Storage BLOB コネクタの作成][3]

- **[場所]** - コネクタをデプロイする地域を選択します。
- **[サブスクリプション]** - コネクタを作成するサブスクリプションを選択します。
- **[リソース グループ]** - コネクタを格納するリソース グループを選択するか、作成します。
- **[Web ホスティング プラン]** - Web ホスティング プランを選択するか、作成します。
- **[価格レベル]** - コネクタの価格レベルを選択します。
- **[名前]** - BLOB ストレージ コネクタの名前を指定します。
- **パッケージの設定** 
	- **[コンテナー/SAS URI]** - BLOB コンテナーの URI を指定します。URI には SAS トークンを含めることもできます。たとえば、http://storageaccountname.blob.core.windows.net/containername や http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah です。
	- **[アクセス キー]** - 有効なプライマリ/セカンダリ ストレージ アカウントのアクセス キーを指定します。認証に SAS トークンを使用する場合は、このフィールドは空白のままにしてください。
- [作成] をクリックします。Azure Storage BLOB コネクタが新しく作成されます。

##ロジック アプリでの Azure Storage BLOB コネクタの使用
Azure Storage BLOB コネクタを作成すると、フローから使用できます。

新しいフローを作成するには、[+ 新規]、[Web + モバイル]、[ロジック アプリ] の順に選択します。リソース グループを含むフローのメタデータを指定します。

![ロジック アプリの作成][4]

*[トリガーとアクション]* をクリックします。フローのデザイナーが開きます。

![ロジック アプリの空のフロー デザイナー][5]

Azure Storage BLOB コネクタはアクションとして使用できます。

###アクション
右側のペインから [Azure Storage BLOB コネクタ] をクリックします。コネクタの下には、サポートされているアクションが一覧表示されます。

![Azure Storage BLOB アクションの一覧][10]

Azure Storage BLOB コネクタは 6 つのアクションをサポートします。サポートされるアクションは次のとおりです。

- **[BLOB の取得]**: コンテナーから特定の BLOB を取得します。
- **[BLOB のアップロード]**: 新しい BLOB のアップロードまたは既存の BLOB の更新を行います。
- **[BLOB の削除]**: コンテナーから特定の BLOB を削除します。
- **[BLOB の一覧表示]**: ディレクトリ内のすべての BLOB を一覧表示します。
- **[BLOB のスナップショット]**: 特定の BLOB の読み取り専用スナップショットを作成します。
- **[BLOB のコピー]**: 別の BLOB をコピーして新しい BLOB を作成します。コピー元の BLOB は、同じアカウントのものであっても、別のアカウントのものであってもかまいません。

[BLOB のアップロード] の例を示します。[BLOB のアップロード] をクリックします。

![BLOB のアップロード アクションの入力][11]


- **[BLOB パス]**: アップロードする BLOB のパスを指定します。パスは構成済みのコンテナーのパスを基準として解釈されます。
- **[BLOB コンテンツの書き込み]**: アップロードする BLOB のコンテンツとプロパティを指定します。
- **[コンテンツ転送エンコード]** - [なし] または [Base64] を指定します。
- **[上書き]** - [true] に設定した場合、既存の BLOB が上書きされます。BLOB が既に同じパスに存在する場合、エラーが返されます。

入力し、チェック マークをクリックすると、入力構成は完了です。


構成済みの Azure Storage BLOB の BLOB のアップロードという BLOB アクションにより、入力パラメーターと出力パラメーターの両方が表示されます。

####Azure Storage BLOB アクションへの入力として前のアクションの出力を使用する
構成済みのスクリーンショットでは、コンテンツは式に設定される値です。

	@triggers().outputs.body.Content


任意の値に設定できます。これは一例です。式はロジック アプリのトリガーの出力を取得し、アップロードするファイルのコンテンツとして使用します。前のアクションの出力を使用する場合は、変換を使います。この場合、式は次のようになります。

	@actions('transformservice').outputs.body.OutputXML


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-azurestorageblob/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-azurestorageblob/SelectAzureStorageBlobConnector.PNG
[3]: ./media/app-service-logic-connector-azurestorageblob/CreateAzureStorageBlobConnector.PNG
[4]: ./media/app-service-logic-connector-azurestorageblob/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-azurestorageblob/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-azurestorageblob/ChooseBlobAvailableTrigger.PNG
[7]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsBlobAvailableTrigger.PNG
[8]: ./media/app-service-logic-connector-azurestorageblob/AdvancedInputsBlobAvailableTrigger.PNG
[9]: ./media/app-service-logic-connector-azurestorageblob/ConfiguredBlobAvailableTrigger.PNG
[10]: ./media/app-service-logic-connector-azurestorageblob/ListOfAzureStorageBlobActions.PNG
[11]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsUploadBlob.PNG
 

<!----HONumber=62-->