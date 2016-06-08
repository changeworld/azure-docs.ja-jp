### 前提条件

- [Azure BLOB ストレージ](https://azure.microsoft.com/documentation/services/storage/) アカウント  


ロジック アプリで Azure BLOB ストレージ アカウントを使用するには、Azure BLOB ストレージ アカウントに接続するロジック アプリを承認しておく必要があります。これは、Azure ポータルのロジック アプリ内から簡単に実行できます。

Azure BLOB ストレージ アカウントに接続するロジック アプリを承認する手順を次に示します。
1. Azure BLOB ストレージへの接続を作成するには、ロジック アプリ デザイナーのドロップダウン リストから **[Show Microsoft managed APIs (Microsoft Managed API を表示)]** を選択し、検索ボックスに「*Azure Blob Storage*」と入力します。使用するトリガーまたはアクションを選択します。  
![Azure BLOB ストレージの接続の作成手順](./media/connectors-create-api-azureblobstorage/azureblobstorage-1.png)  
2. これまでに Azure BLOB ストレージへの接続を作成したことがない場合は、Azure BLOB ストレージの資格情報の入力を求められます。この資格情報を使用して、接続するロジック アプリの承認と、Azure BLOB ストレージ アカウントのデータへのアクセスが行われます。  
![Azure BLOB ストレージの接続の作成手順](./media/connectors-create-api-azureblobstorage/azureblobstorage-2.png)  
3. 接続が作成されたら、ロジック アプリで他の手順を自由に実行できるようになります。  
![Azure BLOB ストレージの接続の作成手順](./media/connectors-create-api-azureblobstorage/azureblobstorage-3.png)  

<!---HONumber=AcomDC_0525_2016-->