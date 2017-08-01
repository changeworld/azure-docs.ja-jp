---
title: "Azure Active Directory の条件付きアクセス | Microsoft Docs"
description: "Azure Active Directory の条件付きアクセス制御を使用して、アプリケーションへのアクセスを認証するときに特定の条件を確認します。"
services: active-directory
keywords: "アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/21/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 5a1ce66e02943caedd52976c5dcb3cf75c23bd49
ms.contentlocale: ja-jp
ms.lasthandoff: 05/11/2017

---
# <a name="conditional-access-in-azure-active-directory"></a>Azure Active Directory の条件付きアクセス

> [!div class="op_single_selector"]
> * [Azure ポータル](active-directory-conditional-access-azure-portal.md)
> * [Azure クラシック ポータル](active-directory-conditional-access.md)

モバイル ファースト、クラウド ファーストの世界で、Azure Active Directory を使用してデバイス、アプリ、およびサービスにどこからでもサインオンできます。 デバイス (BYOD を含みます) や企業ネットワーク外での作業、サードパーティが提供する SaaS アプリの急増によって、IT プロフェッショナルは、次の 2 つの対立する目標を達成することを迫られています。

- 場所や時間を問わず、常にエンド ユーザーの生産性を高められるようにすること
- 企業の資産を常に保護すること

生産性を向上させるため、Azure Active Directory には、ユーザーが企業資産にアクセスするための幅広いオプションが用意されています。 アプリケーションへのアクセス管理によって、Azure Active Directory では*正当なユーザー*のみがアプリケーションにアクセスできることを保証できます。 ただし、正当なユーザーにも、特定の条件下ではリソースにアクセスすることを制限する場合はどうすればよいでしょうか。 "*正当なユーザー*" であっても、特定のアプリへのアクセスをブロックする特定の条件が存在する場合はどうすればよいでしょうか。 たとえば、正当なユーザーが信頼されたネットワークから特定のアプリにアクセスすることには問題はないでしょう。ただし、同じアプリに、信頼されていないネットワークからアクセスすることは認められないでしょう。 これらの問題は、条件付きアクセスを使用することで解決できます。

条件付きアクセスは、特定の条件に基づいて環境内のアプリへのアクセスに対してコントロールを適用できるようにする Azure Active Directory の機能です。 コントロールを使用して、アクセスに要件を追加するか、アクセスをブロックできます。 条件付きアクセスの実装は、ポリシーに基づいています。 ポリシー ベースのアプローチはアクセス要件を考慮する道筋に沿っているため、その構成エクスペリエンスは単純です。  

通常は、次のパターンに基づく文を使用して、アクセス要件を定義します。

![コントロール](./media/active-directory-conditional-access-azure-portal/10.png)

この 2 回出現する “*this*” を現実世界の情報に置き換えれば、次のような一般的なポリシー ステートメントの例になります。

*請負業者が信頼されていないネットワークから会社のクラウド アプリにアクセスしようとしている場合は、アクセスをブロックします。*

上記のポリシー ステートメントは、条件付きアクセスのパワーを強調しています。 請負業者は、基本的には会社のクラウド アプリにアクセスできます (**誰が**) が、条件付きアクセスによって、アクセスが可能な条件を定義することもできます (**どのように**)。

Azure Active Directory の条件付きアクセスの文脈では、

- "**When this happens**" (これが発生した場合は) を**条件文**と呼びます
- "**Then do this**" (これを実行する) を**コントロール**と呼びます

![コントロール](./media/active-directory-conditional-access-azure-portal/11.png)

条件文とコントロールの組み合わせによって、条件付きアクセス ポリシーを表現します。

![コントロール](./media/active-directory-conditional-access-azure-portal/12.png)


## <a name="controls"></a>コントロール

条件付きアクセス ポリシーでは、条件文が満たされた場合に何をすべきであるかをコントロールが定義します。  
コントロールを使用して、アクセスをブロックするか、追加要件付きでアクセスを許可することができます。
アクセスを許可するポリシーを構成するときは、少なくとも 1 つの要件を選択する必要があります。   

### <a name="grant-controls"></a>許可コントロール
Azure Active Directory の現在の実装では、次の許可コントロール要件を構成できます。

![コントロール](./media/active-directory-conditional-access-azure-portal/05.png)

- **多要素認証**: 多要素認証による強力な認証を要求できます。 プロバイダーとして、Azure Multi-Factor またはオンプレミスの多要素認証プロバイダーを Active Directory Federation Services (AD FS) と組み合わせて使用できます。 多要素認証を使用すると、承認されていないユーザーが有効なユーザーの資格情報を入手してリソースにアクセスするのを防ぐことができます。

