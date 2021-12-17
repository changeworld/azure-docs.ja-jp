---
title: ホーム領域検出ポリシー
titleSuffix: Azure AD
description: ホーム領域検出ポリシーを使用して、アプリケーションの自動高速化を制御する方法について説明します。
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 08/20/2021
ms.author: davidmu
ms.reviewer: hirsin
ms.openlocfilehash: 8ac93a42c4911a6694770ee320f84b88d6ea6aa5
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132547448"
---
# <a name="home-realm-discovery-for-an-application"></a>アプリケーションのホーム領域検出

ホーム領域検出 (HRD) は、ユーザーがサインイン時にどの ID プロバイダー ("IdP") に対して認証する必要があるかを Azure AD で決定できるようにするプロセスです。  ユーザーは、Azure AD テナントにサインインしてリソース (または Azure AD の共通サインイン ページ) にアクセスするとき、ユーザー名 (UPN) を入力します。 Azure AD はそれを使用して、ユーザーがどこにサインインする必要があるかを決定します。

ユーザーは、認証を受けるため、以下のいずれかの ID プロバイダーに移動されます。

- ユーザーのホーム テナント (ユーザーがアクセスしようとしているリソースと同じテナントである可能性があります)。

- Microsoft アカウント。  ユーザーは、認証にコンシューマー アカウントを使用するリソース テナント内のゲストです。

- Active Directory フェデレーション サービス (AD FS) など、オンプレミスの ID プロバイダー。

- Azure AD テナントとフェデレーションされた別の ID プロバイダー。

## <a name="auto-acceleration"></a>自動高速化

一部の組織では、その Azure AD テナント内のドメインを別の IdP (ユーザー認証用の AD FS など) とフェデレーションするように構成します。

ユーザーがアプリケーションにサインインすると、最初に Azure AD のサインイン ページが表示されます。 UPN を入力した後、フェデレーション ドメイン内にいると、そのドメインにサービスを提供している IdP のサインイン ページが表示されます。 状況によっては、管理者は特定のアプリケーションにサインインしたユーザーに対してサインイン ページを表示したい場合があります。

そのため、ユーザーは Azure Active Directory の最初のページをスキップできます。 このプロセスは、"サインイン自動高速化" と呼ばれます。

テナントがサインイン用の別の IdP にフェデレーションされている場合、自動高速化によりユーザー サインインがいっそう効率化されます。  自動高速化は個々 のアプリケーションに対して構成できます。

> [!NOTE]
> 自動高速化のためにアプリケーションを構成する場合、ユーザーは管理された資格情報 (FIDO など) を使用できず、ゲスト ユーザーはサインインできません。 ユーザーを認証用のフェデレーション IdP に直接導いた場合、そのユーザーを Azure Active Directory のサインイン ページに戻す方法はありません。 ゲスト ユーザーは、他のテナントや Microsoft アカウントなどの外部 IdP に移動されなければならない場合があり、ホーム領域検出の手順がスキップされるため、そのアプリケーションにサインインすることはできません。

フェデレーション IdP に対する自動高速化を制御する方法は 3 つあります。

- アプリケーションの認証要求でドメイン ヒントを使用する。
- [自動高速化を強制する]()ようにホーム領域検出ポリシーを構成します (configure-authentication-for-federated-users-portal.md)。
- 特定のアプリケーションからの、または特定ドメインの[ドメイ ン ヒントを無視する](prevent-domain-hints-with-home-realm-discovery.md)ようにホーム領域検出ポリシーを構成します。

## <a name="domain-hints"></a>ドメイン ヒント

ドメイン ヒントは、アプリケーションからの認証要求に含まれるディレクティブです。 ドメイン ヒントを使用して、ユーザーのフェデレーション IdP サインイン ページへの移動を高速化できます。 または、マルチテナント アプリケーションで使用して、テナント用にブランディングされた Azure AD サインイン ページをすぐに表示することができます。

たとえば、"largeapp.com" というアプリケーションがあり、顧客は "contoso.largeapp.com" というカスタム URL でアプリケーションにアクセスできるものとします。 アプリは、認証要求に contoso.com へのドメイン ヒントを組み込んでいる可能性もあります。

ドメイン ヒントの構文は、使用されているプロトコルによって異なり、通常は次の方法によってアプリケーションで構成されます。

- **WS-Federation** を使用するアプリケーションの場合: クエリ文字列内の whr=contoso.com。

