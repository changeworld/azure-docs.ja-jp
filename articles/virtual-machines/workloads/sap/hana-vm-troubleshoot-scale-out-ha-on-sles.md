---
title: Azure 仮想マシン上での SLES 12 SP3 による SAP HANA 2.0 スケールアウトの HSR と Pacemaker のセットアップに対するトラブルシューティング | Microsoft Docs
description: Azure 仮想マシンで実行されている SLES 12 SP3 での SAP HANA システム レプリケーション (HSR) と Pacemaker に基づく複雑な SAP HANA スケールアウトの高可用性構成の確認とトラブルシューティングのガイド
services: virtual-machines-linux
documentationcenter: ''
author: hermannd
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: 6c0d6397246e8b8db1d59c26229e37a722d49f48
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2018
ms.locfileid: "47184656"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>SLES 12 SP3 での SAP HANA スケールアウトの高可用性セットアップの確認とトラブルシューティング 

[sles-pacemaker-ha-guide]:high-availability-guide-suse-pacemaker.md
[sles-hana-scale-out-ha-paper]:https://www.suse.com/documentation/suse-best-practices/singlehtml/SLES4SAP-hana-scaleOut-PerfOpt-12/SLES4SAP-hana-scaleOut-PerfOpt-12.html
[sap-hana-iaas-list]:https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html
[suse-pacemaker-support-log-files]:https://www.suse.com/support/kb/doc/?id=7022702
[azure-linux-multiple-nics]:https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics
[suse-cloud-netconfig]:https://www.suse.com/c/multi-nic-cloud-netconfig-ec2-azure/
[sap-list-port-numbers]:https://help.sap.com/viewer/ports
[sles-12-ha-paper]:https://www.suse.com/documentation/sle-ha-12/pdfdoc/book_sleha/book_sleha.pdf
[sles-zero-downtime-paper]:https://www.suse.com/media/presentation/TUT90846_towards_zero_downtime%20_how_to_maintain_sap_hana_system_replication_clusters.pdf
[sap-nw-ha-guide-sles]:high-availability-guide-suse.md
[sles-12-for-sap]:https://www.suse.com/media/white-paper/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf


この記事は、Azure の仮想マシンで実行されている SAP HANA スケールアウトの Pacemaker クラスター構成を確認するためのガイドとして作成されました。 クラスターは、SAP HANA システム レプリケーション (HSR) および SUSE RPM パッケージ SAPHanaSR-ScaleOut と組み合わせてセットアップされました。 すべてのテストは SUSE SLES 12 SP3 でのみ行われました。 さまざまな領域を扱う複数のセクションで構成されており、コマンド例および構成ファイルからの抜粋が含まれます。 これらの例を使用して、クラスターのセットアップ全体を確認することをお勧めします。



## <a name="important-notes"></a>重要

SAP HANA システム レプリケーションおよび Pacemaker と組み合わされた SAP HANA スケールアウトに対するすべてのテストは、SAP HANA 2.0 でのみ行われました。 オペレーティング システムのバージョンは SUSE Linux Enterprise Server 12 SP3 for SAP Applications でした。 また、Pacemaker クラスターのセットアップには SUSE の最新の RPM パッケージ SAPHanaSR-ScaleOut が使用されました。
SUSE では、このパフォーマンス最適化セットアップの詳細な説明が公開されています。[こちら][sles-hana-scale-out-ha-paper]をご覧ください。

SAP HANA スケールアウトでサポートされている仮想マシンの種類については、[SAP HANA 認定 IaaS ディレクトリ][sap-hana-iaas-list]に関するページを参照してください。

複数のサブネットおよび vNIC と組み合わされた SAP HANA スケールアウト、および HSR のセットアップでは、技術的な問題が発生していました。 この問題が修正されている最新の SAP HANA 2.0 修正プログラムを使用する必要があります。 サポートされている SAP HANA のバージョンは次のとおりです。 

**rev2.00.024.04 以降および rev2.00.032 以降。**

SUSE からのサポートが必要な場合は、この[ガイド][suse-pacemaker-support-log-files]に従ってください。 記事の説明に従って、SAP HANA HA クラスターに関するすべての情報を収集します。 SUSE サポートでは、さらに詳しい分析を行うためにこの情報が必要です。

内部テスト中、Azure portal を介した通常の正当な VM シャットダウンにより、クラスターのセットアップが混乱する事態が発生しました。 したがって、クラスターのフェールオーバーを他の方法でテストすることをお勧めします。 カーネル パニックの強制やネットワークのシャットダウンなどの方法を使用するか、または **msl** リソースを移行します (以下のセクションの詳細を参照)。 標準のシャットダウンが意図的に発生することが前提となります。 意図的なシャットダウンの最適な例は、メンテナンスです (計画メンテナンスに関するセクションの詳細を参照)。

内部テスト中、クラスターがメンテナンス モードの間に手動で SAP HANA の引き継ぎが行われた後、クラスターのセットアップが混乱する事態が発生しました。 クラスターのメンテナンス モードを終了する前に、手動で切り替えて元に戻すことをお勧めします。 もう 1 つのオプションとしては、クラスターをメンテナンス モードにする前にフェールオーバーをトリガーします (詳細については、計画メンテナンスに関するセクションを参照)。 SUSE のドキュメントでは、この点に関して、crm コマンドを使用してクラスターをリセットする方法について説明されています。 ただし、前述のアプローチは内部テスト中に堅牢であるように見え、予期しない副作用は何も発生しませんでした。

