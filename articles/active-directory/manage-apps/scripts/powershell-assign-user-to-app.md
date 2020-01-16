---
title: PowerShell サンプル - アプリケーション プロキシ アプリへのユーザーの割り当て
description: Azure Active Directory (Azure AD) アプリケーション プロキシ アプリケーションにユーザーを割り当てる PowerShell の例。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42667ebdfc37b679d56421e3a82d4a6aaaed8fdf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75476017"
---
# <a name="assign-a-user-to-a-specific-azure-ad-application-proxy-application"></a>特定の Azure AD アプリケーション プロキシ アプリケーションへのユーザーの割り当て

この PowerShell スクリプトの例を使用すると、特定の Azure AD アプリケーション プロキシ アプリケーションにユーザーを割り当てることができます。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

このサンプルには、[Graph 用 AzureAD V2 PowerShell モジュール](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) または [Graph 用 AzureAD V2 PowerShell モジュール プレビュー バージョン](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) が必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-user-to-app.ps1 "Assign a user to an application")]

## <a name="script-explanation"></a>スクリプトの説明

| command | メモ |
|---|---|
| [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/AzureAD/new-azureaduserapproleassignment?view=azureadps-2.0) | ユーザーをアプリケーション ロールに割り当てます。 |

## <a name="next-steps"></a>次のステップ

Azure AD PowerShell モジュールの詳細については、[Azure AD PowerShell モジュールの概要](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)を参照してください。

アプリケーション プロキシのその他の PowerShell の例については、[Azure AD アプリケーション プロキシの Azure AD PowerShell の例](../application-proxy-powershell-samples.md)を参照してください。