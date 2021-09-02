---
title: Azure NetApp Files の Linux コンカレンシーのベスト プラクティス - セッション スロットとスロット テーブル エントリ | Microsoft Docs
description: Azure NetApp Files NFS プロトコルのセッション スロットとスロット テーブル エントリのベスト プラクティスについて説明します。
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
ms.date: 08/02/2021
ms.author: b-juche
ms.openlocfilehash: 522c9e590f1f63a12bd4f52f56eac0798ba78aa7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729229"
---
# <a name="linux-concurrency-best-practices-for-azure-netapp-files---session-slots-and-slot-table-entries"></a>Azure NetApp Files の Linux コンカレンシーのベスト プラクティス - セッション スロットとスロット テーブル エントリ

この記事では、Azure NetApp Files NFS プロトコルのセッション スロットとスロット テーブル エントリのコンカレンシーのベスト プラクティスについて説明します。 

## <a name="nfsv3"></a>NFSv3

NFSv3 には、クライアントとサーバーの間のコンカレンシーをネゴシエートするメカニズムがありません。 クライアントとサーバーにはそれぞれ、相互に相談することなく、制限が定義されています。  最高のパフォーマンスを得るには、クライアント側の `sunrpc` スロット テーブル エントリの最大数を、サーバー側でプッシュバックなしでサポートされているものと合わせる必要があります。  クライアントがサーバーのネットワーク スタックの処理能力を超えた場合、サーバー側は接続のウィンドウ サイズを減らすことで応答しますが、これは理想的なパフォーマンス シナリオではありません。

最近の Linux カーネルの既定では、次の表に示すように、接続ごとの `sunrpc` スロット テーブルのエント サイズ `sunrpc.max_tcp_slot_table_entries` が 65,536 個の未処理操作をサポートするように定義されています。 

| Azure NetApp Files NFSv3 サーバー <br> 接続あたりの最大実行コンテキスト | Linux クライアント <br> 接続あたりの既定の最大 `sunrpc` スロット テーブル エントリ |
|-|-|
| 128 | 65,536 |

これらのスロット テーブル エントリに同時実行の制限が定義されています。 これほど高い値は必要ありません。  たとえば、"*リトルの法則*" と呼ばれる待ち行列理論を使用すると、I/O レートはコンカレンシー (つまり、未処理の I/O) と待機時間によって決まることがわかります。 そのため、このアルゴリズムにより、65,536 個のスロットは、非常に負荷の高いワークロードを駆動するために必要なものよりも桁違いに高いことが証明されています。

`Littles Law: (concurrency = operation rate × latency in seconds)`

`nconnect` マウント オプションと似た概念のテクノロジである Oracle Direct NFS を使用して 1 秒間に 155,000 回の Oracle DB NFS 操作を行うには、155 という低いコンカレンシー レベルで十分です。

* 待機時間が 0.5 ms の場合、110,000 IOPS を達成するには、55 のコンカレンシーが必要です。
* 待機時間が 1 ms の場合、155,000 IOPS を達成するには、155 のコンカレンシーが必要です。

