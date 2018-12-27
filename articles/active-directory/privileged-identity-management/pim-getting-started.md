---
title: PIM の使用を開始する - Azure | Microsoft Docs
description: Azure portal で Azure AD Privileged Identity Management (PIM) の使用を始める方法を説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 08/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 5b3bff27821964648713b02589c941c99e3eb03d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190091"
---
# <a name="start-using-pim"></a>PIM の使用を開始する

組織内のアクセス権は、Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用して管理、制御、監視することができます。 その対象には、Azure リソース、Azure AD、Microsoft の各種オンライン サービス (Office 365、Microsoft Intune など) へのアクセスが含まれます。

この記事では、Azure AD PIM アプリを Azure ポータル ダッシュボードに追加する方法を説明します。

## <a name="first-person-to-use-pim"></a>PIM を使用する最初のユーザー

ディレクトリ内で PIM を使用する最初のユーザーには、そのディレクトリの[セキュリティ管理者](../users-groups-roles/directory-assign-admin-roles.md#security-administrator)ロールと[特権ロール管理者](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)ロールが自動的に割り当てられます。 ユーザーの Azure AD ディレクトリ ロールの割り当てを管理できるのは特権ロール管理者だけです。 [セキュリティ ウィザード](pim-security-wizard.md)を実行して、 画面の指示に従って初回の検出と割り当てを体験することもできます。

## <a name="add-pim-tile-to-the-dashboard"></a>ダッシュボードに PIM タイルを追加する

PIM を開きやすくするには、Azure portal のダッシュボードに PIM タイルを追加する必要があります。

1. ディレクトリのグローバル管理者として [Azure portal](https://portal.azure.com/) にサインインします。

1. **[すべてのサービス]** をクリックし、**Azure AD Privileged Identity Management** サービスを探します。

    ![[すべてのサービス] でのAzure AD Privileged Identity Management](./media/pim-getting-started/pim-all-services-find.png)

1. PIM クイック スタートをクリックして開きます。

1. PIM クイック スタート ブレードをダッシュボードにピン留めするには、**[ダッシュボードにブレードをピン留め]** をオンにします。

    ![ダッシュボードにブレードをピン留め](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Azure ダッシュボードでは、タイルが次のように表示されます。

    ![PIM クイック スタート タイル](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="navigate-to-your-tasks"></a>タスクへの移動

PIM を設定した後、このブレードを使用して ID 管理タスクを実行します。

![Top-level tasks for PIM - screenshot](./media/pim-getting-started/pim-quickstart-tasks.png)

| タスク + 管理 | 説明 |
| --- | --- |
| **自分のロール**  | 自分に割り当てられている適格でアクティブなロールの一覧が表示されます。 ここでは、割り当てられている適格なロールをアクティブにできます。 |
| **個人の要求** | 適格なロール割り当てのアクティブ化の保留中要求が表示されます。 |
| **アプリケーション アクセス** | 遅延の可能性を減らし、アクティブ化後すぐにロールを使用できます。 |
| **申請の承認** | ディレクトリ内のユーザーによる適格なロールのアクティブ化要求のうち、自分が承認するものの一覧が表示されます。 |
| **アクセスのレビュー** | 自分に完了が割り当てられているアクティブなアクセス レビューが一覧表示されます (自分自身のアクセスをレビューするものと、他のユーザーのアクセスをレビューするものの両方)。 |
| **Azure AD ディレクトリ ロール** | Azure AD ディレクトリ ロールの割り当てを管理するための、特権ロール管理者向けのダッシュボードと設定が表示されます。 このダッシュボードは、特権ロール管理者以外に対しては無効になっています。 これらのユーザーは、[自分のビュー] という特殊なダッシュボードにアクセスできます。 [自分のビュー] ダッシュボードには、テナント全体ではなく、ダッシュボードにアクセスしているユーザーに関する情報のみが表示されます。 |
| **Azure リソース** | Azure リソース ロールの割り当てを管理するための、特権ロール管理者向けのダッシュボードと設定が表示されます。 このダッシュボードは、特権ロール管理者以外に対しては無効になっています。 これらのユーザーは、[自分のビュー] という特殊なダッシュボードにアクセスできます。 [自分のビュー] ダッシュボードには、テナント全体ではなく、ダッシュボードにアクセスしているユーザーに関する情報のみが表示されます。 |

## <a name="next-steps"></a>次の手順

- [PIM で自分の Azure AD ディレクトリ ロールをアクティブにする](pim-how-to-activate-role.md)
- [PIM で自分の Azure リソース ロールをアクティブにする](pim-resource-roles-activate-your-roles.md)
