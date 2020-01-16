---
title: Azure AD を使用してエンド ユーザーがアプリケーションに同意する方法を構成する
description: 組織のデータにアクセスするアプリケーションにユーザーがいつどのように同意できるかについて管理する方法を説明します。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd305d2943d1b12756171748f28d32300081d71
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443402"
---
# <a name="configure-how-end-users-consent-to-applications"></a>エンド ユーザーがアプリケーションに同意する方法を構成する

アプリケーションを Microsoft ID プラットフォームと統合することにより、ユーザーは Azure Active Directory (Azure AD) で職場または学校のアカウントを使用してサインインすることや、組織のデータにアクセスして豊富なデータドリブン エクスペリエンスを実現することができます。 異なるアクセス許可によって、ユーザーと組織のデータへの異なるレベルのアクセスをアプリケーションに対して許可できます。

既定では、一部のアクセス許可についてのみですが、ユーザーはアプリケーションが組織のデータにアクセスすることに同意できます。 たとえば、既定では、ユーザーは自分のメールボックスやユーザーが所有するチームの Teams でのメッセージ交換にアプリがアクセスすることには同意できますが、組織内のすべての SharePoint サイトの読み取りや書き込みのために、アプリが自動アクセスすることには同意できません。 ユーザーが自分で同意することを許可すると、Microsoft 365、Azure、その他のサービスと統合する便利なアプリケーションを簡単に入手できますが、慎重に使用および監視しないとリスクが発生する可能性があります。

Microsoft では、将来のユーザーの同意動作をすべて無効にし、攻撃の対象となる領域を減らし、このリスクを軽減することをお勧めしています。 ユーザーの同意を無効にした場合でも、以前の同意の許可は有効ですが、それより後のすべての同意操作は管理者が実行する必要があります。 テナント全体の管理者の同意は、統合された[管理者の同意要求ワークフロー](configure-admin-consent-workflow.md)または独自のサポート プロセスによりユーザーが要求できます。 詳細については、「[ID インフラストラクチャをセキュリティ保護する 5 つのステップ](../../security/fundamentals/steps-secure-identity.md)」を参照してください。

## <a name="configure-user-consent-to-applications"></a>アプリケーションに対するユーザーの同意を構成する
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Azure portal からユーザーの同意を無効または有効にする

Azure portal を使用して、組織のデータにアプリケーションがアクセスすることにユーザーが同意する機能を無効または有効にできます。

1. [Azure portal](https://portal.azure.com) に[グローバル管理者](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)としてサインインします。
2. **[Azure Active Directory]** 、 **[エンタープライズ アプリケーション]** 、 **[ユーザー設定]** の順に選択します。
3. **[ユーザーはアプリが自身の代わりに会社のデータにアクセスすることを許可できます]** というラベルのコントロールを使用して、ユーザーの同意を有効または無効にします。
4. (省略可能) [管理者の同意要求ワークフロー](configure-admin-consent-workflow.md)を構成して、アプリへの同意が許可されていないユーザーが承認を要求できるようにします。

> [!TIP]
> ユーザーの同意が許可されていないアプリケーションを管理者がレビューするようにユーザーが要求できるようにするには (たとえば、ユーザーの同意が無効になっているため、またはユーザーに付与することが許可されていないアクセス許可をアプリケーションが要求しているため)、[管理者の同意ワークフローを構成すること](configure-admin-consent-workflow.md)を検討します。

### <a name="disable-or-enable-user-consent-using-powershell"></a>PowerShell を使用してユーザーの同意を無効または有効にする

Azure AD PowerShell v1 モジュール ([MSOnline](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)) を使用して、組織のデータにアプリケーションがアクセスすることにユーザーが同意する機能を有効または無効にできます。

1. 次のコマンドレットを実行して、組織にサインインします。

    ```powershell
    Connect-MsolService
    ```

2. 次のコマンドレットを実行して、ユーザーの同意が有効になっているかどうかを確認します。

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. ユーザーの同意を有効または無効にします。 たとえば、ユーザーの同意を無効にするには、次のコマンドレットを実行します。

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>グループ データにアクセスするアプリに対するグループ所有者の同意を構成する

> [!IMPORTANT]
> 次の情報は、グループの所有者がそのグループのデータへのアクセス権をアプリケーションに付与できるようにする、今後リリースされる機能を対象としています。 この機能がリリースされた場合、この機能は既定で有効になります。 この機能はまだ広くリリースされていませんが、次の手順を使用することで、リリースの前に機能を無効にできます。

グループ所有者は、アプリケーション (たとえば、サードパーティ ベンダーによって発行されたアプリケーション) が、グループに関連付けられている組織のデータにアクセスすることを承認できます。 たとえば、チーム所有者 (チームの Office 365 グループの所有者) は、アプリがチーム内のすべての Teams メッセージを読み取ること、またはグループのメンバーの基本プロファイルを一覧表示することを許可できます。

> [!NOTE]
> この設定に関係なく、グループ所有者は常に他のユーザーまたはアプリをグループ所有者として直接追加できます。

### <a name="configure-group-owner-consent-using-powershell"></a>PowerShell を使用してグループ所有者の同意を構成する

Azure AD PowerShell プレビュー モジュール ([AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)) を使用して、グループ所有者が所有するグループに関連する組織のデータにアクセスするアプリケーションに対してグループ所有者が同意する機能を有効または無効にできます。

1. [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) モジュールを使用していることを確認します (この手順は、[AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) モジュールと [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) モジュールの両方がインストールされている場合に重要です)。

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Azure AD PowerShell に接続します。

   ```powershell
   Connect-AzureAD
   ```

3. テナントの*同意ポリシー設定*ディレクトリ設定の現在の値を取得します。 このために、この機能のディレクトリ設定が作成されているかどうかを確認する必要があります。作成されていない場合は、対応するディレクトリ設定テンプレートの値を使用します。

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

4. 設定値を理解します。 アプリがグループのデータにアクセスすることを許可できるユーザーを定義する設定値は、次の 2 つです。

    | 設定       | 種類         | [説明]  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Boolean |  グループ所有者がグループ固有のアクセス許可を付与できるかどうかを示すフラグです。 |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | _EnableGroupSpecificConsent_ が "True" に設定され、この値がグループのオブジェクト ID に設定されている場合、指定されているグループのメンバーは、所有しているグループにグループ固有のアクセス許可を付与する権限を与えられます。 |

5. 必要な構成の設定値を更新します。

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

6. 設定を保存します。

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="next-steps"></a>次のステップ

[管理者の同意ワークフローの構成](configure-admin-consent-workflow.md)

[アプリケーションに対してテナント全体の管理者の同意を付与する](grant-admin-consent.md)

[Microsoft ID プラットフォームでのアクセス許可と同意](../develop/active-directory-v2-scopes.md)

[StackOverflow での Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
