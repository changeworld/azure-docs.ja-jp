---
title: PowerShell サンプル - ポリシーを持つすべてのアプリケーション プロキシ アプリの一覧表示
description: ディレクトリ内の有効期間トークン ポリシーを持つすべての Azure Active Directory (Azure AD) アプリケーション プロキシ アプリケーションを一覧表示する PowerShell の例。
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
ms.openlocfilehash: c2ad7b769dc890917e5364fe57582acdfe16acac
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75475645"
---
# <a name="get-all-application-proxy-apps-with-a-token-lifetime-policy"></a>トークンの有効期間ポリシーを使用しているすべてのアプリケーション プロキシ アプリを取得する

この PowerShell スクリプトの例では、トークンの有効期間ポリシーが設定されているディレクトリ内のすべての Azure Active Directory (Azure AD) アプリケーション プロキシ アプリケーションを一覧表示し、ポリシーの詳細を一覧表示します。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

このサンプルには、[Graph 用 AzureAD V2 PowerShell モジュール プレビュー バージョン](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) が必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-with-policy.ps1 "Get all Application Proxy apps with a token lifetime policy")]

## <a name="script-explanation"></a>スクリプトの説明

| command | メモ |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | サービス プリンシパルを取得します。 |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | Azure AD アプリケーションを取得します。 |
|[Get-AzureADPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview) | Azure AD 内のポリシーを取得します。 |
|[Get-AzureADServicePrincipalPolicy](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview) | Azure AD 内のサービス プリンシパルのポリシーを取得します。 |


## <a name="next-steps"></a>次のステップ

Azure AD PowerShell モジュールの詳細については、「[Azure AD PowerShell モジュールの概要](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)」を参照してください。

アプリケーション プロキシのためのその他の PowerShell の例については、「[Azure AD アプリケーション プロキシのための Azure AD PowerShell の例](../application-proxy-powershell-samples.md)」を参照してください。