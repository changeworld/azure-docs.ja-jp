---
title: "新機能 Azure Active Directory のリリース ノート | Microsoft Docs"
description: "Azure Active Directory (Azure AD) の 最新のリリース、既知の問題、バグの修正、非推奨になった機能、変更の計画を常に把握するために、これらのリリース ノートをご利用ください。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 1d98233e1bfc2c9a926d7c61737286ec6e4fc422
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory の新着情報

マイクロソフトは、Azure Active Directory の改善を継続的に行っています。 お客様に常に最新の開発情報を入手していただけるよう、このトピックでは以下に関する情報を提供します。

-   最新のリリース 
-   既知の問題 
-   バグの修正 
-   非推奨になった機能 
-   変更の計画 

このページは月単位で更新されるため、定期的にご確認ください。


## <a name="october-2017"></a>2017 年 10 月

**タイプ:** 変更の計画  
**サービス カテゴリ:** レポート  
**製品の機能:** ID ライフサイクル管理  


**`https://graph.windows.net/<tenant-name>/reports/` ノードでの Azure AD レポート (ベータ版) API の非推奨化**

Azure Portal では次の機能が提供されています。

- 新しい Azure Active Directory 管理コンソール 
- アクティビティ レポートとセキュリティ レポートのための新しい API
 
これらの新機能の導入に伴い、**/reports** エンドポイントのレポート API は 2017 年 12 月 10 日に廃止されます。 

---

**タイプ:** 固定   
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** SSO  


