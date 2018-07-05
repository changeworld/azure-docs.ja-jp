---
title: Privileged Identity Management を使用して Azure リソースのアクセス レビューを完了する | Microsoft Docs
description: Azure リソースのアクセス レビューを完了する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e21d0240469a9c775e610c97f98c073b8f83ce8e
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442198"
---
# <a name="complete-an-access-review-for-azure-resources-by-using-privileged-identity-management"></a>Privileged Identity Management を使用して Azure リソースのアクセス レビューを完了する
[セキュリティ レビューが開始](pim-resource-roles-start-access-review.md)されると、特権ロール管理者は特権アクセスの状況を確認できるようになります。 ユーザーに自分のアクセスをレビューすることを求める電子メールは、Azure リソース向けの Privileged Identity Management (PIM) によって自動的に送信されます。 電子メールが届かなかったユーザーがいる場合は、セキュリティ管理者が[セキュリティ レビューを実行する方法](pim-resource-roles-perform-access-review.md)についての指示をユーザーに送信します。

セキュリティ レビューの期間が終わった後、またはすべてのユーザーが自己レビューを完了した後に、この記事に記載されている手順に従って、レビューを管理し、結果を表示することができます。

## <a name="manage-security-reviews"></a>セキュリティ レビューの管理
1. [Azure ポータル](https://portal.azure.com/)にアクセスします。 その後、ダッシュボードで、**Azure リソース** アプリケーションを選択します。

2. リソースを選択します。

3. ダッシュボードの **[アクセス レビュー]** セクションをクリックします。
![アクセス レビュー](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)

4. 管理するアクセス レビューを選択します。

アクセス レビューの [詳細] ブレードには、このレビューを管理するためのオプションが多数あります。 次のようなオプションがあります。

![レビューを管理するためのオプション](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Stop
すべてのアクセス レビューには終了日が設定されていますが、 **[停止]** ボタンを使用するとレビューを早期に終了することができます。 この時点でレビューを完了していないすべてのユーザーは、レビューを停止した後に完了することはできません。 レビューを停止した後に再開することはできません。

### <a name="reset"></a>Reset
アクセス レビューをリセットして、それに対して行われたすべての決定を削除できます。 アクセス レビューをリセットすると、すべてのユーザーは、再度レビューを行っていないユーザーとしてマークされます。 

### <a name="apply"></a>適用
アクセス レビューを完了したら、**[適用]** ボタンを使用して、レビューの結果を実装します。 レビューでユーザーのアクセスが拒否された場合は、この手順によりそのユーザーのロール割り当てが削除されます。  

### <a name="delete"></a>削除
そのレビューが今後も必要なければ、削除します。 **[削除]** ボタンをクリックすると、レビューが PIM アプリケーションから削除されます。

## <a name="results"></a>結果
**[結果]** タブで、レビュー結果の表示とダウンロードを行います。 
![[結果]](media/azure-pim-resource-rbac/rbac-access-review-results.png) タブ

## <a name="reviewers"></a>レビュー担当者
既存のアクセス レビューの表示とレビュー担当者の追加を行います。 レビュー担当者にレビューを完了するように伝えます。
![レビュー担当者の追加](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



