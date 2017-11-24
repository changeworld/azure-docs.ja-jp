---
title: "ホーム領域検出ポリシーを使用して、アプリケーションのサインイン自動高速化を構成する | Microsoft Docs"
description: "Azure AD テナントの概要、および Azure Active Directory で Azure を管理する方法を説明します。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: it-pro
ms.date: 11/09/2017
ms.author: billmath
ms.openlocfilehash: b1f0e5da09ef1d6acd234b72878cc71d66bb551e
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2017
---
# <a name="configure-sign-in-auto-acceleration-for-an-application-using-home-realm-discovery-hrd-policy"></a>ホーム領域検出 (HRD) ポリシーを使用してアプリケーションのサインイン自動高速化を構成する

## <a name="introduction-to-home-realm-discovery-and-auto-acceleration"></a>ホーム領域検出と自動高速化の概要
ここでは、ホーム領域検出と自動高速化の概要について説明します。

### <a name="home-realm-discovery"></a>ホーム領域検出
ホーム領域検出は、ユーザーの認証が必要な場所を、Azure Active Directory がサインイン時に決定できるようにするプロセスです。  Azure AD テナントにサインインしてリソース (または Azure AD の共通サインイン ページ) にアクセスする際、ユーザーはユーザー名 (UPN) を入力します。  Azure AD はそれを使用して、ユーザーがどこにサインインする必要があるかを決定します。   ユーザーは、認証を受けるために次のいずれかの場所にアクセスする必要があります。

- ユーザーのホーム テナント (ユーザーがアクセスしようとしているリソースと同じテナントである可能性があります)。 
- Microsoft アカウント。   ユーザーはリソース テナントのゲストです
- Azure AD テナントとフェデレーションされた別の ID プロバイダー。  オンプレミス ID プロバイダー (AD FS など) のインスタンスです。

### <a name="auto-acceleration"></a>自動高速化 
組織によっては、Azure Active Directory テナントをユーザー認証用の別の IdP (AD FS など) とフェデレーションするように構成することがあります。  そのような場合、アプリケーションへのサインイン時には、まず Azure AD のサインイン ページが表示され、ユーザーが自分の UPN を入力すると、IdP のサインイン ページが表示されます。  ただし管理者によっては、ユーザーが特定のアプリケーションにサインインする際、最初の Azure Active Directory ページをスキップし、すぐにサインイン ページを表示するほうが合理的と判断される場合もあります。 これは、「サインイン自動高速化」と呼ばれます。

テナントがサインイン用の別の IdP にフェデレーションされている場合、サインインするすべてのユーザーをその IdP で認証できることがわかっているのであれば、自動高速化を有効にすることで、ユーザーのサインインを効率化できます。  自動高速化は個々 のアプリケーションに対して構成できます。

>[!NOTE]
>アプリケーションを自動高速化用に構成した場合、ゲスト ユーザーはサインインできません。 ユーザーを認証用のフェデレーション IdP に導く操作は一方通行の処理であるため、Azure Active Directory のサインイン ページに戻ることはできません。  ゲスト ユーザーは、他のテナントや Microsoft アカウントなどの外部 IdP で認証を受けなければならない場合があり、ホーム領域検出の手順がスキップされるため、そのアプリケーションにサインインすることはできません。  

フェデレーション IdP への自動高速化を制御するには、2 つの方法があります。  次のどちらかの操作を行います。   

- アプリケーションの認証要求でドメイン ヒントを使用する 
- HomeRealmDiscovery ポリシーを構成して自動高速化を有効にする

## <a name="domain-hints"></a>ドメイン ヒント 
ドメイン ヒントは、アプリケーションからの認証要求に含まれるディレクティブです。  これらを使用すると、フェデレーション IdP のサインイン ページをすぐに表示できるほか、マルチテナント アプリケーションで使用すれば、テナント用にブランディングされた Azure AD サインイン ページをすぐに表示することができます。  たとえば、largeapp.com というアプリケーションがあり、顧客が contoso.largeapp.com というカスタム URL でアプリケーションにアクセスできる場合は、認証要求に Contoso.com へのドメイン ヒントが含まれている可能性があります。 ドメイン ヒントの構文は、使用されているプロトコルによって異なり、通常はアプリケーション内で構成されます。

**WS-Federation**:  クエリ文字列内の whr=contoso.com

**SAML**: ドメイン ヒントを含んだ SAML 認証要求か、クエリ文字列 whr=contoso.com

**Open ID Connect**: クエリ文字列 domain_hint=contoso.com 

アプリケーションからの認証要求にドメイン ヒントが含まれていて、テナントがそのドメインとフェデレーションされている場合、Azure AD は、そのドメイン用に構成された IdP にサインインをリダイレクトしようとします。  ドメイン ヒントが確認済みのフェデレーション ドメインを参照していない場合、そのドメイン ヒントは無視され、通常のホーム領域検出が起動されます。

