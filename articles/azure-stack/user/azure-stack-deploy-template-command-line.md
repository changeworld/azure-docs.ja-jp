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
ms.date: 08/15/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 7814552256f17c5265bbeb4ce8c069dd8dca1bb2
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "41947949"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>コマンド ラインを使った Azure Stack でのテンプレートのデプロイ

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

コマンド ラインを使用して、Azure Resource Manager テンプレートを Azure Stack Development Kit にデプロイします。 Azure Resource Manager テンプレートは、お使いのアプリケーションのすべてのリソースを、単一の連携した操作でデプロイしてプロビジョニングします。

## <a name="before-you-begin"></a>開始する前に
 - Azure CLI で Azure Stack を[インストールして接続](azure-stack-version-profiles-azurecli2.md)します。
 - [ストレージ アカウントの作成例のテンプレート](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account)からファイル *azuredeploy.json* と *azuredeploy.parameters.json* をダウンロードします。
 
## <a name="deploy-template"></a>テンプレートのデプロイ
これらのファイルをダウンロードしたフォルダーに移動し、次のコマンドを実行してテンプレートをデプロイします。

    azure group create "cliRG" "local" –f azuredeploy.json –d "testDeploy" –e azuredeploy.parameters.json

このコマンドで、テンプレートが Azure Stack POC の既定の場所のリソース グループ **cliRG** にデプロイされます。

## <a name="validate-template-deployment"></a>テンプレートのデプロイを検証する
このリソース グループとストレージ アカウントを表示するには、次のコマンドを使用します。

    azure group list

    azure storage account list




