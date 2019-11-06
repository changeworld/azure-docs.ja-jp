---
title: Azure AD エンタイトルメント管理 (プレビュー) でアクセス パッケージの割り当てを表示、追加、および削除する - Azure Active Directory
description: Azure Active Directory エンタイトルメント管理 (プレビュー) でアクセス パッケージの割り当てを追加および削除する方法を説明します。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 694c8866a69d8488511be1670ba3d3013a4a3423
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392026"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Azure AD エンタイトルメント管理 (プレビュー) でアクセス パッケージの割り当てを表示、追加、および削除する

> [!IMPORTANT]
> Azure Active Directory (Azure AD) エンタイトルメント管理は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure AD エンタイトルメント管理では、アクセス パッケージを割り当てたユーザー、そのポリシー、および状態を確認できます。 アクセス パッケージに適切なポリシーが設定されている場合は、ユーザーをアクセス パッケージに直接割り当てることもできます。 この記事では、アクセス パッケージの割り当てを表示、追加、および削除する方法について説明します。

## <a name="view-who-has-an-assignment"></a>割り当てられているユーザーを表示する

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. **[割り当て]** をクリックして、アクティブな割り当ての一覧を表示します。

    ![アクセス パッケージへの割り当ての一覧](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. 特定の割り当てをクリックすると、その他の詳細が表示されます。

1. 必ずしもすべてのリソース ロールが適切にプロビジョニングされなかった割り当ての一覧を表示するには、フィルターの状態をクリックし、 **[Delivering]\(配信中)** を選択します。

    **[要求]** ページでユーザーの対応する要求を見つけることで、配信エラーのその他の詳細を確認できます。

1. 有効期限切れの割り当てを確認するには、フィルターの状態をクリックし、 **[Expired]\(有効期限切れ)** を選択します。

1. フィルター処理された一覧の CSV ファイルをダウンロードするには、 **[ダウンロード]** をクリックします。

## <a name="directly-assign-a-user"></a>ユーザーを直接割り当てる

場合によっては、ユーザーがアクセス パッケージを要求するプロセスをたどらなくて済むように、アクセス パッケージに特定のユーザーを直接割り当てることもできます。 ユーザーを直接割り当てるには、アクセス パッケージに、管理者に直接割り当てを許可するポリシーが必要です。

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. 左側のメニューで **[割り当て]** をクリックします。

1. **[新しい割り当て]** をクリックして、[Add user to access package]\(アクセス パッケージにユーザーを追加する) を開きます。

    ![割り当て - アクセス パッケージにユーザーを追加する](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. **[ユーザーの追加]** をクリックして、アクセス パッケージを割り当てるユーザーを選択します。

1. **[ポリシーの選択]** の一覧で、[[None (administrator direct assignments only)]\(なし (管理者による直接割り当てのみ))](entitlement-management-access-package-request-policy.md#none-administrator-direct-assignments-only) の設定があるポリシーを選択します。

    このアクセス パッケージにこの種類のポリシーがない場合は、 **[新しいポリシーの作成]** をクリックして作成することができます。

1. 選択したユーザーの割り当てを開始および終了する日付と時刻を設定します。 終了日が指定されていない場合は、ポリシーのライフサイクルの設定が使用されます。

1. 必要に応じて、記録保存のために直接割り当ての妥当性を入力します。

1. **[追加]** をクリックして、選択したユーザーをアクセス パッケージに直接割り当てます。

    しばらくしてから **[更新]** をクリックすると、割り当ての一覧にユーザーが表示されます。

## <a name="next-steps"></a>次の手順

- [アクセス パッケージの要求と設定を変更する](entitlement-management-access-package-request-policy.md)
- [レポートとログを表示する](entitlement-management-reports.md)
