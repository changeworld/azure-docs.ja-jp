---
title: Azure Active Directory の条件付きアクセスの条件 | Microsoft Docs
description: Azure Active Directory の条件付きアクセスで割り当てを使用してポリシーをトリガーする方法について学習します。
services: active-directory
keywords: アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/01/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 3cb8e598864bccfbea24a2aec5d9387ff903e51c
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/03/2018
ms.locfileid: "32770623"
---
# <a name="conditions-in-azure-active-directory-conditional-access"></a>Azure Active Directory の条件付きアクセスの条件 

[Azure Active Directory (Azure AD) の条件付きアクセス](active-directory-conditional-access-azure-portal.md)を使うと、承認されたユーザーがどのようにクラウド アプリにアクセスするかを制御できます。 条件付きアクセス ポリシーでは、ポリシーをトリガーする理由 ("～が発生した場合") に対する応答 ("～を実行する") を定義します。 

![コントロール](./media/active-directory-conditional-access-conditions/10.png)


条件付きアクセスの文脈では、

- "**When this happens**" (これが発生した場合は) を**条件**と呼びます。 
- "**Then do this**" (これを実行する) を**アクセス制御**と呼びます。

条件とアクセスの制御の組み合わせによって、条件付きアクセス ポリシーを表現します。

![コントロール](./media/active-directory-conditional-access-conditions/61.png)


