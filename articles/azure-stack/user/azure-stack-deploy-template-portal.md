---
title: Azure Stack でポータルを使用してテンプレートをデプロイする | Microsoft Docs
description: Azure Stack ポータルを使用してテンプレートをデプロイする方法について説明します。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 278f15271d3a5443102f5e387d3db1adb53fe7db
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/20/2018
ms.locfileid: "34357386"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Azure Stack ポータルを使用したテンプレートのデプロイ

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

ポータルを使用して、Azure Resource Manager テンプレートを Azure Stack にデプロイできます。

テンプレートをデプロイするには:

1. ポータルにサインインし、**[新規]**、**[カスタム]** の順に選択します。
2. **[テンプレートのデプロイ]** を選択します。
3. **[テンプレートの編集]** を選択してから、JSON テンプレート コードをコード ウィンドウに貼り付けます。 **[保存]** を選択します。
4. **[パラメーターの編集]** を選択し、表示されているパラメーターの値を指定して、**[OK]** を選択します。
5. **[サブスクリプション]** を選択します。 使用するサブスクリプションを選択してから、**[OK]** を選択します。
6. **[リソース グループ]** を選択します。 既存のリソース グループを選択するか、新しいリソース グループを作成して、**[OK]** を選択します。
7. **[作成]** を選択します。 ダッシュボードの新しいタイルにより、テンプレート デプロイの進行状況を追跡します。

## <a name="next-steps"></a>次の手順

[PowerShell を使用したテンプレートのデプロイ](azure-stack-deploy-template-powershell.md)
