---
title: Azure Active Directory (プレビュー) の Log Analytics ビューをインストールして使用する方法 |Microsoft Docs
description: Azure Active Directory (プレビュー) の Log Analytics ビューをインストールして使用する方法について説明します。
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 7f3db8b16e275ff130cc3d8cb162780d595a7f32
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624643"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Azure Active Directory 用の Log Analytics ビューのインストールと使用

Azure Active Directory の Log Analytics ビューを使用して、お使いの Azure AD テナントで Azure AD アクティビティ ログを分析して検索できます。 Azure AD アクティビティ ログには、次のログが含まれます。

* [監査ログ](concept-audit-logs.md): テナント内で実行されたすべてのタスクの履歴は、監査ログ アクティビティ レポートで把握できます。
* サインイン ログ: 監査ログによって報告されたタスクをだれが実行したかは、[サインイン アクティビティ レポート](concept-sign-ins.md)で判断することができます。

## <a name="prerequisites"></a>前提条件

Log Analytics ビューを使用するには、次の準備が必要です。

* Azure サブスクリプションの Log Analytics ワークスペース。 [Log Analytics ワークスペースの作成方法](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace)を確認してください。
* 最初に、[Azure AD アクティビティ ログを Log Analytics ワークスペースにルーティングする](howto-integrate-activity-logs-with-log-analytics.md)手順を完了する。
* [GitHub リポジトリ](https://aka.ms/AADLogAnalyticsviews)からローカル コンピューターに、ビューをダウンロードする。

## <a name="install-the-log-analytics-views"></a>Log Analytics ビューのインストール

1. Log Analytics ワークスペースに移動します。 これを行うには、最初に [Azure portal](https://portal.azure.com) に移動して、**[All services]\(すべてのサービス\)** を選択します。 テキスト ボックスに「**Log Analytics**」と入力して、**[Log Analytics]** を選択します。 前提条件の中で、アクティビティ ログをルーティングしたワークスペースを選択します。
2. **[ビュー デザイナー]** を選択し、**[インポート]** してから、**[ファイルの選択]** を選択して、ローカル コンピューターからビューをインポートします。
3. 前提条件からダウンロードしたビューを選択し、**[保存]** を選択してインポートを保存します。 **[Azure AD Account Provisioning Events]\(Azure AD アカウントのプロビジョニング イベント\)** ビューと **[Sign-ins Events]\(サインイン イベント\)** ビューに対しても同じ操作を実行します。

## <a name="use-the-views"></a>ビューの使用

1. Log Analytics ワークスペースに移動します。 これを行うには、最初に [Azure portal](https://portal.azure.com) に移動して、**[All services]\(すべてのサービス\)** を選択します。 テキスト ボックスに「**Log Analytics**」と入力して、**[Log Analytics]** を選択します。 前提条件の中で、アクティビティ ログをルーティングしたワークスペースを選択します。

2. ワークスペースに移動して、**[ワークスペースの概要]** を選択します。 次の 3 つのビューを確認する必要があります。

    * **[Azure AD Account Provisioning Events]\(Azure AD アカウントのプロビジョニング イベント\)**: プロビジョニングされた新しいユーザーとプロビジョニング エラーの数、更新されたユーザーと更新エラーの数、プロビジョニング解除されたユーザーと該当するエラーの数など、プロビジョニング アクティビティの監査に関連するレポートを表示します。    
    * **[Sign-ins Events]\(サインイン イベント\)**: このビューは、経時的にサインイン数を追跡する概要ビューだけでなく、アプリケーション、ユーザー、デバイスごとのサインインなど、サインイン アクティビティの監視に関連する最も重要なレポートを表示します。

3. これらのいずれかのビューを選択して、個々のレポートに進みます。 また、任意のレポート パラメーターにアラートを設定することも可能です。 たとえば、サインイン エラーが発生するたびに、アラートを設定するとします。 これを行うには、まず、**[Sign-ins Events]\(サインイン イベント\)** ビューを選択し、**[Sign-in errors over time]\(経時的なサインイン エラー\)** レポートを選択し、**[Analytics]** を選択して、レポートの背景となる実際のクエリを示した詳細ページを開きます。 

    ![詳細](./media/howto-install-use-log-analytics-views/details.png)


4. **[アラートの設定]** を選択して、**[アラートの条件]** セクションで **[Whenever the Custom log search]\(次の場合に、カスタム ログ検索を行う: \) に &lt;ロジックが定義されていません&gt;** を選択します。 サインイン エラーが発生するたびにアラートを生成したいので、既定のアラート ロジックの **[しきい値]** に **1** を設定してから、**[完了]** を選択します。 

    ![シグナル ロジックの構成](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. アラートの名前と説明を入力して、重大度を **[警告]** に設定します。

    ![ルールを作成する](./media/howto-install-use-log-analytics-views/create-rule.png)

6. アラートに対するアクション グループを選択します。 これは一般に、、電子メールまたはテキスト メッセージ経由で通知したいチームか、Webhook、Runbook、関数、ロジック アプリ、または外部の ITSM ソリューションを使用して自動化されたタスクになります。 [Azure portal でアクション グループを作成および管理する方法](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)について確認してください。

7. **[アラート ルールの作成]** を選択して、アラートを作成します。 これで、サインイン エラーが発生するたびに、アラート通知が行われるようになりました。

## <a name="next-steps"></a>次の手順

* [Log Analytics でアクティビティ ログを分析する方法](howto-analyze-activity-logs-log-analytics.md)
* [Azure portal で Log Analytics の使用を開始する](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)
