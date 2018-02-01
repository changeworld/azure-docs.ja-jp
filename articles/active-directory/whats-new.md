---
title: "新機能 Azure Active Directory のリリース ノート | Microsoft Docs"
description: "最新のリリース ノート、既知の問題、バグの修正、非推奨になった機能、予定されている変更点など、Azure Active Directory (Azure AD) の新着情報について説明します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: f32fc2d2df73f4a01c300d565595e2c00b2baf4b
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2018
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory の新着情報




> [![RSS](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us)[フィード](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us)をサブスクライブして、Azure Active Directory (Azure AD) の新着情報を常に把握するようにしてください。



Azure AD は随時改善されています。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

-   最新のリリース
-   既知の問題
-   バグの修正
-   非推奨になった機能
-   変更の計画

このページは毎月更新されるため、定期的にアクセスしてご確認ください。


## <a name="january-2018"></a>2018 年 1 月
 

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ 

**タイプ:** 新機能  
**サービス カテゴリ:** Enterprise アプリ  
**製品の機能:** サード パーティ統合
 

2018 年 1 月に、フェデレーションをサポートする次の新しいアプリがアプリ ギャラリーに追加されました。

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698)、[OneTrust Privacy Management Software](https://go.microsoft.com/fwlink/?linkid=861660)、[Dealpath](https://go.microsoft.com/fwlink/?linkid=863526)、[IriusRisk Federated Directory](https://go.microsoft.com/fwlink/?linkid=864699)、[Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701)。

閲覧できるすべてのチュートリアルの詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」をご覧ください。
 

---
 


### <a name="sign-in-with-additional-risk-detected"></a>追加のリスクが検出されたサインイン

**タイプ:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティと保護
 

検出されたリスク イベントに対して取得する洞察は、Azure AD サブスクリプションに関連付けられています。 Azure AD Premium P2 エディションでは、基になるすべての検出に関する最も詳細な情報を取得できます。

Azure AD Premium P1 エディションでは、ライセンスに含まれない検出は、追加のリスクが検出されたサインインというリスク イベントとして表示されます。

詳細については、「[Azure Active Directory リスク イベント](active-directory-reporting-risk-events.md)」を参照してください。
 

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>エンド ユーザーのアクセス パネルでの Office 365 アプリケーションの非表示

**タイプ:** 新機能  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** SSO
 

新しいユーザー設定を使用することで、ユーザーのアクセス パネルで Office 365 アプリケーションを表示する方法が管理しやすくなりました。 このオプションは、Office ポータルでのみ Office アプリを表示する場合に、ユーザーのアクセス パネル内のアプリ数を減らすのに役立ちます。 この設定は **[ユーザー設定]** 内にあり、**[ユーザーは Office 365 ポータルでのみ Office 365 アプリを表示できる]** というラベルが付いています。
 

詳細については、「[Azure Active Directory でユーザーのエクスペリエンスからアプリケーションを非表示にする](active-directory-coreapps-hide-third-party-app.md)」をご覧ください。

---
 


### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>アプリ URL からの直接のパスワード SSO が有効なアプリへのシームレスなサインイン 

**タイプ:** 新機能  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** SSO
 

マイ アプリのブラウザー拡張機能は、マイ アプリのシングル サインオン機能をブラウザーでショートカットとして使用できる、便利なツール上で利用できます。 インストール後、ユーザーのブラウザーにワッフル アイコンが表示され、そこからアプリにクイック アクセスできるようになります。 ユーザーは次のメリットを得ることができます。

- アプリのログイン ページから直接、パスワード SSO ベースのアプリにサインインできます
- クイック検索機能を使用して、すべてのアプリを起動できます
- 拡張機能で最近使用したアプリにショートカットできます
- 拡張機能は Edge、Chrome、Firefox で使用できます。
 
詳細については、「[マイ アプリによるセキュリティで保護されたサインイン拡張機能](active-directory-saas-access-panel-introduction.md#my-apps-secure-sign-in-extension)」をご覧ください。

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure クラシック ポータルでの Azure AD 管理エクスペリエンスの廃止

**タイプ:** 非推奨   
**サービス カテゴリ:** Azure AD  
**製品の機能:** ディレクトリ
 

2018 年 1 月 8 日に、Azure クラシック ポータルでの Azure AD 管理エクスペリエンスは廃止されました。 これは Azure クラシック ポータル自体の廃止と合わせて行われました。 今後は、Azure AD のポータル ベースの管理についてはすべて [Azure AD 管理センター](https://aad.portal.azure.com)をご利用ください。
 
---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure クラシック ポータルでの Azure AD 管理エクスペリエンスの廃止

**タイプ:** 非推奨  
**サービス カテゴリ:** Azure AD  
**製品の機能:** ディレクトリ
 

2018 年 1 月 8日に、PhoneFactor Web ポータルは廃止されました。 このポータルは、MFA サーバーの管理に使用されていましたが、これらの機能は portal.azure.com の Azure Portal に移行しました。 

MFA の構成は、**[Azure Active Directory] \> [MFA サーバー]** にあります。
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Azure AD レポートの廃止


**タイプ:** 非推奨  
**サービス カテゴリ:** レポート  
**製品の機能:** ID ライフサイクル管理  


新しい Azure Active Directory 管理コンソールと、アクティビティおよびセキュリティ レポートに使用できる新しい API の一般公開に伴い、"/reports" エンドポイントにあるレポート API は 2017 年 12 月 31 日に廃止されました。


**利用可能な機能**

新しい管理コンソールへの移行の一環として、Azure AD アクティビティ ログの取得に使用できる新しい 2 つの API が作成されました。 新しい API のセットは、より豊富な監査およびサインイン アクティビティに加えて、より豊富なフィルターおよび並べ替え機能を提供します。 以前はセキュリティ レポートを通じて提供されていたデータに、Microsoft Graph の Identity Protection リスク イベント API を通じてアクセスできます。

詳細については、「

- [Azure Active Directory Reporting API の概要](active-directory-reporting-api-getting-started-azure-portal.md)

- [Azure Active Directory Identity Protection と Microsoft Graph の基本](active-directory-identityprotection-graph-getting-started.md)


---


## <a name="december-2017"></a>2017 年 12 月
 

### <a name="terms-of-use-in-the-access-panel"></a>アクセス パネルでの使用条件

**タイプ:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス/コンプライアンス
 
アクセス パネルに移動して、以前承認した使用条件を確認できます。

次の手順に従います。

1. [MyApps ポータル](https://myapps.microsoft.com)に移動し、サインインします。

2. 右上隅に表示されるご自身の名前をクリックし、一覧から **[プロファイル]** を選択します。 

3. **[プロファイル]** で、**[使用条件の確認]** を選択します。 

4. 承認した使用条件を確認できます。 

詳細については、「[Azure Active Directory Terms of Use 機能 (プレビュー)](https://docs.microsoft.com/azure/active-directory/active-directory-tou)」をご覧ください。
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>新しい Azure AD サインイン エクスペリエンス

**タイプ:** 新機能  
**サービス カテゴリ:** Azure AD  
**製品の機能:** ユーザー認証
 
Azure AD と Microsoft アカウント ID システムの UI は、外観と操作性が一貫したものになるよう再設計されました。 また、Azure AD サインイン ページは最初にユーザー名を収集し、2 番目の画面で資格情報を収集します。

詳細については、「[The new Azure AD sign-in experience is now in public preview (サインイン エクスペリエンスのパブリック プレビューを開始)](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)」をご覧ください。
 
---
 

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>回数が減ったサインイン プロンプト: Azure AD サインインの新しい "サインインしたままにする" エクスペリエンス

**タイプ:** 新機能  
**サービス カテゴリ:** Azure AD  
**製品の機能:** ユーザー認証
 
Azure AD サインイン ページの **[サインインしたままにする]** チェック ボックスは、お客様が正常に認証された後に表示される新しいプロンプトに置き換えられました。 

このプロンプトに **[はい]** と応答した場合、サービスにより永続的な更新トークンが付与されます。 この動作は、以前のエクスペリエンスで **[サインインしたままにする]** チェック ボックスをオンにした場合と同じになります。 フェデレーション テナントの場合、お客様がフェデレーション サービスで正常に認証された後にこのプロンプトが表示されます。

詳細については、「[Fewer sign-in prompts: The new "keep me signed in" experience for Azure AD is in preview (回数が減ったサインイン プロンプト: Azure AD の新しい "サインインしたままにする" エクスペリエンスのプレビューを開始)](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/)」をご覧ください。 

---
 

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>同意前に使用条件の展開を要求する構成の追加

**タイプ:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス
 
条項に同意する前に使用条件を展開することをお客様のユーザーに要求する、管理者向けのオプションです。

[ユーザーは使用条件を展開する必要があります] を **[オン]** にするか **[オフ]** にするかを選択します。 **[オン]** に設定すると、同意前に使用条件を承認するようユーザーに要求します。

詳細については、「[Azure Active Directory Terms of Use 機能 (プレビュー)](active-directory-tou.md)」をご覧ください。
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>対象となるロール割り当ての範囲指定されたアクティブ化

**タイプ:** 新機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management
 
範囲指定されたアクティブ化により、元の割り当ての既定値よりも低い自律性で、対象となる Azure リソースのロール割り当てをアクティブ化できます。 たとえば、ご利用のテナントでお客様がサブスクリプションの所有者として割り当てられているとします。 範囲指定されたアクティブ化では、サブスクリプションに含まれる最大 5 つのリソース (リソース グループや仮想マシンなど) に対して所有者のロールをアクティブ化できます。 アクティブ化を範囲指定すると、重要な Azure リソースに対して望ましくない変更が実行される可能性を低減できる場合があります。

詳しくは、「[Azure AD Privileged Identity Management とは](active-directory-privileged-identity-management-configure.md)」をご覧ください。
 
---
 

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Azure AD アプリ ギャラリーの新しいフェデレーション アプリ

**タイプ:** 新機能  
**サービス カテゴリ:** Enterprise アプリ  
**製品の機能:** サード パーティ統合
 
2017 年 12 月に、フェデレーションをサポートする次の新しいアプリがアプリ ギャラリーに追加されました。

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523)、Adobe Experience Manager、[EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685)、[Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe、[FactSet](https://go.microsoft.com/fwlink/?linkid=863525)、[IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517)、[MOBI](https://go.microsoft.com/fwlink/?linkid=863521)、[MobileIron Azure AD integration](https://go.microsoft.com/fwlink/?linkid=858027)、[Reflektive](https://go.microsoft.com/fwlink/?linkid=863518)、[SAML SSO for Bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520)、[SAML SSO for Bitbucket by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519)、[Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522)、WebHR、Zenegy Azure AD Integration。

閲覧できるすべてのチュートリアルの詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」をご覧ください。

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Azure AD ディレクトリ ロールに対する承認ワークフロー

**タイプ:** 変更された機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management
 
Azure AD ディレクトリ ロールの承認ワークフローが一般公開されました。

承認ワークフローでは、特権ロール管理者は特権ロール使用前に、対象となるロール メンバーに対してロールのアクティブ化を要求するよう要求できます。 複数のユーザーとグループに承認責任を委任できます。 対象となるロール メンバーは、承認が完了して各自のロールがアクティブ化されたときに通知を受け取ります。

---
 

### <a name="pass-through-authentication-skype-for-business-support"></a>パススルー認証 - Skype for Business のサポート

**タイプ:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証


パススルー認証では、先進認証 (オンライン トポロジとハイブリッド トポロジを含む) をサポートする Skype for Business クライアント アプリケーションへのユーザー サインインがサポートされるようになりました。 

詳細については、「[先進認証でサポートされる Skype for Business トポロジ](https://technet.microsoft.com/library/mt803262.aspx)」をご覧ください。
 
---
 

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Azure RBAC 用の Azure AD Privileged Identity Management の更新 (プレビュー)

**タイプ:** 変更された機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management
 
Azure ロールベースのアクセス制御 (RBAC) 用の Azure AD Privileged Identity Management (PIM) のパブリック プレビュー更新により、次のことが可能になりました。

* Just Enough Administration を使用する。
* リソース ロールのアクティブ化の前に承認を要求する。
* Azure AD と Azure RBAC の両方のロールの承認を要求するロールの、将来のアクティブ化をスケジュールする。

 
詳細については、「[Azure リソース向けの PIM (プレビュー)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)」をご覧ください。

 
---
 
## <a name="november-2017"></a>2017 年 11 月
 
### <a name="access-control-service-retirement"></a>Access Control Service の廃止



**タイプ:** 変更の計画  
**サービス カテゴリ:** Access Control Service  
**製品の機能:** Access Control Service 


 Azure Active Directory Access Control (Access Control Service とも呼ばれます) は 2018 年後半に廃止されます。 詳しいスケジュールや大まかな移行ガイダンスなど、詳細については数週間以内にお知らせします。 Access Control Service に関する質問をこのページにコメントとして残していただければ、チーム メンバーがお答えします。

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>ブラウザー アクセスを Intune Managed Browser に限定 


**タイプ:** 変更の計画  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護




Intune Managed Browser を承認されたアプリとして使用することで、Office 365 などの Azure AD 接続クラウド アプリへのブラウザー アクセスを制限できるようになります。 

アプリケーションベースの条件付きアクセスで以下の条件を構成できるようになります。

**クライアント アプリ:** ブラウザー

**この変更の影響**

現在、この条件を使用する場合、アクセスはブロックされます。 このプレビューが利用可能になると、すべてのアクセスで Managed Browser アプリケーションの使用が必須になります。 

この機能の詳細については、今後のブログとリリース ノートをお待ちください。 

詳細については、「[Azure Active Directory の条件付きアクセス](active-directory-conditional-access-azure-portal.md)」をご覧ください。

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD アプリベースの条件付きアクセス向けの承認されたクライアント アプリの新規追加

 
**タイプ:** 変更の計画  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護




以下のアプリが、[承認されたクライアント アプリ](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)の一覧に追加される予定です。

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


詳細については、「

- [承認されたクライアント アプリケーションの要件](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)
- [Azure AD のアプリベースの条件付きアクセス](active-directory-conditional-access-mam.md)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>使用条件での多言語のサポート



**タイプ:** 新機能    
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス/コンプライアンス





管理者は、PDF ドキュメントを複数含めた新しい使用条件を作成できるようになりました。 これらの PDF ドキュメントは、対応する言語にタグ付けできます。 PDF はユーザー設定に一致する言語で表示されます。 一致する言語がない場合は、既定の言語が表示されます。


---
 

### <a name="real-time-password-writeback-client-status"></a>パスワード ライトバック クライアントのリアルタイムの状態



**タイプ:** 新機能  
**サービス カテゴリ:** セルフ サービスのパスワード リセット  
**製品の機能:** ユーザー認証


 

オンプレミスのパスワード ライトバック クライアントの状態を確認できるようになりました。 このオプションは、[[パスワードのリセット]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) ページの **[オンプレミスの統合]** セクションで使用できます。 

オンプレミスのライトバック クライアントへの接続で問題が生じた場合、次の内容のエラー メッセージが表示されます。

- オンプレミスのライトバック クライアントへ接続できない理由の詳細
- 問題の解決に役立つドキュメントへのリンク 


詳細については、「[オンプレミスの統合](active-directory-passwords-how-it-works.md#on-premises-integration)」をご覧ください。

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Azure AD のアプリベースの条件付きアクセス 



 
**タイプ:** 新機能  
**サービス カテゴリ:** Azure AD  
**製品の機能:** ID のセキュリティと保護





[Azure AD のアプリベースの条件付きアクセス](active-directory-conditional-access-mam.md)を使用することで、Office 365 などの Azure AD 接続クラウド アプリへのアクセスを、Intune App Protection ポリシーがサポートされる[承認されたクライアント アプリ](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)に限定できるようになりました。 Intune App Protection ポリシーは、これらのクライアント アプリケーション上にある企業データの構成と保護に使用されます。

[アプリベース](active-directory-conditional-access-mam.md)の条件付きアクセス ポリシーを[デバイスベース](active-directory-conditional-access-policy-connected-applications.md)の条件付きアクセス ポリシーと組み合わせることで、個人および会社のデバイスのデータを柔軟に保護できます。

現在、アプリベースの条件付きアクセスでは以下の条件とコントロールを使用できます。

**サポートされるプラットフォームの条件**

- iOS
- Android

**クライアント アプリの条件**

- モバイル アプリとデスクトップ クライアント

**アクセス制御**

- 承認済みクライアント アプリを必須にする


詳細については、「[Azure Active Directory のアプリベースの条件付きアクセス](active-directory-conditional-access-mam.md)」をご覧ください。

 
---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Azure Portal での Azure AD デバイスの管理



**タイプ:** 新機能  
**サービス カテゴリ:** デバイスの登録と管理  
**製品の機能:** ID のセキュリティと保護

 



Azure AD に接続されているすべてのデバイスと、それらのデバイスに関連したアクティビティを 1 か所で確認できるようになりました。 Azure Portal にすべてのデバイスの ID と設定を管理できる新しい管理機能が追加されました。 このリリースでは、次の操作を行うことができます。

- Azure AD の条件付きアクセスで使用可能なすべてのデバイスを表示する。
- プロパティを表示する (ハイブリッド Azure AD 参加済みデバイスも含む)。
- Azure AD 参加済みデバイスの BitLocker キーを確認し、Intune などでデバイスを管理する。
- Azure AD デバイス関連の設定を管理する。

詳細については、[Azure Portal によるデバイスの管理](device-management-azure-portal.md)に関するページをご覧ください。



 
---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Azure AD の条件付きアクセス用デバイス プラットフォームとしての macOS のサポート 



**タイプ:** 新機能    
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護 
 

Azure AD 条件付きアクセス ポリシーのデバイス プラットフォームの条件に macOS を追加 (または除外) できるようになりました。 サポートされるデバイス プラットフォームに macOS が追加されたことで、以下の操作が可能になりました。

- **Intune を使用して macOS デバイスを登録、管理する。** iOS や Android などのプラットフォームと同様に、macOS でも登録を統合するポータル サイト アプリケーションを使用できるようになりました。 macOS 用の新しいポータル サイト アプリを使用すれば、Intune と Azure AD にデバイスを登録できます。
- **Intune で定義したご所属の組織のコンプライアンス ポリシーを厳守するよう macOS デバイスを設定する。** Azure Portal の Intune では、macOS デバイス用のコンプライアンス ポリシーを設定できます。 
- **Azure AD のアプリケーションにアクセスできるデバイスを、ポリシーに準拠した macOS デバイスのみに制限する。** 条件付きアクセス ポリシーを作成する際、別のデバイス プラットフォーム オプションとして macOS を選択できます。 Azure で設定済みのターゲット アプリケーション向けに macOS 専用の条件付きアクセス ポリシーを作成できます。

詳細については、「

- [Intune で macOS デバイス用のデバイス コンプライアンス ポリシーを作成する](https://aka.ms/macoscompliancepolicy)
- [Azure Active Directory の条件付きアクセス](active-directory-conditional-access-azure-portal.md)


 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Azure Multi-factor Authentication の ネットワーク ポリシー サーバー拡張機能 


**タイプ:** 新機能    
**サービス カテゴリ:** Multi-Factor Authentication  
**製品の機能:** ユーザー認証




Azure Multi-Factor Authentication のネットワーク ポリシー サーバー拡張機能は、既存のサーバーを使用してクラウド ベースの多要素認証機能をご利用の認証インフラストラクチャに追加します。 ネットワーク ポリシー サーバー拡張機能を使用すると、電話、テキスト メッセージ、またはモバイル アプリによる検証を、ご利用の既存の認証フローに追加できます。 新しいサーバーをインストール、構成、メンテナンスする必要はありません。 

この拡張機能は、Azure Multi-Factor Authentication Server をデプロイせずに仮想プライベート ネットワーク接続を保護する必要がある組織を対象に作成されました。 ネットワーク ポリシー サーバー拡張機能は、RADIUS とクラウド ベース Azure Multi-Factor Authentication の間のアダプターとして機能し、認証の 2 番目の要素をフェデレーション ユーザーまたは同期済みユーザーに提供します。


詳細については、「[Azure Multi-Factor Authentication と既存の NPS インフラストラクチャの統合](../multi-factor-authentication/multi-factor-authentication-nps-extension.md)」をご覧ください。

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>削除済みユーザーの復元または完全な削除


**タイプ:** 新機能    
**サービス カテゴリ:** ユーザー管理  
**製品の機能:** ディレクトリ 



Azure AD 管理センターで以下の操作が可能になりました。

- 削除済みユーザーを復元する。 
- ユーザーを完全に削除する。


**実行方法:**

1. Azure AD 管理センターの **[管理]** セクションで [[すべてのユーザー]](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) を選択します。 

2. **[表示]** リストで **[最近削除されたユーザー]** を選択します。 

3. 最近削除されたユーザーを 1 名または複数名選択して、復元するか完全に削除します。

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD アプリベースの条件付きアクセス向けの承認されたクライアント アプリの新規追加

 
**タイプ:** 変更された機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護


以下のアプリが、[承認されたクライアント アプリ](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)の一覧に追加されました。

- Microsoft Planner
- Azure Information Protection 


詳細については、「

- [承認されたクライアント アプリケーションの要件](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)
- [Azure AD のアプリベースの条件付きアクセス](active-directory-conditional-access-mam.md)


---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>条件付きアクセス ポリシーのコントロール間での "OR" の使用 


**タイプ:** 変更された機能    
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護

 
条件付きアクセス コントロールで (選択したコントロールのいずれかを必須にする) "OR" を使用できるようになりました。 この機能を使用すると、アクセス コントロール間を "OR" でつないでポリシーを作成できます。 たとえば、この機能を使用して、多要素認証を使用したサインイン "または" 準拠デバイスの使用をユーザーに求めるポリシーを作成できます。

詳細については、「[Azure Active Directory の条件付きアクセスのコントロール](active-directory-conditional-access-controls.md)」をご覧ください。

 
---

### <a name="aggregation-of-real-time-risk-events"></a>リアルタイムのリスク イベントの集計


**タイプ:** 変更された機能    
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティと保護


Azure AD Identity Protection では、指定日に同一の IP アドレスで発生したすべてのリアルタイム リスク イベントが、リスク イベントの種類ごとに集計されるようになりました。 この変更により表示されるリスク イベントの量が制限されますが、ユーザーのセキュリティが変更されることはありません。

根本となるリアルタイム検出は、ユーザーのサインインのたびに行われます。 多要素認証またはアクセスのブロックを行うサインインのリスク セキュリティ ポリシーを設定している場合、このポリシーは引き続きリスクのあるサインインのたびにトリガーされます。

 
---
 




## <a name="october-2017"></a>2017 年 10 月


### <a name="deprecate-azure-ad-reports"></a>Azure AD レポートの廃止


**タイプ:** 変更の計画  
**サービス カテゴリ:** レポート  
**製品の機能:** ID ライフサイクル管理  



Azure Portal では次の機能が提供されています。

- 新しい Azure AD 管理コンソール。
- アクティビティ レポートとセキュリティ レポートのための新しい API。
 
これらの新機能の導入に伴い、/reports エンドポイントのレポート API は 2017 年 12 月 10 日に廃止されました。 

---

### <a name="automatic-sign-in-field-detection"></a>サインイン フィールドの自動検出


**タイプ:** 固定   
**サービス カテゴリ:** マイ アプリ  
**製品の機能:**シングル サインオン  



Azure AD は、HTML ユーザー名とパスワード フィールドをレンダリングするアプリケーションのサインイン フィールドの自動検出をサポートしています。 この手順は「[アプリケーションのサインイン フィールドを自動的にキャプチャする方法](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application)」に示されています。 この機能は、[Azure Portal](http://aad.portal.azure.com) の **[エンタープライズ アプリケーション]** ページで*ギャラリー以外のアプリケーション*を追加することで表示されます。 さらに、Web URL を入力してページを保存することにより、この新しいアプリケーションの**シングル サインオン** モードを**パスワード ベースのシングル サインオン**を実行するように構成できます。
 
サービスの問題により、この機能は一時的に無効化されていました。 問題が解決されたため、サインイン フィールドの自動検出が再び利用可能になりました。

---

### <a name="new-multi-factor-authentication-features"></a>新しい多要素認証機能


**タイプ:** 新機能  
**サービス カテゴリ:** Multi-Factor Authentication  
**製品の機能:** ID のセキュリティと保護  



多要素認証 (MFA) は、組織を保護する上で欠かせない要素です。 資格情報の適応性を高めて多要素認証をよりシームレスに利用できるようにするため、以下の機能が追加されました。 

- MFA 結果へのプログラムによるアクセスなどを含め、多要素認証結果が Azure AD サインイン レポートに直接統合されました。
- MFA 構成 が Azure Portal の Azure AD 構成エクスペリエンスに、より緊密に統合されました。

このパブリック プレビューでは、MFA の管理およびレポートが、コアとなる Azure AD の構成エクスペリエンスに組み込まれました。 Azure AD を使用しながら MFA の管理ポータル機能を管理できます。

詳細については、「[Azure Portal の多要素認証レポートのリファレンス](active-directory-reporting-activity-sign-ins-mfa.md)」をご覧ください。 


---

### <a name="terms-of-use"></a>使用条件



**タイプ:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス  



Azure AD の使用条件を利用して、ユーザーに対して法律またはコンプライアンスの要件に関する免責事項を表示できます。

Azure AD Terms of Use は、次のシナリオで使用できます。

- ご所属の組織内のすべてのユーザーに対する一般的な使用条件
- ユーザーの属性 (たとえば、動的グループによって行われる、医師と看護師や、国内の従業員と国外の従業員などの区別) に基づいた特定の使用条件
- ビジネスへの影響が大きいアプリ (Salesforce など) へのアクセスのための特定の使用条件

詳細については、[Azure AD の使用条件](active-directory-tou.md)に関するページをご覧ください。


---

### <a name="enhancements-to-privileged-identity-management"></a>Privileged Identity Management の機能強化


**タイプ:** 新機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management  


Azure AD Privileged Identity Management で、組織内の以下の Azure リソース (プレビュー) へのアクセスを管理、制御、監視できるようになりました。

- サブスクリプション
- リソース グループ
- 仮想マシン 

Azure RBAC 機能を利用する Azure Portal 内のすべてのリソースで、Azure AD Privileged Identity Management から提供されるセキュリティおよびライフサイクルの管理機能を使用できます。

詳細については、[Azure リソース向けの Privileged Identity Management](privileged-identity-management/azure-pim-resource-rbac.md) に関するページをご覧ください。


---

### <a name="access-reviews"></a>アクセス レビュー


**タイプ:** 新機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** ガバナンス  



組織はアクセス レビュー (プレビュー) を使用して、グループ メンバーシップの管理とエンタープライズ アプリケーションへのアクセスを効率的に行うことができます。 

- アプリケーションへのアクセスおよびグループのメンバーシップのアクセス レビューを使うと、ゲスト ユーザーによるアクセスを認定できます。 レビュー担当者は、アクセス レビューによって提供される分析情報に基づき、ゲストが引き続きアクセスすることを許可するかどうかを効率的に決定できます。
- アクセス レビューを使って従業員のアプリケーション アクセスとグループ メンバーシップを認定できます。

アクセス レビューの制御を組織に関連するプログラムに集めて、コンプライアンスまたはリスクを受けやすいアプリケーションのレビューを追跡できます。

詳細については、「[Azure AD アクセス レビュー](active-directory-azure-ad-controls-access-reviews-overview.md)」をご覧ください。


---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>マイ アプリおよび Office 365 アプリ ランチャーでのサード パーティ製アプリケーションの非表示



**タイプ:** 新機能  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:**シングル サインオン  



新しい**アプリの非表示**プロパティにより、ご利用のユーザー ポータルに表示されるアプリを管理しやすくなりました。 アプリの非表示は、バックエンド サービス用のアプリ タイルが表示されていたり、タイルが重複してユーザーのアプリ ランチャーが整理されていなかったりする場合に役立ちます。 切り替えボタンはサード パーティ製アプリの **[プロパティ]** セクションにあり、**[Visible to user?]\(ユーザーに表示しますか?\)** というラベルが付いています。 PowerShell を介してプログラムでアプリを非表示にすることもできます。 

詳細については、[Azure AD でユーザーのエクスペリエンスからサードパーティ製アプリケーションを非表示にする](active-directory-coreapps-hide-third-party-app.md)方法に関するページをご覧ください。 


**利用可能な機能**

 新しい管理コンソールへの移行の一環として、Azure AD のアクティビティ ログを取得する新しい 2 つの API が利用可能になりました。 この新しい API セットでは監査およびサインイン アクティビティが追加されているだけでなく、より充実したフィルター機能と並べ替え機能も備わっています。 以前はセキュリティ レポートで提供されていたデータに、Microsoft Graph の Identity Protection リスク イベント API を使用してアクセスできます。


## <a name="september-2017"></a>2017 年 9 月

### <a name="hotfix-for-identity-manager"></a>Identity Manager の修正プログラム


**タイプ:** 変更された機能  
**サービス カテゴリ:** Identity Manager  
**製品の機能:** ID ライフサイクル管理  



2017 年 9 月 25 日に、Identity Manager 2016 Service Pack 1 の修正プログラム ロールアップ パッケージ (ビルド 4.4.1642.0) の提供が開始されました。 このロールアップ パッケージの説明は以下のとおりです。

- 問題を解決し、機能を強化します。
- Identity Manager 2016 のビルド 4.4.1459.0 までのすべての Identity Manager 2016 Service Pack 1 更新プログラムに代わる、累積的な更新プログラムです。 
- ご利用のコンピューターに Identity Manager 2016 ビルド 4.4.1302.0 がインストールされている必要があります。 

詳細については、[Identity Manager 2016 Service Pack 1 の修正プログラム ロールアップ パッケージ (ビルド 4.4.1642.0)](https://support.microsoft.com/help/4021562) に関するページをご覧ください。 

---
