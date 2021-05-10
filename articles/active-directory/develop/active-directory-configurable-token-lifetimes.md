---
title: 構成可能なトークンの有効期間
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォームによって発行されるアクセス、SAML、ID トークンの有効期間を設定する方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperf-fy21q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: e1753391c7b61b6e9bd9e6ac0d142b4ee94502d8
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363973"
---
# <a name="configurable-token-lifetimes-in-the-microsoft-identity-platform-preview"></a>Microsoft ID プラットフォームでの構成可能なトークンの有効期間 (プレビュー)

Microsoft ID プラットフォームによって発行されるアクセス、ID、または SAML トークンの有効期間を指定できます。 組織のすべてのアプリ、マルチテナント (複数の組織) アプリケーション、または組織の特定のサービス プリンシパルに対して、トークンの有効期間を設定できます。 ただし、現時点では、[マネージド ID サービス プリンシパル](../managed-identities-azure-resources/overview.md)のトークン有効期間の構成はサポートされていません。

Azure AD では、ポリシー オブジェクトは、組織の個々のアプリケーションまたはすべてのアプリケーションに適用される規則のセットを表します。 それぞれのポリシーの種類は、割り当てられているオブジェクトに適用されるプロパティのセットを含む一意の構造体を持ちます。

組織の既定のポリシーとして、ポリシーを指定できます。 ポリシーは、優先度が高いポリシーによってオーバーライドされない限り、組織内のすべてのアプリケーションに適用されます。 ポリシーを特定のアプリケーションに割り当てることもできます。 優先順位の順序は、ポリシーの種類によって異なります。

例については、[トークンの有効期間の構成方法の例](configure-token-lifetimes.md)をご覧ください。

> [!NOTE]
> 構成可能なトークン ライフタイム ポリシーは、SharePoint Online リソースと OneDrive for Business リソースにアクセスするモバイル クライアントとデスクトップ クライアントにのみ適用され、Web ブラウザー セッションには適用されません。
> SharePoint Online と OneDrive for Business の Web ブラウザー セッションのライフタイムを管理するには、[条件付きアクセス セッション ライフタイム](../conditional-access/howto-conditional-access-session-lifetime.md)機能を使用します。 アイドル状態のセッションのタイムアウトの構成に関する詳細については、[SharePoint Online のブログ](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208)を参照してください。

## <a name="license-requirements"></a>ライセンスの要件

