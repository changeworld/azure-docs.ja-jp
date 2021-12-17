---
title: Azure NetApp Files の SMB のパフォーマンスのベスト プラクティス | Microsoft Docs
description: Azure NetApp Files の SMB のパフォーマンスとベスト プラクティスを理解するのに役立ちます。
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
ms.date: 06/10/2021
ms.author: b-juche
ms.openlocfilehash: dd909362950d961b4dcf944ab608c5103840e9a7
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130239998"
---
# <a name="smb-performance-best-practices-for-azure-netapp-files"></a>Azure NetApp Files の SMB のパフォーマンスのベスト プラクティス

この記事は、Azure NetApp Files の SMB のパフォーマンスとベスト プラクティスを理解するのに役立ちます。

## <a name="smb-multichannel"></a>SMB マルチチャネル

SMB マルチチャネルは、SMB 共有で既定で有効になっています。 既存の SMB ボリュームより前から存在していたすべての SMB 共有では機能が有効にされており、新しく作成されるすべてのボリュームでも、作成時に機能が有効になります。 

SMB マルチチャネル機能を利用するには、機能が有効になる前に確立された SMB 接続をすべてリセットする必要があります。 リセットするには、SMB 共有を切断して再接続します。

Windows では、最適なパフォーマンスを実現するため、Windows 2012 以降で SMB マルチチャネルがサポートされています。  詳しくは、「[SMB マルチチャネルを展開する](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/dn610980(v%3Dws.11))」および [SMB マルチチャネルの基礎](/archive/blogs/josebda/the-basics-of-smb-multichannel-a-feature-of-windows-server-2012-and-smb-3-0)に関する記事をご覧ください。 

### <a name="benefits-of-smb-multichannel"></a>SMB マルチチャネルの利点

SMB マルチチャネル機能を使用すると、SMB3 クライアントでは、単一のネットワーク インターフェイス カード (NIC) または複数の NIC を介して接続のプールを確立し、それらを使用して 1 つの SMB セッションに要求を送信することができます。 これに対し、SMB1 と SMB2 では、設計上、クライアントは 1 つの接続を確立し、特定のセッションに対するすべての SMB トラフィックを、その接続を介して送信する必要があります。 この単一接続により、単一のクライアントから実現できるプロトコル全体のパフォーマンスが制限されます。

### <a name="performance-for-smb-multichannel"></a>SMB マルチチャネルのパフォーマンス

次のテストとグラフは、単一インスタンス ワークロードにおける SMB マルチチャネルの能力を示したものです。

#### <a name="random-io"></a>ランダム I/O  

クライアントで SMB マルチチャネルを無効にし、FIO と 40 GiB のワーキング セットを使用して、純粋な 4 KiB の読み取りと書き込みのテストを実行しました。  SMB 共有は各テスト間でデタッチされており、RSS ネットワーク インターフェイス設定あたりの SMB クライアント接続数の増分は `1`、`4`、`8`、`16`、`set-SmbClientConfiguration -ConnectionCountPerRSSNetworkInterface <count>` でした。 このテストでは、既定の設定 `4` で、I/O 集中型のワークロードに十分であることが示されています。`8` や `16` に増やしても影響はほとんどありませんでした。 

コマンド `netstat -na | findstr 445` により、接続が `1`、`4`、`8`、`16` と追加して確立されたことが示されています。  各テストでは 4 個の CPU コアが SMB に完全に利用されました。これは、perfmon の `Per Processor Network Activity Cycles` の統計によって確認されています (この記事には記載されていません)。

![SMB マルチチャネルのランダム I/O の比較を示す図。](../media/azure-netapp-files/azure-netapp-files-random-io-tests.png)

Azure 仮想マシンは、SMB (または NFS) のストレージ I/O 制限には影響しません。  次のグラフに示すように、インスタンスの種類が D32ds の場合、キャッシュされたストレージ IOPS では 308,000、キャッシュされていないストレージ IOPS では 51,200 に速度が制限されます。  ただし、上のグラフでは、SMB の方が I/O がはるかに多いことが示されています。

