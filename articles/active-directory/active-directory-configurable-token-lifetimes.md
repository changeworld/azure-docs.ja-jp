---
title: "Azure Active Directory における構成可能なトークンの有効期間 | Microsoft Docs"
description: "この機能は、管理者や開発者が、Azure AD によって発行されたトークンの有効期間を指定するために使用します。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: b520b4672dd403981d218c9855c3beb09ef55021
ms.openlocfilehash: 6da28e6273d92445e4b14ea22752a6e59b1dd93a


---
# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Azure Active Directory における構成可能なトークンの有効期間 (パブリック プレビュー)
> [!NOTE]
> この機能は現在パブリック プレビューの段階です。  変更を元に戻すか、削除できるように準備しておく必要があります。  パブリック プレビューでは、すべてのユーザーに対してこの機能を公開していますが、一般公開時には、一部の機能で [Azure AD Premium サブスクリプション](active-directory-get-started-premium.md)が必要となる場合があります。
> 
> 

## <a name="introduction"></a>はじめに
この機能は、管理者や開発者が、Azure AD によって発行されたトークンの有効期間を指定するために使用します。 組織のすべてのアプリ、任意のマルチテナント (複数の組織) アプリケーション、または組織の特定のサービス プリンシパルに対して、トークンの有効期間を構成できます。

Azure AD では、ポリシー オブジェクトは、組織の個々のアプリケーションまたはすべてのアプリケーションに適用される規則のセットを表します。  それぞれのポリシーの種類は、割り当てられているオブジェクトに適用されるプロパティのセットを含む一意の構造体を持ちます。

ポリシーは、組織の既定値として指定できます。 このポリシーは、より優先度が高いポリシーによって上書きされない限り、その組織内に存在するアプリケーションに対して有効です。 ポリシーは、特定のアプリケーションにも割り当てることができます。 優先順位の順序は、ポリシーの種類によって異なります。

更新トークン、アクセス トークン、セッション トークン、および ID トークンのトークンの有効期間ポリシーを構成できます。

### <a name="access-tokens"></a>アクセス トークン
アクセス トークンは、保護されたリソースにアクセスするために、クライアントが使用します。 アクセス トークンは、ユーザー、クライアント、およびリソースの特定の組み合わせに対してのみ使用できます。 アクセス トークンは取り消すことはできず、有効期限まで有効です。 悪意のあるアクターがアクセス トークンを取得した場合は、その有効期間にわたって使用される可能性があります。  アクセス トークンの有効期間の調整は、システム パフォーマンスの向上と、ユーザーのアカウントが無効になった後にクライアントがアクセスを保持する時間の増加との間で、トレードオフとなります。  システム パフォーマンスの向上は、クライアントが新しいアクセス トークンを取得しなければならない回数を減らすことで実現されます。 

### <a name="refresh-tokens"></a>更新トークン
クライアントは、保護されたリソースにアクセスするアクセス トークンを取得するときに、更新トークンおよびアクセス トークンの両方を受け取ります。 更新トークンを使用して、現在のアクセス トークンの有効期限が切れたときに、新しいアクセス トークンと更新トークンのペアを取得します。 更新トークンは、ユーザーとクライアントの組み合わせにバインドされます。 この組み合わせは失効させることができ、使用時に必ず有効性がチェックされます。

Confidential クライアントとパブリック クライアントを区別することは重要です。 さまざまな種類のクライアントの詳細については、[RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1) を参照してください。

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Confidential クライアントの更新トークンの有効期間
Confidential クライアントは、クライアントのパスワード (シークレット) を安全に格納できるアプリケーションで、要求が悪意のあるアクターからではなく、クライアント アプリケーションから送信されていることを証明できます。 たとえば、Web アプリは、Web サーバーにクライアント シークレットを格納でき、公開されません。したがって、Confidential クライアントです。 これらのフローは安全性をより高めているため、フローに対して発行される更新トークンの既定の有効期間は長く、ポリシーを使用して変更することはできません。

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>パブリック クライアントの更新トークンの有効期間 

