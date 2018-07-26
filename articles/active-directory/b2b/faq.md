---
title: Azure Active Directory B2B コラボレーションの FAQ | Microsoft Docs
description: Azure Active Directory B2B コラボレーションに関してよく寄せられる質問への回答を示します。
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 1ff2332e2867f99492d3da254282498d1627a5bf
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/14/2018
ms.locfileid: "39058264"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Azure Active Directory B2B コラボレーションの FAQ

Azure Active Directory (Azure AD) 企業間 (B2B) コラボレーションに関するよく寄せられる質問 (FAQ) は、新しいトピックを追加して定期的に更新されています。

### <a name="can-we-customize-our-sign-in-page-so-it-is-more-intuitive-for-our-b2b-collaboration-guest-users"></a>B2B コラボレーションのゲスト ユーザー向けに、より直感的にするため、サインイン ページをカスタマイズすることはできますか。
もちろんあります。 [この機能に関するブログ記事](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/)をご覧ください。 組織のサインイン ページをカスタマイズする方法の詳細については、「[サインイン ページとアクセス パネル ページに会社のブランドを追加する](../fundamentals/customize-branding.md)」をご覧ください。

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>B2B コラボレーションのユーザーは、SharePoint Online と OneDrive にアクセスできますか。
はい。 ただし、SharePoint Online で、ユーザー選択ウィンドウを使用して既存のゲスト ユーザーを検索する機能は、既定で**オフ**になっています。 既存のゲスト ユーザーを検索するオプションを有効にするには、**ShowPeoplePickerSuggestionsForGuestUsers** を **On** に設定します。 この設定は、テナント レベルで、またはサイト コレクション レベルで有効にできます。 この設定を変更するには、Set-SPOTenant および Set-SPOSite コマンドレットを使用します。 これらのコマンドレットにより、メンバーは、ディレクトリ内のすべての既存のゲスト ユーザーを検索できます。 テナントのスコープの変更は、既にプロビジョニングされている SharePoint Online サイトには影響しません。

### <a name="is-the-csv-upload-feature-still-supported"></a>CSV のアップロード機能は、まだサポートされていますか。
はい。 .csv ファイルのアップロード機能の使用の詳細については、[この PowerShell サンプル](code-samples.md)をご覧ください。

### <a name="how-can-i-customize-my-invitation-emails"></a>招待の電子メールは、どのようにカスタマイズできますか。
[B2B 招待 API](customize-invitation-api.md) を使用して、招待元プロセスのほぼすべてをカスタマイズすることができます。

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>ゲスト ユーザーは、Multi-Factor Authentication 方法をリセットできますか。
はい。 ゲスト ユーザーは、通常のユーザーと同じように、Multi-Factor Authentication 方法をリセットできます。

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>どの組織が Multi-Factor Authentication ライセンスを担当しますか。
招待側組織が Multi-Factor Authentication を実行します。 招待側組織は、Multi-Factor Authentication を使用している B2B ユーザーに対する十分なライセンスがあることを確認する必要があります。

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>パートナー組織で、既に Multi-Factor Authentication を設定している場合はどうなりますか。 パートナー組織の Multi-Factor Authentication を信頼でき、独自の Multi-Factor Authentication は使用しないのですか。
この機能は今後のリリースで計画されています。機能が実装されたら、特定のパートナーを選択して、自社 (招待側組織) の多要素認証から除外できるようになります。

