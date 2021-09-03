---
title: Azure NetApp Files の Linux ダイレクト I/O のベスト プラクティス | Microsoft Docs
description: Azure NetApp Files の Linux ダイレクト I/O とそのベスト プラクティスについて説明します。
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
ms.openlocfilehash: 6497d91169c7530251a473e8e06c26ce411e0e6a
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2021
ms.locfileid: "113233489"
---
# <a name="linux-direct-io-best-practices-for-azure-netapp-files"></a>Azure NetApp Files の Linux ダイレクト I/O のベスト プラクティス

この記事は、Azure NetApp Files のダイレクト I/O のベスト プラクティスを理解するのに役立ちます。  

## <a name="direct-io"></a>ダイレクト I/O

 ストレージ パフォーマンスのベンチマークで最もよく使われるパラメーターは、ダイレクト I/O です。 FIO と Vdbench によってサポートされています。 DISKSPD は、メモリ マップド I/O という同様のコンストラクトをサポートしています。 ダイレクト I/O を使用すると、ファイルシステム キャッシュはバイパスされ、ダイレクト メモリ アクセス コピーの操作が回避されるため、ストレージ テストが高速かつ簡単になります。  

ダイレクト I/O パラメーターを使用すると、ストレージのテストが簡単になります。 クライアント上のファイルシステム キャッシュからデータが読み込まれることはありません。 そのため、このテストを実行すると、メモリ アクセス速度ではなく、ストレージ プロトコルとサービス自体に確実に負荷がかかります。 また、DMA メモリ コピーがないため、読み取りと書き込みの操作は、処理の観点からも効率的です。 

ワークロードの例として、Linux の `dd` コマンドを考えてみましょう。 省略可能な `odirect` フラグがない場合、`dd` で生成された I/O はすべて Linux バッファー キャッシュから提供されます。 既にメモリ内にあるブロックを使用した読み取りは、ストレージから取得されません。 バッファー キャッシュのミスが発生した読み取りは、NFS 先読みの使用してストレージから読み取られますが、その結果は `rsize` のマウントやクライアントの先読み調整項目などの要因によって変わります。 書き込みがバッファー キャッシュを経由して送信されるときは、書き込み遅延メカニズムが使用されます。これは調整されず、データをストレージ デバイスに送信するために大量の並列処理が使用されます。 読み取り用の `dd` と書き込み用の `dd` の 2 つの独立した I/O ストリームを実行する場合があります。 ただし、実際には、調整されていないオペレーティング システムによって、読み取りよりも書き込みが優先され、より多くの並列処理が使用されます。

データベースを除けば、ダイレクト I/O を使用するアプリケーションはほとんどありません。 その代わりとして、繰り返しの読み込みには大容量のメモリ キャッシュ、また非同期の書き込みには書き込み遅延キャッシュの利点が活用されます。 つまり、"*もし*" 合成されるアプリケーションにファイルシステム キャッシュが使用されている場合に、ダイレクト I/O を使用すると、テストはマイクロ ベンチマークに変わります。  

ダイレクト I/O をサポートしているデータベースの例を次に示します。 

* Oracle 
* SAP HANA
* MySQL (InnoDB ストレージ エンジン)
* RocksDB
* PostgreSQL
* Teradata

## <a name="best-practices"></a>ベスト プラクティス 

`directio` を使用したテストは、ストレージ サービスとクライアントの制限を理解するために推奨される方法です。 アプリケーション自体がどのように動作するかを理解するには (アプリケーションに `directio` が使用されていない場合)、ファイルシステム キャッシュを使用したテストも実行することをお勧めします。

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files の Linux ファイルシステム キャッシュのベスト プラクティス](performance-linux-filesystem-cache.md)
* [Azure NetApp Files 用の Linux NFS マウント オプションのベスト プラクティス](performance-linux-mount-options.md)
* [Azure NetApp Files 用の Linux の同時実行のベスト プラクティス](performance-linux-concurrency-session-slots.md)
* [Linux NFS 先読みのベスト プラクティス](performance-linux-nfs-read-ahead.md)
* [Azure 仮想マシン SKU のベスト プラクティス](performance-virtual-machine-sku.md) 
* [Linux のパフォーマンス ベンチマーク](performance-benchmarks-linux.md) 