crm 移行コマンドを使用する際には、クラスター構成を必ずクリーンアップしてください。 このコマンドによって場所の制約が追加されますが、これは認識できない可能性があります。 これらの制約はクラスターの動作に影響を及ぼします (計画メンテナンスに関するセクションの詳細を参照)。



## <a name="test-system-description"></a>テスト システムの説明

SAP HANA スケールアウト HA の確認および認証では、それぞれ 3 つの SAP HANA ノード (1 つのマスターと 2 つのワーカー) を備えた 2 つのシステムで構成されるセットアップが使用されました。 VM 名と内部 IP アドレスの一覧を次に示します。 後半に示されている確認の例は、すべてこれらの VM で実行されました。 コマンド例内でこれらの VM 名と IP アドレスを使用すると、コマンドとその出力をより深く理解できます。


| ノードの種類 | VM 名 | IP アドレス |
| --- | --- | --- |
| サイト 1 のマスター ノード | hso-hana-vm-s1-0 | 10.0.0.30 |
| サイト 1 のワーカー ノード 1 | hso-hana-vm-s1-1 | 10.0.0.31 |
| サイト 1 のワーカー ノード 2 | hso-hana-vm-s1-2 | 10.0.0.32 |
| | | |
| サイト 2 のマスター ノード | hso-hana-vm-s2-0 | 10.0.0.40 |
| サイト 2 のワーカー ノード 1 | hso-hana-vm-s2-1 | 10.0.0.41 |
| サイト 2 のワーカー ノード 2 | hso-hana-vm-s2-2  | 10.0.0.42 |
| | | |
| マジョリティ メーカー ノード | hso-hana-dm | 10.0.0.13 |
| SBD デバイス サーバー | hso-hana-sbd | 10.0.0.19 |
| | | |
| NFS サーバー 1 | hso-nfs-vm-0 | 10.0.0.15 |
| NFS サーバー 2 | hso-nfs-vm-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>複数のサブネットおよび vNIC

SAP HANA ネットワークの推奨事項に従って、1 つの Azure 仮想ネットワーク内に 3 つのサブネットが作成されました。 Azure 上の SAP HANA スケールアウトは、非共有モードでインストールする必要があります。つまり、すべてのノードで **/hana/data** と **/hana/log** のローカル ディスク ボリュームが使用されます。 ローカル ディスク ボリュームのみを使用するので、ストレージ用に別のサブネットを定義する必要はありません。

- 10.0.2.0/24: SAP HANA ノード間通信
- 10.0.1.0/24: SAP HANA システム レプリケーション HSR
- 10.0.0.0/24: その他すべて

複数のネットワークの使用に関連する SAP HANA 構成については、後半の **global.ini** のセクションを参照してください。

サブネットの数に対応して、クラスター内のすべての VM には 3 つの vNIC があります。 [この][azure-linux-multiple-nics]記事では、Linux VM のデプロイ時に Azure で発生する可能性のあるルーティングの問題について説明します。 この特定のルーティングのトピックは、複数の vNIC を使用する場合にのみ適用されます。 SLES 12 SP3 では、この問題は既定で SUSE により解決されます。 このトピックに関する SUSE の記事は、[こちら][suse-cloud-netconfig]で参照できます。


複数のネットワークを使用するために SAP HANA が正しく構成されているかどうかを確かめる基本的な確認としては、以下のコマンドを実行します。 最初の手順では、3 つのすべてのサブネットに対する 3 つのすべての内部 IP アドレスがアクティブであることを、OS レベルで再確認します。 別の IP アドレス範囲でサブネットを定義した場合は、コマンドを調整する必要があります。

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

サイト 2 の 2 番目のワーカー ノードからの出力例を次に示します。 eth0、eth1、eth2 からの 3 つの異なる内部 IP アドレスを確認できます。

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


2 番目の手順では、ネームサーバーおよび HSR 用の SAP HANA ポートを確認します。 SAP HANA は、対応するサブネットでリッスンする必要があります。 SAP HANA インスタンス番号によっては、コマンドを調整する必要があります。 テスト システムでは、インスタンス番号は **00** でした。 使用されているポートを確認するには、さまざまな方法があります。 

インスタンス ID とインスタンス番号およびその他の情報を返す SQL ステートメントを次に示します。

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

正しいポート番号を確認するには、たとえば HANA Studio の **[Configuration]\(構成\)** で、または SQL ステートメントを使用して参照できます。

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

SAP HANA などの SAP ソフトウェア スタックで使用されている各ポートを確認するには、[こちら][sap-list-port-numbers]をご覧ください。

SAP HANA 2.0 テスト システムのインスタンス数が **00** なので、ネームサーバーのポート番号は **30001** です。 HSR メタ データ通信のポート番号は **40002** です。 1 つのオプションとしては、ワーカー ノードにサインインし、マスター ノードのサービスを確認します。 ここでは、サイト 2 のマスター ノードに接続しようとしているサイト 2 のワーカー ノード 2 で確認が行われました。

