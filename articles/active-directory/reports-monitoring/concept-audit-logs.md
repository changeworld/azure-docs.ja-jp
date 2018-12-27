---
title: Azure Active Directory ポータルの監査アクティビティ レポート | Microsoft Docs
description: Azure Active Directory ポータルの監査アクティビティ レポートの概要
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 883f521040c67cb8fe9578bc5c490bc3dfccba28
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624660"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Azure Active Directory ポータルの監査アクティビティ レポート 

環境の動作状況を判断するために必要な情報は、Azure Active Directory (Azure AD) レポートで入手できます。

このレポート アーキテクチャは、次のコンポーネントで構成されます。

- **アクティビティ** 
    - **サインイン** - [サインイン レポート](concept-sign-ins.md)は、マネージド アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報を提供します。
    - **監査ログ** - Azure AD 内のさまざまな機能によって行われたすべての変更についてログによる追跡可能性を提供します。 監査ログの例として、ユーザー、アプリ、グループ、ロール、ポリシーの追加や削除など、Azure AD 内のあらゆるリソースに加えられた変更があります。
- **セキュリティ** 
    - **リスクの高いサインイン** - [リスクの高いサインイン](concept-risky-sign-ins.md)は、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。 
    - **リスクのフラグ付きユーザー** - [リスクの高いユーザー](concept-user-at-risk.md)は、侵害された可能性があるユーザー アカウントの指標です。

この記事では、監査レポートの概要について説明します。
 
## <a name="who-can-access-the-data"></a>誰がデータにアクセスできますか。

* **セキュリティ管理者**、**セキュリティ閲覧者**、**グローバル管理者**のいずれかのロールであるユーザー。
* さらに、すべてのユーザー (管理者以外) は、独自の監査アクティビティを表示できます

## <a name="audit-logs"></a>監査ログ

Azure AD の監査ログは、コンプライアンスのためにシステム アクティビティのレコードを提供します。 監査レポートにアクセスするには、**Azure Active Directory** の **[アクティビティ]** セクションで **[監査ログ]** を選択します。 

![監査ログ](./media/concept-audit-logs/61.png "監査ログ")

監査ログには、次のものを示す既定のリスト ビューがあります。

- 発生の日付と時刻
- アクティビティの開始者またはアクター ("*だれが*" を指す) 
- アクティビティ ("*どうした*" を指す) 
- ターゲット

![監査ログ](./media/concept-audit-logs/18.png "監査ログ")

リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。

![監査ログ](./media/concept-audit-logs/19.png "監査ログ")

これで、追加のフィールドの表示または既に表示されているフィールドの削除ができます。

![監査ログ](./media/concept-audit-logs/21.png "監査ログ")

詳細な情報を取得するには、リスト ビューで項目を選択します。

![監査ログ](./media/concept-audit-logs/22.png "監査ログ")


## <a name="filtering-audit-logs"></a>監査ログのフィルター処理

次のフィールドで監査データをフィルター処理できます。

- 期間
- 開始者 (アクター)
- Category
- アクティビティのリソースの種類
- アクティビティ

![監査ログ](./media/concept-audit-logs/23.png "監査ログ")

**[期間]** フィルターでは、返されるデータの期間を定義できます。  
次のいずれかの値になります。

- 1 か月
- 7 日
- 24 時間
- カスタム

カスタムの期間を選択すると、開始時刻と終了時刻を構成できます。

**[開始者]** フィルターでは、開始者の名前またはそのユニバーサル プリンシパル名 (UPN) を定義できます。

**[カテゴリ]** フィルターでは、次のフィルターのいずれかを選択できます。

- All
- コア カテゴリ
- コア ディレクトリ
- セルフサービスによるパスワード管理
- セルフサービスのグループ管理
- アカウント プロビジョニング - 自動パスワード ロールオーバー
- 招待されたユーザー
- MIM サービス
- Identity Protection
- B2C

**[アクティビティのリソースの種類]** フィルターでは、次のフィルターのいずれかを選択できます。

- All 
- グループ
- Directory
- User
- アプリケーション
- ポリシー
- Device
- その他

**[アクティビティのリソースの種類]** として **[グループ]** が選択されている場合、次の**ソース**も指定できる追加のフィルター カテゴリが示されます。

- Azure AD
- O365


**[アクティビティ]** フィルターは、カテゴリとアクティビティ リソースの種類の選択に基づいたものです。 参照する特定のアクティビティを選択することも、すべてを選択することもできます。 

Graph API https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta を使用して全監査アクティビティのリストを取得できます ($tenantdomain は実際のドメイン名)。または、[監査レポートのイベント](reference-audit-activities.md)に関する記事を参照してください。

## <a name="audit-logs-shortcuts"></a>監査ログのショートカット

**[Azure Active Directory]** の他にも、Azure Portal には監査データに対するエントリ ポイントが 2 つ用意されています。

- ユーザーとグループ
- エンタープライズ アプリケーション

### <a name="users-and-groups-audit-logs"></a>ユーザーとグループの監査ログ

ユーザーとグループ ベースの監査レポートを使用すると、次のような疑問に対する答えを得ることができます。

- どの種類の更新プログラムがユーザーによって適用されているか。

- 何人のユーザーが変更されたか。

- 何個のパスワードが変更されたか。

- 管理者がディレクトリで何を行ったか。

- 追加されたのはどのグループか。

- メンバーシップが変更されたグループはあるか。

- グループの所有者は変更されたか。

- グループまたはユーザーにどのライセンスが割り当てられているか。

ユーザーとグループに関連する監査データだけを確認する場合は、**[ユーザーとグループ]** の **[アクティビティ]** セクションの **[監査ログ]** に、フィルター処理されたビューがあります。 このエントリ ポイントには、**アクティビティのリソースの種類**として、**[ユーザーとグループ]** があらかじめ選択されています。

![監査ログ](./media/concept-audit-logs/93.png "監査ログ")

### <a name="enterprise-applications-audit-logs"></a>エンタープライズ アプリケーションの監査ログ

アプリケーション ベースの監査レポートを使用すると、次のような疑問に対する答えを得ることができます。

* どのアプリケーションが追加または更新されたか。
* どのアプリケーションが削除されたか。
* アプリケーションのサービス プリンシパルは変更されたか。
* アプリケーションの名前は変更されたか。
* アプリケーションに同意したのはだれか。

お使いのアプリケーションに関連する監査データを確認する場合は、**[エンタープライズ アプリケーション]** ブレードの **[アクティビティ]** セクションの **[監査ログ]** に、フィルター処理されたビューがあります。 このエントリ ポイントには、**[アクティビティのリソースの種類]** として **[エンタープライズ アプリケーション]** があらかじめ選択されています。

![監査ログ](./media/concept-audit-logs/134.png "監査ログ")

このビューを**グループ**または**ユーザー**に絞り込むことができます。

![監査ログ](./media/concept-audit-logs/25.png "監査ログ")


## <a name="next-steps"></a>次の手順

- [Azure AD 監査アクティビティのリファレンス](reference-audit-activities.md)
- [Azure AD レポートの保持のリファレンス](reference-reports-data-retention.md)
- [Azure AD ログの待機時間のリファレンス](reference-reports-latencies.md)