---
title: PowerShell のサンプル - Azure Active Directory テナントでのアプリの登録用にシークレットと証明書をエクスポートします。
description: Azure Active Directory テナントで指定したアプリの登録用にすべてのシークレットと証明書をエクスポートする PowerShell の例。
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: iangithinji
ms.reviewer: mifarca
ms.openlocfilehash: b5cbb6b3843e81d9265405dcea24a092e57bf65e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377045"
---
# <a name="export-secrets-and-certificates-for-app-registrations"></a>アプリの登録用にシークレットと証明書をエクスポートする

この PowerShell スクリプトの例では、指定したアプリの登録用にすべてのシークレットと証明書をディレクトリから CSV ファイルにエクスポートします。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

このサンプルでは、[AzureAD V2 PowerShell for Graph モジュール](/powershell/azure/active-directory/install-adv2) (AzureAD)、または[AzureAD V2 PowerShell for Graph モジュール プレビュー バージョン](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview) が必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-app-registrations-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified app registrations in your directory.")]

## <a name="script-explanation"></a>スクリプトの説明

"Add-Member" コマンドは、CSV ファイル内に列を作成する役割を担います。
エクスポートを非対話的に行う場合は、PowerShell で CSV ファイル パスを使用して "$Path" 変数を直接変更できます。

| コマンド | Notes |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | ディレクトリからアプリケーションを取得します。 |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner) | ディレクトリからアプリケーションの所有者を取得します。 |

## <a name="next-steps"></a>次のステップ

Azure AD PowerShell モジュールの詳細については、[Azure AD PowerShell モジュールの概要](/powershell/azure/active-directory/overview)に関する記事を参照してください。

アプリケーション管理のためのその他の PowerShell の例については、[アプリケーション管理のための Azure AD PowerShell の例](../app-management-powershell-samples.md)に関する記事を参照してください。
