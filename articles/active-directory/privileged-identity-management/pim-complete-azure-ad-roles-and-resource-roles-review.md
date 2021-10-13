---
title: PIM で Azure リソース ロールと Azure AD ロールのアクセス レビューを完了する - Azure Active Directory | Microsoft Docs
description: Azure Active Directory の Privileged Identity Management (PIM) で Azure リソース ロールと Azure AD ロールのアクセス レビューを完了する方法を説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7148284bef15b2e3f40a39652d0cb222ed5793c0
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669988"
---
# <a name="complete-an-access-review-of-azure-resource-and-azure-ad-roles-in-pim"></a>PIM で Azure リソース ロールと Azure AD ロールのアクセス レビューを完了する

[アクセス レビューが開始](pim-create-azure-ad-roles-and-resource-roles-review.md)されると、特権ロール管理者は特権アクセスの状況を確認できるようになります。 Azure Active Directory (Azure AD) の Privileged Identity Management (PIM) によって、ユーザーに自分のアクセスをレビューするよう求めるメールが自動的に送信されます。 電子メールが届かなかったユーザーがいる場合は、[アクセス レビューを実行する方法](pim-perform-azure-ad-roles-and-resource-roles-review.md)に関する手順を送信できます。

レビューが作成されたら、この記事の手順に従ってレビューを完了し、結果を確認します。

## <a name="complete-access-reviews"></a>アクセス レビューを完了する

1. [Azure ポータル](https://portal.azure.com/)にログインします。 **Azure リソース** の場合、 **[Privileged Identity Management]** に移動し、 **[Azure リソース]** をダッシュボードの **[管理]** で選択します。 **Azure AD ロール** の場合、同じダッシュボードから **[Azure AD ロール]** を選択します。

2. **Azure リソース** の場合、リソースを **[Azure リソース]** から選択し、ダッシュボードから **[アクセス レビュー]** を選択します。 **Azure AD ロール** の場合、ダッシュボードの **アクセス レビュー** に直接進みます。

3. 管理するアクセス レビューを選択します。 以下は **Azure リソース** と **Azure AD ロール** の両方の **アクセス レビュー** の概要のスクリーンショットの例。

    :::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-azure-ad-roles-home-list.png" alt-text="ロール、所有者、開始日、終了日、状態を示すアクセス レビューの一覧のスクリーンショット。" lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-azure-ad-roles-home-list.png":::

詳細ページで、**Azure リソース** と **Azure AD ロール** のレビューを管理するために、次のオプションを使用できます。

![Azure リソースでレビューを管理するオプション - 停止、リセット、適用、削除のスクリーンショット。](media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-menu.png)

### <a name="stop-an-access-review"></a>アクセス レビューを停止する

すべてのアクセス レビューには終了日が設定されていますが、 **[停止]** ボタンを使用するとレビューを早期に終了することができます。 **[停止]** ボタンは、レビュー インスタンスがアクティブになっている場合にのみ選択できます。 レビューを停止後に再開することはできません。

### <a name="reset-an-access-review"></a>アクセス レビューをリセットする

レビュー インスタンスがアクティブになっていて、レビュー担当者によって少なくとも 1 つの決定が行われた場合は、 **[リセット]** ボタンを選択してアクセス レビューをリセットすることで、それに対して行われたすべての決定を削除できます。 アクセス レビューをリセットすると、すべてのユーザーは、再度レビューを行っていないユーザーとしてマークされます。

### <a name="apply-an-access-review"></a>アクセス レビューを適用する

終了日に達するか手動で停止されたかのいずれかの理由でアクセス レビューが完了した後で、 **[適用]** ボタンをクリックすると、拒否されたユーザーのロールへのアクセスが削除されます。 レビュー中にユーザーのアクセスが拒否された場合は、この手順により、そのユーザーのロール割り当てが削除されます。 レビューの作成時に **[Auto apply]\(自動適用\)** 設定が構成されている場合、レビューは手動ではなく自動的に適用されるため、このボタンは常に無効になります。

### <a name="delete-an-access-review"></a>アクセス レビューを削除する

そのレビューが今後も必要なければ、削除します。 アクセス レビューを Privileged Identity Management サービスから削除するには、 **[削除]** ボタンを選択します。

> [!IMPORTANT]
> この破壊的な変更を確認する必要はありません。そのレビューを削除する必要があるかどうかを確認してください。

## <a name="results"></a>結果

**[結果]** ページで、レビュー結果の一覧の表示とダウンロードが行えます。

:::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-results.png" alt-text="Azure AD ロールのユーザー、結果、理由、レビュー担当者、適用元、適用結果を一覧する [結果] ページのスクリーンショット。" lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-results.png":::

> [!Note]
> **Azure AD ロール** には、ロールを割り当て可能なグループの概念があります。ここで、グループをロールに割り当てることができます。 この場合、グループのメンバーを展開するのではなく、レビューにグループが表示され、レビュー担当者はグループ全体を承認または拒否します。

:::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-results.png" alt-text="Azure リソース ロールのユーザー、結果、理由、レビュー担当者、適用元、適用結果を一覧する [結果] ページのスクリーンショット。" lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-results.png":::

> [!Note]
>グループが **Azure リソース ロール** に割り当てられている場合、Azure リソース ロールのレビュー担当者には、入れ子になったグループ内のユーザーの展開された一覧が表示されます。 レビュー担当者が入れ子になったグループのメンバーを拒否した場合、そのユーザーは入れ子になったグループから削除されないため、その拒否結果は正常に適用されません。

## <a name="reviewers"></a>レビュー担当者

**[レビュー担当者]** ページで、既存のアクセス レビューの表示とレビュー担当者の追加を行います。 ここでレビュー担当者にレビューを完了するためのリマインダーを送信することもできます。

> [!Note]
> 選択したレビュー担当者の種類がユーザーまたはグループの場合は、任意の時点でプライマリ レビュー担当者としてさらに多くのユーザーまたはグループを追加できます。 また、いつでもプライマリ レビュー担当者を削除できます。 レビュー担当者の種類がマネージャーである場合は、ユーザーまたはグループをフォールバック レビュー担当者として追加して、管理者がいないユーザーのレビューを完了することができます。 フォールバック レビュー担当者は削除できません。

:::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-reviewers.png" alt-text="Azure リソース ロールの名前とユーザー プリンシパル名の一覧を示すレビュー担当者ページのスクリーンショット。" lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-reviewers.png":::

## <a name="next-steps"></a>次のステップ

- [PIM で Azure リソース ロールと Azure AD ロールのアクセス レビューを作成する](pim-create-azure-ad-roles-and-resource-roles-review.md)
- [PIM で Azure リソース ロールと Azure AD ロールのアクセス レビューを実行する](pim-perform-azure-ad-roles-and-resource-roles-review.md)
