---
title: Azure サブスクリプションのポリシーを管理する
description: ディレクトリからの Azure サブスクリプションの移動とディレクトリへの Azure サブスクリプションの移動を制御する Azure サブスクリプション ポリシーの管理方法について説明します。
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/28/2021
ms.reviewer: andalmia
ms.author: banders
ms.openlocfilehash: 2530cdd36a2844c5b311e97d9747c8734d808206
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464929"
---
# <a name="manage-azure-subscription-policies"></a>Azure サブスクリプションのポリシーを管理する

この記事は、Azure サブスクリプションのポリシーをサブスクリプションの操作のために構成し、ディレクトリからの Azure サブスクリプションの移動とディレクトリへの Azure サブスクリプションの移動を制御する際に役立ちます。

## <a name="prerequisites"></a>前提条件

- サブスクリプションのポリシーを編集できるのは、ディレクトリの[グローバル管理者](../../active-directory/roles/permissions-reference.md#global-administrator)だけです。 サブスクリプションのポリシーを編集する前に、グローバル管理者は、[Azure のすべてのサブスクリプションと管理グループを管理できるようアクセス権限を昇格させる](../../role-based-access-control/elevate-access-global-admin.md)必要があります。 これでサブスクリプションのポリシーを編集できるようになります。
- それ以外のユーザーはすべて、現在のポリシー設定の読み取りのみ可能です。

## <a name="available-subscription-policy-settings"></a>使用できるサブスクリプション ポリシー設定

ディレクトリからの Azure サブスクリプションの移動とディレクトリへの Azure サブスクリプションの移動は、以下のポリシー設定を使用して制御します。

### <a name="subscriptions-leaving-aad-directory"></a>Subscriptions leaving AAD directory (AAD ディレクトリからのサブスクリプションの移動)

このポリシーでは、現在のディレクトリからのサブスクリプションの移動をユーザーに許可または禁止します。 [サブスクリプションの所有者](../../role-based-access-control/built-in-roles.md#owner)は、自分が所属する別のディレクトリに [Azure サブスクリプションのディレクトリを変更](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)できます。 このことがガバナンス上の問題を招く原因となるため、グローバル管理者は、ディレクトリの変更をディレクトリ ユーザーに許可または禁止することができます。

### <a name="subscriptions-entering-aad-directory"></a>Subscriptions entering AAD directory (AAD ディレクトリへのサブスクリプションの移動)

このポリシーでは、現在のディレクトリのアクセス権を持つ他のディレクトリ ユーザーに、現在のディレクトリへのサブスクリプションの移動を許可または禁止します。 [サブスクリプションの所有者](../../role-based-access-control/built-in-roles.md#owner)は、自分が所属する別のディレクトリに [Azure サブスクリプションのディレクトリを変更](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)できます。 このことがガバナンス上の問題を招く原因となるため、グローバル管理者は、ディレクトリの変更をディレクトリ ユーザーに許可または禁止することができます。

### <a name="exempted-users"></a>除外対象ユーザー

グローバル管理者は、ガバナンス上の理由により、すべてのサブスクリプション ディレクトリを対象に、現在のディレクトリへの移動または現在のディレクトリからの移動を禁止することができます。 ただし、特定のユーザーについては、どちらの操作も許可したい場合もあるでしょう。 どちらの場合でも、除外対象となる一連のユーザーを構成することで、それらのユーザーは、自分以外の全員に適用されたポリシー設定をバイパスできるようになります。

## <a name="setting-subscription-policy"></a>サブスクリプションのポリシーを設定する

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. **[サブスクリプション]** に移動します。 コマンド バーに **[ポリシーの管理]** が表示されます。  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" alt-text="[サブスクリプション] の [ポリシーの管理] を示すスクリーンショット。" lightbox="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" :::
1. **[ポリシーの管理]** を選択して、ディレクトリに関して現在設定されているサブスクリプションのポリシーについての詳細を表示します。 [昇格されたアクセス許可](../../role-based-access-control/elevate-access-global-admin.md)を持つグローバル管理者は、除外対象ユーザーの追加と削除を含め、各種の設定に変更を加えることができます。  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies.png" alt-text="特定のポリシー設定と除外対象ユーザーを示すスクリーンショット。" lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies.png" :::
1. 一番下にある **[Save changes]\(変更の保存\)** を選択して変更内容を保存します。 変更内容はすぐに反映されます。

## <a name="read-subscription-policy"></a>サブスクリプション ポリシーを読み取る

グローバル管理者以外でも、サブスクリプション ポリシー領域に移動して、ディレクトリのポリシー設定を表示することはできます。 編集は一切できません。 プライバシー上の理由から、除外対象ユーザーの一覧は表示できません。 ディレクトリの設定で許可されていれば、これらのユーザーは自分のグローバル管理者を表示してポリシー変更リクエストを送信できます。

:::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" alt-text="閲覧者として [サブスクリプション] の [ポリシーの管理] を表示した画面のスクリーンショット。" lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" :::

## <a name="next-steps"></a>次の手順

- [Cost Management + Billing のドキュメント](../index.yml)をお読みください。