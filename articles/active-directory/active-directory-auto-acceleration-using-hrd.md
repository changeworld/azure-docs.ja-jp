---
title: "ホーム領域検出ポリシーを使用して、アプリケーションのサインイン自動高速化を構成する | Microsoft Docs"
description: "Azure AD テナントの概要、および Azure Active Directory で Azure を管理する方法を説明します。"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: it-pro
ms.date: 11/09/2017
ms.author: billmath
ms.openlocfilehash: e2e6e5c40dc4a9f67f94c45f8394512db3f777f5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="configure-sign-in-auto-acceleration-for-an-application-by-using-a-home-realm-discovery-policy"></a>ホーム領域検出ポリシーを使用して、アプリケーションのサインイン自動高速化を構成する

ここでは、ホーム領域検出と自動高速化の概要について説明します。

## <a name="home-realm-discovery"></a>ホーム領域検出
ホーム領域検出 (HRD) は、ユーザーの認証が必要な場所を、Azure Active Directory (Azure AD) がサインイン時に決定できるようにするプロセスです。  ユーザーは、Azure AD テナントにサインインしてリソース (または Azure AD の共通サインイン ページ) にアクセスするとき、ユーザー名 (UPN) を入力します。 Azure AD はそれを使用して、ユーザーがどこにサインインする必要があるかを決定します。 

ユーザーは、認証を受けるために次のいずれかの場所にアクセスする必要があります。

- ユーザーのホーム テナント (ユーザーがアクセスしようとしているリソースと同じテナントである可能性があります)。 

- Microsoft アカウント。  ユーザーはリソース テナントのゲストです。

- Azure AD テナントとフェデレーションされた別の ID プロバイダー。

-  Active Directory フェデレーション サービス (AD FS) など、オンプレミスの ID プロバイダー。

## <a name="auto-acceleration"></a>自動高速化 
組織によっては、Azure Active Directory テナントをユーザー認証用の別の IdP (AD FS など) とフェデレーションするように構成することがあります。  

このような場合、ユーザーがアプリケーションにサインインすると、最初に Azure AD のサインイン ページが表示されます。 ユーザーが UPN を入力すると、IdP のサインイン ページが表示されます。 状況によっては、管理者は特定のアプリケーションにサインインしたユーザーに対してサインイン ページを表示したい場合があります。 

つまり、ユーザーは Azure Active Directory の最初のページをスキップできます。 このプロセスは、"サインイン自動高速化" と呼ばれます。

テナントがサインイン用の別の IdP にフェデレーションされている場合、自動高速化によりユーザー サインインがいっそう効率化されます。  自動高速化は個々 のアプリケーションに対して構成できます。

>[!NOTE]
>アプリケーションを自動高速化用に構成した場合、ゲスト ユーザーはサインインできません。 ユーザーを認証用のフェデレーション IdP に導く場合、Azure Active Directory のサインイン ページに戻ることはできません。 ゲスト ユーザーは、他のテナントや Microsoft アカウントなどの外部 IdP に移動されなければならない場合があり、ホーム領域検出の手順がスキップされるため、そのアプリケーションにサインインすることはできません。  

フェデレーション IdP への自動高速化を制御するには、2 つの方法があります。   

- アプリケーションの認証要求でドメイン ヒントを使用する。 
- ホーム領域検出ポリシーを構成して自動高速化を有効にする。

## <a name="domain-hints"></a>ドメイン ヒント 
ドメイン ヒントは、アプリケーションからの認証要求に含まれるディレクティブです。 ドメイン ヒントを使用して、ユーザーのフェデレーション IdP サインイン ページへの移動を高速化できます。 または、マルチテナント アプリケーションで使用して、テナント用にブランディングされた Azure AD サインイン ページをすぐに表示することができます。  

たとえば、"largeapp.com" というアプリケーションがあり、顧客は "contoso.largeapp.com" というカスタム URL でアプリケーションにアクセスできるものとします。 アプリは、認証要求に contoso.com へのドメイン ヒントを組み込んでいる可能性もあります。 

ドメイン ヒントの構文は、使用されているプロトコルによって異なり、通常はアプリケーション内で構成されます。

**WS-Federation**:  クエリ文字列内の whr=contoso.com。

**SAML**: ドメイン ヒントを含んだ SAML 認証要求か、クエリ文字列 whr=contoso.com。

**Open ID Connect**: クエリ文字列 domain_hint=contoso.com。 

アプリケーションからの認証要求にドメイン ヒントが含まれていて、テナントがそのドメインとフェデレーションされている場合、Azure AD は、そのドメイン用に構成された IdP にサインインをリダイレクトしようとします。 