パブリック クライアントは、クライアントのパスワード (シークレット) を安全に格納できません。 たとえば、iOS や Android アプリは、リソース所有者のシークレットを難読化できないため、パブリック クライアントと見なされます。  リソースにポリシーを設定して、指定した期間よりも古いパブリック クライアントの更新トークンが、新しいアクセス トークンと更新トークン ペアを取得しないようにできます (Refresh Token Max Inactive Time)。  さらに、ポリシーを使用すると、更新トークンが受け入れられなくなる時点以降に期間を設定することもできます (Refresh Token Max Age)。  更新トークンの有効期間を調整すると、パブリック クライアント アプリケーションの使用時に、ユーザーが自動的に再認証するのではなく、資格情報を再入力する必要があるタイミングと頻度を制御できます。

### <a name="id-tokens"></a>ID トークン
ID トークンは、Web サイトとネイティブ クライアントに渡され、ユーザーに関するプロファイル情報を格納します。 ID トークンは、ユーザーとクライアントの特定の組み合わせにバインドされます。 ID トークンは、有効期限まで有効とみなされます。  通常、Web アプリケーションは、アプリケーションにおけるユーザーのセッションの有効期間と、ユーザーに対して発行された ID トークンの有効期間を照合します。  ID トークンの有効期間を調整すると、Web アプリケーションがアプリケーション セッションを期限切れにし、(自動的か対話形式で) Azure AD でユーザーを再認証するように要求する頻度を制御できます。

### <a name="single-sign-on-session-token"></a>シングル サインオン セッション トークン
ユーザーが Azure AD に対して認証し、[サインインしたままにする] チェックボックスをオンにすると、ユーザーのブラウザーと Azure AD でシングル サインオン セッションが確立されます。  シングル サインオン セッション トークンは、Cookie の形式でこのセッションを表します。 SSO セッション トークンは特定のリソース/クライアント アプリケーションにバインドされていないことに注意してください。 SSO セッション トークンは失効させることができ、使用時に必ず有効性がチェックされます。

SSO セッション トークンには&2; つの種類があります。 永続的なセッション トークンはブラウザーによって永続的な Cookie として格納され、非永続的なセッション トークンはセッション Cookie として格納されます (これらはブラウザーを閉じると破棄されます)。

非永続的なセッション トークンには 24 時間の有効期間があり、永続的なトークンには 180 日間の有効期間があります。 有効期間内に SSO セッション トークンを使用した時点で、有効期間はさらに 24 時間または 180 日間延長されます。 SSO セッション トークンが有効期間内に使用されない場合は、期限切れとみなされ、受け入れられなくなります。 

ポリシーを使用して、セッション トークンが受け付けられなくなる時点以降に、最初のセッション トークンの発行後の期間を設定できます (Session Token Max Age)。  セッション トークンの有効期間を調整すると、Web アプリケーションの使用時に、ユーザーが自動的に認証するのではなく、資格情報を再入力する必要があるタイミングと頻度を制御することができます。

### <a name="token-lifetime-policy-properties"></a>トークンの有効期間ポリシーのプロパティ
トークンの有効期間ポリシーとは、トークンの有効期間の規則が含まれる一種のポリシー オブジェクトです。  ポリシーのプロパティは、指定されたトークンの有効期間の制御に使用されます。  ポリシーが設定されていない場合は、既定の有効期間の値が適用されます。

### <a name="configurable-token-lifetime-properties"></a>構成可能なトークンの有効期間のプロパティ
| プロパティ | ポリシーのプロパティ文字列 | 影響 | 既定値 | 最小値 | 最大値 |
| --- | --- | --- | --- | --- | --- |
| Access Token Lifetime |AccessTokenLifetime |アクセス トークン、ID トークン、SAML2 トークン |1 時間 |10 分 |1 日 |
| Refresh Token Max Inactive Time |MaxInactiveTime |更新トークン |14 日 |10 分 |90 日間 |
| Single-Factor Refresh Token Max Age |MaxAgeSingleFactor |更新トークン (すべてのユーザー向け) |90 日間 |10 分 |Until-revoked* |
| Multi-Factor Refresh Token Max Age |MaxAgeMultiFactor |更新トークン (すべてのユーザー向け) |90 日間 |10 分 |Until-revoked* |
| Single-Factor Session Token Max Age |MaxAgeSessionSingleFactor** |セッション トークン (永続的および非永続的) |Until-revoked |10 分 |Until-revoked* |
| Multi-Factor Session Token Max Age |MaxAgeSessionMultiFactor*** |セッション トークン (永続的および非永続的) |Until-revoked |10 分 |Until-revoked* |

