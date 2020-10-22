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
ms.date: 09/29/2020
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: f70a11165f6433e580fd857f2d5a620deb6640c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91604092"
---
# <a name="configure-token-lifetime-policies-preview"></a>トークンの有効期間ポリシーを構成する (プレビュー)
アプリ、サービス プリンシパル、および組織全体のトークンの有効期間を作成および管理できる場合は、Azure AD で多くのシナリオを実行できます。 詳細については、「[Microsoft ID プラットフォームでの構成可能なトークンの有効期間](active-directory-configurable-token-lifetimes.md)」を参照してください。 

> [!IMPORTANT]
> プレビュー中にお客様のご意見をお聞きした後、Azure AD の条件付きアクセスの[認証セッション管理機能](../conditional-access/howto-conditional-access-session-lifetime.md)を実装しました。 この新機能を使用し、サインインの頻度を設定して更新トークンの有効期間を構成できます。 2020 年 5 月 30 日以降、新しいテナントでは、構成可能なトークンの有効期間ポリシーを使用してセッションと更新トークンを構成できなくなります。 廃止はその数か月後に行われます。つまり、セッションと更新トークンに関する既存のポリシーは適用されなくなります。 非推奨となった後も、アクセス トークンの有効期間を構成することはできます。


このセクションでは新しい規則を適用する場合に役立つ、いくつかの一般的なポリシー シナリオについて説明します。

* トークンの有効期間
* トークンの最大非アクティブ時間
* トークンの最長有効期間

例では、次の方法を説明します。

* 組織の既定のポリシーを管理する
* Web サインインのポリシーを作成する
* Web API を呼び出すネイティブ アプリケーションのポリシーを作成する
* 詳細なポリシーを管理する

## <a name="prerequisites"></a>前提条件
次の例では、アプリ、サービス プリンシパル、および組織全体のポリシーを作成、更新、リンク、および削除します。 Azure AD に慣れていない場合は、これらの例を続行する前に、[Azure AD テナントを取得する方法](quickstart-create-new-tenant.md)について学習することをお勧めします。  

使用を開始するには、次の手順を実行します。

1. 最新版の [Azure AD PowerShell モジュール パブリック プレビュー リリース](https://www.powershellgallery.com/packages/AzureADPreview)をダウンロードします。
2. `Connect` コマンドを実行して、Azure AD 管理者アカウントにサインインします。 新しいセッションを開始するたびにこのコマンドを実行します。

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. 組織に作成されているすべてのポリシーを表示するには、次のコマンドを実行します。 このコマンドは、次のシナリオでほとんどの操作の後に実行します。 コマンドの実行は、ポリシーの ** ** を取得する場合にも役立ちます。

    ```powershell
    Get-AzureADPolicy
    ```

## <a name="manage-an-organizations-default-policy"></a>組織の既定のポリシーを管理する
この例では、組織全体でユーザーがサインインする頻度を少なくするポリシーを作成します。 そのためには、組織全体に適用される単一要素の更新トークンに対してトークンの有効期間ポリシーを作成します。 このポリシーは、組織内のすべてのアプリケーションと、ポリシーがまだ設定されていない各サービス プリンシパルに適用されます。

1. トークンの有効期間ポリシーを作成します。

    1. 単一要素の更新トークンを "until-revoked" に設定します。 トークンは、アクセスが取り消されるまで期限切れになりません。 次のポリシー定義を作成します。

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. ポリシーを作成するには、[New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. 空白を削除するには、[Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. 新しいポリシーを表示し、ポリシーの **ObjectId** を取得するには、次のコマンドを実行します。

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. ポリシーを更新します。

    この例で設定する最初のポリシーは、サービスに求められるほど厳密にしないようにすることもできます。 単一要素の更新トークンを 2 日で期限が切れるように設定するには、次のコマンドを実行します。

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

## <a name="create-a-policy-for-web-sign-in"></a>Web サインインのポリシーを作成する

この例では、ユーザーが、Web アプリで頻繁に認証を必要とするポリシーを作成します。 このポリシーは、アクセス トークンと ID トークンの有効期間と、多要素セッション トークンの最長有効期間を Web アプリのサービス プリンシパルに設定します。

1. トークンの有効期間ポリシーを作成します。

    この Web サインイン用のポリシーでは、アクセス トークンと ID トークンの有効期間と、単一要素セッション トークンの最長有効期間を 2 時間に設定します。

    1. ポリシーを作成するには、[New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. 新しいポリシーを表示して、そのポリシーの **ObjectId** を取得するには、[Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. サービス プリンシパルにポリシーを割り当てます。 サービス プリンシパルの **ObjectId** も取得する必要があります。

    1. [Get-azureadserviceprincipal](/powershell/module/azuread/get-azureadserviceprincipal) コマンドレットを使用して、組織のすべてのサービス プリンシパルまたは 1 つのサービス プリンシパルを表示します。
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. サービス プリンシパルがある場合は、[Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

## <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Web API を呼び出すネイティブ アプリケーションのポリシーを作成する
この例では、ユーザーに必要とする認証の頻度を少なくするポリシーを作成します。 ポリシーにより、ユーザーが再認証を必要とするまでの非アクティブでいられる時間も長くなります。 ポリシーは、Web API に適用されます。 ネイティブ アプリがリソースとして Web API を要求すると、このポリシーが適用されます。

1. トークンの有効期間ポリシーを作成します。

    1. Web API に対して厳密なポリシーを作成するには、[New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. 新しいポリシーを表示するには、次のコマンドを実行します。

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Web API にポリシーを割り当てます。 アプリケーションの **ObjectId** を取得する必要もあります。 [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) コマンドレットを使用して、アプリの **ObjectId** を見つけるか、[Azure portal](https://portal.azure.com/) を使用します。

    アプリの **ObjectId** を取得し、ポリシーを割り当てます。

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

## <a name="manage-an-advanced-policy"></a>詳細なポリシーを管理する
この例では、いくつかのポリシーを作成して、優先度システムのしくみを説明します。 複数のオブジェクトに適用される複数のポリシーを管理する方法も説明します。

1. トークンの有効期間ポリシーを作成します。

    1. 単一要素の更新トークンの有効期間を 30 日間に設定する組織の既定のポリシーを作成するには、[New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. 新しいポリシーを表示するには、[Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを使用します。

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. サービス プリンシパルにポリシーを割り当てます。

    これで、組織全体に適用されるポリシーが得られます。 特定のサービス プリンシパルに対してはこの 30 日間のポリシーを保持しますが、組織の既定のポリシーを "until-revoked" の上限となるよう変更するとします。

    1. 組織のすべてのサービス プリンシパルを表示するには、[Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) コマンドレットを使用します。

    1. サービス プリンシパルがある場合は、[Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) コマンドレットを実行します。

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. `IsOrganizationDefault` フラグを false に設定します。

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. 新しい組織の既定のポリシーを作成します。

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    これで、元のポリシーがサービス プリンシパルにリンクされ、新しいポリシーが組織の既定のポリシーとして設定されます。 サービス プリンシパルに適用されるポリシーが、組織の既定のポリシーよりも優先されることに注意してください。

## <a name="next-steps"></a>次のステップ
Azure AD の条件付きアクセスにおける[認証セッションの管理機能](../conditional-access/howto-conditional-access-session-lifetime.md)について学習する。