ネームサーバー ポートを確認します。

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

結果は次の出力例のようになり、ノード間通信でサブネット **10.0.2.0/24** が使用されていることが証明されます。
サブネット **10.0.2.0/24** 経由の接続のみが成功します。

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

ここで、HSR ポート **40002** を確認します。

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

結果は次の出力例のようになり、HSR 通信でサブネット **10.0.1.0/24** が使用されていることが証明されます。
サブネット **10.0.1.0/24** 経由の接続のみが成功します。

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


corosync 構成ファイルは、マジョリティ メーカー ノードを含むクラスター内のすべてのノードで正しい必要があります。 ノードのクラスター結合が想定どおりに動作しなかった場合は、すべてのノードに **/etc/corosync/corosync.conf** を手動で作成またはコピーし、サービスを再起動します。

例として、**corosync.conf** テスト システムの内容を次に示します。

この[ドキュメント][sles-pacemaker-ha-guide] (クラスターのインストールに関するセクションの手順 11) で説明されているように、最初のセクションは **totem** です。 **mcastaddr** の値は無視できます。 既存のエントリを保持します。 **token** と **consensus** のエントリは、Microsoft Azure SAP HANA のドキュメント ([こちら][sles-pacemaker-ha-guide]を参照) に従って設定する必要があります。

<pre><code>
totem {
    version:    2
    secauth:    on
    crypto_hash:    sha1
    crypto_cipher:  aes256
    cluster_name:   hacluster
    clear_node_high_bit: yes

    token:      30000
    token_retransmits_before_loss_const: 10
    join:       60
    consensus:  36000
    max_messages:   20

    interface {
        ringnumber:     0
        bindnetaddr: 10.0.0.0
        mcastaddr:  239.170.19.232
        mcastport:  5405

        ttl:        1
    }
    transport:      udpu

}
</code></pre>

2 番目のセクション **logging** は、特定の既定値から変更されていません。

<pre><code>
logging {
    fileline:   off
    to_stderr:  no
    to_logfile:     no
    logfile:    /var/log/cluster/corosync.log
    to_syslog:  yes
    debug:      off
    timestamp:  on
    logger_subsys {
        subsys:     QUORUM
        debug:  off
    }
}
</code></pre>

3 番目のセクションは **nodelist** を示しています。 クラスターのすべてのノードは、そのノード ID と共に表示される必要があります。

<pre><code>
nodelist {
  node {
   ring0_addr:hso-hana-vm-s1-0
   nodeid: 1
   }
  node {
   ring0_addr:hso-hana-vm-s1-1
   nodeid: 2
   }
  node {
   ring0_addr:hso-hana-vm-s1-2
   nodeid: 3
   }
  node {
   ring0_addr:hso-hana-vm-s2-0
   nodeid: 4
   }
  node {
   ring0_addr:hso-hana-vm-s2-1
   nodeid: 5
   }
  node {
   ring0_addr:hso-hana-vm-s2-2
   nodeid: 6
   }
  node {
   ring0_addr:hso-hana-dm
   nodeid: 7
   }
}
</code></pre>

最後のセクション **quorum** では、**expected_votes** の値を正しく設定することが重要です。 この値は、マジョリティ メーカー ノードを含むノードの数である必要があります。 また、**two_node** の値は **0** である必要があります。 エントリを完全に削除しないでください。 値を **0** に設定するだけです。

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


**systemctl** を使用してサービスを再起動します。

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>SBD デバイス

Azure VM で SBD デバイスをセットアップする方法については、[こちら][sles-pacemaker-ha-guide] (SBD フェンスのセクション) をご覧ください。

最初に SBD サーバー VM に対して、クラスター内のすべてのノードに ACL エントリがあるかどうかを確認します。 SBD サーバー VM で次のコマンドを実行します。


<pre><code>
targetcli ls
</code></pre>


テスト システムでは、コマンドの出力は次の例のようになります。 **iqn.2006-04.hso-db-0.local:hso-db-0** などの ACL 名を、VM 上の対応するイニシエーター名として入力する必要があります。 VM ごとに異なる名前が必要です。

<pre><code>
 | | o- sbddbhso ................................................................... [/sbd/sbddbhso (50.0MiB) write-thru activated]
  | |   o- alua ................................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................................................... [ALUA state: Active/optimized]
  | o- pscsi .................................................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................................................ [Targets: 1]
  | o- iqn.2006-04.dbhso.local:dbhso ..................................................................................... [TPGs: 1]
  |   o- tpg1 ............................................................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................................................... [ACLs: 7]
  |     | o- iqn.2006-04.hso-db-0.local:hso-db-0 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-1.local:hso-db-1 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-2.local:hso-db-2 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-3.local:hso-db-3 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-4.local:hso-db-4 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-5.local:hso-db-5 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-6.local:hso-db-6 .................................................................. [Mapped LUNs: 1]
</code></pre>

その後、すべての VM 上のイニシエーター名が異なり、上記のエントリに対応していることを確認します。 サイト 1 のワーカー ノード 1 の例を次に示します。

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

出力は次の例のようになります。