* * これらの属性に対して明示的に設定できる最大期間は&365; 日です。
* ** MaxAgeSessionSingleFactor が設定されていない場合、この値には MaxAgeSingleFactor 値が使用されます。 どちらのパラメーターも設定されていない場合、このプロパティは既定値 (Until-revoked) を使用します。
* *** MaxAgeSessionMultiFactor が設定されていない場合、この値には MaxAgeMultiFactor 値が使用されます。 どちらのパラメーターも設定されていない場合、このプロパティは既定値 (Until-revoked) を使用します。

### <a name="exceptions"></a>例外
| プロパティ | 影響 | 既定値 |
| --- | --- | --- |
| Refresh Token Max Inactive Time (失効情報が不十分なフェデレーション ユーザー) |更新トークン (失効情報が不十分なフェデレーション ユーザーに発行済み) |12 時間 |
| Refresh Token Max Inactive Time (Confidential クライアント) |更新トークン (Confidential クライアントに発行) |90 日間 |
| Refresh token Max Age (Confidential クライアントに発行) |更新トークン (Confidential クライアントに発行) |Until-revoked |

### <a name="priority-and-evaluation-of-policies"></a>ポリシーの優先順位と評価
トークンの有効期間ポリシーを作成し、特定のアプリケーション、組織、およびサービス プリンシパルに割り当てることができます。 つまり、複数のポリシーを特定のアプリケーションに適用できます。 有効になるトークンの有効期間ポリシーは、次の規則に従います。

* ポリシーが明示的にサービス プリンシパルに割り当てられている場合は、そのポリシーが適用されます。 
* 明示的にサービス プリンシパルに割り当てられているポリシーがない場合は、サービス プリンシパルの親組織に明示的に割り当てられているポリシーが適用されます。 
* サービス プリンシパルまたは組織に明示的に割り当てられているポリシーがない場合、アプリケーションに割り当てられているポリシーが適用されます。 
* サービス プリンシパル、組織、またはアプリケーション オブジェクトに割り当てられているポリシーがない場合は、既定値が適用されます (上記の表を参照してください)。

Azure AD のアプリケーション オブジェクトとサービス プリンシパル オブジェクトの関係の詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](active-directory-application-objects.md)」を参照してください。

トークンの有効性は、トークンの使用時に評価されます。 アクセスされているアプリケーションに対して、最も優先度が高いポリシーが有効になります。

> [!NOTE]
> 例
> 
> ユーザーは、A と B という 2 つの Web アプリケーションにアクセスしようとしています。 
> 
> * 両方のアプリケーションとも、同じ親組織にあります。 
> * Session Token Max Age を 8 時間に設定したトークンの有効期間ポリシー 1 が、親組織の既定値として設定されています。
> * Web アプリケーション A は、日常的に使用する Web アプリケーションで、どのポリシーにもリンクされていません。 
> * Web アプリケーション B は、機密性の高い処理に使用され、そのサービス プリンシパルは、Session Token Max Age が 30 分間に設定されているトークンの有効期間ポリシー 2 にリンクされています。
> 
> 正午に、ユーザーは新しいブラウザー セッションを開き、Web アプリケーション A へのアクセスを試みます。ユーザーは Azure AD にリダイレクトされ、サインインするよう指示されます。 これによって、セッション トークンを含む Cookie がブラウザーに保存されます。 ユーザーは、Web アプリケーション A にアクセスするための ID トークンによって、Web アプリケーション A にリダイレクトされます。
> 
> 午後 0 時 15 分に、ユーザーは Web アプリケーション B へのアクセスを試みます。ブラウザーは Azure AD にリダイレクトされ、そこでセッション Cookie が検出されます。 Web アプリケーション B のサービス プリンシパルは、ポリシー 2 にリンクされているだけでなく、既定のポリシー 1 が設定されている親組織の一部にもなっています。 サービス プリンシパルにリンクされているポリシーは、組織の既定のポリシーより優先順位が高いため、ポリシー 2 が有効になります。 セッション トークンが最初に発行されたのは、過去 30 分以内であるため、このトークンは有効とみなされます。 ユーザーは、アクセス権限を与える ID トークンによって Web アプリケーション B にリダイレクトされます。
> 
> 午後 1 時に、ユーザーは Web アプリケーション A への移動を試みます。ユーザーは、Azure AD にリダイレクトされます。 Web アプリケーション A は、どのポリシーにもリンクされていませんが、既定のポリシー 1 が設定された組織にあるため、このポリシーが有効になります。 過去 8 時間以内に最初に発行されたセッション Cookie が検出され、ユーザーは新しい ID トークンによって、自動的に Web アプリケーション A にリダイレクトされます。このとき、認証の必要はありません。
> 
> ユーザーは、直ちに Web アプリケーション B へのアクセスを試みます。ユーザーは、Azure AD にリダイレクトされます。 以前と同様に、ポリシー 2 が有効になります。 トークンが発行されてからの時間が 30 分を超えたため、ユーザーは資格情報を再入力するように求められ、まったく新しいセッションと ID トークンが発行されます。 これで、ユーザーは Web アプリケーション B にアクセスできます。
> 
> 

