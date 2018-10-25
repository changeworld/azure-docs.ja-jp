---
title: Azure Digital Twins のロールベースのアクセス制御の概要 | Microsoft Docs
description: Digital Twins でのロールベースのアクセス制御による認証について説明します。
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: 7a6d8565a0f85b4cb81d9f5f23b04fe6b2edc53e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323838"
---
# <a name="role-based-access-control"></a>ロールベースのアクセス制御

Azure Digital Twins では、空間グラフ内の特定のデータ、リソース、アクションへのアクセスを正確に制御できます。 これは、"_ロールベースのアクセス制御_" と呼ばれるきめ細かいロールとアクセス許可の管理によって行われます。 ロールベースのアクセス制御は、"_ロール_" つまりアクセス許可のレベルと、"_ロールの割り当て_" つまりユーザーまたはデバイスへのロールの関連付けで構成されます。

ロールベースのアクセス制御を使用すると、ユーザー、デバイス、サービス プリンシパル、ユーザー定義関数、ドメインに属しているすべてのユーザー、またはテナントに、アクセス許可を付与できます。 さらに、アクセスのレベルを微調整することもできます。

ロールベースのアクセス制御は、アクセス許可が空間グラフを下方に継承される点が独特です。

## <a name="what-can-i-do-with-role-based-access-control"></a>ロールベースのアクセス制御でできること

開発者は、ロールベースのアクセス制御を使用して次のことをできます。

* ビル全体のデバイスまたは特定の部屋やフロアだけのデバイスを管理する権限をユーザーに付与します。
* グラフ全体のすべての空間グラフ ノードまたはグラフのセクションのノードだけに対するグローバル アクセス権を、管理者に付与します。
* サポート スペシャ リストに、アクセス キーを除く、グラフへの読み取りアクセス権を付与します。
* ドメインのすべてのメンバーに、すべてのグラフ オブジェクトへの読み取りアクセス権を付与します。

## <a name="role-based-access-control-best-practices"></a>ロールベースのアクセス制御のベスト プラクティス

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>ロール

### <a name="role-definitions"></a>ロールの定義

**ロールの定義**はアクセス許可のコレクションであり、単に**ロール**と呼ばれることもあります。 ロールの定義では、"*作成*"、"*読み取り*"、"*更新*"、"*削除*" など、許可される操作が列記されます。 また、これらのアクセス許可が適用されるオブジェクトの型も指定されます。

Azure Digital Twins では次のロールを使用できます。

* **スペース管理者**: 指定されたスペースとその下にあるすべてのノードの、作成、読み取り、更新、削除アクセス許可。 グローバル アクセス許可。
* **ユーザー管理者**: ユーザーおよびユーザー関連オブジェクトの、作成、読み取り、更新、削除アクセス許可。 スペースに対する読み取りアクセス許可。
* **デバイス管理者**: デバイスおよびデバイス関連オブジェクトの、作成、読み取り、更新、削除アクセス許可。 スペースに対する読み取りアクセス許可。
* **キー管理者**: アクセス キーの、作成、読み取り、更新、削除アクセス許可。 スペースに対する読み取りアクセス許可。
* **トークン管理者**: アクセス キーの、読み取りおよび更新アクセス許可。 スペースに対する読み取りアクセス許可。
* **ユーザー**: スペース、センサー、ユーザーおよびそれに対応する関連オブジェクトの、読み取りアクセス許可。
* **サポート スペシャ リスト**: アクセス キーを除くすべてのものの読み取りアクセス許可。
* **デバイス インストーラー**: デバイスとセンサーおよびそれに対応する関連オブジェクトの、読み取りおよび更新アクセス許可。 スペースに対する読み取りアクセス許可。
* **ゲートウェイ デバイス**: センサーの作成アクセス許可。 デバイスとセンサーおよびそれに対応する関連オブジェクトの、読み取りアクセス許可。

>[!NOTE]
> *上記のものの完全な定義は、システム/ロール API のクエリを実行することで取得できます。*

### <a name="object-types"></a>オブジェクトの型

`ObjectIdType` は、ロールを提供される ID の種類を示します。 `DeviceId` および `UserDefinedFunctionId` 型を除き、型は Azure Active Directory (Azure AD) オブジェクトのプロパティに対応します。
  
* `UserId` 型は、ユーザーにロールを割り当てます。
* `DeviceId` 型は、デバイスにロールを割り当てます。
* `DomainName` 型は、ドメイン名にロールを割り当てます。 指定されたドメイン名を持つ各ユーザーには、対応するロールのアクセス権があります。
* `TenantId` 型は、テナントにロールを割り当てます。 指定された Azure AD テナント ID に属する各ユーザーには、対応するロールのアクセス権があります。
* `ServicePrincipalId` 型は、サービス プリンシパルのオブジェクト ID にロールを割り当てます。
* `UserDefinedFunctionId` 型は、ユーザー定義関数 (UDF) にロールを割り当てます。

> [!div class="nextstepaction"]
> [ユーザーに対するクエリまたはオブジェクト ID](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)

> [!div class="nextstepaction"]
> [サービス プリンシパルのオブジェクト ID を取得する](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.8.1)

> [!div class="nextstepaction"]
> [Azure AD テナントのオブジェクト ID を取得する](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)

## <a name="role-assignments"></a>ロールの割り当て

アクセス許可は、ロールの割り当てを作成することによって対象者に許可され、ロールの割り当てを削除することによって取り消されます。 Azure Digital Twins のロールの割り当てでは、オブジェクト (ユーザー、Azure AD テナントなど)、ロール、およびスペースが関連付けられます。 アクセス許可は、そのスペースに属するすべてのオブジェクト (その下にある空間グラフ全体を含みます) に付与されます。

たとえば、ユーザーは空間グラフのルート ノードに対するロール `DeviceInstaller` でロールの割り当てを付与され、これは建物を表します。 この場合、ユーザーは、そのノードだけでなく、建物内の他のすべての子スペースで、デバイスの読み取りと更新を行うことができます。

## <a name="next-steps"></a>次の手順

Azure Digital Twins のセキュリティについては、「[ロールの割り当てを作成および管理する](./security-create-manage-role-assignments.md)」をご覧ください。
