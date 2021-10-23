---
title: PowerShell サンプル - Azure Active Directory アプリケーション プロキシ アプリの拡張情報を一覧表示する
description: アプリケーション ID (AppId)、名前 (DisplayName)、外部 URL (ExternalUrl)、内部 URL (InternalUrl)、認証の種類 (ExternalAuthenticationType) と共にすべての Azure Active Directory (Azure AD) アプリケーション プロキシ アプリケーションを一覧表示する PowerShell の例。
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
ms.openlocfilehash: 16fd5ac821f09363249f904b14ac836b063e1b25
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988128"
---
# <a name="get-all-application-proxy-apps-and-list-extended-information"></a>すべてのアプリケーション プロキシ アプリを取得し、拡張情報を一覧表示します

この PowerShell スクリプトの例では、アプリケーション ID (AppId)、名前 (DisplayName)、外部 URL (ExternalUrl)、内部 URL (InternalUrl)、認証の種類 (ExternalAuthenticationType)、SSO モード、各種設定など、すべての Azure Active Directory (Azure AD) アプリケーション プロキシ アプリケーションに関する情報を一覧表示します。

$ssoMode 変数の値を変更すると、SSO モードによってフィルター処理された出力が有効になります。 さらに詳しい情報は、スクリプト内に記載されています。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

このサンプルには、[Graph 用 AzureAD V2 PowerShell モジュール](/powershell/azure/active-directory/install-adv2) (AzureAD) が必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-appproxy-apps-extended.ps1 "Get all Application Proxy apps")]

## <a name="script-explanation"></a>スクリプトの説明

| command | メモ |
|---|---|
|[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) | サービス プリンシパルを取得します。 |
|[Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | Azure AD アプリケーションを取得します。 |
|[Get-AzureADApplicationProxyApplication](/powershell/module/azuread/get-azureadapplicationproxyapplication) | Azure AD でアプリケーション プロキシ用に構成されたアプリケーションを取得します。 |

## <a name="next-steps"></a>次のステップ

Azure AD PowerShell モジュールの詳細については、[Azure AD PowerShell モジュールの概要](/powershell/azure/active-directory/overview)に関する記事を参照してください。

アプリケーション プロキシのその他の PowerShell の例については、[Azure AD アプリケーション プロキシの Azure AD PowerShell の例](../application-proxy-powershell-samples.md)に関する記事を参照してください。