## <a name="configurable-policy-properties-in-depth"></a>構成可能なポリシーのプロパティ: 詳細
### <a name="access-token-lifetime"></a>Access token lifetime
**文字列:** AccessTokenLifetime

**影響:** アクセス トークン、ID トークン

**概要:** このポリシーは、このリソースのアクセス トークンと ID トークンが有効とみなされる期間を制御します。 アクセス トークンの有効期間を短くすると、(トークンを失効させることができないために) 悪意のあるアクターによってアクセス トークンまたは ID トークンが長期間使用されるリスクが軽減されますが、トークンが置き換えられる回数も増えるため、パフォーマンスに悪影響を与えます。

### <a name="refresh-token-max-inactive-time"></a>Refresh token max inactive time
**文字列:** MaxInactiveTime

**影響:** 更新トークン

**概要:** このポリシーは、更新トークンがどの程度古くなると、クライアントがこのリソースにアクセスするときに、新しいアクセス トークンと更新トークンのペアの取得に更新トークンを使用できなくするのかを制御します。 更新トークンを使用すると、通常は新しい更新トークンが返されるため、クライアントはこのポリシーがアクセスを禁止するまでの指定した期間に、現在の更新トークンを使用して、リソースにアクセスすることはありません。 

このポリシーは、クライアントでアクティブでなかったユーザーを強制的に新しい更新トークンを取得するよう再認証させることができます。 

Refresh Token Max Inactive Time は Single-Factor Token Max Age および Multi-Factor Refresh Token Max Age よりも小さな値に設定する必要があります。

### <a name="single-factor-refresh-token-max-age"></a>Single-Factor Refresh Token Max Age
**文字列:** MaxAgeSingleFactor

**影響:** 更新トークン

**概要:** このポリシーは、ユーザーが前回単一要素のみで認証に成功した後に、新しいアクセス トークンと更新トークンのペアを取得するために更新トークンを使い続けることができる期間を制御します。 ユーザーは認証して新しい更新トークンを受け取ると、指定された期間、更新トークンのフローを使用できます (ただし、現在の更新トークンが失効していない状態で、非アクティブな期間よりも長い期間使用されていない場合に限られます)。 その時点で、ユーザーは新しい更新トークンを受信するために再認証するよう強制されます。 

最長有効期間を短くすると、ユーザーに認証を強制する回数が多くなります。 単一要素認証は多要素認証より安全性が低いと考えられるため、このポリシーは、Multi-Factor Refresh Token Max Age ポリシー以下の値に設定することをお勧めします。

### <a name="multi-factor-refresh-token-max-age"></a>Multi-Factor Refresh Token Max Age
**文字列:** MaxAgeMultiFactor

**影響:** 更新トークン

**概要:** このポリシーは、ユーザーが前回多要素で認証に成功した後に、新しいアクセス トークンと更新トークンのペアを取得するために更新トークンを使い続けることができる期間を制御します。 ユーザーは認証して新しい更新トークンを受け取ると、指定された期間、更新トークンのフローを使用できます (ただし、現在の更新トークンが失効していない状態で、非アクティブな期間よりも長い期間使用されていない場合に限られます)。 その時点で、ユーザーは新しい更新トークンを受信するために再認証するよう強制されます。 

最長有効期間を短くすると、ユーザーに認証を強制する回数が多くなります。 単一要素認証は多要素認証より安全性が低いと考えられるため、このポリシーは、Single-Factor Refresh Token Max Age ポリシー以上の値に設定することをお勧めします。

