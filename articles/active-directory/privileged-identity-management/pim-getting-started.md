---
title: PIM の使用を開始する - Azure Active Directory | Microsoft Docs
description: Azure portal で Azure AD Privileged Identity Management (PIM) を有効にして使用を開始する方法を説明します。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 03/13/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cbb9b4340a7cdb9be5039722a8f75e09288ec48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472862"
---
# <a name="start-using-privileged-identity-management"></a>Privileged Identity Management の使用開始

Privileged Identity Management (PIM) を使用すると、Azure Active Directory (Azure AD) 組織内のアクセス権を管理、制御、および監視できます。 この範囲には、Azure リソース、Azure AD のほか、Office 365 や Microsoft Intune などのその他の Microsoft オンライン サービスへのアクセスが含まれます。

この記事では、Privileged Identity Management を有効にしてその使用を開始する方法について説明します。

## <a name="prerequisites"></a>前提条件

Privileged Identity Management を使用するには、次のライセンスのいずれかが必要です。

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

詳細については、「[Privileged Identity Management を使用するためのライセンスの要件](subscription-requirements.md)」を参照してください。

## <a name="sign-up-pim-for-azure-ad-roles"></a>Azure AD ロールを取得するために PIM にサインアップする

ディレクトリに対して Privileged Identity Management を有効にしたら、Azure AD ロールを管理するために Privileged Identity Management にサインアップする必要があります。

1. **[Azure AD Privileged Identity Management]** を開きます。

1. **[Azure AD ロール]** を選択します。

    ![Azure AD ロールを取得するために Privileged Identity Management にサインアップする](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. **[サインアップ]** を選択します。

1. 表示されるメッセージで、 **[はい]** をクリックして、Azure AD ロールを管理するために Privileged Identity Management にサインアップします。

    ![Azure AD ロールのメッセージを取得するために Privileged Identity Management にサインアップする](./media/pim-getting-started/sign-up-pim-message.png)

    サインアップを完了すると、Azure AD オプションが有効になります。 場合によっては、ポータルを更新する必要があります。

    Privileged Identity Management で保護する Azure リソースを検出して選択する方法については、[Privileged Identity Management で管理する Azure リソースの検出](pim-resource-roles-discover-resources.md)に関するページを参照してください。

## <a name="navigate-to-your-tasks"></a>タスクへの移動

Privileged Identity Management が設定されたら、ID 管理タスクを開始できます。

![[タスク] と [管理] のオプションを示す Privileged Identity Management のナビゲーション ウィンドウ](./media/pim-getting-started/pim-quickstart-tasks.png)

| タスク + 管理 | 説明 |
| --- | --- |
| **自分のロール**  | 自分に割り当てられている適格でアクティブなロールの一覧が表示されます。 ここでは、割り当てられている適格なロールをアクティブにできます。 |
| **個人の要求** | 適格なロール割り当てのアクティブ化の保留中要求が表示されます。 |
| **申請の承認** | ディレクトリ内のユーザーによる適格なロールのアクティブ化要求のうち、自分が承認するものの一覧が表示されます。 |
| **アクセスのレビュー** | 自分に完了が割り当てられているアクティブなアクセス レビューが一覧表示されます (自分自身のアクセスをレビューするものと、他のユーザーのアクセスをレビューするものの両方)。 |
| **Azure AD ロール** | Azure AD ロールの割り当てを管理するための、特権ロール管理者向けのダッシュボードと設定が表示されます。 このダッシュボードは、特権ロール管理者以外に対しては無効になっています。 これらのユーザーは、[自分のビュー] という特殊なダッシュボードにアクセスできます。 [自分のビュー] ダッシュボードには、テナント全体ではなく、ダッシュボードにアクセスしているユーザーに関する情報のみが表示されます。 |
| **Azure リソース** | Azure リソース ロールの割り当てを管理するための、特権ロール管理者向けのダッシュボードと設定が表示されます。 このダッシュボードは、特権ロール管理者以外に対しては無効になっています。 これらのユーザーは、[自分のビュー] という特殊なダッシュボードにアクセスできます。 [自分のビュー] ダッシュボードには、テナント全体ではなく、ダッシュボードにアクセスしているユーザーに関する情報のみが表示されます。 |

## <a name="add-a-pim-tile-to-the-dashboard"></a>ダッシュボードに PIM タイルを追加する

Privileged Identity Management を簡単に開けるようにするには、Azure portal ダッシュボードに [Privileged Identity Management] タイルを追加します。

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. **[すべてのサービス]** を選択し、**Azure AD Privileged Identity Management** サービスを探します。

    ![[すべてのサービス] でのAzure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. [Privileged Identity Management Quickstart]\(Privileged Identity Management のクイックスタート\) を選択します。

1. [Privileged Identity Management Quickstart] (Privileged Identity Management のクイックスタート) ブレードをダッシュボードにピン留めするには、 **[ダッシュボードにブレードをピン留め]** をオンにします。

    ![[Privileged Identity Management] ブレードをダッシュボードにピン留めする画びょうアイコン](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Azure ダッシュボードでは、タイルが次のように表示されます。

    ![ダッシュボード上の [Privileged Identity Management Quickstart] (Privileged Identity Management のクイックスタート) タイル](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>次のステップ

- [Privileged Identity Management で Azure AD ロールを割り当てる](pim-how-to-add-role-to-user.md)
- [Privileged Identity Management で管理する Azure リソースを検出する](pim-resource-roles-discover-resources.md)
