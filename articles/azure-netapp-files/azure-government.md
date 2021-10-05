---
title: Azure Government 向け Azure NetApp Files | Microsoft Docs
description: この記事では、Azure NetApp Files と一緒に使用するために Azure Government 米国に接続する方法の概要を説明します
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: b-juche
ms.openlocfilehash: b76895fdd82122413c83ebc017944578001ce0b9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699266"
---
# <a name="azure-netapp-files-for-azure-government"></a>Azure Government 向け Azure NetApp Files 

[Microsoft Azure Government](../azure-government/documentation-government-welcome.md) は、政府機関とそのパートナーがミッションクリティカルなワークロードをクラウドに変換できる専用のクラウドを提供します。  

この記事では、Azure NetApp Files サービスに Azure Government 内でアクセスする方法を示します。 

## <a name="portal"></a>ポータル 

Azure Government ユーザーは、ブラウザーを **portal.azure.us** にポイントすることで、Azure NetApp Files にアクセスできます。ポータル サイト名は **Microsoft Azure Government** です。詳細については、[ポータルを使用した Azure Government への接続](../azure-government/documentation-government-get-started-connect-with-portal.md)に関する記事をご確認ください。   

![URL として portal.azure.us が強調表示された Azure Government ポータルのスクリーンショット](../media/azure-netapp-files/azure-government.jpg)

Microsoft Azure Government ポータルから、Azure portal でアクセスするのと同じ方法で、Azure NetApp Files にアクセスできます。たとえば、Azure portal の [リソースを検索] ボックスに「**Azure NetApp Files**」と入力し、表示された一覧から **[Azure NetApp Files]** を選択します。  

サービスの使用の詳細については、[Azure NetApp Files](/azure/azure-netapp-files/) のドキュメントを参照してください。

## <a name="azure-cli"></a>Azure CLI 

クラウド名を `AzureUSGovernment` に設定し、`az login` コマンドを使用して通常と同じ方法でログインし、Azure Government に接続できます。 ログイン コマンドを実行すると、ブラウザーが起動します。ここで、適切な Azure Government 資格情報を入力します。  

```azurecli 

az cloud set --name AzureUSGovernment 

``` 

クラウドが `AzureUSGovernment` に設定されていることを確認するには、次を実行します。 

```azurecli 

az cloud list --output table 

``` 

このコマンドを実行すると、Azure クラウドの場所を含むテーブルが生成されます。 `AzureUSGovernment` の `isActive` 列エントリは、`true` であるはずです。  

詳細は、「[Azure CLI を使用した Azure Government への接続](../azure-government/documentation-government-get-started-connect-with-cli.md)」を参照してください。

## <a name="rest-api"></a>REST API

Azure Government のエンドポイントは、商用 Azure エンドポイントとは異なります。 さまざまなエンドポイントの一覧については、Azure Government の「[開発者向ガイダンス](../azure-government/compare-azure-government-global-azure.md#guidance-for-developers)」を参照してください。

## <a name="powershell"></a>PowerShell

PowerShell を使用して Azure Government に接続する場合は、環境パラメーターを指定して、正しいエンドポイントに確実に接続する必要があります。 そこから、通常 PowerShell で行うのと同様に、Azure NetApp Files の使用に進めます。 

| 接続の種類 | コマンド | 
| --- | --- | 
| [Azure](/powershell/module/az.accounts/Connect-AzAccount) のコマンド |`Connect-AzAccount -EnvironmentName AzureUSGovernment` | 
| [Azure Active Directory](/powershell/module/azuread/connect-azuread) のコマンド |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` | 
| [Azure (クラシック デプロイ モデル)](/powershell/module/servicemanagement/azure.service/add-azureaccount) のコマンド |`Add-AzureAccount -Environment AzureUSGovernment` | 
| [Azure Active Directory (クラシック デプロイ モデル)](/previous-versions/azure/jj151815(v=azure.100)) のコマンド |`Connect-MsolService -AzureEnvironment UsGovernment` | 

詳しくは、「[PowerShell を使用した Azure Government への接続](../azure-government/documentation-government-get-started-connect-with-ps.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure Government とは?](../azure-government/documentation-government-welcome.md)
* [商用 Azure Government とグローバル Azure の比較](../azure-government/compare-azure-government-global-azure.md)
* [Azure NetApp Files REST API](azure-netapp-files-develop-with-rest-api.md)
* [PowerShell を使用する Azure NetApp Files REST API](develop-rest-api-powershell.md)
