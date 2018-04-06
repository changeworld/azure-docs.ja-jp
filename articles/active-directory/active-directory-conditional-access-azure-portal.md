---
title: Azure Active Directory の条件付きアクセス | Microsoft Docs
description: Azure Active Directory の条件付きアクセスが一元的な場所からアクセスの制御を管理するのにどのように役立つかを説明します。
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
ms.date: 02/23/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 74a87065416b397b4eebbcaa54c769711176708d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="conditional-access-in-azure-active-directory"></a>Azure Active Directory の条件付きアクセス

セキュリティは、クラウドを使用する組織の最大の懸念事項です。 クラウド セキュリティの重要な側面は、クラウド リソースを管理する際の ID とアクセスです。 モバイルを重視したクラウド中心の世界では、ユーザーはさまざまなデバイスやアプリを使用してどこからでも組織のリソースにアクセスできます。 このため、だれがリソースにアクセスできるかに重点を置くだけでは十分ではなくなっています。 セキュリティと生産性のバランスを習得するために、IT プロフェッショナルは、リソースへのアクセス方法も考慮してアクセスの制御を決定する必要があります。 Azure AD の条件付きアクセスで、この要件に対処することができます。 条件付きアクセスは、一元的な場所から特定の条件に基づいて環境内のアプリへのアクセスに対してコントロールを適用できるようにする Azure Active Directory の機能です。 


![コントロール](./media/active-directory-conditional-access-azure-portal/81.png)

この記事では、Azure AD の条件付きアクセスの概念的な概要について説明します。


## <a name="common-scenarios"></a>一般的なシナリオ

モバイル ファースト、クラウド ファーストの世界で、Azure Active Directory を使用してデバイス、アプリ、およびサービスにどこからでもサインオンできます。 デバイス (BYOD を含みます) や企業ネットワーク外での作業、サードパーティが提供する SaaS アプリの急増によって、IT プロフェッショナルは、次の 2 つの対立する目標を達成することを迫られています。

- 場所や時間を問わず、常にユーザーの生産性を高められるようにすること
- 企業の資産を常に保護すること

条件付きアクセス ポリシーを使用すると、必要な条件下で適切なアクセスの制御を適用できます。 Azure AD の条件付きアクセスでは、必要なときにセキュリティを強化し、必要でない場合にはユーザーの自由に任せることができます。 

条件付きアクセスが役立ついくつかの一般的なアクセスの問題を次に示します。



