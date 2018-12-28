---
title: 新機能 リリース ノート - Azure Active Directory | Microsoft Docs
description: 最新のリリース ノート、既知の問題、バグの修正、非推奨の機能、予定されている変更点など、Azure Active Directory の新着情報について説明します。
services: active-directory
author: eross-msft
manager: mtillman
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.openlocfilehash: 3021b919a83d7d5822f2ed5758e7e39cc76663d5
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312872"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory の新着情報

>URL `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` をコピーして、お使いの ![RSS アイコン](./media/whats-new/feed-icon-16x16.png) フィード リーダーに貼り付け、更新内容を確認するためにこのページに再度アクセスするタイミングに関する通知を受け取るようにしてください。

Azure AD は随時改善されています。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

- 最新のリリース
- 既知の問題
- バグの修正
- 非推奨の機能
- 変更の計画

このページは毎月更新されるため、定期的にアクセスしてご確認ください。 6 か月以上前の項目を探している場合は、「[Archive for What's new in Azure Active Directory (Azure Active Directory の新着情報のアーカイブ)](whats-new-archive.md)」で見つけることができます。

---
## <a name="novemberdecember-2018"></a>2018 年 11 月/12 月

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>重大な変更:Azure Monitor を通じた監査およびサインイン ログ スキーマの更新

**種類:** 変更された機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

現在、監査とサインインのログ ストリームはどちらも Azure Monitor を通じて公開していますので、お使いの SIEM ツールや Log Analytics とログ ファイルをシームレスに統合できます。 お客様のフィードバックに基づき、またこの機能の一般提供の発表に備えて、次の変更がスキーマに加えられます。 これらのスキーマ変更と関連ドキュメントの更新は 1 月の第 1 週までに行われる予定です。

#### <a name="new-fields-in-the-audit-schema"></a>監査スキーマの新しいフィールド
リソースに対して実行される操作の種類を提供するために、新しい **[操作の種類]** フィールドが追加されます。 たとえば、**[追加]**、**[更新]**、**[削除]** などです。

#### <a name="changed-fields-in-the-audit-schema"></a>監査スキーマの変更されるフィールド
監査スキーマの次のフィールドが変更されます:

|フィールド名|変更箇所|古い値|新しい値|
|----------|------------|----------|----------|
|Category|以前は **[サービス名]** フィールドでした。 現在は **[監査カテゴリ]** フィールドです。 **[サービス名]** は **loggedByService** フィールドに名前が変更されました。|<ul><li>アカウント プロビジョニング</li><li>Core Directory (コア ディレクトリ)</li><li>セルフサービスのパスワード リセット</li></ul>|<ul><li>[ユーザー管理]</li><li>グループ管理</li><li>アプリの管理</li></ul>|
|targetResources|**TargetResourceType** を最上位レベルに含みます。|&nbsp;|<ul><li>ポリシー</li><li>アプリ</li><li>User</li><li>グループ</li></ul>|
|loggedByService|監査ログを生成したサービスの名前を提供します。|Null|<ul><li>アカウント プロビジョニング</li><li>Core Directory (コア ディレクトリ)</li><li>セルフサービスのパスワード リセット</li></ul>|
|結果|監査ログの結果を提供します。 以前は、これは列挙されていましたが、実際の値を示すようになります。|<ul><li>0</li><li>1</li></ul>|<ul><li>Success</li><li>失敗</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>サインイン スキーマの変更されるフィールド
サインイン スキーマの次のフィールドが変更されます:

|フィールド名|変更箇所|古い値|新しい値|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|これは **conditionalaccessPolicies** フィールドでした。 現在は **appliedConditionalAccessPolicies** フィールドです。|変更なし|変更なし|
|conditionalAccessStatus|サインイン時点の条件付きアクセス ポリシー ステータスの結果を提供します。 以前は、これは列挙されていましたが、実際の値を示すようになります。|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>失敗</li><li>未適用</li><li>Disabled</li></ul>|
|appliedConditionalAccessPolicies: 結果|サインイン時点の個別の条件付きアクセス ポリシー ステータスの結果を提供します。 以前は、これは列挙されていましたが、実際の値を示すようになります。|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>失敗</li><li>未適用</li><li>Disabled</li></ul>|

スキーマの詳細については、「[Azure Monitor の Azure AD 監査ログ スキーマを解釈する (プレビュー)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)」を参照してください。

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Identity Protection の教師あり機械学習モデルとリスク スコア エンジンの機能強化

**種類:** 変更された機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** リスク スコア

Identity Protection に関連した、ユーザーおよびサインインのリスク評価エンジンの機能強化により、ユーザー リスクの精度とカバレッジを向上させることができます。 管理者にとっての注目点は、ユーザーのリスク レベルが特定の検出のリスク レベルに直接リンクされなくなったことと、リスクの高いサインイン イベントの数とレベルが増加していることです。

ユーザーのサインインと検出のパターンの追加機能を使用してユーザーのリスクを計算する、教師ありの機械学習モデルによってリスクの検出が評価されるようになりました。 ユーザーに関連付けられた検出のリスクが低または中の場合でも、このモデルに基づいて、管理者はリスク スコアの高いユーザーを見つけることができます。 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>管理者が Microsoft Authenticator アプリ (パブリック プレビュー) を使用して自分自身のパスワードをリセット可能

**種類:** 変更された機能  
**サービス カテゴリ:** セルフサービスによるパスワードのリセット  
**製品の機能:** ユーザー認証

Azure AD 管理者は、Microsoft Authenticator アプリの通知や、任意のモバイル認証アプリまたはハードウェア トークンのコードを使用して、自分自身のパスワードをリセットできるようになりました。 自分自身のパスワードをリセットするために、管理者は次の方法のうちの 2 つを使用できるようになります:

- Microsoft Authenticator アプリの通知

- その他のモバイル認証アプリ/ハードウェア トークンのコード

- 電子メール

- 音声通話

- テキスト メッセージ

Microsoft Authenticator アプリを使用したパスワードのリセットの詳細については、[Azure AD のセルフサービスでのパスワード リセット - モバイル アプリと SSPR (プレビュー)](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview) に関するページを参照してください。

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>新しい Azure AD Cloud Device Administrator ロール (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** デバイスの登録と管理  
**製品の機能:** アクセス制御

クラウド デバイス管理タスクを実行するために、管理者はユーザーを新しい Cloud Device Administrator ロールに割り当てることができます。 Cloud Device Administrators ロールを割り当てられたユーザーは、Azure AD でデバイスを有効化、無効化、削除できることに加えて、Azure portal で Windows 10 の BitLocker キー (ある場合) を読み取ることができます。

ロールとアクセス許可の詳細については、「[Azure Active Directory の管理者ロールの割り当て](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)」を参照してください

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Azure AD で新しいアクティビティ タイムスタンプを使用してデバイスを管理する (パブリック プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** デバイスの登録と管理  
**製品の機能:** デバイスのライフサイクル管理

Azure AD では、古くなったデバイスを環境から排除するために、定期的に組織のデバイスを更新したり、インベントリから削除したりする必要があります。 このプロセスを支援するために、Azure AD では、新しいアクティビティ タイムスタンプによってデバイスが更新されることで、デバイスのライフサイクルが管理しやすくなりました。

このタイムスタンプを取得および使用する方法については、「[方法:Azure AD で古いデバイスを管理する](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)」を参照してください

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>管理者が各デバイスで利用規約の承諾をユーザーに要求可能

**種類:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス
 
管理者は **[各デバイスでユーザーによる同意が必要]** オプションを有効にすることで、テナントでユーザーが使用しているすべてのデバイスで利用規約の承諾をユーザーに要求できるようになりました。

詳細については、[Azure Active Directory Terms of Use 機能の、デバイスごとの利用規約に関するセクション](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#per-device-terms-of-use)を参照してください。

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>管理者が定期的なスケジュールに基づいて利用規約の有効期限を設定可能

**種類:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス
 

管理者は **[期限切れの同意]** オプションを有効にすることで、すべてのユーザーを対象に、利用規約の有効期限を指定の定期スケジュールに基づいて設定できるようになりました。 年に 1 回、半年に 1 回、3 か月に 1 回、または月に 1 回のスケジュールを指定できます。 利用規約の有効期限が切れた後は、ユーザーの再承諾が必要です。

詳細については、[Azure Active Directory Terms of Use 機能の、利用規約の追加に関するセクション](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#add-terms-of-use)を参照してください。

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>管理者が各ユーザーのスケジュールに基づいて利用規約の有効期限を設定可能

**種類:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス

管理者は、ユーザーが利用規約を再承諾しなければならない期間を指定できるようになりました。 たとえば、管理者は、ユーザーが 90 日おきに利用規約を再承諾しなければならないことを指定できます。

詳細については、[Azure Active Directory Terms of Use 機能の、利用規約の追加に関するセクション](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#add-terms-of-use)を参照してください。
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Azure Active Directory のロールに関する新しい Azure AD Privileged Identity Management (PIM) の電子メール

**種類:** 新機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management
 
Azure AD Privileged Identity Management (PIM) を使用しているお客様は、過去 7 日間の次の情報を含むダイジェスト電子メールを毎週受信できるようになりました:

- 最上位の有資格かつ永続的なロール割り当ての概要

- ロールをアクティブ化しているユーザー数

- PIM でロールに割り当てられたユーザー数

- PIM 外でロールに割り当てられたユーザー数

- PIM で "永続化" されたユーザー数

PIM と利用可能な電子メール通知の詳細については、「[PIM での電子メール通知](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)」を参照してください。

---

### <a name="group-based-licensing-is-now-generally-available"></a>グループベースのライセンスの一般提供開始

**種類:** 変更された機能  
**サービス カテゴリ:** その他  
**製品の機能:** Directory

グループベースのライセンスのパブリック プレビューが終了し、一般提供が開始されました。 この一般向けリリースの一環として、この機能のスケーラビリティの向上に加えて、単一ユーザーへのグループベースのライセンス割り当てを再処理できるようになり、Office 365 E3/A3 ライセンスでグループベースのライセンスが使用できるようになりました。

グループベースのライセンスの詳細については、「[Azure Active Directory のグループベースのライセンスとは](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)」を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2018 年 11 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリ  
**製品の機能:** サード パーティ統合
 
2018 年 11 月に、フェデレーションを使用した以下の 26 の新規アプリのサポートが、アプリ ギャラリーに追加されました:

[CoreStack](https://cloud.corestack.io/site/login)、[HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial)、[GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial)、[Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial)、[eHour](https://getehour.com/try-now)、[Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial)、[Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial)、[DriveDollar](https://www.drivedollar.com/Account/Login)、[Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial)、[Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial)、[Alaya](https://alayagood.com/en/demo/)、[HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial)、[Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial)、[Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial)、[Zenegy for Business Central 365](https://accounting.zenegy.com/)、[Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial)、[IDEO](https://profile.ideo.com/users/sign_up)、[Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial)、[Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial)、[Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial)、[Plex Apps - Classic Test](https://test.plexonline.com/signon)、[Plex Apps – Classic](https://www.plexonline.com/signon)、[Plex Apps - UX Test](https://test.cloud.plex.com/sso)、[Plex Apps – UX](https://cloud.plex.com/sso)、[Plex Apps – IAM](https://accounts.plex.com/)、[CRAFTS - Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration) 

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

## <a name="october-2018"></a>2018 年 10 月

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD のログを Azure Log Analytics (パブリック プレビュー) と連携させることが可能になりました

**種類:** 新機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

Azure AD のログを Azure Log Analytics に転送できるようになりました。 要望の特に多かったこの機能が追加されたことで、ビジネス、運営、セキュリティに関する分析データへのアクセスがこれまで以上に改善され、インフラストラクチャの監視機能も強化されました。 詳しくは、関連のブログ記事 (「[Azure Active Directory Activity logs in Azure Log Analytics now available (Azure Active Directory のアクティビティ ログが Azure Log Analytics で利用できるようになりました)](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843)」) をご覧ください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2018 年 10 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリ  
**製品の機能:** サード パーティ統合
 
2018 年 10 月に、フェデレーションを使用した以下の 14 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[My Award Points](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial)、[Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial)、ambyint、[MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial)、[BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial)、Dialpad、[ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial)、[RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial)、[Zscaler Three](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial)、[Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial)、[Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial)、[Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial)、[Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial)、[Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services の電子メール通知

**種類:** 新機能  
**サービス カテゴリ:** Azure AD Domain Services  
**製品の機能:** Azure AD Domain Services

Azure AD Domain Services では、マネージド ドメインの構成ミスや問題に関するアラートが、Azure portal を通じて提供されます。 これらのアラートにはステップ バイ ステップ ガイドが含まれているので、サポートへの連絡を必要とすることなく、問題の解決に着手できます。

10 月からは、マネージド ドメインの通知設定をカスタマイズできるようになるので、新しいアラートの発生時に、指定のユーザー グループへ電子メールを自動送信できるようになります。これにより、ポータルで更新プログラムを常時確認する必要がなくなります。

詳しくは、「[Azure AD Domain Services の通知設定](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications)」をご覧ください。

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD ポータルで、ForceDelete ドメイン API を使用してカスタム ドメインを削除できるようになりました 

**種類:** 変更された機能  
**サービス カテゴリ:** ディレクトリ管理  
**製品の機能:** Directory

ForceDelete ドメイン API を使用して、ユーザー、グループ、アプリなどの参照を、カスタム ドメイン名 (contoso.com) から既定の初期ドメイン名 (contoso.onmicrosoft.com) へと非同期的に名前変更することにより、カスタム ドメイン名を削除できるようになりました。

これにより、カスタム ドメイン名が使用されなくなった場合や、そのドメイン名を別の Azure AD で使用する必要が生じた場合に、ドメイン名をよりすばやく削除できるようになりました。

詳しくは、「[カスタム ドメイン名を削除する](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name)」をご覧ください。

---

## <a name="september-2018"></a>2018 年 9 月
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>動的グループの管理者ロールのアクセス許可が更新された

**種類:** 修正済み  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

特定の管理者ロールが、グループの所有者になる必要なく、動的メンバーシップ ルールの作成と更新を行えるように問題を修正しました。

これらのロールを次に示します。

- グローバル管理者または会社ライター

- Intune サービス管理者

- ユーザー アカウント管理者

詳細については、「[動的グループの作成と状態チェックを行う](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)」を参照してください

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>一部のサード パーティ製アプリケーションのシングル サインオン (SSO) 構成設定を簡素化

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリ  
**製品の機能:** SSO

各アプリケーション構成の固有の性質が原因で、サービスとしてのソフトウェア (SaaS) アプリケーションに向けたシングル サインオン (SSO) の設定が困難であることは、Microsoft でも認識しています。 以下のサードパーティ製 SaaS アプリケーションの SSO 構成設定を自動入力するため、簡素化した構成エクスペリエンスを構築しました。

- Zendesk

- ArcGis Online

- Jamf Pro

このワンクリック エクスペリエンスの使用を開始するには、**Azure portal** > アプリケーションの **SSO 構成**ページにアクセスします。 詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)」を参照してください

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory のデータの保管場所に関するページ

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** GoLocal

**Azure Active Directory のデータの保管場所**に関するページから会社のリージョンを選択すると、Azure AD のすべてのサービスについて、どの Azure データ センターに Azure AD の保存データが格納されているかが表示されます。 この情報は、会社のリージョンに関して、特定の Azure AD サービスに基づいてフィルター処理できます。

この機能へのアクセス方法と機能の詳細については、[Azure Active Directory のデータの保管場所](https://aka.ms/AADDataMap)に関するページを参照してください。

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>[マイ アプリ] アクセス パネルで新しいデプロイ計画を使用可能

**種類:** 新機能  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** SSO

[マイ アプリ] アクセス パネルで使用できる新しいデプロイ計画をご確認ください (https://aka.ms/deploymentplans)。
[マイ アプリ] アクセス パネルによって、ユーザーは 1 つの場所でアプリケーションの検索とアクセスを行えます。 このポータルでは、ユーザーはセルフ サービスで、アプリケーションとグループへのアクセスを要求したり、他のユーザーに代わってこれらのリソースへのアクセスを管理したりすることなどが可能です。

詳細については、「[マイ アプリ ポータルとは](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)」を参照してください

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Azure portal の [サインイン] ログ ページに [トラブルシューティングおよびサポート] タブを新設

**種類:** 新機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

Azure portal の **[サインイン]** ページの **[トラブルシューティングおよびサポート]** タブは、管理者やサポート エンジニアが、Azure AD のサインインに関連する問題のトラブルシューティングを行う助けとなるように設計されています。この新しいタブには、問題の解決に役立つように、エラー コード、エラー メッセージ、および修復の推奨事項 (ある場合) が表示されます。 問題を解決できない場合は、**クリップボードへのコピー**操作によってサポート チケットを作成する新しい方法も用意されています。この方法で、サポート チケット内のログ ファイルの**要求 ID** と**日付 (UTC)** のフィールドが設定されます。  

![新しいタブのある [サインイン] のログ](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>動的メンバーシップ ルールを作成するために使用されるカスタム拡張機能プロパティのサポートの強化

**種類:** 変更された機能  
**サービス カテゴリ:** グループ管理  
**製品の機能:** コラボレーション

この更新プログラムでは、動的ユーザー グループのルール ビルダーにある **[Get custom extension properties] (カスタム拡張機能プロパティを取得する)** リンクをクリックし、一意のアプリ ID を入力すると、ユーザーの動的メンバーシップ ルールの作成時に使用するカスタム拡張機能プロパティの完全な一覧が表示されるようになりました。 この一覧を最新の情報に更新して、そのアプリの新しいカスタム拡張機能プロパティを取得することもできます。

動的メンバーシップ ルール用のカスタム拡張機能プロパティの使用に関する詳細については、[拡張機能プロパティおよびカスタム拡張機能プロパティ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)についてのページを参照してください

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD アプリベースの条件付きアクセス向けの承認されたクライアント アプリの新規追加

**種類:** 変更の計画  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護

以下のアプリが、[承認されたクライアント アプリ](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement)の一覧に示されます。

- Microsoft To-Do

- Microsoft Stream

詳細については、次を参照してください。

- [Azure AD のアプリベースの条件付きアクセス](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Windows 7/8/8.1 のロック画面からのセルフ サービスによるパスワード リセットを新たにサポート

**種類:** 新機能  
**サービス カテゴリ:** SSPR  
**製品の機能:** ユーザー認証

この新しい機能を設定すると、Windows 7、Windows 8、または Windows 8.1 を実行しているデバイスの**ロック**画面から自分のパスワードをリセットするためのリンクがユーザーに表示されます。 そのリンクをクリックすると、ユーザーには、Web ブラウザーを介する場合と同じパスワードのリセット フローが示されます。

詳細については、[Windows 7、8、8.1 からパスワードのリセットを有効にする方法](https://aka.ms/ssprforwindows78)に関するページを参照してください

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>変更通知:承認コードを再利用できなくなる 

**種類:** 変更の計画  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証

2018 年 11 月 15 日以降、Azure AD では、以前使用されていた、アプリの認証コードの受け入れが停止されます。 このセキュリティの変更により、Azure AD と OAuth の仕様が一致するようになります。この変更は、v1 と v2 両方のエンドポイントに適用されます。

お使いのアプリで承認コードを再利用して複数のリソースに対するトークンを取得している場合は、コードを使用して更新トークンを取得した後、その更新トークンを使用して他のリソース用のトークンを追加取得することお勧めします。 承認コードは 1 回しか使用できませんが、更新トークンは複数のリソースで複数回使用できます。 OAuth コード フローの間にアプリで認証コードを再利用しようとすると、invalid_grant エラーが発生します。

このことと、その他のプロトコル関連の変更については、[認証についての新機能の完全な一覧](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes)を参照してください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2018 年 9 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリ  
**製品の機能:** サード パーティ統合
 
2018 年 9 月に、フェデレーションを使用した以下の 16 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial)、[Comeet Recruiting Software](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial)、[Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial)、[ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial)、[Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial)、[JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial)、[Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial)、NavigoCloud、[Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial)、join.me、[ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial)、[Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial)、Riverbed Xirrus EasyPass、[Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial)、Enlyft SSO for Azure、SurveyMonkey、[Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial)、[dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="support-for-additional-claims-transformations-methods"></a>要求変換メソッドの追加サポート

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリ  
**製品の機能:** SSO

新しい要求変換メソッドの ToLower() と ToUpper() を導入しました。これらは、SAML ベースの **[シングル サインオンの構成]** ページから SAML トークンに適用できます。

詳細については、[Azure AD のエンタープライズ アプリケーションの SAML トークンで発行された要求をカスタマイズする方法](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)についてのページを参照してください

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>SAML ベースのアプリの構成 UI を更新 (プレビュー)

**種類:** 変更された機能  
**サービス カテゴリ:** エンタープライズ アプリ  
**製品の機能:** SSO

SAML ベースのアプリの構成 UI が更新された一部として、以下が提供されます。

- SAML ベースのアプリを構成するためのチュートリアル エクスペリエンスの更新。

- 構成において不足している、または正しくないものに関する可視性の向上。

- 証明書の有効期限通知のために複数のメール アドレスを追加する機能。

- 新しい要求変換メソッドの ToLower() と ToUpper() など。

- エンタープライズ アプリのために独自のトークン署名証明書をアップロードする方法。

- SAML アプリの NameID 形式を設定する方法と、ディレクトリ拡張機能として NameID 値を設定する方法。

この更新されたビューをオンにするには、**[シングル サインオン]** ページ上部の **[Try out our new experience] (新しいエクスペリエンスを試す)** リンクをクリックします。 詳細については、「[チュートリアル:Azure Active Directory による SAML ベースのシングル サインオンをアプリケーションに構成する](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal)」を参照してください。

---

## <a name="august-2018"></a>2018 年 8 月

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Azure Active Directory の IP アドレス範囲の変更

**種類:** 変更の計画  
**サービス カテゴリ:** その他  
**製品の機能:** プラットフォーム

Microsoft では、Azure AD に対してより大規模な IP 範囲の導入を進めています。つまり、ファイアウォール、ルーター、またはネットワーク セキュリティ グループの Azure AD IP アドレス範囲を構成している場合はそれらを更新する必要があります。 Microsoft では、Azure AD で新しいエンドポイントが追加されたときにファイアウォール、ルーター、またはネットワーク セキュリティ グループの IP アドレス範囲の構成を再び変更する必要がないように、この更新を行っています。 

ネットワーク トラフィックは、今後 2 か月にわたってこれらの新しい範囲に移行されます。 中断のないサービスを継続するには、2018 年 9 月 10 日までに、次に示す更新された値を IP アドレスに追加する必要があります。

- 20.190.128.0/18 

- 40.126.0.0/18 

すべてのネットワーク トラフィックが新しい範囲に移行されるまでは古い IP アドレス範囲を削除しないことを強くお勧めします。 移行に関する最新情報や古い範囲を削除できるタイミングについては、「[Office 365 URLs and IP address ranges (Office 365 の URL と IP アドレス範囲)](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)」を参照してください。

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>変更通知:承認コードを再利用できなくなる 

**種類:** 変更の計画  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証

2018 年 11 月 15 日以降、Azure AD では、以前使用されていた、アプリの認証コードの受け入れが停止されます。 このセキュリティの変更により、Azure AD と OAuth の仕様が一致するようになります。この変更は、v1 と v2 両方のエンドポイントに適用されます。

お使いのアプリで承認コードを再利用して複数のリソースに対するトークンを取得している場合は、コードを使用して更新トークンを取得した後、その更新トークンを使用して他のリソース用のトークンを追加取得することお勧めします。 承認コードは 1 回しか使用できませんが、更新トークンは複数のリソースで複数回使用できます。 OAuth コード フローの間にアプリで認証コードを再利用しようとすると、invalid_grant エラーが発生します。

このことと、その他のプロトコル関連の変更については、[認証についての新機能の完全な一覧](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes)を参照してください。
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>セルフサービスのパスワード (SSPR) と Multi-Factor Authentication (MFA) の集中型セキュリティ情報管理

**種類:** 新機能  
**サービス カテゴリ:** SSPR  
**製品の機能:** ユーザー認証
 
この新しい機能を使用すると、ユーザーは SSPR および MFA に対するセキュリティ情報 (電話番号、モバイル アプリなど) を 1 つの場所とエクスペリエンス管理できます。以前は、2 つの異なる場所で行われていました。

この集中型エクスペリエンスは、SSPR または MFA のどちらかを使用しているユーザーでも機能します。 さらに、MFA または SSPR の登録を強制していない組織の場合でも、ユーザーは、組織で許可されている MFA または SSPR のセキュリティ情報の方法に、マイ アプリ ポータルから登録できます。

これは、オプトイン パブリック プレビューです。 管理者は、新しいエクスペリエンスを (必要に応じて) テナント内の特定のグループまたはすべてのユーザーに有効にできます。 集中型エクスペリエンスについて詳しくは、[集中型エクスペリエンスのブログ](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)をご覧ください。

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Azure AD アプリケーション プロキシ アプリでの新しい HTTP 専用 Cookie の設定

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** Access Control

アプリケーション プロキシ アプリには、**HTTP 専用 Cookie** と呼ばれる新しい設定があります。 この設定では、アプリケーション プロキシ アクセス Cookie とセッション Cookie の両方に対する HTTP 応答ヘッダーに HTTPOnly フラグを追加して、クライアント側スクリプトから Cookie へのアクセスを停止し、さらに Cookie のコピーや変更などのアクションを防止することにより、セキュリティを強化します。 このフラグは以前は使用されていませんでしたが、Cookie は常に暗号化され、SSL 接続を使用して送信されることで、不適切な変更から保護されていました。

この設定は、リモート デスクトップなどの ActiveX コントロールを使用するアプリとの互換性はありません。 このような状況の場合は、この設定をオフにすることをお勧めします。

HTTP 専用 Cookie の設定について詳しくは、「[Azure AD アプリケーション プロキシを使用してアプリケーションを発行する](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal)」をご覧ください。

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Azure リソースに対する Privileged Identity Management (PIM) での管理グループのリソースの種類のサポート

**種類:** 新機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management
 
サブスクリプション、リソース グループ、リソース (VM、App Services など) に対して既に行われているのと同じように、管理グループのリソースの種類に対しても Just-In-Time アクティブ化と割り当ての設定を適用できるようになります。 さらに、管理グループに対して管理者アクセスできるロールを持つすべてのユーザーは、PIM でそのリソースを検出して管理できます。

PIM と Azure リソースについて詳しくは、「[Privileged Identity Management を使用した Azure リソースの探索と管理](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)」をご覧ください。
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>アプリケーション アクセス (プレビュー) では、Azure AD ポータルへのより高速なアクセスが提供される

**種類:** 新機能  
**サービス カテゴリ:** Privileged Identity Management  
**製品の機能:** Privileged Identity Management
 
現在、PIM を使用してロールをアクティブ化すると、アクセス許可が有効になるまでに 10 分以上かかることがあります。 アプリケーション アクセス (現在パブリック プレビュー段階) を使用した場合、管理者はアクティブ化要求が完了するとすぐに Azure AD ポータルにアクセスすることができます。

現時点では、アプリケーション アクセスでサポートされているのは、Azure AD ポータル エクスペリエンスと Azure リソースのみです。 PIM とアプリケーション アクセスについて詳しくは、「[Azure AD Privileged Identity Management とは](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)」をご覧ください。
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2018 年 8 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリ  
**製品の機能:** サード パーティ統合
 
2018年 8 月に、フェデレーションを使用した以下の 16 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial)、[Bridgeline Unbound](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial)、[Sauce Labs - Mobile and Web Testing](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial)、[Meta Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial)、[Way We Do](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial)、[Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial)、[ProMaster (by Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial)、SchoolBooking、[4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial)、[Dossier](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial)、[N2F - Expense reports](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial)、[Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial)、[SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial)、[ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial)、[eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial)、[Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial)。

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Tableau のネイティブ サポートが Azure AD アプリケーション プロキシで使用できるようになった

**種類:** 変更された機能  
**サービス カテゴリ:** アプリ プロキシ  
**製品の機能:** Access Control

事前認証プロトコルについての OpenID Connect から OAuth 2.0 Code Grant プロトコルへの更新により、アプリケーション プロキシで Tableau を使用するために追加構成を行う必要がなくなりました。 このプロトコルの変更により、JavaScript と HTML のタグで一般的にサポートされる HTTP のみのリダイレクトを使用することで、アプリケーション プロキシによる最新のアプリのサポートも向上しました。

Tableau のネイティブ サポートについて詳しくは、「[Azure AD Application Proxy now with native Tableau support](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support)」(Tableau をネイティブにサポートするようになった Azure AD アプリケーション プロキシ) をご覧ください。

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Azure Active Directory で B2B ゲスト ユーザーに対する ID プロバイダーとして Google を追加する新しいサポート (プレビュー)

**種類:** 新機能  
**サービス カテゴリ:** B2B  
**製品の機能:** B2B/B2C

組織において Google とのフェデレーションを設定することにより、招待された Gmail ユーザーは既存の Google アカウントを使用して共有アプリおよびリソースにサインインできます。個人の Microsoft アカウント (MSA) または Azure AD アカウントを作成する必要はありません。

これは、オプトイン パブリック プレビューです。 Google のフェデレーションについて詳しくは、「[Add Google as an identity provider for B2B guest users](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)」(B2B ゲスト ユーザーの ID プロバイダーとして Google を追加する) をご覧ください。

---

## <a name="july-2018"></a>2018 年 7 月

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Azure Active Directory の電子メール通知の機能強化

**種類:** 変更された機能  
**サービス カテゴリ:** その他  
**製品の機能:** ID ライフサイクル管理
 
Azure Active Directory (Azure AD) の設計が更新され、以下のサービスから送信された場合、送信者メール アドレスと送信者表示名が変更されます。
 
- Azure AD アクセス レビュー
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- エンタープライズ アプリの証明書期限切れ通知
- エンタープライズ アプリのサービス プロビジョニング通知
 
メール通知は、次のメール アドレスと表示名から送信されます。

- メール アドレス: azure-noreply@microsoft.com
- 表示名:Microsoft Azure
 
新しい電子メール デザインのいくつかの例と詳細は、「[Azure AD PIM の電子メール通知](https://go.microsoft.com/fwlink/?linkid=2005832)」で参照してください。

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD アクティビティ ログは Azure Monitor から使用できるようになりました。

**種類:** 新機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

Azure AD アクティビティ ログは、Azure Monitor (Azure のプラットフォーム全体の監視サービス) のパブリック プレビューで利用可能になりました。 Azure Monitor は、これらの機能強化に加え、長期保有とシームレスな統合を提供します。

- ログ ファイルを Azure ストレージ アカウントにルーティングすることによる長期保有。

- シームレスな SIEM 統合により、カスタム スクリプトを作成または管理する必要がありません。

- お客様独自のカスタム ソリューション、分析ツール、またはインシデント管理ソリューションとシームレスに統合。

これらの新機能の詳細については、マイクロソフト公式ブログ「[Azure AD activity logs in Azure Monitor diagnostics is now in public preview](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/)」(Azure Monitor 診断の Azure AD アクティビティ ログは現在パブリック プレビュー中です) や [Azure Monitor の Azure Active Directory アクティビティ ログ (プレビュー)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview) に関するマイクロソフトのドキュメントを参照してください。

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Azure AD サインイン レポートに条件付きアクセス情報を追加

**種類:** 新機能  
**サービス カテゴリ:** レポート  
**製品の機能:** ID のセキュリティと保護
 
この更新により、ユーザーがサインインしたときにどのポリシーが評価されるか、また、そのポリシーの結果を確認できるようになりました。 さらに、ユーザーによって使用されるクライアント アプリの種類がレポートに含まれるようになったため、レガシ プロトコルのトラフィックを識別できます。 ユーザーに表示されるエラー メッセージに記載されている相関 ID を条件にレポート エントリ内を検索できるようにもなったため、対応するサインイン要求を特定してトラブルシューティングに利用できます。

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>サインイン アクティビティ ログでレガシ認証を表示

**種類:** 新機能  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート
 
サインイン アクティビティ ログに **[クライアント アプリ]** フィールドを導入することで、お客様がレガシ認証を使用しているユーザーを確認できるようになりました。 この情報にアクセスするには、Sign-ins MS Graph API を使用するか、Azure AD ポータルのサインイン アクティビティ ログで **[クライアント アプリ]** コントロールを使用してレガシー認証をフィルター処理します。 詳細については、ドキュメントをご覧ください。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2018 年 7 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリ  
**製品の機能:** サード パーティ統合
 
2018年 7 月に、フェデレーションを使用した以下の 16 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial)、[Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial)、[Certain Admin SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial)、PSUC Staging、[iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial)、[Screencast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial)、PowerSchool Unified Classroom、[Eli Onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial)、[Bomgar Remote Support](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial)、[Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial)、[Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial)、[Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial)、[SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial)、[Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial)、[SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial)、[Skills Base](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://aka.ms/azureadapprequest)」を参照してください。

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>新しいユーザー プロビジョニング SaaS アプリの統合 - 2018 年 7 月

**種類:** 新機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** サード パーティ統合
 
Azure AD を使用すると、Dropbox、Salesforce、ServiceNow などの SaaS アプリケーションで、ユーザー ID の作成、保守、削除を自動化できます。 2018 年 7 月、Azure AD アプリ ギャラリーの次のアプリケーションに対して、ユーザー プロビジョニング サポートが追加されました。

- [Cisco Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-spark-provisioning-tutorial)

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

ユーザー プロビジョニングをサポートする Azure AD ギャラリーのすべてのアプリケーションの一覧については、[Azure Active Directory と SaaS アプリケーションの統合](https://aka.ms/appstutorial)に関する記事を参照してください。

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health for Sync - 孤立および重複した属性の同期エラーを修正する簡単な方法

**種類:** 新機能  
**サービス カテゴリ:** AD Connect  
**製品の機能:** 監視とレポート
 
Azure AD Connect Health は、同期エラーを特定し修正するためのセルフサービス修復機能を提供します。 この機能は、重複した属性同期エラーのトラブルシューティングを行い、Azure AD から孤立したオブジェクトを修正します。 この診断には、次のような利点があります。

- 重複した属性の同期エラーを絞り込み、特定の修正プログラムを提供します。

- 1 つの手順でエラーを解決する、Azure AD のシナリオ専用の修正プログラムを適用します。

- この機能を有効にするために、アップグレードや構成の必要はありません。

詳細については、「[重複する属性の同期エラーの診断](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)」を参照してください。

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Azure AD のビジュアルと MSA のサインイン エクスペリエンスを更新

**種類:** 変更された機能  
**サービス カテゴリ:** Azure AD  
**製品の機能:** ユーザー認証

Office 365 や Azure などの Microsoft のオンライン サービス用のサインイン エクスペリエンスを更新しました。 この変更により、画面がすっきりし見やすくなりました。 この変更の詳細については、ブログ 「[Azure AD のサインイン画面に関するアップデートのお知らせ](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/)」を参照してください。

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Azure AD Connect の新しいリリース - 2018 年 7 月

**種類:** 変更された機能  
**サービス カテゴリ:** アプリ プロビジョニング  
**製品の機能:** ID ライフサイクル管理

Azure AD Connect の最新リリースは次のとおりです。 

- バグの修正とサポートの更新 

- Ping フェデレーション統合の一般公開

- 最新の SQL 2012 クライアントに更新 

この更新の詳細については、「[Azure AD Connect:バージョンのリリース履歴](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)」を参照してください

---

### <a name="updates-to-the-terms-of-use-tou-end-user-ui"></a>使用条件 (ToU) エンドユーザー UI の更新

**種類:** 変更された機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス

TOU エンドユーザー UI の同意の文字列を更新しています。

**現在のテキスト** [tenantName] リソースにアクセスするには、使用条件に同意する必要があります。<br>**新しいテキスト** [tenantName] リソースにアクセスするには、使用条件を読む必要があります。

**現在のテキスト:** 合意を選択すると上記の使用条件すべてに同意することを意味します。<br>**新しいテキスト:** 使用条件を読んで理解したことを確定するには、[同意する] をクリックしてください。

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>パススルー認証で、レガシのプロトコルとアプリケーションをサポート

**種類:** 変更された機能  
**サービス カテゴリ:** 認証 (ログイン)  
**製品の機能:** ユーザー認証
 
パススルー認証で、レガシのプロトコルとアプリケーションをサポートできるようになりました。 次の制限事項は完全にサポートされています。

- レガシの Office クライアント アプリケーション、Office 2010、および Office 2013 へのユーザー サインイン (先進認証なし)。

- Office 2010 上でのみ、Exchange ハイブリッド環境で予定表の共有と空き時間情報にアクセスします。

- Skype for Business クライアント アプリケーションへのユーザー サインイン (先進認証なし)。

- PowerShell バージョン 1.0 へのユーザー サインイン。

- iOS 設定アシスタントを使用している Apple Device Enrollment Program (Apple DEP)。 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>セルフサービスのパスワード リセットと多要素認証の集中型セキュリティ情報管理

**種類:** 新機能  
**サービス カテゴリ:** SSPR  
**製品の機能:** ユーザー認証

この新しい機能を使用すれば、ユーザーはセキュリティ情報 (たとえば、電話番号、電子メール アドレス、モバイル アプリなど) のセルフサービス パスワード リセット (SSPR) と多要素認証 (MFA) を 1 つのエクスペリエンスで管理できます。 ユーザーが SSPR と MFA の 2 つの異なるエクスペリエンスのために同じセキュリティ情報を登録する必要がなくなります。 この新しいエクスペリエンスは、SSPR または MFA のいずれかを持つユーザーにも適用されます。

組織で MFA または SSPR 登録を使用していない場合は、ユーザーは **Myapps** ポータルを通してセキュリティ情報を登録できます。 そこから、ユーザーは MFA または SSPR に有効な任意の方法を登録できます。 

これは、オプトイン パブリック プレビューです。 管理者は、新しいエクスペリエンス (必要に応じて) を特定のユーザー グループまたはテナント内のすべてのユーザーに有効にできます。

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Microsoft Authenticator アプリを使用して、パスワードをリセットしたときに本人確認する

**種類:** 変更された機能  
**サービス カテゴリ:** SSPR  
**製品の機能:** ユーザー認証

この機能を使用すると、パスワードをリセットする際に、Microsoft Authenticator (または他の認証アプリ) からの通知またはコードを使用して、管理者以外のユーザーが本人確認できます。 管理者が、このセルフサービスによるパスワード リセット方法をオンにすると、aka.ms/mfasetup または aka.ms/setupsecurityinfo を通してモバイル アプリを登録したユーザーは、パスワードをリセットする際にモバイル アプリを本人確認の方法として使用できます。

モバイル アプリの通知は、パスワードのリセットに 2 つの認証方法を要求するポリシーの一部としてのみオンに設定できます。

---

## <a name="june-2018"></a>2018 年 6 月

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>変更通知:Azure AD アクティビティ ログ API を使用するアプリの委任された承認ワークフローに対するセキュリティの修正

**種類:** 変更の計画  
**サービス カテゴリ:** レポート  
**製品の機能:** 監視とレポート

セキュリティ強化のため、委任された承認ワークフローを使って [Azure AD アクティビティ ログ API](https://aka.ms/aadreportsapi) にアクセスするアプリのアクセス許可を変更する必要がありました。 この変更は、**2018 年 6 月 26日**までに行われます。

お使いのアプリに Azure AD アクティビティ ログ API を使っているものがある場合は、以下の手順に従って、変更後にアプリで問題が発生しないことを確認してください。

**アプリのアクセス許可を更新するには**

1. Azure Portal にサインインし、**[Azure Active Directory]** を選択してから **[アプリの登録]** を選択します。
2. Azure AD アクティビティ ログ API を使用しているアプリを選択して **[設定]** を選択し、**[必要なアクセス許可]** を選んでから、**[Windows Azure Active Directory]** API を選択します。
3. **[アクセスの有効化]** ブレードの **[委任されたアクセス許可]** 領域で、**[ディレクトリ データの読み取り]** チェック ボックスをオンにして、**[保存]** を選択します。
4. **[アクセス許可の付与]** を選択し、**[はい]** を選択します。
    
    >[!Note]
    >アプリへのアクセス許可の付与は、全体管理者が行う必要があります。

詳細については、「Azure AD レポート API にアクセスするための前提条件」記事の「[アクセス許可を付与する](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions)」セクションを参照してください。

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>PCI DSS コンプライアンスのために TLS 設定を構成して Azure AD サービスに接続する

**種類:** 新機能  
**サービス カテゴリ:** 該当なし  
**製品の機能:** プラットフォーム

トランスポート層セキュリティ (TLS) は、通信する 2 つのアプリケーション間にプライバシーとデータ整合性を提供するプロトコルであり、今日、最も広く展開され、使用されているセキュリティ プロトコルです。

[PCI Security Standards Council](https://www.pcisecuritystandards.org/) は、**2018 年 6 月 30 日**に開始されたコンプライアンスに沿って、新しいより安全なアプリ プロトコルを有効にして、初期バージョンの TLS および Secure Sockets Layer (SSL) を無効化する必要があることを決定しました。 この変更は、Azure AD サービスに接続し、PCI DSS コンプライアンスを必要とする場合には、TLS 1.0 を無効にしなければならないことを意味します。 複数のバージョンの TLS が使用可能ですが、TLS 1.2 は Azure Active Directory Services に利用できる最新バージョンです。 クライアント/サーバーおよびブラウザー/サーバーの両方の組み合わせにおいて、TLS 1.2 に直接移行することを強くお勧めします。

古いブラウザーでは、TLS 1.2 などの新しい TLS バージョンがサポートされない可能性があります。 お使いのブラウザーでどの TLS バージョンがサポートされるかを確認するには、[Qualys SSL Labs](https://www.ssllabs.com/) サイトへ移動して、**[Test your browser]** をクリックします。 お使いの Web ブラウザーを最新バージョンにアップグレードして、可能であれば TLS 1.2 のみを有効にすることをお勧めします。

**TLS 1.2 を有効にするために、ブラウザーで実行する手順**

- **Microsoft Edge および Internet Explorer (いずれも、Internet Explorer を使用して設定される)**

    1. Internet Explorer を開き、**[ツール]** > **[インターネット オプション]** > **[詳細]** の順に選択します。
    2. **[セキュリティ]** 領域で、**[use TLS 1.2]\(TLS 1.2 を使用する\)** を選択して、**[OK]** をクリックします。
    3. すべてのブラウザー ウィンドウを閉じて、Internet Explorer を再起動します。 

- **Google Chrome**

    1. Google Chrome を開き、「*chrome://settings/*」とアドレス バーに入力して、**Enter** キーを押します。
    2. **[詳細]** オプションを展開し、**[システム]** 領域へ移動して、**[プロキシ セットアップを開く]** を選択します。
    3. **[インターネットのプロパティ]** ボックスで、**[詳細]** タブを選択し、**[セキュリティ]** 領域へ移動して、**[use TLS 1.2]\(TLS 1.2 を使用する\)** を選択してから、**[OK]** をクリックします。
    4. すべてのブラウザー ウィンドウを閉じて、Google Chrome を再起動します。

- **Mozilla Firefox**

    1. Firefox を開き、アドレス バーに「*about:config*」と入力してから、**Enter** キーを押します。
    2. *TLS* という語句を検索して、**security.tls.version.max** エントリを選択します。
    3. 値を **3** に設定して、ブラウザーでバージョン TLS 1.2 までを使用するように設定し、**[OK]** をクリックします。

        >[!NOTE]
        >Firefox バージョン 60.0 では TLS 1.3 をサポートしているため、security.tls.version.max の値を **4** に設定することもできます。

    4. すべてのブラウザー ウィンドウを閉じて、Mozilla Firefox を再起動します。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Azure AD アプリ ギャラリーで入手できる新しいフェデレーション アプリ - 2018 年 6 月

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリ  
**製品の機能:** サード パーティ統合
 
2018年 6 月に、フェデレーションを使用した以下の 15 の新規アプリのサポートが、アプリ ギャラリーに追加されました。

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial)、[Settling music](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial)、[SAML 1.1 Token enabled LOB App](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial)、[Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial)、[Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial)、[Endpoint Backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial)、[Skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial)、Smartway2、[TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial)、[Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial)、[Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial)、[SharePoint on-premises](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial)、[ForeSee CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial)、[Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial)、[ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

アプリに関する詳細については、「[SaaS アプリケーションと Azure Active Directory の統合](https://aka.ms/appstutorial)」を参照してください。 アプリケーションの Azure AD アプリ ギャラリーでの公開に関する詳細については、「[アプリケーションを Azure Active Directory アプリケーション ギャラリーで公開する](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)」を参照してください。 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>パブリック プレビューで使用可能な Azure AD Password Protection

**種類:** 新機能  
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ユーザー認証

Azure AD Password Protection を使用して、簡単に推測できるパスワードをお使いの環境から排除できます。 これらのパスワードを排除すると、パスワード スプレー タイプの攻撃に侵害されるリスクが低下します。

具体的には、Azure AD Password Protection は次のような機能を提供します。

- Azure AD および Windows Server Active Directory (AD) の両方で組織のアカウントを保護する。 
- 最も一般的に使用されている 500 を超えるパスワード一覧のパスワードと、それらのパスワードの 100 万を超える代替文字のバリエーションを、ユーザーが使用しないようにする。
- Azure AD およびオンプレミスの Windows Server AD の両方に対して、Azure AD ポータルの単一の場所から Azure AD Password Protection を管理する。

Azure AD Password Protection の詳細については、「[組織内の不適切なパスワードを排除する](https://aka.ms/aadpasswordprotectiondocs)」を参照してください。

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>使用条件 (ToU: Terms of Use) の作成時に作成された、新しい "すべてのゲスト" 条件付きアクセス ポリシー テンプレート

**種類:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス

使用条件 (ToU) の作成時に、"すべてのゲスト" および "すべてのアプリ" 用の新しい条件付きアクセス ポリシー テンプレートも作成されます。 この新しいポリシー テンプレートでは、新しく作成された ToU を適用し、ゲストの作成および強制プロセスを合理化します。

詳しくは、「[Azure Active Directory Terms of Use 機能](https://docs.microsoft.com/azure/active-directory/active-directory-tou)」を参照してください。

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>使用条件 (ToU) の作成時に作成された、新しい "カスタム" 条件付きアクセス ポリシー テンプレート

**種類:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス

使用条件 (ToU) の作成時に、新しい "カスタム" 条件付きアクセス ポリシー テンプレートも作成されます。 この新しいポリシー テンプレートでは、ToU を作成してから、すぐに条件付きアクセス ポリシーの作成ブレードに移動できます。ポータル経由で、手動で移動する必要はありません。

詳しくは、「[Azure Active Directory Terms of Use 機能](https://docs.microsoft.com/azure/active-directory/active-directory-tou)」を参照してください。

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication のデプロイに関する新しい包括的なガイダンス

**種類:** 新機能  
**サービス カテゴリ:** その他  
**製品の機能:** ID のセキュリティと保護
 
組織に Azure Multi-Factor Authentication (MFA) をデプロイする方法に関する新しいステップ バイ ステップ ガイドを公開しました。

MFA のデプロイ ガイドを表示するには、GitHub の [Identity Deployment Guides](https://aka.ms/DeploymentPlans) (ID デプロイ ガイド) リポジトリに移動します。 デプロイ ガイドに関するフィードバックを提出するには、[Deployment Plan Feedback (デプロイ プランに関するフィードバック) フォーム](https://aka.ms/deploymentplanfeedback)を使用してください。 デプロイ ガイドに関する質問がある場合は、[IDGitDeploy](mailto:idgitdeploy@microsoft.com) までお問い合わせください。

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>パブリック プレビューにある Azure AD の委任されたアプリ管理ロール

**種類:** 新機能  
**サービス カテゴリ:** エンタープライズ アプリ  
**製品の機能:** Access Control

管理者は、グローバル管理者ロールを割り当てずに、アプリ管理タスクを委任できるようになりました。 新しいロールと機能は次のとおりです。

- **新しい標準 Azure AD 管理者ロール:**

    - **アプリケーション管理者。** 登録、SSO 設定、アプリの割り当てとライセンス付与、アプリのプロキシ設定、および承認 (Azure AD リソースを除く) を含め、すべてのアプリの全側面を管理する権限が付与されます。

    - **クラウド アプリケーション管理者。** アプリ プロキシではオンプレミス アクセスを提供していないため、アプリ プロキシを除くすべてのアプリケーション管理者の権限が付与されます。

    - **アプリケーション開発者。** **[allow users to register apps]\(ユーザーによるアプリ登録を許可する\)** オプションが無効になっている場合でも、アプリ登録を作成する権限を付与します。

- **所有権 (アプリ登録ごと、および企業アプリごとに設定し、グループ所有権のプロセスとほぼ同じ):**
 
    - **アプリ登録の所有者。** アプリ マニフェストや別の所有者の追加など、所有するアプリ登録の全側面を管理する権限が付与されます。

    - **エンタープライズ アプリ所有者。** SSO 設定、アプリの割り当て、および承認 (Azure AD リソースを除く) を含め、所有するエンタープライズ アプリの多くの側面を管理する権限が付与されます。

パブリック プレビューの詳細については、ブログ「[Azure AD delegated application management roles are in public preview!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/)」(Azure AD の委任されたアプリケーション管理ロールをパブリック プレビューで確認しよう!)  を参照してください。 ロールとアクセス許可の詳細については、「[Azure Active Directory の管理者ロールの割り当て](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)」を参照してください。

---