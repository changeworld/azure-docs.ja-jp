---
title: PowerShell サンプル - ワイルドカードを使用しているアプリケーション プロキシ アプリの一覧を表示する
description: ワイルドカードを使用しているすべての Azure Active Directory (Azure AD) アプリケーション プロキシ アプリケーションの一覧を表示する PowerShell の例。
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
ms.openlocfilehash: 8149e9230de3e8b6c18d150c81ea67b1d861746c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475613"
---
# <a name="get-all-application-proxy-apps-using-wildcard-publishing"></a>ワイルドカードによる発行を使用しているすべてのアプリケーション プロキシ アプリを取得する

この PowerShell スクリプトの例では、ワイルドカードによる発行を使用しているすべての Azure Active Directory (Azure AD) アプリケーション プロキシ アプリケーションの一覧を表示します。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

このサンプルには、[Graph 用 AzureAD V2 PowerShell モジュール](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) (AzureAD) または [Graph 用 AzureAD V2 PowerShell モジュール プレビュー バージョン](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) が必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-wildcard-apps.ps1 "Get all Application Proxy apps using wildcards")]

## <a name="script-explanation"></a>スクリプトの説明

| command | メモ |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | サービス プリンシパルを取得します。 |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Azure AD アプリケーションを取得します。 |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | Azure AD でアプリケーション プロキシ用に構成されたアプリケーションを取得します。 |

## <a name="next-steps"></a>次のステップ

Azure AD PowerShell モジュールの詳細については、「[Azure AD PowerShell モジュールの概要](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)」を参照してください。

アプリケーション プロキシのためのその他の PowerShell の例については、「[Azure AD アプリケーション プロキシのための Azure AD PowerShell の例](../application-proxy-powershell-samples.md)」を参照してください。