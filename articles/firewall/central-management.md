---
title: Azure Firewall 中央管理
description: Azure Firewall Manager の中央管理について説明します
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 58f670f3f55a63f0c1823adc13c98f5863d4d650
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444860"
---
# <a name="azure-firewall-central-management"></a>Azure Firewall 中央管理

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

複数のファイアウォールを管理する場合、ファイアウォール規則を絶えず変更していると、規則の同期が難しくなります。中央の IT チームには、ファイアウォールの基本方針を定義し、複数の事業単位にそれを適用する方法を与える必要があります。 同時に、DevOps チームには、独自のローカルから派生したファイアウォール ポリシーを作成し、機敏性を上げることが求められます。

Azure Firewall Manager Preview は以上のような問題の解決に役立ちます。


## <a name="azure-firewall-manager-preview"></a>Azure Firewall Manager Preview

Azure Firewall Manager プレビューは、クラウドベースのセキュリティ境界に対して集約型セキュリティ ポリシーとルート管理を提供するネットワーク セキュリティ管理サービスです。 これにより、企業の IT チームは複数の Azure Firewall インスタンスを対象としてトラフィックにフィルターを適用するネットワークおよびアプリケーション レベルの規則を一箇所で簡単に定義できます。 トラフィックの統制と保護を目的とし、ハブ アンド スポークのアーキテクチャで、さまざまな Azure リージョンおよびサブスクリプションを対象範囲とすることができます。 また、派生したローカルのファイアウォール セキュリティ ポリシーを組織全体で実装することで DevOps の機敏性が上がります。

### <a name="firewall-policy"></a>ファイアウォール ポリシー

ファイアウォール ポリシーは、NAT ルール、ネットワーク ルール、アプリケーション ルールを集めたものに脅威インテリジェンスの設定を加えた Azure リソースです。 "*セキュリティ保護付き仮想ハブ*" 内および "*ハブ仮想ネットワーク*" 内の複数の Azure Firewall インスタンスに使用できるグローバル リソースです。 ポリシーはゼロから作成するか、既存のポリシーから継承することができます。 DevOps は継承を利用することで、組織から指示されている基本ポリシーの上にローカル ファイアウォール ポリシーを作成できます。 ポリシーはすべてのリージョンとサブスクリプションに適用されます。
 
Azure Firewall Manager でファイアウォール ポリシーと関連付けを作成できます。 ただし、REST API、テンプレート、Azure PowerShell、CLI を利用してポリシーを作成し、管理することもできます。 ポリシーを作成したら、仮想 WAN ハブでポリシーとファイアウォールを関連付け、"*セキュリティ保護付き仮想ハブ*" にするか、仮想ネットワークでポリシーとファイアウォールを関連付け、"*ハブ仮想ネットワーク*" にすることができます。

### <a name="pricing"></a>価格

ポリシーは、ファイアウォールの関連付けに基づいて課金されます。 ファイアウォールの関連付けが 0 個または 1 個のポリシーは無料です。 ファイアウォールの関連付けが複数存在するポリシーは、固定レートで課金されます。 詳細については、「[Azure Firewall Manager の価格](https://azure.microsoft.com/pricing/details/firewall-manager/)」を参照してください。

## <a name="azure-firewall-management-partners"></a>Azure Firewall Management パートナー

業界をリードする次のサードパーティ製ソリューションでは、標準の Azure REST API による Azure Firewall 中央管理を支援します。 いずれのソリューションにも個性的な特徴と機能があります。

- [AlgoSec CloudFlow](https://www.algosec.com/azure/) 
- [Barracuda Cloud Security Guardian](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [Tufin Orca](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>次のステップ

Azure Firewall Manager Preview の詳細については、「[Azure Firewall Manager Preview とは](../firewall-manager/overview.md)」を参照してください。