---
title: Azure リージョンの枠を超えた SAP HANA の可用性 | Microsoft Docs
description: 複数の Azure リージョンの Azure VM 上で SAP HANA を実行している場合の可用性の考慮事項の概要。
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
ms.openlocfilehash: edbd1885dd529e4ccd38f2012d56865a2147f64d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/05/2018
ms.locfileid: "30842273"
---
# <a name="sap-hana-availability-across-azure-regions"></a>Azure リージョンの枠を越えた SAP HANA の可用性

この記事では、さまざまな Azure リージョンにまたがる SAP HANA の可用性に関連するシナリオについて説明します。 Azure リージョン間の距離が原因で、複数の Azure リージョンでの SAP HANA の可用性の設定には特別な考慮事項があります。

## <a name="why-deploy-across-multiple-azure-regions"></a>複数の Azure リージョンにまたがって展開する理由

多くの場合、Azure リージョンは長い距離で隔てられています。 地理的リージョンによっては、米国でのように Azure リージョン間の距離が何百 km から何千 km にもなる可能性があります。 このような距離が原因で、2 つの異なる Azure リージョンに展開されたアセット間のネットワーク トラフィックは、ネットワーク ラウンドトリップの待機時間が長くなります。 待機時間が長いので、一般的な SAP ワークロードでは、2 つの SAP HANA インスタンス間で同期データの交換を行わないことも考えられます。 

その一方で、多くの場合、組織にはプライマリ データセンターの場所とセカンダリ データセンターの場所の間に距離の要件があります。 距離の要件は、地理的に広い場所で自然災害が発生した場合に可用性を確保するのに役立ちます。 たとえば、2017 年 9 月と 10 月にカリブ海とフロリダを襲ったハリケーンなどです。 組織には、少なくとも最短距離の要件がある可能性があります。 ほとんどの Azure のお客様では、最短距離の定義で [Azure リージョン](https://azure.microsoft.com/regions/)をまたがる可用性の設計が要求されています。 2 つの Azure リージョン間の距離が長すぎて HANA の同期レプリケーション モードを使用できないため、RTO と RPO の要件により、1 つ目のリージョン内に可用性の構成を展開し、2 つ目のリージョンの追加の展開で補完する必要があります。

このシナリオで考慮すべきもう 1 つの側面は、フェールオーバーとクライアントのリダイレクトです。 2 つの Azure リージョン内にある SAP HANA インスタンス間のフェールオーバーは、常に手動フェールオーバーであるという前提があります。 SAP HANA システム レプリケーションのレプリケーション モードは非同期に設定されているため、プライマリ HANA インスタンスでコミットされたデータはセカンダリ HANA インスタンスにまだ作成されていない可能性があります。 そのため、レプリケーションが非同期の構成では、自動フェールオーバーを選択できません。 フェールオーバーのように手動で制御されたフェールオーバーでも、プライマリ側でコミットされたすべてのデータがセカンダリ インスタンスに確実に転送されてから、別の Azure リージョンに手動で移動する必要があります。
 
Azure Virtual Network では、異なる IP アドレス範囲を使用します。 IP アドレスは、2 番目の Azure リージョンに展開されます。 そのため、SAP HANA クライアント構成を変更するか、可能であれば、名前解決を変更するステップを作成する必要があります。 これにより、クライアントは新しいセカンダリ サイトのサーバー IP アドレスにリダイレクトされます。 詳しくは、[引き継ぎ後のクライアント接続の回復](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html)に関する SAP の記事をご覧ください。   

## <a name="simple-availability-between-two-azure-regions"></a>2 つの Azure リージョン間の単純な可用性

1 つのリージョン内に可用性構成を配置しないことを選択しても、災害が発生した場合にはワークロードを処理することが必要な場合があります。 このようなシステムの一般的な例として、非運用環境システムがあります。 システムを半日または 1 日停止することには耐えられても、48 時間以上システムを利用不可にすることはできません。 セットアップ コストを低く抑えるには、重要度の低い別のシステムを VM で実行します。 他のシステムは同期先として機能します。 セカンダリ リージョンの VM のサイズを小さくして、データを事前に読み込まないことも選択できます。 フェールオーバーは手動で行われ、完全なアプリケーション スタックをフェールオーバーするには手順が増えるため、VM を停止し、サイズを変更して VM を再起動するための追加の時間は許容されます。

> [!NOTE]
> HANA システム レプリケーション ターゲットでデータの事前読み込みを使用しない場合でも、少なくとも 64 GB のメモリが必要です。 64 GB に加えて、ターゲット インスタンスのメモリに行ストア データを保持するための十分なメモリも必要です。

![2 つのリージョン上の 2 つの VM のダイアグラム](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> この構成では、HANA システム レプリケーション モードが非同期なので、RPO = 0 を指定できません。 RPO = 0 を指定する必要がある場合、この構成は選択できません。

データを事前読み込みとして構成するために、構成で小さな変更を行える可能性があります。 ただし、手動のフェールオーバーという性質と、アプリケーション レイヤーを 2 つ目のリージョンに移行する必要があるという点も考慮すると、データの事前読み込みは意味がない可能性があります。 

## <a name="combine-availability-within-one-region-and-across-regions"></a>1 つのリージョン内の可用性とリージョンをまたがる可用性を組み合わせる 

1 つのリージョン内の可用性とリージョンをまたがる可用性の組み合わせは、次の要因で推進される可能性があります。

- 1 つの Azure リージョン内で RPO = 0 の要件がある。
- リージョンの広域に影響する大規模な自然災害の影響を受ける事業について、組織がグローバルに展開するつもりがない、または展開することができない。 これは、数年前にカリブ海を襲ったハリケーンのケースです。
- プライマリ サイトとセカンダリ サイト間の必要距離を規定した規制が、Azure 可用性ゾーンが提供できる範囲を明らかに超えている。

このような場合、HANA システム レプリケーションを使用して、SAP が [SAP HANA 複数階層システム レプリケーション構成](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html)と呼ぶ構成を設定できます。 アーキテクチャは次のようになります。

![2 つのリージョン上の 3 つの VM のダイアグラム](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

この構成は、プライマリ リージョン内に RPO = 0、低 RTO を提供します。 構成は、2 つ目のリージョンへの移行が必要な場合にも適切な RPO を提供します。 2 つ目のリージョンの RTO 時間は、データが事前に読み込まれるかどうかによって変わります。 多くのお客様は、セカンダリ リージョンの VMを 使用してテスト システムを実行します。 このユース ケースでは、データを事前読み込みできません。

> [!NOTE]
> 階層 1 から階層 2 (プライマリ リージョンの同期レプリケーション) の方向の HANA システム レプリケーションでは、**logreplay** 操作モードを使用しているため、階層 2 と階層 3 (セカンダリ サイトへのレプリケーション) 間のレプリケーションを **delta_datashipping** 操作モードにすることはできません。 操作モードと一部の制限事項について詳しくは、SAP の [SAP HANA システム レプリケーションの操作モード](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html)に関する記事をご覧ください。 

## <a name="next-steps"></a>次の手順

Azure でのこれらの構成の設定手順については、以下をご覧ください。

- [Azure VM での SAP HANA システム レプリケーションのセットアップ](sap-hana-high-availability.md)
- [システム レプリケーションを使用する SAP HANA の高可用性](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
