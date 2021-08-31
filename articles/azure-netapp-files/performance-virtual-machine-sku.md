---
title: Azure NetApp Files 用の Azure 仮想マシン SKU のベスト プラクティス | Microsoft Docs
description: Azure 仮想マシン SKU に関する Azure NetApp Files のベスト プラクティスについて、SKU 内および他のそれとの違いを含めて説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: b-juche
ms.openlocfilehash: 6644a52ee094ce0076293b37fa79db28568c9c67
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113233480"
---
# <a name="azure-virtual-machine-skus-best-practices-for-azure-netapp-files"></a>Azure NetApp Files 用の Azure 仮想マシン SKU のベスト プラクティス

この記事では、Azure 仮想マシン SKU に関する Azure NetApp Files のベスト プラクティスについて、SKU 内および他のそれとの違いを含めて説明します。   

## <a name="sku-selection-considerations"></a>SKU の選択に関する考慮事項

ストレージのパフォーマンスは、そのストレージ自体の速度よりも関連します。 プロセッサの速度とアーキテクチャは、特定のコンピューティング ノードからの全体的なエクスペリエンスと大きく関連します。 特定の SKU の選択プロセスの一環として、次の要因を考慮する必要があります。

* AMD または Intel: たとえば、SAS では、Intel プロセッサ専用に設計された Math Kernel Library が使用されます。  この場合、Intel SKU は AMD SKU より優先されます。
* F2、E_v3、および D_v3 マシンの種類は、それぞれ複数のチップセットに基づいています。  Azure Dedicated Host を使用する場合は、特定のモデル (たとえば、E の種類を選択するときは Broadwell、Cascade Lake、または Skylake) を選択できます。 それ以外の場合、チップセットの選択は非決定的となります。  HPC クラスターをデプロイする際に、インベントリ全体で一貫したエクスペリエンスが重要である場合は、単一の Azure Dedicated Host を検討するか、E_v4 や D_v4 などの単一のチップセット SKU を使用することができます。
* ネットワーク接続ストレージ (NAS) でのパフォーマンスの変動は、Intel Broadwell ベースの SKU と AMD EPYC™ 7551 ベースの SKU の両方を使用したテストで確認されています。 次の 2 つの問題が確認されています。
    * 高速ネットワーク インターフェイスが最適でない NUMA ノードに不適切にマップされている場合、読み取りパフォーマンスが大幅に低下します。   高速ネットワーク インターフェイスを特定の NUMA ノードにマッピングすることは新しい SKU には有益ですが、これらのチップセット (Lv2|E_v3|D_v3) を使用する SKU の要件と見なす必要があります。
    * Lv2 で実行されている仮想マシン、あるいは Broadwell チップセットで実行されている E_v3 または D_v3 は、他の SKU で実行される場合よりもリソースの競合の影響を受けやすくなります。  単一の Azure Dedicated Host 内で実行されている複数の仮想マシンを使用してテストするときに、1 つの仮想マシンからネットワーク ベースのストレージ ワークロードを実行すると、2 つ目の仮想マシンから実行するネットワーク ベースのストレージ ワークロードのパフォーマンスの低下が確認されました。 ノード上の仮想マシンのいずれかに高速ネットワーク インターフェイスまたは NUMA ノードが最適にマップされていなかった場合は、この低下がより顕著になります。  E_v3 と D_V3 は、Haswell、Broadwell、Cascade Lake、または Skylake に配置される可能性があることにご注意ください。 

仮想マシンを選択する際に、最も一貫したパフォーマンスを得るには、単一の種類のチップセットを使用する SKU から選択します。使用可能な場合は、古いモデルよりも新しい SKU が優先されます。  専用ホストを使用する場合を除き、E_v3 または D_v3 仮想マシンが配置されるハードウェアの種類を正しく予測する可能性は低くなることにご注意ください。  E_v3 または D_v3 SKU を使用する場合は以下のようになります。

* 仮想マシンをオフにし、割り当てを取り消してから、再度オンにすると、その仮想マシンでホストが (そのためハードウェア モデルも) 変更される可能性があります。
* 複数の仮想マシンにまたがってアプリケーションがデプロイされている場合は、異種ハードウェア上で仮想マシンが実行されることが予想されます。