Azure Active Directory でサポートされている、ドメイン ヒントを使用した自動高速化の詳細については、こちらの[ブログ投稿](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/)を参照してください。

>[!NOTE]
>認証要求にドメイン ヒントが含まれている場合は、アプリケーションに対して設定されているすべての HRD ポリシーが、そのドメイン ヒントによって上書きされます。

## <a name="home-realm-discovery-hrd-policy"></a>ホーム領域検出 (HRD) ポリシー
アプリケーションによっては、認証要求を構成する手段が提供されおらず、ドメイン ヒントを使用して自動高速化を制御することができない場合があります。   そのような場合は、ポリシーを使用して自動高速化を構成することで、同じ動作を実現できます。  

### <a name="setting-hrd-policy"></a>HRD ポリシーの設定
アプリケーションでサインイン自動高速化を設定するには、3 つの手順があります


1. 自動高速化のための HRD ポリシーを作成します
2. ポリシーをアタッチするサービス プリンシパルを検索します
3. サービス プリンシパルにポリシーをアタッチします。  テナント内で既にポリシーが作成されていても、それらがエンティティにアタッチされるまでは、影響はありません。  HRD ポリシーはサービス プリンシパルに関連付けることができます。なお、特定のエンティティ上で同時にアクティブにできる HRD ポリシーは 1 つだけです。  

HRD ポリシーを使用して自動高速化を設定する場合は、Microsoft Azure Active Directory Graph API を直接使用することもできますし、Azure Active Directory PowerShell コマンドレットを使用することもできます

ポリシーを操作する Graph API については、[こちら](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations)で説明されています。

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

**AccelerateToFederatedDomain** が false の場合、ポリシーは影響力を持ちません。**PreferredDomain** では、高速化するドメインを指定します。これは、テナントにフェデレーション ドメインが 1 つしかない場合には省略できます。  これを省略した場合で、確認済みのフェデレーション ドメインが 2 つ以上ある場合には、ポリシーは効力を持ちません。

**PreferredDomain** が指定されている場合、そのドメインは、テナントの確認済みフェデレーション ドメインに一致する必要があり、対象のアプリケーションのすべてのユーザーは、そのドメインにサインインできる必要があります。

### <a name="priority-and-evaluation-of-hrd-policies"></a>HRD ポリシーの優先順位と評価
HRD ポリシーを作成し、特定の組織およびサービス プリンシパルに割り当てることができます。 つまり、複数のポリシーを特定のアプリケーションに適用できます。 HRD ポリシーは、次の規則に従って発効されます。


- 認証要求内にドメイン ヒントが存在する場合は、すべての HRD ポリシーが無視され、ドメイン ヒントによって指定された動作が使用されます。
- その他の場合、ポリシーがサービス プリンシパルに明示的に割り当てられていれば、そのポリシーが適用されます。 
- ドメイン ヒントが存在せず、サービス プリンシパルに明示的に割り当てられているポリシーがない場合は、サービス プリンシパルの親組織に明示的に割り当てられているポリシーが適用されます。 
- ドメイン ヒントが存在せず、サービス プリンシパルまたは組織にポリシーが割り当てられていない場合は、既定の HRD 動作が使用されます。

## <a name="tutorial-for-setting-sign-in-auto-acceleration-on-an-application-using-hrd-policy-with-azure-active-directory-powershell-cmdlets"></a>Azure Active Directory PowerShell コマンドレットを用いた HRD ポリシーを使用してアプリケーションのサイン イン自動高速化を設定するためのチュートリアル
次を含む、いくつかのシナリオについて説明します。


- 1 つのフェデレーション ドメインを持つテナントについて、アプリケーションの自動高速化を設定する
- テナント用に確認された複数のドメインのいずれかに対して、アプリケーションの自動高速化を設定する
- ポリシーが構成されているアプリケーションを一覧表示する

### <a name="prerequisites"></a>前提条件
以下の例では、Azure AD 内のアプリケーション サービス プリンシパルを対象に、ポリシーを作成、更新、リンク、および削除します。

1.  まず、最新版の Azure AD PowerShell コマンドレットのプレビューをダウンロードします。 
2.  Azure AD PowerShell コマンドレットを入手したら、Connect コマンドを実行して、Azure AD に管理者アカウントでサインインします。

    ``` powershell
    Connect-AzureAD -Confirm
    ```
3.  次のコマンドを実行して、組織内のすべてのポリシーを表示します。

    ``` powershell
    Get-AzureADPolicy
    ```

何も返されない場合、テナント内にはポリシーが作成されていません