![Oracle DNFS の待機時間曲線](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

詳細については、「[Azure NetApp Files の単一ボリュームでの Oracle データベースのパフォーマンス](performance-oracle-single-volumes.md)」を参照してください。

`sunrpc.max_tcp_slot_table_entries` tunable は、接続レベルのチューニング パラメーターです。  "*ベスト プラクティスとして、この値を 1 接続あたり 128 以下に設定し、環境全体で 10,000 スロットを超えないようにしてください。* "

### <a name="examples-of-slot-count-based-on-concurrency-recommendation"></a>コンカレンシーの推奨に基づくスロット数の例 

このセクションの例は、コンカレンシーの推奨に基づくスロット数を示しています。  

#### <a name="example-1--one-nfs-client-65536-sunrpcmax_tcp_slot_table_entries-and-no-nconnect-for-a-maximum-concurrency-of-128-based-on-the-server-side-limit-of-128"></a>例 1 - サーバー側の制限値 128 に基づいて 128 の最大コンカレンシーを達成するための 1 つの NFS クライアント、65,536 の `sunrpc.max_tcp_slot_table_entries`、`nconnect` なし

例 1 は、既定の `sunrpc.max_tcp_slot_table_entry` 値が 65,536 でネットワーク接続が 1 つ (つまり `nconnect` なし) である単一クライアント ワークロードに基づいています。  この場合、128 のコンカレンシーを達成できます。

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP`) 
        * 理論上、クライアントからサーバーに対して同時に発行できる要求は 1 つの接続あたり 65,536 以下です。
        * サーバーがこの 1 つの接続から同時に受け入れることができる要求は 128 以下です。

#### <a name="example-2--one-nfs-client-128-sunrpcmax_tcp_slot_table_entries-and-no-nconnect-for-a-maximum-concurrency-of-128"></a>例 2 - 128 の最大コンカレンシーを達成するための 1 つの NFS クライアント、128 の `sunrpc.max_tcp_slot_table_entries`、`nconnect` なし

例 2 は、`sunrpc.max_tcp_slot_table_entry` 値が 128 であり、`nconnect` のマウント オプションを使用しない単一クライアント ワークロードに基づいています。  この設定では、1 つのネットワーク接続から 128 のコンカレンシーを達成できます。

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP) `
        * クライアントからサーバーに対して同時に発行できる要求は 1 つの接続あたり 128 以下です。
        * サーバーがこの 1 つの接続から同時に受け入れることができる要求は 128 以下です。

#### <a name="example-3--one-nfs-client-100-sunrpcmax_tcp_slot_table_entries-and-nconnect8-for-a-maximum-concurrency-of-800"></a>例 3 - 800 の最大コンカレンシーを達成するための 1 つの NFS クライアント、100 の `sunrpc.max_tcp_slot_table_entries`、`nconnect=8`

例 3 は、単一クライアント ワークロードに基づいていますが、`sunrpc.max_tcp_slot_table_entry` の値が 100 と低くなっています。  今回は、`nconnect=8` のマウント オプションを使用して、8 つの接続に作業負荷を分散しています。  この設定では、8 つの接続に分散して 800 のコンカレンシーを達成できます。  この量は、400,000 IOPS を達成するために必要なコンカレンシーです。

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection 1 (10.10.10.10:2049, 10.10.10.11:6543,TCP), Connection 2 (10.10.10.10:2049, 10.10.10.11:6454,TCP)… Connection 8 (10.10.10.10:2049, 10.10.10.11:7321,TCP)`
    * 接続 1
        * クライアントからサーバーに対してこの接続から同時に発行できる要求は 100 以下です。
        * サーバー側は、この接続についてクライアントから同時に 128 個以下の要求を受け入れることが期待されます。
    * 接続 2
        * クライアントからサーバーに対してこの接続から同時に発行できる要求は 100 以下です。
        * サーバー側は、この接続についてクライアントから同時に 128 個以下の要求を受け入れることが期待されます。
    * `…`
    * `…`
    * 接続 8
        * クライアントからサーバーに対してこの接続から同時に発行できる要求は 100 以下です。
        * サーバー側は、この接続についてクライアントから同時に 128 個以下の要求を受け入れることが期待されます。

#### <a name="example-4--250-nfs-clients-8-sunrpcmax_tcp_slot_table_entries-and-no-nconnect-for-a-maximum-concurrency-of-2000"></a>例 4 - 2000 の最大コンカレンシーを達成するための 250 個の NFS クライアント、8 の `sunrpc.max_tcp_slot_table_entries`、`nconnect` なし

例 4 では、マシン数 250 台の EDA 環境で、クライアントあたりの `sunrpc.max_tcp_slot_table_entry` 値を 8 に減らして使用しています。 このシナリオでは、環境全体で 2,000 のコンカレンシーに達します。これは、バックエンドの EDA ワークロードを 4,000 MiB/s で駆動するのに十分な値です。

* `NFS_Server=10.10.10.10, NFS_Client1=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP)` 
        * クライアントからサーバーに対して同時に発行できる要求は 1 つの接続あたり 8 以下です。
        * サーバーがこの 1 つの接続から同時に受け入れることができる要求は 128 以下です。
* `NFS_Server=10.10.10.10, NFS_Client2=10.10.10.12`
    * `Connection (10.10.10.10:2049, 10.10.10.12:7820,TCP) `
        * クライアントからサーバーに対して同時に発行できる要求は 1 つの接続あたり 8 以下です。
        * サーバーがこの 1 つの接続から同時に受け入れることができる要求は 128 以下です。
* `…`
* `…`
* `NFS_Server=10.10.10.10, NFS_Client250=10.10.11.13`
    * `Connection (10.10.10.10:2049, 10.10.11.13:4320,TCP) `
        * クライアントからサーバーに対して同時に発行できる要求は 1 つの接続あたり 8 以下です。
        * サーバーがこの 1 つの接続から同時に受け入れることができる要求は 128 以下です。

NFSv3 を使用する場合、"*ストレージ エンドポイントのスロット数をまとめて 10,000 以下に維持する必要があります*"。 多数のネットワーク接続にまたがってアプリケーションがスケールアウトされる場合は、`sunrpc.max_tcp_slot_table_entries` の接続あたりの値を 128 以下に設定するのが最適です (`nconnect`と HPC の場合は全般的に、EDA の場合は特に)。  

### <a name="how-to-calculate-the-best-sunrpcmax_tcp_slot_table_entries"></a>最適な `sunrpc.max_tcp_slot_table_entries` を計算する方法 

"*リトルの法則*" を使用して、必要なスロット テーブル エントリ数の合計を計算することができます。 一般的な例として、次の要因を考えてみましょう。  

* スケールアウト ワークロードは、多くの場合、本質的に大規模なシーケンシャルです。
* データベースのワークロード (特に OLTP) は、多くの場合、本質的にランダムです。 

次の表は、任意の待機時間を指定した場合のコンカレンシーの調査例です。

|     I/O サイズ    |     待機時間    |     I/O またはスループット    |     コンカレンシー    |
|-|-|-|-|
|     8 KiB    |     0.5 ms    |     110,000 IOPS \| 859 MiB/s    |     55    |
|     8 KiB    |     2 ミリ秒    |     400,000 IOPS \| 3,125 MiB/s    |     800    |
|     256 KiB    |     2 ミリ秒    |     16,000 IOPS \| 4,000 MiB/s    |     32    |
|     256 KiB    |     4 ミリ秒    |     32,000 IOPS \| 8,000 MiB/s    |     128    |

### <a name="how-to-calculate-concurrency-settings-by-connection-count"></a>接続数によるコンカレンシー設定の計算方法

たとえば、ワークロードが EDA ファームであり、1,250 のクライアントからすべて同じストレージ エンドポイントに対してワークロードを駆動する場合 (ストレージ エンドポイントはストレージの IP アドレスです)、必要な I/O レートを計算し、ファーム全体でコンカレンシーを分割します。

平均操作サイズが 256 KiB、平均待機時間が 10 ms を使用してワークロードが 4,000 MiB/s あると仮定します。 コンカレンシーを計算するには、次の数式を使用します。

`(concurrency = operation rate × latency in seconds)`

この計算の結果、コンカレンシーは 160 になります。 
 
`(160 = 16,000 × 0.010)`

1,250 のクライアントのニーズがある場合、4,000 MiB/s を達成するには `sunrpc.max_tcp_slot_table_entries` をクライアントあたり 2 に設定するのが安全です。  ただし、推奨スロット数の上限である 10,000 を超えないように、クライアントあたりの数を 4 または 8 に設定して余裕を持たせることもできます。 

### <a name="how-to-set-sunrpcmax_tcp_slot_table_entries-on-the-client"></a>クライアント上に `sunrpc.max_tcp_slot_table_entries` を設定する方法

1.  `/etc/sysctl.conf` 構成ファイルに `sunrpc.max_tcp_slot_table_entries=<n>` を追加します。   
    チューニング時に 128 未満の値が最適と判断された場合は、128 を適切な数値に置き換えます。
2.  次のコマンドを実行します。   
    `$ sysctl -p`
3.  tunable が設定された後にのみ tunable がマウントに適用されるため、すべての NFS ファイル システムをマウント (または再マウント) します。

## <a name="nfsv41"></a>NFSv4.1 

NFSv4.1 では、クライアントとサーバー間の関係がセッションに定義されています。 マウントされた NFS ファイル システムが 1 つの接続と多数 (`nconnect` のような場合) の接続のどちらに配置されていても、セッションの規則が適用されます。 セッションのセットアップ時に、クライアントとサーバー間でセッションの最大要求数がネゴシエートされ、サポートされている 2 つの値のうち低い方に決まります。 Azure NetApp Files は 180 個の未処理の要求をサポートしており、Linux クライアントの既定値は 64 です。 次の表は、セッションの制限を示しています。 

| Azure NetApp Files NFSv4.1 サーバー <br>  セッションあたりの最大コマンド数 | Linux クライアント <br>  セッションあたりの既定の最大コマンド数    | セッションのためにネゴシエートされる最大コマンド数 |
|-|-|-|
|     180    |     64    |     64    |

Linux クライアントの既定値は、セッションあたり最大 64 要求ですが、`max_session_slots` の値は調整可能です。  変更を有効にするには、再起動が必要です。  クライアントによって使用されている現在の最大値を確認するには、`systool -v -m nfs` コマンドを使用します。  このコマンドが機能するには、少なくとも 1 つの NFSv4.1 マウントが存在する必要があります。

```
$ systool -v -m nfs
{
Module = "nfs"
…
  Parameters:
…
    max_session_slots   = "64"   
…
}
```

`max_session_slots` を調整するには、`/etc/modprobe.d` 以下に次のような構成ファイルを作成します。  ファイル内の行に "引用符" が存在しないことを確認してください。 そうしないと、このオプションは有効になりません。

`$ echo “options nfs max_session_slots=180” > /etc/modprobe.d/nfsclient.conf`
`$ reboot`

Azure NetApp Files により、各セッションの最大コマンド数は 180 に制限されています。 そのため、現在構成できる最大値は 180 と考えてください。  Azure NetApp Files により、各接続の最大 NFS コマンド数は 128 に制限されているため、セッションを複数の接続に分割しないと、クライアントは 128 を超えるコンカレンシーを達成できません。  複数の接続を取得するには、`nconnect` マウント オプションが推奨されます。また、2 以上の値にする必要があります。

### <a name="examples-of-expected-concurrency-maximums"></a>予想されるコンカレンシーの最大数の例

このセクションの例では、予想されるコンカレンシーの最大数を示しています。  

#### <a name="example-1--64-max_session_slots-and-no-nconnect"></a>例 1 - 64 の `max_session_slots`、`nconnect` なし

例 1 は、既定の設定である 64 `max_session_slots` と `nconnect` なしに基づいています。 この設定では、いずれも 1 つのネットワーク接続から 64 のコンカレンシーを達成できます。

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP)` 
        * クライアントからサーバーに対してそのセッションについて同時に発行できる要求は 64 以下です。
        * サーバーがそのセッションについてクライアントから同時に受け入れることができる要求は 64 以下です (64 はネゴシエートされた値です)。

