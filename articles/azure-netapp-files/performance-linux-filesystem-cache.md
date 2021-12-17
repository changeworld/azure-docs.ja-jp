---
title: Azure NetApp Files の Linux ファイルシステム キャッシュのベスト プラクティス | Microsoft Docs
description: Azure NetApp Files に関わる Linux ファイル システム キャッシュのベスト プラクティスについて説明します。
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
ms.openlocfilehash: d0d097a13fa24fb62f70648a34edd27e3b5a0636
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114203861"
---
# <a name="linux-filesystem-cache-best-practices-for-azure-netapp-files"></a>Azure NetApp Files の Linux ファイルシステム キャッシュのベスト プラクティス

この記事は、Azure NetApp Files のファイルシステム キャッシュのベスト プラクティスを理解するのに役立ちます。  

## <a name="filesystem-cache-tunables"></a>ファイルシステム キャッシュの設定値

ファイルシステム キャッシュの設定値に関する次の要素を理解する必要があります。  

* ダーティ バッファーをフラッシュすると、データがクリーンな状態で残り、メモリ不足により削除されることにつながるまで、将来の読み取りに使用できるようになります。  
* 非同期フラッシュ操作には、3 つのトリガーがあります。
    * 時間ベース: この設定値によって定義された期間が経過すると、バッファーがクリーニング (フラッシュつまりストレージへの書き込み) 対象としてマークされる必要があります。
    * メモリ不足: 詳細については、「[`vm.dirty_ratio | vm.dirty_bytes`](#vmdirty_ratio--vmdirty_bytes)」を参照してください。
    * 閉じる: ファイル ハンドルが閉じられると、すべてのダーティ バッファーがストレージに非同期的にフラッシュされます。

これらの要因は、4 つの設定値によって制御されます。 各設定値は、`/etc/sysctl.conf` ファイル内で `tuned` または `sysctl` を使用して動的かつ永続的にチューニングできます。 これらの変数をチューニングすると、アプリケーションのパフォーマンスが向上します。  

> [!NOTE]
> この記事で説明している情報は、SAS GRID と SAS Viya の検証の演習中に明らかになりました。 そのため、これらの設定値は、その検証の演習から学んだ教訓に基づいています。 多くのアプリケーションも同様に、これらのパラメーターをチューニングする利点があります。

### `vm.dirty_ratio | vm.dirty_bytes` 

これらの 2 つの設定値によって、変更されたもののまだ安定したストレージに書き込まれていないデータに対して使用できるようになった RAM の量が定義されます。  どちらか一方の設定値が設定されると、もう一方が自動的にゼロに設定されます。RedHat では、2 つの設定値のいずれかを手動でゼロに設定することはお勧めしていません。  オプション `vm.dirty_ratio` (2 つの既定値) は、RedHat によって OS に応じて物理メモリの 20% か 30% のいずれかに設定されます。これは、最新のシステムのメモリ フットプリントを考慮すると、かなりの量です。 メモリ サイズに関係なく、より一貫したエクスペリエンスを得るためには、設定 `vm.dirty_ratio` ではなく `vm.dirty_bytes` を考慮する必要があります。  たとえば、SAS GRID で実行中の操作により、30 MiB が混在ワークロード全体のパフォーマンスとって最適な設定であると判定されました。 

### `vm.dirty_background_ratio | vm.dirty_background_bytes` 

これらの設定値によって、Linux の書き戻りメカニズムでダーティなブロックの安定したストレージへのフラッシュが始まる、開始点が定義されます。 RedHat の既定値は物理メモリの 10% です。これは、大規模なメモリ システムでフラッシュを開始するには大量のデータです。 SAS GRID を例にとってみると、従来は `vm.dirty_background` を `vm.dirty_ratio` または `vm.dirty_bytes` の 1/5 のサイズに設定することが推奨されています。 SAS GRID に対して `vm.dirty_bytes` の設定が積極的に設定されている割には、ここには具体的な値は設定されていません。  

### `vm.dirty_expire_centisecs` 

この設定値によって、ダーティ バッファーが非同期的に書き出す対象としてタグ付けされるまでの経過時間が定義されます。SAS Viya の CAS ワークロードを例にとってみましょう。 一時的な書き込みが優先されるワークロードでは、この値を 300 センチ秒 (3 秒) に設定するのが最適であることがわかりました (既定値は 3,000 センチ秒 (30 秒))。  

SAS Viya では、CAS データがそれぞれ数メガバイトの複数の小さなチャンクに共有されます。  各シャードにデータが書き込まれた後にこれらのファイル ハンドルは閉じられず、ハンドルは開いたままになり、その中のバッファーはアプリケーションによってメモリマップされます。  閉じられないと、メモリ不足が発生するか 30 秒が経過するまで、フラッシュは発生しません。 メモリ不足の発生を待つことと、長いタイマーが切れるまで待つこととは、どちらも最適な処理ではありません。 全体的なスループットを最適化することを追求したSAS GRID とは異なり、SAS Viya では書き込みの帯域幅を最適化することを追求しました。  

### `vm.dirty_writeback_centisecs` 

カーネル フラッシュ スレッドは、各フラッシュ スレッド スリープ間でダーティ バッファーを非同期的にフラッシュすることを担います。  この設定値によって、フラッシュ間のスリープに費やされた量が定義されます。  SAS Viya によって使用される 3 秒の `vm.dirty_expire_centisecs` 値を考慮して、SAS では全体としての最適なパフォーマンスを見つけるために、この設定値を既定値の 500 センチ秒 (5 秒) ではなく、100 センチ秒 (1 秒) に設定しました。

## <a name="impact-of-an-untuned-filesystem-cache"></a>チューニングされていないファイルシステム キャッシュの影響

仮想メモリの設定値の既定値と最新のシステムの RAM の量を考慮すると、この混在ワークロードを動かす具体的なクライアントの観点からは、書き戻しによってストレージにバインドされた他の操作の速度が低下する可能性があります。  チューニングされていない、書き込みの負荷が高く、キャッシュが大量に存在する Linux マシンからは、次のような症状が現れる場合があります。  

* ディレクトリ リスト `ls` に時間がかかり、応答していないように見える。
* ファイルシステムに対する読み取りスループットが書き込みスループットと比較して大幅に減少する。
* `nfsiostat` で **数秒以上** の書き込みの遅延が報告される。

この動作が見られるのは、書き込みの多い混在ワークロードが実行されている "*Linux マシン*" のみである場合があります。  さらに、単一のストレージ エンドポイントに対してマウントされているすべての NFS ボリュームのエクスペリエンスが低下します。  マウントが 2 つ以上のエンドポイントからのものである場合、1 つのエンドポイントが共有されているボリュームでのみこの動作が見られます。

このセクションで説明しているとおりにファイルシステム キャッシュのパラメーターを設定すると、この問題が対処されていることがわかります。

## <a name="monitoring-virtual-memory"></a>仮想メモリの監視

仮想メモリと書き戻しで何が起こっているかについて理解するには、次のコード スニペットと出力を検討してください。  *Dirty* は、システム内のダーティなメモリの量を表し、*writeback* は、ストレージにアクティブに書き込まれているメモリの量を表します。  

`# while true; do echo "###" ;date ; egrep "^Cached:|^Dirty:|^Writeback:|file" /proc/meminfo; sleep 5; done`

次の出力は、`vm.dirty_ratio` と `vm.dirty_background` の割合がそれぞれ物理メモリの 2% と 1% に設定された、ある実験から得られたものです。  このケースでは、384 GiB メモリ システムの 1% である 3.8 GiB からフラッシュが始まりました。  書き戻しは NFS への書き込みスループットによく似ていました。 

```
Cons
Dirty:                                    1174836 kB
Writeback:                         4 kB
###
Dirty:                                    3319540 kB
Writeback:                         4 kB
###
Dirty:                                    3902916 kB        <-- Writes to stable storage begins here
Writeback:                         72232 kB   
###
Dirty:                                    3131480 kB
Writeback:                         1298772 kB   
``` 

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files 用の Linux のダイレクト I/O のベスト プラクティス](performance-linux-direct-io.md)
* [Azure NetApp Files 用の Linux NFS マウント オプションのベスト プラクティス](performance-linux-mount-options.md)
* [Azure NetApp Files 用の Linux の同時実行のベスト プラクティス](performance-linux-concurrency-session-slots.md)
* [Linux NFS 先読みのベスト プラクティス](performance-linux-nfs-read-ahead.md)
* [Azure 仮想マシン SKU のベスト プラクティス](performance-virtual-machine-sku.md) 
* [Linux のパフォーマンス ベンチマーク](performance-benchmarks-linux.md) 
