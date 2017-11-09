---
title: "Azure PowerShell スクリプトのサンプル - マネージ アプリケーションをデプロイする | Microsoft Docs"
description: "Azure PowerShell スクリプトのサンプル - マネージ アプリケーション定義をデプロイする"
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 2429d561beffed5bc171b9dbc2c2c9c88eba3313
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2017
---
# <a name="deploy-a-managed-application-for-a-service-catalog-with-powershell"></a>PowerShell を使用してサービス カタログのマネージ アプリケーションをデプロイする

このスクリプトは、サービス カタログからマネージ アプリケーション定義をデプロイします。


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/managed-applications/create-application/create-application.ps1 "Create application")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは次のコマンドを使用してマネージ アプリケーションをデプロイします。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [New-AzureRmManagedApplication](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermmanagedapplication) | マネージ アプリケーションを作成します。 テンプレートの定義 ID とパラメーターを指定します。 |


## <a name="next-steps"></a>次のステップ

* マネージ アプリケーションの概要については、「[Azure マネージ アプリケーションの概要](../overview.md)」を参照してください。
* PowerShell について詳しくは、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/azure/get-started-azureps)をご覧ください。
