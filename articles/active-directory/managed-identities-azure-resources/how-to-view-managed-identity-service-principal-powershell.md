---
title: PowerShell を使用してマネージド ID のサービス プリンシパルを表示する方法
description: PowerShell を使用してマネージド ID のサービス プリンシパルを表示するための段階的手順。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: daveba
ms.openlocfilehash: 0ad3a52b837a5f79c9976c4c509e0a8516de1e7d
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714116"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>PowerShell を使用してマネージド ID のサービス プリンシパルを表示する

Azure リソースのマネージド ID は、Azure Active Directory で自動的に管理される ID を Azure サービスに提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、PowerShell を使用してマネージド ID のサービス プリンシパルを表示する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure リソースのマネージド ID の基本点な事柄については、[概要](overview.md)に関するセクションを参照してください。
- まだ Azure アカウントを持っていない場合は、[無料アカウントを新規登録](https://azure.microsoft.com/free/)してください。
- [仮想マシン](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity)または[アプリケーション](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity)でシステム割り当ての ID を有効にします。
- PowerShell をインストールしてローカルで使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 5.7.0 以降が必要になります。 バージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 
- PowerShell をローカルで実行している場合は、次の操作を行う必要もあります。 
    - `Login-AzureRmAccount` を実行して、Azure との接続を作成します。
    - [PowerShellGet の最新バージョン](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget)をインストールします。
    - `Install-Module -Name PowerShellGet -AllowPrerelease` を実行して `PowerShellGet` モジュールのプレリリース バージョンを取得します (`AzureRM.ManagedServiceIdentity` モジュールをインストールするには、このコマンドを実行した後に現在の PowerShell セッションから `Exit` することが必要になる場合があります)。
    - `Install-Module -Name AzureRM.ManagedServiceIdentity -AllowPrerelease` を実行して `AzureRM.ManagedServiceIdentity` モジュールのプレリリース バージョンをインストールして、この記事のユーザー割り当てマネージド ID 操作を実行します。

## <a name="view-the-service-principal"></a>サービス プリンシパルを表示する

次のコマンドは、システム割り当ての ID を有効にして VM またはアプリケーションのサービス プリンシパルを表示する方法を示しています。 `<VM or application name>` を独自の値に置き換えます。

```PowerShell
Get-AzureRmADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>次の手順

PowerShell を使用して Azure AD サービス プリンシパルを表示する方法の詳細については、[Get-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal) を参照してください。