<pre><code>
##
## /etc/iscsi/iscsi.initiatorname
##
## Default iSCSI Initiatorname.
##
## DO NOT EDIT OR REMOVE THIS FILE!
## If you remove this file, the iSCSI daemon will not start.
## If you change the InitiatorName, existing access control lists
## may reject this initiator.  The InitiatorName must be unique
## for each iSCSI initiator.  Do NOT duplicate iSCSI InitiatorNames.
InitiatorName=iqn.2006-04.hso-db-1.local:hso-db-1
</code></pre>

次に、**discovery** が正しく動作するかどうかを確認し、SBD サーバー VM の IP アドレスを使用して、すべてのクラスター ノードで次のコマンドを実行します。

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

出力は次の例のようになります。

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

次のプルーフ ポイントでは、ノードで SDB デバイスが認識されていることを確認します。 この確認は、マジョリティ メーカー ノードを含むすべてのノードで行います。

<pre><code>
lsscsi | grep dbhso
</code></pre>

出力は次の例のようになります。 名前が異なる場合があることに注意してください (VM の再起動後にデバイス名も変化することがあります)。

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

システムの状態によっては、iscsi サービスを再起動すると問題の解決に役立つ場合があります。 次のコマンドを実行します。

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


任意のノードから、すべてのノードが **clear** であるかどうかを確認できます。 ただし、そのノードで正しいデバイス名を使用するよう注意してください。

<pre><code>
sbd -d /dev/sdm list
</code></pre>

クラスター内のすべてのノードについて、出力に **clear** と表示されるはずです。

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


SBD の別のチェックでは、sbd コマンドの **dump** オプションを使用します。 コマンドの例と、マジョリティ メーカー ノードからの出力を次に示します。ここでは、デバイス名は **sdm** ではなく **sdd** です。

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

出力 (デバイス名以外) は、すべてのノードで同じになるはずです。

<pre><code>
==Dumping header on disk /dev/sdd
Header version     : 2.1
UUID               : 9fa6cc49-c294-4f1e-9527-c973f4d5a5b0
Number of slots    : 255
Sector size        : 512
Timeout (watchdog) : 60
Timeout (allocate) : 2
Timeout (loop)     : 1
Timeout (msgwait)  : 120
==Header on disk /dev/sdd is dumped
</code></pre>

SBD のもう 1 つのチェックとして、別のノードにメッセージを送信できるかどうかを確認します。 サイト 2 のワーカー ノード 2 にメッセージを送信するには、サイト 2 のワーカー ノード 1 で次のコマンドを実行します。

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

ターゲット VM 側 (この例では **hso-hana-vm-s2-2**) の **/var/log/messages** に、次のエントリがあります。

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

**/etc/sysconfig/sbd** のエントリが、[ドキュメント][sles-pacemaker-ha-guide] (SBD フェンスのセクション) の説明に対応しているかどうかを再確認します。 **/etc/iscsi/iscsid.conf** のスタートアップ設定が自動に設定されていることを確認します。

**/etc/sysconfig/sbd** の重要なエントリを次に示します (必要に応じて ID 値を調整します)。

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


確認するもう 1 つの項目は、**/etc/iscsi/iscsid.conf** のスタートアップ設定です。 ドキュメントで説明されているように、次に示す **iscsiadm** コマンドによって、必要な設定が行われています。 この設定を確認し、異なる場合は **vi** を使用して手動で調整することをお勧めします。

スタートアップ動作を設定するコマンドは次のとおりです。

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

**/etc/iscsi/iscsid.conf** のエントリを次に示します。

<pre><code>
node.startup = automatic
</code></pre>

テストおよび検証中に、VM の再起動後に SBD デバイスが表示されなくなる状況が発生しました。 スタートアップ設定と、yast2 によって表示される内容との間に相違が見られました。 設定を再確認するには、以下の手順を実行します。

1. yast2 を起動します。
2. 左側にある **[Network Services]\(ネットワーク サービス\)** を選択します。
3. 右側で **[iSCSI Initiator]\(iSCSI イニシエーター\)** までスクロールして選択します。
4. 次の画面の **[Service]\(サービス\)** タブに、ノードの一意のイニシエーター名が表示されます。
5. イニシエーター名の上にある **[Service Start]\(サービスの開始\)** 値が **[When Booting]\(起動時\)** に設定されていることを確認します。
6. そうでない場合は、**[Manually]\(手動\)** の代わりに **[When Booting]\(起動時\)** に設定します。
7. 次に、上部のタブを **[Connected Targets]\(接続済みのターゲット\)** に切り替えます。
8. [Connected Targets]\(接続済みのターゲット\) 画面に、この例: **10.0.0.19:3260 iqn.2006-04.dbhso.local:dbhso** のような SBD デバイスのエントリが表示されます。
9. スタートアップ値が "**onboot**" に設定されているかどうかを確認します。
10. そうでない場合は、**[Edit]\(編集\)** を選択して変更します。
11. 変更内容を保存し、yast2 を終了します。



## <a name="pacemaker"></a>Pacemaker

すべてのセットアップが正しく行われたら、すべてのノードで次のコマンドを実行して、Pacemaker サービスの状態を確認できます。

<pre><code>
systemctl status pacemaker
</code></pre>

出力の先頭は次の例のようになります。 **Active** の後の状態は **loaded** および **active (running)** と表示される必要があります。 "Loaded" の後の状態は **enabled** と表示される必要があります。