![ランダム I/O の比較テストを示すグラフ。](../media/azure-netapp-files/azure-netapp-files-random-io-tests-list.png)

#### <a name="sequential-io"></a>シーケンシャル I/O 

前述のランダム I/O のテストと同様のテストを、64 KiB のシーケンシャル I/O で実行しました。 ランダム I/O では RSS ネットワーク インターフェイスあたりのクライアント接続数を 4 より大きくしても大きな効果はありませんでしたが、シーケンシャル I/O には同じことは当てはまりません。 次のグラフが示すように、接続数を増やすたびに、それに対応して読み取りスループットも増加します。 書き込みスループットは、インスタンスの種類とサイズごとに Azure によって課せられるネットワーク帯域幅の制限により、フラットなままです。 

![スループット テストの比較を示すグラフ。](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests.png)

Azure では、仮想マシンの種類とサイズごとにネットワーク速度が制限されます。 速度制限は、送信トラフィックに対してのみ適用されます。 仮想マシンに存在する NIC の数は、マシンで利用可能な合計帯域幅には影響しません。  たとえば、インスタンスの種類が D32ds の場合、16,000 Mbps (2,000 MiB/秒) のネットワーク制限が適用されます。  上のシーケンシャルのグラフで示されているように、この制限は送信トラフィック (書き込み) には影響しますが、マルチチャネルの読み取りには影響しません。

![シーケンシャル I/O の比較テストを示すグラフ。](../media/azure-netapp-files/azure-netapp-files-sequential-io-tests-list.png)

## <a name="smb-signing"></a>SMB 署名

SMB プロトコルでは、ファイルや印刷の共有や、Windows のリモート管理などの他のネットワーク操作に対する基盤が提供されます。 転送中に SMB パケットを変更する中間者攻撃を防ぐために、SMB プロトコルでは SMB パケットのデジタル署名がサポートされています。 

SMB 署名は、Azure NetApp Files によってサポートされているすべての SMB プロトコル バージョンでサポートされています。 

### <a name="performance-impact-of-smb-signing"></a>SMB 署名によるパフォーマンスへの影響  

SMB 署名を使用すると、SMB のパフォーマンスに悪影響があります。 パフォーマンスが低下する可能性のある原因の 1 つは、以下の perfmon の出力に示すように、各パケットのデジタル署名によってクライアント側の CPU が余計に使用されることです。 この場合、コア 0 で SMB 署名を含む SMB の処理が行われています。  前のセクションのマルチチャネルではない場合のシーケンシャル読み取りスループットの数値と比較すると、SMB 署名により、全体的なスループットが 875 MiB/秒から約 250 MiB/秒に低下することがわかります。 

![SMB 署名のパフォーマンスの影響を示すグラフ。](../media/azure-netapp-files/azure-netapp-files-smb-signing-performance.png)


## <a name="performance-for-a-single-instance-with-a-1-tb-dataset"></a>1 TB のデータセットを使用する単一インスタンスのパフォーマンス

読み取り/書き込みを組み合わせたワークロードの詳細な分析情報を提供するために、次の 2 つのグラフで、1 TB のデータセットと SMB マルチチャネル 4 を使用した、50 TB の単一の Ultra サービスレベル クラウド ボリュームのパフォーマンスを示します。 最適な IODepth 16 が使用されました。また、ネットワーク帯域幅を最大限に使用するために、Flexible IO (FIO) パラメーターが使用されました (`numjobs=16`)。

次のグラフは、1 つの VM インスタンスと、10% 間隔での読み取り/書き込みの組み合わせを使用した、4k のランダム I/O の結果を示しています。

![Windows 2019 standard _D32ds_v4 4K のランダム IO テストを示すグラフ。](../media/azure-netapp-files/smb-performance-standard-4k-random-io.png)

次のグラフは、シーケンシャル I/O の結果を示しています。