ドメイン ヒントが確認済みのフェデレーション ドメインを参照していない場合、そのドメイン ヒントは無視され、通常のホーム領域検出が起動されます。

Azure Active Directory によってサポートされるドメイン ヒントを使った自動高速化について詳しくは、[Enterprise Mobility + Security のブログ](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/)をご覧ください。

>[!NOTE]
>認証要求にドメイン ヒントが含まれている場合は、アプリケーションに対して設定されているすべての HRD ポリシーが、そのドメイン ヒントによって上書きされます。

## <a name="home-realm-discovery-policy"></a>ホーム領域検出ポリシー
アプリケーションで、出力される認証要求を構成する手段が提供されていない場合があります。 そのような場合は、ドメイン ヒントを使って自動高速化を制御することはできません。 そのような場合は、ポリシーを使用して自動高速化を構成することで、同じ動作を実現できます。  

### <a name="set-hrd-policy"></a>HRD ポリシーを設定する
アプリケーションでサインイン自動高速化を設定するには、3 つの手順があります


1. 自動高速化のための HRD ポリシーを作成します。

2. ポリシーをアタッチするサービス プリンシパルを検索します。

3. サービス プリンシパルにポリシーをアタッチします。 テナント内で既にポリシーが作成されていても、それらがエンティティにアタッチされるまでは、影響はありません。 

HRD ポリシーはサービス プリンシパルに関連付けることができます。なお、特定のエンティティ上で同時にアクティブにできる HRD ポリシーは 1 つだけです。  

HRD ポリシーを使用して自動高速化を設定する場合は、Microsoft Azure Active Directory Graph API を直接使用することもできますし、Azure Active Directory PowerShell コマンドレットを使用することもできます。

ポリシーを操作する Graph API については、MSDN の記事「[Operations on policy](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations)」(ポリシーに対する操作) をご覧ください。

HRD ポリシーの定義の例を次に示します。
    
 ```
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu"
    }
   }
```

ポリシー タイプは、"HomeRealmDiscoveryPolicy" です。

**AccelerateToFederatedDomain** が false の場合、ポリシーにはどのような効果もありません。

**PreferredDomain** では、高速化するドメインを指定する必要があります。 テナントのフェデレーション ドメインが 1 つだけの場合は、これを省略できます。  これを省略した場合で、確認済みのフェデレーション ドメインが 2 つ以上ある場合には、ポリシーは効力を持ちません。

**PreferredDomain** を指定する場合は、テナントの検証済みのフェデレーション ドメインと一致している必要があります。 アプリケーションのすべてのユーザーが、そのドメインにサインインできる必要があります。

### <a name="priority-and-evaluation-of-hrd-policies"></a>HRD ポリシーの優先順位と評価
HRD ポリシーを作成し、特定の組織およびサービス プリンシパルに割り当てることができます。 つまり、複数のポリシーを特定のアプリケーションに適用できます。 HRD ポリシーは、次の規則に従って発効されます。


- 認証要求内にドメイン ヒントが存在する場合は、すべての HRD ポリシーが無視されます。 ドメイン ヒントによって指定された動作が使われます。

- その他の場合、ポリシーがサービス プリンシパルに明示的に割り当てられていれば、そのポリシーが適用されます。 

- ドメイン ヒントが存在せず、サービス プリンシパルに明示的に割り当てられているポリシーがない場合は、サービス プリンシパルの親組織に明示的に割り当てられているポリシーが適用されます。 

- ドメイン ヒントが存在せず、サービス プリンシパルまたは組織にポリシーが割り当てられていない場合は、既定の HRD 動作が使用されます。

## <a name="tutorial-for-setting-sign-in-auto-acceleration-on-an-application-by-using-an-hrd-policy"></a>HRD ポリシーを使ってアプリケーションにサインイン自動高速化を設定するチュートリアル
Azure AD PowerShell コマンドレットを使って次のようなシナリオを設定する手順を示します。


- 1 つのフェデレーション ドメインを持つテナントについて、アプリケーションの自動高速化を設定する。

- テナント用に確認された複数のドメインのいずれかに対して、アプリケーションの自動高速化を設定する。

- ポリシーが構成されているアプリケーションを一覧表示する。

### <a name="prerequisites"></a>前提条件
以下の例では、Azure AD 内のアプリケーション サービス プリンシパルを対象に、ポリシーを作成、更新、リンク、および削除します。

