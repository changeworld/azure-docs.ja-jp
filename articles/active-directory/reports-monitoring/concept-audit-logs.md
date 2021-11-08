---
title: Azure Active Directory の監査ログ | Microsoft Docs
description: Azure Active Directory の監査ログの概要。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/30/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: d551124491c33cd74f3fbdf1f4b401ada99399d4
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997174"
---
# <a name="audit-logs-in-azure-active-directory"></a>Azure Active Directory の監査ログ 

IT 管理者は、IT 環境がどのように動作しているかを知る必要があります。 システムの正常性に関する情報を使用すると、潜在的な問題に対処する必要があるかどうか、およびその方法を評価できます。 

この目標を達成するために、Azure Active Directory ポータルでは、次の 3 つのアクティビティ ログにアクセスできます。

- **[サインイン](concept-sign-ins.md)** - サインインとユーザーのリソース使用状況に関する情報。
- **[監査](concept-audit-logs.md)** - ユーザーやグループの管理、テナントのリソースに適用された更新など、テナントに適用された変更に関する情報。
- **[プロビジョニング](concept-provisioning-logs.md)** - ServiceNow でのグループの作成や、Workday からインポートされたユーザーなど、プロビジョニング サービスによって実行されるアクティビティ。

この記事では、監査ログの概要について説明します。


## <a name="what-is-it"></a>紹介

Azure AD の監査ログを使用すると、コンプライアンスのためのシステム アクティビティのレコードにアクセスできます。
このログの最も一般的なビューは、次のカテゴリに基づいています。

- [ユーザー管理]

- グループ管理
 
- アプリケーション管理  


ユーザー中心のビューを使用すると、次のような疑問に対する答えを得ることができます。

- どの種類の更新プログラムがユーザーによって適用されているか。

- 何人のユーザーが変更されたか。

- 何個のパスワードが変更されたか。

- 管理者がディレクトリで何を行ったか。


グループ中心のビューを使用すると、次のような疑問に対する答えを得ることができます。

- 追加されたのはどのグループか。

- メンバーシップが変更されたグループはあるか。

- グループの所有者は変更されたか。

- グループまたはユーザーにどのライセンスが割り当てられているか。

アプリケーション中心のビューを使用すると、次のような疑問に対する答えを得ることができます。

- どのアプリケーションが追加または更新されたか。

- どのアプリケーションが削除されたか。

- アプリケーションのサービス プリンシパルは変更されたか。

- アプリケーションの名前は変更されたか。

- アプリケーションに同意したのはだれか。

 
## <a name="what-license-do-i-need"></a>必要なライセンス

監査アクティビティ レポートは、Azure AD のすべてのエディションで使用できます。

## <a name="who-can-access-it"></a>だれがアクセスできるのか。

監査ログにアクセスするには、次のいずれかのロールである必要があります。 

- セキュリティ管理者
- セキュリティ閲覧者
- レポート閲覧者
- グローバル閲覧者
- グローバル管理者

## <a name="where-can-i-find-it"></a>どこで入手できますか。

Azure portal でログにアクセスする方法はいくつかあります。 たとえば、[Azure Active Directory] メニューの **[監視]** セクションでログを開けます。  

![監査ログを開く](./media/concept-audit-logs/audit-logs-menu.png)

さらに、[このリンク](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/ProvisioningEvents)を使用して、監査ログに直接アクセスできます。


Microsoft Graph API を使用して、監査ログにアクセスすることもできます。


## <a name="what-is-the-default-view"></a>既定の表示項目

監査ログには、次のものを示す既定のリスト ビューがあります。

- 発生の日付と時刻
- 発生をログに記録したサービス
- アクティビティの名前とカテゴリ (*何か* を指す) 
- アクティビティの状況 (成功または失敗)
- ターゲット
- アクティビティのイニシエーターまたはアクター ("だれが" を指す)

![監査ログ](./media/concept-audit-logs/listview.png "監査ログ")

リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。

![列の監査](./media/concept-audit-logs/columns.png "列の監査")

これで、追加のフィールドの表示または既に表示されているフィールドの削除ができます。

![フィールドの削除](./media/concept-audit-logs/columnselect.png "フィールドの削除")

詳細な情報を取得するには、リスト ビューで項目を選択します。

![項目の選択](./media/concept-audit-logs/details.png "項目の選択")

## <a name="filtering-audit-logs"></a>監査ログのフィルター処理

次のフィールドで監査データをフィルター処理できます。

