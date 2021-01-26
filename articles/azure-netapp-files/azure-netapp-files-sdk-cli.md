---
title: Azure NetApp Files SDK および CLI ツール | Microsoft Docs
description: Azure NetApp Files のサポートされる SDK と、その GitHub の公開場所、サポートされるコマンド行ツールであるAzure CLI と PowerShell について説明します。
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
ms.date: 10/21/2020
ms.author: b-juche
ms.openlocfilehash: f7b9cabdc7c2c706ff3b8dd5a0b3b5f7ed3666d6
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369031"
---
# <a name="azure-netapp-files-sdks-cli-tools-and-arm-templates"></a>Azure NetApp Files SDK、CLI ツール、ARM テンプレート

この記事では、Azure NetApp Files でサポートされている SDK、コマンドライン (CLI) ツール、Azure Resource Manager (ARM) テンプレートを一覧表示しています。

## <a name="supported-sdks"></a>サポートされる SDK 

次の表は、サポートされる SDK の一覧です。  サポートされている SDK の詳細については、GitHub の公開場所を参照してください。  

|    Language    |    GitHub の SDK の公開場所    |
|------------------|--------------------------------------------------------------|
|    .NET  |    [Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/netapp)    |
|    Python  |  [Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/netapp)    |
|    Go    |    [Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/netapp)       |
|    Java |     [Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/netapp) |
|    JavaScript    |    [Azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/netapp/arm-netapp)    |
|    Ruby   |    [Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby/tree/master/management/azure_mgmt_netapp)    |

## <a name="cli-tools"></a>CLI ツール

次の表は、サポートされている CLI ツールとそのコマンド リファレンスをまとめたものです。   

|    ツール    |    コマンド リファレンス    |
|------------------|--------------------------------------------|
|    Azure CLI  |    [az netappfiles](/cli/azure/netappfiles?view=azure-cli-latest&preserve-view=true)    |
|    PowerShell   |    [Azure PowerShell for Azure NetApp Files](/powershell/module/az.netappfiles/?view=azps-2.5.0#netapp_files&preserve-view=true)    |

## <a name="code-samples"></a>コード サンプル

[Azure NetApp Files のコード サンプル](/samples/browse/?filter-products=netapp&products=azure-netapp-files)を参照してください。

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager のテンプレート  

Azure Resource Manager (ARM) では、宣言型のテンプレートを使用して、アプリケーションをプロビジョニングすることができます。 1 つのテンプレートで、複数のサービスをその依存関係と共にデプロイできます。 アプリケーション ライフサイクルの各ステージで、同じテンプレートを使用してアプリケーションを繰り返しデプロイします。   

[Azure NetApp Files に使用できる ARM テンプレート](https://azure.microsoft.com/resources/templates/?term=anf)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ   
 [Azure SDK のダウンロード](https://azure.microsoft.com/downloads/)
