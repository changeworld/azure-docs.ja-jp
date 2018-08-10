---
title: SAML に基づいたシングル サインオンをデバッグする - Azure Active Directory | Microsoft Docs
description: Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグします。
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/15/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin, dastrock, smalser
ms.openlocfilehash: 5895e918fbd3b67074069ccc81bd794a75725c9a
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39620938"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする

[Security Assertion Markup Language (SAML) 2.0](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language) をサポートする Azure Active Directory (Azure AD) のアプリケーションについて、[シングル サインオン](../manage-apps/what-is-single-sign-on.md)の問題を見つけて修正する方法を説明します。 

## <a name="before-you-begin"></a>開始する前に
[マイ アプリによるセキュリティで保護されたサインイン拡張機能](../user-help/active-directory-saas-access-panel-user-help.md#i-am-having-trouble-installing-the-my-apps-secure-sign-in-extension)をインストールすることをお勧めします。 このブラウザー拡張機能により、シングル サインオンに関する問題の解決に必要な SAML 要求および SAML 応答の情報を収集しやすくなります。 拡張機能をインストールできない場合でも、この記事では、拡張機能がインストールされている場合とされていない場合の両方について、問題を解決する方法が示されています。

マイ アプリによるセキュリティで保護されたサインイン拡張機能ををダウンロードしてインストールするには、次のいずれかのリンクを使用します。

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)


## <a name="test-saml-based-single-sign-on"></a>SAML に基づいたシングル サインオンをテストする

AAD と対象アプリケーションの間の、SAML に基づいたシングル サインオンをテストするには:

