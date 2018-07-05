---
title: Azure Active Directory で ID インフラストラクチャをセキュリティ保護する 5 つのステップ
description: このドキュメントでは、管理者が Azure AD 機能を使って組織をセキュリティで保護するうえで実行すべき一連の重要なアクションについて概説します
services: active-directory
author: martincoetzer
manager: manmeetb
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 06/18/2018
ms.author: martincoetzer
ms.openlocfilehash: b15fff6e868bfac973f9d2a7277f0fac1e29d845
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36940024"
---
# <a name="five-steps-to-securing-your-identity-infrastructure"></a>ID インフラストラクチャをセキュリティ保護する 5 つのステップ

このドキュメントをご覧になっているお客様は、セキュリティの重要性を認識なさっていると思います。 おそらく、既に組織をセキュリティで保護する責任を負っていらっしゃるでしょう。 セキュリティの重要性を他の人に理解してもらう必要がある場合は、最新の [Microsoft セキュリティ インテリジェンス レポート](https://www.microsoft.com/security/intelligence-report)を読むようご案内ください。

このドキュメントは、Azure Active Directory の機能を使用してセキュリティ体制を強化するのに役立ちます。そのために、サイバー攻撃への対策を組織に取り入れる 5 ステップのチェックリストが使用されます。

このチェックリストは、重要な推奨アクションを迅速に展開して、組織を直ちに保護するうえで役に立ちます。ここでは、以下の方法について説明されています。

* 資格情報を強化する。
* 攻撃の対象となる領域を減らす。
* 脅威への対応を自動化する。
* 監査と監視の認識を高める。
* セルフヘルプによる、より予測可能で完全なエンドユーザー セキュリティを有効にする。

> [!NOTE]
> このドキュメントの推奨事項の多くは、Azure Active Directory を ID プロバイダーとして使用するよう構成されたアプリケーションにのみ適用されます。 シングル サインオンを使用するようアプリを構成すると、資格情報ポリシーや脅威の検出、監査、ログの記録などの機能がこれらのアプリケーションに追加されるというベネフィットがあります。 [Azure Active Directory を通じたシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-sso)は、これらのすべての推奨事項の基礎となります。

## <a name="before-you-begin-protect-privileged-accounts-with-mfa"></a>開始する前に: MFA で特権アカウントを保護する

このチェックリストを読み始める前に、読んでいる間にセキュリティが侵害されないようにしてください。 まず、特権アカウントを保護する必要があります。

攻撃者に特権アカウントを支配されると、多大な損害が発生する可能性があるので、まず特権アカウントを保護することが重要です。 [ベースラインの保護](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-baseline-protection)を使用して、すべての管理者に関して [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) (MFA) を有効にして要求します。 MFA を実装していない場合はすぐに実装してください。 これは非常に重要です。

準備はできたでしょうか。 それでは、チェックリストの確認に入りましょう。

## <a name="step-1---strengthen-your-credentials"></a>ステップ 1. - 資格情報を強化する 

ほとんどのエンタープライズのセキュリティ侵害では、パスワード スプレーや侵害リプレイ、フィッシングなど、いくつかある手法のいずれかによるアカウント侵害が原因です。 次の動画で、これらの攻撃についてご確認ください。
> [!VIDEO https://channel9.msdn.com/events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3016/player]

ID システム内のユーザーが脆弱なパスワードを使用しており、多要素認証による強化が行われていない場合、問題はいつセキュリティ侵害されるかではなく、"どのくらいの頻度でセキュリティ侵害されるか" になります。

### <a name="make-sure-your-organization-use-strong-authentication"></a>組織で強力な認証が使用されるようにする

パスワードの推測、フィッシング、マルウェアによる盗難、または再利用が頻繁に行われることを考えると、何らかの強力な資格情報でパスワードを強化することが非常に重要です。[Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) の詳細をご確認ください。

### <a name="turn-off-traditional-complexity-expiration-rules-and-start-banning-commonly-attacked-passwords-instead"></a>複雑さと有効期限に関する伝統的なルールを廃止して、攻撃されやすいパスワードを禁止する

多くの組織は、複雑さ (特殊文字など) とパスワードの有効期限に関する伝統的なルールを使用しています。 Microsoft の調査によると、これらのポリシーは有害であり、推測されやすいパスワードをユーザーが選択する原因となります。

Microsoft は、[NIST ガイダンス](https://pages.nist.gov/800-63-3/sp800-63b.html)に従って、次の 3 つを実施することをお勧めします。

1. パスワードが 8 文字以上であることを要求する。 ユーザーが予測可能なパスワードを選んだり、パスワードをファイルに保存したり、書き留めたりする原因となるため、必ずしも長ければ長いほど良いというわけではありません。
2. 有効期限のルールを無効にする。このルールは、簡単に推測されるパスワード (**Summer2018!** など) をユーザーが使用する原因になります。
3. 文字構成の要件を無効にして、よく攻撃されるパスワードをユーザーが選択するのを防ぐ。これは、文字構成の要件が、パスワードで予測可能な文字置換をユーザーが選択する原因となるためです。

Azure AD で直接 ID を作成した場合、[PowerShell を使用してユーザーのパスワードの有効期限が切れるのを防ぐ](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)ことができます。 オンプレミスの AD と Azure AD Connect を使用して ID を Azure AD と同期している場合 (つまり、ハイブリッド デプロイの場合)、組織は[ドメイン グループ ポリシー設定](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh994572(v%3dws.10))または [Windows PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/set-addefaultdomainpasswordpolicy) を使用して、オンプレミスの[インテリジェントなパスワード ポリシー](https://aka.ms/passwordguidance)を実装する必要があります。

Azure Active Directory の[パスワードの動的禁止](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)機能では、簡単に推測できるパスワードをユーザーが設定するの防ぐために、現在の攻撃者の行動が利用されます。 この機能は常に有効であり、ハイブリッド デプロイの組織は、[パスワード ライトバック](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-writeback)を有効にすることで、この機能を利用できます。または、[Azure AD パスワード保護を Windows Server Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises) に展開できます。 Azure AD パスワード保護を使用すると、ユーザーは、構成できる一般的なパスワードとカスタム パスワードに含まれている、ありふれたパスワードを選択できなくなります。

### <a name="protect-against-leaked-credentials-and-add-resilience-against-outages"></a>資格情報の漏洩から保護し、障害に対する回復力を高める

組織がハイブリッド ID ソリューションを使用する場合、次の 2 つの理由から、パスワード ハッシュ同期を有効にする必要があります。

* Azure AD 管理の[資格情報が漏洩したユーザー](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events) レポートでは、"闇サイト" で公開されているユーザー名とパスワードのペアについて、警告を受け取れます。 驚くほど大量のパスワードが、後にセキュリティ侵害されるサードパーティ サイトでのパスワードの再利用、フィッシング、マルウェアによって漏洩しています。 Microsoft は、これらの漏洩した資格情報の多くを発見し、それらがお客様の組織の資格情報に一致する場合に、このレポートでお客様に報告します。しかしそのためには、[パスワード ハッシュ同期を有効にしておく](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)必要があります。
* (たとえばランサムウェア攻撃で) オンプレミスの障害が発生した場合、[パスワード ハッシュ同期を使用してクラウド認証](https://docs.microsoft.com/azure/security/azure-ad-choose-authn)に切り替えることができます。 このバックアップ認証方法では、Azure Active Directory による認証が構成されたアプリ (Office 365 など) へのアクセスを継続できます。

[パスワード ハッシュ同期](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)のしくみについて、詳しくご確認ください。

### <a name="implement-ad-fs-extranet-lockout"></a>AD FS エクストラネットのロックアウトを実装する

Azure AD で直接認証されるようアプリケーションを構成している組織は、[Azure AD スマート ロックアウト](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords)を利用できます。 AD FS を使用している場合は、AD FS [エクストラネットのロックアウト](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-lockout-protection)を実装します。 エクストラネットのロックアウトでは、ユーザーが Active Directory からロックアウトされることなく、AD FS に対するブルート フォース攻撃を防げます。

### <a name="take-advantage-of-intrinsically-secure-easier-to-use-credentials"></a>本質的に安全な、より使いやすい資格情報を活用する

[Windows Hello](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification) を使用して、PC およびモバイル デバイスでパスワードを強力な 2 要素認証に置き換えることができます。 この認証はデバイスに関連付けられた新しい種類のユーザー資格情報で構成され、生体認証または PIN が使用されます。

## <a name="step-2---reduce-your-attack-surface"></a>ステップ 2. - 攻撃の対象となる領域を減らす

漏洩したパスワードが広まりやすいことを考えると、組織において攻撃の対象となる領域を最小化することが重要です。 安全性が低い旧式のプロトコルの使用をやめ、アクセス エントリ ポイントを制限して、リソースへの管理アクセス権の制御を強化すると、攻撃の対象となる領域を減らせます。

### <a name="block-legacy-authentication"></a>レガシ認証をブロックする

Azure AD による認証と会社データへのアクセスに、独自の古い方法がアプリで使用されている場合、組織には別のリスクが生じます。 レガシ認証が使用されているアプリは、POP3 クライアント、IMAP4 クライアント、SMTP クライアントなどです。 レガシ認証アプリでは、ユーザーの代わりに認証が行われ、Azure AD による高度なセキュリティ評価の実行が妨げられます。 代わりとなる最新の認証では、多要素認証と条件付きアクセスがサポートされているので、セキュリティ リスクを抑えられます。 次の 3 つのアクションをお勧めします。

1. [AD FS を使用している場合はレガシ認証](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)をブロックする。
2. [最新の認証を使用するよう SharePoint Online と Exchange Online](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-no-modern-authentication) を設定する。
3. [レガシ認証をブロックするために条件付きアクセス ポリシー](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-conditions#legacy-authentication)を使用する。

### <a name="block-invalid-authentication-entry-points"></a>無効な認証エントリ ポイントをブロックする

侵害想定の考え方を取り入れて、ユーザー資格情報の漏洩が発生した場合にその影響を抑える必要があります。 環境内のアプリごとに有効なユース ケースを検討して、どのグループ、ネットワーク、デバイスなどの要素が承認されるかを判断し、残りをブロックします。 [高度な特権アカウントまたはサービス アカウント](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices)の使用を慎重に制限します。 [Azure AD の条件付きアクセス](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)では、定義した特定の条件に基づいて、承認されたユーザーがどのようにアプリとリソースにアクセスするかを制御できます。

サービス アカウント (自動でタスクを実行するために使用されるアカウント) には特別な注意を払います。 条件付きアクセスを使用すると、そのようなアカウントが、1 日の適切な時間に、適切な IP から、適切なサービスに対してのみ実行されるようにできます。

### <a name="implement-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management を実装する

さらに "侵害想定" に基づくと、セキュリティ侵害されたアカウントが特権ロールで操作され得る確率を最小限に抑える必要があります。 

[Azure AD Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) は、以下をサポートしており、アカウントの特権を最小限に抑えるのに役立ちます。

* 管理者ロールに割り当てられたユーザーを特定して管理する。
* 削除すべき未使用の特権ロールまたは余計な特権ロールを把握する。
* 多要素認証によって特権ロールが保護されるようルールを確立する。
* 特権タスクを完了するのに十分な期間のみ特権ロールが付与されるようルールを確立する。

Azure AD PIM を有効にして管理者ロールが割り当てられたユーザーを表示し、それらのロールの不必要なアカウントを削除します。 残りの特権ユーザーを永続から候補に変更します。 最後に、これらの特権ロールにアクセスできる必要がある場合に安全にアクセスできるよう、適切なポリシーを確立します。

特権アカウントのプロセスを展開する一環として、自分自身をロックアウトしてしまった場合に Azure AD にアクセスできるよう、[少なくとも 2 つの緊急アカウントを作成するベスト プラクティス](https://docs.microsoft.com/azure/active-directory/admin-roles-best-practices)に従ってください。

## <a name="step-3---automate-threat-response"></a>ステップ 3. - 脅威への対応を自動化する

Azure Active Directory には、検出と対応に時間差が生じないよう、攻撃を自動的に遮断する多数の機能が備わっています。 犯罪者が環境に侵入するために使える時間を減らせれば、コストとリスクを抑えることができます。 実行できる具体的な手順は以下のとおりです。

### <a name="implement-user-risk-security-policy-using-azure-ad-identity-protection"></a>Azure AD Identity Protection を使用してユーザーのリスク セキュリティ ポリシーを実装する

ユーザー リスクは、ユーザーの ID がセキュリティ侵害された確率を示すもので、ユーザーの ID に関連付けられている[ユーザー リスク イベント](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)に基づいて計算されます。 ユーザー リスク ポリシーは、特定のユーザーまたはグループに関してリスク レベルを評価する条件付きアクセスポリシーです。 低、中、高のリスクレベルに基づいて、アクセスをブロックしたり、多要素認証を使用して安全なパスワードへの変更を要求したりするよう、ポリシーを構成できます。 Microsoft は、リスクの高いユーザーについて、安全なパスワードへの変更を要求することをお勧めします。

![リスクのフラグ付きユーザー](media/azure-ad/azure-ad-sec-steps1.png)

### <a name="implement-sign-in-risk-policy-using-azure-ad-identity-protection"></a>Azure AD Identity Protection を使用してサインインのリスク ポリシーを実装する

サインイン リスクは、アカウント所有者でないユーザーが ID を使用してサインオンを試みている確率です。 [サインイン リスク ポリシー](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)は、特定のユーザーまたはグループに関してリスク レベルを評価する条件付きアクセスポリシーです。 リスク レベル (高/中/低) に基づいて、アクセスをブロックしたり、多要素認証を適用したりするよう、ポリシーを構成できます。 リスクが中以上のサインインには多要素認証を適用するようにしてください。

![匿名の IP からのサインイン](media/azure-ad/azure-ad-sec-steps2.png)

## <a name="step-4---increase-your-awareness"></a>ステップ 4. - 認識を高める

セキュリティ関連イベントの監査とログ、および関連する警告は、効率的な保護戦略に欠かせない構成要素です。 セキュリティのログやレポートは、疑わしいアクティビティの電子記録となり、外部からネットワークへの侵入または内部からの攻撃が試みられたこと、または成功したことを示すパターンを検出するために役立ちます。 監査機能を使うと、ユーザーのアクティビティの監視、規制へのコンプライアンスの文書化、フォレンジック分析などが可能になります。 警告によってセキュリティ イベントが通知されます。

### <a name="monitor-azure-ad"></a>Azure AD を監視する

Microsoft Azure のサービスや機能が提供する構成可能なセキュリティ監査およびログのオプションは、セキュリティ ポリシーやセキュリティ メカニズムのギャップを識別してセキュリティ侵害を防止するための対策を講じるうえで役立ちます。 [Azure のログと監査](https://docs.microsoft.com/azure/security/azure-log-audit)を使用できるほか、[Azure Active Directory ポータルの監査アクティビティ レポート](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)を使用できます。

### <a name="monitor-azure-ad-connect-health-in-hybrid-environments"></a>ハイブリッド環境で Azure AD Connect Health を監視する

[Azure AD Connect Health による ADFS の監視](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-adfs)では、潜在的な問題に関するより優れた分析情報と、ADFS インフラストラクチャへの攻撃の可視性が得られます。 Azure AD Connect Health では、詳細、解決手順、関連ドキュメントへのリンクが含まれた警告のほか、認証トラフィックに関するいくつかのメトリックの利用状況分析、パフォーマンスの監視、レポートが提供されます。

![Azure AD Connect Health](media/azure-ad/azure-ad-sec-steps4.png)

### <a name="monitor-azure-ad-identity-protection-events"></a>Azure AD Identity Protection イベントを監視する

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) は、通知、監視、およびレポートのツールであり、組織の ID に影響する潜在的な脆弱性を検出するために使用できます。 感染しているデバイス、匿名の IP アドレス、疑わしいアクティビティに関連した IP アドレス、および不明な場所からのサインイン、漏洩した資格情報、あり得ない移動など、リスク イベントを検出します。 通知アラートを有効にすると、危険な状態のユーザーのメールや週間ダイジェスト メールが届きます。

![リスクのフラグ付きユーザー](media/azure-ad/azure-ad-sec-steps3.png)

## <a name="step-5---enable-end-user-self-help"></a>ステップ 5. - エンドユーザー セルフヘルプを有効にする

できるだけセキュリティと生産性のバランスをとる必要があります。 長期的なセキュリティの基盤を構築するという考え方と同様の方針の運用アプローチに従って、警戒を緩めないままユーザーに権限を与えることで、組織の手間を省くことができます。 

### <a name="implement-self-service-password-reset"></a>セルフサービスのパスワード リセットを実装する

Azure の[セルフサービスのパスワード リセット (SSPR)](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started) では、IT 管理者は簡単に、ユーザーが管理者の手を借りずにパスワードやアカウントのリセットまたはロック解除を行えるようにできます。 このシステムには、ユーザーがいつシステムにアクセスしたかを追跡する詳細なレポートと、誤用または悪用について警告する通知が用意されています。 

### <a name="implement-self-service-group-management"></a>セルフサービス グループ管理を実装する

Azure AD には、セキュリティ グループと Office 365 グループを使用してリソースへのアクセスを管理する機能があります。 これらのグループは、IT 管理者ではなくグループ所有者が管理できます。 [セルフサービス グループ管理](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)と呼ばれるこの機能では、管理者ロールが割り当てられていないグループ所有者が、彼らの依頼を処理する管理者に頼らずにグループを作成して管理できます。

### <a name="implement-azure-ad-access-reviews"></a>Azure AD アクセス レビューを実装する

[Azure AD アクセス レビュー](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview)では、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、特権ロールの割り当てを管理して、必要ない場合に長時間アクセス権をユーザーに付与しないよう、セキュリティ標準を確実に維持できます。

## <a name="summary"></a>まとめ

ID インフラストラクチャのセキュリティについては多数の側面があります。しかし、この 5 ステップのチェックリストを利用すれば、セキュリティで保護されたより安全な ID インフラストラクチャをすばやく実現できます。

* 資格情報を強化する。
* 攻撃の対象となる領域を減らす。
* 脅威への対応を自動化する。
* 監査と監視の認識を高める。
* セルフヘルプによる、より予測可能で完全なエンドユーザー セキュリティを有効にする。

Microsoft は、お客様が ID セキュリティの重要性を認識していることを理解しています。ぜひ、このドキュメントを、組織のセキュリティ体制を強化するためのロードマップとしてお役立てください。

## <a name="next-steps"></a>次の手順
推奨アクションの計画と展開にサポートが必要な場合は、[Azure AD プロジェクト デプロイ計画](http://aka.ms/deploymentplans)をヘルプとして参照してください。
