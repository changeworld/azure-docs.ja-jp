---
title: PowerShell サンプル - 同じ証明書を使用している Azure Active Directory アプリケーション プロキシ アプリ
description: 同じ証明書を使用して公開されたすべての Azure Active Directory (Azure AD) アプリケーション プロキシ アプリケーションを一覧表示する PowerShell の例。
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: sample
ms.date: 04/29/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 319c97b95a3908d0f7f3d0f9c8813b105c188647
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988291"
---
# <a name="get-all-azure-active-directory-application-proxy-apps-that-are-published-with-the-identical-certificate"></a>同じ証明書を使用して公開されているすべての Azure Active Directory アプリケーション プロキシ アプリを取得する

この PowerShell スクリプトの例では、同じ証明書を使用して公開されたすべての Azure Active Directory (Azure AD) アプリケーション プロキシ アプリケーションを一覧表示します。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

このサンプルでは、[AzureAD V2 PowerShell for Graph モジュール](/powershell/azure/active-directory/install-adv2) (AzureAD)、または[AzureAD V2 PowerShell for Graph モジュール プレビュー バージョン](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview) が必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-custom-domain-identical-cert.ps1 "Get all Azure AD Proxy application apps published with the identical certificate")]

## <a name="script-explanation"></a>スクリプトの説明

| command | メモ |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | サービス プリンシパルを取得します。 |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Azure AD アプリケーションを取得します。 |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Azure AD でアプリケーション プロキシ用に構成されたアプリケーションを取得します。 |

## <a name="next-steps"></a>次のステップ

Azure AD PowerShell モジュールの詳細については、[Azure AD PowerShell モジュールの概要](/powershell/azure/active-directory/overview)に関する記事を参照してください。

アプリケーション プロキシのその他の PowerShell の例については、[Azure AD アプリケーション プロキシの Azure AD PowerShell の例](../application-proxy-powershell-samples.md)に関する記事を参照してください。
