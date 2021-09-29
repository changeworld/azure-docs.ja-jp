---
title: エンタイトルメント管理の委任とロール - Azure AD
description: 部門マネージャーとプロジェクト マネージャーが自分でアクセスを管理できるよう、IT 管理者からアクセス ガバナンスを委任する方法について説明します。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 7/6/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 864633246a673e331e65a1b9de329b9528ab701f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124814583"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理の委任とロール

既定では、グローバル管理者と ID ガバナンス管理者は、Azure AD エンタイトルメント管理のすべての側面を作成および管理できます。 ただし、これらのロールのユーザーは、アクセス パッケージが必要なあらゆる状況を把握しているとは限りません。 通常、コラボレーションの相手、使用するリソース、期間はそれぞれの部門、チーム、プロジェクト内のユーザーが把握しています。 管理者以外のユーザーに無制限のアクセス許可を付与する代わりに、それぞれの業務に必要な最小限のアクセス許可を付与することで、競合の発生や不適切なアクセス許可を回避することができます。

この動画では、IT 管理者から管理者ではないユーザーにアクセス ガバナンスを委任する方法について概説します。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>委任の例

エンタイトルメント管理でアクセス ガバナンスを委任する方法を理解するには、例を考えてみるとよいでしょう。 組織に次の管理者やマネージャーがいるとします。

