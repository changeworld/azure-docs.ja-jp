---
title: "Azure での Oracle データベースの設計と実装 | Microsoft Docs"
description: "ご利用の Azure 環境で Oracle データベースを設計および実装します。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/22/2017
ms.author: rclaus
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: e13e61a2d055ce4f9777cea8bf6199f2acf9e7bf
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---


# <a name="design-and-implement-of-oracle-database-on-azure"></a>Azure での Oracle データベースの設計と実装

## <a name="assumptions"></a>前提条件

- オンプレミスから Azure への Oracle データベースの移行を計画している
- Oracle AWR レポートの各種メトリックを理解している
- アプリケーションのパフォーマンスとプラットフォームの使用率に関するベースラインがある

## <a name="goals"></a>目標

- Azure で Oracle のデプロイを最適化する方法を理解する
- Azure 環境での Oracle データベースのパフォーマンスを調整するオプションを確認する

### <a name="on-premises-vs-on-azure"></a>オンプレミスと Azure の比較

ここでは、オンプレミスのアプリケーションを Azure に移行するときに考慮すべき重要なポイントをいくつかご紹介します。 オンプレミスとは異なり、Azure のリソース (仮想マシン、ディスク、VNet など) は他のクライアント間で共有されます。 また、要件に基づいてリソースをスロットルできます。 Azure では、失敗の回避 (MTBF) に焦点を当てる代わりに、失敗の克服 (MTTR) に注力しています。

次の表に、相違点の一部を掲載します。

