---
title: PowerShell サンプル - アプリケーション プロキシ アプリへのユーザーの割り当て
description: Azure Active Directory (Azure AD) アプリケーション プロキシ アプリケーションにユーザーを割り当てる PowerShell の例。
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 020909464ca7c271c260dc9662c9e4a18d32f86f
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2020
ms.locfileid: "96859382"
---
# <a name="assign-a-user-to-a-specific-azure-ad-application-proxy-application"></a>特定の Azure AD アプリケーション プロキシ アプリケーションへのユーザーの割り当て

この PowerShell スクリプトの例を使用すると、特定の Azure AD アプリケーション プロキシ アプリケーションにユーザーを割り当てることができます。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

このサンプルでは、[AzureAD V2 PowerShell for Graph モジュール](/powershell/azure/active-directory/install-adv2) (AzureAD)、または[AzureAD V2 PowerShell for Graph モジュール プレビュー バージョン](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) が必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/assign-user-to-app.ps1 "Assign a user to an application")]

## <a name="script-explanation"></a>スクリプトの説明

| command | メモ |
|---|---|
| [New-AzureADUserAppRoleAssignment](/powershell/module/AzureAD/new-azureaduserapproleassignment) | ユーザーをアプリケーション ロールに割り当てます。 |

## <a name="next-steps"></a>次のステップ

Azure AD PowerShell モジュールの詳細については、[Azure AD PowerShell モジュールの概要](/powershell/azure/active-directory/overview)に関する記事を参照してください。

アプリケーション プロキシのその他の PowerShell の例については、[Azure AD アプリケーション プロキシの Azure AD PowerShell の例](../application-proxy-powershell-samples.md)に関する記事を参照してください。