#### <a name="example-2--64-max_session_slots-and-nconnect2"></a>例 2 - 64 の `max_session_slots` と `nconnect=2` 

例 2 は最大値が 64 の `session_slots` に基づいていますが、`nconnect=2` のマウント オプションが追加されています。  64 のコンカレンシーを達成できますが、2 つの接続に分割されています。  このシナリオでは、複数の接続によってコンカレンシーは向上しませんが、接続あたりのキューの深さが減ることで、待機時間には良い影響があります。

`max_session_slots` は 64 のままで `nconnect=2` の場合、最大数の要求は複数の接続に分割されることに注意してください。

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection 1 (10.10.10.10:2049, 10.10.10.11:6543,TCP) &&  Connection 2 (10.10.10.10:2049, 10.10.10.11:6454,TCP)`
    * 接続 1
        * クライアントからサーバーに対してこの接続から同時に発行できる要求は 32 以下です。
        * サーバー側は、この接続についてクライアントから同時に 32 個以下の要求を受け入れることが期待されます。
    * 接続 2
        * クライアントからサーバーに対してこの接続から同時に発行できる要求は 32 以下です。
        * サーバー側は、この接続についてクライアントから同時に 32 個以下の要求を受け入れることが期待されます。

#### <a name="example-3--180-max_session_slots-and-no-nconnect"></a>例 3 - 180 の `max_session_slots`、`nconnect` なし

例 3 では、`nconnect` マウント オプションをドロップし、`max_session_slots` 値を 180 に設定して、サーバーの NFSv4.1 セッションの最大コンカレンシーと一致させています。  このシナリオでは、接続が 1 つのみであり、Azure NetApp Files の NFS 接続あたりの最大未処理操作数が 128 である場合、セッションは同時に 128 操作に制限されます。

そのため、`max_session_slots` は 180 に設定されていますが、1 つのネットワーク接続は 128 の最大要求数に制限されます。 

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection (10.10.10.10:2049, 10.10.10.11:6543,TCP) `
        * クライアントからサーバーに対してそのセッションについて同時に発行できる要求は 180 以下です。
        * サーバーがそのセッションについてクライアントから同時に受け入れることができる要求は 180 以下です。
        * *サーバーがこの 1 つの接続について同時に受け入れることができる要求は 128 以下です。*