Azure Active Directory は、HTML ユーザー名とパスワード フィールドをレンダリングするアプリケーションのサインイン フィールドの自動検出をサポートしています。  この手順は「[アプリケーションのサインイン フィールドを自動的にキャプチャする方法](application-config-sso-problem-configure-password-sso-non-gallery.md#how-to-manually-capture-sign-in-fields-for-an-application)」に示されています。 この機能は、[Azure Portal](http://aad.portal.azure.com) の **[エンタープライズ アプリケーション]** ページで*ギャラリー以外のアプリケーション*を追加することで表示されます。 さらに、Web URL を入力してページを保存することにより、この新しいアプリケーションの **シングル サインオン** モードを**パスワード ベースのシングル サインオン**を実行するように構成できます。
 
サービスの問題により、この機能はしばらくの間、一時的に無効化されていました。 問題が解決されたため、サインイン フィールドの自動検出が再び利用可能になりました。

---

**タイプ:** 新機能  
**サービス カテゴリ:** MFA  
**製品の機能:** ID のセキュリティと保護  


現代の世界において、多要素認証 (MFA) は組織を保護するための重要な要素となっています。 マイクロソフトの ID チームは、資格情報の適応性を高め、よりシームレスなエクスペリエンスを提供するために多要素認証を進化させています。 ここでは、その取り組みにおける重要な 2 つの進歩をご紹介します。 

- 多要素認証結果の、Azure AD サインイン レポートへの直接統合 (プログラムによる MFA へのアクセスを含む)

- MFA 構成の、Azure Portal のコア Azure AD 構成エクスペリエンスへのより緊密な統合

このパブリック プレビューでは、MFA の管理およびレポート機能がコア Azure AD 構成エクスペリエンスの一部として統合されているため、MFA 管理ポータルの機能を Azure AD エクスペリエンス内で管理できます。

詳しくは、「[Azure Portal の多要素認証レポートの参照情報](active-directory-reporting-activity-sign-ins-mfa.md)」をご覧ください。 


---
**タイプ:** 新機能  
**サービス カテゴリ:** 使用条件  
**製品の機能:** ガバナンス  


**Azure AD Terms of Use** は、エンド ユーザーに情報を提示するための簡単な方法を提供します。 これにより、ユーザーは法律またはコンプライアンスの要件に関する免責事項を確認できます。

Azure AD Terms of Use は、次のシナリオで使用できます。

- 組織内のすべてのユーザーに対する一般的な使用条件。 

- ユーザーの属性に基づく特定の使用条件 (例:  動的グループによって行われる、医師と看護師や、国内従業員と国際従業員などの区別)。 

- ビジネスへの影響が大きいアプリ (Salesforce など) へのアクセスのための特定の使用条件。

詳細については、[Azure Active Directory Terms of Use](active-directory-tou.md)に関する記事をご覧ください。


---
**タイプ:** 新機能  
**サービス カテゴリ:** PIM  
**製品の機能:** Privileged Identity Management  


組織内の Azure リソース (プレビュー) に対するアクセス権が、Azure Active Directory Privileged Identity Management (PIM) で管理、制御、監視できるようになりました。 対象には、サブスクリプション、リソース グループ、さらに仮想マシンも含まれます。 Azure ロールベースのアクセス制御 (RBAC) 機能を活用する Azure Portal 内のすべてのリソースが、Azure AD PIM から提供される優れたセキュリティとライフサイクル管理機能、そして間もなく Azure AD ロールに提供される強力な新機能の一部を活用できます。

詳細については、「[Azure リソースの PIM](privileged-identity-management/azure-pim-resource-rbac.md)」をご覧ください。


---
**タイプ:** 新機能  
**サービス カテゴリ:** アクセス レビュー  
**製品の機能:** ガバナンス  


組織でアクセス レビュー (プレビュー) を使うことにより、グループ メンバーシップおよびエンタープライズ アプリケーションへのアクセスを、効率的に管理できます。 

- アプリケーションへのアクセスおよびグループのメンバーシップのアクセス レビューを使うと、ゲスト ユーザーによるアクセスを認定できます。 アクセス レビューによって提供される洞察を使用して、ゲストが引き続きアクセスする必要があるかどうかを効率的に決定できます。

- アクセス レビューを使って従業員のアプリケーション アクセスとグループ メンバーシップを認定できます。

アクセス レビューの制御を組織に関連するプログラムに集めて、コンプライアンスまたはリスクを受けやすいアプリケーションのレビューを追跡できます。

詳細については、「[Azure AD アクセス レビュー](active-directory-azure-ad-controls-access-reviews-overview.md)」をご覧ください。


---
**タイプ:** 新機能  
**サービス カテゴリ:** マイ アプリ  
**製品の機能:** SSO  


**マイ アプリと Office 365 ランチャーからサード パーティ製アプリケーションを非表示にする機能**

新しい**アプリの非表示**プロパティにより、ユーザー ポータルに表示されるアプリを管理しやすくなりました。 これは、バックエンド サービスのタイルや重複したタイルの表示によって、ユーザーのアプリ ランチャーが雑然としている場合に役立ちます。 切り替えボタンはサードパーティ製アプリのプロパティ セクションにあり、**[ユーザーに表示しますか?]** というラベルが付いています。 PowerShell を介してプログラムでアプリを非表示にすることもできます。 

詳細については、「[Azure Active Directory でユーザーのエクスペリエンスからサードパーティ製アプリケーションを非表示にする](active-directory-coreapps-hide-third-party-app.md)」をご覧ください。 


**利用可能な機能**

 新しい管理コンソールへの移行の一環として、Azure AD アクティビティ ログを取得するための新しい 2 つの API が作成されました。 新しい API のセットは、より豊富な監査およびサインイン アクティビティに加えて、より豊富なフィルターおよび並べ替え機能を提供します。 以前はセキュリティ レポートを通じて提供されていたデータに、Microsoft Graph の Identity Protection リスク イベント API を通じてアクセスできます。


## <a name="september-2017"></a>2017 年 9 月

**タイプ:** 変更された機能  
**サービス カテゴリ:** Microsoft Identity Manager  
**製品の機能:** ID ライフサイクル管理  


2017 年 9 月 25 日より、Microsoft Identity Manager (MIM) 2016 2016 Service Pack 1 (SP1) の修正プログラム ロールアップ パッケージ (ビルド 4.4.1642.0) の提供が開始されました。 このロールアップ パッケージの説明は以下のとおりです。

- 問題を解決し、機能強化を追加します。
- Microsoft Identity Manager 2016 のビルド 4.4.1459.0 までのすべての MIM 2016 SP1 更新プログラムに代わる累積的な更新プログラムです。 
- **Microsoft Identity Manager 2016 ビルド 4.4.1302.0** がインストールされている必要があります。 

詳細については、「[Hotfix rollup package (build 4.4.1642.0) is available for Microsoft Identity Manager 2016 SP1](https://support.microsoft.com/en-us/help/4021562)」(Microsoft Identity Manager 2016 SP1 の修正プログラム ロールアップ パッケージ (ビルド 4.4.1642.0) の提供開始) をご覧ください。 

---