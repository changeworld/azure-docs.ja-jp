---
title: "Azure Portal でアクティビティ レポートを確認する方法 | Microsoft Docs"
description: "Azure Portal でアクティビティ レポートを確認する方法の詳細"
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: d93521f8-dc21-4feb-aaff-4bb300f04812
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/01/2017
ms.author: dhanyahk;markvi
translationtype: Human Translation
ms.sourcegitcommit: 35323a56297c9aaf4939febd32140d6d101ee4e5
ms.openlocfilehash: 9cb98ad710d82bade25f99d171f49433aea1204c
ms.lasthandoff: 03/02/2017


---
# <a name="how-to-find-activity-reports-in-the-azure-portal"></a>Azure Portal でアクティビティ レポートを確認する方法

Azure クラシック ポータルから Azure Portal への移行に際して、Azure Active Directory のアクティビティ ログの外観が一新されました。 数か月前に、作業対象のリソースのコンテキスト別にアクティビティ ログが表示される仕様について説明する[ブログ投稿](https://blogs.technet.microsoft.com/enterprisemobility/2016/11/08/azuread-weve-just-turned-on-detailed-auditing-and-sign-in-logs-in-the-new-azure-portal/)を公開しました。 本記事では、Azure クラシック ポータルの既存のレポートが新たな Azure Portal でどのように変更されたのかを説明します。

## <a name="what-is-new"></a>変更点

Azure クラシック ポータルの各レポートは、次のカテゴリに分けられました。

1.    セキュリティ レポート
2.    アクティビティ レポート
3.    統合アプリケーション レポート

### <a name="activity-and-integrated-app-reports"></a>アクティビティ レポートと統合アプリケーション レポート

Azure Portal でのコンテキスト ベースのレポートへの移行に伴い、既存のレポートは、データの提供元になる&1; つの API を備えた単一のビューに統合されました。 

このビューは、**[Azure Active Directory]** ブレードの **[アクティビティ]** セクションの **[監査ログ]** にあります。


![監査ログ](./media/active-directory-reporting-migration/482.png "監査ログ")








このビューに統合されたレポートは以下の通りです。

-    監査レポート

-     パスワード リセット アクティビティ

-     パスワード リセット登録アクティビティ

-     セルフ サービス グループ アクティビティ

-     Office&365; グループ名の変更

-     アカウント プロビジョニングのアクティビティ

-     パスワード ロールオーバーの状態
-     アカウント プロビジョニング エラー


アプリケーションの使用状況レポートは強化され、**[サインイン]** という名前のビューに含まれました。 このビューは、**[Azure Active Directory]** ブレードの **[アクティビティ]** セクションにあります。


![監査ログ](./media/active-directory-reporting-migration/483.png "監査ログ")

このビューにはすべてのユーザーのサインインが含まれており、これをもとにアプリケーションの使用状況に関する情報を得ることができます。 アプリケーションの使用状況に関する情報は、**[エンタープライズ アプリケーション]** の概要から確認することもできます。

![監査ログ](./media/active-directory-reporting-migration/484.png "監査ログ")



## <a name="how-can-i-access-a-specific-report-in-this-single-view"></a>この単一ビューで特定のレポートにアクセスする方法

### <a name="audit-logs"></a>監査ログ

多くのお客様から寄せられた要望の&1; つが、Azure AD でアクティビティ ログにアクセスする複数のフィルター オプションを追加してほしいというものでした。 この代替手段として、必要なデータに絞り込むができる詳細なフィルター処理メカニズムが搭載されました。 こうしたフィルターの&1; つに**アクティビティのカテゴリ**と呼ばれるものがあり、このフィルターには Azure AD で提供されるさまざまな種類のアクティビティ ログが表示されています。 目的のカテゴリを選択することで、結果を表示したいものに絞り込むことができます。 

たとえば、**セルフサービスによるパスワードのリセット**関連のアクティビティのみを確認する場合は、**[Self-service Password Management] (セルフサービスによるパスワード管理)** カテゴリを選択してください。 表示されるカテゴリは、作業対象のリソースに応じて異なります。  


![監査ログ](./media/active-directory-reporting-migration/06.png "監査ログ")

 
現時点で含まれているカテゴリは次のとおりです。

- Core Directory (コア ディレクトリ)

- Self-service Password Management (セルフサービスによるパスワード管理)

- セルフサービスによるグループ管理

- アカウント プロビジョニング

### <a name="application-usage"></a>アプリケーションの使用状況

**[アクティビティ] > [サインイン]** ビューで、すべてのアプリケーションまたは単一のアプリケーションの使用状況を確認できます。 下図に示すように、このビューではすべてのアプリケーションか単一のアプリケーションが表示されます。 結果を絞り込む場合は、**[ユーザー名]** または**[アプリケーション名]** でフィルタリングできます。
 

![監査ログ](./media/active-directory-reporting-migration/07.png "監査ログ")


### <a name="security-reports"></a>セキュリティ レポート

Azure クラシック ポータルの **Azure AD 異常アクティビティ** セキュリティ レポートは、Azure Active Directory が検出して報告できるすべてのセキュリティ関連リスク イベントの中央ビューを提供できるように統合されました。 

次の表では、さまざまな **Azure AD 異常アクティビティ** セキュリティ レポート、および Azure Portal での相当するリスク イベントの種類を一覧表示しています。 

| Azure AD 異常アクティビティ レポート |  Identity Protection のリスク イベントの種類|
| :--- | :--- |
| 資格情報が漏洩したユーザー | 漏洩した資格情報 |
| 不規則なサインイン アクティビティ | 特殊な場所へのあり得ない移動 |
| 感染している可能性があるデバイスからのサインイン | 感染しているデバイスからのサインイン|
| 不明なソースからのサインイン | 匿名の IP アドレスからのサインイン |
| 不審なアクティビティのある IP アドレスからのサインイン | 不審なアクティビティのある IP アドレスからのサインイン |
| - | 未知の場所からのサインイン |

以下の Azure AD 異常アクティビティ セキュリティ レポートは Azure AD のリスク イベントには含まれません。 これらのレポートは Azure クラシック ポータルでまだ使用できますが、 ただし、これは将来的に廃止されます。

* 複数のエラー後のサインイン
* 複数の地域からのサインイン

詳細については、「[Azure Active Directory リスク イベント](active-directory-identity-protection-risk-events.md)」をご覧ください。  

Azure Portal では、**[Azure Active Directory]** ブレードの **[セキュリティ]** セクションで、検出されたリスク イベントに関するレポートにアクセスできます。 検出されたリスク イベントの追跡状況は、次のレポートで参照できます。   

- 危険な状態のユーザー
- リスクの高いサインイン 


![監査ログ](./media/active-directory-reporting-migration/04.png "監査ログ")

これらのレポートの詳細については、以下をご覧ください。

- [Azure Active Directory ポータルの危険な状態のユーザー セキュリティ レポート - プレビュー](active-directory-reporting-security-user-at-risk.md)
- [Azure Active Directory ポータルのリスクの高いサインイン レポート - プレビュー](active-directory-reporting-security-risky-sign-ins.md)






## <a name="activity-reports-in-azure-classic-portal-versus-azure-portal"></a>Azure クラシック ポータルと Azure Portal のアクティビティ レポートの違い

このセクションでは、Azure クラシック ポータルにある既存のレポートの一覧と、各レポートの情報を Azure Portal で確認する方法について説明します。


監査データへのエントリ ポイントはすべて、**[Azure Active Directory]** ブレードの **[アクティビティ]** セクションの **[監査ログ]** です。

![監査ログ](./media/active-directory-reporting-migration/61.png "監査ログ")


| Azure クラシック ポータル                 | Azure Portal の手順                                                         |
| ---                                  | ---                                                                        |
| Audit Logs                           | **アクティビティのカテゴリ**として **[コア ディレクトリ]** を選択します。                       |
| パスワード リセット アクティビティ              | **アクティビティのカテゴリ**として **[Self-service Password Management] (セルフサービスによるパスワード管理)** を選択します。 | 
| パスワード リセット登録アクティビティ | **アクティビティのカテゴリ**として **[Self-service Password Management] (セルフサービスによるパスワード管理)** を選択します。     |
| セルフ サービス グループ アクティビティ         | **アクティビティのカテゴリ**として **[セルフサービスによるグループ管理]** を選択します。        |
| アカウント プロビジョニングのアクティビティ        | **アクティビティのカテゴリ**として **[Account User Provisioning] (アカウント ユーザーのプロビジョニング)** を選択します。         |
| パスワード ロールオーバーの状態             | **アクティビティのカテゴリ**として、**[Automatic App Password Rollover] (アプリ パスワードの自動ロールオーバー)** を選択します。      |
| アカウント プロビジョニング エラー          | **アクティビティのカテゴリ**として **[Account User Provisioning] (アカウント ユーザーのプロビジョニング)** を選択します。        |
| Office&365; グループ名の変更         | **アクティビティのカテゴリ**として **[Self-service Password Management] (セルフサービスによるパスワード管理)**、**アクティビティのリソースの種類**として **[グループ]**、**アクティビティ ソース**として **[O365 groups] (O365 グループ)** を選択します。|

 

**[アプリケーションの使用状況]** レポートへのエントリ ポイントは、**[Azure Active Directory] > [エンタープライズ アプリケーション] > [サインイン]** です。 


![監査ログ](./media/active-directory-reporting-migration/199.png "監査ログ")


