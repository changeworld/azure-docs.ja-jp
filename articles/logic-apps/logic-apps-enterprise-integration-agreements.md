---
title: B2B 通信の契約 - Azure Logic Apps | Microsoft Docs
description: パートナーが Azure Logic Apps と Enterprise Integration Pack を使用する B2B シナリオで通信できるようにする契約を作成します
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: jeconnoc
editor: cgronlun
ms.assetid: 447ffb8e-3e91-4403-872b-2f496495899d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: LADocs
ms.openlocfilehash: df41a57b5b6e364ff537a28c939f362b4d399e7a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298727"
---
# <a name="partner-agreements-for-b2b-communication-with-azure-logic-apps-and-enterprise-integration-pack"></a>Azure Logic Apps および Enterprise Integration PackPartner を使用する B2B 通信用のパートナー契約

契約は、ビジネス エンティティが業界標準のプロトコルを使用してシームレスに通信できるよにする、企業間 (B2B) 通信に必要不可欠なものです。 Logic Apps と Enterprise Integration Pack を使用する B2B シナリオを有効にする場合、契約は B2B 取引先間の通信契約になります。 この契約は、パートナーが確立したいと考えている通信に基づいており、プロトコルまたはトランスポートに固有のものです。

エンタープライズ統合では、次のプロトコル/トランスポート標準がサポートされます。

* [AS2](logic-apps-enterprise-integration-as2.md)
* [X12](logic-apps-enterprise-integration-x12.md)
* [EDIFACT](logic-apps-enterprise-integration-edifact.md)

## <a name="why-use-agreements"></a>契約を使用する理由

契約を使用した場合のいくつかの一般的な利点を次に示します。

* 複数の組織や企業が、よく知られている形式で情報交換できます。
* B2B トランザクションを実行する際に、効率性を改善します。
* エンタープライズ統合アプリを作成する際に、作成、管理、および使用が簡単です。

## <a name="how-to-create-agreements"></a>契約の作成方法

* [AS2 契約の作成](logic-apps-enterprise-integration-as2.md)
* [X12 契約の作成](logic-apps-enterprise-integration-x12.md)
* [EDIFACT 契約の作成](logic-apps-enterprise-integration-edifact.md)

## <a name="how-to-use-an-agreement"></a>契約の使用方法

作成した契約を使用して、B2B 機能を備えた[ロジック アプリ](logic-apps-overview.md "ロジック アプリの詳細")を作成できます。

## <a name="how-to-edit-an-agreement"></a>契約の編集方法

契約を編集するには、次の手順に従います。

1. 更新する契約を含む統合アカウントを選択します。

2. **[契約]** タイルを選択します。

3. **[契約]** ブレードで、契約を選択します。

4. **[編集]** を選択します。 変更を行います。

5. 変更を保存するには、**[OK]** をクリックします。

## <a name="how-to-delete-an-agreement"></a>契約の削除方法

契約を削除するには、次の手順に従います。

1. 削除する契約を含む統合アカウントを選択します。
2. **[契約]** タイルを選択します。
3. **[契約]** ブレードで、契約を選択します。
4. **[削除]** を選択します。
5. 選択した契約を削除することを確認します。

    削除した契約は [契約] ブレードに表示されなくなります。

## <a name="next-steps"></a>次の手順
* [AS2 契約の作成](logic-apps-enterprise-integration-as2.md)
