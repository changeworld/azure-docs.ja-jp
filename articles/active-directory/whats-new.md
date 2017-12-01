---
title: "新機能 Azure Active Directory のリリース ノート | Microsoft Docs"
description: "最新のリリース ノート、既知の問題、バグの修正、非推奨になった機能、予定されている変更点を含む、Azure Active Directory (Azure AD) の新着情報について説明します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
featureFlags: clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/22/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9de535b2fb70181c68fb698e847dd8361bf54385
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2017
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory の新着情報




> お気に入りの RSS フィード リーダーでこの[フィード](https://docs.microsoft.com/api/search/rss?search=%22what%27s%20new%20in%20azure%20active%20directory%3F%22&locale=en-us)をサブスクライブして、Azure Active Directory の新着情報を常に把握するようにしてください。



マイクロソフトは、Azure Active Directory の改善を継続的に行っています。 お客様に常に最新の開発情報を入手していただけるよう、このトピックでは以下に関する情報を提供します。

-   最新のリリース 
-   既知の問題 
-   バグの修正 
-   非推奨になった機能 
-   変更の計画 

このページは月単位で更新されるため、定期的にご確認ください。

## <a name="november-2017"></a>2017 年 11 月
 


**タイプ:** 変更の計画  
**サービス カテゴリ:** ACS  
**製品の機能:** Access Control Service 

**ACS の廃止**

Microsoft Azure Active Directory Access Control (Access Control Service または ACS とも呼ばれます) は 2018 年後半に廃止されます。  詳しいスケジュールや大まかな移行ガイダンスなどの詳細については、数週間以内にお知らせします。 この間に、ACS に関する質問があればこのページにコメントを投稿してください。Microsoft のチームのメンバーが回答します。

---



**タイプ:** 変更の計画  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護


**ブラウザー アクセスを Intune Managed Browser に限定する** 


この動作により、Intune Managed Browser を承認されたアプリとして使用して、Office 365 やその他の Azure AD 接続クラウド アプリへのブラウザー アクセスを制限できるようになります。 

この変更では、アプリケーションベースの条件付きアクセスで以下の条件を設定できるようになります。

**クライアント アプリ:** ブラウザー

**この変更の影響**

現在、この条件を使用する場合アクセスはブロックされます。 この動作のプレビューが利用可能になると、すべてのアクセスで Managed Browser アプリケーションの使用が必須になります。 

この機能の詳細については、今度のブログおよびリリース ノートをお待ちください。 

詳細については、「[Azure Active Directory の条件付きアクセス](active-directory-conditional-access-azure-portal.md)」を参照してください。

 
---


 
**タイプ:** 変更の計画  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護


**Azure AD アプリベースの条件付きアクセス向けの承認されたクライアント アプリの新規追加**


以下のアプリが、[承認されたクライアント アプリ](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)の一覧に追加される予定です。

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)

