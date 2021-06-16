---
title: PowerShell のサンプル - Azure Active Directory テナントで、間もなく期限が切れるシークレットと証明書を含むアプリをエクスポートします。
description: Azure Active Directory テナントで指定したアプリについて、間もなく期限が切れるシークレットと証明書を含むすべてのアプリをエクスポートする PowerShell の例。
services: active-directory
author: mtillman
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: mtillman
ms.reviewer: mifarca
ms.openlocfilehash: 38b7b424e23c196f89004b443b8945d6f7bfe44b
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112076782"
---
# <a name="export-apps-with-expiring-secrets-and-certificates"></a>間もなく期限が切れるシークレットと証明書を含むアプリをエクスポートする

この PowerShell スクリプトの例では、指定したアプリについて、間もなく期限が切れるシークレット、証明書、およびそれらの所有者を含むすべてのアプリ登録をディレクトリから CSV ファイルにエクスポートします。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

このサンプルでは、[AzureAD V2 PowerShell for Graph モジュール](/powershell/azure/active-directory/install-adv2) (AzureAD)、または[AzureAD V2 PowerShell for Graph モジュール プレビュー バージョン](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true) (AzureADPreview) が必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-apps-with-expiring-secrets.ps1 "Exports all apps with expiring secrets and certificates for the specified apps in your directory.")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、変更せずに直接使用できます。 管理者には、有効期限と、既に有効期限が切れているシークレットまたは証明書を表示するかどうかを確認するメッセージが表示されます。

"Add-Member" コマンドは、CSV ファイル内に列を作成する役割を担います。
"New-Object" コマンドを使用すると、CSV ファイル エクスポートの列に使用されるオブジェクトが作成されます。
エクスポートを非対話的に行う場合は、PowerShell で CSV ファイル パスを使用して "$Path" 変数を直接変更できます。

| コマンド | Notes |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0&preserve-view=true) | ディレクトリからアプリケーションを取得します。 |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner?view=azureadps-2.0&preserve-view=true) | ディレクトリからアプリケーションの所有者を取得します。 |

## <a name="next-steps"></a>次のステップ

Azure AD PowerShell モジュールの詳細については、[Azure AD PowerShell モジュールの概要](/powershell/azure/active-directory/overview)に関する記事を参照してください。

アプリケーション管理のためのその他の PowerShell の例については、[アプリケーション管理のための Azure AD PowerShell の例](../app-management-powershell-samples.md)に関する記事を参照してください。