<pre><code>
  pacemaker.service - Pacemaker High Availability Cluster Manager
   Loaded: loaded (/usr/lib/systemd/system/pacemaker.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-09-07 05:56:27 UTC; 4 days ago
     Docs: man:pacemakerd
           http://clusterlabs.org/doc/en-US/Pacemaker/1.1-pcs/html/Pacemaker_Explained/index.html
 Main PID: 4496 (pacemakerd)
    Tasks: 7 (limit: 4915)
   CGroup: /system.slice/pacemaker.service
           ├─4496 /usr/sbin/pacemakerd -f
           ├─4499 /usr/lib/pacemaker/cib
           ├─4500 /usr/lib/pacemaker/stonithd
           ├─4501 /usr/lib/pacemaker/lrmd
           ├─4502 /usr/lib/pacemaker/attrd
           ├─4503 /usr/lib/pacemaker/pengine
           └─4504 /usr/lib/pacemaker/crmd
</code></pre>

設定が **disabled** のままの場合は、次のコマンドを実行します。

<pre><code>
systemctl enable pacemaker
</code></pre>

Pacemaker 内の構成済みのすべてのリソースを表示するには、次のコマンドを実行します。

<pre><code>
crm status
</code></pre>

出力は次の例のようになります。 マジョリティ メーカー VM (**hso-hana-dm**) で cln と msl のリソースが stopped と表示されていても問題ありません。 マジョリティ メーカー ノードには SAP HANA インストールがありません。 そのため、**cln** と **msl** のリソースは stopped と表示されています。 VM の正確な総数が示される必要があります (**7**)。 クラスターの一部であるすべての VM が、**Online** の状態で一覧表示される必要があります。 現在のプライマリ マスター ノードが正しく認識される必要があります (この例では、**hso-hana-vm-s1-0**)。

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Tue Sep 11 15:56:40 2018
Last change: Tue Sep 11 15:56:23 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0
</code></pre>

Pacemaker の重要な機能は、メンテナンス モードにすることです。 このモードでは、即時にクラスター アクション (たとえば、VM の再起動) を誘発することなく変更を行うことができます。 一般的なユース ケースは、OS または Azure インフラストラクチャの計画メンテナンスです (計画メンテナンスに関する別のセクションを参照)。 Pacemaker をメンテナンス モードにするには、次のコマンドを使用します。

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

**crm status** を使用して確認すると、出力内ですべてのリソースが **unmanaged** とマークされていることがわかります。 この状態では、クラスターは SAP HANA の開始や停止などのいずれの変更にも反応しません。
クラスターがメンテナンス モード中の **crm status** コマンドの出力例を次に示します。

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Wed Sep 12 07:48:10 2018
Last change: Wed Sep 12 07:46:54 2018 by root via cibadmin on hso-hana-vm-s2-1

7 nodes configured
17 resources configured

              *** Resource management is DISABLED ***
  The cluster will not attempt to start, stop or recover services

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm (unmanaged)
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00] (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-0 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00] (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Master hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm hso-hana-vm-s1-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s2-0 (unmanaged)
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s2-0 (unmanaged)
</code></pre>


また、次のコマンド例では、クラスターのメンテナンス モードを終了する方法を示しています。

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


別の crm コマンドでは、編集可能になるようエディター内に完全なクラスター構成を取得することができます。 変更を保存したら、クラスターでは適切なアクションが開始されます。

<pre><code>
crm configure edit
</code></pre>

クラスター構成全体を単に確認するには、crm の **show** オプションを使用します。

<pre><code>
crm configure show
</code></pre>



クラスター リソースのエラーが発生した後は、**crm status** コマンドによって **Failed Actions** の一覧が表示されます。 次のこの出力の例を参照してください。


<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Thu Sep 13 07:30:44 2018
Last change: Thu Sep 13 07:30:20 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm hso-hana-vm-s2-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0

Failed Actions:
* rsc_SAPHanaCon_HSO_HDB00_monitor_60000 on hso-hana-vm-s2-0 'unknown error' (1): call=86, status=complete, exitreason='none',
    last-rc-change='Wed Sep 12 17:01:28 2018', queued=0ms, exec=277663ms
</code></pre>

エラーの発生後は、クラスターのクリーンアップを実行する必要があります。 次に示すように対応するクラスター リソースを指定して、crm コマンドを再度使用し、コマンド オプション **cleanup** を使用して、エラーが発生したこれらのアクション エントリを終了します。

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

コマンドにより、次の例のような出力が返されます。

<pre><code>
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-dm
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-2
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-2
Waiting for 7 replies from the CRMd....... OK
</code></pre>



## <a name="failover--takeover"></a>フェールオーバーと引き継ぎ

最初のセクションの重要な注意事項で既に説明したように、通常の正当なシャットダウンを使用してクラスターのフェールオーバーまたは SAP HANA HSR の引き継ぎをテストすることはできません。 代わりに、カーネル パニックなどをトリガーするか、リソースの移行を強制するか、または VM の OS レベルですべてのネットワークをシャットダウンすることをお勧めします。 もう 1 つの方法としては、**crm \<node\> standby** コマンドを使用します。 また、[こちら][sles-12-ha-paper]の SUSE ドキュメントも参照してください。 クラスターのフェールオーバーを強制する 3 つのコマンド例を次に示します。

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

計画メンテナンスに関するセクションで説明されているように、クラスター アクティビティを監視するには、**watch** コマンドと共に **SAPHanaSR-showAttr** を実行することをお勧めします。

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

さらに、SAP Python スクリプトからの SAP HANA ランドスケープの状態を確認すると役立ちます。 この状態値は、クラスターのセットアップで求められる値です。 このことは、ワーカー ノードのエラーについて考えるとよくわかります。 ワーカー ノードがダウンした場合、SAP HANA では、スケールアウト システム全体の正常性のエラーはすぐに返されません。 不要なフェールオーバーを回避するために、数回の再試行が行われます。 クラスターは、状態が正常 (戻り値 4) からエラー (戻り値 1) に変化した場合にのみ反応します。 したがって、**SAPHanaSR showAttr** からの出力に **offline** 状態の VM が示されているが、プライマリとセカンダリを切り替えるアクティビティがまだ発生しないのは、正常な動作です。 SAP HANA によってエラーが返されない限り、クラスター アクティビティはトリガーされません。

次のようにして (場合によっては、パスを調整する必要があります)、SAP python スクリプトを呼び出すことで、ユーザー \<HANA SID\>adm として SAP HANA ランドス ケープの正常性状態を監視することができます。

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

このコマンドの出力は、次の例のようになります。 重要なのは、**Host Status** 列と **overall host status** です。 実際の出力は、さらに列が表示されて幅広になっています。
このドキュメントでは、出力テーブルを見やすくするために、右側にあるほとんどの列を削除しています。

<pre><code>
| Host             | Host   | Host   | Failover | Remove | 
|                  | Active | Status | Status   | Status | 
|                  |        |        |          |        | 
| ---------------- | ------ | ------ | -------- | ------ |    .......
| hso-hana-vm-s2-0 | yes    | ok     |          |        |        
| hso-hana-vm-s2-1 | yes    | ok     |          |        |         
| hso-hana-vm-s2-2 | yes    | ok     |          |        |        

overall host status: ok
</code></pre>


現在のクラスター アクティビティを確認するもう 1 つのコマンドがあります。 次のコマンドと、プライマリ サイトのマスター ノードが強制終了された後の出力の末尾をご覧ください。 前のセカンダリ マスター ノード (**hso-hana-vm-s2-0**) を新しいプライマリ マスターとして**昇格**させるなど、遷移アクションの一覧が表示されています。 何も問題がなく、すべてのアクティビティが終了したら、**Transition Summary** のこの一覧は空になります。

<pre><code>
 crm_simulate -Ls

...........

Transition Summary:
 * Fence hso-hana-vm-s1-0
 * Stop    rsc_SAPHanaTop_HSO_HDB00:1   (hso-hana-vm-s1-0)
 * Demote  rsc_SAPHanaCon_HSO_HDB00:1   (Master -> Stopped hso-hana-vm-s1-0)
 * Promote rsc_SAPHanaCon_HSO_HDB00:5   (Slave -> Master hso-hana-vm-s2-0)
 * Move    rsc_ip_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
 * Move    rsc_nc_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
</code></pre>



## <a name="planned-maintenance"></a>Azure の計画メンテナンス 

計画メンテナンスについては、さまざまなユース ケースがあります。 たとえば、OS レベルでの変更やディスク構成などのインフラストラクチャのメンテナンスだけであるか、または HANA アップグレードであるかという違いがあります。
追加情報については、[こちら][sles-zero-downtime-paper]や[こちら][sles-12-for-sap]など、SUSE のドキュメントを参照してください。 これらのドキュメントには、プライマリを手動で移行する方法の例も記載されています。

インフラストラクチャのメンテナンスのユース ケースを確認するために、集中的な内部テストが行われました。 プライマリの移行に関連するあらゆる種類の問題を回避するために、クラスターをメンテナンス モードにする前に、必ずプライマリを移行することが決定されました。 このようにすると、クラスターに前の状況 (どちらの側がプライマリで、どちら側がセカンダリであったか) を忘れさせる必要がなくなります。

この点については、次の 2 つの異なる状況があります。

1. 現在のセカンダリでの計画メンテナンス。 
   この場合は、クラスターをメンテナンス モードにし、クラスターに影響を与えることなくセカンダリで作業を行います。

2. 現在のプライマリでの計画メンテナンス。 
   メンテナンス時にユーザーが作業を続行できるようにするには、フェールオーバーを強制する必要があります。 このアプローチでは、SAP HANA HSR レベルだけでなく、Pacemaker によってクラスターのフェールオーバーをトリガーする必要があります。 Pacemaker のセットアップにより、SAP HANA の引き継ぎが自動的にトリガーされます。 さらに、クラスターをメンテナンス モードにする前にフェールオーバーを実行する必要があります。

現在のセカンダリ サイトでのメンテナンス手順は、次のようになります。

1. クラスターをメンテナンス モードにします。
2. セカンダリ サイトで作業を行います。 
3. クラスターのメンテナンス モードを終了します。

現在のプライマリ サイトでのメンテナンス手順は、より複雑です。

1. Pacemaker リソースの移行を使用して、手動でフェールオーバーまたは SAP HANA の引き継ぎをトリガーします (以下の詳細を参照)。
2. クラスターのセットアップにより、前のプライマリ サイト上の SAP HANA がシャットダウンされます。
3. クラスターをメンテナンス モードにします。
4. メンテナンス作業が完了したら、前のプライマリを新しいセカンダリ サイトとして登録します。
5. クラスター構成をクリーンアップします (以下の詳細を参照)。
6. クラスターのメンテナンス モードを終了します。


(たとえば、フェールオーバーを強制するために) リソースを移行すると、クラスター構成にエントリが追加されます。 メンテナンス モードを終了する前に、これらのエントリをクリーンアップする必要があります。 例を次に示します。

最初の手順では、msl リソースを現在のセカンダリ マスター ノードに移行することで、クラスターのフェールオーバーを強制します。 次のコマンドは、"移動制約" が作成されたという警告を示しています。

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


コマンド **SAPHanaSR-showAttr** を使用して、フェールオーバー プロセスを確認します。 クラスターの状態を監視するために、専用シェル ウィンドウを開き、**watch** を使用してコマンドを開始します。

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

出力は手動フェールオーバーを反映する必要があります。 前のセカンダリ マスター ノードが**昇格**し (この例では **hso-hana-vm-s2-0**)、前のプライマリ サイトが停止されました (前のプライマリ マスター ノード **hso-hana-vm-s1-0** の **lss** 値 **1**)。 

<pre><code>
Global cib-time                 prim  sec srHook sync_state
------------------------------------------------------------
global Wed Sep 12 07:40:02 2018 HSOS2 -   SFAIL  SFAIL


Sites lpt        lss mns              srr
------------------------------------------
HSOS1 10         1   hso-hana-vm-s1-0 P
HSOS2 1536738002 4   hso-hana-vm-s2-0 P


Hosts            clone_state node_state roles                        score  site
----------------------------------------------------------------------------------
hso-hana-dm                  online
hso-hana-vm-s1-0 UNDEFINED   online     master1::worker:             150    HSOS1
hso-hana-vm-s1-1 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s1-2 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s2-0 PROMOTED    online     master1:master:worker:master 150    HSOS2
hso-hana-vm-s2-1 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
hso-hana-vm-s2-2 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
</code></pre>

Pacemaker のセクションで説明されているように、クラスターのフェールオーバーと SAP HANA の引き継ぎが終了したら、クラスターをメンテナンス モードにします。

コマンド **SAPHanaSR-showAttr** または **crm status** は、リソースの移行によって作成された制約については何も示しません。 これらの制約を表示する 1 つのオプションとしては、次のコマンドを使用して、完全なクラスター リソース構成を表示します。

<pre><code>
crm configure show
</code></pre>

クラスター構成内で、以前の手動によるリソースの移行に起因する新しい場所の制約を見つけます。 例を次に示します (**location cli-** で始まるエントリ)。

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

残念ながらこのような制約は、クラスターの全体的な動作に影響を与える可能性があります。 したがって、システム全体をバックアップする前に、これらの制約をもう一度削除する必要があります。 **unmigrate** コマンドを使用すると、前に作成された場所の制約をクリーンアップできます。 このコマンドの名前は少し誤解されやすいかもしれません。 これは、リソースをその移行前の元の VM に移行し直すという意味ではありません。 コマンドを実行すると、場所の制約が削除され、対応する情報が返されるだけです。


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

Pacemaker のセクションで説明されているように、メンテナンス作業の最後に、クラスターのメンテナンス モードを停止します。



## <a name="hbreport-to-collect-log-files"></a>ログ ファイルを収集する hb_report

Pacemaker クラスターの問題を分析するには、**hb_report** ユーティリティの実行が有用であり、また SUSE サポートで要求されています。 これにより、すべての重要なログ ファイルが収集されて、何が起こったかを分析できます。 次に、特定のインシデント発生の開始時刻と終了時刻を使用した呼び出しの例を示します (重要な注意事項に関する最初のセクションも参照)。

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

コマンドにより、圧縮されたログ ファイルが格納される場所が出力されます。

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

その後、標準の **tar** コマンドを使用して、個々のファイルを抽出できます。

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

抽出したファイルを確認して、すべてのログ ファイルを見つけます。 これらのファイルのほとんどは、クラスター内のノードごとに別々のディレクトリに格納されています。

<pre><code>
-rw-r--r-- 1 root root  13655 Sep 13 09:01 analysis.txt
-rw-r--r-- 1 root root  14422 Sep 13 09:01 description.txt
-rw-r--r-- 1 root root      0 Sep 13 09:01 events.txt
-rw-r--r-- 1 root root 275560 Sep 13 09:00 ha-log.txt
-rw-r--r-- 1 root root     26 Sep 13 09:00 ha-log.txt.info
drwxr-xr-x 4 root root   4096 Sep 13 09:01 hso-hana-dm
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-2
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-2
-rw-r--r-- 1 root root 264726 Sep 13 09:00 journal.log
</code></pre>


指定された時間範囲内に、現在のマスター ノード **hso-hana-vm-s1-0** が強制終了されました。 **Journal.log** 内で、このイベントに関連するエントリを見つけることができます。

<pre><code>
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: (to hsoadm) root on none
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session opened for user hsoadm by (uid=0)
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 systemd[1]: Started Session c44290 of user hsoadm.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] A new membership (10.0.0.13:120996) was formed. Members left: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] Failed to receive the leave message. failed: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Removing all hso-hana-vm-s1-0 attributes for peer loss
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [QUORUM] Members[6]: 7 2 3 4 5 6
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [MAIN  ] Completed service synchronization, ready to provide service.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 crmd[28315]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 pacemakerd[28308]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:03+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session closed for user hsoadm
</code></pre>