- **準拠デバイス**: 条件付きアクセス ポリシーをデバイス レベルで設定できます。 準拠しているコンピューターのみ、またはモバイル デバイス管理アプリケーションに登録されているモバイル デバイスのみが組織のリソースにアクセスできるポリシーを設定できます。 たとえば、ユーザーがアプリケーションへのアクセスを試みたときに、Intune を使用してデバイスの準拠を確認し、その結果を Azure AD に報告することができます。 Intune を使用してアプリとデータを保護する方法の詳細なガイダンスについては、「Microsoft Intune でアプリとデータを保護する」を参照してください。 また、Intune を使用して紛失したデバイスや盗難されたデバイスのデータ保護を適用することもできます。 詳細については、「Microsoft Intune のフル ワイプまたは選択的ワイプを使用してデータを保護する」を参照してください。

- **ドメイン参加済みデバイス**: Azure Active directory に接続するために使用しているデバイスがドメインに参加しているデバイスであることを要求できます。 このポリシーは、Windows のデスクトップ、ノート PC、およびエンタープライズ タブレットに適用されます。 Azure AD へのドメイン参加済みデバイスの自動登録をセットアップする方法の詳細については、「[Azure Active Directory への Windows ドメイン参加済みデバイスの自動デバイス登録](active-directory-conditional-access-automatic-device-registration.md)」を参照してください。

条件付きアクセス ポリシーで複数の要件を選択した場合は、それらの適用方法を構成できます。 選択したコントロールをすべて適用するか、いずれかを適用することを選択できます。

![コントロール](./media/active-directory-conditional-access-azure-portal/06.png)

### <a name="session-controls"></a>セッション コントロール
セッション コントロールでは、クラウド アプリ内のエクスペリエンスを制限できます。 セッション コントロールは、クラウド アプリによって適用され、Azure AD がアプリに提供するセッションに関する追加情報に依存します。

![コントロール](./media/active-directory-conditional-access-azure-portal/session-control-pic.png)