条件付きアクセス ポリシーで構成していない条件は適用されません。 一部の条件は、条件付きアクセス ポリシーを環境に適用するために[必須](active-directory-conditional-access-best-practices.md#whats-required-to-make-a-policy-work)です。 

この記事では、条件の概要と、条件付きアクセス ポリシーでの使用方法について説明します。 

## <a name="users-and-groups"></a>[概要]

ユーザーとグループの条件は、条件付きアクセス ポリシーに必須です。 ポリシーでは、**[すべてのユーザー]** を選択するか、特定のユーザーとグループを選択することができます。

![コントロール](./media/active-directory-conditional-access-conditions/111.png)

選択できるオプションは次のとおりです。

- **すべてのユーザー**: ディレクトリ内のすべてのユーザーにポリシーを適用します。 これには、ゲスト ユーザーも含まれます。

- **ユーザーとグループの選択**: 次のオプションを設定できます。

    - **すべてのゲスト ユーザー**: ポリシーのターゲットを B2B ゲスト ユーザーに設定することができます。 この条件は、*userType* 属性が *guest* に設定されている任意のユーザー アカウントと一致します。 この設定は、Azure AD の招待フローでアカウントが作成されたときにすぐにポリシーを適用する必要がある場合に使用できます。

    - **ディレクトリ ロール**: ユーザーのロールの割り当てに基づいてポリシーのターゲットを設定できます。 この条件は、*グローバル管理者*または*パスワード管理者*などのディレクトリ ロールをサポートします。

    - **ユーザーとグループ**: 特定のユーザー セットのターゲットを設定することができます。 たとえば、人事部アプリがクラウド アプリとして選択されている場合は、人事部のすべてのメンバーを含むグループを選択できます。

グループ: Azure Active Directory 内の任意の種類のグループを指定できます。これには、動的なグループや、割り当て済みのセキュリティ グループおよび配布グループが含まれます。

ポリシーから特定のユーザーまたはグループを除外することもできます。 一般的なユース ケースとして、サービス アカウントに対してポリシーで多要素認証 (MFA) を強制的に適用する例が挙げられます。 

新しいポリシーをデプロイする際は、特定のユーザーのセットをターゲットにすると役立ちます。 新しいポリシーでは、初期のユーザー セットのみをターゲットにして、ポリシーの動作を検証することをお勧めします。 



## <a name="cloud-apps"></a>クラウド アプリ 

クラウド アプリは Web サイトまたは Web サービスです。 これには、Azure アプリケーション プロキシで保護された Web サイトも含まれます。 サポートされているクラウド アプリの詳細については、「[クラウド アプリの割り当て](active-directory-conditional-access-technical-reference.md#cloud-apps-assignments)」をご覧ください。    

クラウド アプリの条件は、条件付きアクセス ポリシーに必須です。 ポリシーでは、**[すべてのクラウド アプリ]** を選択するか、特定のクラウド アプリを選択することができます。

![コントロール](./media/active-directory-conditional-access-conditions/03.png)

選択できるオプションは次のとおりです。

- **すべてのクラウド アプリ**: ベースライン ポリシーを組織全体に適用できます。 このオプションの一般的なユース ケースとしては、いずれかのクラウド アプリへのサインインにリスクが検出されたときに多要素認証を要求するポリシーが挙げられます。 **[すべてのクラウド アプリ]** に適用されるポリシーは、すべての Web サイトおよびサービスへのアクセスに適用されます。 この設定は、**[アプリを選択]** リストに表示されるクラウド アプリに限定されません。

- 個々のクラウド アプリ: ポリシーで特定のサービスをターゲットにすることができます。 たとえば、SharePoint Online にアクセスするには[準拠デバイス](active-directory-conditional-access-policy-connected-applications.md)を使用するようユーザーに要求できます。 このポリシーは、他のサービスが Microsoft Teams などの SharePoint コンテンツにアクセスする際にも適用されます。 

特定のアプリをポリシーから除外することもできます。ただし、アクセスするサービスに適用されるポリシーがあれば、これらのアプリも影響を受けます。 



## <a name="sign-in-risk"></a>サインイン リスク

サインイン リスクは、サインイン試行がユーザー アカウントの正当な所有者によって行われなかった可能性のレベル (高、中、低) を示します。 Azure Active Directory では、ユーザーのサインイン時にサインイン リスク レベルを計算します。 計算されたサインイン リスク レベルを条件付きアクセス ポリシーの条件として利用できます。 

![条件](./media/active-directory-conditional-access-conditions/22.png)

この条件を使用するには、[Azure Active Directory Identity Protection](active-directory-identityprotection.md) を有効にする必要があります。
 
この条件の一般的なユース ケースには、次のようなものがあります。

- サインイン リスクが高いユーザーをブロックし、正当でない可能性のあるユーザーがクラウド アプリにアクセスするのを防止する。 
- サインイン リスクが中であるユーザーに多要素認証を要求する。 多要素認証を強制的に適用することで、サインインがアカウントの正当な所有者によって実行されていることを確認し、信頼性を強化できます。

詳細については、「[リスクの高いサインイン](active-directory-identityprotection.md#risky-sign-ins)」を参照してください。  

## <a name="device-platforms"></a>デバイス プラットフォーム

デバイス プラットフォームは、デバイスで実行されているオペレーティング システムによって分類されます。 Azure Active Directory では、ユーザー エージェントなどのデバイスによって提供される情報を使用して、プラットフォームを識別します。 この情報は検証されていないため、すべてのプラットフォームに対して、アクセスをブロックする、Intune ポリシーへの準拠を要求する、またはデバイスをドメインに参加するよう要求するポリシーを適用することをお勧めします。 既定では、すべてのデバイス プラットフォームにポリシーが適用されます。 


![条件](./media/active-directory-conditional-access-conditions/24.png)

サポートされているデバイス プラットフォームの一覧については、「[デバイス プラットフォームの条件](active-directory-conditional-access-technical-reference.md#device-platform-condition)」をご覧ください。


この条件の一般的なユース ケースとしては、クラウド アプリへのアクセスを[マネージド デバイス](active-directory-conditional-access-policy-connected-applications.md#managed-devices)のみに制限するポリシーが挙げられます。 デバイス プラットフォームの条件を使用するその他のシナリオについては、「[Azure Active Directory のアプリベースの条件付きアクセス](active-directory-conditional-access-mam.md)」をご覧ください。



## <a name="device-state"></a>デバイスの状態

デバイスの状態の条件で、Hybrid Azure AD の参加を許可し、準拠とマークされたデバイスを条件付きアクセス ポリシーから除外することができます。 この処理は、追加のセッション セキュリティを提供するために、アンマネージド デバイスにのみポリシーを適用する必要がある場合に便利です。 たとえば、アンマネージド デバイスの場合にのみ、Microsoft Cloud App Security のセッション制御を実施します。 


![条件](./media/active-directory-conditional-access-conditions/112.png)

アンマネージド デバイスのアクセスをブロックする場合は、[デバイスベースの条件付きアクセス](active-directory-conditional-access-policy-connected-applications.md)を実装することをお勧めします。


## <a name="locations"></a>場所

場所では、接続試行が開始された場所に基づいた条件を定義するオプションがあります。 
     
![条件](./media/active-directory-conditional-access-conditions/25.png)

この条件の一般的なユース ケースには、次のようなものがあります。

- ユーザーが社内ネットワークから離れているときにサービスにアクセスする場合に、多要素認証を必須にする。  

- ユーザーが特定の国や地域からサービスにアクセスする場合にアクセスをブロックする。 

詳細については、「[Azure Active Directory の条件付きアクセスの場所の条件](active-directory-conditional-access-locations.md)」をご覧ください。


## <a name="client-apps"></a>クライアント アプリ

クライアント アプリの条件を使用すると、次のような各種アプリケーションにポリシーを適用することができます。

- Web サイトと Web サービス
- モバイル アプリとデスクトップ アプリ 

![条件](./media/active-directory-conditional-access-conditions/04.png)

アプリケーションは、次のように分類されます。

- Web サイトと Web サービス: 機密クライアントに対して Web SSO プロトコル (SAML、WS-Fed、OpenID Connect) を使用する場合。

- モバイル アプリまたはデスクトップ アプリ: ネイティブ クライアントに対してモバイル アプリ向けの OpenID Connect を使用する場合。

条件付きアクセス ポリシーで使用できるクライアント アプリの一覧については、「[Azure Active Directory の条件付きアクセスに関するテクニカル リファレンス](active-directory-conditional-access-technical-reference.md#client-apps-condition)」をご覧ください。

この条件の一般的なユース ケースには、次のようなものがあります。

- モバイル アプリやデスクトップ アプリが大量のデータをデバイスにダウンロードする際は[準拠デバイス](active-directory-conditional-access-policy-connected-applications.md)の使用を必須にする一方で、ブラウザー アクセスはすべてのデバイスに許可する。

- Web アプリからのアクセスはブロックするが、モバイル アプリとデスクトップ アプリからのアクセスは許可する。

Web SSO や最新の認証プロトコルを使用できるだけでなく、ほとんどのスマートフォン上のネイティブ メール アプリなど、Exchange ActiveSync を使用するメール アプリケーションにこの条件を適用できます。 現時点では、従来のプロトコルを使用するクライアント アプリでは、Active Directory フェデレーション サービス (AD FS) を使用してセキュリティ保護する必要があります。

 詳細については、次を参照してください。

- [SharePoint Online と Exchange Online に Azure Active Directory の条件付きアクセスを設定する](active-directory-conditional-access-no-modern-authentication.md)
 
- [Azure Active Directory のアプリベースの条件付きアクセス](active-directory-conditional-access-mam.md) 








## <a name="next-steps"></a>次の手順

- 条件付きアクセスポリシーの構成方法については、「[Azure Active Directory での条件付きアクセスの基本](active-directory-conditional-access-azure-portal-get-started.md)」を参照してください。

- 環境に適用する条件付きアクセス ポリシーを構成する準備ができたら、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](active-directory-conditional-access-best-practices.md)」を参照してください。 

