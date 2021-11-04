---
title: リソースを登録する方法
titleSuffix: Azure Network Function Manager
description: リソースを登録し、ユーザーが割り当てたマネージド ID を作成する方法について説明する
author: prmitt
ms.service: network-function-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.author: prmitt
ms.custom: ignite-fall-2021
ms.openlocfilehash: e42527e5fdca01b58c08458bc360268b5d36d7d1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092274"
---
# <a name="how-to-register-resources-and-create-user-assigned-managed-identities"></a>リソースを登録し、ユーザーが割り当てたマネージド ID を作成する方法

この記事は、デバイス リソースの作成、マネージド アプリケーション リソースの作成、およびネットワーク機能をデプロイするためのユーザー割り当てのマネージド ID の要件を理解するのに役立ちます。

## <a name="resource-provider-registration-and-permissions"></a><a name="permissions"></a>リソース プロバイダーの登録とアクセス許可

Azure Network Function Manager のリソースは、Microsoft.HybridNetwork リソース プロバイダー内にあります。 Microsoft.HybridNetwork リソース プロバイダーにサブスクリプション ID を登録します。 登録方法の詳細については、[[Azure リソース プロバイダーと種類]](../azure-resource-manager/management/resource-providers-and-types.md) を参照してください。

### <a name="device-resource-accounts"></a>デバイス リソース アカウント

Network Function Manager デバイス リソースの作成に使用するアカウントは、次の表に示す必要なアクションが割り当てられたカスタム ロールに割り当てる必要があります。 詳細については、[カスタム ロール](../role-based-access-control/custom-roles.md)に関する記事を参照してください。

| 名前 | アクション|
|---|---|
| Microsoft.DataBoxEdge/dataBoxEdgeDevices/read|ネットワーク機能がデプロイされる Azure Stack Edge リソースを読み取るために必要です。 |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action |Azure Stack Edge リソースのプロパティ セクションを読み取るために必要です。 |
|Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write |Azure Stack Edge リソース上に Network Function Manager デバイス リソースを作成するために必要です。|
| Microsoft.HybridNetwork/devices/* | Network Function Manager デバイス リソースを作成、更新、削除するために必要です。 |

### <a name="managed-applications-resource-accounts"></a>マネージド アプリケーション リソース アカウント

Azure Managed Applications リソースの作成に使用するアカウントは、次の表に示す必要なアクションが割り当てられた[カスタム ロール](../role-based-access-control/custom-roles.md)に割り当てる必要があります。 

|名前 |操作 |
|---|---|
|[Managed Application Contributor Role](../role-based-access-control/built-in-roles.md#managed-application-contributor-role)|マネージド アプリ リソースを作成するために必要です。|

## <a name="managed-identity"></a><a name="managed-identity"></a>マネージド ID

Network Function Manager を使用して Azure マネージド アプリケーションを提供するネットワーク機能パートナーは、既存の Network Function Manager デバイス リソースにアタッチされたマネージド アプリケーションをデプロイすることができます。 パートナー マネージド アプリケーションを Azure portal にデプロイする際には、Network Function Manager デバイス リソースへのアクセス許可を持つ Azure ユーザー割り当てマネージド ID リソースを指定する必要があります。 ユーザー割り当てマネージド ID は、マネージド アプリケーション リソース プロバイダーとネットワーク機能のパブリッシャーに、管理対象リソース グループの外にデプロイされている Network Function Manager デバイス リソースに対する適切な許可を与えます。 詳細については、[Azure portal でユーザー割り当てマネージド ID を管理する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)方法に関する記事を参照してください。

ネットワーク機能をデプロイするためのユーザー割り当てマネージド ID を作成するには、次の手順に従います。

1. ユーザー割り当てマネージド ID を作成し、Microsoft.HybridNetwork/devices/join/action のアクセス許可を持つカスタム ロールに割り当てます。 詳細については、[Azure portal でユーザー割り当てマネージド ID を管理する](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)方法に関する記事を参照してください。

1. Azure portal でパートナーのマネージド アプリケーションを作成するときに、このマネージド ID を指定します。 詳細については、[[Azure portal を使用してリソースにマネージド ID アクセスを割り当てる]](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md) を参照してください。

## <a name="next-steps"></a>次のステップ

詳細については、[ネットワーク機能マネージャー FAQ](faq.md) を参照してください。
