---
title: "ストレージの Resource Manager テンプレート | Microsoft Docs"
description: "テンプレートを使ってストレージ アカウントをデプロイするためのリソース マネージャー スキーマを示します。"
services: azure-resource-manager,storage
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 25d35683-fe99-4a11-8b1a-b80accab58e7
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: b089bc075ec3ec718eb21357bbbc5c4af09f4af1
ms.openlocfilehash: b01125160875e367b9b6e1d02031c5dd80b41594


---
# <a name="storage-account-template-schema"></a>ストレージ アカウント テンプレート スキーマ
ストレージ アカウントを作成します。

## <a name="schema-format"></a>スキーマの形式
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

## <a name="values"></a>値
次の表では、スキーマに設定する必要がある値について説明します。

| 名前 | 値 |
| --- | --- |
| type |列挙型<br />必須<br />**Microsoft.Storage/storageAccounts**<br /><br />作成するリソースの種類。 |
| apiVersion |列挙型<br />必須<br />**2015-06-15** または **2015-05-01-preview**<br /><br />リソースの作成に使用する API バージョン。 |
| 名前 |string<br />必須<br />3 ～ 24 文字、数字と小文字のみ。<br /><br />作成するストレージ アカウントの名前。 Azure 全体で重複しない、一意の名前にしてください。 次の例で示すように、名前付け規則では [uniqueString](resource-group-template-functions.md#uniquestring) 関数の使用を検討してください。 |
| location |string<br />必須<br />ストレージ アカウントをサポートするリージョン。 有効なリージョンを確認するには、「[サポートされているリージョン](resource-manager-supported-services.md#supported-regions)」を参照します。<br /><br />ストレージ アカウントをホストするリージョン。 |
| プロパティ |オブジェクト<br />必須<br />[プロパティ オブジェクト](#properties)<br /><br />作成するストレージ アカウントの種類を指定するオブジェクト。 |

<a id="properties" />

### <a name="properties-object"></a>プロパティ オブジェクト
| 名前 | 値 |
| --- | --- |
| accountType |String<br />必須<br />**Standard_LRS**、**Standard_ZRS**、**Standard_GRS**、**Standard_RAGRS**、または **Premium_LRS**<br /><br />ストレージ アカウントの種類。 使用できる値は、Standard ローカル冗長、Standard ゾーン冗長、Standard geo 冗長、Standard 読み取りアクセス geo 冗長、および Premium ローカル冗長に対応しています。 これらのアカウントの種類については、「[Azure Storage のレプリケーション](../storage/storage-redundancy.md)」を参照してください。 |

## <a name="examples"></a>例
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

## <a name="quickstart-templates"></a>クイック スタート テンプレート
ストレージ アカウントを含んだクイック スタート テンプレートは多数存在します。 以下のテンプレートで、いくつかの一般的なシナリオを紹介しています。

* [標準的なストレージ アカウントの作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create)
* [Windows VM の単純なデプロイ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
* [Linux VM の単純なデプロイ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* [ストレージ アカウントを配信元とする CDN プロファイル、CDN エンドポイントの作成]https://github.com/Azure/azure-quickstart-templates/tree/master/201-cdn-with-storage-account)
* [9 つの VM から成る高可用性 SharePoint ファームを PowerShell DSC 拡張を使って作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/sharepoint-server-farm-ha)
* [WAD を有効にした 5 ノードの安全な Service Fabric クラスターから成る単純なデプロイ](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype)
* [4 つの空のデータ ディスクを使って Windows イメージから仮想マシンを作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-data-disk)

## <a name="next-steps"></a>次のステップ
* Storage の一般情報については、「[Microsoft Azure Storage の概要](../storage/storage-introduction.md)」を参照してください。
* 仮想マシンで新しいストレージ アカウントを使用するテンプレートの例については、「[単純な Linux VM のデプロイ](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/)」または「[単純な Windows VM のデプロイ](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/)」を参照します。




<!--HONumber=Jan17_HO1-->


