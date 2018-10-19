---
title: Azure Active Directory を使用してグループ情報を編集する方法 | Microsoft Docs
description: Azure Active Directory を使用してグループの情報を編集する方法について説明します。
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: a02987fdce3a15cd5d416234e3717df6d33622ec
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2018
ms.locfileid: "45731344"
---
# <a name="how-to-edit-your-group-information-using-azure-active-directory"></a>方法: Azure Active Directory を使用してグループ情報を編集する

Azure Active Directory を使用すると、名前、説明、またはメンバーシップの種類の更新など、グループの設定を編集できます。

## <a name="to-edit-your-group-settings"></a>グループ設定を編集するには
1. ディレクトリの全体管理者アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。

2. **[Azure Active Directory]** を選択し、**[グループ]** を選択します。

    **[グループ - すべてのグループ]** ページが表示され、ご自分のアクティブなグループがすべて表示されます。

3. **[グループ - すべてのグループ]** ページから、**[検索]** ボックスに必要なだけグループ名を入力します。 この記事では、**[MDM policy - West]\(MDM ポリシー - 西部\)** グループを検索します。

    検索結果が、文字を入力すると更新されて、**[検索]** ボックスの下に表示されます。

    ![[検索] ボックスに検索テキストを含む [すべてのグループ] ページ](media/active-directory-groups-settings-azure-portal/search-for-specific-group.png)

4. グループの **[MDM policy - West]\(MDM ポリシー - 西部\)** を選択して、**[管理]** 領域から **[プロパティ]** を選択します。

    ![数値とメンバーとメンバーのオプションが強調表示されている [グループの概要] ページ](media/active-directory-groups-settings-azure-portal/group-overview-blade.png)

5. 必要に応じて、次のような **[全般設定]** の情報を更新します。

    ![グループにおけるプロパティの設定](media/active-directory-groups-settings-azure-portal/group-properties-settings.png)

    - **[グループ名]。** 既存のグループ名を編集します。
    
    - **[グループの説明]。** 既存のグループの説明を編集します。

    - **[グループの種類]。** グループの種類が作成されると、変更することはできません。 **[グループの種類]** を変更するには、グループを削除して、新しいグループを作成する必要があります。
    
    - **[メンバーシップの種類]。** メンバーシップの種類を変更します。 さまざまな利用可能なメンバーシップの種類の詳細については、「[方法: Azure Active Directory を使用して基本グループを作成してメンバーを追加する](active-directory-groups-create-azure-portal.md)」を参照してください。
    
    - **[オブジェクト ID]。** オブジェクト ID は変更できませんが、コピーして PowerShell コマンドでグループに対して使用できます。 PowerShell コマンドレットの使用に関する詳細については、[グループ設定の構成用の Azure Active Directory コマンドレット](../users-groups-roles/groups-settings-v2-cmdlets.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順
次の記事は、Azure Active Directory に関する追加情報を示します。

- [グループとメンバーを表示する](active-directory-groups-view-azure-portal.md)

- [基本的なグループを作成し、メンバーを追加する](active-directory-groups-create-azure-portal.md)

- [グループからメンバーを追加または削除する方法](active-directory-groups-members-azure-portal.md)

- [グループ内のユーザーの動的ルールの管理](../users-groups-roles/groups-create-rule.md)

- [グループのメンバーシップの管理](active-directory-groups-membership-azure-portal.md)

- [グループを使用したリソースへのアクセス管理](active-directory-manage-groups.md)

- [Azure サブスクリプションを Azure Active Directory に関連付けるまたは追加する](active-directory-how-subscriptions-associated-directory.md)
