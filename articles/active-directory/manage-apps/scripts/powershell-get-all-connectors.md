---
title: PowerShell サンプル - すべてのアプリケーション プロキシ コネクタ グループの一覧表示
description: ディレクトリ内のすべての Azure Active Directory (Azure AD) アプリケーション プロキシ コネクタ グループとコネクタを一覧表示する PowerShell の例。
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
ms.openlocfilehash: 544bdf1c4f9e000c79241418f5e6adefbc1b6121
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2020
ms.locfileid: "96858622"
---
# <a name="get-all-connector-groups-and-connectors-in-the-directory"></a>ディレクトリ内のすべてのコネクタ グループとコネクタを取得する

この PowerShell スクリプトの例では、ディレクトリ内のすべての Azure Active Directory (Azure AD) アプリケーション プロキシ コネクタ グループとコネクタを一覧表示します。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

このサンプルでは、[AzureAD V2 PowerShell for Graph モジュール](/powershell/azure/active-directory/install-adv2) (AzureAD)、または[AzureAD V2 PowerShell for Graph モジュール プレビュー バージョン](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview) (AzureADPreview) が必要です。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-connectors.ps1 "Get all connector groups and connectors in the directory")]

## <a name="script-explanation"></a>スクリプトの説明

| command | メモ |
|---|---|
| [Get-AzureADApplicationProxyConnectorGroup](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroup) | すべてのコネクタ グループの一覧を取得するか、指定した場合には、指定されたコネクタ グループの詳細を取得します。 |
| [Get-AzureADApplicationProxyConnectorGroupMembers](/powershell/module/azuread/get-azureadapplicationproxyconnectorgroupmembers) | 各コネクタ グループに関連付けられているすべてのアプリケーション プロキシ コネクタを取得します。|

## <a name="next-steps"></a>次のステップ

Azure AD PowerShell モジュールの詳細については、[Azure AD PowerShell モジュールの概要](/powershell/azure/active-directory/overview)に関する記事を参照してください。

アプリケーション プロキシのその他の PowerShell の例については、[Azure AD アプリケーション プロキシの Azure AD PowerShell の例](../application-proxy-powershell-samples.md)に関する記事を参照してください。
