---
title: SAP HANA on Azure (L インスタンス) のストレージ アーキテクチャ | Microsoft Docs
description: SAP HANA on Azure (Large Instance) のストレージ アーキテクチャについて説明します。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/22/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f0d27c03d32b748473e9b1d32afff051eed0cf7
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130222952"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>SAP HANA (Large Instances) のストレージ アーキテクチャ

この記事では、SAP HANA on Azure Large Instance (別称 BareMetal Infrastructure) をデプロイするためのストレージ アーキテクチャについて説明します。 

SAP HANA on Azure (L インスタンス) のストレージ レイアウトは、SAP 推奨ガイドラインに従い、クラシック デプロイ モデルで SAP HANA によって構成されます。 ガイドラインの詳細は、[SAP HANA のストレージの要件](https://archive.sap.com/kmuuid2/70c8e423-c8aa-3210-3fae-e043f5c1ca92/SAP%20HANA%20TDI%20-%20Storage%20Requirements.pdf)に関するドキュメントをご覧ください。

HANA Large Instances の Type I クラスには、ストレージ ボリュームの 4 倍のメモリ ボリュームが装備されています。 一方、HANA Large Instances の Type II クラスには、HANA トランザクション ログのバックアップを格納するためのボリュームが装備されています。 詳細については、[SAP HANA on Azure (L インスタンス) のインストールと構成](hana-installation.md)に関する記事をご覧ください。

ストレージの割り当ては、次の表をご覧ください。 表には、異なる種類の HANA Large Instance ユニットに用意されているボリュームの大まかな容量を記載しています。

| HANA L インスタンス SKU | hana/data | hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328 GB | 768 GB |1,280 GB | 768 GB |
| S96 | 1,280 GB | 512 GB | 768 GB | 512 GB |
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
| S896m | 33,792 GB | 512 GB | 1,024 GB | 512 GB |

これよりも新しい HANA Large Instance の SKU は、次のストレージ構成で提供しています。

| HANA L インスタンス SKU | hana/data | hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S224 | 4,224 GB | 512 GB | 1,024 GB | 512 GB |
| S224oo | 6,336 GB | 512 GB | 1,024 GB | 512 GB |
| S224m | 8,448 GB | 512 GB | 1,024 GB | 512 GB |
| S224om | 8,448 GB | 512 GB | 1,024 GB | 512 GB |
| S224ooo | 10,560 GB | 512 GB | 1,024 GB | 512 GB |
| S224oom | 12,672 GB | 512 GB | 1,024 GB | 512 GB |
| S448 | 8,448 GB | 512 GB | 1,024 GB | 512 GB |
| S448oo | 12,672 GB | 512 GB | 1,024 GB | 512 GB |
| S448m | 16,896 GB | 512 GB | 1,024 GB | 512 GB |
| S448om | 16,896 GB | 512 GB | 1,024 GB | 512 GB |
| S448ooo | 21,120 GB | 512 GB | 1,024 GB | 512 GB |
| S448oom | 25,344 GB | 512 GB | 1,024 GB | 512 GB |
| S672 | 12,672 GB | 512 GB | 1,024 GB | 512 GB |
| S672oo | 19,008 GB | 512 GB | 1,024 GB | 512 GB |
| S672m | 25,344 GB | 512 GB | 1,024 GB | 512 GB |
| S672om | 25,344 GB | 512 GB | 1,024 GB | 512 GB |
| S672ooo | 31,680 GB | 512 GB | 1,024 GB | 512 GB |
| S672oom | 38,016 GB | 512 GB | 1,024 GB | 512 GB |
| S896 | 16,896 GB | 512 GB | 1,024 GB | 512 GB |
| S896oo | 25,344 GB | 512 GB | 1,024 GB | 512 GB |
| S896om | 33,792 GB | 512 GB | 1,024 GB | 512 GB |
| S896ooo | 42,240 GB | 512 GB | 1,024 GB | 512 GB |
| S896oom | 50,688 GB | 512 GB | 1,024 GB | 512 GB |


デプロイ後のボリュームの実際の容量は、デプロイ方法により変化します。表示されるボリュームの容量は、これを表示するツールによっても変化します。

HANA L インスタンス SKU を分割する場合、考えられる各分割部分の例は次のようになります。

| メモリ パーティション (GB) | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1,280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792 GB | 640 GB | 1,024 GB | 640 GB |
| 1,536 | 3,328 GB | 768 GB | 1,280 GB | 768 GB |


これらのサイズはおおよそのボリューム数であり、デプロイと、ボリュームの確認に使用するツールによって若干異なる場合があります。 2\.5 TB など、他のパーティション サイズもあります。 これらのストレージ容量は、上のパーティションに使用したものと同様の計算式によって計算しています。 ここでは、オペレーティング システム、メモリまたは CPU リソースのパーティション分割とは異なる意味で、“パーティション” という用語を使用しています。 これは、1 つの HANA L インスタンス ユニットにデプロイできるさまざまな HANA インスタンスのストレージ パーティションを示しています。 

ストレージを増やす必要がある場合は、1 TB 単位で購入できます。 追加ストレージを新たなボリュームとして使用する、追加ボリュームを使用して別のボリュームを拡張する、既存のボリュームの容量を増やす、という方法があります。 最初にデプロイした容量を減らすこと、上の表に記載した条件に反して容量を減らすことはできません。 ボリューム名とマウント名の変更もできません。 上記のストレージ ボリュームは、NFS4 ボリュームとして HANA L インスタンス ユニットに接続されます。

バックアップ/復元とディザスター リカバリーのために、ストレージ スナップショットをご利用いただけます。 詳細については、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md)」をご覧ください。