![IT 管理者からマネージャーに委任する](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

IT 管理者の Hana には、各部署に連絡先担当者がいます。マーケティングの Mamta、財務の Mark、法務の Joe がそれぞれの部署のリソースとビジネス クリティカル コンテンツを担当しています。

アクセスを必要とするユーザー、アクセスの期間、アクセスされるリソースを把握しているのが管理者以外の人であれば、エンタイトルメント管理を利用することで、そうした管理者以外の人にアクセス ガバナンスを委任できます。 管理者以外に委任することにより、適任者がその部署のアクセスを管理することになります。

たとえば次の方法で Hana はマーケティング部、財務部、法務部にアクセス ガバナンスを委任できます。

1. Hana は新しい Azure AD セキュリティ グループを作成し、グループのメンバーとして Mamta、Mark、Joe を追加します。

1. Hana はそのグループをカタログ作成者ロールに追加します。

    Mamta、Mark、Joe はこれで、自分の部署にカタログを作成したり、自分の部署に必要なリソースを追加したり、カタログ内で追加の委任を行ったりできます。 お互いのカタログを見ることはできません。

1. Mamta は、リソースのコンテナーである **Marketing** カタログを作成します。

1. Mamta は、マーケティング部が所有するリソースをこのカタログに追加します。

1. Mamta は、このカタログのカタログ所有者として自分の部署の他のユーザーを追加できます。これにより、カタログ管理の責任を共有できます。

1. Mamta はさらに、マーケティング カタログのアクセス パッケージの作成と管理をマーケティング部のプロジェクト マネージャーに委任できます。 これは、アクセス パッケージ管理者ロールに割り当てることで実行できます。 アクセス パッケージ管理者はアクセス パッケージを作成し、管理できます。 

次の図は、マーケティング部、財務部、法務部のリソースを含むカタログを示しています。 プロジェクト マネージャーは、これらのカタログを使用し、自分のチームまたはプロジェクトのアクセス パッケージを作成できます。

![エンタイトルメント管理の委任の例](./media/entitlement-management-delegate/elm-delegate.png)

委任後、マーケティング部に含まれるロールは次の表のようになります。

| User | 職務 | Azure AD ロール | エンタイトルメント管理ロール |
| --- | --- | --- | --- |
| Hana | IT 管理者 | グローバル管理者または ID ガバナンス管理者  |  |
| Mamta | マーケティング マネージャー | User | カタログ作成者とカタログ所有者 |
| Bob | マーケティング リーダー | User | カタログ所有者 |
| Jessica | マーケティング プロジェクト マネージャー | User | アクセス パッケージ マネージャー |

## <a name="entitlement-management-roles"></a>エンタイトルメント管理のロール

エンタイトルメント管理には、エンタイトルメント管理に固有の次のロールがあります。

| エンタイトルメント管理ロール | ロール定義 ID | 説明 |
| --- | --- | -- |
| カタログ作成者 | `ba92d953-d8e0-4e39-a797-0cbedb0a89e8` | カタログを作成および管理します。 通常は、グローバル管理者ではない IT 管理者、またはリソース コレクションのリソース所有者です。 カタログを作成した人物が、自動的にカタログの最初のカタログ所有者になります。カタログ所有者はさらに追加することができます。 カタログ作成者は、自分が所有していないカタログを管理したり表示したりすることはできず、所有していないリソースをカタログに追加することはできません。 カタログ作成者が別のカタログを管理したり、所有していないリソースを追加したりする必要がある場合は、そのカタログまたはリソースの共同所有者になることを要求できます。 |
| カタログ所有者 | `ae79f266-94d4-4dab-b730-feca7e132178` | 既存のカタログを編集および管理します。 通常は、IT 管理者かリソース所有者、またはカタログ所有者が指定したユーザーです。 |
| カタログ リーダー | `44272f93-9762-48e8-af59-1b5351b1d6b3` | カタログ内の既存のアクセス パッケージを表示します。 |
| アクセス パッケージ マネージャー | `7f480852-ebdc-47d4-87de-0d8498384a83` | カタログ内のすべての既存アクセス パッケージを編集および管理します。 |
| アクセス パッケージ割り当てマネージャー | `e2182095-804a-4656-ae11-64734e9b7ae5` | すべての既存のアクセス パッケージの割り当てを編集および管理します。 |

また、アクセス パッケージの指定された承認者と申請者も、ロールではありませんが権限を持ちます。

| Right | 説明 |
| --- | --- |
| 承認者 | アクセス パッケージへの要求を承認または拒否することがポリシーによって許可されています。ただし、アクセス パッケージの定義を変更することはできません。 |
| 要求元 | アクセス パッケージのポリシーによって、そのアクセス パッケージへの要求が許可されています。 |

次の表は、エンタイトルメント管理ロールで実行できるタスクを一覧にしたものです。

| タスク | [Admin] | カタログ作成者 | カタログ所有者 | アクセス パッケージ マネージャー | アクセス パッケージ割り当てマネージャー |
| --- | :---: | :---: | :---: | :---: | :---: |
| [カタログ作成者に委任する](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |  |
| [接続されている組織を追加する](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |  |
| [新しいカタログを作成する](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |  |
| [カタログにリソースを追加する](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [カタログ所有者を追加する](entitlement-management-catalog-create.md#add-more-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [カタログを編集する](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [カタログを削除する](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [アクセス パッケージ管理者に委任する](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [アクセス パッケージ管理者を削除する](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |  |
| [カタログ内に新しいアクセス パッケージを作成する](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |  |
| [アクセス パッケージ内のリソースのロールを変更する](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [ポリシーの作成と編集](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [アクセス パッケージにユーザーを直接割り当てる](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [アクセス パッケージからユーザーを直接削除する](entitlement-management-access-package-assignments.md#remove-an-assignment) | :heavy_check_mark:  |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [アクセス パッケージに割り当てられているユーザーを表示する](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [アクセス パッケージの要求を表示する](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [要求の配信エラーを表示する](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  :heavy_check_mark: |
| [要求を再処理する](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark:  |
| [保留中の要求をキャンセルする](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [アクセス パッケージを非表示にする](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [アクセス パッケージを削除する](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>カタログにリソースを追加するために必要なロール

グローバル管理者は、カタログ内の任意のグループ (クラウドが作成したセキュリティ グループまたはクラウドが作成した Microsoft 365 グループ)、アプリケーション、または SharePoint Online サイトを追加または削除することができます。 ユーザー管理者は、ディレクトリ ロールに割り当て可能であるように構成されたグループを除き、カタログ内の任意のグループまたはアプリケーションを追加または削除することができます。  ロールを割り当て可能なグループの詳細については、「[Azure Active Directory でロールを割り当て可能なグループを作成する](../roles/groups-create-eligible.md)」を参照してください。

> [!NOTE]
> ユーザー管理者ロールが割り当てられているユーザーは、所有していないカタログでカタログを作成したり、アクセス パッケージを管理したりすることはできなくなりました。 組織内のユーザーに、エンタイトルメント管理でカタログ、アクセス パッケージ、またはポリシーを構成するユーザー管理者ロールが割り当てられている場合、代わりに、これらのユーザーに **Identity Governance 管理者** ロールを割り当てる必要があります。

グローバル管理者ではないユーザーがカタログにグループ、アプリケーション、または SharePoint Online サイトを追加する場合、そのユーザーは、Azure AD ディレクトリ ロールまたはリソースの所有権と、カタログに対するカタログ所有者エンタイトルメント管理ロールの *両方* を持っている必要があります。 次の表は、カタログにリソースを追加するために必要なロールの組み合わせを示します。 カタログからリソースを削除するには、同じロールが必要です。

| Azure AD ディレクトリ ロール | エンタイトルメント管理ロール | セキュリティ グループの追加 | Microsoft 365 グループの追加 | アプリの追加 | SharePoint Online サイトの追加 |
| --- | :---: | :---: | :---: | :---: | :---: |
| [グローバル管理者](../roles/permissions-reference.md) | 該当なし |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [ユーザー管理者](../roles/permissions-reference.md) | 該当なし |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune 管理者](../roles/permissions-reference.md) | カタログ所有者 | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange 管理者](../roles/permissions-reference.md) | カタログ所有者 |  | :heavy_check_mark: |  |  |
| [Teams サービス管理者](../roles/permissions-reference.md) | カタログ所有者 |  | :heavy_check_mark: |  |  |
| [SharePoint 管理者](../roles/permissions-reference.md) | カタログ所有者 |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [アプリケーション管理者](../roles/permissions-reference.md) | カタログ所有者 |  |  | :heavy_check_mark: |  |
| [クラウド アプリケーション管理者](../roles/permissions-reference.md) | カタログ所有者 |  |  | :heavy_check_mark: |  |
| User | カタログ所有者 | グループ所有者の場合のみ | グループ所有者の場合のみ | アプリ所有者の場合のみ |  |

タスクの最小限の特権ロールを決定するには、「[Azure Active Directory における管理タスク別の管理者ロール](../roles/delegate-by-task.md#entitlement-management)」を参照することもできます。

## <a name="manage-role-assignments-programmatically-preview"></a>プログラムによるロール割り当ての管理 (プレビュー)

Microsoft Graph を使用して、カタログの作成者と、エンタイトルメント管理のカタログ固有のロール割り当てを表示して、更新することもできます。  委任された `EntitlementManagement.ReadWrite.All` 権限を持つアプリケーションを使用する適切なロールのユーザーは、Graph API を呼び出して、エンタイトルメント管理の[ロール定義を一覧表示し](/graph/api/rbacapplication-list-roledefinitions?view=graph-rest-beta&preserve-view=true)、それらのロール定義に対する[ロール割り当てを一覧表示](/graph/api/rbacapplication-list-roleassignments?view=graph-rest-beta&preserve-view=true)することができます。

たとえば、特定のユーザーまたはグループが割り当てられているエンタイトルメント管理固有のロールを表示するには、Graph クエリを使用してロールの割り当てを一覧表示し、ユーザーまたはグループの ID を `principalId` クエリ フィルターの値として指定します。次に例を示します。

```http
GET https://graph.microsoft.com/beta/roleManagement/entitlementManagement/roleAssignments?$filter=principalId eq '10850a21-5283-41a6-9df3-3d90051dd111'&$expand=roleDefinition&$select=id,appScopeId,roleDefinition
```

カタログに固有のロールの場合、応答内の `appScopeId` は、ユーザーにロールが割り当てられているカタログを示します。  この応答では、エンタイトルメント管理でのロールに対するプリンシパルの明示的な割り当てのみが取得されます。ディレクトリ ロールを介してアクセス権を持つユーザー、またはロールに割り当てられたグループのメンバーシップを介してアクセス権を持つユーザーの結果は返されません。


## <a name="next-steps"></a>次のステップ

- [カタログ作成者にアクセス ガバナンスを委任する](entitlement-management-delegate-catalog.md)
- [アクセス パッケージ管理者にアクセス ガバナンスを委任する](entitlement-management-delegate-managers.md)
- [リソースのカタログを作成および管理する](entitlement-management-catalog-create.md)
