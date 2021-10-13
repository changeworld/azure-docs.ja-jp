---
title: Privileged Identity Management における API の概念 - Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM) の API について理解するための情報。
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: how-to
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac89554135277e0656392a7035b34aac307008b7
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668544"
---
# <a name="understand-the-privileged-identity-management-apis"></a>Privileged Identity Management API について理解する

Azure Active Directory (Azure AD) ロールの場合は Microsoft Graph API、Azure リソース ロールの場合は Azure Resource Manager API (Azure RBAC ロールと呼ばれることもあります) を使用して、Privileged Identity Management (PIM) タスクを実行することができます。 この記事では、Privileged Identity Management の API を使用するための重要な概念について説明します。

PIM API のリクエストとその他の詳細については、以下をご覧ください。

- [Azure AD ロール用の PIM の API レファレンス](/graph/api/resources/unifiedroleeligibilityschedulerequest?view=graph-rest-beta&preserve-view=true)
- [Azure リソース ロール用の PIM の API レファレンス](/rest/api/authorization/roleeligibilityschedulerequests)

> [!IMPORTANT]
> PIM API [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="pim-api-history"></a>PIM API の歴史

PIM API は、過去数年に渡って何度か見直しが行われてきました。 いくつかの機能は重複している場合もありますが、直線的にバージョンが上がっているわけではありません。

### <a name="iteration-1--only-supports-azure-ad-roles-deprecating"></a>見直し 1: Azure AD ロールのみサポート、非推奨化の予定

Microsoft の /beta/privilegedRoles エンドポイントには、ほとんどのテナントでサポートされなくなった、従来のバージョンの PIM API が配置されていました。 現在、5/31 にこの API への残りのアクセスを非推奨にする手続きを行っています。

### <a name="iteration-2--supports-azure-ad-roles-and-azure-resource-roles"></a>見直し 2 – Azure AD ロールと Azure リソース ロールのサポート

Microsoft では、/beta/privilegedAccess エンドポイントの /aadRoles と/azureResources の両方がサポートされていました。 このエンドポイントはテナントで引き続き使用できますが、Microsoft では、この API を使用して新しい開発を開始することは推奨していません。 このベータ版の API は一般提供されることはなく、最終的には非推奨となります。

### <a name="current-iteration--azure-ad-roles-in-microsoft-graph-and-azure-resource-roles-in-azure-resource-manager"></a>現在の見直し – Microsoft Graph の Azure AD ロールと Azure Resource Manager の Azure リソース ロール

Microsoft は、現在ベータ版の PIM API を一般提供する前の最終的な見直しを行っています。 お客様からのフィードバックに基づいて、Azure AD PIM API は現在、API の unifiedRoleManagement セットの下に置かれており、Azure Resource PIM API は Azure Resource Manager の Role Assignment API の下に置かれています。 これらの場所に配置することで、以下のような利点もあります:

- Azure AD ロールと Azure Resource ロールの両方における、PIM API と通常の Role Assignment API との一致
- リソースのオンボード、リソースの取得、またはロール定義の取得を行うために追加の PIM API を呼び出す必要性の削減。
- アプリ専用のアクセス許可のサポート。
- 承認やメール通知の構成などの新機能。

現在の見直しでは、PIM アラートと特権アクセス グループに対する API サポートはありません。

## <a name="current-permissions-required"></a>現在必要なアクセス許可

### <a name="azure-ad-roles"></a>Azure AD ロール

  Azure AD ロールの PIM Graph API を呼び出すには、次のアクセス許可のいずれか 1 つが必要です。

- RoleManagement.ReadWrite.Directory
- RoleManagement.Read.Directory

  必要なアクセス許可を指定する最も簡単な方法は、Azure AD 同意フレームワークを使用することです。

### <a name="azure-resource-roles"></a>Azure リソース ロール

  Azure リソース ロールの PIM API は、Azure Resource Manager フレームワーク上で開発されています。 Azure Resource Management に同意する必要がありますが、Graph API のアクセス許可は必要ありません。 また、API を呼び出すユーザーまたはサービス プリンシパルに、管理予定のリソースに対する所有者またはユーザー アクセス管理者以上のロールが割り当てられていることを確認します。

## <a name="calling-pim-api-with-an-app-only-token"></a>アプリ専用トークンを使用した PIM API の呼び出し

### <a name="azure-ad-roles"></a>Azure AD ロール

  PIM API では、委任されたアクセス許可に加えて、アプリ専用のアクセス許可がサポートされるようになりました。

- アプリ専用のアクセス許可については、必要な Azure AD ロールまたは Azure ロールのアクセス許可が既に同意されているアプリケーションを使用して、API を呼び出す必要があります。
- 委任されたアクセス許可の場合は、ユーザーとアプリケーション トークンの両方を使用して PIM API を呼び出す必要があります。 ユーザーには、グローバル管理者ロールまたは特権ロール管理者ロールが割り当てられている必要があります。また、API を呼び出すサービス プリンシパルに、管理予定のリソースに対する所有者またはユーザー アクセス管理者以上のロールが付与されていることを確認してください。

### <a name="azure-resource-roles"></a>Azure リソース ロール

  Azure リソース用の PIM API では、ユーザー専用およびアプリケーション専用の呼び出しの両方がサポートされています。 サービス プリンシパルに、リソースに対する所有者またはユーザー アクセス管理者のロールのいずれかが付与されていることを確認します。

## <a name="design-of-current-api-iteration"></a>現在の API の見直しの設計

PIM API は、Azure AD ロールと Azure リソース ロールの API の両方で一貫している Assignment と Activation API 要求という 2 つのカテゴリと、ポリシー設定で構成されています。

### <a name="assignment-and-activation-api"></a>Assignment と Activation API

適格な割り当て、期限付きの有効またはアクティブな割り当て、および割り当てのアクティブ化を行うために、PIM では次のエンティティが提供されています。

- RoleAssignmentSchedule
- RoleEligibilitySchedule
- RoleAssignmentScheduleInstance
- RoleEligibilityScheduleInstance
- RoleAssignmentScheduleRequest
- RoleEligibilityScheduleRequest

これらのエンティティは、エンド ツー エンドのシナリオを作成できるように、Azure AD ロールと Azure ロールの両方の既存の roleDefinition および roleAssignment のエンティティと共に動作します。

- スケジュール (開始または終了時刻) が指定されていない永続的な (アクティブな) ロール割り当てを作成または取得する場合は、これらの PIM エンティティは使用せずに、roleAssignment エンティティの読み取り/書き込み操作に注目する必要があります

- 有効期限の有無を問わず、資格のある割り当てを作成するには、roleEligibilityScheduleRequest の書き込み操作を使用します

- スケジュール (開始または終了時刻) を指定して永続的な (アクティブな) 割り当てを作成するには、roleAssignmentScheduleRequest の書き込み操作を使用します  

- 資格のある割り当てをアクティブ化するには、selfActivate という変更されたアクション パラメーターを持つ roleAssignmentScheduleRequest の書き込み操作を使用します

各要求オブジェクトによって、roleAssignmentSchedule または roleEligibilitySchedule オブジェクトが作成されます。 これらのオブジェクトは読み取り専用で、現在と将来のすべての割り当てのスケジュールが表示されています。

資格のある割り当てがアクティブになると、roleEligibilityScheduleInstance は引き続き残ります。 アクティブ化のための roleAssignmentScheduleRequest によって、そのアクティブ化期間に対して個別の roleAssignmentSchedule と roleAssignmentScheduleInstance が作成されます。

インスタンス オブジェクトは、資格のある割り当てでもアクティブな割り当てでも、現在存在している実際の割り当てです。 ロールまたはユーザーに対する資格のある割り当てやアクティブな割り当ての一覧を取得するには、インスタンス エンティティに対して GET 操作を実行する必要があります。

### <a name="policy-setting-api"></a>Policy Setting API

設定を管理するための次のエンティティが提供されています。

- roleManagementPolicy
- roleManagementPolicyAssignment

"*ロール管理ポリシー*" は、ルールの設定を定義します。 たとえば、MFA や承認が必要かどうか、メール通知を送信するかどうかとその対象者、永続的な割り当てを許可するかどうかなどです。 "*ポリシーの割り当て*" を行うと、ポリシーが特定のロールにアタッチされます。

この API を使用して roleManagementPolicyAssignments のすべての一覧を取得し、変更したい roleDefinitionID でフィルタリングしてから、policyAssignment に関連付けられたポリシーを更新します。

## <a name="relationship-between-pim-entities-and-role-assignment-entities"></a>PIM エンティティとロールの割り当てエンティティ間の関係

Azure AD ロールや Azure ロールの永続的な (アクティブな) 割り当てのいずれかにおける、PIM エンティティとロールの割り当てエンティティの間の唯一のリンクは、roleAssignmentScheduleInstance です。 2 つのエンティティ間には 1 対 1 マッピングが存在します。 このマッピングは、roleAssignment と roleAssignmentScheduleInstance の両方に次のものが含まれることを意味します。  

- PIM の外部で行われた永続的な (アクティブな) 割り当て
- PIM 内で作成されたスケジュールでの永続的な (アクティブな) 割り当て
- アクティブ化された資格のある割り当て

## <a name="next-steps"></a>次のステップ

- [Azure AD Privileged Identity Management API のリファレンス](/graph/api/resources/privilegedidentitymanagement-root?view=graph-rest-beta&preserve-view=true)