### <a name="single-factor-session-token-max-age"></a>Single-Factor Session Token Max Age
**文字列:** MaxAgeSessionSingleFactor

**影響:** セッション トークン (永続的および非永続的)

**概要:** このポリシーは、ユーザーが前回単一要素のみで認証に成功した後に、新しい ID トークンとセッション トークンを取得するためにセッショントークンを使い続けることができる期間を制御します。 ユーザーは認証して新しいセッション トークンを受け取ると、指定された期間、セッション トークンのフローを使用できます (ただし、現在のセッション トークンが失効していない、または期限切れになっていない場合に限られます)。 その時点で、ユーザーは新しいセッション トークンを受信するために再認証するよう強制されます。 

最長有効期間を短くすると、ユーザーに認証を強制する回数が多くなります。 単一要素認証は多要素認証より安全性が低いと考えられるため、このポリシーは、Multi-Factor Session Token Max Age ポリシー以下の値に設定することをお勧めします。

### <a name="multi-factor-session-token-max-age"></a>Multi-Factor Session Token Max Age
**文字列:** MaxAgeSessionMultiFactor

**影響:** セッション トークン (永続的および非永続的)

**概要:** このポリシーは、ユーザーが前回多要素で認証に成功した後に、新しい ID トークンとセッション トークンを取得するためにセッショントークンを使い続けることができる期間を制御します。 ユーザーは認証して新しいセッション トークンを受け取ると、指定された期間、セッション トークンのフローを使用できます (ただし、現在のセッション トークンが失効していない、または期限切れになっていない場合に限られます)。 その時点で、ユーザーは新しいセッション トークンを受信するために再認証するよう強制されます。 

最長有効期間を短くすると、ユーザーに認証を強制する回数が多くなります。 単一要素認証は多要素認証より安全性が低いと考えられるため、このポリシーは、Single-Factor Session Token Max Age ポリシー以上の値に設定することをお勧めします。

## <a name="sample-token-lifetime-policies"></a>トークンの有効期間ポリシーのサンプル
アプリ、サービス プリンシパル、および組織全体のトークンの有効期間を作成および管理できる場合は、Azure AD で利用できるすべての種類の新しいシナリオを実行可能です。  以下では新しい規則を適用するのに役立つ、いくつかの一般的なポリシー シナリオについて説明します。

* トークンの有効期間
* トークンの最大非アクティブ時間
* トークンの最長有効期間

次を含む、いくつかのシナリオについて説明します。

* 組織の既定のポリシーの管理
* Web サインインのポリシーの作成
* Web API を呼び出すネイティブ アプリのポリシーの作成
* 詳細なポリシーの管理 

### <a name="prerequisites"></a>前提条件
このサンプル シナリオでは、アプリ、サービス プリンシパル、および組織全体に対するポリシーの作成、更新、リンク、削除を行います。  初めて Azure AD を使用する場合は、これらのサンプルを続行する前に[この記事](active-directory-howto-tenant.md)を確認して準備してください。  

1. まず、最新版の [Azure AD PowerShell コマンドレットのプレビュー](https://www.powershellgallery.com/packages/AzureADPreview)をダウンロードします。 
2. Azure AD PowerShell コマンドレットを入手したら、Azure AD 管理者アカウントにサインインする Connect コマンドを実行します。 新しいセッションを開始するたびに、これを実行する必要があります。
   
     Connect-AzureAD -Confirm
3. 組織に作成されているすべてのポリシーを表示するには、次のコマンドを実行します。  このコマンドは、次のシナリオでのほとんどの操作の後に使用する必要があります。  これは、ポリシーの**オブジェクト ID** を取得する場合にも役立ちます。 
   
     Get-AzureADPolicy

### <a name="sample-managing-a-organizations-default-policy"></a>サンプル: 組織の既定のポリシーの管理
このサンプルでは、組織全体でユーザーがサインインする頻度を低くするポリシーを作成します。 

そのためには、組織全体に適用される単一要素の更新トークンに対してトークンの有効期間ポリシーを作成します。 このポリシーは、組織内のすべてのアプリケーションと、ポリシーがまだ設定されていない各サービス プリンシパルに適用されます。 

1. **トークンの有効期間ポリシーを作成します。** 

単一要素の更新トークンに "until-revoked" を設定します。これはアクセスが取り消されるまで期限切れにならないという意味です。  次のポリシー定義をこれから作成していきます。

        @("{
          `"TokenLifetimePolicy`":
              {
                 `"Version`":1, 
                 `"MaxAgeSingleFactor`":`"until-revoked`"
              }
        }")

その後、次のコマンドを実行してポリシーを作成します。 

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1, `"MaxAgeSingleFactor`":`"until-revoked`"}}") -DisplayName OrganizationDefaultPolicyScenario -IsOrganizationDefault $true -Type TokenLifetimePolicy

新しいポリシーを表示し、その ObjectID を取得するには、次のコマンドを実行します。

    Get-AzureADPolicy
&nbsp;&nbsp;2.  **ポリシーを更新します。**

最初のポリシーはサービスで要求されるほど厳格にはしないで、単一要素の更新トークンを 2 日で期限切れにするとします。 次のコマンドを実行します。 

    Set-AzureADPolicy -ObjectId <ObjectID FROM GET COMMAND> -DisplayName OrganizationDefaultPolicyUpdatedScenario -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"2.00:00:00`"}}")

