---
title: ホーム領域検出を使用してサインイン自動高速化を構成する
titleSuffix: Azure AD
description: 自動高速化やドメインのヒントを含む、フェデレーション ユーザーのための Azure Active Directory 認証のホーム領域検出ポリシーを構成する方法について説明します。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/13/2021
ms.author: davidmu
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.reviewer: hirsin
ms.openlocfilehash: c0d182f2e0eb4fc3efe21be1c0a4e6111ad8de39
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555492"
---
# <a name="configure-sign-in-behavior"></a>サインイン動作を構成する

この記事では、ホーム領域検出 (HRD) ポリシーを使用してフェデレーション ユーザーのために Azure Active Directory (Azure AD) 認証の動作を構成することの概要を示します。  自動高速化を使用して、ユーザー名入力画面をスキップし、自動的にフェデレーション ログイン エンドポイントにユーザーを転送することを取り上げます。  FIDO (First Identity Online) などのより強力な認証方法を使用できない可能性があり、コラボレーションの妨げとなるため、Microsoft では自動高速化を構成することは、もはやお勧めしていません。

## <a name="prerequisites"></a>前提条件

Azure AD でアプリケーションのために HRD ポリシーを構成するには、以下が必要です。

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 次のいずれかのロール: グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者。
- Azure AD PowerShell コマンドレットの最新のプレビュー。

## <a name="set-up-an-hrd-policy-on-an-application"></a>アプリケーションに HRD ポリシーを設定する

Azure AD PowerShell コマンドレットを使って次のようなシナリオを設定する手順を示します。

- 1 つのフェデレーション ドメインを持つテナント内でアプリケーションの自動高速化を実行するように HRD ポリシーを設定する。

- テナント用に確認された複数のドメインのいずれかに対してアプリケーションの自動高速化を実行するように HRD ポリシーを設定する。

- フェデレーション ユーザー用の Azure AD に対して、レガシ アプリケーションがユーザー名とパスワードの認証を直接実行できるように HRD ポリシーを設定する。

- ポリシーが構成されているアプリケーションを一覧表示する。

以下の例では、Azure AD 内のアプリケーション サービス プリンシパルを対象に、ポリシーを作成、更新、リンク、および削除します。

1. 開始する前に、Connect コマンドを実行して、管理者アカウントで Azure AD にサインインします。

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. 次のコマンドを実行して、組織内のすべてのポリシーを表示します。

    ```powershell
    Get-AzureADPolicy
    ```

何も返されない場合は、テナント内にポリシーが作成されていないことを意味します。

この例では、アプリケーションに割り当てられたときに以下を行うポリシーを作成します。

- テナント内のドメインが 1 つのみの場合、ユーザーがアプリケーションにサインインするときに、AD FS サインイン画面への移動を自動高速化する。
- テナント内にフェデレーション ドメインが複数ある場合に、AD FS サインイン画面への移動を自動高速化する。
- ポリシー割り当て先のアプリケーションのフェデレーション ユーザー用の Azure AD に対して、ユーザー名とパスワードによる非対話型のサインインを直接行えるようにする。

## <a name="create-an-hrd-policy"></a>HRD ポリシーを作成する

以下のポリシーは、テナント内のドメインが 1 つのみの場合、ユーザーがアプリケーションにサインインするときに、AD FS サインイン画面への移動を自動高速化します。

```powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

次のポリシーは、テナント内にフェデレーション ドメインが複数あるときに、AD FS サインイン画面への移動を自動高速化します。 アプリケーションのユーザーを認証するフェデレーション ドメインが複数ある場合は、自動高速化するドメインを指定する必要があります。

```powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

特定のアプリケーション用の Azure AD に対し、ユーザー名とパスワードを使用してフェデレーション ユーザーを直接認証できるようにするポリシーを作成するには、次のコマンドを実行します。

```powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```

新しいポリシーを表示し、その **ObjectId** を取得するには、次のコマンドを実行します。

```powershell
Get-AzureADPolicy
```

作成した HRD ポリシーを適用するには、複数のアプリケーション サービス プリンシパルに HRD ポリシーを割り当てる必要があります。

## <a name="locate-the-service-principal-to-which-to-assign-the-policy"></a>ポリシーを割り当てるサービス プリンシパルを見つける

ポリシーを割り当てるサービス プリンシパルの **ObjectID** が必要となります。 サービス プリンシパルの **ObjectID** を検索するには、複数の方法があります。

[Azure portal](https://portal.azure.com) を使用することも、[Microsoft Graph](/graph/api/resources/serviceprincipal) のクエリを実行することもできます。 また、[Graph エクスプローラー ツール](https://developer.microsoft.com/graph/graph-explorer)に移動して Azure AD アカウントにサインインし、組織のすべてのサービス プリンシパルを表示することもできます。

PowerShell を使用しているため、次のコマンドレットを使用してサービス プリンシパルとその ID を一覧表示できます。

```powershell
Get-AzureADServicePrincipal
```

## <a name="assign-the-policy-to-your-service-principal"></a>サービス プリンシパルにポリシーを割り当てる

自動高速化を構成するアプリケーションのサービス プリンシパルの **ObjectID** を入手した後、次のコマンドを実行します。 このコマンドは、手順 1 で作成した HRD ポリシーを、手順 2 で取得したサービス プリンシパルに関連付けます。

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

このコマンドは、ポリシーを追加する各サービス プリンシパルに対して繰り返し実行できます。

アプリケーションに HomeRealmDiscovery ポリシーが既に割り当てられている場合は、さらにポリシーを追加することはできません。  その場合は、アプリケーションに割り当てられているホーム領域検出ポリシーの定義を変更して、別のパラメーターを追加します。

### <a name="check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>HRD ポリシーが割り当てられているアプリケーション サービス プリンシパルを確認する

HRD ポリシーがどのアプリケーションに構成されているかを確認するには、**Get-AzureADPolicyAppliedObject** コマンドレットを使用します。 そして、確認するポリシーの **ObjectID** を渡します。

```powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>

Try the application to check that the new policy is working.

### List the applications for which HRD policy is configured

1. List all policies that were created in your organization

```powershell
Get-AzureADPolicy
```

割り当てを一覧表示するポリシーの **ObjectID** をメモします。

2. ポリシーが割り当てられているサービス プリンシパルを一覧表示する

```powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="remove-an-hrd-policy-from-an-application"></a>アプリケーションから HRD ポリシーを削除する

1. ObjectID を取得する

先述の例を使って、ポリシーの **ObjectID** と、ポリシーを削除するアプリケーション サービス プリンシパルのオブジェクト ID を取得します。

2. アプリケーション サービス プリンシパルからポリシーの割り当てを削除する

```powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

3. ポリシーが割り当てられているサービス プリンシパルを一覧表示して、削除を確認する

```powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="next-steps"></a>次のステップ

- Azure AD でホーム領域検出がどのように動作するかの詳細については、[Azure AD でのアプリケーションのホーム領域検出](home-realm-discovery-policy.md)に関するページを参照してください。