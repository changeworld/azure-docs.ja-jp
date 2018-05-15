---
title: 'PowerShell スクリプト: Azure Lab Services のカスタム ラボに外部ユーザーを追加する | Microsoft Docs'
description: この PowerShell スクリプトは、Azure Lab Services でカスタム ラボに外部ユーザーを追加します。
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: b089067a889f0ffd3b317fcc3f0784d176473b91
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-add-an-external-user-to-a-custom-lab"></a>PowerShell を使用して外部ユーザーをカスタム ラボに追加する

この PowerShell のサンプル スクリプトは、Azure Lab Services で外部ユーザーをカスタム ラボに追加します。 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>前提条件
* **カスタム ラボ**。 スクリプトを実行するには、既存のカスタム ラボが必要です。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a custom lab")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。 

| コマンド | メモ |
|---|---|
| [Get-AzureRmADUser](/powershell/module/azurerm.resources/get-azurermaduser) | Azure Active Directory からユーザー オブジェクトを再試行します。 |
| [New-AzureRmRoleAssignment](/module/azurerm.resources/new-azurermroleassignment) | 指定したプリンシパルに、指定のスコープで、指定したロールを割り当てます。 |

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Lab Services のその他の PowerShell のサンプル スクリプトについては、[Azure Lab Services の PowerShell のサンプル](../samples-powershell.md)に関する記事をご覧ください。