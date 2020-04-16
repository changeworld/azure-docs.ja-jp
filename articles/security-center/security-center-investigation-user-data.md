---
title: Azure Security Center 調査で検出されたユーザー データの管理
description: " Azure Security Center 調査で検出されたユーザー データを管理する方法について説明します。 "
services: operations-management-suite
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: memildin
ms.openlocfilehash: aa262b0be3902f6e143a53f8f1302156fc5aede6
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582982"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Azure Security Center 調査で検出されたユーザー データの管理
この記事では、Azure Security Center 調査機能で検出されたユーザー データの管理方法を説明します。 調査データは [Azure Monitor ログ](../log-analytics/log-analytics-overview.md)に格納され、Azure Security Center で公開されます。 ユーザー データの管理には、データを削除したりエクスポートしたりする機能が含まれます。

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>個人データの検索と識別
Azure portal では、Azure Security Center の調査機能を使用して、個人データを検索できます。 調査機能は、 **[セキュリティ アラート]** の中にあります。

調査機能の **[エンティティ]** タブに、すべてのエンティティ、ユーザー情報、およびデータが表示されます。

## <a name="securing-and-controlling-access-to-personal-information"></a>個人情報のセキュリティ保護とアクセス制御
閲覧者、所有者、共同作成者、またはアカウント管理者のロールが割り当てられている Azure Security Center のユーザーは、ツール内の顧客データにアクセスできます。

閲覧者、所有者、共同作成者ロールの詳細については、「[Azure ロールベースのアクセス制御の組み込みロール](../role-based-access-control/built-in-roles.md)」を参照してください。 アカウント管理者ロールの詳細については、[Azure サブスクリプション管理者](../cost-management-billing/manage/add-change-subscription-administrator.md)に関する記事を参照してください。

## <a name="deleting-personal-data"></a>個人データの削除
閲覧者、所有者、共同作成者、またはアカウント管理者のロールが割り当てられている Azure Security Center のユーザーは、調査情報を削除できます。

調査を削除するには、Azure Resource Manager REST API へ `DELETE` 要求を送信できます。

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

`incidentName` 入力は、`GET` 要求を使用してすべてのインシデントを一覧表示することで見つけられます。

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>個人データのエクスポート
閲覧者、所有者、共同作成者、またはアカウント管理者のロールが割り当てられている Azure Security Center のユーザーは、調査情報をエクスポートできます。 調査情報をエクスポートするには、 **[エンティティ]** タブに移動して関連する情報をコピーして貼り付けます。

## <a name="next-steps"></a>次のステップ
ユーザー データの管理の詳細については、[Azure Security Center でのユーザー データの管理](security-center-privacy.md)に関する記事を参照してください。
Azure Monitor ログでのプライベート データの削除について詳しくは、「[プライベート データをエクスポートして削除する方法](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data)」を参照してください。