#### <a name="use-app-enforced-restrictions"></a>アプリによって適用される制限を使用する
このコントロールを使用して、デバイス情報をクラウド アプリに渡すよう Azure AD に要求できます。 これにより、クラウド アプリは、ユーザーが準拠デバイスとドメイン参加済みデバイスのどちらからアクセスしているかを把握できます。 このコントロールは、現在、クラウド アプリとしての SharePoint でのみサポートされます。 SharePoint では、デバイス情報を使用して、デバイスの状態に応じて制限付きまたは完全なエクスペリエンスをユーザーに提供します。
SharePoint での制限付きアクセスを要求する方法について詳しくは、[こちら](https://aka.ms/spolimitedaccessdocs)をご覧ください。

## <a name="condition-statement"></a>条件文

前のセクションで、リソースへのアクセスをブロックするか制限するための、コントロールという形のサポート オプションを紹介しました。 条件付きアクセス ポリシーには、コントロールを適用するために満たす必要がある条件を、条件文という形で定義します。  

条件文には、次の割り当てを含めることができます。

![コントロール](./media/active-directory-conditional-access-azure-portal/07.png)


- **誰が**: 多くの場合、コントロールは、ユーザーの特定のセットに適用します。 条件文では、ポリシーを適用するユーザーとグループを選択することで、このセットを定義できます。 必要であれば、ユーザーを除外することで、明示的にポリシーの対象外にすることもできます。  
ユーザーとグループを選択することで、ポリシーが適用されるユーザーの範囲を定義します。    

    ![コントロール](./media/active-directory-conditional-access-azure-portal/08.png)



- **何を**: 通常、環境内で実行されているアプリの中には、保護の観点から見て、他のアプリよりも注意が必要なアプリがあります。 たとえば、機密データにアクセスするアプリがそうです。
クラウド アプリを選択することで、ポリシーが適用されるクラウド アプリの範囲を定義します。 必要であれば、一連のアプリを、明示的にポリシーの対象から除外することもできます。

    ![コントロール](./media/active-directory-conditional-access-azure-portal/09.png)


- **どのように**: アプリへのアクセスが制御可能な状況で実行されている限り、ユーザーがどのようにクラウド アプリにアクセスしているかについて、さらに制限する必要はありません。 ただし、クラウド アプリへのアクセスが、たとえば信頼されていないネットワークや準拠していないデバイスから実行される場合は、状況は異なります。 条件文に、アプリへのアクセスがどのように実行されているかを示す追加要件があるアクセス条件を定義できます。

    ![条件](./media/active-directory-conditional-access-azure-portal/21.png)


## <a name="conditions"></a>条件

Azure Active Directory の現在の実装では、次の領域の条件を定義できます。

- **サインイン リスク** – サインイン リスクは、サインイン試行がユーザー アカウントの正当な所有者によって行われなかった可能性を追跡する目的で Azure Active Directory が利用するオブジェクトです。 このオブジェクトには、この可能性 (高、中、低) が[サインイン リスク レベル](active-directory-reporting-risk-events.md#risk-level)と呼ばれる属性で格納されます。 このオブジェクトは、ユーザーのサインイン中に Azure Active Directory がサインイン リスクを検出した場合に生成されます。 詳細については、「[Risky sign-ins (リスクの高いサインイン)](active-directory-identityprotection.md#risky-sign-ins)」を参照してください。  
計算されたサインイン リスク レベルを条件付きアクセス ポリシーの条件として利用できます。 

    ![条件](./media/active-directory-conditional-access-azure-portal/22.png)

- **デバイス プラットフォーム**: デバイス プラットフォームは、デバイスで実行されているオペレーティング システムによって分類されます (Android、iOS、Windows Phone、Windows)。 ポリシーの対象とするデバイス プラットフォームだけでなく、ポリシーから除外するデバイス プラットフォームも定義できます。  
デバイス プラットフォームをポリシーで使用するには、構成トグル スイッチを **[はい]** に変更してから、ポリシーを適用するデバイス プラットフォームを選択します (すべてを選択するか、個別に選択します)。 デバイス プラットフォームを個別に選択した場合、ポリシーは、選択したプラットフォームにのみ影響します。 この場合、サポートされているその他のプラットフォームへのサインインがポリシーに影響されることはありません。

    ![条件](./media/active-directory-conditional-access-azure-portal/02.png)

- **場所**: 場所は、Azure Active Directory に接続するために使用しているクライアントの IP アドレスによって識別されます。 この条件を設定するには、信頼できる IP を理解している必要があります。 信頼できる IP は多要素認証の機能であり、この機能を使用して、組織のイントラネットを表す、信頼できる IP アドレスの範囲を定義できます。 場所の条件を構成するとき、信頼できる IP アドレスによって、組織のネットワークとそれ以外のすべての場所からの接続を区別できます。 詳細については、「[信頼できる IP](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips)」を参照してください。  
すべての場所を含める、すべての信頼できるIP アドレスを含める、または信頼できるすべての IP アドレスを除外することができます。

    ![条件](./media/active-directory-conditional-access-azure-portal/03.png)


- **クライアント アプリ**: クライアント アプリは、Azure Active Directory に接続するために使用している汎用レベルのアプリ (Web ブラウザー、モバイル アプリ、デスクトップ クライアント) アプリが可能です。または、Exchange Active Sync を明確に選択できます。  
従来の認証とは、最新の認証を使用しない古い Office クライアントなどの基本認証を使用しているクライアントを指します。 現時点では、従来の認証では条件付きアクセスはサポートされません。

    ![条件](./media/active-directory-conditional-access-azure-portal/04.png)


## <a name="common-scenarios"></a>一般的なシナリオ

### <a name="requiring-multi-factor-authentication-for-apps"></a>アプリに対する多要素認証の要求

多くの環境には、他のアプリよりも高いレベルの保護を必要とするアプリがあります。
たとえば、機密データにアクセスするアプリがそうです。
これらのアプリに対して別の保護層を追加する場合は、ユーザーがこれらのアプリにアクセスするときに多要素認証を要求する条件付きアクセス ポリシーを構成できます。


### <a name="requiring-multi-factor-authentication-for-access-from-networks-that-are-not-trusted"></a>信頼されていないネットワークからのアクセスに対する多要素認証の要求

このシナリオは、多要素認証要件を追加するという点で前のシナリオに似ています。
ただし、主な違いは、この要件の条件です。  
前のシナリオの焦点は機密データにアクセスするアプリにありますが、このこのシナリオの焦点は信頼している場所にあります。  
つまり、信頼していないネットワークからユーザーがアプリにアクセスする場合に、多要素認証を要求します。


### <a name="only-trusted-devices-can-access-office-365-services"></a>信頼済みデバイスのみが Office 365 サービスにアクセス可能

環境内で Intune を使用している場合は、Azure コンソールで条件付きアクセス ポリシー インターフェイスの使用をすぐに開始できます。

多数の Intune ユーザーは、条件付きアクセスを使用して、信頼済みデバイスのみが Office 365 サービスにアクセスできるようにしています。 これは、モバイル デバイスが Intune に登録されていること、コンプライアンス ポリシーの要件を満たしていること、および Windows PC がオンプレミス ドメインに参加していることを意味します。 重要な機能強化は、Office 365 サービスごとに同じポリシーを設定する必要がないことです。  新しいポリシーを作成するときは、条件付きアクセスによって保護する O365 アプリが含まれるようにクラウド アプリを構成します。

## <a name="next-steps"></a>次のステップ

条件付きアクセスポリシーの構成方法については、[Azure Active Directory での条件付きアクセスの使用](active-directory-conditional-access-azure-portal-get-started.md)に関する記事を参照してください。

条件付きアクセス ポリシーを構成するときに知っておくべきことと避けるべきことについては、次をご覧ください。 