もう 1 つの例として、セカンダリ マスター上の Pacemaker ログ ファイルが、新しいプライマリ マスターになりました。 次の引用部分は、強制終了されたプライマリ マスター ノードの状態が **offline** に設定されたことを示しています。

<pre><code>
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 3 still member of group stonith-ng (peer=hso-hana-vm-s1-2, counter=5.1)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 4 still member of group stonith-ng (peer=hso-hana-vm-s2-0, counter=5.2)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 5 still member of group stonith-ng (peer=hso-hana-vm-s2-1, counter=5.3)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 6 still member of group stonith-ng (peer=hso-hana-vm-s2-2, counter=5.4)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 7 still member of group stonith-ng (peer=hso-hana-dm, counter=5.5)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 1 left group crmd (peer=hso-hana-vm-s1-0, counter=5.0)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: crm_update_peer_proc:     pcmk_cpg_membership: Node hso-hana-vm-s1-0[1] - corosync-cpg is now offline
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: peer_update_callback:     Client hso-hana-vm-s1-0/peer now has status [offline] (DC=hso-hana-dm, changed=4000000)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 2 still member of group crmd (peer=hso-hana-vm-s1-1, counter=5.0)
</code></pre>





## <a name="sap-hana-globalini"></a>SAP HANA global.ini


