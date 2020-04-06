---
title: インクルード ファイル
description: インクルード ファイル
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 03/30/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: aacf1c287dca01b5993d35b311eed22e88ef7e87
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421158"
---
これらのサンプルでは、Azure Monitor を使用して、Azure の委任されたリソース管理用にオンボードされたサブスクリプションのアラートを作成する方法を示します。

| **テンプレート** | **説明** |
|---------|---------|
| [monitor-delegation-changes](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/monitor-delegation-changes) | 管理テナント内の過去 1 日間のアクティビティを照会し、[追加または削除された委任](../articles/lighthouse/how-to/monitor-delegation-changes.md) (または、成功しなかった試行) があればレポートします。|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | このテンプレートは、Azure アラートを作成し、既存のアクション グループに接続します。|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Kusto クエリに基づいてログ警告を複数作成します。|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | ユーザーが管理テナントにサブスクリプションを委任するときに、テナントにアラートをデプロイします。|
