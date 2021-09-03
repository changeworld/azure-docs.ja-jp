---
title: Azure NetApp Files 用の Linux NFS マウント オプションのベスト プラクティス | Microsoft Docs
description: マウント オプションと、それらを Azure NetApp Files で使用するためのベスト プラクティスについて説明します。
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
ms.date: 06/01/2021
ms.author: b-juche
ms.openlocfilehash: 445920e1846e668f0baa3567111f169fd6dd7508
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113232522"
---
# <a name="linux-nfs-mount-options-best-practices-for-azure-netapp-files"></a>Azure NetApp Files 用の Linux NFS マウント オプションのベスト プラクティス

この記事は、マウント オプションと、それらを Azure NetApp Files で使用するためのベスト プラクティスについて理解するのに役立ちます。

## `Nconnect` 

`nconnect` マウント オプションを使用すると、NFS クライアントと NFS エンドポイントの間に確立される接続 (ネットワーク フロー) の数を上限の 16 まで指定できます。 従来、NFS クライアントでは、それ自体とエンドポイント間には 1 つの接続が使用されます。  ネットワーク フローの数を増やすことで、I/O とスループットの上限が大幅に増えます。 テストで `nconnect=8` のパフォーマンスが最も高いことがわかりました。  

実稼働用にマルチノード SAS グリッド環境を準備する際に、実行時間を 8 時間から 5.5 時間に 30% 繰り返し短縮できることに気付くかもしれません。 

| マウント オプション | ジョブの実行時間 |
|-|-|
| `nconnect` なし | 8 時間 |
| `nconnect=8`  | 5.5 時間 | 

両方のテスト セットで同じ E32-8_v4 仮想マシンと RHEL 8.3 を使用しており、先読みは 15 MiB に設定されています。

`nconnect` を使用する場合は、次の規則にご注意ください。

* `nconnect` は、すべての主要な Linux ディストリビューションの Azure NetApp Files でサポートされますが、新しいリリースでのみとなります。

    | Linux リリース | NFSv3 (最小リリース) | NFSv4.1 (最小リリース) |
    |-|-|-|
    | Redhat Enterprise Linux | RHEL8.3 | RHEL8.3 |
    | SUSE | SLES12SP4 または SLES15SP1 | SLES15SP2 |
    | Ubuntu | Ubuntu18.04 |          |

    > [!NOTE]
    > SLES15SP2 は SUSE の最小リリースであり、`nconnect` は NFSv4.1 の Azure NetApp Files でサポートされています。  指定されている他のすべてのリリースは、`nconnect` 機能を導入した最初のリリースです。

* 次のシナリオに示すように、1 つのエンドポイントからのすべてのマウントで、マウントされた最初のエクスポートの `nconnect` 設定が継承されます。 

    シナリオ 1: `nconnect` は最初のマウントで使用されます。 そのため、同じエンドポイントに対するすべてのマウントで `nconnect=8` が使用されます。

    * `mount 10.10.10.10:/volume1 /mnt/volume1 -o nconnect=8`
    * `mount 10.10.10.10:/volume2 /mnt/volume2`
    * `mount 10.10.10.10:/volume3 /mnt/volume3`

    シナリオ 2: `nconnect` は最初のマウントでは使用されません。 そのため、同じエンドポイントに対するマウントで `nconnect` は使用されません。その後すぐに `nconnect` が指定される場合でも同様です。

    * `mount 10.10.10.10:/volume1 /mnt/volume1`
    * `mount 10.10.10.10:/volume2 /mnt/volume2 -o nconnect=8`
    * `mount 10.10.10.10:/volume3 /mnt/volume3 -o nconnect=8`

    シナリオ 3: `nconnect` 設定は個別のストレージ エンドポイントに伝達されません。  `nconnect` は、`10.10.10.10` からのマウントで使用されますが、`10.12.12.12` からのマウントでは使用されません。

    * `mount 10.10.10.10:/volume1 /mnt/volume1 -o nconnect=8`
    * `mount 10.12.12.12:/volume2 /mnt/volume2`

