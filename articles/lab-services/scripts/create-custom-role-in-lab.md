---
title: PowerShell - Azure DevTest Labs のラボにカスタム ロールを作成する
description: この記事では、Azure DevTest Labs のラボに外部ユーザーを追加する Azure PowerShell スクリプトについて説明します。
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 9605b3c63877335b8f180ae5fd2b06edc1601096
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760462"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>PowerShell を使用して Azure DevTest Labs のラボにカスタム ロールを作成する

この PowerShell のサンプル スクリプトは、Azure DevTest Labs のラボで使用するカスタム ロールを作成します。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>前提条件
* **ラボ** 。 スクリプトを実行するには、既存のラボが必要です。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。 

| command | メモ |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Azure RBAC を使用してセキュリティで保護された Azure リソース プロバイダーの操作を取得します。 |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | 割り当て可能なすべての Azure RBAC ロールを一覧表示します。 |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | カスタム ロールを作成します。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Lab Services のその他の PowerShell のサンプル スクリプトについては、[Azure Lab Services の PowerShell のサンプル](../samples-powershell.md)に関する記事をご覧ください。
