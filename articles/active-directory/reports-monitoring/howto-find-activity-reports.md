---
title: Azure Portal で Azure Active Directory のユーザー アクティビティ レポートを見つける | Microsoft Docs
description: Azure Portal で Azure Active Directory のアクティビティ レポートがある場所について説明します。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: report-monitor
ms.date: 12/06/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 182537d6f07b624f2395f591681ed4596579bde0
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "42139893"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Azure Portal でアクティビティ レポートを見つける

この記事では、Azure Portal で Azure Active Directory のユーザー アクティビティ レポートを見つける方法について説明します。

## <a name="activity-and-integrated-app-reports"></a>アクティビティ レポートと統合アプリケーション レポート

Azure Portal のコンテキストに基づくレポートでは、既存のレポートは 1 つのビューに統合されています。 このビューのデータは、単一の API によって提供されます。

このビューを表示するには、**[Azure Active Directory]** ブレードで、**[アクティビティ]** の下の **[監査ログ]** を選択します。

![監査ログ](./media/howto-find-activity-reports/482.png "監査ログ")

このビューには、次のレポートが統合されています。

* 監査レポート
* パスワード リセット アクティビティ
* パスワード リセット登録アクティビティ
* セルフ サービス グループ アクティビティ
* Office 365 グループ名の変更
* アカウント プロビジョニングのアクティビティ
* パスワード ロールオーバーの状態
* アカウント プロビジョニング エラー


アプリケーションの使用状況レポートは強化され、**[サインイン]** ビューに表示されるようになりました。 このビューを表示するには、**[Azure Active Directory]** ブレードで、**[アクティビティ]** の下の **[サインイン]** を選択します。

![[サインイン] ビュー](./media/howto-find-activity-reports/483.png "[サインイン] ビュー")

**[サインイン]** ビューには、すべてのユーザーのサインインが表示されます。この情報を使用して、アプリケーションの使用状況に関する情報を取得できます。 アプリケーションの使用状況に関する情報は、**[管理]** セクションの **[エンタープライズ アプリケーション]** で概要を確認することもできます。

![[エンタープライズ アプリケーション]](./media/howto-find-activity-reports/484.png "[エンタープライズ アプリケーション]")

## <a name="access-a-specific-report"></a>特定のレポートへのアクセス

Azure Portal では単一のビューが用意されていますが、特定のレポートを表示することもできます。

### <a name="audit-logs"></a>監査ログ

ユーザーからのフィードバックに応じて、Azure Portal では、高度なフィルター処理を使用して目的のデータにアクセスできます。 使用できる 1 つのフィルターは*アクティビティのカテゴリ*です。これは、Azure AD のさまざまな種類のアクティビティ ログを一覧表示します。 結果を絞り込むために、カテゴリを選択することができます。

たとえば、セルフサービスによるパスワードのリセットに関連するアクティビティのみを確認する場合は、**[Self-service Password Management (セルフサービスによるパスワード管理)]** カテゴリを選択できます。 表示されるカテゴリは、作業対象のリソースに応じて異なります。  

![[監査ログのフィルター] ページの [カテゴリ] オプション](./media/howto-find-activity-reports/06.png "[監査ログのフィルター] ページの [カテゴリ] オプション")

アクティビティのカテゴリには、以下が含まれます。

- Core Directory (コア ディレクトリ)
- Self-service Password Management \(セルフサービスによるパスワード管理)
- セルフサービスによるグループ管理
- アカウント プロビジョニング

### <a name="application-usage"></a>アプリケーションの使用状況

すべてのアプリまたは 1 つのアプリの使用状況に関する詳細を表示するには、**[アクティビティ]** の下の **[サインイン]** を選択します。結果を絞り込むために、ユーザー名またはアプリケーション名でフィルター処理できます。

![[サインイン イベントのフィルター処理] ページ](./media/howto-find-activity-reports/07.png "[サインイン イベントのフィルター処理] ページ")

### <a name="security-reports"></a>セキュリティ レポート

#### <a name="azure-ad-anomalous-activity-reports"></a>Azure AD 異常アクティビティ レポート

Azure AD の異常なアクティビティに関するセキュリティレポートは、1 つの中心となるビューに統合されています。 このビューには、Azure AD が検出して報告できるセキュリティ関連のリスク イベントがすべて表示されます。

次の表は、Azure AD 異常アクティビティ セキュリティ レポートと、各レポートに対応する Azure Portal のリスク イベントの種類を示しています。

| Azure AD 異常アクティビティ レポート |  Identity Protection のリスク イベントの種類|
| :--- | :--- |
| 資格情報が漏洩したユーザー | 漏洩した資格情報 |
| 不規則なサインイン アクティビティ | 特殊な場所へのあり得ない移動 |
| 感染している可能性があるデバイスからのサインイン | 感染しているデバイスからのサインイン|
| 不明なソースからのサインイン | 匿名の IP アドレスからのサインイン |
| 不審なアクティビティのある IP アドレスからのサインイン | 不審なアクティビティのある IP アドレスからのサインイン |
| - | 未知の場所からのサインイン |

次の Azure AD 異常アクティビティ セキュリティ レポートは、Azure Portal ではリスク イベントに含まれません。

* 複数のエラー後のサインイン
* 複数の地域からのサインイン

詳細については、「[Azure Active Directory リスク イベント](concept-risk-events.md)」を参照してください。  


#### <a name="detected-risk-events"></a>検出されたリスク イベント

Azure Portal では、**[Azure Active Directory]** ブレードの **[セキュリティ]** セクションで、検出されたリスク イベントに関するレポートにアクセスできます。 検出されたリスク イベントは、次のレポートで追跡されます。   

- 危険な状態のユーザー
- リスクの高いサインイン

![セキュリティ レポート](./media/howto-find-activity-reports/04.png "セキュリティ レポート")

セキュリティ レポートの詳細については、次のトピックを参照してください。

- [Azure Active Directory ポータルの危険な状態のユーザー セキュリティ レポート](concept-user-at-risk.md)
- [Azure Active Directory ポータルのリスクの高いサインイン レポート](concept-risky-sign-ins.md)


**アプリケーションの使用状況**レポートに表示するには、**[Azure Active Directory]** ブレードで、**[管理]** の下の **[エンタープライズ アプリケーション]** を選択し、**[サインイン]** を選択します。


![エンタープライズ アプリケーションのサインイン レポート](./media/howto-find-activity-reports/199.png)

## <a name="next-steps"></a>次の手順

レポートの概要については、「[Azure Active Directory レポート](overview-reports.md)」を参照してください。
