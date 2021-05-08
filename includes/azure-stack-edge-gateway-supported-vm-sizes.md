---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/27/2021
ms.author: alkohli
ms.openlocfilehash: f92dc476d3bc79fdb522424e36e44a13644c725e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078802"
---
VM のサイズにより、VM で利用できる計算リソース (CPU、GPU、メモリなど) の量が決定されます。 仮想マシンは、ワークロードに適した VM サイズを使用して作成する必要があります。 すべてのマシンが同じハードウェア上で実行される場合でも、マシンのサイズによってディスク アクセスの制限が異なります。 これは、VM 間のディスク アクセス全体を管理するのに役立ちます。 ワークロードが増えた場合は、既存の仮想マシンのサイズを変更することもできます。

次の VM は、Azure Stack Edge デバイスでの作成でサポートされています。

### <a name="dv2-series"></a>Dv2 シリーズ
|サイズ     |vCPU     |メモリ (GiB) | 一時ストレージ (GiB)   | 最大データ ディスク数 | 最大 NIC 数 |
|-------------------|----|----|-----|----|------|
|**Standard_D1_v2** |1   |3.5 |50   | 4    |2 |
|**Standard_D2_v2** |2   |7   |100  | 8    |4 |
|**Standard_D3_v2** |4   |14  |200  | 16  |4 |
|**Standard_D4_v2** |8   |28  |400  | 32  |8 |
|**Standard_D5_v2** |16  |56  |800  | 64  |8 |
|**Standard_D11_v2** |2   |14  |100 | 8     |2 |
|**Standard_D12_v2** |4   |28  |200  | 16   |4 |
|**Standard_D13_v2** |8   |56  |400  | 32  |8 |

### <a name="dsv2-series"></a>DSv2 シリーズ
|サイズ     |vCPU     |メモリ (GiB) | 一時ストレージ (GiB)  | 最大データ ディスク数| 
|--------------------|----|----|----|-----|
|**Standard_DS1_v2** |1   |3.5 |4000  |4  | 
|**Standard_DS2_v2** |2   |7   |8000  |8  | 
|**Standard_DS3_v2** |4   |14  |16000 |16 | 
|**Standard_DS4_v2** |8   |28  |32000 |32 | 
|**Standard_DS5_v2** |16  |56  |64000 |64 |  
|**Standard_DS11_v2**|2   |14  |8000  |4  | 
|**Standard_DS12_v2**|4   |28  |16000 |8  | 
|**Standard_DS13_v2**|8   |56  |32000 |16 | 


詳細については、「[Dv2 および DSv2 シリーズ](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series)」を参照してください。

### <a name="ncast4_v3-series-preview"></a>NCasT4_v3 シリーズ (プレビュー)

これらのサイズは、デバイス上の GPU VM でサポートされており、コンピューティング集中型 GPU 高速化アプリケーション用に最適化されています。 このシリーズは、NVIDIA の Tesla T4 GPU を搭載した推論のワークロードにフォーカスしています。 

|サイズ     |vCPU     |メモリ (GiB) | 一時ストレージ (GiB)  | GPU | GPU メモリ (GiB) | 最大 NIC 数 |
|---------------------|----|----|-----|-----|-------|--------------|
|**Standard_NC4as_T4_v3** |4   |28  |180   |1 |16   |4 |
|**Standard_NC8as_T4_v3** |8   |56  |360   |1 |16  |8 |

詳細については、「[NCasT4_v3 シリーズ](../articles/virtual-machines/nct4-v3-series.md)」を参照してください。

### <a name="f-series"></a>F シリーズ

これらのシリーズは、計算ワークロード用に最適化され、Intel Xeon プロセッサで実行されます。 

| サイズ | vCPU の数 | メモリ:GiB | 一時ストレージ (GiB) |  最大データ ディスク数 | 最大 NIC 数 |
|---|---|---|---|---|---|
| Standard_F1  | 1  | 2   |16      | 4  |  2 |
| Standard_F2 | 2  | 4 |32      | 8  |  4 |
| Standard_F4  | 4  | 8 |64   | 16 |  4 |
| Standard_F8 | 8 | 16  |132    | 32 |  8 |
| Standard_F16 | 16 | 32  |262   | 64 |  8 |
| Standard_F1s | 1 | 2  | 4  | 4 | 1 |
| Standard_F2s | 2 | 4 |8   | 8 | 4 |
| Standard_F4s | 4 | 8 |16 | 16 |  4 |
| Standard_F8s | 8 | 16 |32 | 32 |  8 |
| Standard_F16s | 16 | 32 |64 | 64 |  8 |

詳細については、「[Fsv2 シリーズ](../articles/virtual-machines/fsv2-series.md)」を参照してください。