### <a name="example-setting-auto-acceleration-for-an-application"></a>例: アプリケーションの自動高速化の設定 
この例では、アプリケーションへのサインイン時に自動高速化によって AD FS のサインイン画面をすぐに表示するためのポリシーを作成します。  この動作は、ユーザーが最初に Azure AD のサインイン ページでユーザー名を入力しなくても実行されます。 

#### <a name="step-1-create-an-hrd-policy"></a>手順 1: HRD ポリシーを作成する
``` powershell
New-AzureADPoly -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

アプリケーションのユーザーを認証するフェデレーション ドメインが 1 つである場合、作成する必要がある HRD ポリシーは 1 つだけです。  
新しいポリシーを表示し、その ObjectID を取得するには、次のコマンドを実行します。

``` powershell
Get-AzureADPolicy
```


HRD ポリシーを作成したら、自動高速化を有効にするために、そのポリシーを複数のアプリケーション サービス プリンシパルに割り当てることができます。

#### <a name="step-2-locate-the-service-principal-to-assign-the-policy-to"></a>手順 2: ポリシーを割り当てるサービス プリンシパルを見つける。  
ポリシーを割り当てるサービス プリンシパルのオブジェクト ID が必要となります。 サービス プリンシパルのオブジェクト ID を検索するには、複数の方法があります。    

ポータルを使用したり、[Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) でクエリを実行したり、[Graph エクスプ ローラー ツール](https://graphexplorer.cloudapp.net/)にアクセスするなどして Azure AD アカウントにサインインし、組織内のすべてのサービス プリンシパルを参照することもできますし、PowerShell を使用しているのであれば、get-AzureADServicePrincipal コマンドレットを使用して、サービス プリンシパルとそれらの ID を一覧表示することもできます。

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>手順 3: サービス プリンシパルにポリシーを割り当てる  
自動高速化を構成するアプリケーションのサービス プリンシパルのオブジェクト ID が確認できたら、次のコマンドを実行して、手順 1 で作成した HRD ポリシーを、手順 2 で特定したサービス プリンシパルに関連付けます。

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

このコマンドは、ポリシーを追加する各サービス プリンシパルに対して繰り返し実行できます。

#### <a name="step-4-check-which-application-service-principals-your-auto-acceleration-policy-is-assigned-to"></a>手順 4: 自動高速化ポリシーが割り当てられているアプリケーション サービス プリンシパルを確認する
自動高速化ポリシーがどのアプリケーションに構成されているかを確認するには、 Get-AzureADPolicyAppliedObject コマンドレットを使用して、確認するポリシーのオブジェクト ID を渡します。

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
#### <a name="step-5-youre-done"></a>手順 5: 完了
アプリケーションを実行して、新しいポリシーが機能していることを確認します。

### <a name="example-listing-the-applications-for-which-an-auto-acceleration-policy-is-configured"></a>例: 自動高速化ポリシーが構成されているアプリケーションを一覧表示する

#### <a name="step-1-list-all-policies-created-in-your-organization"></a>手順 1: 組織で作成されたすべてのポリシーを一覧表示する 

``` powershell
Get-AzureADPolicy
```

割り当てを一覧表示するポリシーの**オブジェクト ID** をメモします。

#### <a name="step-2-list-the-service-principals-the-policy-is-assigned-to"></a>手順 2: ポリシーが割り当てられているサービス プリンシパルを一覧表示する。  

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```

### <a name="example-removing-an-auto-acceleration-policy-for-an-application"></a>例: アプリケーションの自動高速化ポリシーを削除する
#### <a name="step-1-use-the-previous-example-to-get-the-objectid-of-the-policy-and-that-of-the-application-service-principal-you-wish-to-remove-it-from"></a>手順 1: 先述の例を使用して、ポリシーのオブジェクト ID と、ポリシーを削除するアプリケーション サービス プリンシパルのオブジェクト ID を取得する
#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>手順 2: アプリケーション サービス プリンシパルからポリシー割り当てを削除する。  

``` powershell
Remove-AzureADApplicationPolicy -ObjectId <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-the-policy-is-assigned-to"></a>手順 3: ポリシーが割り当てられているサービス プリンシパルを一覧表示して、削除を確認する 

``` powershell
Get-AzureADPolicyAppliedObject -ObjectId <ObjectId of the Policy>
```
## <a name="next-steps"></a>次のステップ
- Azure AD での認証のしくみの詳細については、「[Azure AD の認証シナリオ](develop/active-directory-authentication-scenarios.md)」を参照してください。
- ユーザー シングル サインオンの詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-enterprise-apps-manage-sso.md)」を参照してください
- 開発者向けのすべての関連コンテンツの概要については、[Active Directory 開発者ガイド](develop/active-directory-developers-guide.md)を参照してください。
