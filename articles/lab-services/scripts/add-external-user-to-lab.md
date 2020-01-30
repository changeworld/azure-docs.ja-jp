---
title: PowerShell - Azure DevTest Labs のラボに外部ユーザーを追加する
description: この PowerShell スクリプトは、Azure DevTest Labs でラボに外部ユーザーを追加します。
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: da5cc4a3809b1867e8aa2c3b21e88c2f30ae3d4d
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169681"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>PowerShell を使用して Azure DevTest Labs のラボに外部ユーザーを追加する

このサンプル PowerShell スクリプトは、Azure DevTest Labs でラボに外部ユーザーを追加します。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>前提条件
* **ラボ** 。 スクリプトを実行するには、既存のラボが必要です。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。 

| command | メモ |
|---|---|
| [Get-AzADUser](/powershell/module/az.resources/get-azaduser) | Azure Active Directory からユーザー オブジェクトを再試行します。 |
| [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) | 指定したプリンシパルに、指定のスコープで、指定したロールを割り当てます。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Lab Services のその他の PowerShell のサンプル スクリプトについては、[Azure Lab Services の PowerShell のサンプル](../samples-powershell.md)に関する記事をご覧ください。