#### <a name="example-4--180-max_session_slots-and-nconnect2-"></a>例 4 - 180 の `max_session_slots` と `nconnect=2 ` 

例 4 では、`nconnect=2` マウント オプションを追加し、180 `max_session_slots` 値を再利用しています。 全体のワークロードが 2 つの接続に分割されているため、180 の未処理操作を達成できます。

2 つの接続を使用することで、セッションは 180 の未処理要求の完全な割り当てをサポートします。

* `NFS_Server=10.10.10.10, NFS_Client=10.10.10.11`
    * `Connection 1 (10.10.10.10:2049, 10.10.10.11:6543,TCP) &&  Connection 2 (10.10.10.10:2049, 10.10.10.11:6454,TCP)`
    * 接続 1
        * クライアントは、接続 1 からサーバーへの同時の要求を 90 以下に維持することが期待されます。
        * *サーバー側は、セッション内のこの接続についてクライアントからの同時の要求を 90 個以下に維持することが期待されます。*
    * 接続 2
        * クライアントは、接続 1 からサーバーへの同時の要求を 90 以下に維持することが期待されます。
        * *サーバー側は、セッション内のこの接続についてクライアントからの同時の要求を 90 個以下に維持することが期待されます。*

> [!NOTE]
> 最大コンカレンシーのためには、`max_session_slots` を 180 に設定します。これは、現在 Azure NetApp Files でサポートされているセッションレベルの最大コンカレンシーです。

