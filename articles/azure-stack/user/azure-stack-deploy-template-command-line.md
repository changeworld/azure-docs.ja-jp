---
title: Azure Stack でコマンド ラインを使ってテンプレートをデプロイする | Microsoft Docs
description: クロスプラットフォーム コマンドライン インターフェイス (CLI) を使用して、Azure Stack にテンプレートをデプロイする方法について説明します
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 2b64591b9042b2c972b149427359a447f80a640e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251520"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>コマンド ラインを使った Azure Stack でのテンプレートのデプロイ

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

コマンド ラインを使用し、Azure Stack Development Kit 環境で Azure Resource Manager テンプレートをデプロイします。 Azure Resource Manager テンプレートは、お使いのアプリケーションのすべてのリソースを、単一の連携した操作でデプロイしてプロビジョニングします。

## <a name="before-you-begin"></a>開始する前に

- Azure CLI で Azure Stack を[インストールして接続](azure-stack-version-profiles-azurecli2.md)します。
- [ストレージ アカウントの作成例のテンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account)からファイル *azuredeploy.json* と *azuredeploy.parameters.json* をダウンロードします。

## <a name="deploy-template"></a>テンプレートのデプロイ

これらのファイルをダウンロードしたフォルダーに移動し、次のコマンドを実行してテンプレートをデプロイします。

```azurecli
az group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json
```

このコマンドで、テンプレートが Azure Stack POC の既定の場所のリソース グループ **cliRG** にデプロイされます。

## <a name="validate-template-deployment"></a>テンプレートのデプロイを検証する

このリソース グループとストレージ アカウントを表示するには、次の CLI コマンドを使用します。

```azurecli
az group list

az storage account list
```

## <a name="next-steps"></a>次の手順

- テンプレートをデプロイする方法の詳細については、次をご覧ください。

[PowerShell を使用したテンプレートのデプロイ](azure-stack-deploy-template-powershell.md)
