<properties
   pageTitle="ストレージのリソース マネージャー テンプレート | Microsoft Azure"
   description="ストレージ アカウントのリソース マネージャーのスキーマを示しています。"
   services="azure-resource-manager,storage"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2015"
   ms.author="tomfitz"/>

# ストレージ アカウント - テンプレート スキーマ

ストレージ アカウントを作成します。

## スキーマの形式

ストレージ アカウントを作成するには、テンプレートのリソース セクションに次のスキーマを追加します。

    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2015-06-15",
        "name": string,
        "location": string,
        "properties": 
        {
        	"accountType": string
        }
    }

## 値

次の表では、スキーマに設定する必要がある値について説明します。

| 名前 | 型 | 必須 | 使用できる値 | 説明 |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | あり | **Microsoft.Storage/storageAccounts** | 作成するリソースの種類。 |
| apiVersion | enum | あり | **2015-06-15** <br /> **2015-05-01-preview** | リソースの作成に使用する API バージョン。 | 
| name | string | あり | 3 ～ 24 文字、数字と小文字のみ | 作成するストレージ アカウントの名前。Azure 内で重複しない、一意の名前にしてください。次の例で示すように、名前付け規則では [uniqueString](resource-group-template-functions.md#uniquestring) 関数の使用を検討してください。 |
| location | string | あり | 有効なリージョンを確認するには、[サポートされているリージョン](resource-manager-supported-services.md#supported-regions)を参照します。 | ストレージ アカウントをホストするリージョン。 |
| プロパティ | オブジェクト | あり | (下記参照) | 作成するストレージ アカウントの種類を指定するオブジェクト。

### プロパティ オブジェクト

| 名前 | 型 | 必須 | 使用できる値 | 説明 |
| ---- | ---- | -------- | ---------------- | ----------- |
| accountType | string | あり | **Standard\_LRS**<br />Standard\_ZRS<br />Standard\_GRS<br />Standard\_RAGRS<br />Premium\_LRS | ストレージ アカウントの種類。使用できる値は、標準のローカル冗長、標準のゾーン冗長、標準の地理冗長、標準の読み取りアクセス地理冗長、および Premium ローカル冗長に対応しています。これらのアカウントの種類については、「[Azure Storage のレプリケーション](./storage/storage-redundancy.md)」を参照してください。 |

	
## 例

次の例では、リソース グループの ID に基づく一意の名前を持つ、標準のローカル冗長ストレージ アカウントをデプロイします。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2015-06-15",
                "name": "[concat('storage', uniqueString(resourceGroup().id))]",
		         "location": "[resourceGroup().location]",
        	     "properties": 
        	     {
        		      "accountType": "Standard_LRS"
        	     }
	        }
	    ],
	    "outputs": {}
    }

## 次のステップ

- ストレージの一般情報については、「[Microsoft Azure Storage の概要](./storage/storage-introduction.md)」を参照してください。
- Virtual Machine で新しいストレージ アカウントを使用するテンプレートの例については、「[単純な Linux VM のデプロイ](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/)」または「[単純な Windows VM のデプロイ](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/)」を参照します。

<!---HONumber=Nov15_HO1-->