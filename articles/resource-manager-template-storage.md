<properties
   pageTitle="ストレージのリソース マネージャー テンプレート | Microsoft Azure"
   description="テンプレートを使ってストレージ アカウントをデプロイするためのリソース マネージャー スキーマを示します。"
   services="azure-resource-manager,storage"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# ストレージ アカウント テンプレート スキーマ

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

| 名前 | 値 |
| ---- | ---- |
| type | 列挙型<br />必須<br />**Microsoft.Storage/storageAccounts**<br /><br />作成するリソースの種類。 |
| apiVersion | 列挙型<br />必須<br />**2015-06-15** または **2015-05-01-preview**<br /><br />リソースの作成に使用する API バージョン。 | 
| name | 文字列<br />必須<br />3 ～ 24 文字。数字と小文字のみ。<br /><br />作成するストレージ アカウントの名前。Azure 全体で重複しない、一意の名前にしてください。次の例で示すように、名前付け規則では [uniqueString](resource-group-template-functions.md#uniquestring) 関数の使用を検討してください。 |
| location | 文字列<br />必須<br />ストレージ アカウントをサポートするリージョン。有効なリージョンを確認する場合は、「[サポートされているリージョン](resource-manager-supported-services.md#supported-regions)」をご覧ください。<br /><br />ストレージ アカウントをホストするリージョン。 |
| properties | オブジェクト<br />必須<br />[プロパティ オブジェクト](#properties)<br /><br />作成するストレージ アカウントの種類を指定するオブジェクト。 |

<a id="properties" />
### プロパティ オブジェクト

| 名前 | 値 |
| ---- | ---- | 
| accountType | 文字列<br />必須<br />**Standard\_LRS**、**Standard\_ZRS**、**Standard\_GRS**、**Standard\_RAGRS**、または **Premium\_LRS**<br /><br />ストレージ アカウントの種類。使用できる値は、Standard ローカル冗長、Standard ゾーン冗長、Standard geo 冗長、Standard 読み取りアクセス geo 冗長、および Premium ローカル冗長に対応しています。これらのアカウントの種類については、「[Azure Storage のレプリケーション](./storage/storage-redundancy.md)」を参照してください。 |

	
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

## クイック スタート テンプレート

ストレージ アカウントを含んだクイック スタート テンプレートは多数存在します。以下のテンプレートで、いくつかの一般的なシナリオを紹介しています。

- [標準的なストレージ アカウントの作成](https://azure.microsoft.com/documentation/templates/101-storage-account-create)
- [Windows VM の単純なデプロイ](https://azure.microsoft.com/documentation/templates/101-vm-simple-windows)
- [Linux VM の単純なデプロイ](https://azure.microsoft.com/documentation/templates/101-vm-simple-linux)
- [ストレージ アカウントを配信元とする CDN プロファイルと CDN エンドポイントの作成](https://azure.microsoft.com/documentation/templates/201-cdn-with-storage-account)
- [9 つの VM から成る高可用性 SharePoint ファームを PowerShell DSC 拡張を使って作成する](https://azure.microsoft.com/documentation/templates/sharepoint-server-farm-ha)
- [WAD を有効にした 5 ノードの安全な Service Fabric クラスターから成る単純なデプロイ](https://azure.microsoft.com/documentation/templates/service-fabric-secure-cluster-5-node-1-nodetype-wad)
- [4 つの空のデータ ディスクを使って Windows イメージから仮想マシンを作成する](https://azure.microsoft.com/documentation/templates/101-vm-multiple-data-disk)


## 次のステップ

- Storage の一般情報については、「[Microsoft Azure Storage の概要](./storage/storage-introduction.md)」を参照してください。
- 仮想マシンで新しいストレージ アカウントを使用するテンプレートの例については、「[単純な Linux VM のデプロイ](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/)」または「[単純な Windows VM のデプロイ](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/)」を参照します。

<!---HONumber=AcomDC_0406_2016-->