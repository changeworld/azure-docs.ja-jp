---
title: サービス プロバイダーのアクティビティを表示する
description: お客様は、ログに記録されたアクティビティを表示して、Azure の委任されたリソース管理を通じてサービス プロバイダーによって実行されるアクションを確認できます。
ms.date: 12/6/2019
ms.topic: conceptual
ms.openlocfilehash: 69517e942aa9f82be16fa3d0e7d6f9252de44d4c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932248"
---
# <a name="view-service-provider-activity"></a>サービス プロバイダーのアクティビティを表示する

Azure の委任されたリソース管理に委任サブスクリプションのあるお客様は、[Azure アクティビティ ログ](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) データを表示して、実行されたすべてのアクションを確認できます。 これによりお客様は、Azure の委任されたリソース管理を通じてサービス プロバイダーが実行している操作と、お客様独自の Azure Active Directory (Azure AD) テナント内のユーザーが実行している操作を完全に可視化できます。

## <a name="view-activity-log-data"></a>アクティビティ ログ データを表示する

[アクティビティ ログ](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview#view-the-activity-log)は、Azure portal の **[モニター]** メニューから表示することができます。 特定のサブスクリプションに対して結果を制限するには、フィルターを使用して特定のサブスクリプションを選択します。 また、プログラムで[アクティビティ ログ イベントを表示および取得する](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)こともできます。

> [!NOTE]
> Azure の委任されたリソース管理にお客様のテナント内の委任サブスクリプションがオンボードされたときに、サービス プロバイダーのテナント内のユーザーに[閲覧者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)ロール (または、閲覧者アクセスを含む別の組み込みロール) が付与されている場合、これらのユーザーは、そのサブスクリプションに対するアクティビティ ログの結果を表示することができます。

アクティビティ ログには、操作の名前とその状態が、実行された日付および時刻と共に表示されます。 **[イベント開始者]** 列には、操作を実行したユーザー (Azure の委任されたリソース管理を通じて操作しているサービス プロバイダーのテナント内のユーザーと、お客様独自のテナント内のユーザーのどちらの場合も) が表示されます。 テナント、またはそのサブスクリプションについてユーザーに割り当てられているロールではなく、ユーザーの名前が表示されることに注意してください。

ログに記録されたアクティビティは、過去 90 日間について Azure portal に格納されます。 このデータを 90 日間より長く格納する方法については、「[Azure Monitor の Log Analytics ワークスペースで Azure アクティビティ ログを収集して分析する](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect)」を参照してください。

## <a name="set-alerts-for-critical-operations"></a>重要な操作のアラートを設定する

サービス プロバイダー (またはお客様独自のテナント内のユーザー) が実行している重要な操作を把握するには、[アクティビティ ログ アラート](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-alerts)を作成することをお勧めします。 たとえば、サブスクリプションに対するすべての管理操作を追跡したり、特定のリソース グループ内の仮想マシンが削除されたときに通知を受け取ったりすることができます。 アラートを作成すると、お客様独自のテナント内のユーザー、およびすべての管理テナント内のユーザーが実行するアクションがアラートに含まれます。

詳細については、[アクティビティ ログ アラートの作成と管理](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)に関するページを参照してください。

## <a name="create-log-queries"></a>ログ クエリを作成する

クエリを作成すると、ログに記録されたアクティビティを分析したり、特定のアイテムに焦点を当てたりすることができます。 たとえば、監査で、サブスクリプションに対して実行されるすべての管理レベルのアクションに関するレポートを作成することが必要になる場合があります。 これらのアクションのみをフィルター処理するクエリを作成し、結果をユーザー、日付、または別の値で並べ替えることができます。

詳細については、「[Azure Monitor のログ クエリの概要](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)」を参照してください。

## <a name="next-steps"></a>次の手順

- 詳細については、「[Azure Monitor の概要](https://docs.microsoft.com/azure/azure-monitor/)」を参照してください。
- Azure portal で[サービス プロバイダーのサービスを表示および管理する](view-manage-service-providers.md)方法について説明します。