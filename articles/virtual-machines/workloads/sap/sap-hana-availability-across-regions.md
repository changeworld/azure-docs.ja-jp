---
title: Azure リージョンの枠を超えた SAP HANA の可用性 | Microsoft Docs
description: Azure VM 上で SPA HANA を実行している場合の可用性の考慮事項について概要を説明します。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 052394884f85d527caa88ff6c9464af669f47bb5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-across-azure-regions"></a>Azure リージョンの枠を越えた SAP HANA の可用性
この記事では、さまざまな Azure リージョンにまたがる SAP HANA の可用性のシナリオについて説明します。 各 Azure リージョン間の距離は非常に長いので、特殊な考慮事項があります。この記事では、その考慮事項について説明します。

## <a name="motivation-to-deploy-across-multiple-azure-regions"></a>複数の Azure リージョンにまたがって展開する動機
各 Azure リージョン間の距離は長大です。 地政学的地域によっては、米国のように何百 km から何千 km も離れている可能性があります。 Azure リージョン間にこのような距離があるため、2 つの Azure リージョンに展開されたアセット間のネットワーク トラフィックは、ネットワーク ラウンドトリップの待機時間に大きな影響があります。 影響が大きいので、一般的な SAP ワークロードでは、2 つの SAP HANA インスタンス間で同期データの交換を行わないことも考えられます。 一方、自然災害が広範囲に発生した場合に可用性を確保するために、プライマリ データセンターとセカンダリ データセンター間の距離に関する要件が定義されているということもよくあります。 たとえば、2017 年 9 月と 10 月にカリブ海とフロリダ地方を襲ったハリケーンなどです。 また、少なくとも最短距離要件が定義されていることがあります。 ほとんどのお客様のケースでは、最短距離の定義で [Azure リージョン](https://azure.microsoft.com/regions/)をまたがる可用性を設計する必要があります。 2 つの Azure リージョン間の距離が長すぎて HANA の同期レプリケーション モードを使用できないため、RTO と RPO の要件により、1 つ目のリージョン内に可用性の構成を展開し、2 つ目のリージョンに追加の展開を補完する必要があります。

このような構成で考慮するもう 1 つの側面は、フェールオーバーとクライアントのリダイレクトです。 2 つの Azure リージョン内にある SAP HANA インスタンス間のフェールオーバーは、常に手動フェールオーバーであるという前提があります。 SAP HANA システム レプリケーションのレプリケーション モードは非同期に設定されているため、プライマリ HANA インスタンスでコミットされたデータはセカンダリ HANA インスタンスにまだ作成されていない可能性があります。 そのため、レプリケーションが非同期の構成では、自動フェールオーバーを受け入れられません。 フェールオーバーのように手動で制御されたフェールオーバーでも、プライマリ側でコミットされたすべてのデータがセカンダリ インスタンスに確実に転送されてから、別の Azure リージョンに手動で移動する必要があります。
 
2 つ目の Azure リージョンに展開されている Azure VNet では異なる IP アドレス範囲を使用しているため、SAP HANA クライアントの構成を変更するか、名前解決を変更するための手順を適切に実行する必要があります。 また、クライアントが新しいセカンダリのサーバー IP アドレスにリダイレクトされるようにする必要があります。 詳細については、[引き継ぎ後のクライアント接続の回復](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html)に関する SAP の記事を参照してください。   

## <a name="simple-availability-between-two-azure-regions"></a>2 つの Azure リージョン間の単純な可用性
単一のリージョン内に可用性の構成を設定しないというシナリオです。 ただし、災害の発生時にワークロードを提供するという要件があります。 このようなシステムの一般的な例として、非運用環境システムがあります。 半日または 1 日システムを停止することができますが、48 時間以上使用を停止することはできません。 セットアップのコストを下げるために、VM 上でターゲットとして機能する重要性の低い別のシステムを実行するか、セカンダリ リージョンの VM のサイズを小さくして、データの事前読み込みを実行しない設定にします。 フェールオーバーは手動で行われ、完全なアプリケーション スタックのフェールオーバーには手順が増えるため、VM を停止し、サイズを変更して VM を再起動するにはさらに時間がかかります。

> [!NOTE]
> HANA システム レプリケーション ターゲットでデータの事前読み込みを行わない場合であっても、少なくとも 64 GB のメモリが必要であり、ターゲット インスタンスのメモリ内の行ストア データを保持するにはさらに多くのメモリが必要です。

![2 つのリージョン上の 2 つの VM](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> この構成では、HANA システム レプリケーション モードが非同期なので、RPO = 0 を指定できません。 RPO = 0 を指定する必要がある場合、この構成は選択できません。

構成の軽微な変更でも、データの事前読み込みを構成することになる可能性があります。 ただし、手動のフェールオーバーという性質と、アプリケーション層を 2 つ目のリージョンに移行する必要があるという点を考慮すると、データの事前読み込みは意味がない可能性があります。 

## <a name="combine-availability-within-one-region-and-across-regions"></a>1 つのリージョン内の可用性とリージョンをまたがる可用性を組み合わせる 
1 つのリージョン内の可用性とリージョンをまたがる可用性の組み合わせは、次の理由で推進される可能性があります。

- 1 つの Azure リージョン内で RPO = 0 の要件がある。
- リージョンの広域に影響する大規模な自然災害の影響を受ける企業の事業について、グローバルに展開するつもりがない、または展開することができない。 たとえば、数年前にカリブ海を襲ったハリケーンのケースなどがあります。
- プライマリ サイトとセカンダリ サイト間の必要距離を規定した規制が、Azure 可用性ゾーンが提供できる範囲をはるかに超えている。

 
このような場合、SAP が HANA システム レプリケーションを使用する [SAP HANA 複数階層システム レプリケーション構成](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html)と呼ぶ構成を行います。 アーキテクチャは次のようになります。

![2 つのリージョン上の 3 つの VM](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

この構成では、プライマリ リージョン内に RTO 時間が短い RPO = 0 を用意し、さらに 2 つ目のリージョンに移行した場合には RPO を下げます。 2 つ目のリージョンの RTO 時間は、データの事前読み込みを構成するかどうかによって変わります。 多くのユーザー事例では、第 2 リージョンの VM はテスト システムの実行に使用されています。 このような使用方法なので、データを事前に読み込むことができません。

> [!NOTE]
> 階層 1 から階層 2 (プライマリ リージョンの同期レプリケーション) 方向の HANA システム レプリケーションでは、logreplay 操作モードを使用しているため、階層 2 と階層 3 (セカンダリ サイトへのレプリケーション) 間のレプリケーションを delta_datashipping 操作モードにすることはできません。 操作モードと一部の制限事項の詳細については、SAP の「[Operation Modes for SAP HANA System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html)」(SAP HANA システム レプリケーションの操作モード) を参照してください。 

## <a name="next-steps"></a>次の手順
Azure でこのような構成を設定する方法の詳細な手順については、以下の記事を参照してください。

- [Azure VM での SAP HANA システム レプリケーションのセットアップ](sap-hana-high-availability.md)
- [Azure での SAP – パート 4 – システム レプリケーションを使用する SAP HANA の高可用性](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
