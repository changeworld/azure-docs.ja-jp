---
title: SAP HANA on Azure (L インスタンス) のストレージ アーキテクチャ | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) をデプロイする方法のストレージ アーキテクチャ。
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 614d6aef4a2b7be551574fd3c8e25e2a3e3c1c07
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2018
ms.locfileid: "44030957"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>SAP HANA (Large Instances) のストレージ アーキテクチャ

SAP HANA on Azure (L インスタンス) の記憶域のレイアウトは、SAP が推奨するガイドラインを使用して、クラシック デプロイ モデルで SAP HANA によって構成されます。 これらのガイドラインは、ホワイト ペーパー「[SAP HANA Storage Requirements (SAP HANA ストレージ要件)](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)」に記載されています。

Type I クラスの HANA L インスタンスは、ストレージ ボリュームの 4 倍のメモリ ボリュームを備えています。 Type II クラスの HANA L インスタンス ユニットの場合、ストレージが 4 倍以上になることはありません。 これらのユニットは、HANA トランザクション ログ バックアップを保存するためのボリュームを備えています。 詳細については、[SAP HANA on Azure (L インスタンス) のインストールと構成](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。

記憶域割り当てに関しては、次の表を参照してください。 この表は、各種 HANA L インスタンス ユニットで提供されるさまざまなボリュームのおおよその容量を示しています。

| HANA L インスタンス SKU | hana/data | hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |1,280 GB | 768 GB |
| S192 | 4,608 GB | 1,024 GB | 1,536 GB | 1,024 GB |
| S192m | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S192xm |  11,520 GB |  1,536 GB |  1,792 GB |  1,536 GB |
| S384 | 11,520 GB | 1,536 GB | 1,792 GB | 1,536 GB |
| S384m | 12,000 GB | 2,050 GB | 2,050 GB | 2,040 GB |
| S384xm | 16,000 GB | 2,050 GB | 2,050 GB | 2,040 GB |
| S384xxm |  20,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S576m | 20,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S576xm | 31,744 GB | 4,096 GB | 2,048 GB | 4,096 GB |
| S768m | 28,000 GB | 3,100 GB | 2,050 GB | 3,100 GB |
| S768xm | 40,960 GB | 6,144 GB | 4,096 GB | 6,144 GB |
| S960m | 36,000 GB | 4,100 GB | 2,050 GB | 4,100 GB |


実際にデプロイされるボリュームは、デプロイと、ボリューム サイズの表示に使用するツールによって異なる場合があります。

HANA L インスタンス SKU を分割する場合、考えられる各分割部分の例は次のようになります。

| メモリ パーティション (GB) | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1,024 GB | 640 GB |
| 1,536 | 3,328 GB | 768 GB | 1,280 GB | 768 GB |


これらのサイズはおおよそのボリューム数であり、デプロイと、ボリュームの確認に使用するツールによって若干異なる場合があります。 他のパーティション サイズ (2.5 TB など) もあります。 これらのストレージ サイズは、上記のパーティションに使用したものと同様の数式で計算されます。 "パーティション" という用語は、オペレーティング システム、メモリ、または CPU リソースがパーティション分割されていることを意味するわけではありません。 これは、1 つの HANA L インスタンス ユニットにデプロイできるさまざまな HANA インスタンスのストレージ パーティションを示しています。 

ストレージを増やすことが必要な場合があります。 ストレージを追加するには、1 TB 単位で追加のストレージを購入します。 この追加のストレージは、ボリュームとして追加できます。 また、1 つ以上の既存のボリュームを拡張するために使用することもできます。 もともとデプロイされているボリュームのサイズ (上記の表に記載) を減らすことはできません。 また、ボリュームの名前やマウント名を変更することもできません。 上記のストレージ ボリュームは、NFS4 ボリュームとして HANA L インスタンス ユニットに接続されます。

バックアップ/復元とディザスター リカバリーのために、ストレージ スナップショットをご利用いただけます。 詳細については、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

ご使用のシナリオの記憶域レイアウトの詳細については、[HLI でサポートされるシナリオ](hana-supported-scenario.md)に関する記事を参照してください。

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>1 つの HANA L インスタンス ユニットでの複数の SAP HANA インスタンスの実行

HANA L インスタンス ユニットで複数のアクティブな SAP HANA インスタンスをホストできます。 ストレージ スナップショットとディザスター リカバリーの機能を提供するために、このような構成ではインスタンスごとにボリューム セットが必要になります。 現時点では、HANA L インスタンス ユニットを次のように分けることができます。

- **S72、S72m、S144、S192**: 256 GB の最小ユニットから 256 GB ずつ増加。 256 GB、512 GB などの異なる増分値をユニットの最大メモリまで組み合わせることができます。
- **S144m、S192m**: 512 GB の最小ユニットから 256 GB ずつ増加。 512 GB、768 GB などの異なる増分値をユニットの最大メモリまで組み合わせることができます。
- **Type II クラス**: 2 TB の最小ユニットから 512 GB ずつ増加。 512 GB、1 TB、1.5 TB などの異なる増分値をユニットの最大メモリまで組み合わせることができます。

複数の SAP HANA インスタンスの実行例は次のようになります。

| SKU | メモリ サイズ | ストレージ サイズ | 複数のデータベースとサイズ |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 768 GB HANA インスタンス x 1<br /> または 512 GB インスタンス x 1 + 256 GB インスタンス x 1<br /> または 256 GB インスタンス x 3 | 
| S72m | 1.5 TB | 6 TB | 512 GB HANA インスタンス x 3<br />または 512 GB インスタンス x 1 + 1 TB インスタンス x 1<br />または 256 GB インスタンス x 6<br />または 1.5 TB インスタンス x 1 | 
| S192m | 4 TB | 16 TB | 512 GB インスタンス x 8<br />または 1 TB インスタンス x 4<br />または 512 GB インスタンス x 4 + 1 TB インスタンス x 2<br />または 768 GB インスタンス x 4 + 512 GB インスタンス x 2<br />または 4 TB インスタンス x 1 |
| S384xm | 8 TB | 22 TB | 2 TB インスタンス x 4<br />または 4 TB インスタンス x 2<br />または 3 TB インスタンス x 2 + 2 TB インスタンス x 1<br />または 2.5 TB インスタンス x 2 + 3 TB インスタンス x 1<br />または 8 TB インスタンス x 1 |


他のバリエーションもあります。 

## <a name="encryption-of-data-at-rest"></a>保存データの暗号化
HANA L インスタンスに使用されるストレージでは、ディスクに保存されているデータを透過的に暗号化することができます。 HANA L インスタンス ユニットのデプロイ時に、この種の暗号化を有効にすることができます。 また、デプロイの実行後に暗号化されたボリュームを変更することもできます。 暗号化されていないボリュームから暗号化されたボリュームへの移行は透過的に行われ、ダウンタイムは発生しません。 

Type I クラスの SKU では、ブート LUN が格納されているボリュームが暗号化されます。 HANA L インスタンスの Type II クラスの SKU の場合、OS 方式でブート LUN を暗号化する必要があります。 詳細については、Microsoft のサービス管理チームに問い合わせてください。

**次のステップ**
- [HANA L インスタンスのサポートされるシナリオ](hana-supported-scenario.md)を参照