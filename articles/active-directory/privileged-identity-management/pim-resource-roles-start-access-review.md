---
title: Azure リソース向けの PIM でアクセス レビューを開始する| Microsoft Docs
description: Azure リソース向けの Privileged Identity Management でアクセス レビューを開始する方法について説明します
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 61ed4e82e0b782b423668564dae6efb272967702
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---start-access-review"></a>Privileged Identity Management - リソース ロール - アクセス レビューを開始する
ユーザーが持っているアクセス特権がユーザーには不要になった場合、そのロールの割り当ては "古く" なっています。 このような古くなったロールの割り当てに関連するリスクを軽減するために、特権ロール管理者は、ユーザーに与えられているロールを定期的に確認する必要があります。 このドキュメントでは、Azure リソース向けの Privileged Identity Management (PIM) でアクセス レビューを開始する手順を説明します。

PIM アプリケーションのメイン ページで、以下に移動します。

* **[アクセス レビュー]** > **[追加]**

![](media/azure-pim-resource-rbac/rbac-access-review-home.png)

**[追加]** ボタンをクリックすると、**[アクセス レビューの作成]** ブレードが表示されます。 このブレードでは、名前と時間制限を使用してレビューを構成し、確認するロールを選択し、レビューを実行するユーザーを決定します。

![](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>レビューを構成する
アクセス レビューを作成するには、名前を付け、開始日と終了日を設定する必要があります。

![レビューを構成する - スクリーン ショット](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

ユーザーが完了するための十分なレビューの期間の長さを作成します。 終了日の前に完了したら、常にレビューを早く停止できます。

### <a name="choose-a-role-to-review"></a>確認するロールを選択する
各レビューでは、1 つのロールに重点を置いています。 特定のロールのブレードからアクセス レビューを開始した場合を除き、ここでロールを選択する必要があります。

1. **[役割メンバーシップをレビューする]**
   
    ![ロール メンバーシップを確認する - スクリーン ショット](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. 一覧から 1 つのロールを選択します。

### <a name="decide-who-will-perform-the-review"></a>レビューを実行するユーザを決定する
レビューを実行するための 3 つのオプションがあります。 他のユーザーにレビューを割り当てて完了してもらうか、自分でレビューするか、または各ユーザーが自身のアクセスを確認できます。

1. いずれかのオプションを選択してください。
   
   * **選択したユーザー**: アクセスする必要があるユーザーがわからない場合は、このオプションを使用します。 このオプションでは、リソース所有者またはグループ マネージャーにレビューを割り当て、完了してもらうことができます。
   * **割り当て済み (セルフ)**: ユーザーに自分のロール割り当てを確認してもらう場合は、このオプションを使用します。
   
2. **[レビュー担当者の選択]**
   
    ![レビュー担当者を選択する - スクリーン ショット](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>レビューを開始する
最後に、アクセスを承認する場合にユーザーが理由を提供することを要求するオプションがあります。 必要に応じて、レビューの説明を追加し、 **[開始]**を選択します。

待機しているアクセス レビューがあることをユーザーに通知し、 [アクセス レビューを実行する方法](pim-resource-roles-perform-access-review.md)を示します。

## <a name="manage-the-access-review"></a>アクセスレビューを管理する
レビュー担当者が PIM Azure リソース ダッシュボードでレビューを完了する場合は、[アクセス レビュー] セクションで進行状況を追跡できます。 ディレクトリのアクセス権は、 [レビューが完了する](pim-resource-roles-complete-access-review.md)まで変更されません。

レビュー期間が終了するまで、レビューの完了をユーザーに通知するか、または [アクセス レビュー] セクションからレビューを早めに停止することができます。

