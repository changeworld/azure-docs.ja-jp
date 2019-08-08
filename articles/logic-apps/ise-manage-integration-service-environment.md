---
title: Azure Logic Apps で統合サービス環境を管理する
description: ネットワーク正常性を確認し、Azure Logic Apps の統合サービス環境 (ISE) のロジック アプリ、接続、カスタム コネクタ、および統合アカウントを管理します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 8f10e3d3fd7c67d1e803e8f85c9918c91bb81d59
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517360"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Azure Logic Apps で統合サービス環境 (ISE) を管理する

[統合サービス環境 (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) のネットワーク正常性を確認し、ISE に存在するロジック アプリ、接続、統合アカウント、カスタム コネクタを管理するには、このトピックの手順に従います。

## <a name="view-your-ise"></a>ISE を表示する

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. ポータルの検索ボックスに「統合サービス環境」と入力し、 **[統合サービス環境]** を選択します。

   ![統合サービス環境を見つける](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. 結果リストで [統合サービス環境] を選択します。

   ![統合サービス環境を選択する](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. 次のセクションに進み、ISE のロジック アプリ、接続、コネクタ、または統合アカウントを見つけます。

<a name="check-network-health"></a>

## <a name="check-network-health"></a>ネットワーク正常性を確認する

ISE メニューの **[設定]** で、 **[ネットワーク正常性]** を選択します。 このウィンドウには、サブネットの正常性状態と、他のサービスに対する送信依存関係が表示されます。

![ネットワーク正常性を確認する](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>ロジック アプリを管理する

1. ISE メニューの **[設定]** で、 **[ロジック アプリ]** を選択します。

   ![ロジック アプリを見つける](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. 不要になったロジック アプリを ISE から削除するには、該当するロジック アプリを選択し、 **[削除]** を選択します。

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>API 接続を管理する

1. ISE で実行しているロジック アプリによって作成された API 接続を表示するには、ISE メニューの **[設定]** で **[API 接続]** を選択します。

   ![API 接続を見つける](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. 不要になった接続を ISE から削除するには、該当する接続を選択し、 **[削除]** を選択します。

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>カスタム コネクタを管理する

1. ISE で作成されたカスタム コネクタを表示するには、ISE メニューの **[設定]** で **[カスタム コネクタ]** を選択します。

   ![カスタム コネクタを見つける](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. 不要になったカスタム コネクタを ISE から削除するには、該当するコネクタを選択し、 **[削除]** を選択します。

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>統合アカウントを管理する

1. ISE メニューの **[設定]** で、 **[統合アカウント]** を選択します。

   ![統合アカウントの検索](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. 不要になった統合アカウントを ISE から削除するには、該当する統合アカウントを選択し、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

* [分離されたロジック アプリから Azure Virtual Network に接続する](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)方法を理解する