- **[サインイン リスク](active-directory-conditional-access-conditions.md#sign-in-risk)**: Azure AD Identity Protection は、サインイン リスクを検出します。 検出されたサインイン リスクが悪意のあるユーザーを示している場合、どのようにアクセスを制限しますか。 サインインが正当なユーザーによって実行されたこと、または特定のユーザーをアプリへのアクセスからブロックするのに十分な疑いがあることを示す強力な証拠を取得する必要がある場合はどうしますか。

- **[ネットワークの場所](active-directory-conditional-access-locations.md)**: Azure AD はどこからでもアクセスできます。 IT 部門の管理下にないネットワークの場所からアクセスが試行された場合はどうしますか。 ユーザー名とパスワードの組み合わせの使用は、企業ネットワークからリソースへのアクセスを試行するための十分な身元証明になることがあります。 他の予期しない国または地域から開始されたアクセス試行に対してより強力な身元証明が必要な場合はどうしますか。 特定の場所からのアクセス試行をブロックする必要がある場合はどうしますか。  

- **[デバイス管理](active-directory-conditional-access-conditions.md#device-platforms)**: Azure AD では、ユーザーはモバイル デバイスと個人デバイスを含むさまざまなデバイスからクラウド アプリにアクセスできます。 IT 部門によって管理されているデバイスによってのみアクセスを試行できるようにする必要がある場合はどうしますか。 環境内のクラウド アプリで特定の種類のデバイスによるアクセスをブロックする必要がある場合はどうしますか。 

- **[クライアント アプリケーション](active-directory-conditional-access-conditions.md#client-apps)**: 現在、Web ベースのアプリ、モバイル アプリ、デスクトップ アプリなどのさまざまな種類のアプリを使用して多くのクラウド アプリにアクセスできます。 既知の問題の原因となるクライアント アプリの種類を使用してアクセス試行が実行された場合はどうしますか。 特定の種類のアプリについて IT 部門が管理するデバイスが必要な場合はどうしますか。 

これらの質問と関連する回答は、Azure AD の条件付きアクセスの一般的なアクセス シナリオを表します。 条件付きアクセスは、ポリシー ベースのアプローチを使用してアクセス シナリオを処理できるようにする Azure Active Directory の機能です。


## <a name="conditional-access-policies"></a>条件付きアクセス ポリシー

条件付きアクセス ポリシーは、次のパターンを使用したアクセス シナリオの定義です。

![コントロール](./media/active-directory-conditional-access-azure-portal/10.png)

**Then do this** (これを実行する) はポリシーの応答を定義します。 条件付きアクセス ポリシーの目的は、クラウド アプリへのアクセスを許可することではないという点に注意する必要があります。 Azure AD では、クラウド アプリへのアクセスの許可はユーザー割り当てのサブジェクトです。 条件付きアクセス ポリシーでは、承認されたユーザー (クラウド アプリへのアクセスを許可されているユーザー) が特定の条件下でクラウド アプリにアクセスできる方法を制御します。 応答では、多要素認証や管理されたデバイスなどの追加の要件を適用します。 Azure AD の条件付きアクセスのコンテキストでは、ポリシーで適用される要件をアクセスの制御と呼びます。 最も制限の厳しい形式で、ポリシーはアクセスをブロックできます。 詳しくは、「[Azure Active Directory の条件付きアクセスのコントロール](active-directory-conditional-access-controls.md)」をご覧ください。
     

**When this happens** (これが発生した場合は) では、ポリシーをトリガーする理由を定義します。 この理由は、満たされている条件のグループによって特徴付けられます。 Azure AD の条件付きアクセスでは、2 つの割り当て条件が特別な役割を果たします。

- **[ユーザー](active-directory-conditional-access-conditions.md#users-and-groups)**: アクセスを試行するユーザー (**Who (だれが)**)。 

- **[クラウド アプリ](active-directory-conditional-access-conditions.md#cloud-apps)**: アクセス試行のターゲット (**What (何を)**)。    

この 2 つの条件は、条件付きアクセス ポリシーでは必須です。 2 つの必須条件に加えて、アクセス試行の実行方法を説明する追加の条件を含めることもできます。 一般的な例として、モバイル デバイスの使用や、企業ネットワーク外の場所があります。 詳しくは、「[Conditions in Azure Active Directory conditional access](active-directory-conditional-access-conditions.md)」(Azure Active Directory の条件付きアクセスの条件) をご覧ください。   

条件とアクセスの制御の組み合わせによって、条件付きアクセス ポリシーを表現します。 

![コントロール](./media/active-directory-conditional-access-azure-portal/51.png)

Azure AD の条件付きアクセスを使うと、承認されたユーザーがクラウド アプリにどのようにアクセスするかを制御できます。 条件付きアクセス ポリシーの目的は、クラウド アプリへのアクセス試行に対して、アクセス試行が実行される方法によって主導される追加のアクセスの制御を適用することです。

ポリシー ベースのアプローチを使用してクラウド アプリへのアクセスを保護する利点の 1 つは、技術的な実装について心配することなく、この記事で概説している構造を使用して環境のポリシー要件の下書き作成を開始できることです。 

## <a name="what-you-need-to-know"></a>知っておくべきこと

### <a name="general-requirements-for-using-conditional-access"></a>条件付きアクセスを使用するための一般的な要件

以下のいずれかから認証が試行された場合には、クラウド アプリの保護に Azure AD の条件付きアクセスを使用できます:

- Web ブラウザー

- 先進認証を使用するクライアント アプリ

- Exchange ActiveSync

詳しくは、[クライアント アプリ](active-directory-conditional-access-conditions.md#client-apps)に関するページをご覧ください。

一部の[クラウド アプリ](active-directory-conditional-access-conditions.md#cloud-apps)では、レガシ認証プロトコルもサポートされています。 たとえば、SharePoint Online や Exchange Online がこれに該当します。 クライアント アプリでクラウド アプリへのアクセスにレガシ認証プロトコルを利用できる場合、そのようなアクセスの試行に対しては Azure AD の条件付きアクセス ポリシーを適用できません。 クライアント アプリがポリシーの適用を回避する事態を防ぐため、該当するクラウド アプリで先進認証のみを有効にできるかどうかを確認する必要があります。

条件付きアクセスが適用されないクライアント アプリの例を以下に示します:

- Office 2010 およびそれ以前

- 先進認証を有効にしていない場合の Office 2013

詳しくは、「[SharePoint Online と Exchange Online に Azure Active Directory の条件付きアクセスを設定する」](active-directory-conditional-access-no-modern-authentication.md)をご覧ください。


### <a name="license-requirements-for-using-conditional-access"></a>条件付きアクセスを使用するためのライセンス要件

条件付きアクセスを使用するには、Azure AD Premium ライセンスが必要です。 要件に対する適切なライセンスを確認するには、「[Free、Basic、および Premium エディションの一般公開されている機能の比較](https://www.microsoft.com/en-us/cloud-platform/azure-active-directory-features)」をご覧ください。


## <a name="next-steps"></a>次の手順

- 詳細を知りたい場合:
    - 条件については、「[Conditions in Azure Active Directory conditional access](active-directory-conditional-access-conditions.md)」(Azure Active Directory の条件付きアクセスの条件) をご覧ください。

    - アクセスの制御については、「[Azure Active Directory の条件付きアクセスのコントロール](active-directory-conditional-access-controls.md)」をご覧ください。

- 条件付きアクセス ポリシーの構成について経験するには、「[Azure Active Directory での条件付きアクセスの基本](active-directory-conditional-access-azure-portal-get-started.md)」をご覧ください。

- 環境に適用する条件付きアクセス ポリシーを構成する準備ができたら、「[Azure Active Directory の条件付きアクセスのベスト プラクティス](active-directory-conditional-access-best-practices.md)」を参照してください。 