### <a name="how-to-check-for-the-maximum-requests-outstanding-for-the-session"></a>セッションの未処理要求の最大数を確認する方法 

クライアントとサーバーによってサポートされている `session_slot` のサイズを確認するには、パケット トレースで mount コマンドをキャプチャします。  次の例のように、`CREATE_SESSION` の呼び出しと `CREATE_SESSION` の応答を探します。  呼び出しはクライアントから、応答はサーバーから送信されました。

次の `tcpdump` コマンドを使用して、マウント コマンドをキャプチャします。

`$ tcpdump -i eth0 -s 900 -w /tmp/write.trc port 2049`

Wireshark を使用すると、対象のあるパケットは次のとおりです。

![対象のパケットを示すスクリーンショット。](../media/azure-netapp-files/performance-packets-interest.png)  

この 2 つのパケットのうち、トレース ファイルの中ほどのセクションにある `max_reqs` フィールドを確認します。

* ネットワーク ファイル システム
    * 操作
        * `Opcode`
            * `csa_fore_channel_attrs`
            * `max reqs`

パケット 12 (クライアントの最大要求) は、クライアントの `max_session_slots` 値が 64 であることを示しています。  次のセクションでは、サーバーがセッションのコンカレンシーとして 180 をサポートしていることに注目してください。  このセッションでは、最終的に、指定された 2 つの値のうち低い方の値がネゴシエートされます。

![パケット 12 の最大セッション スロットを示すスクリーンショット。](../media/azure-netapp-files/performance-max-session-packet-12.png)  

次の例は、パケット 14 (サーバーの最大要求数) を示しています。

![パケット 14 の最大セッション スロットを示すスクリーンショット。](../media/azure-netapp-files/performance-max-session-packet-14.png) 

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files 用の Linux のダイレクト I/O のベスト プラクティス](performance-linux-direct-io.md)
* [Azure NetApp Files 用の Linux ファイルシステム キャッシュのベスト プラクティス](performance-linux-filesystem-cache.md)
* [Azure NetApp Files 用の Linux NFS マウント オプションのベスト プラクティス](performance-linux-mount-options.md)
* [Linux NFS 先読みのベスト プラクティス](performance-linux-nfs-read-ahead.md)
* [Azure 仮想マシン SKU のベスト プラクティス](performance-virtual-machine-sku.md) 
* [Linux のパフォーマンス ベンチマーク](performance-benchmarks-linux.md) 
