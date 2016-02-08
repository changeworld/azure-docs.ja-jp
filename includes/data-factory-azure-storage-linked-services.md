## Azure Storage のリンクされたサービス

**Azure Storage のリンクされたサービス**を利用すると、**アカウント キー**を使用して Azure ストレージ アカウントを Azure Data Factory にリンクできます。これによって、Data Factory は Azure Storage にグローバルにアクセスすることができます。次の表は、Azure Storage のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| :-------- | :----------- | :-------- |
| type | type プロパティを **AzureStorage** に設定する必要があります。 | はい |
| connectionString | connectionString プロパティのために Azure Storage に接続するために必要な情報を指定します。 | はい |

Azure Storage のアカウント キーを表示またはコピーする手順については、[ストレージ アクセス キーの表示、コピー、再生成](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)に関するページを参照してください。

**例:**
  
	{  
		"name": "StorageLinkedService",  
		"properties": {  
			"type": "AzureStorage",  
			"typeProperties": {  
				"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"  
			}  
		}  
	}  


## Azure Storage SAS のリンクされたサービス  
Shared Access Signature (SAS) を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。つまり、ストレージ アカウントのオブジェクトへの制限付きアクセス許可を、期間とアクセス許可セットを指定してクライアントに付与できます。また、アカウント アクセス キーを共有する必要はありません。SAS とは、ストレージ リソースへの認証アクセスに必要なすべての情報をクエリ パラメーター内に含む URI です。クライアントは、SAS 内で適切なコンストラクターまたはメソッドに渡すだけで、SAS でストレージ リソースにアクセスできます。SAS の詳細については、「[共有アクセス署名、第 1 部 : SAS モデルについて](../storage/storage-dotnet-shared-access-signature-part-1.md)」を参照してください。
  
Azure Storage SAS のリンクされたサービスを利用すると、Shared Access Signature (SAS) を使用して Azure ストレージ アカウントを Azure Data Factory にリンクできます。これによって、Data Factory は、ストレージ内のすべてまたは特定のリソース (BLOB/コンテナー) へのアクセスが制限付きまたは期限付きになります。次の表は、Azure Storage SAS のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| :-------- | :----------- | :-------- |
| type | type プロパティを **AzureStorageSas** に設定する必要があります。 | はい |
| sasUri | BLOB、コンテナー、テーブルなどの Azure Storage リソースへの Shared Access Signature URI を指定します。詳細については、下の注意事項を参照してください。 | はい | 


**例:**
  
	{  
		"name": "StorageSasLinkedService",  
		"properties": {  
			"type": "AzureStorageSas",  
			"typeProperties": {  
				"sasUri": "<storageUri>?<sasToken>"   
			}  
		}  
	}  

**SAS URI** を作成する際は、次の点を考慮してください。

- Azure Data Factory は、**サービス SAS** のみをサポートします。アカウント SAS はサポートしません。この 2 種類の SAS の詳細については、「[共有アクセス署名の種類](../storage/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures)」を参照してください。
- リンクされたサービス (読み取り、書き込み、読み取り/書き込み) が Data Factory 内でどのように使用されるかに応じて、適切な読み取り/書き込み**アクセス許可**をオブジェクトに設定する必要があります。
- **有効期限**は適切に設定する必要があります。Azure Storage オブジェクトへのアクセスがパイプラインのアクティブな期間内に期限切れにならないことを確認します。
- URI は、必要に応じて、適切なコンテナーや BLOB またはテーブル レベルで作成する必要があります。Azure BLOB への SAS URI を使用すると、Data Factory サービスから特定の BLOB にアクセスできます。Azure BLOB コンテナーへの SAS URI を使用すると、Data Factory サービスはそのコンテナー内の BLOB に対して反復処理を行うことができます。アクセスするオブジェクトの数を後で変更する必要がある場合、または SAS URI を更新する必要がある場合は、リンクされたサービスを新しい URI で更新することを忘れないでください。   

<!---HONumber=AcomDC_0128_2016-->