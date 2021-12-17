---
title: Azure AD エンタイトルメント管理でカタログ作成者にアクセス ガバナンスを委任する - Azure Active Directory
description: カタログ作成者とプロジェクト マネージャーが自分でアクセスを管理できるよう、IT 管理者からアクセス ガバナンスを委任する方法について説明します。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 07/6/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5afa05b8a529d8a9e9fceeb4a113f0b743acfc05
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286575"
---
# <a name="delegate-access-governance-to-catalog-creators-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理でカタログ作成者にアクセス ガバナンスを委任する

カタログは、リソースとアクセス パッケージのコンテナーです。 関連するリソースとアクセス パッケージをグループ化するときは、カタログを作成します。 既定では、グローバル管理者または　ID ガバナンス管理者は[カタログを作成する](entitlement-management-catalog-create.md)ことができ、カタログ所有者としてさらにユーザーを追加できます。

自分のカタログを作成できるよう、管理者ではないユーザーに委任する目的で、Azure AD エンタイトルメント管理で定義されたカタログ作成者ロールにそのユーザーを追加できます。 個々のユーザーを追加することも、グループを追加することもできます。グループのメンバーはカタログを作成できるようになります。  カタログを作成した後、所有しているリソースをそのカタログに追加できます。

## <a name="as-an-it-administrator-delegate-to-a-catalog-creator"></a>IT 管理者としてカタログ作成者に委任する

カタログ作成者ロールにユーザーを割り当てるには、これらの手順に従います。

**事前に必要なロール:** グローバル管理者、ID ガバナンス管理者、またはユーザー管理者

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューの **[エンタイトルメント管理]** セクションで、 **[設定]** をクリックします。

1. **[編集]** をクリックします。

    ![カタログ作成者を追加するための設定](./media/entitlement-management-delegate-catalog/settings-delegate.png)

1. **[Delegate entitlement management]\(エンタイトルメント管理の委任\)** セクションで、**[Add catalog creators]\(カタログ作成者の追加\)** をクリックし、このエンタイトルメント管理ロールを委任するユーザーまたはグループを選択します。

1. **[選択]** をクリックします。

1. **[保存]** をクリックします。

## <a name="allow-delegated-roles-to-access-the-azure-portal"></a>委任されたロールに Azure portal へのアクセスを許可する

委任されたロール (カタログ作成者、アクセス パッケージ マネージャーなど) がアクセス パッケージを管理するために Azure portal にアクセスできるようにするには、管理ポータルの設定を確認する必要があります。

**事前に必要なロール:** グローバル管理者またはユーザー管理者

1. Azure portal で **[Azure Active Directory]** をクリックし、**[ユーザー]** をクリックします。

1. 左側のメニューで **[ユーザー設定]** をクリックします。

1. **[Azure AD 管理ポータルへのアクセスを制限する]** が **[いいえ]** に設定されていることを確認します。

    ![Azure AD ユーザー設定 - 管理ポータル](./media/entitlement-management-delegate-catalog/user-settings.png)

## <a name="manage-role-assignments-programmatically-preview"></a>プログラムによるロール割り当ての管理 (プレビュー)

Microsoft Graph を使用して、カタログの作成者と、エンタイトルメント管理のカタログ固有のロール割り当てを表示して、更新することもできます。  委任された `EntitlementManagement.ReadWrite.All` 権限を持つアプリケーションを使用する適切なロールのユーザーは、Graph API を呼び出して、エンタイトルメント管理の[ロール定義を一覧表示し](/graph/api/rbacapplication-list-roledefinitions?view=graph-rest-beta&preserve-view=true)、それらのロール定義に対する[ロール割り当てを一覧表示](/graph/api/rbacapplication-list-roleassignments?view=graph-rest-beta&preserve-view=true)することができます。

カタログ作成者ロールに割り当てられたユーザーとグループの一覧を取得するには、定義 id `ba92d953-d8e0-4e39-a797-0cbedb0a89e8` を持つロールを使用して Graph クエリを使用します

```http
GET https://graph.microsoft.com/beta/roleManagement/entitlementManagement/roleAssignments?$filter=roleDefinitionId eq 'ba92d953-d8e0-4e39-a797-0cbedb0a89e8'&$expand=principal
```


## <a name="next-steps"></a>次のステップ

- [リソースのカタログを作成および管理する](entitlement-management-catalog-create.md)
- [アクセス パッケージ管理者にアクセス ガバナンスを委任する](entitlement-management-delegate-managers.md)
- [アクセス ガバナンスをリソース所有者に委任する](entitlement-management-delegate.md)