- [Microsoft StaffHub](https://staffhub.office.com/what-it-is)


詳細については、次を参照してください。

- [承認されたクライアント アプリケーションの要件](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Azure Active Directory のアプリベースの条件付きアクセス](active-directory-conditional-access-mam.md)


---


**タイプ:** 新機能    
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス/コンプライアンス



**使用条件での多言語のサポート**


管理者は、PDF ドキュメントを複数含めた新しい使用条件 (TOU) を作成できるようになりました。 これらの PDF ドキュメントは、対応する言語にタグ付けできます。 スコープに該当するユーザーには、PDF はユーザー設定に基づいて一致する言語で表示されます。 一致する言語がない場合は、既定の言語が表示されます。


---
 


**タイプ:** 新機能  
**サービス カテゴリ:** SSPR  
**製品の機能:** ユーザー認証


**パスワード ライトバック クライアントのリアルタイムの状態**
 

オンプレミスのパスワード ライトバック クライアントの状態を確認できるようになりました。 このオプションは、**[[パスワードのリセット]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)** ページの **[オンプレミスの統合]** セクションで使用できます。 

オンプレミスのライトバック クライアントへの接続で問題が生じた場合、以下の内容のエラー メッセージが表示されます。

- オンプレミスのライトバック クライアントへ接続できない理由の詳細 
- 問題の解決に役立つドキュメントへのリンク 


詳細については、「[オンプレミスの統合](active-directory-passwords-how-it-works.md#on-premises-integration)」を参照してください。

 
---
 
**タイプ:** 新機能  
**サービス カテゴリ:** Azure AD  
**製品の機能:** ID のセキュリティと保護



**Azure AD のアプリベースの条件付きアクセス** 


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



**タイプ:** 新機能  
**サービス カテゴリ:** デバイスの登録と管理  
**製品の機能:** ID のセキュリティと保護

 

**Azure Portal での Azure AD デバイスの管理**


Azure AD に接続されているすべてのデバイスと、それらのデバイス関連のアクティビティを 1 か所で確認できるようになりました。 Azure Portal にすべてのデバイスの ID と設定を管理できる新しい管理機能が追加されました。 このリリースでは以下の操作を行うことができます。

- Azure AD の条件付きアクセスで使用可能なすべてのデバイスを表示する

- プロパティを表示する (ハイブリッド Azure AD 参加済みデバイスも含む)

- Azure AD 参加済みデバイスの BitLocker キーを確認し Intune などでデバイスを管理する

- Azure AD デバイス関連の設定を管理する


詳細については、「[Azure Portal によるデバイスの管理](device-management-azure-portal.md)」を参照してください。



 
---


**タイプ:** 新機能    
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護 



**Azure AD の条件付きアクセス用デバイス プラットフォームでの macOS のサポート** 
 

Azure AD 条件付きアクセス ポリシーのデバイス プラットフォームの条件に macOS を追加 (または除外) できるようになりました。 サポートされるデバイス プラットフォームに macOS が追加されたことで、以下の操作が可能になりました。

- **Intune を使用して macOS デバイスを登録および管理する** - iOS や Android などの他のプラットフォームと同様に、macOS でも登録を統合するポータル サイト アプリケーションを使用できるようになりました。 この macOS 用の新しいポータル サイト アプリでは、Intune および Azure AD へデバイスを登録できます。
 
- **Intune で定義されている組織のコンプライアンス ポリシーを macOS デバイスに遵守させる** - Azure Portal の Intune で、macOS デバイス向けのコンプライアンス ポリシーを設定できるようになりました。 
  
- **Azure AD 内のアプリケーションへのアクセスを準拠 macOS デバイスのみに制限する** - 条件付きアクセス ポリシーの作成で、macOS が個別のデバイス プラットフォーム オプションとして追加されました。 このオプションを使用すると、Azure で設定済みのターゲット アプリケーション向けに macOS 固有の条件付きアクセス ポリシーを作成できます。

詳細については、次を参照してください。

- [Intune で macOS デバイス用のデバイス コンプライアンス ポリシーを作成する](https://aka.ms/macoscompliancepolicy)
- [Azure Active Directory の条件付きアクセス](active-directory-conditional-access-azure-portal.md)


 
---


**タイプ:** 新機能    
**サービス カテゴリ:** MFA  
**製品の機能:** ユーザー認証


**Azure MFA の NPS 拡張機能** 


Azure MFA のネットワーク ポリシー サーバー (NPS) 拡張機能は、既存のサーバーを使用してクラウド ベースの MFA 機能を認証インフラストラクチャに追加します。 NPS 拡張機能を使用すると、新しいサーバーをインストール、構成、管理することなく、電話、テキスト メッセージ、またはモバイル アプリによる検証を既存の認証フローに追加できます。 

この拡張機能は、Azure MFA Server をデプロイしないで VPN 接続を保護する必要のある組織を対象に作成されました。 NPS 拡張機能は、RADIUS とクラウド ベース Azure MFA の間のアダプターとして機能し、フェデレーション ユーザーまたは同期済みユーザーに、認証の 2 番目の要素を提供します。


詳細については、「[Azure Multi-Factor Authentication と既存の NPS インフラストラクチャの統合](../multi-factor-authentication/multi-factor-authentication-nps-extension.md)」を参照してください。

 
---


**タイプ:** 新機能    
**サービス カテゴリ:** ユーザー管理  
**製品の機能:** ディレクトリ 


**削除済みユーザーの復元または完全な削除**


Azure AD 管理センターで以下の操作が可能になりました。

- 削除済みユーザーの復元 
- ユーザーの完全な削除 


**実行方法:**

1. Azure AD 管理センターの **[管理]** セクションで [**[すべてのユーザー]**](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All users) を選択します。 

2. **[表示]** リストで **[最近削除されたユーザー]** を選択します。 

4. 最近削除されたユーザーを 1 名または複数選択して、復元するか完全に削除します。

 
---



 
**タイプ:** 変更された機能  
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護

**Azure AD アプリベースの条件付きアクセス向けの承認されたクライアント アプリの新規追加**


以下のアプリが、[承認されたクライアント アプリ](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)の一覧に追加されました。

- Microsoft Planner

- Microsoft Azure Information Protection 


詳細については、次を参照してください。

- [承認されたクライアント アプリケーションの要件](active-directory-conditional-access-technical-reference.md#approved-client-app-requirement)

- [Azure Active Directory のアプリベースの条件付きアクセス](active-directory-conditional-access-mam.md)


---



**タイプ:** 変更された機能    
**サービス カテゴリ:** 条件付きアクセス  
**製品の機能:** ID のセキュリティと保護


**条件付きアクセス ポリシーのコントロールの "OR" 結合機能** 
 
条件付きアクセス コントロールを "OR" で結合する (選択したコントロールのいずれかを必須にする) 機能がリリースされました。 この機能を使用すると、アクセス コントロールを **OR** で結合してポリシーを作成することができます。 たとえば、この機能を使用して、多要素認証を使用したサインイン**または**準拠デバイスの使用をユーザーに求めるポリシーを作成できます。

詳細については、「[Azure Active Directory の条件付きアクセスのコントロール](active-directory-conditional-access-controls.md)」を参照してください。

 
---



**タイプ:** 変更された機能    
**サービス カテゴリ:** Identity Protection  
**製品の機能:** ID のセキュリティと保護

**リアルタイムのリスク イベントの集計**

管理能力の向上のため、Azure AD Identity Protection では、指定日に同一の IP アドレスで発生したすべてのリアルタイム リスク イベントが、リスク イベントの種類ごとに集計されるようになりました。 この変更により表示されるリスク イベントの量が制限されますが、ユーザーのセキュリティが変更されることはありません。

根本となるリアルタイム検出は、ユーザーのログインのたびに行われます。 多要素認証またはアクセスのブロックを行うサインインのリスク セキュリティ ポリシーを設定をしている場合、このポリシーは引き続きリスクのあるサインインのたびにトリガーされます。

 
---
 




## <a name="october-2017"></a>2017 年 10 月

**タイプ:** 変更の計画  
**サービス カテゴリ:** レポート  
**製品の機能:** ID ライフサイクル管理  


**Azure AD レポートの廃止**

Azure Portal では次の機能が提供されています。

- 新しい Azure Active Directory 管理コンソール 
- アクティビティ レポートとセキュリティ レポートのための新しい API
 
これらの新機能の導入に伴い、**/reports** エンドポイントのレポート API は 2017 年 12 月 10 日に廃止されます。 

---

**タイプ:** 固定   
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** SSO  


**サインイン フィールドの自動検出**


Azure Active Directory は、HTML ユーザー名とパスワード フィールドをレンダリングするアプリケーションのサインイン フィールドの自動検出をサポートしています。  この手順は「[アプリケーションのサインイン フィールドを自動的にキャプチャする方法](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application)」に示されています。 この機能は、[Azure Portal](http://aad.portal.azure.com) の **[エンタープライズ アプリケーション]** ページで*ギャラリー以外のアプリケーション*を追加することで表示されます。 さらに、Web URL を入力してページを保存することにより、この新しいアプリケーションの **シングル サインオン** モードを**パスワード ベースのシングル サインオン**を実行するように構成できます。
 
サービスの問題により、この機能はしばらくの間、一時的に無効化されていました。 問題が解決されたため、サインイン フィールドの自動検出が再び利用可能になりました。

---

**タイプ:** 新機能  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティと保護  


**MFA の新機能**

多要素認証 (MFA) は、組織を保護する上で欠かせない要素です。 資格情報の適応性を高めて多要素認証をよりシームレスに利用できるようにするため、以下の機能が追加されました。 

- 多要素認証結果の、Azure AD サインイン レポートへの直接統合 (プログラムによる MFA へのアクセスを含む)

- Azure Portal の Azure AD 構成エクスペリエンスと MFA 構成の統合の緊密化

このパブリック プレビューでは、MFA の管理およびレポートが、コアとなる Azure AD の構成エクスペリエンスに組み込まれました。 両方の機能の集約により、Azure AD を使用しながら MFA の管理ポータル機能を管理できます。

詳しくは、「[Azure Portal の多要素認証レポートの参照情報](active-directory-reporting-activity-sign-ins-mfa.md)」をご覧ください。 


---
**タイプ:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス  


**使用条件の導入**

Azure AD Terms of Use では、エンド ユーザーに対して簡単な方法で情報を提示できます。 これにより、ユーザーは法律またはコンプライアンスの要件に関する免責事項を確認できます。

Azure AD Terms of Use は、次のシナリオで使用できます。

- 組織内のすべてのユーザーに対する一般的な使用条件。 

- ユーザーの属性に基づく特定の使用条件 (例:  動的グループによって行われる、医師と看護師や、国内従業員と国際従業員などの区別)。 

- ビジネスへの影響が大きいアプリ (Salesforce など) へのアクセスのための特定の使用条件。

詳細については、[Azure Active Directory Terms of Use](active-directory-tou.md)に関する記事をご覧ください。


---
**タイプ:** 新機能  
**サービス カテゴリ:** PIM  
**製品の機能:** Privileged Identity Management  

**Privileged Identity Management の機能強化**

Azure Active Directory Privileged Identity Management (PIM) で、組織内の以下の Azure リソース (プレビュー) へのアクセスを管理、制御、監視できるようになりました。

- サブスクリプション
- リソース グループ
- 仮想マシン 

Azure のロール ベースのアクセス制御 (RBAC) 機能を利用する Azure Portal 内のすべてのリソースで、Azure AD PIM から提供されるセキュリティおよびライフサイクルの管理機能を使用できます。

詳細については、「[Azure リソースの PIM](privileged-identity-management/azure-pim-resource-rbac.md)」をご覧ください。


---
**タイプ:** 新機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** ガバナンス  

**アクセス レビューの導入**


組織でアクセス レビュー (プレビュー) を使うことにより、グループ メンバーシップおよびエンタープライズ アプリケーションへのアクセスを、効率的に管理できます。 

- アプリケーションへのアクセスおよびグループのメンバーシップのアクセス レビューを使うと、ゲスト ユーザーによるアクセスを認定できます。 アクセス レビューによって提供される洞察を使用して、ゲストが引き続きアクセスする必要があるかどうかを効率的に決定できます。

- アクセス レビューを使って従業員のアプリケーション アクセスとグループ メンバーシップを認定できます。

アクセス レビューの制御を組織に関連するプログラムに集めて、コンプライアンスまたはリスクを受けやすいアプリケーションのレビューを追跡できます。

詳細については、「[Azure AD アクセス レビュー](active-directory-azure-ad-controls-access-reviews-overview.md)」をご覧ください。


---
**タイプ:** 新機能  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** SSO  


**マイ アプリおよび Office 365 ランチャーでサード パーティ製アプリケーションを非表示にする**

新しい**アプリの非表示**プロパティにより、ユーザー ポータルに表示されるアプリを管理しやすくなりました。 アプリの非表示は、アプリ タイルにバックエンド サービスのタイルや重複したタイルが表示され、ユーザーのアプリ ランチャーが雑然となっている場合に役立ちます。 切り替えボタンはサード パーティ製アプリの [プロパティ] セクションにあり、**[ユーザーに表示しますか?]** というラベルが付いています。 PowerShell を介してプログラムでアプリを非表示にすることもできます。 

詳細については、「[Azure Active Directory でユーザーのエクスペリエンスからサードパーティ製アプリケーションを非表示にする](active-directory-coreapps-hide-third-party-app.md)」をご覧ください。 


**利用可能な機能**

 新しい管理コンソールへの移行の一環として、Azure AD のアクティビティ ログを取得する新しい 2 つの API が利用可能になりました。 この新しい API セットでは監査およびサインイン アクティビティが追加されているだけでなく、より充実したフィルター機能と並べ替え機能も備わっています。 以前はセキュリティ レポートを通じて提供されていたデータに、Microsoft Graph の Identity Protection リスク イベント API を通じてアクセスできます。


## <a name="september-2017"></a>2017 年 9 月

**タイプ:** 変更された機能  
**サービス カテゴリ:** Microsoft Identity Manager  
**製品の機能:** ID ライフサイクル管理  


**Microsoft Identity Manager の修正プログラム**

2017 年 9 月 25 日より、Microsoft Identity Manager (MIM) 2016 2016 Service Pack 1 (SP1) の修正プログラム ロールアップ パッケージ (ビルド 4.4.1642.0) の提供が開始されました。 このロールアップ パッケージの説明は以下のとおりです。

- 問題を解決し、機能強化を追加します。
- Microsoft Identity Manager 2016 のビルド 4.4.1459.0 までのすべての MIM 2016 SP1 更新プログラムに代わる累積的な更新プログラムです。 
- **Microsoft Identity Manager 2016 ビルド 4.4.1302.0** がインストールされている必要があります。 

詳細については、「[Hotfix rollup package (build 4.4.1642.0) is available for Microsoft Identity Manager 2016 SP1](https://support.microsoft.com/en-us/help/4021562)」(Microsoft Identity Manager 2016 SP1 の修正プログラム ロールアップ パッケージ (ビルド 4.4.1642.0) の提供開始) をご覧ください。 

---
