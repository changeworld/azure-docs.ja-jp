---
title: Azure AD エンタイトルメント管理 (プレビュー) でアクセス パッケージの要求を表示および管理する - Azure Active Directory
description: Azure Active Directory エンタイトルメント管理 (プレビュー) でアクセス パッケージの要求を表示、再処理、およびキャンセルする方法を説明します。
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
ms.openlocfilehash: f7211fea4781904b9d97428ecf00b57970e4b57d
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430282"
---
# <a name="view-and-manage-requests-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Azure AD エンタイトルメント管理 (プレビュー) でアクセス パッケージの要求を表示および管理する

> [!IMPORTANT]
> Azure Active Directory (Azure AD) エンタイトルメント管理は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure AD エンタイトルメント管理では、アクセス パッケージを要求したユーザー、そのポリシー、および状態を確認できます。 この記事では、アクセス パッケージの要求を表示、再処理、およびキャンセルする方法について説明します。

## <a name="view-requests"></a>要求を表示する

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. **[要求]** をクリックします。

1. 特定の要求をクリックすると、その他の詳細が表示されます。

    ![アクセス パッケージの要求の一覧](./media/entitlement-management-access-package-requests/requests-list.png)

## <a name="view-a-requests-delivery-errors"></a>要求の配信エラーを表示する

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. **[要求]** をクリックします。

1. 表示する要求を選択します。

    要求に配信エラーが発生した場合、要求の状態は **[Undelivered]\(未配信)** または **[Partially delivered]\(一部配信済み)** になります。

    配信エラーが発生した場合は、要求の詳細ウィンドウに配信エラーの数が表示されます。

1. この数をクリックすると、要求のすべての配信エラーが表示されます。

## <a name="reprocess-a-request"></a>要求を再処理する

要求でエラーが発生した場合、要求を再処理してもう一度試すことができます。 再処理できるのは、状態が **[Delivery failed]\(配信失敗)** または **[Partially delivered]\(一部配信済み)** であり、完了日が 1 週間以内の要求のみです。

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. **[要求]** をクリックします。

1. 再処理する要求をクリックします。

1. 要求の詳細ウィンドウで、 **[Reprocess request]\(要求の再処理\)** をクリックします。

    ![失敗した要求を再処理する](./media/entitlement-management-access-package-requests/reprocess-request.png)

## <a name="cancel-a-pending-request"></a>保留中の要求をキャンセルする

キャンセルできるのは、まだ配信されていないか、配信に失敗した保留中の要求のみです。

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. **[要求]** をクリックします。

1. キャンセルする要求をクリックします。

1. 要求の詳細ウィンドウで、 **[要求のキャンセル]** をクリックします。

## <a name="next-steps"></a>次の手順

- [Change request and approval settings for an access package](entitlement-management-access-package-request-policy.md) (アクセス パッケージの要求と承認の設定を変更する)
- [View, add, and remove assignments for an access package](entitlement-management-access-package-assignments.md) (アクセス パッケージの割り当てを表示、追加、および削除する)