### <a name="how-can-i-use-delayed-invitations"></a>招待の遅延は、どのように使用するのですか。
組織で、B2B コラボレーション ユーザーを追加し、必要に応じてそれらのユーザーをアプリケーションにプロビジョニングして、招待を送信したいと考える場合があります。 B2B コラボレーションの招待 API を使用して、オンボード ワークフローをカスタマイズできます。

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Exchange のグローバル アドレス一覧にゲスト ユーザーを表示できますか。
はい。 既定では、ゲスト オブジェクトは組織のグローバル アドレス一覧には表示されませんが、Azure Active Directory PowerShell を使用してそれらを表示できます。 詳しくは、「[Office 365 グループのゲスト アクセス](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6#PickTab=FAQ)」の「**グローバル アドレス一覧にゲスト オブジェクトを表示することはできますか?**」をご覧ください。

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>ゲスト ユーザーを制限付き管理者にできますか。
そして、 詳細については、[ゲスト ユーザーのロールへの追加](add-guest-to-role.md)に関するページをご覧ください。

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Azure AD B2B コラボレーションでは、B2B ユーザーの Azure ポータルへのアクセスを許可していますか。
B2B コラボレーション ユーザーは、制限付き管理者または全体管理者のロールが割り当てられない限り、Azure ポータルにアクセスする必要はありません。 ただし、制限付き管理者または全体管理者のロールを割り当てられている B2B コラボレーション ユーザーはポータルにアクセスできます。 また、これらのいずれかの管理者ロールが割り当てられていないゲスト ユーザーがポータルにアクセスすると、ユーザーは、エクスペリエンスの特定の部分にアクセスできます。 ゲスト ユーザー ロールは、ディレクトリのアクセス許可の一部を持ちます。

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>ゲスト ユーザーに対して Azure Portal へのアクセスをブロックできますか。
はい。 このポリシーを構成する場合は、誤ってメンバーと管理者のアクセスをブロックしないように注意してください。
ゲスト ユーザーの [Azure ポータル](https://portal.azure.com)へのアクセスをブロックするには、Windows Azure クラシック デプロイ モデル API で条件付きアクセス ポリシーを使用します。
1. **すべてのユーザー** グループをメンバーだけが含まれるように変更します。
  ![グループの変更スクリーンショット](media/faq/modify-all-users-group.png)
2. ゲスト ユーザーを含む動的グループを作成します。
  ![グループの作成スクリーンショット](media/faq/group-with-guest-users.png)
3. 次のビデオで示されているように、ゲスト ユーザーによるポータルへのアクセスをブロックする条件付きアクセス ポリシーをセットアップします。
  
  > [!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Azure AD B2B コラボレーションは、Multi-Factor Authentication とコンシューマー電子メール アカウントをサポートしていますか。
はい。 Azure AD B2B コラボレーションでは、Multi-Factor Authentication とコンシューマー電子メール アカウントのどちらもサポートされています。

### <a name="do-you-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Azure AD B2B コラボレーション ユーザーのパスワード リセットをサポートする計画はありますか。
はい。 パートナー組織から招待されている B2B ユーザーのセルフ サービス パスワード リセット (SSPR) の重要な詳細情報を次に示します。
 
* SSPR は B2B ユーザーの ID テナントでのみ行われます。
* ID テナントが Microsoft アカウントの場合は、Microsoft アカウント SSPR メカニズムが使用されます。
* ID テナントが Just-In-Time (JIT) または "バイラル" テナントの場合、パスワード リセット電子メールが送信されます。
* その他のテナントの場合、B2B ユーザーは標準の SSPR 手順に従います。 B2B ユーザーのメンバー SSPR のように、リソースのコンテキストで、テナントがブロックされます。 

### <a name="is-password-reset-available-for-guest-users-in-a-just-in-time-jit-or-viral-tenant-who-accepted-invitations-with-a-work-or-school-email-address-but-who-didnt-have-a-pre-existing-azure-ad-account"></a>Just-In-Time (JIT) または "バイラル" テナントのゲスト ユーザーで、職場または学校の電子メール アドレスで招待を承諾たが、既存の Azure AD アカウントを持っていないユーザーは、パスワード リセットを利用できますか。
はい。 ユーザーが JIT テナントで自分のパスワードをリセットできるパスワード リセット メールを送信できます。

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Microsoft Dynamics 365 は、Azure AD B2B コラボレーションをオンラインでサポートしていますか。
はい、Dynamics 365 (オンライン) は、Azure AD B2B コラボレーションをサポートしています。 詳細については、Dynamics 365 に関する記事「[Azure Active Directory B2B コラボレーションでユーザーを招待する](https://docs.microsoft.com/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration)」を参照してください。

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>新しく作成した B2B コラボレーション ユーザー用の初期パスワードの有効期間はどうなっていますか。
Azure AD には、文字、パスワードの強度、およびアカウント ロックアウトに関する要件の固定セットがあり、これはすべての Azure AD クラウド ユーザー アカウントにも同様に適用されます。 クラウド ユーザー アカウントは、次のような別の ID プロバイダーとフェデレーションされないアカウントです。 
* Microsoft アカウント
* Facebook
* Active Directory フェデレーション サービス (AD FS)
* (B2B コラボレーション用の) 別のクラウド テナント

フェデレーション アカウントの場合、パスワード ポリシーは、オンプレミス テナントに適用されるポリシーとユーザーの Microsoft アカウント設定に依存します。

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>組織で、アプリケーションにテナント ユーザーとゲスト ユーザーに対して異なるエクスペリエンスを設定したいと考える場合があります。 これを行うための標準的なガイダンスはありますか。 ID プロバイダー要求の存在は、使用する正しいモデルですか。
 ゲスト ユーザーは、任意の ID プロバイダーを使用して認証できます。 詳細については、[B2B コラボレーション ユーザーのプロパティ](user-properties.md)に関するページをご覧ください。 **UserType** プロパティを使用して、ユーザー エクスペリエンスを決定します。 **UserType 要求**は、現在トークンに含まれていません。 アプリケーションでは、Graph API を使用してディレクトリでユーザーのクエリを実行し、UserType を取得する必要があります。

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>ソリューションを共有し、アイデアを提供するための B2B コラボレーション コミュニティにはどこからアクセスできますか。
B2B コラボレーションを向上させるためのフィードバックは絶えず受け付けています。 マイクロソフトでは、ユーザー シナリオ、ベスト プラクティス、また Azure AD B2B コラボレーションに関するご意見の共有を求めています。 [マイクロソフト Tech コミュニティ](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)のディスカッションにご参加ください。
 
また、[B2B Collaboration Ideas](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas) でも、ご意見の送信や、今後の機能への投票を行うことができます。

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>ユーザーがすぐに "準備" できるように、自動的に引き換えられる招待を送信できますか。 それとも、ユーザーは常に引き換えの URL をクリックする必要があるのですか。
招待元は、UI、PowerShell スクリプト、または API を使用して、取引先組織の他のユーザーを招待できます。 次に、招待元はゲスト ユーザーに共有アプリへの直接リンクを送信できます。 ほとんどの場合、メール招待状を開いて、引き換えの URL をクリックする必要はなくなります。 詳細については、「[Azure Active Directory B2B コラボレーションの招待の利用](redemption-experience.md)」を参照してください。

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>招待されたパートナーがフェデレーションを使用して独自のオンプレミス認証を追加するとき、B2B コラボレーションはどのように機能しますか?
パートナーの Azure AD テナントがオンプレミス認証インフラストラクチャにフェデレーションされている場合、オンプレミスのシングル サインオン (SSO) が自動的に実行されます。 パートナーに Azure AD テナントがない場合、新しいユーザーに対し Azure AD アカウントが作成されます。 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Azure AD B2B は gmail.com および outlook.com の電子メール アドレスを受け付けず、このようなアカウントに対しては、B2C が使われていたと思います。
マイクロソフトでは、サポートされる ID に関して、B2B と business-to-consumer (B2C) のコラボレーションの違いをなくしています。 使用される ID は、B2B と B2C のどちらを使うかを選択する十分な理由になりません。 コラボレーション オプションの選択については、「[Azure Active Directory での B2B コラボレーションと B2C の比較](compare-with-b2c.md)」をご覧ください。

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Azure B2B ゲスト ユーザーをサポートするのはどのアプリケーションとサービスですか。
すべての Azure AD 統合アプリケーションで、Azure B2B ゲスト ユーザーをサポートします。 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>パートナーが Multi-Factor Authentication を使用していない場合に、B2B ゲスト ユーザーに Multi-Factor Authentication を強制できますか。
はい。 詳細は、「[B2B コラボレーション ユーザーの条件付きアクセス](conditional-access.md)」をご覧ください。

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>SharePoint では、外部ユーザーに対して "許可" または "拒否" リストを定義できます。 これを Azure で実行できますか。
はい。 Azure AD B2B コラボレーションでは、許可リストと拒否リストをサポートしています。 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Azure AD B2B を使用する必要があるライセンスは何ですか。
組織で Azure AD B2B を使用する必要があるライセンスについては、「[Azure Active Directory B2B コラボレーションのライセンスに関するガイダンス](licensing-guidance.md)」をご覧ください。

### <a name="next-steps"></a>次の手順

- [Azure AD B2B コラボレーションとは](what-is-b2b.md)