## <a name="differences-within-and-between-skus"></a>SKU 内のおよび他のそれとの違い
 
次の表では、SKU 内のおよび他のそれとの違いに焦点を当てています。  たとえば、基になる E_v3 と D_v3 のチップセットは、Broadwell、Cascade Lake、Skylake の間で異なることに注意してください (D_v3 の場合も同様)。  

|     ファミリ    |     バージョン    |   説明     |     周波数 (GHz)    |
|-|-|-|-|
|     E    |     V3    |     Intel® Xeon® E5-2673   v4 (Broadwell)    |     2.3 (3.6)    |
|     E    |     V3    |     Intel® Xeon®   Platinum 8272CL (Cascade Lake)    |     2.6 (3.7)    |
|     E    |     V3    |     Intel® Xeon® Platinum   8171M (Skylake)    |     2.1 (3.8)    |
|     E    |     V4    |     Intel® Xeon®   Platinum 8272CL (Cascade Lake)    |     2.6 (3.7)    |
|     Ea    |     V4    |     AMD EPYC™ 7452    |     2.35 (3.35)    |
|     D    |     V3    |     Intel® Xeon®   E5-2673 v4 (Broadwell)    |     2.3 (3.6)    |
|     D    |     V3    |     Intel® Xeon® E5-2673   v3 (Haswell)    |     2.3 (2.3)    |
|     D    |     V3    |     Intel® Xeon®   Platinum 8272CL (Cascade Lake)    |     2.6 (3.7)    |
|     D    |     V3    |     Intel® Xeon® Platinum   8171M (Skylake)    |     2.1 (3.8)    |
|     D    |     V4    |     Intel® Xeon® Platinum   8272CL (Cascade Lake)    |     2.6 (3.7)    |
|     Da    |     V4    |     AMD EPYC™ 7452    |     2.35 (3.35)    |
|     L    |     V2    |     AMD EPYC™   7551    |     2.0 (3.2)    |
|     F    |     1    |     Intel Xeon® E5-2673 v3 (Haswell)     |     2.3 (2.3)    |
|     F    |     2    |     Intel® Xeon®   Platinum 8168M (Cascade Lake)    |     2.7 (3.7)    |
|     F    |     2    |     Gen 2 Intel® Xeon® Platinum 8272CL (Skylake)    |     2.1 (3.8)   |

複数ノードの SAS GRID 環境を実稼働用に準備するときに、分析の実行間で 1 時間 15 分の差異が繰り返し見られ、基になるハードウェア以外の違いがないことに気付くかもしれません。  

|     SKU とハードウェア   プラットフォーム    |     ジョブの実行時間    |
|-|-|
|     E32-8_v3 (Broadwell)    |     5.5 時間    |
|     E32-8_v3 (Cascade   Lake)    |     4.25 時間    |

両方のテスト セットで、8_v3 E32 SKU が選択され、RHEL 8.3 が `nconnect=8` マウント オプションと共に使用されました。

## <a name="best-practices"></a>ベスト プラクティス 

* できる限り、E_v3 や D_v3 SKU ではなく、E_v4、D_v4、またはそれ以降を選択してださい。  
* できる限り、L2 SKU ではなく、Ed_v4、Dd_v4、またはそれ以降を選択してください。

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files 用の Linux のダイレクト I/O のベスト プラクティス](performance-linux-direct-io.md)
* [Azure NetApp Files の Linux ファイルシステム キャッシュのベスト プラクティス](performance-linux-filesystem-cache.md)
* [Azure NetApp Files 用の Linux NFS マウント オプションのベスト プラクティス](performance-linux-mount-options.md)
* [Linux コンカレンシーのベスト プラクティス](performance-linux-concurrency-session-slots.md)
* [Linux NFS 先読みのベスト プラクティス](performance-linux-nfs-read-ahead.md)
* [Linux のパフォーマンス ベンチマーク](performance-benchmarks-linux.md) 
