---
title: "企業間 (B2B) メッセージのパートナーを作成する - Azure Logic Apps | Microsoft Docs"
description: "Enterprise Integration Pack と Logic Apps を使用してパートナーを統合アカウントに追加する方法について説明します"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 89066ba062c2b243136a03a52144fd99ae87eddc
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2018
---
# <a name="add-or-update-partners-in-business-to-business-agreements-in-your-workflow"></a>ワークフロー内の企業間契約のパートナーを追加または更新する

パートナーとは、企業間 (B2B) のトランザクションとメッセージング交換に参加するエンティティです。 これらのトランザクションで自身の組織と別の組織を表すパートナーを作成するには、まず交換するメッセージを識別して検証するための情報を共有する必要があります。 これらの詳細について話し合い、取引関係を開始する準備が整ったら、統合アカウントに両者を表すパートナーを作成できます。

## <a name="what-roles-do-partners-play-in-your-integration-account"></a>統合アカウントでのパートナーの役割

パートナー間で交換するメッセージの詳細を定義するには、それらのパートナー間の契約を作成します。 ただし、契約を作成する前に、少なくとも 2 つのパートナーを統合アカウントに追加しておく必要があります。 自身の組織は、契約の**ホスト パートナー**にする必要があります。 もう一方の組織、すなわち**ゲスト パートナー**は、自身の組織とメッセージを交換する組織です。 ゲスト パートナーには、別の会社のほか、自身の組織内の部署も指定できます。

これらのパートナーを追加したら、契約を作成できます。

受信と送信の設定は、ホスト パートナー側から見た方向になります。 たとえば、契約の受信設定は、ホスト パートナーがゲスト パートナーからメッセージを受信する方法を決めるものです。 同様に、契約の送信設定は、ホスト パートナーがゲスト パートナーにメッセージを送信する方法を示すものです。

## <a name="create-partner"></a>パートナーの作成

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. Azure のメイン メニューで、**[すべてのサービス]** を選びます。 検索ボックスに「統合」と入力し、"**統合アカウント**" を選びます。

   ![統合アカウントの検索](./media/logic-apps-enterprise-integration-partners/account-1.png)

3. **[統合アカウント]** で、パートナーを追加する統合アカウントを選びます。

   ![統合アカウントを選択する](./media/logic-apps-enterprise-integration-partners/account-2.png)

4. **[パートナー]** タイルを選びます。

   !["パートナー" を選ぶ](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. **[パートナー]** で **[追加]** を選びます。

   ![[追加] の選択](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. パートナーの名前を入力してから、**[Qualifier (修飾子)]** を選択します。 アプリが受け取るドキュメントを示す **[値]** を入力します。 完了したら、**[OK]** を選びます。

   ![パートナーの詳細を追加する](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. **[パートナー]** タイルを再び選びます。

   ![[パートナー] タイルを選ぶ](./media/logic-apps-enterprise-integration-partners/partner-5.png)

   新しいパートナーが表示されます。 

   ![新しいパートナーを表示する](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="edit-partner"></a>パートナーを編集する

1. [Azure ポータル](https://portal.azure.com)で、統合アカウントを検索して選択します。 **[パートナー]** タイルを選びます。

   ![[パートナー] タイルを選ぶ](./media/logic-apps-enterprise-integration-partners/edit.png)

2. **[パートナー]** で、編集するパートナーを選びます。

   ![削除するパートナーを選ぶ](./media/logic-apps-enterprise-integration-partners/edit-1.png)

3. **[パートナーの更新]** で、必要な変更を行います。
完了したら、**[保存]** を選びます。 

   ![変更して保存する](./media/logic-apps-enterprise-integration-partners/edit-2.png)

   変更を取り消すには、**[破棄]** を選びます。

## <a name="delete-partner"></a>パートナーを削除する

1. [Azure ポータル](https://portal.azure.com)で、統合アカウントを検索して選択します。 **[パートナー]** タイルを選びます。

   ![[パートナー] タイルを選ぶ](./media/logic-apps-enterprise-integration-partners/delete.png)

2. **[パートナー]** で、削除するパートナーを選びます。
**[削除]** を選択します。

   ![パートナーを削除する](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>次の手順

* [契約についての詳細情報](../logic-apps/logic-apps-enterprise-integration-agreements.md "Enterprise Integration の契約についての詳細情報")  

