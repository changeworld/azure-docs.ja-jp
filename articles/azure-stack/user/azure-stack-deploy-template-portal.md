---
title: Azure Stack でポータルを使用してテンプレートをデプロイする | Microsoft Docs
description: Azure Stack ポータルを使用してテンプレートをデプロイする方法について説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: b9adac3f2f56093c3559570aab4e905eb047ccd2
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247087"
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Azure Stack ポータルを使用したテンプレートのデプロイ

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

ポータルを使用して、Azure Resource Manager テンプレートを Azure Stack にデプロイできます。

## <a name="to-deploy-a-template"></a>テンプレートをデプロイするには

1. ポータルにサインインし、**[+ リソースの作成]** を選択し、**[カスタム]** を選択します。
2. **[テンプレートのデプロイ]** を選択します。
3. **[テンプレートの編集]** を選択してから、JSON テンプレート コードをコード ウィンドウに貼り付けます。 **[保存]** を選択します。
4. **[パラメーターの編集]** を選択し、表示されているパラメーターの値を指定して、**[OK]** を選択します。
5. **[サブスクリプション]** を選択します。 使用するサブスクリプションを選択してから、**[OK]** を選択します。
6. **[リソース グループ]** を選択します。 既存のリソース グループを選択するか、新しいリソース グループを作成して、**[OK]** を選択します。
7. **作成**を選択します。 ダッシュボードの新しいタイルにより、テンプレート デプロイの進行状況を追跡します。

## <a name="next-steps"></a>次の手順

テンプレートのデプロイの詳細については、次の記事を参照してください。

- [PowerShell を使用したテンプレートのデプロイ](azure-stack-deploy-template-powershell.md)
