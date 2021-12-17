---
title: Azure Active Directory におけるフラグが設定されたサインインとは
description: Azure Active Directory におけるフラグが設定されたサインインの概要について説明します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/12/2021
ms.author: markvi
ms.reviewer: tspring
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef44ff683717bad391fcb0fa8f108000a810b8a2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715494"
---
# <a name="what-are-flagged-sign-ins-in-azure-active-directory"></a>Azure Active Directory におけるフラグが設定されたサインインとは

IT 管理者は、ユーザーがサインインに失敗した場合、できるだけ早く問題を解決してユーザーのブロックを解除したいと考えています。 サインイン ログで使用可能なデータの量が原因で、適切な情報を見つけるのが難しい場合があります。

この記事では、関連する問題を見つけやすくすることで、ユーザーのサインインの問題の解決にかかる時間を大幅に短縮する機能の概要について説明します。




## <a name="what-it-is"></a>説明

Azure AD のサインイン イベントは、ユーザーのサインインとテナントの認証構成で何がうまくいったか、またはうまくいかなかったかを理解するために非常に重要です。 しかし、Azure AD では 1 日に 80 億を超える認証が処理されるため、非常に多くのサインイン イベントが発生する可能性があり、管理者が重要な認証を見つけるのが難しい場合があります。 つまり、サインイン イベントの数が非常に多い場合、支援が必要なユーザーのシグナルが、大量のイベントに紛れ込んで見失われてしまう恐れがあります。

フラグが設定されたサインインは、ヘルプを必要とするユーザー サインインのシグナル対ノイズ比を向上させることを目的とした機能です。 この機能は、ユーザーが支援を必要としているサインイン エラーに対する認識を高め、管理者やヘルプ デスクの従業員が適切なイベントをより迅速かつ効率的に見つけることを可能にすることを目的にしています。 フラグが設定されたサインイン イベントには、他のサインイン イベントと同じ情報に加えて、ユーザーが管理者による確認のためにイベントにフラグを設定したことも示されます。
 
フラグが設定されたサインインを使用すると、ユーザーはサインイン ページでエラーが表示された場合にフラグを設定し、そのエラーを再現することができます。 そうすると、そのエラー イベントが、[サインイン] の [Azure AD Reporting]\(Azure AD レポート\) ブレードに [確認用にフラグが設定されています] として表示されるようになります。

まとめると、フラグが設定されたサインインを使用して、次のことができます。

- ユーザーに、テナント管理者の支援が必要なサインイン エラーを示す **権限を付与する**。

- ユーザーが解決する必要のあるサインイン エラーを見つけるプロセスを **簡素化する**。

- エンド ユーザーがイベントにフラグを設定するだけで、ヘルプ デスクの担当者がユーザーがヘルプを必要としている問題をプロアクティブに **見つけられるようになる**。

## <a name="how-it-works"></a>しくみ

フラグが設定されたサインインを使用することで、ブラウザーを使用してサインインし、認証エラーを受け取る際にフラグを設定できます。 ユーザーは、サインイン エラーが表示された場合は、フラグを有効にすることを選択できます。 その後 20 分間、同じブラウザーとクライアント デバイスまたはコンピューターでのそのユーザーからのサインイン イベントは、サインイン レポートに [確認用にフラグが設定されています: はい] と表示されます。 20 分後、フラグは自動的にオフになります。

### <a name="user-how-to-flag-an-error"></a>ユーザー: エラーにフラグを設定する方法

1. ユーザーはサインイン中にエラーを受け取ります。
2. ユーザーはエラー ページの **[詳細の表示]** をクリックします。
3. **[トラブルシューティングの詳細]** で、 **[フラグを有効にする]** をクリックします。 テキストが **[フラグを無効にする]** に変わります。 これでフラグ設定が有効になりました。
4. ブラウザー ウィンドウを閉じます。
5. (同じブラウザー アプリケーションで) 新しいブラウザー ウィンドウを開き、失敗したのと同じサインインを試します。 
6.  以前に表示されたサインイン エラーを再現します。

フラグを有効にした後、同じブラウザー アプリケーションとクライアントを使用する必要があります。そうしないと、イベントにフラグが設定されません。


### <a name="admin-find-flagged-events-in-reports"></a>管理者: レポートでフラグが設定されたイベントを見つける

1.  Azure AD ポータルの左側のペインで **[サインイン ログ]** を選択します。
2.  **[フィルターの追加]** をクリックします。
3.  **[フィールドの選択]** というタイトルのフィルター メニューで、 **[確認用にフラグが設定されています]** を選択し、 **[適用]** をクリックします。
4.  ユーザーによってフラグが設定されたイベントがすべて表示されます。
5.  必要に応じて、追加のフィルターを適用してイベント ビューを絞り込みます。
6.  イベントを選択して、何が起こったかを確認します。


### <a name="admin-or-developer-find-flagged-events-using-ms-graph"></a>管理者または開発者: MS Graph を使用してフラグが設定されたイベントを見つける

サインイン レポート API を使用して、フィルター処理されたクエリでフラグが設定されたサインインを見つけることができます。

フラグが設定されたすべてのサインインを表示する: `https://graph.microsoft.com/beta/auditLogs/signIns?&$filter=flaggedforReview eq true`

UPN (例: user@contoso.com) による特定のユーザーのフラグが設定されたサインインのクエリ: `https://graph.microsoft.com/beta/auditLogs/signIns?&$filter=flaggedforReview eq true and userPrincipalname eq 'user@contoso.com'`

特定のユーザー、かつ特定の日付以降のフラグが設定されたサインインのクエリ: `https://graph.microsoft.com/beta/auditLogs/signIns?&$filter=flaggedforReview eq true and createdDateTime ge 2021-10-01 and userPrincipalname eq 'user@contoso.com'`
 
サインイン Graph API の使用の詳細については、「[サインイン リソースの種類](/graph/api/resources/signin?preserve-view=true&view=graph-rest-1.0)」を参照してください。



 
## <a name="who-can-create-flagged-sign-ins"></a>フラグが設定されたサインインを作成できる人

Web ページ経由で Azure AD にサインインしているすべてのユーザーが、確認用にフラグが設定されたサインインを使用できます。 メンバーとゲスト ユーザーも同様に、確認用にサインイン エラーにフラグを設定できます。 

## <a name="who-can-review-flagged-sign-ins"></a>フラグが設定されたサインインを確認できる人

フラグが設定されたサインイン イベントを確認するには、Azure AD ポータルでサインイン レポート イベントを読み取るためのアクセス許可が必要です。 詳細については、「[だれがアクセスできるのか](concept-sign-ins.md#who-can-access-it)」を参照してください。


サインイン エラーにフラグを設定するために、追加のアクセス許可は必要ありません。


## <a name="what-you-should-know"></a>知っておくべきこと 

名前は似ていますが、**フラグが設定されたサインイン** と **危険なサインイン** は異なる機能です。

- フラグが設定されたサインインは、ユーザーが支援を求めているサインイン エラー イベントです。 
- 危険なサインインは、Identity Protection の機能です。 詳細については、「[Identity Protection とは](../identity-protection/overview-identity-protection.md)」をご覧ください。




## <a name="next-steps"></a>次のステップ

- [Azure Active Directory のサインイン ログ](concept-sign-ins.md)
- [Azure AD シナリオのサインイン診断](concept-sign-in-diagnostics-scenarios.md)