![Windows 2019 standard _D32ds_v4 64K のシーケンシャル スループットを示すグラフ。](../media/azure-netapp-files/smb-performance-standard-64k-throughput.png)

## <a name="performance-when-scaling-out-using-5-vms-with-a-1-tb-dataset"></a>1 TB のデータセットを使用する 5 台の VM を使ってスケールアウトした場合のパフォーマンス

5 台の VM を使ったこれらのテストでは、単一の VM と同じテスト環境が使用され、各プロセスは独自のファイルに書き込みを行います。

次のグラフは、ランダム I/O の結果を示しています。

![Windows 2019 standard _D32ds_v4 4K の 5 つのインスタンスのランダム IO テストを示すグラフ。](../media/azure-netapp-files/smb-performance-standard-4k-random-io-5-instances.png)

次のグラフは、シーケンシャル I/O の結果を示しています。

![Windows 2019 standard _D32ds_v4 64K の 5 つのインスタンスのシーケンシャル スループットを示すグラフ。](../media/azure-netapp-files/smb-performance-standard-64k-throughput-5-instances.png)

## <a name="how-to-monitor-hyper-v-ethernet-adapters"></a>Hyper-V イーサネット アダプターを監視する方法  

FIO を使ったテストで使用される戦略の 1 つは、`numjobs=16` を設定することです。 これにより各ジョブが 16 個の特定のインスタンスに分岐され、Microsoft Hyper-V ネットワーク アダプターが最大化されます。

Windows パフォーマンス モニターで各アダプターのアクティビティを確認するには、 **[パフォーマンス モニター] > [カウンターの追加] > [ネットワーク インターフェイス] > [Microsoft Hyper-V ネットワーク アダプター]** の順に選択します。

![パフォーマンス モニターの [カウンターの追加] インターフェイスを示すスクリーンショット。](../media/azure-netapp-files/smb-performance-performance-monitor-add-counter.png)

データ トラフィックがボリューム内で実行されたら、Windows パフォーマンス モニターでアダプターを監視できます。 これらの 16 個すべての仮想アダプターを使用しない場合は、ネットワーク帯域幅の容量が最大化されない可能性があります。

![パフォーマンス モニターの出力を示すスクリーンショット。](../media/azure-netapp-files/smb-performance-performance-monitor-output.png)

## <a name="smb-encryption"></a>SMB 暗号化

このセクションは、SMB 暗号化 (SMB 3.0 および SMB 3.1.1) を理解するのに役立ちます。 

[SMB 暗号化](/windows-server/storage/file-server/smb-security)は、SMB データをエンド ツー エンドで暗号化し、信頼できないネットワークで発生する傍受からデータを保護できます。 SMB 暗号化は、SMB 3.0 以上でサポートされています。 

要求をストレージに送信するときに、クライアントは要求を暗号化します。この暗号化がストレージによって解除されます。 応答は、同様にサーバーによって暗号化され、クライアントによって暗号化が解除されます。

SMB 暗号化は、Windows 10、および Windows 2012 以降のバージョンでサポートされています。

### <a name="smb-encryption-and-azure-netapp-files"></a>SMB 暗号化と Azure NetApp Files

Azure NetApp Files では、SMB 暗号化は共有レベルで有効になっています。 SMB 3.0 では AES-CCM アルゴリズム、SMB 3.1.1 では AES-GCM アルゴリズムが採用されています。

SMB 暗号化は必須ではありません。 そのため、ユーザーが Azure NetApp Files で有効にするように要求した場合に、特定の共有に対してのみ有効になります。 Azure NetApp Files 共有がインターネットに公開されることはありません。 特定の VNet 内から、VPN または ExpressRoute 経由でのみアクセスできます。したがって、Azure NetApp Files 共有は本質的にセキュリティで保護されています。 SMB 暗号化を有効にするかどうかは、ユーザーが選択します。 この機能を有効にする前に、パフォーマンスの低下が予想されることを理解しておいてください。

### <a name="impact-of-smb-encryption-on-client-workloads"></a><a name="smb_encryption_impact"></a>クライアント ワークロードに対する SMB 暗号化の影響

