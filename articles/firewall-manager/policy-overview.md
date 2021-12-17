---
title: Azure Firewall Manager ポリシーの概要
description: Azure Firewall Manager のポリシーについて説明します
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/26/2021
ms.author: victorh
ms.openlocfilehash: 98524c2c5c73ab7a75395464911585f80bcd092c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024298"
---
# <a name="azure-firewall-manager-policy-overview"></a>Azure Firewall Manager ポリシーの概要

Azure Firewall を構成するために推奨されるファイアウォール ポリシー。 セキュリティ保護付き仮想ハブ内およびハブ仮想ネットワーク内の複数の Azure Firewall インスタンスにわたって使用できるグローバル リソースです。 ポリシーは、複数のリージョンおよび複数のサブスクリプションにわたって作用します。

![Azure Firewall Manager ポリシー](media/policy-overview/policy-overview.png)

## <a name="policy-creation-and-association"></a>ポリシーの作成と関連付け

ポリシーは、Azure portal、REST API、テンプレート、Azure PowerShell、CLI など、さまざまな方法で作成、管理できます。

ポータルまたは Azure PowerShell を使用し、Azure Firewall から既存のクラシック ルールを移行して、ポリシーを作成することもできます。 詳細については、[Azure Firewall 構成を Azure Firewall ポリシーに移行する方法](migrate-to-policy.md)に関するページを参照してください。 

ポリシーは、1 つ以上の仮想ハブまたは VNet に関連付けることができます。 ファイアウォールは、リージョンにかかわらず、ご利用のアカウントに関連付けられた任意のサブスクリプションに置くことができます。

## <a name="classic-rules-and-policies"></a>クラシック ルールとポリシー

Azure Firewall はクラシック ルールとポリシーの両方がサポートされますが、ポリシーは再指定された構成です。 ポリシーとクラシック ルールを比較した表を次に示します。


| サブジェクト | ポリシー  | クラシック ルール |
| ------- | ------- | ----- |
|Contains     |NAT、ネットワーク、アプリケーションルール、カスタム DNS および DNS プロキシ設定、IP グループ、脅威インテリジェンス設定 (許可リストを含む)、IDPS、TLS 検査、Web カテゴリ、URL フィルタリング|NAT、ネットワーク、アプリケーションの規則、カスタム DNS と DNS プロキシ設定、IP グループ、脅威インテリジェンスの設定 (許可リストを含む)|
|保護     |仮想ハブと仮想ネットワーク|仮想ネットワークのみ|
|ポータルでの操作     |Firewall Manager を使用した一元管理|スタンドアロンのファイアウォール エクスペリエンス|
|複数のファイアウォールのサポート     |ファイアウォール ポリシーは、複数のファイアウォールにまたがって使用できる個別のリソース|ルールのエクスポートとインポートを手動で行うか、サードパーティの管理ソリューションを使用 |
|価格     |ファイアウォールの関連付けに基づいて課金されます。 「[価格](#pricing)」を参照してください。|Free|
|サポートされるデプロイ メカニズム     |ポータル、REST API、テンプレート、Azure PowerShell、CLI|ポータル、REST API、テンプレート、PowerShell、CLI |

## <a name="standard-and-premium-policies"></a>Standard ディスクと Premium ディスク

Azure Firewall では、Standard ポリシーと プレミアム ポリシーがサポートされています。 次の表は、2 つの違いについて説明しています。


|ポリシーの種類|機能サポート  | ファイアウォール SKU のサポート|
|---------|---------|----|
|標準ポリシー    |NAT 規則、ネットワーク規則、アプリケーション 規則<br>カスタム DNS、DNS プロキシ<br>IP グループ<br>Web カテゴリ<br>[脅威インテリジェンス]|Standard または Premium|
|プレミアム ポリシー    |すべての Standard 機能のサポートに加えて、次の機能がサポートされます。<br><br>TLS インスペクション<br>Web カテゴリ<br>URL フィルタリング<br>IDPS|Premium


## <a name="hierarchical-policies"></a>階層構造のポリシー

ポリシーはゼロから作成するか、既存のポリシーから継承することができます。 DevOps は継承を利用することで、規定されている組織の基本ポリシーの上にローカル ファイアウォール ポリシーを作成することができます。

空ではない親ポリシーを使って作成されたポリシーは、親ポリシーからすべてのルール コレクションを継承します。 親ポリシーから継承されたネットワーク ルール コレクションは、新しいポリシーの一部として定義されているネットワーク ルール コレクションより常に優先されます。 この同じロジックがアプリケーション ルール コレクションにも適用されます。 ただし、ネットワーク ルール コレクションは、継承に関係なく、常にアプリケーション ルール コレクションより先に処理されます。

親ポリシーからは、脅威インテリジェンス モードも継承されます。 この動作は、脅威インテリジェンス モードを別の値に設定することでオーバーライドすることはできますが、オフにすることはできません。 より厳密な値でオーバーライドすることのみできます。 たとえば、親ポリシーが **[警告のみ]** に設定されている場合、このローカル ポリシーを **[警告して拒否]** に構成することができます。

脅威インテリジェンス モードと同様に、脅威インテリジェンスの許可リストは親ポリシーから継承されます。 子ポリシーによって、許可リストに IP アドレスを追加できます。

NAT ルール コレクションは、特定のファイアウォールに固有のものであるため継承されません。

継承では、親ポリシーに対するすべての変更が、対応する子のファイアウォール ポリシーに自動的に適用されます。



## <a name="pricing"></a>価格

ポリシーは、ファイアウォールの関連付けに基づいて課金されます。 ファイアウォールの関連付けが 0 個または 1 個のポリシーは無料です。 ファイアウォールの関連付けが複数存在するポリシーは、固定レートで課金されます。 詳細については、「[Azure Firewall Manager の価格](https://azure.microsoft.com/pricing/details/firewall-manager/)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure ファイアーウォールのデプロイ方法については、「[Tutorial: Azure portal を使用して Azure Firewall Manager でクラウド ネットワークをセキュリティで保護する](secure-cloud-network.md)を参照してください。
