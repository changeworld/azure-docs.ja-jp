---
title: "Azure の高可用性ポートの概要 | Microsoft Docs"
description: "内部 Load Balancer 上の高可用性ポートの負荷分散について説明します"
services: load-balancer
documentationcenter: na
author: rdhillon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/26/2017
ms.author: kumud
ms.openlocfilehash: 3e54cb45cf002a183a5b0bd9b3082a235cd825f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="high-availability-ports-overview-preview"></a>高可用性ポートの概要 (プレビュー)

Azure Load Balancer の Standard SKU は高可用性 (HA) ポートを導入しています。HA ポートは、すべてのサポートされるプロトコルについて、すべてのポートのトラフィックを分散する機能です。 ユーザーは、内部 Load Balancer を構成するときに、フロントエンドとバックエンドのポートを **0** に設定し、プロトコルを **all** に設定する HA ポート ルールを構成し、すべてのトラフィックが内部 Load Balancer を経由するようにすることができます。

>[!NOTE]
> 高可用性ポート機能は、Load Balancer Standard で利用できますが、現在プレビューの段階です。 プレビュー期間は、一般公開リリースの機能と同じレベルの可用性と信頼性がない場合があります。 詳細については、[Microsoft Azure プレビューのMicrosoft Azure 追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 Load Balancer Standard リソースで HA ポートを使用するには、Load Balancer Standard プレビューにサインアップする必要があります。 Load Balancer Standard プレビューと、サインアップの手順については、[こちら](https://aka.ms/lbpreview#preview-sign-up)を参照してください。

負荷分散アルゴリズムは同じままです。また、分散先は 5 つのタプル <ソース IP アドレス、ソース ポート、分散先 IP アドレス、分散先ポート、プロトコル> に基づいて選択されます。 ただし、この構成では、すべての使用できるトラフィックを処理するために 1 つの LB ルールを使用できるため、構成の複雑さが軽減されるだけでなく、追加できる負荷分散ルールの最大数によって制限が課せられます。

HA ポートで可能になる重要なシナリオの 1 つとして、Azure 仮想ネットワークへのネットワーク仮想アプライアンスの高可用性デプロイがあります。 また、HA ポートで可能になる一般的なシナリオとして、ポートの範囲に対する負荷分散があります。 

## <a name="why-use-high-ha-ports"></a>高い HA ポートを使用する理由

Azure ユーザーは、さまざまなセキュリティの脅威からセキュリティでワークロードを保護するために、ネットワーク仮想アプライアンス (NVA) に大きく依存しています。 また、NVA は信頼性が高く、高可用である必要があります。  

HA ポートを使用すると、Zookeeper のように複雑なソリューションが不要になるため、NVA HA シナリオの複雑さが軽減されます。また、迅速なフェールオーバーとスケールアウト オプションで信頼性が向上します。 Azure 内部のロード バランサーのバックエンド プールに NVA を追加し、HA ポートのロード バランサー ルールを構成することで、NVA HA を実現できるようになりました。

次の例は、ハブとスポークの仮想ネットワーク デプロイを示しています。スポークによってトラフィックをハブの仮想ネットワークへのトンネリングを強制し、NVA を経由してから、信頼される空間を離れます。 NVA は、HA ポート構成の内部 Load Balancer の背後にあるため、それに従ってすべてのトラフィックを処理し、転送します。 

![HA ポートの例](./media/load-balancer-ha-ports-overview/nvaha.png)

図 1 - HA モードで NVA がデプロイされているハブとスポークの仮想ネットワーク


## <a name="region-availability"></a>利用可能なリージョン

HA ポートは現在、次のリージョンでご利用いただけます。
- 米国東部 2
- 米国中央部
- 北ヨーロッパ
- 米国中西部
- 西ヨーロッパ
- 東南アジア 

## <a name="preview-sign-up"></a>プレビューのサインアップ

Load Balancer Standard SKU の HA ポート機能のプレビューに参加するには、PowerShell または Azure CLI 2.0 を使用してサブスクリプションを登録します。

- PowerShell を使用したサインアップ

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowILBAllPortsRule -ProviderNamespace Microsoft.Network
    ```

- Azure CLI 2.0 を使用したサインアップ

    ```cli
    az feature register --name AllowILBAllPortsRule --namespace Microsoft.Network 
    ```  


>[!NOTE]
>この機能を使用するには、HA ポートに加えて、Load Balancer [Standard プレビュー](https://aka.ms/lbpreview#preview-sign-up)にもサインアップする必要があります。 HA ポートまたは Load Balancer Standard プレビューの登録には、最大で 1 時間ほどかかる場合があります。

## <a name="limitations"></a>制限事項

HA ポートでサポートされる構成または例外は次のとおりです。

- 1 つのフロントエンド IP 構成は、HA ポート (すべてのポート) が指定された 1 つの DSR Load Balancer ルールを持つことができます。または、HA ポート (すべてのポート) が指定された 1 つの DSR 以外の Load Balancer ルールを持つことができます。 両方を持つことはできません。
- 1 つのネットワーク インターフェイス IP 構成は、HA ポートが指定された DSR 以外のロード バランサー ルールを 1 つのみ持つことができます。 この ipconfig には、他のルールを構成できません。
- 個々のフロントエンド IP 構成のすべてが一意であれば、1 つのネットワーク インターフェイス IP 構成は、HA ポートが指定された 1 つまたは複数の DSR ロード バランサー ルールを持つことができます。
- すべての Load Balancer ルールが HA ポート (DSR のみ) の場合、またはすべてのルールが HA ポート以外 (DSR および DSR 以外) の場合、同じバックエンド プールを示す複数の Load Balancer ルールが共存することができます。 このような 2 つの LB ルールは、HA ポートと HA ポート以外のルールの組み合わせがある場合は共存できません。
- IPv6 対応テナントでは HA ポートを使用できません。


## <a name="next-steps"></a>次のステップ

[内部 Load Balancer で HA ポートを構成する](load-balancer-configure-ha-ports.md)