* `nconnect` は、特定のクライアントからストレージの同時実行性を向上させるために使用される場合があります。 

詳細については、[Azure NetApp Files 用の Linux の同時実行のベスト プラクティス](performance-linux-concurrency-session-slots.md)に関するページを参照してください。

## <a name="rsize-and-wsize"></a>`Rsize` および `Wsize`
 
`rsize` および `wsize` フラグでは、NFS 操作の最大転送サイズを設定します。  マウント時に `rsize` や `wsize` が指定されていない場合、クライアントとサーバーでは、その 2 つでサポートされる最大サイズをネゴシエートします。   現在、Azure NetApp Files と最新の Linux ディストリビューションの両方で、1,048,576 バイト (1 MiB) の読み取りおよび書き込みサイズがサポートされています。   ただし、全体的なスループットと待機時間を最適にするために、Azure NetApp Files では `rsize` と `wsize` の両方を 262,144 バイト (256 K) 以下に設定することをお勧めします。 256 KiB より大きい `rsize` と `wsize` を使用すると、待機時間が長くなり、スループットが低下するおそれがあります。 

たとえば、「[SUSE Linux Enterprise Server 上の Azure NetApp Files を使用して Azure VM のスタンバイ ノードで SAP HANA スケールアウト システムをデプロイする](../virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse.md#mount-the-azure-netapp-files-volumes)」では、次のように 256 KiB の `rsize` と `wsize` が示されています。

```
sudo vi /etc/fstab
# Add the following entries
10.23.1.5:/HN1-data-mnt00001 /hana/data/HN1/mnt00001  nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
10.23.1.6:/HN1-data-mnt00002 /hana/data/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
10.23.1.4:/HN1-log-mnt00001 /hana/log/HN1/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
10.23.1.6:/HN1-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
10.23.1.4:/HN1-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
```
 
また、たとえば、SAS Viya では、256 KiB の読み取りと書き込みのサイズが推奨され、[SAS GRID](https://communities.sas.com/t5/Administration-and-Deployment/Azure-NetApp-Files-A-shared-file-system-to-use-with-SAS-Grid-on/m-p/606973/highlight/true#M17740) では、NFS マウントの先読みを増やして読み取りパフォーマンスを向上させる一方で、`r/wsize` が 64 KiB に制限されます。 詳細については、[Azure NetApp Files の NFS 先読みのベスト プラクティス](performance-linux-nfs-read-ahead.md)に関するページを参照してください。

`rsize` と `wsize` の使用には以下の考慮事項が適用されます。

* 多くの場合、ランダム I/O 操作のサイズは、`rsize` と `wsize` のマウント オプションよりも小さくなります。 そのため、実際にはそれらが制約を受けることはありません。
* ファイルシステム キャッシュを使用する場合、ファイル サイズが `rsize` および `wsize` よりも小さい場合を除き、`rsize` と `wsize` のマウント オプションによって指定されるサイズで順次 I/O が発生します。
* `rsize` および `wsize` のマウント オプションによって制約されていても、ファイルシステム キャッシュをバイパスする操作では、`rsize` または `wsize` によって指定された最大サイズになるとは限りません。  `directio` オプションが指定されたワークロード ジェネレーターを使用する場合、この考慮事項は重要です。

''*Azure NetApp Files を使用する際のベスト プラクティスとして、全体的なスループットと待機時間を最適にするために、`rsize` と `wsize` を 262,144 バイト以下に指定します。* ''

## <a name="close-to-open-consistency-and-cache-attribute-timers"></a>クローズツーオープンの整合性とキャッシュ属性タイマー   

NFS では緩やかな整合性モデルを使用します。 アプリケーションで共有ストレージにアクセスし、使用するたびにデータをフェッチする必要がないため、整合性は緩やかになります。このようなシナリオでは、アプリケーションのパフォーマンスに大きな影響があります。  このプロセスを管理するメカニズムには、キャッシュ属性タイマーとクローズツーオープンの整合性の 2 つがあります。

''*クライアントにデータの完全な所有権がある場合、つまり、複数のノード間やシステム間で共有されていない場合は、整合性が保証されます。* '' この場合、ストレージへの `getattr` アクセス操作を減らし、アプリケーションを高速化できます。そのためには、(マウント オプションとして `nocto`) クローズツーオープン (`cto`) 整合性をオフにし、属性キャッシュ管理のタイムアウトをオンにします (マウント オプションとして `actimeo=600` を指定すると、タイマーが 10m に変わります。これに対して、既定値は `acregmin=3,acregmax=30,acdirmin=30,acdirmax=60`)。 一部のテストでは、`nocto` で `getattr` アクセス呼び出しの約 65 から 70% を減らし、`actimeo` を調整することで、これらの呼び出しをさらに 20 から 25% 減らします。

### <a name="how-attribute-cache-timers-work"></a>属性キャッシュ タイマーのしくみ  

`acregmin`、`acregmax`、`acdirmin`、および `acdirmax` 属性でキャッシュの整合性を制御します。 前者の 2 つの属性では、ファイルの属性が信頼される期間を制御します。 後者の 2 つの属性では、ディレクトリ ファイル自体の属性 (ディレクトリ サイズ、ディレクトリの所有権、ディレクトリのアクセス許可) が信頼される期間を制御します。  `min` および `max` 属性では、ディレクトリの属性、ファイルの属性、ファイルのキャッシュ コンテンツがそれぞれ信頼できると見なされる最小および最大期間を定義します。 `min` と `max` との間では、キャッシュされたエントリが信頼される期間を定義するアルゴリズムが使用されます。

たとえば、`acregmin` と `acregmax` の既定値がそれぞれ 3 秒と 30 秒であるとします。  たとえば、ディレクトリ内のファイルに対して属性が繰り返し評価されます。  3 秒が経過すると、鮮度について、NFS サービスに対してクエリが実行されます。  属性が有効と見なされると、クライアントでは信頼される時間が 6 秒、12 秒、24 秒と倍になり、その後、30 秒になります (最大値が 30 に設定されているため)。  この時点から、キャッシュされた属性が古いと見なされるまで (その時点でサイクルがやり直される)、信頼性は `acregmax` で指定された値である 30 秒として定義されます。

クライアントでデータが読み取り専用として使用され、データ更新が別のパスを介して管理されている場合など、クライアントに完全な所有権がない場合でも、同様のマウント オプション セットのベネフィットが得られる場合があります。  EDA、Web ホスティングおよびムービー レンダリングなどのクライアントのグリッドを使用し、比較的静的なデータ セット (EDA ツールまたはライブラリ、Web コンテンツ、テクスチャ データ) があるアプリケーションの場合、一般的な動作では、データ セットがクライアント上で主にキャッシュされます。 読み取りはほとんどなく、書き込みはありません。 ストレージに戻される `getattr` とアクセス呼び出しが多くなります。  これらのデータ セットは通常、ファイル システムをマウントしてコンテンツの更新を定期的にプッシュする別のクライアントを通じて更新されます。

このような場合、新しいコンテンツを選択する際に遅延が発生するのがわかっており、アプリケーションでは引き続き古い可能性のあるデータが処理されます。  このような場合は、`nocto` および `actimeo` を使用して、古いデータを管理できる期間を制御できます。  たとえば、EDA ツールおよびライブラリでは、`actimeo=600` が適しています。このデータは通常は頻繁に更新されないためです。  クライアントでサイトを編集しているときにデータの更新をタイムリーに確認する必要がある小規模な Web ホスティングでは、`actimeo=10` を許容できる場合があります。 複数のファイル システムにプッシュされたコンテンツがある大規模な Web サイトでは、`actimeo=60` が許容される場合があります。

これらのマウント オプションを使用すると、このような場合のストレージに対するワークロードの負荷が大幅に削減されます (たとえば、最近の EDA エクスペリエンスでは、ツール ボリュームへの IOPS が 150 K 以上から 6 K までに削減されています)。アプリケーションは、メモリ内のデータを信頼できるため、非常に高速に実行できます (メモリ アクセス時間はナノ秒で、これに対して、高速ネットワークの `getattr` とアクセスでは数百マイクロ秒となります)。

### <a name="close-to-open-consistency"></a>クローズツーオープンの整合性 

クローズツーオープンの整合性 (`cto` マウント オプション) を使用すれば、キャッシュの状態に関わらず、ファイルを開いたときに常に最新のデータがアプリケーションに表示されるようになります。  

* ディレクトリがクロールされると (たとえば、`ls`、`ls -l`)、特定の PRC 呼び出しセットが発行されます。  
    NFS サーバーでは、そのファイル システムのビューを共有します。 指定された NFS エクスポートにアクセスするすべての NFS クライアントで `cto` が使用されている限り、すべてのクライアントにファイルとディレクトリの同じリストが表示されます。  ディレクトリ内のファイルの属性の鮮度は、[属性キャッシュ タイマー](#how-attribute-cache-timers-work)によって制御されます。  つまり、`cto` が使用されている限り、ファイルは作成されてストレージに配置されるとすぐに、リモート クライアントに表示されます。
* ファイルが開いている場合、そのファイルのコンテンツは NFS サーバーの観点から最新であることが保証されます。  
    マシン 2 でファイルが開いているときにマシン 1 からのコンテンツのフラッシュが終了していない競合状態が発生した場合、マシン 2 では、開いている時点でサーバー上に存在するデータのみを受信します。 この場合、マシン 2 では、`acreg` タイマーに達するまでファイルからさらにデータを取得しません。また、マシン 2 では、再度サーバーからキャッシュの整合性を確認します。  このシナリオは、ファイルがマシン 1 からまだ書き込まれている場合に、マシン 2 から末尾 `-f` を使用して確認できます。

### <a name="no-close-to-open-consistency"></a>クローズツーオープンの整合性なし  

クローズツーオープンの整合性 (`nocto`) が使用されていない場合、クライアントでは、キャッシュ属性タイマーに達するまで、ファイルおよびディレクトリの現在のビューの鮮度を信頼します。  

* ディレクトリがクロールされると (たとえば、`ls`、`ls -l`)、特定の PRC 呼び出しセットが発行されます。  
    クライアントでは、`acdir` キャッシュ タイマーの値に達した場合にのみ、ファイルの現在のリストに対してサーバーへの呼び出しを発行します。  この場合、最近作成されたファイルとディレクトリは表示されず、最近削除されたファイルとディレクトリは引き続き表示されます。  

* ファイルが開いている場合、そのファイルがまだキャッシュ内にある限り、NFS サーバーとの整合性が検証されることなく、キャッシュされたコンテンツ (存在する場合) が返されます。

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files の Linux ダイレクト I/O のベスト プラクティス](performance-linux-direct-io.md)
* [Azure NetApp Files の Linux ファイルシステム キャッシュのベスト プラクティス](performance-linux-filesystem-cache.md)
* [Azure NetApp Files 用の Linux の同時実行のベスト プラクティス](performance-linux-concurrency-session-slots.md)
* [Linux NFS 先読みのベスト プラクティス](performance-linux-nfs-read-ahead.md)
* [Azure 仮想マシン SKU のベスト プラクティス](performance-virtual-machine-sku.md) 
* [Linux のパフォーマンス ベンチマーク](performance-benchmarks-linux.md) 
