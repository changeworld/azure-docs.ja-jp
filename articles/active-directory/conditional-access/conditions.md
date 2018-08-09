---
title: Azure Active Directory 条件付きアクセスの条件の概要 | Microsoft Docs
description: Azure Active Directory の条件付きアクセスで条件を使用してポリシーをトリガーする方法について学習します。
services: active-directory
keywords: アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 5f5e2051f9c67fa4e37ce0e1213e14e197222f05
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39627544"
---
# <a name="what-are-conditions-in-azure-active-directory-conditional-access"></a>Azure Active Directory 条件付きアクセスの条件の概要 

[Azure Active Directory (Azure AD) の条件付きアクセス](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-azure-portal)を使用して、承認されたユーザーがどのようにクラウド アプリにアクセスするかを制御できます。 条件付きアクセス ポリシーでは、ポリシーをトリガーする理由に対する応答を定義します。 応答の一例として **Then do this** (その場合はこの処理を実行する) があります。 理由の一例として **When this happens** (この状況になった場合) があります。

![理由と応答](./media/conditions/10.png)


条件付きアクセスのコンテキストで、**When this happens** (この状況になった場合) は**条件**と呼ばれます。 **Then do this** (その場合はこの処理を実行する) は**アクセス制御**と呼ばれます。 条件とアクセスの制御の組み合わせによって、条件付きアクセス ポリシーを表現します。

![条件付きアクセス ポリシー](./media/conditions/61.png)


条件付きアクセス ポリシーで構成していない条件は適用されません。 一部の条件は、条件付きアクセス ポリシーを環境に適用するために[必須](best-practices.md)です。 

この記事では、条件の概要と、条件付きアクセス ポリシーでの使用方法について説明します。 

## <a name="users-and-groups"></a>[概要]

ユーザーとグループの条件は、条件付きアクセス ポリシーに必須です。 ポリシーでは、**[すべてのユーザー]** を選択するか、特定のユーザーとグループを選択することができます。

![[概要]](./media/conditions/111.png)

**[すべてのユーザー]** を選択すると、ゲスト ユーザーを含め、ディレクトリ内のすべてのユーザーにポリシーが適用されます。

**[ユーザーとグループの選択]** を選択すると、次のオプションを設定できます。

* **[すべてのゲスト ユーザー]** では、ポリシーのターゲットを B2B ゲスト ユーザーに設定することができます。 この条件は、**userType** 属性が **guest** に設定されている任意のユーザー アカウントと一致します。 この設定は、Azure AD の招待フローでアカウントが作成されたときにすぐにポリシーを適用する必要がある場合に使用できます。

* **[ディレクトリ ロール]** では、ユーザーのロールの割り当てに基づいてポリシーのターゲットを設定できます。 この条件は、**グローバル管理者**または**パスワード管理者**などのディレクトリ ロールをサポートします。

* **[ユーザーとグループ]** は、特定セットのユーザーをターゲットとしています。 たとえば、人事部アプリがクラウド アプリとして選択されている場合は、人事部のすべてのメンバーを含むグループを選択できます。 Azure AD 内の任意の種類のグループを指定できます。これには、動的なグループや、割り当て済みのセキュリティ グループおよび配布グループが含まれます。

ポリシーから特定のユーザーまたはグループを除外することもできます。 一般的なユース ケースとして、サービス アカウントに対してポリシーで多要素認証 (MFA) を強制的に適用する例が挙げられます。 

新しいポリシーをデプロイする際は、特定のユーザーのセットをターゲットにすると役立ちます。 新しいポリシーでは、初期のユーザー セットのみをターゲットにして、ポリシーの動作を検証することをお勧めします。 



## <a name="cloud-apps"></a>クラウド アプリ 

