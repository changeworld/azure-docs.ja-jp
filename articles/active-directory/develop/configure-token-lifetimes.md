---
title: トークンの有効期間を設定する
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォームによって発行されたトークンの有効期間を設定する方法について説明します。 組織の既定のポリシーを管理する方法、Web サインインのポリシーを作成する方法、Web API を呼び出すネイティブ アプリのポリシーを作成する方法、および高度なポリシーを管理する方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 66e9817c6d3bbcd199418b9afd78eda016c5f291
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363888"
---
# <a name="configure-token-lifetime-policies-preview"></a>トークンの有効期間ポリシーを構成する (プレビュー)
Microsoft ID プラットフォームによって発行されたアクセス トークン、SAML トークン、または ID トークンの有効期間を指定できます。 組織のすべてのアプリ、マルチテナント (複数の組織) アプリケーション、または組織の特定のサービス プリンシパルに対して、トークンの有効期間を設定できます。 詳細については、[構成可能なトークンの有効期間](active-directory-configurable-token-lifetimes.md)に関する記事を参照してください。

このセクションでは、トークンの有効期間に新しいルールを適用する場合に役立つ、一般的なポリシー シナリオについて説明します。 この例では、Web アプリでユーザーによる頻繁な認証を必須とするポリシーを作成する方法を説明します。

## <a name="get-started"></a>はじめに

はじめに、最新版の [Azure AD PowerShell モジュール パブリック プレビュー リリース](https://www.powershellgallery.com/packages/AzureADPreview)をダウンロードします。

次に、`Connect` コマンドを実行して、Azure AD 管理者アカウントにサインインします。 新しいセッションを開始するたびにこのコマンドを実行します。

```powershell
Connect-AzureAD -Confirm
```

## <a name="create-a-policy-for-web-sign-in"></a>Web サインインのポリシーを作成する

この例では、ユーザーが、Web アプリで頻繁に認証を必要とするポリシーを作成します。 このポリシーにより、Web アプリのサービス プリンシパルへのアクセス/ID トークンの有効期間が設定されます。

1. トークンの有効期間ポリシーを作成します。

    このポリシーは、Web サインインの場合、アクセス/ID トークンの有効期間を 2 時間に設定します。

    ポリシーを作成するには、[New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。

    ```powershell
    $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
    ```

    新しいポリシーを表示して、そのポリシーの **ObjectId** を取得するには、[Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。

    ```powershell
    Get-AzureADPolicy -Id $policy.Id
    ```

1. サービス プリンシパルにポリシーを割り当てます。 サービス プリンシパルの **ObjectId** も取得する必要があります。

    [Get-azureadserviceprincipal](/powershell/module/azuread/get-azureadserviceprincipal) コマンドレットを使用して、組織のすべてのサービス プリンシパルまたは 1 つのサービス プリンシパルを表示します。

    ```powershell
    # Get ID of the service principal
    $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
    ```

    サービス プリンシパルがある場合は、[Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。

    ```powershell
    # Assign policy to a service principal
    Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
    ```

## <a name="view-existing-policies-in-a-tenant"></a>テナント内の既存のポリシーを表示する

組織で作成されたすべてのポリシーを表示するには、[Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。  上記の既定値と異なる定義されたプロパティ値が含まれる結果は、廃止の範囲内にあります。

```powershell
Get-AzureADPolicy -All $true
```

特定のポリシーにリンクされているアプリとサービス プリンシパルを確認するには、**1a37dad8-5da7-4cc8-87c7-efbc0326cf20** を独自のポリシー ID に置き換えて、次の [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。 その後、条件付きアクセスのサインイン頻度を構成するか、Azure AD の既定値をそのまま使用するかを決定できます。

```powershell
Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
```

更新およびセッション トークン構成プロパティのカスタム値を定義するポリシーがテナントにある場合、Microsoft は、これらのポリシーを上記の既定値を反映する値に更新することをお勧めします。 変更が加えられていない場合、Azure AD によって自動的に既定値が使用されます。

### <a name="troubleshooting"></a>トラブルシューティング
`Get-AzureADPolicy` コマンドレットの実行後に、`Get-AzureADPolicy : The term 'Get-AzureADPolicy' is not recognized` エラーが発生することが報告されています。 回避策として、次を実行して AzureAD モジュールをアンインストール/再インストールしてから、AzureADPreview モジュールをインストールします。

```powershell
# Uninstall the AzureAD Module
UnInstall-Module AzureAD

# Re-install the AzureAD Module
Install-Module AzureAD

# Install the AzureAD Preview Module adding the -AllowClobber
Install-Module AzureADPreview -AllowClobber

Connect-AzureAD
Get-AzureADPolicy -All $true
```

## <a name="next-steps"></a>次のステップ
Azure AD の条件付きアクセスにおける[認証セッションの管理機能](../conditional-access/howto-conditional-access-session-lifetime.md)について学習する。
