
GPU 最適化済み VM サイズは、1 つまたは複数の NVIDIA GPU を備えた、特殊な用途に特化した仮想マシンです。 これらのサイズは、コンピューティング処理やグラフィック処理の負荷が高い視覚化ワークロードを意図して設計されています。 この記事では、このグループ内の各サイズのストレージのスループットとネットワーク帯域幅に加え、GPU、vCPU、データ ディスク、NIC の数や種類に関する情報を提供します。 

* **NC、NCv2、ND** の各サイズは、CUDA や OpenCL ベースのアプリケーションやシミュレーション、AI、ディープ ラーニングなどの、コンピューティング集中型およびネットワーク集中型のアプリケーション、アルゴリズム用に最適化されています。 
* **NV** サイズは、リモートの視覚化、ストリーミング、ゲーム、エンコーディング、および OpenGL や DirectX などのフレームワークを使用する VDI シナリオ用に最適化されています。  


## <a name="nc-instances"></a>NC インスタンス

NC インスタンスは [NVIDIA の Tesla K80](http://images.nvidia.com/content/pdf/kepler/Tesla-K80-BoardSpec-07317-001-v05.pdf) カードを備えています。 エネルギー調査アプリケーション向け CUDA やクラッシュ シミュレーション、レイ トレーシング レンダリング、ディープ ラーニングなどを活用することで、データをさらに高速に処理することができます。 NC24r 構成には、密結合並列コンピューティングのワークロード向けに最適化された、低待機時間かつ高スループットのネットワーク インターフェイスが搭載されています。


| サイズ | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | GPU | 最大データ ディスク数 |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 | 24 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 |
| Standard_NC24 |24 |224 | 1440 | 4 | 64 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 64 |

1 GPU = K80 カードの 2 分の 1 相当。

* RDMA 対応

## <a name="ncv2-instances"></a>NCv2 インスタンス

NCv2 インスタンスは、[NVIDIA Tesla P100](http://images.nvidia.com/content/tesla/pdf/nvidia-tesla-p100-datasheet.pdf) の GPU を搭載した次世代の NC シリーズ マシンです。 これらの GPU は、現在の NC シリーズの 2 倍以上の計算性能を有しています。 貯留層モデリング、DNA シーケンシング、タンパク質解析、モンテ カルロ シミュレーションをはじめとする従来の HPC ワークロードに、これらの最新の GPU を活用することができます。 NC シリーズと同様、NCv2 シリーズでは、密結合の並列コンピューティングのワークロード向けに最適化された、低待機時間かつ高スループットのネットワーク インターフェイスを搭載した構成も利用できます。

> [!IMPORTANT]
> このサイズ ファミリーでは、ご利用のサブスクリプションの vCPU (コア) クォータが、各リージョンで 0 に初期設定されています。 このファミリーについては、[提供リージョン](https://azure.microsoft.com/regions/services/)で [vCPU クォータの引き上げを要求](../articles/azure-supportability/resource-manager-core-quotas-request.md)してください。
>

| サイズ | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | GPU | 最大データ ディスク数 |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6s_v2 |6 |112 | 336 | 1 | 12 |
| Standard_NC12s_v2 |12 |224 | 672 | 2 | 24 |
| Standard_NC24s_v2 |24 |448 | 1344 | 4 | 32 |
| Standard_NC24rs_v2* |24 |448 | 1344 | 4 | 32 |

1 GPU = P100 カード 1 枚

* RDMA 対応

## <a name="nd-instances"></a>ND インスタンス

ND シリーズは、AI やディープ ラーニングのワークロードを想定して GPU ファミリーに新たに追加された仮想マシンです。 トレーニングや推論で優れたパフォーマンスを発揮します。 ND インスタンスは [NVIDIA Tesla P40](http://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPU を備えています。 これらのインスタンスは、Microsoft Cognitive Toolkit、TensorFlow、Caffe などのフレームワークを活用する AI ワークロードの単精度浮動小数点演算において、非常に高いパフォーマンスを発揮します。 ND シリーズでは GPU のメモリ サイズ (24 GB) も大幅に増強されているため、より大規模なニューラル ネット モデルにも対応できます。 NC シリーズと同様に、ND シリーズでは 2 番目に少ない待機時間、RDMA を利用した高スループットのネットワーク、InfiniBand との接続性などを備えた構成が利用できます。これにより、多数の GPU を利用した大規模なトレーニング ジョブを実行できます。

> [!IMPORTANT]
> このサイズ ファミリーでは、ご利用のサブスクリプションの vCPU (コア) クォータが、各リージョンで 0 に初期設定されています。 このファミリーについては、[提供リージョン](https://azure.microsoft.com/regions/services/)で [vCPU クォータの引き上げを要求](../articles/azure-supportability/resource-manager-core-quotas-request.md)してください。
>

| サイズ | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | GPU | 最大データ ディスク数 |
| --- | --- | --- | --- | --- | --- |
| Standard_ND6s |6 |112 | 336 | 1 | 12 |
| Standard_ND12s |12 |224 | 672 | 2 | 24 |
| Standard_ND24s |24 |448 | 1344 | 4 | 32 |
| Standard_ND24rs* |24 |1448 | 1344 | 4 | 32 |

1 GPU = P40 カード 1 枚

* RDMA 対応

## <a name="nv-instances"></a>NV インスタンス



NV インスタンスは、[NVIDIA の Tesla M60](http://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU およびデスクトップ アクセラレータ アプリケーションや仮想デスクトップ向けの NVIDIA GRID テクノロジを備えていて、お客様は、データやシミュレーションを視覚化することができます。 NV インスタンスでは、グラフィックス処理を要するワークフローを視覚化して優れたグラフィックス機能を活用し、さらにエンコードやレンダリングなどの単精度のワークロードを実行することもできます。 

| サイズ | vCPU | メモリ: GiB | 一時ストレージ (SSD) GiB | GPU | 最大データ ディスク数 |
| --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 | 24 |
| Standard_NV12 |12 |112 |680 | 2 | 48 |
| Standard_NV24 |24 |224 |1440 | 4 | 64 |

1 GPU = M60 カードの 2 分の 1 相当。


