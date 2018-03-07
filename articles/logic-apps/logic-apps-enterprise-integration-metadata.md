---
title: "統合アカウント アーティファクト メタデータの管理 - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps の統合アカウントからアーティファクト メタデータを追加または取得する"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 02/23/2018
ms.author: LADocs; padmavc
ms.openlocfilehash: 59cebb6c0b86f4e3c4e16a5b6d2ada7b3e7a44a2
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2018
---
# <a name="manage-artifact-metadata-in-integration-accounts-for-logic-apps"></a>ロジック アプリの統合アカウントでアーティファクト メタデータを管理する

統合アカウント内でアーティファクトのカスタム メタデータを定義し、ロジック アプリの実行時にそのメタデータを取得することができます。 たとえば、パートナー、契約、スキーマ、マップなどのアーティファクトに対してメタデータを指定することができます。これらすべてのアーティファクトには、キーと値のペアを使用してメタデータが保存されます。 

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>統合アカウント内のアーティファクトにメタデータを追加する

1. Azure Portal で、[統合アカウント](logic-apps-enterprise-integration-create-integration-account.md)を作成します。

2. 統合アカウントにアーティファクトを追加します (たとえば、[パートナー](logic-apps-enterprise-integration-partners.md)、[契約](logic-apps-enterprise-integration-agreements.md)、[スキーマ](logic-apps-enterprise-integration-schemas.md))。

3. アーティファクトを選択し、**[編集]** を選択して、メタデータの詳細を入力します。

   ![メタデータを入力する](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>ロジック アプリのアーティファクトからメタデータを取得する

1. Azure Portal で、[ロジック アプリ](quickstart-create-first-logic-app-workflow.md)を作成します。

2. [ロジック アプリから統合アカウントへのリンク](logic-apps-enterprise-integration-create-integration-account.md#link-an-integration-account-to-a-logic-app)を作成します。 

3. ロジック アプリ デザイナーで、ロジック アプリに**要求**や **HTTP** などのトリガーを追加します。

4. トリガーで、**[新しいステップ]** > **[アクションの追加]** の順に選びます。 "統合アカウント" を検索し、**[統合アカウント - 統合アカウントのアーティファクトの検索]** アクションを選びます。

   ![[統合アカウントのアーティファクトの検索] を選択する](media/logic-apps-enterprise-integration-metadata/image2.png)

5. **アーティファクトの種類**を選択し、**アーティファクトの名前**を指定します。 例: 

   ![アーティファクトの種類を選択し、アーティファクト名を指定する](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>例: パートナーのメタデータを取得する

このパートナーが、`routingUrl` の詳細を含むこのメタデータを持っているものとします。

![パートナーの "routingURL" メタデータを見つける](media/logic-apps-enterprise-integration-metadata/image6.png)

1. ロジック アプリで、トリガー、パートナーの **[統合アカウント - 統合アカウントのアーティファクトの検索]** アクション、および **HTTP** アクションを追加します。次はその例です。

   ![ロジック アプリに、トリガー、アーティファクト検索、および HTTP アクションを追加する](media/logic-apps-enterprise-integration-metadata/image4.png)

2. URI を取得するには、ロジック アプリ デザイナー ツールバーで、ロジック アプリの **[コード ビュー]** を選びます。 ロジック アプリの定義は、次の例のようになります。

   ![参照の検索](media/logic-apps-enterprise-integration-metadata/image5.png)

## <a name="next-steps"></a>次の手順

* [契約の詳細について学習する](logic-apps-enterprise-integration-agreements.md)