シナリオのストレージ レイアウトの詳細は、[HLI がサポートするシナリオ](hana-supported-scenario.md)に関する記事をご覧ください。

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>1 つの HANA L インスタンス ユニットでの複数の SAP HANA インスタンスの実行

HANA L インスタンス ユニットで複数のアクティブな SAP HANA インスタンスをホストできます。 ストレージ スナップショットとディザスター リカバリーの機能を提供するために、このような構成ではインスタンスごとにボリューム セットが必要になります。 現時点では、HANA L インスタンス ユニットを次のように分けることができます。

- **S72、S72m、S96、S144、S192**: 最小の容量追加単位が 256 GB、ユニットの最小容量も 256 GB です。 ユニットの最大メモリは、256 GB や 512 GB などの異なる追加単位で増量できます。
- **S144mとS192m**: 最小の容量追加単位が 256 GB、ユニットの最小容量は 512 GB です。 ユニットの最大メモリは、512 GB や 768 GB などの異なる追加単位で増量できます。
- **Type II クラス**:2 TB の最小ユニットから 512 GB ずつ増加。 ユニットの最大メモリは、512 GB、1 TB、1.5 TB などの異なる追加単位で増量できます。

複数の SAP HANA インスタンスの運用例を次に挙げます。

| SKU | メモリ サイズ | ストレージ サイズ | 複数のデータベースとサイズ |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 768 GB HANA インスタンス x 1<br /> または 512 GB インスタンス x 1 + 256 GB インスタンス x 1<br /> または 256 GB インスタンス x 3 | 
| S72m | 1.5 TB | 6 TB | 512 GB HANA インスタンス x 3<br />または 512 GB インスタンス x 1 + 1 TB インスタンス x 1<br />または 256 GB インスタンス x 6<br />または 1.5 TB インスタンス x 1 | 
| S192m | 4 TB | 16 TB | 512 GB インスタンス x 8<br />または 1 TB インスタンス x 4<br />または 512 GB インスタンス x 4 + 1 TB インスタンス x 2<br />または 768 GB インスタンス x 4 + 512 GB インスタンス x 2<br />または 4 TB インスタンス x 1 |
| S384xm | 8 TB | 22 TB | 2 TB インスタンス x 4<br />または 4 TB インスタンス x 2<br />または 3 TB インスタンス x 2 + 2 TB インスタンス x 1<br />または 2.5 TB インスタンス x 2 + 3 TB インスタンス x 1<br />または 8 TB インスタンス x 1 |


他のバリエーションもあります。 

## <a name="encryption-of-data-at-rest"></a>保存データの暗号化
HANA Large Instance のストレージでは、ディスクにデータを保存するときに透過的暗号化を行います。 2018 年末以前のデプロイでは、ボリュームを暗号化できました。 そうしたくない場合は、ボリュームをオンラインで暗号化できました。 暗号化されていないボリュームから暗号化されたボリュームへの移行は透過的に行われ、ダウンタイムは発生しません。 

HANA Large Instance の SKU の Type I クラスでは、ブート LUN を含むボリュームが暗号化されます。 SKU の Type II クラスを使用するリビジョン 3 の HANA Large Instance スタンプでは、OS の方法でブート LUN を暗号化する必要があります。 SKU の Type II クラスを使用するリビジョン 4 の HANA Large Instance スタンプでは、ブート LUN が格納されているボリュームは、保存時に既定で暗号化されます。 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>HANA L インスタンス上のさらに大きい HANA インスタンスに対する必要な設定

HANA L インスタンスで使用されるストレージには、ファイル サイズの制限があります。 ファイルごとの[サイズの制限は 16 TB です](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html)。 EXT3 ファイルシステムの場合と異なり、HANA Large Instance ストレージでは、ユーザーが気付かない方法でファイルサイズの上限に対処することはありません。 そのため HANA では、ファイルサイズが 16 TB の上限に達したとき、自動的に新しいデータ ファイルを作成することはありません。 HANA でファイル サイズが 16 TB を超えそうになったら、エラーを報告し、最終的にはインデックス サーバーがクラッシュします。

> [!IMPORTANT]
> HANA のファイル サイズが、HANA Large Instance ストレージの上限である 16 TB を超えないためには、HANA の global.ini 構成ファイルで次のパラメーターを設定する必要があります。
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - SAP Note [#2400005](https://launchpad.support.sap.com/#/notes/2400005) も参照してください
> - SAP Note [#2631285](https://launchpad.support.sap.com/#/notes/2631285) に注意してください

## <a name="next-steps"></a>次の手順

SAP HANA (Large Instance) のデプロイについて説明します。

> [!div class="nextstepaction"]
> [SAP HANA (Large Instance) のデプロイ](hana-overview-infrastructure-connectivity.md)
