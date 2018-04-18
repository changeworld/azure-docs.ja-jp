---
title: Azure リソース向けの Privileged Identity Management - Azure リソースのアクセス レビューを完了する | Microsoft Docs
description: Azure リソースのアクセス レビューを完了する方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 5ce02c2d27ec3de87fe44e9c904f19409600e5c5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---finish-access-review"></a>Privileged Identity Management - リソース ロール - アクセス レビューを終了する
[セキュリティ レビューが開始](pim-resource-roles-start-access-review.md)されると、特権ロール管理者は特権アクセスの状況を確認できるようになります。 ユーザーに自分のアクセスをレビューすることを求める電子メールは、Azure リソース向けの Privileged Identity Management (PIM) によって自動的に送信されます。 電子メールが届かなかったユーザーがいる場合は、セキュリティ管理者が [セキュリティ レビューを実行する方法](pim-resource-roles-perform-access-review.md)についての指示をユーザーに送信します。

セキュリティ レビューの期間が終わった後、またはすべてのユーザーが自己レビューを完了した後に、この記事に記載されている手順に従って、レビューを管理し、結果を表示することができます。

## <a name="manage-security-reviews"></a>セキュリティ レビューの管理
1. [Azure Portal](https://portal.azure.com/) に移動し、ダッシュボードで **Azure リソース** アプリケーションを選択します。
2. リソースを選択します。
3. ダッシュボードの **[アクセス レビュー]** セクションをクリックします。
![](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)
4. 管理するアクセス レビューを選択します。

アクセス レビューの [詳細] ブレードには、このレビューを管理するためのオプションが多数あります。
![](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>停止
すべてのアクセス レビューには終了日が設定されていますが、 **[停止]** ボタンを使用するとレビューを早期に終了することができます。 この時点までにレビューを行っていないユーザーは、レビューが停止されるとレビューを行うことができなくなります。 レビューを停止後に再開することはできません。

### <a name="reset"></a>リセット
アクセス レビューをリセットして、それに対して行われたすべての決定を削除できます。 アクセス レビューをリセットすると、すべてのユーザーは、再度レビューを行っていないユーザーとしてマークされます。 

### <a name="apply"></a>適用
終了日に達するか手動で停止されたためにアクセス レビューが完了した後で、 **[適用]** ボタンをクリックすると、レビューの結果が適用されます。 レビューでユーザーのアクセスが拒否された場合は、この手順によりそのユーザーのロール割り当てが削除されます。  

### <a name="delete"></a>削除
そのレビューが今後も必要なければ、削除します。 **[削除]** ボタンをクリックすると、レビューが PIM アプリケーションから削除されます。

## <a name="results"></a>結果
[結果] タブで、レビュー結果の表示とダウンロードを行います。![](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>レビュー担当者
既存のアクセス レビューの表示とレビュー担当者の追加を行います。 レビュー担当者にレビューを完了するように伝えます。
![](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