この機能を使用するには、Azure AD Premium P1 ライセンスが必要です。 ご自分の要件に対して適切なライセンスを探すには、[一般公開されている Free および Premium エディションの機能比較](https://azure.microsoft.com/pricing/details/active-directory/)に関するページをご覧ください。

[Microsoft 365 Business ライセンス](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)をお持ちのお客様も、条件付きアクセス機能にアクセスできます。

## <a name="token-lifetime-policies-for-access-saml-and-id-tokens"></a>アクセス トークン、SAML トークン、および ID トークンのトークン有効期間ポリシー

アクセス トークン、SAML トークン、および ID トークンにトークン有効期間ポリシーを設定できます。

### <a name="access-tokens"></a>アクセス トークン

クライアントは保護されたリソースにアクセスするためにアクセス トークンを使用します。 アクセス トークンは、ユーザー、クライアント、およびリソースの特定の組み合わせに対してのみ使用できます。 アクセス トークンは取り消すことはできず、有効期限まで有効です。 悪意のあるアクターがアクセス トークンを取得した場合は、その有効期間にわたって使用される可能性があります。 アクセス トークンの有効期間の調整は、システム パフォーマンスの向上と、ユーザーのアカウントが無効になった後にクライアントがアクセスを保持する時間の増加との間で、トレードオフとなります。 システム パフォーマンスの向上は、クライアントが新しいアクセス トークンを取得しなければならない回数を減らすことで実現されます。  既定値は、トークンを要求するクライアント アプリケーションによって異なります。 たとえば、継続的アクセス評価 (CAE) 対応のセッションをネゴシエートする CAE 対応クライアントには、有効期間が長いトークン (最大 28 時間) が表示されます。 トークンの有効期限が切れると、クライアントは更新トークンを使用して新しい更新トークンとアクセス トークンを (通常は自動で) 取得する必要があります。

### <a name="saml-tokens"></a>SAML トークン

SAML トークンは、Web ベースの SaaS アプリケーションの多くで使用され、Azure Active Directory の SAML2 プロトコル エンドポイントを使用して取得されます。 また、WS-Federation を使用するアプリケーションでも使用されます。 トークンの既定の有効期間は 1 時間です。 アプリケーションの観点からは、トークンの有効期間は、そのトークン内の `<conditions …>` 要素の NotOnOrAfter 値によって指定されます。 トークンの有効期間が終了したら、クライアントは新しい認証要求を開始する必要があります。これは多くの場合、シングル サインオン (SSO) セッション トークンの結果として、対話型サインインなしで満たされます。

NotOnOrAfter の値は、`TokenLifetimePolicy` 内の `AccessTokenLifetime` パラメーターを使用して変更できます。 この値は、ポリシーで構成されている有効期間に設定され (構成されている場合)、クロック スキュー係数が 5 分になります。

`<SubjectConfirmationData>` 要素で指定されているサブジェクト確認 NotOnOrAfter は、トークンの有効期間の構成には影響されません。 

### <a name="id-tokens"></a>ID トークン

ID トークンは、Web サイトとネイティブ クライアントに渡されます。 ID トークンは、ユーザーに関するプロファイル情報を格納します。 ID トークンは、ユーザーとクライアントの特定の組み合わせにバインドされます。 ID トークンは、それらの有効期限まで有効とみなされます。 通常、Web アプリケーションは、アプリケーションにおけるユーザーのセッションの有効期間と、ユーザーに対して発行された ID トークンの有効期間を照合します。 ID トークンの有効期間を調整して、Web アプリケーションでアプリケーション セッションが期限切れになる頻度、および Microsoft ID プラットフォームでユーザーの再認証 (自動または対話形式) が要求される頻度を制御できます。

## <a name="token-lifetime-policies-for-refresh-tokens-and-session-tokens"></a>更新トークンとセッション トークンのトークン有効期間ポリシー

更新トークンとセッション トークンに対してトークン有効期間ポリシーを設定することはできません。

> [!IMPORTANT]
> 2021 年 1 月 30 日の時点で、更新およびセッション トークンの有効期間は構成できません。 Azure Active Directory では、既存のポリシーの更新およびセッション トークンの構成が考慮されなくなくなりました。  既存のトークンの有効期限が切れた後に発行される新しいトークンは、[既定の構成](#configurable-token-lifetime-properties)に設定されるようになりました。 更新およびセッション トークンの構成の廃止後も、アクセス、SAML、ID の各トークンの有効期間を構成することはできます。
>
> 既存のトークンの有効期間は変更されません。 有効期限が切れると、既定値に基づいて新しいトークンが発行されます。
>
> ユーザーが再度サインインするように求められるまでの時間を定義し続ける必要がある場合は、条件付きアクセスでサインインの頻度を構成します。 条件付きアクセスの詳細については、「[条件付きアクセスを使用して認証セッション管理を構成する](../conditional-access/howto-conditional-access-session-lifetime.md)」をお読みください。

## <a name="configurable-token-lifetime-properties"></a>構成可能なトークンの有効期間のプロパティ
トークンの有効期間ポリシーとは、トークンの有効期間の規則が含まれる一種のポリシー オブジェクトです。 このポリシーは、このリソースのアクセス トークン、SAML トークン、および ID トークンが有効とみなされる期間を制御します。 更新トークンとセッション トークンに対して有効期間ポリシーを設定することはできません。 ポリシーが設定されていない場合は、既定の有効期間の値が適用されます。

### <a name="access-id-and-saml2-token-lifetime-policy-properties"></a>アクセス、ID、および SAML2 トークンの有効期間ポリシーのプロパティ

Access Token Lifetime プロパティを減らすと、悪意のあるアクターによって、長時間にわたってアクセス トークンや ID トークンが使用されるリスクが軽減します (これらのトークンは取り消しできません)。このトレードオフは、トークンを頻繁に交換する必要があるため、パフォーマンスが影響を受けることです。

例については、「[Web サインインのポリシーを作成する](configure-token-lifetimes.md#create-a-policy-for-web-sign-in)」を参照してください。

アクセス、ID、SAML2 トークンの構成は、次のプロパティとそれぞれの設定値の影響を受けます。

- **プロパティ**: アクセス トークンの有効期間
- **ポリシーのプロパティ文字列**: AccessTokenLifetime
- **影響**: アクセス トークン、ID トークン、SAML2 トークン
- **既定**:
    - アクセス トークン: トークンを要求するクライアント アプリケーションによって異なります。 たとえば、継続的アクセス評価 (CAE) 対応のセッションをネゴシエートする CAE 対応クライアントには、有効期間が長いトークン (最大 28 時間) が表示されます。
    - ID トークン、SAML2 トークン: 1 時間
- **最小**: 10 分
- **最大**: 1 日

### <a name="refresh-and-session-token-lifetime-policy-properties"></a>更新トークンとセッション トークンの有効期間ポリシーのプロパティ

更新とセッション トークンの構成は、次のプロパティとそれぞれの設定値の影響を受けます。 2021 年 1 月 30 日に更新トークンとセッション トークンの構成が廃止された後、Azure AD では、以下に説明する既定値のみを受け入れます。 廃止後に、サインイン頻度を管理するための[条件付きアクセス](../conditional-access/howto-conditional-access-session-lifetime.md)を使用しない場合、更新トークンとセッション トークンは廃止日の既定の構成に設定され、有効期間を変更することはできなくなります。  

|プロパティ   |ポリシーのプロパティ文字列    |影響 |Default |
|----------|-----------|------------|------------|
|更新トークンの最大非アクティブ時間 |MaxInactiveTime  |更新トークン |90 日間  |
|単一要素更新トークンの最長有効期間  |MaxAgeSingleFactor  |更新トークン (すべてのユーザー向け)  |Until-revoked  |
|多要素更新トークンの最長有効期間  |MaxAgeMultiFactor  |更新トークン (すべてのユーザー向け) |Until-revoked  |
|単一要素セッション トークンの最長有効期間  |MaxAgeSessionSingleFactor |セッション トークン (永続的および非永続的)  |Until-revoked |
|多要素セッション トークンの最長有効期間  |MaxAgeSessionMultiFactor  |セッション トークン (永続的および非永続的)  |Until-revoked |

PowerShell を使用すると、廃止の影響を受けるポリシーを特定できます。  [PowerShell コマンドレット](configure-token-lifetimes.md#get-started)を使用すると、組織で作成されたすべてのポリシーを確認したり、特定のポリシーにリンクされているアプリとサービス プリンシパルを検索したりできます。

## <a name="policy-evaluation-and-prioritization"></a>ポリシーの評価と優先順位付け
トークン有効期間ポリシーを作成して、特定のアプリケーション、組織、およびサービス プリンシパルに割り当てることができます。 複数のポリシーを、特定のアプリケーションに適用できます。 有効なトークン有効期間ポリシーは、次の規則に従います。

* ポリシーが明示的にサービス プリンシパルに割り当てられている場合は、そのポリシーが適用されます。
* 明示的にサービス プリンシパルに割り当てられているポリシーがない場合は、サービス プリンシパルの親組織に明示的に割り当てられているポリシーが適用されます。
* サービス プリンシパルまたは組織に明示的に割り当てられているポリシーがない場合、アプリケーションに割り当てられているポリシーが適用されます。
* サービス プリンシパル、組織、またはアプリケーション オブジェクトに割り当てられているポリシーがない場合は、既定値が適用されます。 ([構成可能なトークンの有効期間のプロパティ](#configurable-token-lifetime-properties)の表を参照してください)。

アプリケーション オブジェクトとサービス プリンシパル オブジェクトの関係の詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](app-objects-and-service-principals.md)」を参照してください。

トークンの有効性は、トークンの使用時に評価されます。 アクセスされているアプリケーションに対して、最も優先度が高いポリシーが有効になります。

ここで使用されるすべての時間帯は、C# [TimeSpan](/dotnet/api/system.timespan) オブジェクト (D.HH:MM:SS) 形式に従って書式設定されます。  つまり、80 日と 30 分は `80.00:30:00` になります。  0 の場合、先頭の D を削除することができますので 90 分は `00:90:00` になります。  

## <a name="cmdlet-reference"></a>コマンドレット リファレンス

これらは、[Azure Active Directory PowerShell for Graph Preview モジュール](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#service-principals)のコマンドレットです。

### <a name="manage-policies"></a>ポリシーの管理

次のコマンドレットを使用して、ポリシーを管理することができます。

| コマンドレット | 説明 | 
| --- | --- |
| [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 新しいポリシーを作成します。 |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | すべての AzureAD ポリシーまたは指定されたポリシーを取得します。 |
| [Get AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | ポリシーにリンクされたすべてのアプリとサービス プリンシパルを取得します。 |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 既存のポリシーを更新します。 |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | 指定したポリシーを削除します。 |

### <a name="application-policies"></a>アプリケーション ポリシー
アプリケーション ポリシーには次のコマンドレットを使用できます。</br></br>

| コマンドレット | 説明 | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | 指定したポリシーをアプリケーションにリンクします。 |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | アプリケーションに割り当てられているポリシーを取得します。 |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | アプリケーションからポリシーを削除します。 |

### <a name="service-principal-policies"></a>サービス プリンシパル ポリシー
サービス プリンシパル ポリシーには次のコマンドレットを使用できます。

| コマンドレット | 説明 | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | 指定したポリシーをサービス プリンシパルにリンクします。 |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | 指定したサービス プリンシパルにリンクされている任意のポリシーを取得します。|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | 指定したサービス プリンシパルからポリシーを削除します。|

## <a name="next-steps"></a>次のステップ

詳細については、[トークンの有効期間の構成方法の例](configure-token-lifetimes.md)をご覧ください。