- サービス
- カテゴリ
- アクティビティ
- Status
- 移行先
- 開始者 (アクター)
- 期間

![オブジェクトのフィルタリング](./media/concept-audit-logs/filter.png "Filter オブジェクト")

**[サービス]** フィルターでは、次のサービスのドロップダウン リストから選択できます。

- All
- AAD 管理 UX
- アクセス レビュー
- アカウント プロビジョニング
- アプリケーション プロキシ
- 認証方法
- B2C
- 条件付きアクセス
- Core Directory (コア ディレクトリ)
- エンタイトルメント管理
- ハイブリッド認証
- Identity Protection
- 招待されたユーザー
- MIM サービス
- MyApps
- PIM
- セルフサービスによるグループ管理
- Self-service Password Management \(セルフサービスによるパスワード管理)
- 使用条件

**[カテゴリ]** フィルターでは、次のフィルターのいずれかを選択できます。

- All
- AdministrativeUnit
- ApplicationManagement
- 認証
- 承認
- Contact
- Device
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- KerberosDomain
- KeyManagement
- Label
- その他
- PermissionGrantPolicy
- ポリシー
- ResourceManagement
- RoleManagement
- UserManagement

**[アクティビティ]** フィルターは、カテゴリとアクティビティ リソースの種類の選択に基づいたものです。 参照する特定のアクティビティを選択することも、すべてを選択することもできます。 

Graph API を使用して、すべての監査アクティビティの一覧を取得できます。`https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

**[状態]** フィルターでは、監査操作の状態に基づいてフィルターに掛けることができます。 状態は、次のいずれかになります。

- All
- Success
- 障害

**[ターゲット]** フィルターでは、名前またはユーザー プリンシパル名 (UPN) の先頭で特定のターゲットを検索できます。 ターゲット名と UPN では大文字小文字を区別します。 

**[開始者]** フィルターでは、開始者の名前またはユニバーサル プリンシパル名 (UPN) の先頭を定義できます。 名前と UPN では大文字小文字を区別します。

**[期間]** フィルターでは、返されるデータの期間を定義できます。  
次のいずれかの値になります。

- 7 日
- 24 時間
- Custom

カスタムの期間を選択すると、開始時刻と終了時刻を構成できます。

また、 **[ダウンロード]** ボタンを選択して、フィルターされたデータ (最大 250,000 個のレコード) をダウンロードすることもできます。 CSV 形式または JSON 形式でログをダウンロードできます。 ダウンロードできるレコードの数は、[Azure Active Directory レポートの保持ポリシー](reference-reports-data-retention.md)によって制限されます。

![データをダウンロードする](./media/concept-audit-logs/download.png "データをダウンロードする")



## <a name="microsoft-365-activity-logs"></a>Microsoft 365 のアクティビティ ログ

Microsoft 365 のアクティビティ ログは、[Microsoft 365 管理センター](/office365/admin/admin-overview/about-the-admin-center)から確認できます。 Microsoft 365 のアクティビティ ログと Azure AD のアクティビティ ログでは多くのディレクトリ リソースが共有されていますが、Microsoft 365 のアクティビティ ログがすべて表示されるのは、Microsoft 365 管理センターのみです。 

また、[Office 365 Management API](/office/office-365-management-api/office-365-management-apis-overview) を使用すると、Microsoft 365 のアクティビティ ログにプログラムでアクセスすることもできます。

> [!NOTE]
> ほとんどのスタンドアロンまたはバンドルされた Microsoft 365 サブスクリプションには、Microsoft 365 データセンターの境界内の一部のサブシステムへのバックエンド依存関係があります。 この依存関係には、ディレクトリの同期を維持するため、および基本的に Exchange Online のサブスクリプション オプトインで手間のかからないオンボーディングを有効にできるようにするために情報の書き戻しが必要です。 これらの書き戻しの場合、監査ログ エントリには、"Microsoft Substrate Management" によって実行されたアクションが表示されます。 これらの監査ログ エントリでは、Exchange Online によって Azure AD に対して実行された作成、更新、削除の各操作を参照します。 エントリは情報を提供するものであり、アクションは必要ありません。

## <a name="next-steps"></a>次のステップ

- [Azure AD 監査アクティビティのリファレンス](reference-audit-activities.md)
- [Azure AD ログの保有のリファレンス](reference-reports-data-retention.md)
- [Azure AD ログの待機時間のリファレンス](reference-reports-latencies.md)
- [監査レポートの不明なアクター](/troubleshoot/azure/active-directory/unknown-actors-in-audit-reports)
