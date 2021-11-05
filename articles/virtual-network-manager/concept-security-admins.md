---
title: Azure Virtual Network Manager のセキュリティ管理ルール (プレビュー)
description: Azure Virtual Network Manager のセキュリティ管理ルールについて説明します。
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: c56df99e68dc40886f01a65fe101fa142a04c43e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092110"
---
# <a name="security-admin-rules-in-azure-virtual-network-manager-preview"></a>Azure Virtual Network Manager のセキュリティ管理ルール (プレビュー)

Azure Virtual Network Manager には、仮想ネットワーク全体にデプロイできる2種類の構成が用意されています。1つは *SecurityAdmin (セキュリティ管理者)* 構成です。 セキュリティ管理者の構成には、一連のルールのコレクションが含まれます。 各ルールコレクションには、1つ以上のセキュリティ管理ルールが含まれています。 次に、セキュリティ管理ルールを適用するネットワークグループにルールコレクションを関連付けます。

> [!IMPORTANT]
> 現在、Azure Virtual Network Manager は、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="security-admin-rules"></a>セキュリティ管理規則

セキュリティ管理ルールを使用すると、設定した条件に一致するセキュリティポリシーの条件を適用できます。 リソースのセキュリティ管理ルールは、Azure Virtual Network Manager インスタンスのスコープ内でのみ定義できます。 これらのセキュリティルールは、ネットワークセキュリティグループ (NSG) のルールよりも優先順位が高く、NSG ルールの前に評価されます。 たとえば、管理者は、セキュリティ管理者ルールを使用して、インターネットからの危険度の高いすべてのポートまたはプロトコルを拒否できます。これらのルールは、VM またはサブネットレベルで作成された許可済み NSG ルールを上書きします。

> [!IMPORTANT]
> 一部のサービスには、トラフィックがサービスに対して必要に応じて動作していることを確認するためのネットワークインテントポリシーがあります。 セキュリティ管理ルールを使用すると、これらのサービスのために作成されたネットワークインテントポリシーを解除できます。 たとえば、拒否管理者ルールを作成すると、 *SQL マネージインスタンス* サービスによって許可されている一部のトラフィックがブロックされる可能性があります。これは、ネットワークインテントポリシーによって定義されます。 セキュリティ管理者の構成を適用する前に、環境を必ず確認してください。 

セキュリティ管理者ルールで定義できるフィールドは次のとおりです。

## <a name="required-fields"></a>必須フィールド

### <a name="priority"></a>Priority

セキュリティルールの優先度は 0～99 の整数によって決定されます。 値が小さいほど、ルールの優先度が高くなります。 たとえば、優先度が10の拒否ルールは、優先度が20の許可ルールより優先されます。 

### <a name="action"></a><a name = "action"></a>アクション

セキュリティルールに対して、次の3つの操作のいずれかを定義できます。

* **許可**: 特定のポート、プロトコル、および送信元/送信先 IP プレフィックスのトラフィックを指定した方向に許可します。
* **拒否**: 指定したポート、プロトコル、および送信元/送信先 IP プレフィックスのトラフィックを指定した方向にブロックします。
* **常に許可**: 優先順位の低い他のルールまたはユーザー定義NSGに関係なく、指定されたポート、プロトコル、および送信元/送信先 IP プレフィックスのトラフィックを指定された方向に許可します。

### <a name="direction"></a>Direction

ルールを適用するトラフィックの方向を指定できます。 受信または送信のどちらかを定義できます。

### <a name="protocol"></a>Protocol

セキュリティ管理ルールで現在サポートされているプロトコルは次のとおりです。

* TCP
* UDP
* ICMP
* ESP
* AH
* 任意のプロトコル

## <a name="optional-fields"></a>省略可能なフィールド

### <a name="source-and-destination-types"></a>変換元と変換先の種類

* **IP アドレス**: IPv4 または IPv6 アドレス、またはアドレスのブロックを CIDR 表記で指定できます。 複数の IP アドレスを一覧表示するには、各 IP アドレスをコンマで区切ります。
* **サービスタグ**: リージョンまたはサービス全体に基づいて特定のサービスタグを定義できます。 サポートされているタグの一覧については、「[利用可能なサービス タグ](../virtual-network/service-tags-overview.md#available-service-tags)」を参照してください。

### <a name="source-and-destination-ports"></a>送信元と送信先のポート

ソースまたは宛先に対してブロックする特定の共通ポートを定義できます。 一般的な TCP ポートの一覧については、以下を参照してください。

| Port | [サービス名] |
| ----- | ------------ |
| 20、21 | FTP |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP |
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS |
| 3389 | RDP |

## <a name="next-steps"></a>次の手順 

[SecurityAdmin 構成](how-to-block-network-traffic-portal.md)を使用してネットワーク トラフィックをブロックする方法を学びます。