- **SAML** を使用するアプリケーションの場合: ドメイン ヒントを含む SAML 認証要求か、またはクエリ文字列 whr=contoso.com のどちらか。

- **Open ID Connect** を使用するアプリケーションの場合: クエリ文字列 domain_hint=contoso.com。

既定では、次の **両方** に当てはまる場合、Azure AD はドメインに対して構成されている IdP へのサインインのリダイレクトを試みます。

- アプリケーションからの認証要求にドメイン ヒントが含まれていて、**さらに**
- そのドメインとテナントがフェデレーションされている。

ドメイン ヒントが確認済みのフェデレーション ドメインを参照していない場合、そのドメイン ヒントは無視されます。

> [!NOTE]
> 認証要求にドメイン ヒントが含まれていて、[優先する必要がある](#home-realm-discovery-policy-to-prevent-auto-acceleration)場合、そのドメイン ヒントにより、HRD ポリシー内でアプリケーションに対して設定されている自動高速化がオーバーライドされます。

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>自動高速化に対するホーム領域検出ポリシー

アプリケーションで、出力される認証要求を構成する手段が提供されていない場合があります。 そのような場合は、ドメイン ヒントを使って自動高速化を制御することはできません。 自動高速化を[ホーム領域検出ポリシーによって構成](configure-authentication-for-federated-users-portal.md)して、同じ動作を実現できます。

### <a name="home-realm-discovery-policy-to-prevent-auto-acceleration"></a>自動高速化を防止するに対するホーム領域検出ポリシー

一部の Microsoft アプリケーションや SaaS アプリケーションには、自動的に domain_hints が含められます (たとえば `https://outlook.com/contoso.com` は、`&domain_hint=contoso.com` が追加されたログイン要求になります)。これによって、FIDO などの管理された資格情報のロールアウトが中断される可能性があります。  管理された資格情報のロールアウト中に、[ホーム領域検出ポリシー](prevent-domain-hints-with-home-realm-discovery.md)を使用して、特定のアプリまたは特定のドメインのドメイン ヒントを無視できます。

## <a name="enable-direct-ropc-authentication-of-federated-users-for-legacy-applications"></a>レガシ アプリケーションに対するフェデレーション ユーザーの直接 ROPC 認証を有効にする

アプリケーションで Azure AD ライブラリと対話型サインインを使用してユーザーを認証することがベスト プラクティスです。 ライブラリは、フェデレーション ユーザーのフローを処理します。  レガシ アプリケーションでは、特にリソース所有者のパスワード資格情報 (ROPC) 付与を使用しているとき、ユーザー名とパスワードが Azure AD に直接送信されるため、フェデレーションを理解するために書き込まれない場合があります。 これらは HRD を実行せず、正しいフェデレーション エンドポイントと対話してユーザーを認証することも行いません。 選択した場合は、[ホーム領域検出ポリシー](configure-authentication-for-federated-users-portal.md)を使用して、ROPC 付与を使用してユーザー名とパスワード資格情報を送信する特定のレガシ アプリケーションが Azure Active Directory に対して直接認証するようにできます。パスワード ハッシュの同期が有効になっている必要があります。

> [!IMPORTANT]
> 直接認証を有効にするのは、パスワード ハッシュ同期がオンであり、なおかつオンプレミス IdP でポリシーを実装せずにこのアプリケーションを認証しても問題ないことがわかっている場合のみにしてください。 何らかの理由でパスワード ハッシュ同期をオフにした場合、または AD Connect によるディレクトリ同期をオフにした場合は、このポリシーを削除し、古いパスワード ハッシュを使用して直接認証が行われないようにしてください。

## <a name="set-hrd-policy"></a>HRD ポリシーを設定する

フェデレーション サインイン自動高速化用のアプリケーション、または直接的なクラウドベース アプリケーションに対して HRD ポリシーを設定するには、以下の 3 つのステップを実行します。

1. HRD ポリシーを作成します。

2. ポリシーをアタッチするサービス プリンシパルを探します。

3. サービス プリンシパルにポリシーをアタッチします。

ポリシーは、サービス プリンシパルにアタッチされてはじめて、特定のアプリケーションに対して有効になります。

サービス プリンシパル上でアクティブにできる HRD ポリシーは、一度に 1 つだけです。

Azure Active Directory の PowerShell コマンドレットを使用して、HRD ポリシーを作成および管理できます。

HRD ポリシーの定義の例を次に示します。

```json
{  
  "HomeRealmDiscoveryPolicy":
  {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":false,    
  }
}
```

ポリシーの種類は、"[HomeRealmDiscoveryPolicy](/graph/api/resources/homeRealmDiscoveryPolicy)" です。

**AccelerateToFederatedDomain** は、省略可能です。 **AccelerateToFederatedDomain** が false の場合、ポリシーが自動高速化に影響を与えることはありません。 **AccelerateToFederatedDomain** が true で、テナント内に検証およびフェデレーションされたドメインが 1 つのみある場合は、フェデレーション IdP のサインイン ページが直接表示されます。 これが true で、テナント内に検証されたドメインが複数ある場合は、**PreferredDomain** を指定する必要があります。

**PreferredDomain** は、省略可能です。 **PreferredDomain** では、高速化するドメインを指定する必要があります。 テナントのフェデレーション ドメインが 1 つだけの場合は、これを省略できます。  これを省略した場合で、確認済みのフェデレーション ドメインが 2 つ以上ある場合には、ポリシーは効力を持ちません。

 **PreferredDomain** を指定する場合は、テナントの検証済みのフェデレーション ドメインと一致している必要があります。 アプリケーションのすべてのユーザーがそのドメインにサインインできる必要があります。フェデレーション ドメインでサインインできないユーザーは、トラップされてサインインを完了できません。

**AllowCloudPasswordValidation** は、省略可能です。 **AllowCloudPasswordValidation** が true の場合、アプリケーションは、Azure Active Directory トークン エンドポイントに対して直接ユーザー名とパスワードの資格情報を提示することにより、フェデレーション ユーザーを認証できます。 これが機能するのは、パスワード ハッシュ同期が有効の場合に限られます。

さらに、上に示されていないテナント レベルの HRD オプションが 2 つ存在します。

- **AlternateIdLogin** は省略可能です。  有効にすると、ユーザーは Azure AD のサインイン ページで、[UPN の代わりに自分の電子メール アドレスを使用してサインインできるようになります](../authentication/howto-authentication-use-email-signin.md)。  代替 ID は、フェデレーション IDP に対して自動高速化されていないユーザーに依存します。

- **DomainHintPolicy** は、[ドメイン ヒントがフェデレーション ドメインに対してユーザーを自動高速化 "*しないようにする*" 省略可能な複合オブジェクトです](prevent-domain-hints-with-home-realm-discovery.md)。 このテナント全体の設定は、ドメイン ヒントを送信するアプリケーションが、クラウドで管理されている資格情報を使用してユーザーがサインインすることを妨げないようにするために使用されます。

### <a name="priority-and-evaluation-of-hrd-policies"></a>HRD ポリシーの優先順位と評価

HRD ポリシーを作成し、特定の組織およびサービス プリンシパルに割り当てることができます。 これは、特定のアプリケーションに複数のポリシーを適用できることを意味するため、Azure AD で、どのポリシーを優先するかを決定する必要があります。 一連の規則によって、(適用される多くのポリシーのうち) どの HRD ポリシー が有効になるかが決定されます。

- 認証要求内にドメイン ヒントがある場合は、テナントに対する HRD ポリシー (テナントの既定値として設定されているポリシー) がチェックされ、ドメイン ヒントを無視する必要があるかどうかが確認されます。 ドメイン ヒントが許可されている場合は、ドメイン ヒントによって指定されてている動作が使用されます。

- その他の場合、ポリシーがサービス プリンシパルに明示的に割り当てられていれば、そのポリシーが適用されます。

- ドメイン ヒントが存在せず、サービス プリンシパルに明示的に割り当てられているポリシーがない場合は、サービス プリンシパルの親組織に明示的に割り当てられているポリシーが適用されます。

- ドメイン ヒントが存在せず、サービス プリンシパルまたは組織にポリシーが割り当てられていない場合は、既定の HRD 動作が使用されます。

## <a name="next-steps"></a>次のステップ

- [ホーム領域検出ポリシーを使用してアプリケーションのサインイン動作を構成する](configure-authentication-for-federated-users-portal.md)
- [ホーム領域検出ポリシーを使用してユーザー サインイン時のフェデレーション IDP への自動高速化を無効にする](prevent-domain-hints-with-home-realm-discovery.md)
- Azure AD での認証のしくみについて詳しくは、「[Azure AD の認証シナリオ](../develop/authentication-vs-authorization.md)」をご覧ください。
