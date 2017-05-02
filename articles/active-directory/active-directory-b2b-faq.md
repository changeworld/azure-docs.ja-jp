---
title: "Azure Active Directory B2B コラボレーションの FAQ | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーションに関してよく寄せられる質問"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/13/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: c06e8b98fea6036704b07b0f12397fc4bb59cb3f
ms.lasthandoff: 04/15/2017


---

# <a name="azure-active-directory-b2b-collaboration-frequently-asked-questions-faq"></a>Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)

よく寄せられる質問は、新しい関心を反映するために定期的に更新されます。

### <a name="is-this-functionality-available-in-the-azure-classic-portal"></a>この機能は Azure クラシック ポータルで使用できますか。
この Azure AD B2B コラボレーションにおける新機能は、[Azure Portal](https://portal.azure.com) と新しいアクセス パネルを通じてのみ使用できます。 

### <a name="is-it-possible-to-customize-our-sign-in-page-so-its-more-intuitive-for-your-b2b-collaboration-guest-users"></a>サインイン ページをカスタマイズして B2B コラボレーションの guest ユーザーにとって直感的にすることはできますか?
もちろんあります。 [この機能を説明しているブログ投稿](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/)があり、詳細は「[会社ブランドをサインインおよびアクセス パネル ページに追加する](active-directory-add-company-branding.md)」で説明しています。

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>B2B コラボレーションのユーザーは、SharePoint Online と OneDrive にアクセスできますか。
はい。 ただし、SharePoint Online のユーザー選択ウィンドウでの既存のゲスト ユーザーの検索は、従来の動作と一致させるために、既定ではオフになっています。 これは、テナントとサイト コレクション レベルで 'ShowPeoplePickerSuggestionsForGuestUsers' 設定を使用することで有効にできます。 この設定は、Set-SPOTenant コマンドレットと Set-SPOSite コマンドレットを使用して設定できます。これにより、メンバーは、ディレクトリ内のすべての既存のゲスト ユーザーを検索することができます。 テナントのスコープの変更は、既にプロビジョニングされている SharePoint Online サイトには影響しません。

### <a name="is-the-csv-upload-mechanism-still-supported"></a>CSV のアップロード メカニズムは、まだサポートされていますか。
はい。 付属の [PowerShell のサンプル](active-directory-b2b-code-samples.md)を参照してください。

### <a name="how-can-i-customize-my-invitation-emails"></a>招待の電子メールは、どのようにカスタマイズできますか。
[B2B 招待 API](active-directory-b2b-api.md) を使用して、招待元プロセスのほぼすべてをカスタマイズすることができます。

### <a name="can-the-invited-external-user-leave-the-organization-to-which-he-was-invited"></a>招待された外部ユーザーは、招待元の組織から離脱することができますか。
これは、現在は使用できません。

### <a name="now-that-multi-factor-authentication-mfa-is-available-for-guest-users-can-they-also-reset-their-mfa-method"></a>ゲスト ユーザーに対して多要素認証 (MFA) を利用できるようになりましたが、MFA 方法をリセットすることもできますか。
はい、通常のユーザーと同じようにすることができます。

### <a name="which-organization-is-responsible-for-mfa-licenses"></a>MFA ライセンスは、どちらの組織が責任を負いますか。
招待する側の組織が、MFA を担当し、実行します。 そのため、招待する側の組織に、MFA を行う B2B ユーザーのための十分なライセンスを確保する責任があります。

### <a name="what-if-my-partner-org-already-has-mfa-set-up-can-we-trust-their-mfa-and-not-use-our-mfa"></a>パートナー組織が既に MFA をセットアップしている場合はどうすればよいですか。 相手の MFA を信頼し、自社の MFA は使用しなくてもよいですか。
これは、今後のリリースでサポートする予定です。 それがリリースされたら、自社 (招待する側の組織) の MFA から除外する特定のパートナーを選択できるようになります。

### <a name="how-can-i-achieve-delayed-invitations"></a>招待の遅延は、どのようにして行うことができますか。
一部の組織は、B2B コラボレーション ユーザーを追加し、プロビジョニングを必要とするアプリケーションにプロビジョニングしてから、招待を送信しようとします。 そのような場合は、B2B コラボレーションの招待 API を使用して、オンボード ワークフローをカスタマイズすることができます。

### <a name="can-i-make-my-guest-users-limited-admins"></a>ゲスト ユーザーを制限付き管理者にすることができますか。
そして、 組織でそうする必要がある場合、その方法については、[ロールへのゲスト ユーザーの追加](active-directory-users-assign-role-azure-portal.md)に関するページを参照してください。

### <a name="does-azure-ad-b2b-collaboration-support-permitting-b2b-users-to-access-the-azure-portal"></a>Azure AD B2B コラボレーションでは、B2B ユーザーによる Azure Portal へのアクセスの許可がサポートされていますか。
B2B コラボレーション ユーザーは、制限付き管理者ロールまたは全体管理者ロールに割り当てられない限り、Azure Portal にアクセスする必要がありません。 割り当てられた場合は、ポータルにアクセスすることができます。 これらのロールではないゲスト ユーザーがポータルにアクセスした場合、エクスペリエンスの特定の部分にアクセスできることがあります。これは、前のセクションで説明したように、ゲスト ユーザー ロールにディレクトリの特定のアクセス許可があるためです。

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>ゲスト ユーザーに対して Azure Portal へのアクセスをブロックできますか。
はい。 ただし、このポリシーを構成する際に、誤ってメンバーと管理者のアクセスをブロックしないように注意してください。
ゲスト ユーザーによる [Azure Portal](https://portal.azure.com) へのアクセスは、Windows Azure Service Management API の条件付きアクセス ポリシーを通じて、次の 3 つの手順でブロックできます。
1. **[All Users]**(すべてのユーザー) グループを、「![グループ スクリーンショットを変更](media/active-directory-b2b-faq/modify-all-users-group.png)」メンバーのみが含まれるように変更します
2. ゲスト ユーザー「![グループ スクリーンショットを作成](media/active-directory-b2b-faq/group-with-guest-users.png)」を含む動的グループを作成します
3. 次のビデオで示されているように、ゲスト ユーザーによるポータルへのアクセスをブロックする条件付きアクセス ポリシーをセットアップします。
  
  >[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="what-is-the-timeline-by-which-azure-ad-b2b-collaboration-will-start-support-for-mfa-and-consumer-email-accounts"></a>Azure AD B2B コラボレーションが MFA とコンシューマー電子メール アカウントのサポートを開始する予定はどうなっていますか。
MFA とコンシューマー電子メール アカウントの両方がサポートされるようになりました。

### <a name="is-there-a-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Azure AD B2B コラボレーション ユーザーのパスワード リセットをサポートする計画はありますか。
はい。 自分のID テナントからリソース テナントに招待されている B2B ユーザーのセルフ サービス パスワード リセット (SSPR) に関する詳細を以下に示します。
 
* SSPR が B2B ユーザーの ID テナントのみで行われる
* ID テナントが Microsoft アカウントである場合 – Microsoft アカウント SSPR メカニズムを使用する
* ID テナントがジャスト イン タイム/バイラル テナントの場合、パスワード リセット電子メールが送信される
* それ以外の場合、標準 SSPR プロセスが B2B ユーザーで行われ、同様に、リソース テナントのコンテキストでの B2B ユーザー用 SSPR メンバーはブロックされます。

### <a name="is-it-also-enabled-for-users-in-a-viral-tenant"></a>バイラル テナントのユーザーに対しても有効ですか。
現時点では連携しません。

### <a name="does-microsoft-crm-provide-online-support-to-azure-ad-b2b-collaboration"></a>Microsoft CRM は、Azure AD B2B コラボレーションをオンラインでサポートしていますか。
このサポート作業は進行中です。

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>新しく作成した B2B コラボレーション ユーザー用の初期パスワードの有効期間はどうなっていますか。
Azure AD には、文字、パスワードの強度、およびアカウント ロックアウトに関する要件の固定セットがあり、これはすべての Azure AD クラウド ユーザー アカウントにも同様に適用されます。 クラウド ユーザー アカウントは、他の ID プロバイダー (Microsoft アカウント、Facebook、ADFS など) や他のクラウド テナント (B2B コラボレーションの場合) とフェデレーションされないアカウントです。 フェデレーション アカウントの場合、パスワード ポリシーは、オンプレミス テナントのポリシーとユーザーの Microsoft アカウント設定に依存します。

### <a name="applications-want-to-differentiate-their-experience-between-a-tenant-user-and-a-guest-user-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-right-model-for-this"></a>アプリケーションのエクスペリエンスをテナント ユーザーとゲスト ユーザーで区別したいと思っています。 これを行うための標準的なガイダンスはありますか。 このモデルに適した ID プロバイダー要求は存在しますか。
 
「[Azure Active Directory B2B コラボレーション ユーザーのプロパティ](active-directory-b2b-user-properties.md)」で説明しているように、ゲスト ユーザーは、認証用に任意の ID プロバイダーを使用することができます。 このため、判断を行うための適切なプロパティは UserType です。 現在、UserType 要求はトークンには含まれていません。 アプリケーションは、Graph API を使用してディレクトリでユーザーのクエリを実行することで、ユーザーの UserType を取得する必要があります。

### <a name="where-can-find-a-b2b-collaboration-community-to-share-solutions-and-submit-ideas"></a>ソリューションを共有し、アイデアを提供するための B2B コラボレーション コミュニティにはどこからアクセスできますか。

B2B コラボレーションを向上させる方法に関するフィードバックは絶えず受け付けています。 マイクロソフトはお客様に、ディスカッションや、ユーザー シナリオ、ベスト プラクティス、また Azure AD B2B コラボレーションに関するご意見の共有への参加を [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b) で招待しています。
 
また、[B2B Collaboration Ideas](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas) サイトでも、ご意見の送信や、今後の機能への投票を行うことができます。

### <a name="is-there-a-way-to-invite-the-user-such-that-the-invitation-is-automatically-redeemed-and-the-user-is-just-ready-to-go-or-will-the-user-always-have-to-click-through-to-the-redemption-url"></a>招待が自動的に引き換えされて、ユーザーが「準備 OK」になるようにユーザー招待する方法はありますか。それともユーザーは常に引き換え URL をクリックしなければなりませんか?
招待状を送信した招待側組織のユーザーが被招待組織 (B2B ユーザーの組織) のメンバーでもある場合、B2B ユーザーによる引き換えは必要ありません。

これを実現するためには、被招待組織から 1 人のユーザーを招待側組織に招待することをお勧めします。 [このユーザーを、リソース組織のゲスト招待者ロールに追加](active-directory-b2b-add-guest-to-role.md)します。 このユーザーは被招待組織の他のユーザーを、サインイン UI、PowerShell スクリプト、または API によって招待できます。その組織の B2B ユーザーが招待を引き換える必要はありません。

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>招待されたパートナーがフェデレーションを使用して独自のオンプレミス認証を追加するとき、B2B コラボレーションはどのように機能しますか?
パートナーの Azure AD テナントがオンプレミス認証インフラストラクチャにフェデレーションされている場合、オンプレミスのシングル サインオン (SSO) が自動的に実行されます。 パートナーに Azure AD テナントがない場合、新たなユーザーに Azure AD アカウントが作成されます。 

### <a name="i-didnt-think-azure-ad-b2b-would-accept-gmailcom-and-outlookcom-email-addresses-b2c-was-what-you-used-for-those"></a>Azure AD B2B が gmail.com や outlook.com の電子メール アドレスを受け付けるとは思いませんでした。 B2C は、これらのために使用されました。
どちらの ID がサポートされているかに関して B2B と B2C の相違点を削除しています。 使用される ID は、B2B と B2C のどちらを使うかを決めるポイントではありません。 どちらを使用するのかを決めるには、「[B2B コラボレーションと Azure Active directory の B2C を比較する](active-directory-b2b-compare-b2c.md)」の記事を参照してください。

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Azure B2B ゲスト ユーザーをサポートするのはどのアプリケーションとサービスですか。
すべての Azure AD 統合型アプリケーションです。 

### <a name="is-it-possible-to-force-mfa-for-b2b-guest-users-if-partners-have-no-mfa-enabled"></a>パートナーに有効な MFA がない場合、B2B ゲスト ユーザーに MFA を強制することはできますか?
はい。 詳細は、「[B2B コラボレーション ユーザーの条件付きアクセス](active-directory-b2b-mfa-instructions.md)」をご覧ください。

### <a name="within-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-any-plans-to-extend-this-to-azure-or-across-all-of-office-365"></a>SharePoint 内では、外部ユーザーに対して「許可」または「拒否」リストを定義できます。 この機能を Azure または Office 365 全体に拡張する予定はありますか?
はい。 Azure AD B2B コラボレーションでは、allowlist/denylist 機能をサポートしています。 

### <a name="what-licenses-are-needed-for-azure-ad-b2b"></a>Azure AD B2B で必要なのは何のライセンスですか?
「[Azure Active Directory B2B コラボレーションのライセンスに関するガイダンス](active-directory-b2b-licensing.md)」をご覧ください。

### <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-admin-add-users.md)
* [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-iw-add-users.md)
* [B2B コラボレーションの招待メールの要素](active-directory-b2b-invitation-email.md)
* [B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B コラボレーションのライセンス](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B コラボレーションのトラブルシューティング](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B コラボレーションの API とカスタマイズ](active-directory-b2b-api.md)
* [B2B コラボレーション ユーザーの多要素認証](active-directory-b2b-mfa-instructions.md)
* [招待を使用せずに B2B コラボレーション ユーザーを追加する](active-directory-b2b-add-user-without-invite.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)

