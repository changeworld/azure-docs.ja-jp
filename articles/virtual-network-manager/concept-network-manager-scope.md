---
title: Azure Virtual Network Manager のスコープを理解し、操作する
description: Azure Virtual Network Manager のスコープと、仮想ネットワークの管理に与える影響について説明します。
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 9a5a78fc77a2b0f0891bfdd2922ad1cd771aba42
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322902"
---
# <a name="understand-and-work-with-azure-virtual-network-manager-preview-scopes"></a>Azure Virtual Network Manager (プレビュー) のスコープを理解し、操作する

この記事では、Azure Virtual Network Manager で *スコープ* の概念を使用して、管理グループまたはサブスクリプションで Virtual Network Manager の特定の機能を使用する方法について説明します。 階層と、管理 *マネージャーを* 使用する時ユーザーに与える影響Virtual Networkします。 

> [!IMPORTANT]
> 現在、Azure Virtual Network Manager は、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="network-manager"></a>ネットワーク マネージャー

*Network Manager* は、*ネットワーク グループ*、*構成*、*ルール* などの子リソースで構成される、最も高いレベルのオブジェクトです。 

* **ネットワーク グループ** - 特定の接続ポリシーまたはセキュリティ管理者ポリシーを適用できるスコープ全体のサブセット。

* **構成** - Azure Virtual Network Manager には、接続構成とセキュリティ構成という 2 種類の構成があります。 接続構成を使用すると、ネットワーク トポロジを作成できます。一方、セキュリティ構成を使用すると、仮想ネットワーク間で適用できる規則のコレクションを作成できます。

* ルール - ルール コレクションは、仮想ネットワークのグローバル レベルでネットワーク トラフィックを許可または拒否できる一連のネットワーク セキュリティ規則です。 

> [!NOTE]
> Azure Virtual Network Manager では、削除する前に、すべての子リソースを削除する必要があります。
>

## <a name="scope"></a>スコープ

Azure Virtual Network Manager の *スコープ* は、機能を適用できる一連のリソースです。 スコープを指定する場合は、ネットワーク マネージャーがリソースを管理できるアクセスを制限します。 スコープの値は、管理グループ レベルまたはサブスクリプション レベルで指定できます。 リソース 階層を管理する方法については、[Azure管理グループ](../governance/management-groups/overview.md)を参照してください。 スコープとして管理グループを選択すると、すべての子リソースがスコープ内に含まれます。 

> [!NOTE]
> 同じ階層と同じ機能を選択し、(スコープが重複する) 複数の Azure Virtual Network マネージャーを作成することはできません。
> 

## <a name="features"></a>機能

機能は、Azure Virtual Network Manager で管理できるスコープ アクセスです。 Azure Virtual Network Manager には現在、*接続性* と *SecurityAdmin* の 2つの機能スコープがあります。 同じマネージャー インスタンスで両方の機能スコープVirtual Network有効にできます。 各機能の詳細については、 [Connectivity](concept-connectivity-configuration.md) and [SecurityAdmin](concept-security-admins.md)を参照してください。

> [!NOTE]
> 機能は、Azure Virtual Network Manager のデプロイ中にのみ有効になります。 機能スコープが 1 つのみVirtual Network Manager インスタンスをデプロイする場合は、新しい Azure Virtual Network Manager を再デプロイして両方の機能を有効にする必要があります。
>

## <a name="hierarchy"></a>Hierarchy

Azure Virtual Network Manager を使用すると、階層内のネットワーク リソースを管理できます。 階層とは、複数の Virtual Network Manager インスタンスで重複するスコープを管理し、各 Virtual Network Manager 内の構成を互いにオーバーレイすることもできます。 たとえば、1 つの Virtual Network Managerのトップ レベルの[管理グループ](../governance/management-groups/overview.md)を作成し、別の Virtual Network Manager のスコープとして子管理グループを作成できます。 同じリソースを含む異なる Virtual Network Manager インスタンス間で構成が競合している場合だ。 スコープが高い Virtual Network Manager からの構成が適用されます。

## <a name="next-steps"></a>次の手順

- [Azure Virtual Network Manager](create-virtual-network-manager-portal.md)  インスタンスを作成する方法について説明します。
- [ネットワーク グループ ](concept-network-groups.md)について学習します。