クラウド アプリは Web サイトまたは Web サービスです。 Azure AD アプリケーション プロキシによって保護されている Web サイトもクラウド アプリです。 サポートされているクラウド アプリの詳細については、[クラウド アプリの割り当て](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-technical-reference#cloud-apps-assignments)に関する記事を参照してください。 

**クラウド アプリ**の条件は、条件付きアクセス ポリシーに必須です。 ポリシーでは、**[すべてのクラウド アプリ]** を選択するか、特定のクラウド アプリを選択することができます。

![クラウド アプリを含める](./media/conditions/03.png)

- ベースライン ポリシーを組織全体に適用するには、**[すべてのクラウド アプリ]** を選択します。 いずれかのクラウド アプリへのサインインにリスクが検出されたときに多要素認証を要求するポリシーの場合に、この設定を選択します。 **[すべてのクラウド アプリ]** に適用されるポリシーは、すべての Web サイトおよびサービスへのアクセスに適用されます。 この設定は、**[アプリを選択]** リストに表示されるクラウド アプリに限定されません。 

- ポリシーで特定のサービスをターゲットにするには、個々のクラウド アプリを選択します。 たとえば、SharePoint Online にアクセスするには[準拠デバイス](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online)を使用するようユーザーに要求できます。 このポリシーは、他のサービスが SharePoint コンテンツにアクセスする際にも適用されます。 たとえば、Microsoft Teams などです。 

ポリシーから特定のアプリを除外できます。 ただし、アクセスするサービスに適用されるポリシーがあれば、これらのアプリも影響を受けます。 



## <a name="sign-in-risk"></a>サインイン リスク

サインイン リスクは、サインイン試行がユーザー アカウントの正当な所有者によって行われなかった可能性のレベル (高、中、低) を示します。 Azure AD では、ユーザーのサインイン時にサインイン リスク レベルを計算します。 計算されたサインイン リスク レベルを条件付きアクセス ポリシーの条件として利用できます。 

![サインインのリスク レベル](./media/conditions/22.png)

この条件を使用するには、[Azure Active Directory Identity Protection](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-identityprotection-enable) を有効にする必要があります。
 
この条件の一般的なユース ケースには、次のような保護を含むポリシーがあります。 

- サインイン リスクが高いユーザーをブロックする。 このような保護で、正当ではない可能性があるユーザーがクラウド アプリにアクセスすることを防ぐことができます。 
- サインイン リスクが中であるユーザーに多要素認証を要求する。 多要素認証を強制的に適用することで、サインインがアカウントの正当な所有者によって実行されていることを確認し、信頼性を強化できます。

詳細については、「[リスクの高いサインイン](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-security-risky-sign-ins)」を参照してください。  

## <a name="device-platforms"></a>デバイス プラットフォーム

デバイス プラットフォームは、デバイスで実行されているオペレーティング システムによって分類されます。 Azure Active Directory では、ユーザー エージェントなどのデバイスによって提供される情報を使用して、プラットフォームを識別します。 この情報は検証されません。 すべてのプラットフォームにポリシーを適用することをお勧めします。 ポリシーでは、アクセスをブロックするか、Microsoft Intune ポリシーへの準拠を必須にするか、デバイスのドメインへの参加を必須にします。 既定では、すべてのデバイス プラットフォームにポリシーが適用されます。 


![デバイス プラットフォームを構成する](./media/conditions/24.png)

サポートされているデバイス プラットフォームの一覧については、[デバイス プラットフォームの条件](technical-reference.md#device-platform-condition)に関する記事を参照してください。


この条件の一般的なユース ケースとしては、クラウド アプリへのアクセスを[マネージド デバイス](require-managed-devices.md)のみに制限するポリシーが挙げられます。 デバイス プラットフォームの条件を使用するその他のシナリオについては、「[Azure Active Directory のアプリベースの条件付きアクセス](app-based-conditional-access.md)」をご覧ください。



## <a name="device-state"></a>デバイスの状態

デバイスの状態の条件では、ハイブリッド Azure AD 参加デバイスと、準拠とマークされたデバイスを条件付きアクセス ポリシーから除外します。 この条件は、追加のセッション セキュリティを提供するために、アンマネージド デバイスにのみポリシーを適用する必要がある場合に便利です。 たとえば、アンマネージド デバイスの場合にのみ、Microsoft Cloud App Security のセッション制御を実施します。 


![デバイスの状態を構成する](./media/conditions/112.png)

アンマネージド デバイスのアクセスをブロックする場合は、[デバイスベースの条件付きアクセス](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online)を実装します。


## <a name="locations"></a>場所

場所を使用すると、接続が試行された場所に基づいて条件を定義できます。 

![場所を構成する](./media/conditions/25.png)

この条件の一般的なユース ケースには、次のような保護を含むポリシーがあります。

- ユーザーが社内ネットワークから離れているときにサービスにアクセスする場合に、多要素認証を必須にする。  

- ユーザーが特定の国や地域からサービスにアクセスする場合にアクセスをブロックする。 

詳細については、「[Azure Active Directory 条件付きアクセスの場所の条件の概要](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)」を参照してください。


## <a name="client-apps"></a>クライアント アプリ

クライアント アプリの条件を使用すると、さまざまなアプリケーションにポリシーを適用することができます。 たとえば、Web サイト、サービス、モバイル アプリ、デスクトップ アプリケーションなどです。 



アプリケーションは次のように分類されます。

- Web サイトまたは Web サービス: 機密クライアントに対して Web SSO プロトコル (SAML、WS-Fed、OpenID Connect) を使用する場合。

- モバイル アプリまたはデスクトップ アプリケーション: ネイティブ クライアントに対してモバイル アプリ向けの OpenID Connect を使用する場合。

条件付きアクセス ポリシーで使用できるクライアント アプリの一覧については、「Azure Active Directory の条件付きアクセス設定に関するリファレンス」の「[クライアント アプリの条件](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#client-apps-condition)」を参照してください。

この条件の一般的なユース ケースには、次のような保護を含むポリシーがあります。 

- モバイル アプリやデスクトップ アプリケーションが大量のデータをデバイスにダウンロードする際に[準拠デバイス](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online)の使用を必須にする。 同時に、ブラウザー アクセスはすべてのデバイスに許可する。

- Web アプリからのアクセスはブロックするが、モバイル アプリとデスクトップ アプリからのアクセスは許可する。

この条件は、Web SSO および先進認証プロトコルに適用できます。 また、Microsoft Exchange ActiveSync を使用するメール アプリケーションに適用することもできます。 たとえば、ほとんどのスマートフォンのネイティブ メール アプリです。 

このクラウド アプリの条件は、選択しているクラウド アプリが Microsoft Office 365 Exchange Online の場合にのみ選択できます。

![クラウド アプリ](./media/conditions/32.png)

クライアント アプリの条件としての **Exchange ActiveSync** は、ポリシーに他の条件が構成されていない場合にのみ選択できます。 ただし、この条件の範囲を絞り込んで、サポートされるプラットフォームのみに適用できます。

 
![サポートされているプラットフォームのみにポリシーを適用する](./media/conditions/33.png)

この条件をサポートされるプラットフォームのみに適用することは、[デバイス プラットフォームの条件](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-conditional-access-mam#app-based-or-compliant-device-policy-for-exchange-online-and-sharepoint-online)のすべてのデバイス プラットフォームに相当します。

![デバイス プラットフォームを構成する](./media/conditions/34.png)


 詳細と例については、次の記事をご覧ください。

- [SharePoint Online と Exchange Online に Azure Active Directory の条件付きアクセスを設定する](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication)。
 
- [Azure Active Directory のアプリベースの条件付きアクセス](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam)。 


### <a name="legacy-authentication"></a>レガシ認証  

条件付きアクセスは、先進認証をサポートしていない旧バージョンの Microsoft Office クライアントに適用されるようになりました。 また、POP、IMAP、SMTP などのメール プロトコルを使用するクライアントにも適用されます。 レガシ認証を使用すると、**他のクライアントからのアクセスをブロックする**などのポリシーを構成できます。


![クライアント アプリを構成する](./media/conditions/160.png)  


#### <a name="known-issues"></a>既知の問題

- **その他のクライアント**を対象とするポリシーを構成すると、SPConnect などの特定のクライアントから組織全体がブロックされます。 このブロックは、旧バージョンのクライアントが想定していない方法で認証されるために発生します。 この問題は、古い Office クライアントなどの重要な Office アプリケーションには適用されません。 

- ポリシーが有効になるまで、最大で 24 時間かかる可能性があります。 


#### <a name="frequently-asked-questions"></a>よく寄せられる質問

**Q:** この認証で、Microsoft Exchange Web Services はブロックされますか。

Exchange Web サービスが使用する認証プロトコルによって異なります。 Exchange Web サービス アプリケーションが先進認証を使用している場合、**[モバイル アプリとデスクトップ クライアント]** クライアント アプリによって対応されます。 基本認証は、**[他のクライアント]** クライアント アプリによって対応されます。


**Q:** **[他のクライアント]** にはどのようなコントロールを使用できますか。

**[他のクライアント]** には、任意のコントロールを構成できます。 ただし、どの場合でも、エンド ユーザー エクスペリエンスのアクセスはブロックされます。 **[他のクライアント]** は、MFA、対応デバイス、ドメインへの参加などのコントロールをサポートしていません。 
 
**Q:** **[他のクライアント]** にはどのような条件を使用できますか。

**[他のクライアント]** には、任意の条件を構成できます。

**Q:** Exchange ActiveSync はすべての条件とコントロールをサポートしていますか。

いいえ。 Exchange ActiveSync サポートをまとめた次の一覧を参照してください。 

- Exchange ActiveSync は、ユーザーとグループのターゲット指定のみをサポートします。 ゲストまたはロールはサポートされません。 ゲストまたはロールの条件が構成されている場合、すべてのユーザーはブロックされます。 Exchange ActiveSync は、ポリシーがユーザーに適用されるかどうかを判断できないため、すべてのユーザーをブロックします。

- Exchange ActiveSync は、Microsoft Exchange Online でのみクラウド アプリとして機能します。 

- Exchange ActiveSync は、クライアント アプリ自体を除くすべての条件をサポートしません。 

- Exchange ActiveSync は、任意のコントロールで構成できます。 デバイスのコンプライアンス以外のすべてのコントロールはブロックの原因になります。

**Q:** 今後、ポリシーは、すべてのクライアント アプリに既定で適用されますか。

いいえ。 ポリシーの既定の動作に変更はありません。 ポリシーは、引き続き、ブラウザーとモバイル アプリケーションとデスクトップ クライアントに既定で適用されます。



## <a name="next-steps"></a>次の手順

- 条件付きアクセス ポリシーの構成方法を把握するには、「[クイック スタート: Azure Active Directory の条件付きアクセスを使用して特定のアプリケーションに対して MFA を必要にする](app-based-mfa.md)」を参照してください。

- 環境に適用する条件付きアクセス ポリシーを構成するには、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](best-practices.md)」を参照してください。 