クラスター サイト 2 上の SAP HANA global.ini ファイルからの次の引用部分は、SAP HANA ノード間通信と HSR 用に異なるネットワークを使用するためのホスト名解決エントリの例を示しています。

<pre><code>
[communication]
tcp_keepalive_interval = 20
internal_network = 10.0.2/24
listeninterface = .internal
</code></pre>


<pre><code>
[internal_hostname_resolution]
10.0.2.40 = hso-hana-vm-s2-0
10.0.2.42 = hso-hana-vm-s2-2
10.0.2.41 = hso-hana-vm-s2-1
</code></pre>

<pre><code>
[ha_dr_provider_SAPHanaSR]
provider = SAPHanaSR
path = /hana/shared/myHooks
execution_order = 1
</code></pre>

<pre><code>
[system_replication_communication]
listeninterface = .internal

[system_replication_hostname_resolution]
10.0.1.30 = hso-hana-vm-s1-0
10.0.1.31 = hso-hana-vm-s1-1
10.0.1.32 = hso-hana-vm-s1-2
10.0.1.40 = hso-hana-vm-s2-0
10.0.1.41 = hso-hana-vm-s2-1
10.0.1.42 = hso-hana-vm-s2-2
</code></pre>



## <a name="hawk"></a>HAWK

また、クラスター ソリューションでは、シェル レベルでのすべてのコマンドよりもメニューやグラフィックスを好むユーザー向けに、優れた GUI を提供するブラウザー インターフェイスが用意されています。
ブラウザー インターフェイスを使用するには、次に示す URL を取得し、**\<node\>** を実際の SAP HANA ノードに置き換えてから、クラスターの資格情報を入力します (ユーザー **hacluster**)。