&nbsp;&nbsp;3.**これで完了です。** 

### <a name="sample-creating-a-policy-for-web-sign-in"></a>サンプル: Web サインインのポリシーの作成
このサンプルでは、ユーザーが頻繁に Web アプリに対して認証する必要があるポリシーを作成します。 このポリシーでは、アクセス トークンと ID トークンの有効期間と、多要素セッション トークンの最長有効期間を Web アプリのサービス プリンシパルに設定します。

1. **トークンの有効期間ポリシーを作成します。**

この Web サインイン用のポリシーでは、アクセス トークンと ID トークンの有効期間と、多要素セッション トークンの最長有効期間を 2 時間に設定します。

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"AccessTokenLifetime`":`"02:00:00`",`"MaxAgeSessionSingleFactor`":`"02:00:00`"}}") -DisplayName WebPolicyScenario -IsOrganizationDefault $false -Type TokenLifetimePolicy

新しいポリシーを表示し、その ObjectID を取得するには、次のコマンドを実行します。

    Get-AzureADPolicy
&nbsp;&nbsp;2.  **サービス プリンシパルにポリシーを割り当てます。**

この新しいポリシーをサービス プリンシパルにリンクします。  サービス プリンシパルの **ObjectId** にアクセスする方法も必要です。 [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) に対してクエリを実行するか、[Graph エクスプローラー ツール](https://graphexplorer.cloudapp.net/)に移動して Azure AD アカウントにサインインし、組織のすべてのサービス プリンシパルを表示できます。 

**ObjectId** を取得したら、次のコマンドを実行します。

    Add-AzureADServicePrincipalPolicy -ObjectId <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
&nbsp;&nbsp;3.  **これで完了です。** 

 

### <a name="sample-creating-a-policy-for-native-apps-calling-a-web-api"></a>サンプル: Web API を呼び出すネイティブ アプリのポリシーの作成
このサンプルでは、ユーザーの認証の頻度を低くし、再認証が不要な非アクティブな時間を長くするポリシーを作成します。 ポリシーは Web API に適用されるため、ネイティブ アプリがリソースとして Web API を要求すると、このポリシーが適用されます。

1. **トークンの有効期間ポリシーを作成します。** 

このコマンドでは、Web API の厳格なポリシーが作成されます。 

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"30.00:00:00`",`"MaxAgeMultiFactor`":`"until-revoked`",`"MaxAgeSingleFactor`":`"180.00:00:00`"}}") -DisplayName WebApiDefaultPolicyScenario -IsOrganizationDefault $false -Type TokenLifetimePolicy

新しいポリシーを表示し、その ObjectID を取得するには、次のコマンドを実行します。

    Get-AzureADPolicy

&nbsp;&nbsp;2.  **Web API にポリシーを割り当てます**。

