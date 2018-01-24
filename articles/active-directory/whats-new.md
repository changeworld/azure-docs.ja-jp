---
title: "新機能 Azure Active Directory のリリース ノート | Microsoft Docs"
description: "最新のリリース ノート、既知の問題、バグの修正、非推奨になった機能、予定されている変更点を含む、Azure Active Directory (Azure AD) の新着情報について説明します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
featureFlags: clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 6e1cf6e2ee717ef7629e1388d7bca2090eed46fa
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2018
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory の新着情報




> [![RSS](./media/whats-new/feed-icon-16x16.png)](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us) [フィード](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20in%20azure%20active%20directory%22&locale=en-us)をサブスクライブして、Azure Active Directory の新着情報を常に把握するようにしてください。



マイクロソフトは、Azure Active Directory の改善を継続的に行っています。 お客様に常に最新の開発情報を入手していただけるよう、このトピックでは以下に関する情報を提供します。

-   最新のリリース 
-   既知の問題 
-   バグの修正 
-   非推奨になった機能 
-   変更の計画 

このページは月単位で更新されるため、定期的にご確認ください。


## <a name="december-2017"></a>2017 年 12 月
 

### <a name="terms-of-use-in-the-access-panel-for-end-users"></a>エンド ユーザー向けのアクセス パネルでの使用条件

**タイプ:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス/コンプライアンス
 
エンド ユーザーは、アクセス パネルに移動し、以前に承諾した使用条件を表示できるようになりました。

ユーザーは、同意した使用条件を確認できます。 これは、次の手順で実行できます。

