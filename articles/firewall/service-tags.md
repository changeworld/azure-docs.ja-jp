---
title: Azure Firewall サービス タグの概要
description: サービス タグは IP アドレス プレフィックスのグループを表し、セキュリティ規則の作成の複雑さを最小限に抑えるのに役立ちます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 47377817b62d33e8af79e4a0d2dceb68ba9dbdc5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658649"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall サービス タグ

サービス タグは IP アドレス プレフィックスのグループを表し、セキュリティ規則の作成の複雑さを最小限に抑えるのに役立ちます。 独自のサービス タグを作成したり、タグに含まれる IP アドレスを指定したりすることはできません。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

Azure Firewall サービス タグは、ネットワーク ルールの宛先フィールドで使用できます。 特定の IP アドレスの代わりにそれらを使用できます。

## <a name="supported-service-tags"></a>サポートされるサービス タグ

Azure ファイアウォール ネットワーク ルール内で使用できるサービス タグの一覧については、「[セキュリティ グループ](../virtual-network/network-security-groups-overview.md#service-tags)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Firewall ルールの詳細については、「[Azure Firewall ルール処理ロジック](rule-processing.md)」を参照してください。