この新しいポリシーをアプリケーションにリンクします。  アプリケーションの **ObjectId** にアクセスする方法も必要です。 [Azure Portal](https://portal.azure.com/) を使用すると、最も効果的にアプリの **ObjectId** を検索できます。 

**ObjectId** を取得したら、次のコマンドを実行します。

    Add-AzureADApplicationPolicy -ObjectId <ObjectID of the App> -RefObjectId <ObjectId of the Policy>

&nbsp;&nbsp;3.  **これで完了です。** 

### <a name="sample-managing-an-advanced-policy"></a>サンプル: 詳細なポリシーの管理
このサンプルでは、優先度の体系の仕組みと、オブジェクトに適用される複数のポリシーの管理方法を説明するために、いくつかのポリシーを作成します。 このサンプルでは、以前に説明したポリシーの優先度について深く理解し、より複雑なシナリオの管理もできるようになります。 

1. **トークンの有効期間ポリシーを作成します。**

ここまでは、非常に簡単です。 既に、単一要素の更新トークンの有効期間を 30 日間に設定する組織の既定のポリシーを作成しました。 

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"30.00:00:00`"}}") -DisplayName ComplexPolicyScenario -IsOrganizationDefault $true -Type TokenLifetimePolicy
新しいポリシーを表示し、その ObjectID を取得するには、次のコマンドを実行します。

    Get-AzureADPolicy

&nbsp;&nbsp;2.  **サービス プリンシパルにポリシーを割り当てます。**

これで、組織全体にポリシーが設定されました。  ここで、特定のサービス プリンシパルに対してはこの 30 日間のポリシーを保持しますが、組織の既定のポリシーを "until-revoked" の上限となるよう変更するとします。 

最初に、この新しいポリシーをサービス プリンシパルにリンクします。  サービス プリンシパルの **ObjectId** にアクセスする方法も必要です。 [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) に対してクエリを実行するか、[Graph エクスプローラー ツール](https://graphexplorer.cloudapp.net/)に移動して Azure AD アカウントにサインインし、組織のすべてのサービス プリンシパルを表示できます。 

**ObjectId** を取得したら、次のコマンドを実行します。

    Add-AzureADServicePrincipalPolicy -ObjectId <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>

&nbsp;&nbsp;3.  **次のコマンドを使用して IsOrganizationDefault フラグを false に設定します**。 

    Set-AzureADPolicy -ObjectId <ObjectId of Policy> -DisplayName ComplexPolicyScenario -IsOrganizationDefault $false
&nbsp;&nbsp;4.  **新しい組織の既定のポリシーを作成します**。

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"until-revoked`"}}") -DisplayName ComplexPolicyScenarioTwo -IsOrganizationDefault $true -Type TokenLifetimePolicy

&nbsp;&nbsp;5.   **これで完了です。** 

これで、元のポリシーがサービス プリンシパルにリンクされ、新しいポリシーが組織の既定のポリシーとして設定されました。  サービス プリンシパルに適用されるポリシーが、組織の既定のポリシーよりも優先されることに注意してください。 

## <a name="cmdlet-reference"></a>コマンドレット リファレンス
### <a name="manage-policies"></a>ポリシーの管理
次のコマンドレットを使用して、ポリシーを管理できます。</br></br>

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy
新しいポリシーを作成します。

    New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type> 

| parameters | 説明 | 例 |
| --- | --- | --- |
| -Definition |ポリシーのすべてのルールが含まれる文字列化された JSON の配列。 |-Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"20:00:00`"}}") |
| -DisplayName |ポリシー名の文字列 |-DisplayName MyTokenPolicy |
| -IsOrganizationDefault |true の場合は組織の既定のポリシーとしてポリシーを設定し、false の場合は何もしません。 |-IsOrganizationDefault $true |
| -Type |ポリシーの種類。トークンの有効期間に対しては、常に "TokenLifetimePolicy" を使用します。 |-Type TokenLifetimePolicy |
| -AlternativeIdentifier [省略可能] |ポリシーに代替の ID を設定します。 |-AlternativeIdentifier myAltId |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
すべての AzureAD ポリシーまたは指定されたポリシーを取得します。 

    Get-AzureADPolicy 

| parameters | 説明 | 例 |
| --- | --- | --- |
| -ObjectId [省略可能] |取得するポリシーのオブジェクト ID。 |-ObjectId &lt;ポリシーの ObjectID&gt; |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get AzureADPolicyAppliedObject
ポリシーにリンクされたすべてのアプリとサービス プリンシパルを取得します。

    Get-AzureADPolicyAppliedObject -ObjectId <object id of policy> 

| parameters | 説明 | 例 |
| --- | --- | --- |
| -ObjectId |取得するポリシーのオブジェクト ID。 |-ObjectId &lt;ポリシーの ObjectID&gt; |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
既存のポリシーを更新します。

    Set-AzureADPolicy -ObjectId <object id of policy> -DisplayName <string> 