SMB 暗号化は、クライアント (メッセージの暗号化と復号化のための CPU オーバーヘッド) とストレージ (スループットの低下) の両方に影響を与えますが、次の表は、ストレージに対する影響のみを示しています。 ワークロードを運用環境にデプロイする前に、自分のアプリケーションのパフォーマンスに対する暗号化の影響をテストする必要があります。

|     I/O プロファイル       |     影響        |
|-  |-  |
|     ワークロードの読み取りと書き込み      |     10% ～ 15%        |
|     メタデータ集中型        |     5%    |

## <a name="accelerated-networking"></a>高速ネットワーク 

パフォーマンスを最大化するために、可能であれば、仮想マシンで[高速ネットワーク](../virtual-network/create-vm-accelerated-networking-powershell.md)を構成することをお勧めします。 以下の点に注意してください。  

* Azure portal では、この機能をサポートする仮想マシンに対し、既定で高速ネットワークが有効になります。  ただし、Ansible やそれと似た構成ツールなどの他のデプロイ方法では、有効にならない場合があります。  高速ネットワークを有効にしないと、マシンのパフォーマンスが低下する可能性があります。  
* インスタンスの種類またはサイズがサポートされていないために仮想マシンのネットワーク インターフェイスで高速ネットワークが有効になっていない場合、インスタンスの種類を大きくしても無効のままです。 そのような場合は、手動で介入する必要があります。

## <a name="rss"></a>RSS 

Azure NetApp Files では、受信側のスケーリング (RSS) がサポートされています。

SMB マルチチャネルを有効にすると、SMB3 クライアントでは、単一 RSS 対応のネットワーク インターフェイス カード (NIC) を介して、Azure NetApp Files SMB サーバーへの複数の TCP 接続が確立されます。 

お使いの Azure 仮想マシンの NIC で RSS がサポートされているかどうかを確認するには、次のようにコマンド `Get-SmbClientNetworkInterface` を実行し、フィールド `RSS Capable` を確認します。 

![Azure 仮想マシンの RSS 出力を示すスクリーンショット。](../media/azure-netapp-files/azure-netapp-files-formance-rss-support.png)


## <a name="multiple-nics-on-smb-clients"></a>SMB クライアント上の複数の NIC

SMB 用のクライアントで複数の NIC を構成しないでください。 SMB クライアントは、SMB サーバーから返される NIC の数に合わせます。  各ストレージ ボリュームには、1 つのストレージ エンドポイントからしかアクセスできません。  つまり、特定の SMB 関係に対して使用される NIC は 1 つだけです。  

次の `Get-SmbClientNetworkInterace` の出力でわかるように、この仮想マシンには 2 つのネットワーク インターフェイス 15 と 12 があります。  次のコマンド `Get-SmbMultichannelConnection` の下に示されているように、RSS 対応の NIC は 2 つありますが、SMB 共有との接続にはインターフェイス 12 のみが使用され、インターフェイス 15 は使用されていません。

![RSS 対応の NIC の出力を示すスクリーンショット。](../media/azure-netapp-files/azure-netapp-files-rss-capable-nics.png)

## <a name="next-steps"></a>次のステップ  

- [SMB に関する FAQ](faq-smb.md)
- 「[Azure NetApp Files: SMB ワークロード用のマネージド エンタープライズ ファイル共有](https://cloud.netapp.com/hubfs/Resources/ANF%20SMB%20Quickstart%20doc%20-%2027-Aug-2019.pdf?__hstc=177456119.bb186880ac5cfbb6108d962fcef99615.1550595766408.1573471687088.1573477411104.328&__hssc=177456119.1.1573486285424&__hsfp=1115680788&hsCtaTracking=cd03aeb4-7f3a-4458-8680-1ddeae3f045e%7C5d5c041f-29b4-44c3-9096-b46a0a15b9b1)」で、Azure NetApp Files での SMB ファイル共有の使用についてご覧ください。