1.  グローバル管理者またはアプリケーションを管理する権限があるその他の管理者として、[Azure Portal](https://portal.azure.com) にサインインします。
2.  左のブレードで、**[Azure Active Directory]** をクリックしてから、**[エンタープライズ アプリケーション]** をクリックします。 
3.  [エンタープライズ アプリケーション] の一覧で、シングル サインオンをテストするアプリケーションをクリックし、左側のオプションの **[シングル サインオン]** をクリックします。
4.  SAML に基づいたシングル サインオン テスト エクスペリエンスを開くには、**[ドメインと URL]** で、**[Test SAML Setting]**(SAML 設定のテスト) をクリックします。 Test SAML Setting(SAML 設定のテスト) ボタンがテスト SAML 設定 ボタンが淡色表示される場合は、まず、必須の属性を入力して保存する必要があります。
5.  **[シングル サインオンのテスト]** ブレードで、会社の資格情報を使用して対象のアプリケーションにサインインします。 現在のユーザーまたは別のユーザーとしてサインインできます。 別のユーザーとしてサインインする場合は、認証するよう求めるプロンプトが表示されます。

    ![SAML のテストのページ](./media/howto-v1-debug-saml-sso-issues/testing.png)


正常にサインインしている場合は、テストに合格しました。 この場合、Azure AD がアプリケーションに、SAML 応答トークンを発行しました。 アプリケーションはこの SAML トークンを使用して、正常にユーザーをサインインさせました。

会社のサインイン ページまたはアプリケーションのページでエラーが発生する場合は、以降のセクションのいずれかに従ってエラーを解決します。


## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>会社のサインイン ページでのサインイン エラーを解決する

サインインしようとすると、会社のサインイン ページにエラーが表示されることがあります。 

![サインイン エラー](./media/howto-v1-debug-saml-sso-issues/error.png)

このエラーをデバッグするには、エラー メッセージと SAML 要求が必要です。 マイ アプリによるセキュリティで保護されたサインイン拡張機能は、この情報を自動的に収集し、Azure AD に解決ガイダンスを表示します。 

インストールしたマイ アプリによるセキュリティで保護されたサインイン拡張機能を使用してサインインのエラーを解決するには:

1.  エラーが発生すると、拡張機能によって、ユーザーには Azure AD の **[シングル サインオンのテスト]** ブレードが表示されます。 
2.  **[シングル サインオンのテスト]** ブレードで、**[Download the SAML request]**(SAML 要求のダウンロード) をクリックします。 
3.  エラーと SAML 要求内の値に基づいて、具体的な解決ガイダンスが表示されます。 ガイダンスの内容をよく調べます。

マイ アプリによるセキュリティで保護されたサインイン拡張機能をインストールしないでエラーを解決するには:

1. ページの右下隅のエラー メッセージをコピーします。 エラー メッセージには以下が含まれています。
    - CorrelationID とタイムスタンプ。 これらの値は、Microsoft のエンジニアが問題を識別して、実際の問題に対する正確な解決策を提供する助けとなるため、Microsoft とのサポート ケースを作成するときに重要です。
    - 問題の根本原因を明らかにしている文章。
2.  Azure AD に戻り、**[シングル サインオンのテスト]** ブレードを見つけます。
3.  **[Get resolution guidance]**(解決ガイダンスの取得) の上にあるテキスト ボックスに、エラー メッセージを貼り付けます。
3.  **[Get resolution guidance]**(解決ガイダンスの取得) をクリックし、問題を解決するための手順を表示します。 ガイダンスには、SAML 要求または SAML 応答からの情報が必要な場合があります。 マイ アプリによるセキュリティで保護されたサインイン拡張機能をを使用していない場合は、SAML の要求や応答を取得するために [Fiddler](http://www.telerik.com/fiddler) などのツールが必要なことがあります。
4.  SAML 要求に含まれる送信先が、Azure Active Directory から取得した SAML シングル サインオン サービス URL に対応していることを確認します。
5.  SAML 要求に含まれる発行者は、Azure Active Directory のアプリケーションのために構成した識別子と同じです。 Azure AD は、発行者を使用してディレクトリ内のアプリケーションを検索します。
6.  AssertionConsumerServiceURL は、アプリケーションが Azure Active Directory から SAML トークンを受け取ることになっている場所であることを確認します。 この値は Azure Active Directory 内で構成できますが、SAML 要求の一部としては必須の値ではありません。


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>アプリケーションのページでサインイン エラーを解決する

正常にサインインしてから、アプリケーションのページにエラーが表示される場合があります。 これは、Azure AD はアプリケーションにトークンを発行したのに、アプリケーションが応答を受け付けないときに発生します。   

このエラーを解決するには:

1. アプリケーションが Azure AD ギャラリー内にある場合は、アプリケーションを Azure AD と統合するすべての手順に従ったことを確認します。 アプリケーションの統合手順については、[SaaS アプリケーションの統合に関するチュートリアルの一覧](../saas-apps/tutorial-list.md)を参照してください。
2. SAML 応答を取得します。
    - マイ アプリによるセキュリティで保護されたサインイン拡張機能がインストールされている場合、**[シングル サインオンのテスト]** ブレードで、**[download the SAML response]**(SAML 応答のダウンロード) をクリックします。
    - この拡張機能がインストールされていない場合は、[Fiddler](http://www.telerik.com/fiddler) などのツールを使用して SAML 応答を取得します。 
3. SAML 応答トークン内の以下の要素に注目します。
    - ユーザーの一意の識別子である NameID の値形式
    - そのトークンで発行された要求
    - トークンの署名に使用された証明書。 SAML 応答の内容を調べる方法については、「[シングル サインオンの SAML プロトコル](single-sign-on-saml-protocol.md)」を参照してください。
4. SAML 応答の詳細については、「[シングル サインオンの SAML プロトコル](single-sign-on-saml-protocol.md)」を参照してください。
5. SAML 応答の内容を確認したので、問題解決のガイダンスについて、「[サインイン後、アプリケーションのページでエラーが発生する](../application-sign-in-problem-application-error.md)」を参照してください。 
6. まだ正常にサインインできない場合は、SAML 応答には何が不足しているか、アプリケーション ベンダーに問い合わせることができます。


## <a name="next-steps"></a>次の手順
アプリケーションに対してシングル サインオンが動作するようになったので、[SaaS アプリケーションに対するユーザーのプロビジョニングとプロビジョニング解除を自動化](../active-directory-saas-app-provisioning.md)するか、[条件付きアクセスを使ってみる](../conditional-access/app-based-conditional-access.md)ことができます。