| parameters | 説明 | 例 |
| --- | --- | --- |
| -ObjectId |取得するポリシーのオブジェクト ID。 |-ObjectId &lt;ポリシーの ObjectID&gt; |
| -DisplayName |ポリシー名の文字列 |-DisplayName MyTokenPolicy |
| -Definition [省略可能] |ポリシーのすべてのルールが含まれる文字列化された JSON の配列。 |-Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"20:00:00`"}}") |
| -IsOrganizationDefault [省略可能] |true の場合は組織の既定のポリシーとしてポリシーを設定し、false の場合は何もしません。 |-IsOrganizationDefault $true |
| -Type [省略可能] |ポリシーの種類。トークンの有効期間に対しては、常に "TokenLifetimePolicy" を使用します。 |-Type TokenLifetimePolicy |
| -AlternativeIdentifier [省略可能] |ポリシーに代替の ID を設定します。 |-AlternativeIdentifier myAltId |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
指定したポリシーを削除します。

     Remove-AzureADPolicy -ObjectId <object id of policy>

| parameters | 説明 | 例 |
| --- | --- | --- |
| -ObjectId |取得するポリシーのオブジェクト ID。 |-ObjectId &lt;ポリシーの ObjectID&gt; |

</br></br>

### <a name="application-policies"></a>アプリケーション ポリシー
次のコマンドレットが、アプリケーション ポリシーに対して使用できます。</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
指定したポリシーをアプリケーションにリンクします。

    Add-AzureADApplicationPolicy -ObjectId <object id of application> -RefObjectId <object id of policy>

| parameters | 説明 | 例 |
| --- | --- | --- |
| -ObjectId |アプリケーションのオブジェクト ID。 |-ObjectId &lt;アプリケーションの ObjectID&gt; |
| -RefObjectId |ポリシーのオブジェクト ID。 |-RefObjectId &lt;ポリシーの ObjectID&gt; |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
アプリケーションに割り当てられているポリシーを取得します。

    Get-AzureADApplicationPolicy -ObjectId <object id of application>

| parameters | 説明 | 例 |
| --- | --- | --- |
| -ObjectId |アプリケーションのオブジェクト ID。 |-ObjectId &lt;アプリケーションの ObjectID&gt; |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
アプリケーションからポリシーを削除します。

    Remove-AzureADApplicationPolicy -ObjectId <object id of application> -PolicyId <object id of policy>

| parameters | 説明 | 例 |
| --- | --- | --- |
| -ObjectId |アプリケーションのオブジェクト ID。 |-ObjectId &lt;アプリケーションの ObjectID&gt; |
| -PolicyId |ポリシーの ObjectID。 |-PolicyId &lt;ポリシーの ObjectID&gt; |

</br></br>

### <a name="service-principal-policies"></a>サービス プリンシパル ポリシー
次のコマンドレットが、サービス プリンシパル ポリシーに対して使用できます。</br></br>

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
指定したポリシーをサービス プリンシパルにリンクします。

    Add-AzureADServicePrincipalPolicy -ObjectId <object id of service principal> -RefObjectId <object id of policy>

| parameters | 説明 | 例 |
| --- | --- | --- |
| -ObjectId |アプリケーションのオブジェクト ID。 |-ObjectId &lt;アプリケーションの ObjectID&gt; |
| -RefObjectId |ポリシーのオブジェクト ID。 |-RefObjectId &lt;ポリシーの ObjectID&gt; |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
指定したサービス プリンシパルにリンクされている任意のポリシーを取得します。

    Get-AzureADServicePrincipalPolicy -ObjectId <object id of service principal>

| parameters | 説明 | 例 |
| --- | --- | --- |
| -ObjectId |アプリケーションのオブジェクト ID。 |-ObjectId &lt;アプリケーションの ObjectID&gt; |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
指定したサービス プリンシパルからポリシーを削除します。

    Remove-AzureADServicePrincipalPolicy -ObjectId <object id of service principal>  -PolicyId <object id of policy>

| parameters | 説明 | 例 |
| --- | --- | --- |
| -ObjectId |アプリケーションのオブジェクト ID。 |-ObjectId &lt;アプリケーションの ObjectID&gt; |
| -PolicyId |ポリシーの ObjectID。 |-PolicyId &lt;ポリシーの ObjectID&gt; |




<!--HONumber=Jan17_HO3-->


