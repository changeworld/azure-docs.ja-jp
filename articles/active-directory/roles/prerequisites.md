---
title: Azure AD ロール用に PowerShell または Graph エクスプローラーを使用するための前提条件 - Azure Active Directory
description: Azure Active Directory ロール用に PowerShell または Graph エクスプローラーを使用するための前提条件
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 08/06/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef457948a28d9d07464b96bfaea4df6a6f2a8723
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747855"
---
# <a name="prerequisites-to-use-powershell-or-graph-explorer-for-azure-ad-roles"></a>Azure AD ロール用に PowerShell または Graph エクスプローラーを使用するための前提条件

PowerShell または Graph エクスプローラーを使用して Azure Active Directory (Azure AD) ロールを管理する場合は、必要な前提条件が満たされている必要があります。 この記事では、さまざまな Azure AD ロール機能のための PowerShell と Graph エクスプローラーの前提条件について説明します。

## <a name="azuread-module"></a>AzureAD モジュール

次を実行するための PowerShell コマンドを使用するには、

- ロールの割り当ての一覧表示
- ロールを割り当て可能なグループを作成する
- 管理単位の管理

以下のモジュールがインストールされている必要があります。

- [AzureAD](https://www.powershellgallery.com/packages/AzureAD) バージョン 2.0.2.137 以降


#### <a name="check-azuread-version"></a>AzureAD バージョンを確認する

インストールされている AzureAD のバージョンを確認するには、[Get-InstalledModule](/powershell/module/powershellget/get-installedmodule) を使用します。

```powershell
Get-InstalledModule -Name AzureAD
```

次のような出力が表示されます。

```powershell
Version    Name                                Repository           Description
-------    ----                                ----------           -----------
2.0.2.137  AzureAD                             PSGallery            Azure Active Directory V2 General Availability M...
```

#### <a name="install-azuread"></a>AzureAD のインストール

AzureAD をインストールしていない場合は、[Install-Module](/powershell/module/powershellget/install-module) を使用して、AzureAD をインストールします。

```powershell
Install-Module -Name AzureAD
```

#### <a name="update-azuread"></a>AzureAD を更新する

AzureAD を最新バージョンに更新するには、[Install-Module](/powershell/module/powershellget/install-module) を再実行します。

```powershell
Install-Module -Name AzureAD
```

#### <a name="use-azuread"></a>AzureAD を使用する

AzureAD を使用するには、次の手順に従って、現在のセッションにインポートされていることを確認してください。

1. AzureAD がメモリに読み込まれているかどうかを確認するには、[Get-Module](/powershell/module/microsoft.powershell.core/get-module) を使用します。

    ```powershell
    Get-Module -Name AzureAD
    ```

1. 前の手順で出力が表示されない場合は、[Import-Module](/powershell/module/microsoft.powershell.core/import-module) を使用して AzureAD をインポートします。 `-Force` パラメーターは、読み込まれたモジュールを削除し、再度インポートします。

    ```powershell
    Import-Module -Name AzureAD -Force
    ```

1. [Get-Module](/powershell/module/microsoft.powershell.core/get-module) をもう一度実行してください。

    ```powershell
    Get-Module -Name AzureAD
    ```

    次のような出力が表示されます。
    
    ```powershell
    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Binary     2.0.2.137  AzureAD                             {Add-AzureADApplicationOwner, Add-AzureADDeviceRegisteredO...
    ```

## <a name="azureadpreview-module"></a>AzureADPreview モジュール

次を実行するための PowerShell コマンドを使用するには、

- ユーザーまたはグループにロールを割り当てる
- ロールの割り当てを削除する
- Privileged Identity Management を使用してグループをロールに適合するようにする
- カスタム ロールを作成する

以下のモジュールがインストールされている必要があります。

- [AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview) バージョン 2.0.2.138 以降


#### <a name="check-azureadpreview-version"></a>AzureADPreview バージョンの確認

インストールされている AzureADPreview のバージョンを確認するには、[Get-InstalledModule](/powershell/module/powershellget/get-installedmodule) を使用します。

```powershell
Get-InstalledModule -Name AzureADPreview
```

次のような出力が表示されます。

```powershell
Version    Name                                Repository           Description
-------    ----                                ----------           -----------
2.0.2.138  AzureADPreview                      PSGallery            Azure Active Directory V2 Preview Module. ...
```

#### <a name="install-azureadpreview"></a>AzureADPreview のインストール

AzureADPreview をインストールしていない場合は、[Install-Module](/powershell/module/powershellget/install-module) を使用して、AzureADPreview をインストールします。

```powershell
Install-Module -Name AzureADPreview
```

#### <a name="update-azureadpreview"></a>AzureADPreview を更新する

AzureADPreview を最新バージョンに更新するには、[Install-Module](/powershell/module/powershellget/install-module) を再実行します。

```powershell
Install-Module -Name AzureADPreview
```

#### <a name="use-azureadpreview"></a>AzureADPreview を使用する

AzureADPreview を使用するには、次の手順に従って、現在のセッションにインポートされていることを確認してください。

1. AzureADPreview がメモリに読み込まれているかどうかを確認するには、[Get-Module](/powershell/module/microsoft.powershell.core/get-module) を使用します。

    ```powershell
    Get-Module -Name AzureADPreview
    ```

1. 前の手順で出力が表示されない場合は、[Import-Module](/powershell/module/microsoft.powershell.core/import-module) を使用して AzureADPreview をインポートします。 `-Force` パラメーターは、読み込まれたモジュールを削除し、再度インポートします。

    ```powershell
    Import-Module -Name AzureADPreview -Force
    ```

1. [Get-Module](/powershell/module/microsoft.powershell.core/get-module) をもう一度実行してください。

    ```powershell
    Get-Module -Name AzureADPreview
    ```

    次のような出力が表示されます。
    
    ```powershell
    ModuleType Version    Name                                ExportedCommands
    ---------- -------    ----                                ----------------
    Binary     2.0.2.138  AzureADPreview                      {Add-AzureADAdministrativeUnitMember, Add-AzureADApplicati...
    ```

## <a name="graph-explorer"></a>グラフ エクスプローラー

[Microsoft Graph API](/graph/overview) と [Graph エクスプローラー](/graph/graph-explorer/graph-explorer-overview)を使用して Azure AD ロールを管理するには、次の操作を行う必要があります。

1. [Azure portal](https://portal.azure.com) または [Azure AD 管理センター](https://aad.portal.azure.com)にサインインします。

1. **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** の順に選択します。

1. アプリケーションの一覧で、 **[Graph エクスプローラー]** を見つけて選択します。

1. **[アクセス許可]** を選択します。

1. **[Graph エクスプローラーに管理者の同意を与えます]** を選択します。

    ![[Graph エクスプローラーに管理者の同意を与えます] リンクを示すスクリーンショット。](./media/prerequisites/select-graph-explorer.png)

1. [Graph エクスプローラー ツール](https://aka.ms/ge)を使用します。

## <a name="next-steps"></a>次のステップ

- [Graph 用 Azure Active Directory PowerShell のインストール](/powershell/azure/active-directory/install-adv2)
- [AzureAD モジュールのドキュメント](/powershell/module/azuread/)
- [グラフ エクスプローラー](/graph/graph-explorer/graph-explorer-overview)