<pre><code>
https://&ltnode&gt:7630
</code></pre>

次のスクリーンショットは、クラスター ダッシュボードを示しています。


![HAWK クラスター ダッシュボード](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


2 番目のスクリーンショットは、計画メンテナンスのセクションで説明されているように、クラスター リソースの移行に起因する場所の制約の例を示しています。


![HAWK で制約を一覧表示する](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


もう 1 つの優れた機能は、次のスクリーンショットに示すように、**HAWK** の**履歴**で **hb_report** 出力をアップロードできることです (**hb_report** に関するセクションを参照)。

![HAWK で hb_report 出力をアップロードする](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

その後、**履歴エクスプローラー**により、**hb_report** 出力に含まれるクラスターのすべての遷移を確認できます。

![HAWK で hb_report 出力内の遷移を表示する](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

最後のスクリーンショットは、1 つの遷移の詳細セクションを示しており、クラスターがプライマリ マスター ノードのクラッシュに反応したことがわかります (ノード **hso-hana-vm-s1-0**)。また、セカンダリ ノードが新しいマスターとして昇格しています (**hso-hana-vm-s2-0**)。

![HAWK で 1 つの遷移を確認する](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>次の手順

このトラブルシューティング ガイドでは、スケールアウト構成での SAP HANA の高可用性について説明しました。 データベース以外で、SAP ランドスケープ内のもう 1 つの重要なコンポーネントは、SAP NetWeaver スタックです。 次は、SUSE Enterprise Linux Server を使用した Azure の仮想マシン上の SAP NetWeaver の高可用性について、[この][sap-nw-ha-guide-sles]記事をお読みください。