1.  まず、最新版の Azure AD PowerShell コマンドレットのプレビューをダウンロードします。 

2.  Azure AD PowerShell コマンドレットをダウンロードした後、Connect コマンドを実行して、Azure AD に管理者アカウントでサインインします。

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  次のコマンドを実行して、組織内のすべてのポリシーを表示します。

    ``` powershell
    Get-AzureADPolicy
    ```

何も返されない場合は、テナント内にポリシーが作成されていないことを意味します。

### <a name="example-set-auto-acceleration-for-an-application"></a>例: アプリケーションの自動高速化を設定する 
この例では、アプリケーションへのサインイン時に自動高速化によって AD FS のサインイン画面をすぐに表示するためのポリシーを作成します。 ユーザーは、最初に Azure AD のサインイン ページでユーザー名を入力しなくても、AD FS にサインインできます。 

#### <a name="step-1-create-an-hrd-policy"></a>手順 1: HRD ポリシーを作成する
``` powershell
New-AzureADPoly -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

アプリケーションのユーザーを認証するフェデレーション ドメインが 1 つである場合、作成する必要がある HRD ポリシーは 1 つだけです。  

新しいポリシーを表示し、その **ObjectId** を取得するには、次のコマンドを実行します。

``` powershell
Get-AzureADPolicy
```


HRD ポリシーを作成した後で自動高速化を有効にするには、そのポリシーを複数のアプリケーション サービス プリンシパルに割り当てます。

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>手順 2: ポリシーを割り当てるサービス プリンシパルを見つける  
ポリシーを割り当てるサービス プリンシパルの **ObjectID** が必要となります。 サービス プリンシパルの **ObjectID** を検索するには、複数の方法があります。    

ポータルを使うか、[Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) のクエリを行うことができます。 また、[Graph エクスプローラー ツール](https://graphexplorer.cloudapp.net/)に移動して Azure AD アカウントにサインインし、組織のすべてのサービス プリンシパルを表示することもできます。 PowerShell を使っているので、get-AzureADServicePrincipal コマンドレットを使ってサービス プリンシパルとその ID を一覧表示できます。

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>手順 3: サービス プリンシパルにポリシーを割り当てる  
自動高速化を構成するアプリケーションのサービス プリンシパルの **ObjectID** を入手した後、次のコマンドを実行します。 このコマンドは、手順 1 で作成した HRD ポリシーを、手順 2 で取得したサービス プリンシパルに関連付けます。

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

このコマンドは、ポリシーを追加する各サービス プリンシパルに対して繰り返し実行できます。

#### <a name="step-4-check-which-application-service-principals-your-auto-acceleration-policy-is-assigned-to"></a>手順 4: 自動高速化ポリシーが割り当てられているアプリケーション サービス プリンシパルを確認する
自動高速化ポリシーがどのアプリケーションに構成されているかを確認するには、**Get-AzureADPolicyAppliedObject** コマンドレットを使います。 そして、確認するポリシーの **ObjectID** を渡します。

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>手順 5: 完了
アプリケーションを実行して、新しいポリシーが機能していることを確認します。

### <a name="example-list-the-applications-for-which-an-auto-acceleration-policy-is-configured"></a>例: 自動高速化ポリシーが構成されているアプリケーションを一覧表示する

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>手順 1: 組織で作成されたすべてのポリシーを一覧表示する 

``` powershell
Get-AzureADPolicy
```

割り当てを一覧表示するポリシーの **ObjectID** をメモします。

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>手順 2: ポリシーが割り当てられているサービス プリンシパルを一覧表示する  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-remove-an-auto-acceleration-policy-for-an-application"></a>例: アプリケーションの自動高速化ポリシーを削除する
#### <a name="step-1-get-the-objectid"></a>手順 1: ObjectID を取得する
先述の例を使って、ポリシーの **ObjectID** と、ポリシーを削除するアプリケーション サービス プリンシパルのオブジェクト ID を取得します。 

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>手順 2: アプリケーション サービス プリンシパルからポリシー割り当てを削除する  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>手順 3: ポリシーが割り当てられているサービス プリンシパルを一覧表示して、削除を確認する 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>次のステップ
- Azure AD での認証のしくみについて詳しくは、「[Azure AD の認証シナリオ](develop/active-directory-authentication-scenarios.md)」をご覧ください。
- ユーザー シングル サインオンについて詳しくは、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-enterprise-apps-manage-sso.md)」をご覧ください。
- 開発者向けのすべての関連コンテンツの概要については、[Active Directory 開発者ガイド](develop/active-directory-developers-guide.md)を参照してください。
