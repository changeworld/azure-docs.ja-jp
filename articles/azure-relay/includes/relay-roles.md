---
author: spelluru
ms.service: service-bus-relay
ms.topic: include
ms.date: 07/19/2021
ms.author: spelluru
ms.openlocfilehash: 43550a98e5df6c58214427a989e290b3c69ab338
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129378181"
---
## <a name="overview"></a>概要
セキュリティ プリンシパル (ユーザー、グループ、アプリケーション) で Relay エンティティへのアクセスが試行された場合、要求が承認される必要があります。 Azure AD では、リソースへのアクセスは 2 段階のプロセスです。

1. まず、セキュリティ プリンシパルの ID が **認証** され、OAuth 2.0 トークンが返されます。 トークンを要求するリソース名は `https://relay.azure.net` です。 アプリケーションが Azure VM、仮想マシン スケール セット、または Azure 関数アプリなどの Azure エンティティ内から実行されている場合、そのアプリケーションは、マネージド ID を使用してリソースにアクセスできます。
2. 次に、トークンは、指定したリソース (ハイブリッド接続、WCF Relay) へのアクセスを **承認** するために、要求の一部として Relay サービスに渡されます。 Azure Active Directory (Azure AD) では、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を通じて、セキュリティで保護されたリソースへのアクセス権が承認されます。 Azure Relay によって、Relay エンティティへのアクセスに使用される一般的なアクセス許可セットを含む一連の Azure 組み込みロールが定義されます。 データにアクセスするためのカスタム ロールを定義することもできます。 Azure Relay でサポートされている組み込みロールの一覧については、「[Azure Relay の Azure 組み込みロール](#azure-built-in-roles-for-azure-relay)」を参照してください。 Relay に対して要求を行うネイティブ アプリケーションと Web アプリケーションは、Azure AD を使用して承認することもできます。  

## <a name="azure-built-in-roles-for-azure-relay"></a>Azure Relay の Azure 組み込みロール
Azure Relay の場合、名前空間およびそれに関連するすべてのリソースの Azure portal および Azure リソース管理 API による管理は、Azure RBAC モデルを使って既に保護されています。 Azure では、Relay 名前空間へのアクセスを承認するための次の Azure 組み込みロールが提供されています。

| Role | 説明 | 
| ---- | ----------- | 
| [Azure Relay 所有者](../../role-based-access-control/built-in-roles.md#azure-relay-owner) | このロールを使用して、Azure Relay リソースへの **フル** アクセスを許可します。 |
| [Azure Relay リスナー](../../role-based-access-control/built-in-roles.md#azure-relay-listener) | このロールを使用して、Azure Relay リソースへの **リッスンおよびエンティティ読み取り** アクセスを許可します。 |
| [Azure Relay 送信者](../../role-based-access-control/built-in-roles.md#azure-relay-sender) | このロールを使用して、Azure Relay リソースへの **送信およびエンティティ読み取り** アクセスを許可します。 | 

## <a name="resource-scope"></a>リソースのスコープ
セキュリティ プリンシパルに Azure ロールを割り当てる前に、セキュリティ プリンシパルに必要なアクセスのスコープを決定します。 ベスト プラクティスとしては、常にできるだけ狭いスコープのみを付与するのが最善の方法です。

次のリストは、Azure Relay リソースへのアクセスのスコープとして指定できるレベルを、最も狭いスコープから順に示しています。

- **Relay エンティティ**: ロールの割り当ては、ハイブリッド接続や WCF Relay などの特定の Relay エンティティに適用されます。
- **Relay 名前空間**: ロールの割り当ては、名前空間の下のすべての Relay エンティティに適用されます。
- **リソース グループ**: ロールの割り当ては、リソース グループの下のすべての Relay リソースに適用されます。
- **サブスクリプション**: ロールの割り当ては、サブスクリプションのすべてのリソース グループ内のすべての Relay リソースに適用されます。

> [!NOTE]
> Azure ロールの割り当ての反映には最大で 5 分かかる場合があることに留意してください。 組み込みのロールの定義方法の詳細については、[ロール定義](../../role-based-access-control/role-definitions.md#control-and-data-actions)に関するページを参照してください。 Azure カスタム ロールの作成については、「[Azure カスタム ロール](../../role-based-access-control/custom-roles.md)」を参照してください。 

