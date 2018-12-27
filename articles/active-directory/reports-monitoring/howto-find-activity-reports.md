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
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: fab94088d1d54012a955b0663b078d03b13d6299
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624914"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Azure Portal でアクティビティ レポートを見つける

この記事では、Azure portal で Azure Active Directory (Azure AD) のユーザー アクティビティ レポートを見つける方法について説明します。

## <a name="audit-logs-report"></a>監査ログ レポート

監査ログ レポートは、アプリケーション アクティビティに関する複数のレポートをコンテキストベースのレポートのために単一のビューに結合したものです。 監査ログ レポートにアクセスするには:

1. [Azure Portal](https://portal.azure.com) に移動します。
2. 右上からディレクトリを選択し、左側のナビゲーション ウィンドウで **[Azure Active Directory]** ブレードを選択します。
3. Azure Active Directory ブレードの **[アクティビティ]** セクションから **[監査ログ]** を選択します。 

    ![監査ログ](./media/howto-find-activity-reports/482.png "監査ログ")

監査ログ レポートは、次のレポートを統合しています。

* 監査レポート
* パスワード リセット アクティビティ
* パスワード リセット登録アクティビティ
* セルフ サービス グループ アクティビティ
* Office 365 グループ名の変更
* アカウント プロビジョニングのアクティビティ
* パスワード ロールオーバーの状態
* アカウント プロビジョニング エラー

### <a name="filtering-on-audit-logs"></a>監査ログのフィルター処理

監査レポートで高度なフィルター処理を使用すると、**[アクティビティ カテゴリ]** フィルターで指定することで、特定のカテゴリの監査データにアクセスできます。 たとえば、セルフサービスによるパスワードのリセットに関連するすべてのアクティビティを表示するには、**[Self-service password management]\(セルフ サービスによるパスワード管理\)** カテゴリを選択します。 

    ![Category options on the Filter Audit Logs page](./media/howto-find-activity-reports/06.png "Category options on the Filter Audit Logs page")

アクティビティのカテゴリには、以下が含まれます。

- Core Directory (コア ディレクトリ)
- Self-service Password Management \(セルフサービスによるパスワード管理)
- セルフサービスによるグループ管理
- アカウント プロビジョニング


## <a name="sign-ins-report"></a>サインイン レポート 

**[サインイン]** ビューには、すべてのユーザー サインインと、**アプリケーションの使用状況**レポートが表示されます。 アプリケーションの使用状況に関する情報は、**[エンタープライズ アプリケーション]** の概要の **[管理]** セクションで確認することもできます。

    ![Enterprise applications](./media/howto-find-activity-reports/484.png "Enterprise applications")

サインイン レポートにアクセスするには:

1. [Azure Portal](https://portal.azure.com) に移動します。
2. 右上からディレクトリを選択し、左側のナビゲーション ウィンドウで **[Azure Active Directory]** ブレードを選択します。
3. Azure Active Directory ブレードの **[アクティビティ]** セクションから **[サインイン]** を選択します。 

    ![[サインイン] ビュー](./media/howto-find-activity-reports/483.png "[サインイン] ビュー")


### <a name="filtering-on-application-name"></a>アプリケーション名のフィルター処理

サインイン レポートを使用すると、ユーザー名またはアプリケーション名をフィルター処理して、アプリケーションの使用状況の詳細を表示できます。

![[サインイン イベントのフィルター処理] ページ](./media/howto-find-activity-reports/07.png "[サインイン イベントのフィルター処理] ページ")

## <a name="security-reports"></a>セキュリティ レポート

### <a name="anomalous-activity-reports"></a>異常アクティビティ レポート

異常アクティビティ レポートは、Azure AD で検出および報告されるセキュリティ関連のリスク イベントに関する情報を提供します。

次の表は、Azure AD 異常アクティビティ セキュリティ レポートと、各レポートに対応する Azure Portal のリスク イベントの種類を示しています。 詳細については、「[Azure Active Directory リスク イベント](concept-risk-events.md)」を参照してください。  


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


### <a name="detected-risk-events"></a>検出されたリスク イベント

[Azure portal](https://portal.azure.com) では、**[Azure Active Directory]** ブレードの **[セキュリティ]** セクションで、検出されたリスク イベントに関するレポートにアクセスできます。 検出されたリスク イベントは、次のレポートで追跡されます。   

- [危険な状態のユーザー](concept-user-at-risk.md)
- [リスクの高いサインイン](concept-risky-sign-ins.md)

    ![セキュリティ レポート](./media/howto-find-activity-reports/04.png "セキュリティ レポート")

## <a name="next-steps"></a>次の手順

* [監査ログの概要](concept-audit-logs.md)
* [サインインの概要](concept-sign-ins.md)
* [危険なイベントの概要](concept-risk-events.md)