> 
> |  | **オンプレミス** | **Azure** |
> | --- | --- | --- |
> | **ネットワーク** |LAN/WAN  |SDN - ソフトウェア定義ネットワーク|
> | **セキュリティ グループ** |IP/ポートの制限ツール |[ネットワーク セキュリティ グループ (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **回復力** |MTBF (平均故障間隔) |MTTR (平均復旧時間)|
> | **定期的なメンテナンス** |修正/更新プログラム|[可用性セット](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (修正/更新プログラムは Azure によって管理) |
> | **リソース** |専用  |他のクライアントと共有|
> | **リージョン** |データ センター |[リージョンのペア](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability)|
> | **Storage** |記憶域ネットワーク/物理ディスク |[Azure 管理のストレージ](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **スケール** |垂直スケール |水平スケール|


### <a name="requirements"></a>必要条件

- データベース サイズと増加率を決定します
- IOPS の要件を決定します。これは、Oracle AWR レポートや他のネットワーク監視ツールに基づいて見積もることができます

## <a name="configuration-options"></a>構成オプション

Azure 環境でのパフォーマンスを向上させるために調整できる領域が 4 つあります。

- 仮想マシンのサイズ
- ネットワーク スループット
- ディスクの種類と構成
- ディスク キャッシュの設定

### <a name="generate-awr-report"></a>AWR レポートの生成

既存の Oracle データベースがあり、Azure への移行を計画している場合は、 Oracle AWR レポートを実行してメトリック (IOPS、Mbps、GiB など) を取得し、収集したメトリックに基づいて仮想マシンを選択することができます。 または、自社のインフラストラクチャ チームに問い合わせて、同様の情報を取得することもできます。

差を比較できるように、通常のワークロード時とピークのワークロード時に AWR レポートを実行することを検討してください。 これらのレポートを参考に、平均のワークロードや最大のワークロードに基づいて、仮想マシンのサイズを決めることができます。

AWR レポートを生成する方法の例を次に示します。

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>主要なメトリック

AWR レポートから取得できるメトリックは次のとおりです。

- コアの合計数
- CPU のクロック速度
- メモリの合計 (GB)
- CPU 使用率
- ピーク時のデータ転送速度
- I/O 率の変化 (読み取り/書き込み)
- REDO ログの速度 (Mbps)
- ネットワーク スループット
- ネットワーク待機時間の割合 (高/低)
- データベース サイズ (GB)
- SQL*Net を介してクライアントとの間で送受信されたバイト数

### <a name="virtual-machine-size"></a>仮想マシンのサイズ

#### <a name="1-initial-estimate-of-vm-size-is-based-on-cpumemoryio-usage-from-the-awr-report"></a>1.まず AWR レポートの CPU、メモリ、I/O 使用率に基づき、仮想マシンのサイズを見積もる

確認することとしては、システムのボトルネックの場所を示す、合計待機時間が長かった上位 5 つのフォアグラウンドのイベントが挙げられます。

例: 次の図では、LGWR による REDO ログ ファイルへのログ バッファーの書き込みの待機回数を表す、ログ ファイルの同期が 1 位になっています。 このことから、ストレージやディスクの性能を改善する必要があることがわかります。 また図には、CPU (コア) とメモリの数値も表示されています。

![AWR レポート ページのスクリーンショット](./media/oracle-design/cpu_memory_info.png)

次の図は、読み取りおよび書き込みの I/O の合計を示します。 レポート期間中に、59 GB の読み取りと 247.3 GB の書き込みが行われました。

![AWR レポート ページのスクリーンショット](./media/oracle-design/io_info.png)

#### <a name="2-estimate-the-vm-size"></a>2.仮想マシンのサイズを見積もる

AWR レポートから収集した情報に基づき、次のステップでは要件を満たすサイズの仮想マシンを選択します。 使用可能な仮想マシンについては、こちらの[仮想マシンのサイズ](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes-memory)に関するリンクをご覧ください。

#### <a name="3-fine-tune-the-vm-sizing-with-similar-vm-series-based-on-the-acu"></a>3.ACU に基づき、同様の仮想マシン シリーズで仮想マシンのサイズを細かく調整する

仮想マシンを選択したら、仮想マシンの ACU に注意を向けてください。 お客様の要件に合うように、ACU に基づいて同様の仮想マシンを選択することもできます。 詳細については、「[Azure コンピューティング ユニット](https://docs.microsoft.com/azure/virtual-machines/windows/acu)」をご覧ください。

![ACU ユニット ページのスクリーン ショット](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>ネットワーク スループット

スループットと IOPS の間には、次に示すような関係があります。

![スループットのスクリーン ショット](./media/oracle-design/throughput.png)

ネットワーク スループットの合計は、次に基づいて見積もられます。
- SQL*Net トラフィック
- Mbps × サーバー数 (Data Guard などの送信ストリーム)
- その他 (アプリケーションのレプリケーションなど)

![SQL*Net スループットのスクリーンショット](./media/oracle-design/sqlnet_info.png)

ご利用のネットワーク帯域幅の要件に基づいて、Basic、VpnGw、ExpressRoute といった多彩な種類からゲートウェイを選択できます。 詳細については、[VPN Gateway の価格ページ](https://azure.microsoft.com/en-us/pricing/details/vpn-gateway/?v=17.23h)をご覧ください。

推奨事項

- オンプレミスのデプロイと比べて、ネットワーク待機時間が比較的長くなります。 ネットワークのラウンド トリップ数を削減すると、パフォーマンスが大幅に向上します。
- 同じ仮想マシン上のトランザクション数が多いアプリケーション、つまり "おしゃべりな" アプリを統合することで、ラウンド トリップ数を削減できます。

### <a name="disk-types-and-configurations"></a>ディスクの種類と構成

- 既定の OS ディスク: 永続データとキャッシュに使用します。 このディスクは起動時の OS アクセス用に最適化されており、トランザクション ワークロードやデータ ウェアハウス (分析) のワークロード向けには設計されていません。

- 非管理対象ディスク: VM ディスクに対応する仮想ハード ディスク (VHD) ファイルの格納に使用するストレージ アカウントを管理します。 VHD ファイルは、Azure ストレージ アカウントにページ BLOB として格納されます。

- 管理ディスク: Azure により、VM ディスクに使用するストレージ アカウントが管理されます。 ディスクの種類 (Premium または Standard) と必要なディスクのサイズを指定します。 Azure により自動的にディスクが作成、管理されます。

- Premium Storage ディスク (実稼働のワークロードに最適): Premium Storage は、特定のサイズ シリーズの VM (DS、DSv2、GS、F) に接続できる VM ディスクをサポートしています。 Premium ディスクはさまざまなサイズで提供されており、32 GB から 4096 GB までの多彩なディスク サイズから選択できます。 各ディスク サイズは、それぞれ独自のパフォーマンス仕様があります。 アプリケーションの要件に応じて、VM には 1 つ以上のディスクを接続できます。

ポータルから新しい管理ディスクを作成すると、使用する特定の種類のディスクについて "アカウントの種類" を選択できます。 使用可能なすべてのディスクがドロップダウン ボックスに表示されるわけではないことに注意してください。 これは、Azure ストレージが仮想マシンと統合され、SKU 全体にわたって制限 (最大ドライブ数、最大 IOPS など) が適用されるためです。 このため、特定のサイズの仮想マシンを選択すると、この仮想マシンのサイズに基づいて、使用可能な Premium Storage の SKU のみが表示されます。

![管理ディスク ページのスクリーンショット](./media/oracle-design/premium_disk01.png)

詳細については、[Azure Premium Storage](https://docs.microsoft.com/azure/storage/storage-premium-storage) に関する記事をご覧ください。

仮想マシンでストレージを構成したら、データベースを作成する前にディスクのロード テストを行います。 待機時間とスループットの観点から I/O 率を知ることは、仮想マシンが待機時間の目標値を達成し、期待されるスループットをサポートしているかを把握するのに役立ちます。

アプリケーションのロード テスト用ツールは、多数提供されています (Oracle Orion、Sysbench、Fio など)。

Oracle データベースのデプロイ後にロード テストを再度実行し、通常のワークロードとピーク時のワークロードを開始すると、お使いの環境のベースラインが結果に表示されます。

ストレージ サイズではなく、IOPS の速度に基づいてストレージのサイズを決めることが重要です。 たとえば、必要な IOPS が 5000 でも、200 GB しか必要としないとします。 この場合でも、提供されるストレージ サイズは 200 GB を超えますが、P30 クラスの Premium ディスクを利用します。

IOPS の速度は AWR レポートから取得でき、REDO ログ、物理的な読み取りおよび書き込みの速度によって決まります。

![AWR レポート ページのスクリーンショット](./media/oracle-design/awr_report.png)

例: REDO のサイズは 12200000 バイト/秒で、これは 11.63 Mbps と等しく、IOPS は 12200000 ÷ 2358 = 5174 になります。

I/O 要件を明確に把握すると、これらの要件に最適なドライブの組み合わせを選択できます。

推奨事項

- DATA 表領域は、管理ストレージまたは Oracle ASM を使用して、ディスク全体に I/O ワークロードを分散させます
- 読み取りと書き込みの集中的な操作による I/O ブロック サイズの増加に合わせて、データ ディスクを追加します
- 大規模な順次処理に備えてブロック サイズを増やします
- I/O 削減のためにデータ圧縮を使用します (データとインデックスの両方)
- REDO ログ、SYSTEM 表領域、TEMP 表領域、UNDO表領域は、それぞれ別のデータ ディスクに分離します
- アプリケーション ファイルを既定の OS ディスク (/dev/sda) に保存しないことをお勧めします。 このディスクは仮想マシンの高速起動用に最適化されているため、アプリケーションに適切なパフォーマンスが提供されない可能性があります。

### <a name="disk-cache-settings"></a>ディスク キャッシュの設定

ホスト キャッシュには次の 3 つのオプションがあります。

- 読み取り専用: 今後の読み取りのためにすべての要求をキャッシュします。 すべての書き込みは、Microsoft Azure Storage Blob に直接保存されます

- 読み取り/書き込み: これは "先読み" アルゴリズムです。 今後の読み取りのために読み取りと書き込みがキャッシュされます。 ライトスルー以外の書き込みは、最初にローカル キャッシュに保存されます。 SQL Server ではライトスルーが使用されるため、書き込みは WA ストレージに保存されます。軽いワークロードでは最短のディスク待機時間が実現します

- なし (無効): キャッシュをバイパスすることができます。 すべてのデータはディスクに転送されて、Microsoft Azure Storage に保存されます。 この方法を使用すると、I/O 集約型ワークロードで最も優れた I/O 率が実現します。 また、"トランザクション費用" も考慮する必要があります。

推奨事項

スループットを最大化するには、ホスト キャッシュを [なし] で開始することをお勧めします。 Premium Storage では、[読み取り専用] または [なし] のオプションでファイル システムをマウントするときに、"バリア" を無効にする必要があることに注意してください。 UUID を使用して、/etc/fstab ファイルをディスクに更新します。

詳細については、「[Linux VM 用の Premium Storage](https://docs.microsoft.com/azure/storage/storage-premium-storage#premium-storage-for-linux-vms)」をご覧ください。

![管理ディスク ページのスクリーンショット](./media/oracle-design/premium_disk02.png)

- OS ディスクには、既定の [読み取り/書き込み] キャッシュを使用します
- SYSTEM、TEMP、UNDO には、キャッシュに [なし] を使用します
- DATA には、キャッシュに [なし] を使用しますが、お使いのデータベースが読み取り専用または読み取り集約型の場合は、[読み取り専用] キャッシュを使用します

データ ディスクの設定が保存されると、OS レベルでドライブのマウントを解除し、変更後に再マウントするまでは、ホスト (AFAIK) のキャッシュ設定を変更できません。


## <a name="security"></a>セキュリティ

Azure 環境のセットアップと構成が完了したら、 今度はネットワークをセキュリティ保護します。 以下に、推奨事項をいくつか示します。

- NSG ポリシー

NSG はサブネットまたは NIC に定義できます。  セキュリティとアプリケーション ファイアウォールなどの強制ルーティングの両方について、サブネット レベルでアクセスを簡単に制御できます。

- Jumpbox![Jumpbox トポロジ ページのスクリーンショット](./media/oracle-design/jumpbox.png)

アクセスの安全性を高めるために、管理者がアプリケーション サービスやデータベースに直接接続することは推奨されません。 管理者のコンピューターと Azure リソース間の媒介役として、Jumpbox を使用します。

管理者のコンピューターからは Jumpbox にしかアクセスできないように、IP 制限をかけます。 その後、Jumpbox からアプリケーションやデータベースにアクセスします。

- プライベート ネットワーク (サブネット)

アプリケーション サービスとデータベースは別々のサブネットに配置することをお勧めします。 これにより、NSG ポリシーで適切な制御を設定できます。


## <a name="additional-readings"></a>次の記事もご覧ください。

- [Oracle ASM の構成](configure-oracle-asm.md)
- [Oracle Data Guard の構成](configure-oracle-dataguard.md)
- [Oracle Golden Gate の構成](configure-oracle-golden-gate.md)
- [Oracle のバックアップと回復](oracle-backup-recovery.md)

## <a name="next-steps"></a>次のステップ

[チュートリアル: 高可用性 VM の作成](../../linux/create-cli-complete.md)

[VM デプロイ Azure CLI サンプルを探索する](../../linux/cli-samples.md)