1. [MyApps ポータル](https://myapps.microsoft.com)に移動してサインインします。

2. 右上隅に表示される自分の名前をクリックし、ドロップダウンから **[プロファイル]** を選択します。 

3. プロファイルで、**[使用条件の確認]** をクリックします。 

4. そこから同意した使用条件を確認できます。 

詳しくは、「[Azure Active Directory Terms of Use 機能 (プレビュー)](https://docs.microsoft.com/azure/active-directory/active-directory-tou)」をご覧ください
 
---
 

### <a name="new-azure-ad-sign-in-experience"></a>新しい Azure AD サインイン エクスペリエンス

**タイプ:** 新機能  
**サービス カテゴリ:** Azure AD  
**製品の機能:** ユーザー認証
 
Azure AD と Microsoft アカウント ID システムの統合への道のりの一貫として、両方のシステムで UI を再設計し、一貫したルック アンド フィールを持つようにしました。 さらに、ユーザー名を最初に収集してから 2 番目の画面で資格情報を収集するように、Azure AD サインイン ページを改ページしました。

詳しくは、「[The new Azure AD Signin Experience is now in Public Preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)」(新しい Azure AD サインイン エクスペリエンスのパブリック プレビューを開始) をご覧ください
 
---
 

### <a name="fewer-login-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-login"></a>より少数のログイン プロンプト: Azure AD ログインの新しい "サインインしたままにする" エクスペリエンス

**タイプ:** 新機能  
**サービス カテゴリ:** Azure AD  
**製品の機能:** ユーザー認証
 
Azure AD ログイン ページの **[サインインしたままにする]** チェックボックスを、ユーザーが正常に認証された後に表示される新しいプロンプトに置き換えました。 

ユーザーがこのプロンプトに **[はい]** と応答した場合、サービスにより永続的な更新トークンが付与されます。 これは、ユーザーが以前のエクスペリエンスで **[サインインしたままにする]** チェック ボックスをオンした場合と同じ動作です。 フェデレーション テナントの場合、このプロンプトは、ユーザーがフェデレーション サービスで正常に認証された後に表示されます。

詳しくは、「[Fewer login prompts: The new "Keep me signed in" experience for Azure AD is in preview](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/)」(より少数のログイン プロンプト: Azure AD の新しい "サインインしたままにする" エクスペリエンスがプレビュー段階に) をご覧ください 

---
 

### <a name="add-configuration-to-require-the-tou-to-be-expanded-prior-to-accepting"></a>同意する前に TOU の展開を要求する構成の追加。

**タイプ:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス
 
条項に同意する前に使用条件を展開することをエンド ユーザーに要求する管理者向けオプションを追加しました。

[ユーザーは使用条件を展開する必要があります] をオンにするかオフにするかを選択します。 オンに設定された場合、エンド ユーザーは、同意する前に使用条件を表示する必要があります。

詳しくは、「[Azure Active Directory Terms of Use 機能 (プレビュー)](active-directory-tou.md)」をご覧ください
 
---
 

### <a name="scoped-activation-for-eligible-role-assignments"></a>対象となるロール割り当ての範囲指定されたアクティブ化

**タイプ:** 新機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management
 
範囲指定されたアクティブ化により、元の割り当ての既定値よりも低い自律性で対象となる Azure リソースのロール割り当てをアクティブ化できます。 たとえば、テナントでサブスクリプションの所有者を割り当てられているとします。 範囲指定されたアクティブ化では、サブスクリプションに含まれる最大 5 つのリソース (リソース グループ、Virtual Machines など) に対して所有者をアクティブにできます。アクティブ化を範囲指定すると、重要な Azure リソースに対して望ましくない変更が実行される可能性を低減できます。

詳しくは、「[Azure AD Privileged Identity Management とは](active-directory-privileged-identity-management-configure.md)」をご覧ください。
 
---
 

### <a name="new-federated-apps-in-azure-ad-app-gallery"></a>Azure AD アプリ ギャラリーの新しいフェデレーション アプリ

**タイプ:** 新機能  
**サービス カテゴリ:** Enterprise アプリ  
**製品の機能:** サード パーティ統合
 
2017 年 12 月に、フェデレーションをサポートする次の新しいアプリをアプリ ギャラリーに追加しました。

|Name|統合の種類|[説明]|
|:-- |----------------|:----------|
|EFI Digital StoreFront|SAML 2.0|[Web 2 Print アプリケーション](https://go.microsoft.com/fwlink/?linkid=861685)|
|Vodeclic|SAML 2.0|[Azure AD を使用して、ユーザー アクセスを管理し、Vodeclic によるシングル サインオンを有効にします](https://go.microsoft.com/fwlink/?linkid=863522)。  既存の Vodeclic アカウントが必要です。|
|Accredible|SAML 2.0|[証明書、バッジ、ブロックチェーン資格情報を作成、管理、配信します](https://go.microsoft.com/fwlink/?linkid=863523)|
|FactSet|SAML 2.0|[FactSet の FDSWeb アプリケーションにシングル サインオンできます](https://go.microsoft.com/fwlink/?linkid=863525)|
|MobileIron Azure AD の統合|SAML 2.0|[MobileIron](https://go.microsoft.com/fwlink/?linkid=858027) の目的は、現代の企業がエンドユーザーのプライバシーと信頼を維持しながら、モバイルやクラウドに移行するときに情報をセキュリティで保護し、管理できるようにすることです。|
|IMAGE WORKS|SAML 2.0|Azure AD を使用して、ユーザー アクセスを管理し、ユーザー アカウントをプロビジョニングし、[IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517) によるシングル サインオンを有効にします。 既存の IMAGE WORKS サブスクリプションが必要です。|
|SAML SSO for Bitbucket by resolution GmbH|SAML 2.0|[SSO Bitbucket](https://go.microsoft.com/fwlink/?linkid=863519) は Azure AD に認証を委任し、Azure AD に既にログインしているユーザーは Bitbucket に直接アクセスできます。 ユーザーは、SAML 属性のデータを即座に作成および更新できます。|
|SAML SSO for Bamboo by resolution GmbH|SAML 2.0|[SSO Bamboo](https://go.microsoft.com/fwlink/?linkid=863520) は、Azure AD に認証を委任し、Azure AD に既にログインしているユーザーは Bamboo に直接アクセスできます。|
|Communifire|SAML 2.0|[Communifire](https://go.microsoft.com/fwlink/?linkid=861676) は、従業員とビジネスをサポートする、完全な機能を備えた最新のソーシャル イントラネット ソフトウェアです。|
|MOBI|SAML 2.0|[デバイス エコシステム全体を集中化、把握、制御します](https://go.microsoft.com/fwlink/?linkid=863521)。|
|Reflektive|SAML 2.0|[Reflektive](https://go.microsoft.com/fwlink/?linkid=863518) は、パフォーマンス管理、リアルタイム フィードバック、目標設定のための最新のプラットフォームです。 従業員が自身の開発を推進できるので、より戦略的に取り組むことができます。|
|CybSafe|OpenID Connect と OAuth|CybSafe は GCHQ 認定を受けたサイバー対応プラットフォームです。 高度なテクノロジとデータ分析を使用して、サイバー セキュリティとデータ保護のリスクの人間的な側面を確実に低減します。|
|WebHR|OpenID Connect と OAuth|すべてのユーザーのお気に入りのオール イン ワン ソーシャル HR ソフトウェア。 197 か国の 20,000 を超える企業によって信頼されています|
 |Zenegy Azure AD Integration|OpenID Connect と OAuth|このアプリでは、Zenegy へのログインに自社の Azure Active Directory 資格情報を使用できます。|
|Adobe Experience Manager|SAML 2.0|Adobe Experience Manager (AEM) は、Web サイト、モバイル アプリ、フォームをビルドするための包括的なコンテンツ管理プラットフォーム ソリューションで、マーケティングのコンテンツと資産を簡単に管理できるようにします。|

 
---
 

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Azure AD ディレクトリ ロールに対する承認ワークフロー

**タイプ:** 変更された機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management
 
Azure AD ディレクトリ ロールの承認ワークフローが一般公開されました。

承認ワークフローでは、特権ロール管理者が、特権ロールを使用する前に、対象となるロール メンバーにロールのアクティブ化要求を要求できます。
複数のユーザーとグループに承認責任を委任でき、対象となるロール メンバーは、承認が完了し、ロールがアクティブになると通知を受け取ります

---
 

### <a name="pass-through-authentication---skype-for-business-support"></a>パススルー認証 - Skype for Business のサポート

**タイプ:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証


パススルー認証では、先進認証 (オンライン トポロジとハイブリッド トポロジを含む) をサポートする Skype for Business クライアント アプリケーションへのユーザー サインインがサポートされるようになりました。 

詳しくは、「[先進認証でサポートされる Skype for Business トポロジ](https://technet.microsoft.com/library/mt803262.aspx)」をご覧ください。
 
---
 

### <a name="updates-to-azure-active-directory-privileged-identity-management-pim-for-azure-rbac-preview"></a>Azure RBAC 用の Azure Active Directory Privileged Identity Management (PIM) に対する更新 (プレビュー)

**タイプ:** 変更された機能  
**サービス カテゴリ:** PIM  
**製品の機能:** Privileged Identity Management
 
Azure RBAC 用の Azure Active Directory Privileged Identity Management (PIM) のパブリック プレビューの更新により、次のことが可能になりました。

Just Enough Administration Require 承認を使用して、リソース ロールをアクティブにします。AAD と Azure RBAC の両方のロールの承認を要求するロールの将来のアクティブ化をスケジュールします

 
詳しくは、「[Azure リソース向けの PIM (プレビュー)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)」をご覧ください

 
---
 
## <a name="november-2017"></a>2017 年 11 月
 
### <a name="retiring-acs"></a>ACS の廃止



**タイプ:** 変更の計画  
**サービス カテゴリ:** ACS  
**製品の機能:** Access Control Service 


Microsoft Azure Active Directory Access Control (Access Control Service または ACS とも呼ばれます) は 2018 年後半に廃止されます。  詳しいスケジュールや大まかな移行ガイダンスなどの詳細については、数週間以内にお知らせします。 この間に、ACS に関する質問があればこのページにコメントを投稿してください。Microsoft のチームのメンバーが回答します。

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>ブラウザー アクセスを Intune Managed Browser に限定する 


**タイプ:** 変更の計画  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護




この動作により、Intune Managed Browser を承認されたアプリとして使用して、Office 365 やその他の Azure AD 接続クラウド アプリへのブラウザー アクセスを制限できるようになります。 

この変更では、アプリケーションベースの条件付きアクセスで以下の条件を設定できるようになります。

**クライアント アプリ:** ブラウザー

**この変更の影響**

現在、この条件を使用する場合アクセスはブロックされます。 この動作のプレビューが利用可能になると、すべてのアクセスで Managed Browser アプリケーションの使用が必須になります。 

この機能の詳細については、今度のブログおよびリリース ノートをお待ちください。 

詳細については、「[Azure Active Directory の条件付きアクセス](active-directory-conditional-access-azure-portal.md)」を参照してください。

 
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

- [Azure Active Directory のアプリベースの条件付きアクセス](active-directory-conditional-access-mam.md)


---

### <a name="terms-of-use-support-for-multiple-languages"></a>使用条件での多言語のサポート



**タイプ:** 新機能    
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス/コンプライアンス





管理者は、PDF ドキュメントを複数含めた新しい使用条件 (TOU) を作成できるようになりました。 これらの PDF ドキュメントは、対応する言語にタグ付けできます。 スコープに該当するユーザーには、PDF はユーザー設定に基づいて一致する言語で表示されます。 一致する言語がない場合は、既定の言語が表示されます。


---
 

### <a name="realtime-password-writeback-client-status"></a>パスワード ライトバック クライアントのリアルタイムの状態



**タイプ:** 新機能  
**サービス カテゴリ:** SSPR  
**製品の機能:** ユーザー認証


 

オンプレミスのパスワード ライトバック クライアントの状態を確認できるようになりました。 このオプションは、**[[パスワードのリセット]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)** ページの **[オンプレミスの統合]** セクションで使用できます。 

オンプレミスのライトバック クライアントへの接続で問題が生じた場合、以下の内容のエラー メッセージが表示されます。

- オンプレミスのライトバック クライアントへ接続できない理由の詳細 
- 問題の解決に役立つドキュメントへのリンク 


詳細については、「[オンプレミスの統合](active-directory-passwords-how-it-works.md#on-premises-integration)」を参照してください。

 
---


### <a name="azure-ad-app-based-conditional-access"></a>Azure AD のアプリベースの条件付きアクセス 



 
**タイプ:** 新機能  
**サービス カテゴリ:** Azure AD  
**製品の機能:** ID のセキュリティと保護





[Azure AD アプリベースの条件付きアクセス](active-directory-conditional-access-mam.md)を使用して、Office 365 やその他の Azure AD 接続クラウド アプリへのアクセスを Intune App Protection ポリシーがサポートされる[承認されたクライアント アプリ](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)に限定できるようになりました。 Intune App Protection ポリシーは、これらのクライアント アプリケーション上にある企業データの構成と保護に使用されます。

[アプリベース](active-directory-conditional-access-mam.md)の条件付きアクセス ポリシーを[デバイスベース](active-directory-conditional-access-policy-connected-applications.md)の条件付きアクセス ポリシーと組み合わせることで、個人および会社のデバイスのデータを柔軟に保護できます。

現在、アプリベースの条件付きアクセスでは以下の条件とコントロールを使用できます。

**サポートされるプラットフォームの条件**

- iOS
- Android

**クライアント アプリの条件**

- モバイル アプリとデスクトップ クライアント

**アクセス制御**

- 承認済みクライアント アプリを必須にする


詳細については、「[Azure Active Directory のアプリベースの条件付きアクセス](active-directory-conditional-access-mam.md)」を参照してください。

 
---

### <a name="managing-azure-ad-devices-in-the-azure-portal"></a>Azure Portal での Azure AD デバイスの管理



**タイプ:** 新機能  
**サービス カテゴリ:** デバイスの登録と管理  
**製品の機能:** ID のセキュリティと保護

 



Azure AD に接続されているすべてのデバイスと、それらのデバイス関連のアクティビティを 1 か所で確認できるようになりました。 Azure Portal にすべてのデバイスの ID と設定を管理できる新しい管理機能が追加されました。 このリリースでは以下の操作を行うことができます。

- Azure AD の条件付きアクセスで使用可能なすべてのデバイスを表示する

- プロパティを表示する (ハイブリッド Azure AD 参加済みデバイスも含む)

- Azure AD 参加済みデバイスの BitLocker キーを確認し Intune などでデバイスを管理する

- Azure AD デバイス関連の設定を管理する


詳細については、「[Azure Portal によるデバイスの管理](device-management-azure-portal.md)」を参照してください。



 
---

### <a name="support-for-macos-as-device-platform-for-azure-ad-conditional-access"></a>Azure AD の条件付きアクセス用デバイス プラットフォームとしての macOS のサポート 



**タイプ:** 新機能    
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護 
 

Azure AD 条件付きアクセス ポリシーのデバイス プラットフォームの条件に macOS を追加 (または除外) できるようになりました。 サポートされるデバイス プラットフォームに macOS が追加されたことで、以下の操作が可能になりました。

- **Intune を使用して macOS デバイスを登録および管理する** - iOS や Android などの他のプラットフォームと同様に、macOS でも登録を統合するポータル サイト アプリケーションを使用できるようになりました。 この macOS 用の新しいポータル サイト アプリでは、Intune および Azure AD へデバイスを登録できます。
 
- **Intune で定義されている組織のコンプライアンス ポリシーを macOS デバイスに遵守させる** - Azure Portal の Intune で、macOS デバイス向けのコンプライアンス ポリシーを設定できるようになりました。 
  
- **Azure AD 内のアプリケーションへのアクセスを準拠 macOS デバイスのみに制限する** - 条件付きアクセス ポリシーの作成で、macOS が個別のデバイス プラットフォーム オプションとして追加されました。 このオプションを使用すると、Azure で設定済みのターゲット アプリケーション向けに macOS 固有の条件付きアクセス ポリシーを作成できます。

詳細については、「

- [Intune で macOS デバイス用のデバイス コンプライアンス ポリシーを作成する](https://aka.ms/macoscompliancepolicy)
- [Azure Active Directory の条件付きアクセス](active-directory-conditional-access-azure-portal.md)


 
---

### <a name="nps-extension-for-azure-mfa"></a>Azure MFA の NPS 拡張機能 


**タイプ:** 新機能    
**サービス カテゴリ:** MFA  
**製品の機能:** ユーザー認証




Azure MFA のネットワーク ポリシー サーバー (NPS) 拡張機能は、既存のサーバーを使用してクラウド ベースの MFA 機能を認証インフラストラクチャに追加します。 NPS 拡張機能を使用すると、新しいサーバーをインストール、構成、管理することなく、電話、テキスト メッセージ、またはモバイル アプリによる検証を既存の認証フローに追加できます。 

この拡張機能は、Azure MFA Server をデプロイしないで VPN 接続を保護する必要のある組織を対象に作成されました。 NPS 拡張機能は、RADIUS とクラウド ベース Azure MFA の間のアダプターとして機能し、フェデレーション ユーザーまたは同期済みユーザーに、認証の 2 番目の要素を提供します。


詳細については、「[Azure Multi-Factor Authentication と既存の NPS インフラストラクチャの統合](../multi-factor-authentication/multi-factor-authentication-nps-extension.md)」を参照してください。

 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>削除済みユーザーの復元または完全な削除


**タイプ:** 新機能    
**サービス カテゴリ:** ユーザー管理  
**製品の機能:** ディレクトリ 



Azure AD 管理センターで以下の操作が可能になりました。

- 削除済みユーザーの復元 
- ユーザーの完全な削除 


**実行方法:**

1. Azure AD 管理センターの **[管理]** セクションで [**[すべてのユーザー]**](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) を選択します。 

2. **[表示]** リストで **[最近削除されたユーザー]** を選択します。 

4. 最近削除されたユーザーを 1 名または複数選択して、復元するか完全に削除します。

 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD アプリベースの条件付きアクセス向けの承認されたクライアント アプリの新規追加

 
**タイプ:** 変更された機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護


以下のアプリが、[承認されたクライアント アプリ](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)の一覧に追加されました。

- Microsoft Planner

- Microsoft Azure Information Protection 


詳細については、「

- [承認されたクライアント アプリケーションの要件](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Azure Active Directory のアプリベースの条件付きアクセス](active-directory-conditional-access-mam.md)


---

### <a name="ability-to-or-between-controls-in-a-conditional-access-policy"></a>条件付きアクセス ポリシーのコントロールの "OR" 結合機能 


**タイプ:** 変更された機能    
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護

 
条件付きアクセス コントロールを "OR" で結合する (選択したコントロールのいずれかを必須にする) 機能がリリースされました。 この機能を使用すると、アクセス コントロールを **OR** で結合してポリシーを作成することができます。 たとえば、この機能を使用して、多要素認証を使用したサインイン**または**準拠デバイスの使用をユーザーに求めるポリシーを作成できます。

詳細については、「[Azure Active Directory の条件付きアクセスのコントロール](active-directory-conditional-access-controls.md)」を参照してください。

 
---

### <a name="aggregation-of-realtime-risk-events"></a>リアルタイムのリスク イベントの集計


**タイプ:** 変更された機能    
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティと保護


管理能力の向上のため、Azure AD Identity Protection では、指定日に同一の IP アドレスで発生したすべてのリアルタイム リスク イベントが、リスク イベントの種類ごとに集計されるようになりました。 この変更により表示されるリスク イベントの量が制限されますが、ユーザーのセキュリティが変更されることはありません。

根本となるリアルタイム検出は、ユーザーのログインのたびに行われます。 多要素認証またはアクセスのブロックを行うサインインのリスク セキュリティ ポリシーを設定をしている場合、このポリシーは引き続きリスクのあるサインインのたびにトリガーされます。

 
---
 




## <a name="october-2017"></a>2017 年 10 月


### <a name="deprecating-azure-ad-reports"></a>Azure AD レポートの廃止


**タイプ:** 変更の計画  
**サービス カテゴリ:** レポート  
**製品の機能:** ID ライフサイクル管理  



Azure Portal では次の機能が提供されています。

- 新しい Azure Active Directory 管理コンソール 
- アクティビティ レポートとセキュリティ レポートのための新しい API
 
これらの新機能の導入に伴い、**/reports** エンドポイントのレポート API は 2017 年 12 月 10 日に廃止されます。 

---

### <a name="automatic-sign-in-field-detection"></a>サインイン フィールドの自動検出


**タイプ:** 固定   
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** SSO  



Azure Active Directory は、HTML ユーザー名とパスワード フィールドをレンダリングするアプリケーションのサインイン フィールドの自動検出をサポートしています。  この手順は「[アプリケーションのサインイン フィールドを自動的にキャプチャする方法](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application)」に示されています。 この機能は、[Azure Portal](http://aad.portal.azure.com) の **[エンタープライズ アプリケーション]** ページで*ギャラリー以外のアプリケーション*を追加することで表示されます。 さらに、Web URL を入力してページを保存することにより、この新しいアプリケーションの **シングル サインオン** モードを**パスワード ベースのシングル サインオン**を実行するように構成できます。
 
サービスの問題により、この機能はしばらくの間、一時的に無効化されていました。 問題が解決されたため、サインイン フィールドの自動検出が再び利用可能になりました。

---

### <a name="new-mfa-features"></a>MFA の新機能


**タイプ:** 新機能  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティと保護  



多要素認証 (MFA) は、組織を保護する上で欠かせない要素です。 資格情報の適応性を高めて多要素認証をよりシームレスに利用できるようにするため、以下の機能が追加されました。 

- 多要素認証結果の、Azure AD サインイン レポートへの直接統合 (プログラムによる MFA へのアクセスを含む)

- Azure Portal の Azure AD 構成エクスペリエンスと MFA 構成の統合の緊密化

このパブリック プレビューでは、MFA の管理およびレポートが、コアとなる Azure AD の構成エクスペリエンスに組み込まれました。 両方の機能の集約により、Azure AD を使用しながら MFA の管理ポータル機能を管理できます。

詳しくは、「[Azure Portal の多要素認証レポートの参照情報](active-directory-reporting-activity-sign-ins-mfa.md)」をご覧ください。 


---

### <a name="introducing-terms-of-use"></a>使用条件の導入



**タイプ:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス  



Azure AD Terms of Use では、エンド ユーザーに対して簡単な方法で情報を提示できます。 これにより、ユーザーは法律またはコンプライアンスの要件に関する免責事項を確認できます。

Azure AD Terms of Use は、次のシナリオで使用できます。

- 組織内のすべてのユーザーに対する一般的な使用条件。 

- ユーザーの属性に基づく特定の使用条件 (例:  動的グループによって行われる、医師と看護師や、国内従業員と国際従業員などの区別)。 

- ビジネスへの影響が大きいアプリ (Salesforce など) へのアクセスのための特定の使用条件。

詳細については、[Azure Active Directory Terms of Use](active-directory-tou.md)に関する記事をご覧ください。


---

### <a name="enhancements-to-privileged-identity-management"></a>Privileged Identity Management の機能強化


**タイプ:** 新機能  
**サービス カテゴリ:** PIM  
**製品の機能:** Privileged Identity Management  


Azure Active Directory Privileged Identity Management (PIM) で、組織内の以下の Azure リソース (プレビュー) へのアクセスを管理、制御、監視できるようになりました。

- サブスクリプション
- リソース グループ
- 仮想マシン 

Azure のロール ベースのアクセス制御 (RBAC) 機能を利用する Azure Portal 内のすべてのリソースで、Azure AD PIM から提供されるセキュリティおよびライフサイクルの管理機能を使用できます。

詳細については、「[Azure リソースの PIM](privileged-identity-management/azure-pim-resource-rbac.md)」をご覧ください。


---

### <a name="introducing-access-reviews"></a>アクセス レビューの導入


**タイプ:** 新機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** ガバナンス  



組織でアクセス レビュー (プレビュー) を使うことにより、グループ メンバーシップおよびエンタープライズ アプリケーションへのアクセスを、効率的に管理できます。 

- アプリケーションへのアクセスおよびグループのメンバーシップのアクセス レビューを使うと、ゲスト ユーザーによるアクセスを認定できます。 アクセス レビューによって提供される洞察を使用して、ゲストが引き続きアクセスする必要があるかどうかを効率的に決定できます。

- アクセス レビューを使って従業員のアプリケーション アクセスとグループ メンバーシップを認定できます。

アクセス レビューの制御を組織に関連するプログラムに集めて、コンプライアンスまたはリスクを受けやすいアプリケーションのレビューを追跡できます。

詳細については、「[Azure AD アクセス レビュー](active-directory-azure-ad-controls-access-reviews-overview.md)」をご覧ください。


---

### <a name="hiding-third-party-applications-from-my-apps-and-the-office-365-launcher"></a>マイ アプリおよび Office 365 ランチャーでサード パーティ製アプリケーションを非表示にする



**タイプ:** 新機能  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** SSO  



新しい**アプリの非表示**プロパティにより、ユーザー ポータルに表示されるアプリを管理しやすくなりました。 アプリの非表示は、アプリ タイルにバックエンド サービスのタイルや重複したタイルが表示され、ユーザーのアプリ ランチャーが雑然となっている場合に役立ちます。 切り替えボタンはサード パーティ製アプリの [プロパティ] セクションにあり、**[ユーザーに表示しますか?]** というラベルが付いています。 PowerShell を介してプログラムでアプリを非表示にすることもできます。 

詳細については、「[Azure Active Directory でユーザーのエクスペリエンスからサードパーティ製アプリケーションを非表示にする](active-directory-coreapps-hide-third-party-app.md)」をご覧ください。 


**利用可能な機能**

 新しい管理コンソールへの移行の一環として、Azure AD のアクティビティ ログを取得する新しい 2 つの API が利用可能になりました。 この新しい API セットでは監査およびサインイン アクティビティが追加されているだけでなく、より充実したフィルター機能と並べ替え機能も備わっています。 以前はセキュリティ レポートを通じて提供されていたデータに、Microsoft Graph の Identity Protection リスク イベント API を通じてアクセスできます。


## <a name="september-2017"></a>2017 年 9 月

### <a name="hotfix-for-microsoft-identity-manager"></a>Microsoft Identity Manager の修正プログラム


**タイプ:** 変更された機能  
**サービス カテゴリ:** Microsoft Identity Manager  
**製品の機能:** ID ライフサイクル管理  



2017 年 9 月 25 日より、Microsoft Identity Manager (MIM) 2016 2016 Service Pack 1 (SP1) の修正プログラム ロールアップ パッケージ (ビルド 4.4.1642.0) の提供が開始されました。 このロールアップ パッケージの説明は以下のとおりです。

- 問題を解決し、機能強化を追加します。
- Microsoft Identity Manager 2016 のビルド 4.4.1459.0 までのすべての MIM 2016 SP1 更新プログラムに代わる累積的な更新プログラムです。 
- **Microsoft Identity Manager 2016 ビルド 4.4.1302.0** がインストールされている必要があります。 

詳細については、「[Hotfix rollup package (build 4.4.1642.0) is available for Microsoft Identity Manager 2016 SP1](https://support.microsoft.com/help/4021562)」(Microsoft Identity Manager 2016 SP1 の修正プログラム ロールアップ パッケージ (ビルド 4.4.1642.0) の提供開始) をご覧ください。 

---
