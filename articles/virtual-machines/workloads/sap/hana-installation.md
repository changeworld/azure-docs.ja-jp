---
title: "SAP HANA on Azure (L インスタンス) への SAP HANA のインストール | Microsoft Docs"
description: "SAP HANA on Azure (L インスタンス) への SAP HANA のインストール方法を説明します。"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: e66d1e245d74c11e8bf36e307009910126551b0b
ms.contentlocale: ja-jp
ms.lasthandoff: 07/06/2017


---
<a id="how-to-install-and-configure-sap-hana-large-instances-on-azure" class="xliff"></a>

# SAP HANA on Azure (L インスタンス) のインストールと構成の方法

SAP HANA はお客様がご自分でインストールする必要があります。新しい SAP HANA on Azure (L インスタンス) サーバーのハンドオフ後、Azure VNet と HANA L インスタンス ユニットの間の接続が確立されたらインストールを開始できます。 ただし SAP HANA のインストールは、SAP ポリシーに従い、SAP HANA のインストールを実施する資格のある担当者 (SAP HANA インストールの認定試験である Certified SAP Technology Associate の合格者) または SAP 認定システム インテグレーター (SI) が実施する必要があります。

インストールする SAP HANA リリースで OS がサポートされていることを確認するために、「[SAP Support Note #2235581 - SAP HANA: Supported Operating Systems (SAP サポート ノート #2235581 - SAP HANA: サポートされているオペレーティング システム)](https://launchpad.support.sap.com/#/notes/2235581/E)」を再度確認してください (特に HANA 2.0 をインストールする場合)。 HANA 2.0 でサポートされている OS は、HANA 1.0 でサポートされている OS よりも限られています。 

<a id="first-steps-after-receiving-the-hana-large-instance-units" class="xliff"></a>

## HANA L インスタンス ユニットを入手した後で最初に行うこと

**手順 1**: HANA L インスタンスを入手し、インスタンスへのアクセスと接続を確立したら、そのインスタンスの OS をご利用の OS プロバイダーに登録します。 たとえば、SUSE SMT のインスタンス (HANA L インスタンス ユニットが接続可能な Azure の VM で独自にデプロイしている必要があります) に SUSE Linux OS を登録します。 または RedHat OS を、接続先となる Red Hat Subscription Manager に登録する必要があります。 [こちらのドキュメント](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)の備考も参照してください。 今後 OS にパッチを適用できるようにするうえでも、この手順が必要となります。 これはお客様が各自で行う作業です。 SUSE については、SMT のインストールと構成に関するドキュメントを[ここから](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html)参照してください。

**手順 2**: 特定の OS リリース/バージョンに関する新しいパッチと修正プログラムの有無をチェックします。 HANA L インスタンスのパッチ レベルが最新の状態になっているかどうかを確認してください。 OS のパッチ/リリースのタイミングと、Microsoft でデプロイ可能なイメージへの変更によっては、最新のパッチが含まれていない場合があります。 そのため、HANA L インスタンス ユニットの引き渡しが済んで OS のインストールを Linux ディストリビューターに登録したら、セキュリティや機能、可用性、パフォーマンスに関連して最新のパッチが特定の Linux ベンダーからリリースされているかどうか、また適用する必要があるかどうかを確認する作業が欠かせません。

**手順 3**: 特定の OS リリース/バージョンへのSAP HANA のインストールと構成に関連した SAP ノートを確認します。 推奨事項は絶えず変化します。また SAP ノートや構成はインストールのシナリオによって異なるため、Microsoft が HANA L インスタンス ユニットを完璧に構成できるとは限りません。 そのためお客様がご自身で SAP ノート (最小限の内容を以下に記載) を読み、OS リリース/バージョンの構成をチェックし、まだ必要な構成設定がなされていなければ適用する必要があります。

特に、以下のパラメーターをチェックしたうえで、次のように調整してください。

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

SLES12 SP1 および RHEL 7.2 以降では、これらのパラメーターを /etc/sysctl.d ディレクトリにある構成ファイルで設定する必要があります。 たとえば、91-NetApp-HANA.conf という名前の構成ファイルを作成する必要があります。 それより前にリリースされた SLES と RHEL では、これらのパラメーターを /etc/sysctl.conf で設定する必要があります。

すべてのバージョンの RHEL と SLES12 以降では、次のようにパラメーターを設定してください。 
- sunrpc.tcp_slot_table_entries = 128

このパラメーターは /etc/modprobe.d/sunrpc-local.conf で設定する必要があります。 このファイルが存在しない場合は、最初に次のエントリを追加して作成する必要があります。 
- options sunrpc tcp_max_slot_table_entries=128

**手順 4**: HANA L インスタンス ユニットのシステム時刻をチェックします。 このインスタンスは、HANA L インスタンス スタンプが置かれている Azure リージョンの場所を表すシステム タイム ゾーンでデプロイされます。 自分が所有するインスタンスのタイム ゾーンとシステム時刻は、自由に変更することができます。 その場合、テナントにインスタンスを追加注文したときは、新たに納入されたインスタンスのタイム ゾーンをご自身で調整しなければならないことに注意してください。 Microsoft のオペレーションでは、インスタンスの納入後にお客様が設定したシステム タイム ゾーンは把握していません。 したがって、新たにデプロイされたインスタンスのタイム ゾーンが、お客様による変更後のタイムゾーンと異なる場合があります。 納入されたインスタンスのタイム ゾーンは、お客様ご自身でチェックし、必要に応じて調整してください。 

**手順 5**: etc/hosts をチェックします。 納入されたブレードには、さまざまな目的で異なる IP アドレスが割り当てられています (次のセクションを参照)。 etc/hosts を確認してください。 既存のテナントにユニットが追加されるケースでは、新しくデプロイされたシステムの etc/hosts に、以前に納入されたシステムの IP アドレスが正しく記述されているとは限りません。 新しくデプロイされたインスタンスが、テナントにデプロイ済みのユニットと連動し、名前を解決できるように、設定が正しいことをお客様ご自身でチェックしてください。 

<a id="networking" class="xliff"></a>

## ネットワーク
以降の説明は、Azure VNet が次の文書の推奨事項に従って設計され、HANA L インスタンスに接続されていることを前提としています。

- [SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

個々の装置のネットワークに関して、いくつかの注意点があります。 すべての HANA L インスタンス ユニットに 2 ～ 3 個の IP アドレスが備わっており、それがそのユニットの 2 ～ 3 個の NIC ポートに割り当てられています。 HANA スケールアウト構成と HANA システム レプリケーション シナリオでは、3 つの IP アドレスが使用されます。 ユニットの NIC に割り当てられている IP アドレスの 1 つは、「[SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)」で説明されているサーバー IP プールから割り当てられたものです。

2 つの IP アドレスが割り当てられているユニットの区分は次にようになっています。

- eth0.xx には、お客様が Microsoft に提出したサーバー IP プールのアドレス範囲から IP アドレスが割り当てられます。 OS の /etc/hosts には、この IP アドレスが記述されます。
- eth1.xx には、NFS との通信用の IP アドレスが割り当てられます。 そのため、テナント内のインスタンスどうしがトラフィックをやり取りするために、これらのアドレスが etc/hosts に記述されている必要は**ありません**。

HANA システム レプリケーションまたは HANA スケールアウトのデプロイの場合、2 つの IP アドレスが割り当てられたブレード構成は適しません。 IP アドレスを 2 つだけ割り当てたうえで、そのような構成をデプロイする必要がある場合は、Microsoft Service Management に問い合わせて、第 3 の VLAN から 3 つ目の IP アドレスを割り当ててください。 3 つの NIC ポートに 3 つの IP アドレスが割り当てられている HANA L インスタンス ユニットでは、次の事柄が当てはまります。

- eth0.xx には、お客様が Microsoft に提出したサーバー IP プールのアドレス範囲から IP アドレスが割り当てられます。 したがって OS の /etc/hosts には、この IP アドレスが記述されません。
- eth1.xx には、NFS ストレージとの通信用の IP アドレスが割り当てられます。 そのため、このタイプのアドレスは etc/hosts には記述されません。
- eth2.xx だけが etc/hosts に記述され、さまざまなインスタンス間の通信に使用されます。 これは、スケールアウト HANA 構成において、HANA によるノード間の構成用に保持すべき IP アドレスでもあります。



<a id="storage" class="xliff"></a>

## Storage

SAP HANA on Azure (L インスタンス) のストレージ レイアウトは、SAP により推奨されたベスト プラクティスを使用し、SAP HANA on Azure サービス管理によって構成されています。詳細はホワイト ペーパー『[SAP HANA Storage Requirements (SAP HANA ストレージ要件)](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)』を参照してください。 このホワイト ペーパーと HANA L インスタンス ユニットを見ると、HANA/data に対してかなり大きなディスク ボリュームが備わっていること、また HANA/log/backup というボリュームがあることがわかります。 HANA/data にそこまでのサイズが確保されているのは、Microsoft がお客様に提供しているストレージ スナップショットに、ほぼ同じディスク ボリュームが使われているためです。 つまりストレージ スナップショットは、実行すればするほど多くの領域が必要となります。 HANA/log/backup ボリュームは、データベースのバックアップを格納するためのボリュームとしてではなく、HANA トランザクション ログ用のバックアップ ボリュームとしての利用が想定されています。 将来のバージョンのストレージ スナップショット セルフサービスでは、このボリュームに格納するスナップショットの頻度を増やすと共に、ディザスター リカバリー サイトへのレプリケーション頻度も増やす予定です (HANA L インスタンス インフラストラクチャに用意されているディザスター リカバリー機能をお客様が希望する場合)。詳しくは、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。 

お客様は、提供されているストレージに加え、追加のストレージ容量を 1 TB 単位で購入することができます。 この追加のストレージは、新しいボリュームとして HANA L インスタンスに追加できます。

Microsoft Service Management とのオンボーディング中、お客様は、<SID>ADM ユーザーと sapsys グループに対するユーザー ID (UID) とグループ ID (GID) を指定します (例: 1000、500)。これと同じ値を SAP HANA システムのインストール時に使用することになります。

L インスタンス スタンプ内のストレージ コントローラーとノードは NTP サーバーに同期されます。 SAP HANA on Azure (L インスタンス) のユニットと Azure VM を NTP サーバーに対して同期する際は、Azure または L インスタンス スタンプ内のコンピューティング ユニットとインフラストラクチャの間で大きな時間のずれが発生しないようにする必要があります。

内部で使用されているストレージに SAP HANA を合わせるために、次の SAP HANA 構成パラメーターを設定する必要があります。

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
SAP HANA 1.0 バージョンから SPS12 までは、これらのパラメーターが SAP HANA データベースのインストール中に設定されます (「[SAP Note #2267798 - Configuration of the SAP HANA Database (SAP ノート #2267798 - SAP HANA データベースの構成)](https://launchpad.support.sap.com/#/notes/2267798)」を参照)。

SAP HANA データベースのインストール後に、hdbparam フレームワークを使ってパラメーターを構成することもできます。 

SAP HANA 2.0 では、hdbparam フレームワークが廃止されています。 パラメーターの設定には SQL コマンドを使ってください。 詳しくは、「[SAP Note #2399079: Elimination of hdbparam in HANA 2 (SAP ノート #2399079: HANA 2 で hdbparam を廃止)](https://launchpad.support.sap.com/#/notes/2399079)」をご覧ください。


<a id="operating-system" class="xliff"></a>

## オペレーティング システム

「[SAP Support Note #1999997 - FAQ: SAP HANA Memory (SAP サポート ノート #1999997 - FAQ: SAP HANA のメモリ)](https://launchpad.support.sap.com/#/notes/1999997/E)」に従って、提供される OS イメージのスワップ領域は 2 GB に設定されています。 必要な設定が異なる場合は、お客様が設定する必要があります。

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/hana) は、SAP HANA on Azure (L インスタンス) 用にインストールする Linux ディストリビューションです。 このディストリビューションでは、SAP 特有の機能を &quot;すぐに使う&quot; ことができます (SAP on SLES を効果的に実行するための事前設定されたパラメーターを含む)。

SUSE の Web サイトの[「Resource Library (リソース ライブラリ)」内の「White Papers (ホワイト ペーパー)」](https://www.suse.com/products/sles-for-sap/resource-library#white-papers)と、SAP Community Network (SCN) の「[SAP on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)」を参照して、SAP HANA on SLES のデプロイに関連するいくつかの便利なリソース (High Availability の設定や、SAP 運用向けのセキュリティ強化など) を確認できます。

その他の便利な SAP on SUSE 関連のリンクは次のとおりです。

- [SAP HANA on SUSE Linux のサイト](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Best Practice for SAP: Enqueue Replication – SAP NetWeaver on SUSE Linux Enterprise 12 (SAP のベスト プラクティス: レプリケーションのエンキュー – SAP NetWeaver on SUSE Linux Enterprise 12)](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)。
- [ClamSAP – SLES Virus Protection for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (SLES 12 for SAP Applications を含む)。

SAP HANA on SLES 12 の実装に適用できる SAP サポート ノートは次のとおりです。

- [SAP Support Note #1944799 – SAP HANA Guidelines for SLES Operating System Installation (SAP サポート ノート #1944799 – SLES オペレーティング システムのインストールに関する SAP HANA ガイドライン)](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)。
- [SAP Support Note #2205917 – SAP HANA DB Recommended OS Settings for SLES 12 for SAP Applications (SAP サポート ノート #2205917 – SAP HANA DB: 推奨される SLES 12 for SAP Applications 向け OS 設定)](https://launchpad.support.sap.com/#/notes/2205917/E)。
- [SAP Support Note #1984787 – SUSE Linux Enterprise Server 12:  Installation Notes (SAP サポート ノート #1984787 – SUSE Linux Enterprise Server 12: インストールに関する注意事項)](https://launchpad.support.sap.com/#/notes/1984787)。
- [SAP Support Note #171356 – SAP Software on Linux:  General Information (SAP サポート ノート #171356 – SAP Software on Linux: 一般情報)](https://launchpad.support.sap.com/#/notes/1984787)。
- [SAP Support Note #1391070 – Linux UUID Solutions (SAP サポート ノート #1391070 – Linux UUID ソリューション)](https://launchpad.support.sap.com/#/notes/1391070)。

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) は、HANA L インスタンスで SAP HANA を実行するためのもう 1 つのプランです。 RHEL 6.7 および 7.2 のリリースが利用できます。 

その他の便利な SAP on Red Hat 関連のリンクは次のとおりです。
- [SAP HANA on Red Hat Linux のサイト](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat)。

SAP HANA on Red Hat の実装に適用できる SAP サポート ノートは次のとおりです。

- [SAP Support Note #2009879 – SAP HANA Guidelines for Red Hat Enterprise Linux (RHEL) Operating System (SAP サポート ノート #2009879 – Red Hat Enterprise Linux (RHEL) オペレーティング システムに関する SAP HANA ガイドライン)](https://launchpad.support.sap.com/#/notes/2009879/E)。
- [SAP Support Note #2292690 - SAP HANA DB: Recommended OS settings for RHEL 7 (SAP サポート ノート #2292690 - SAP HANA DB: RHEL 7 に推奨される OS 設定)](https://launchpad.support.sap.com/#/notes/2292690)。
- [SAP Support Note #2247020 - SAP HANA DB: Recommended OS settings for RHEL 6.7 (SAP サポート ノート #2247020 - SAP HANA DB: RHEL 6.7 に推奨される OS 設定)](https://launchpad.support.sap.com/#/notes/2247020)。
- [SAP Support Note #1391070 – Linux UUID Solutions (SAP サポート ノート #1391070 – Linux UUID ソリューション)](https://launchpad.support.sap.com/#/notes/1391070)。
- [SAP Support Note #2228351 - Linux: SAP HANA Database SPS 11 revision 110 (or higher) on RHEL 6 or SLES 11 (SAP サポート ノート #2228351 - Linux: RHEL 6 または SLES 11 の SAP HANA データベース SPS 11 リビジョン 110 以上)](https://launchpad.support.sap.com/#/notes/2228351)。
- [SAP Support Note #2397039 - FAQ: SAP on RHEL (SAP サポート ノート #2397039 - FAQ: SAP on RHEL)](https://launchpad.support.sap.com/#/notes/2397039)。
- [SAP Support Note #1496410 - Red Hat Enterprise Linux 6.x: Installation and Upgrade (SAP サポート ノート #1496410 - Red Hat Enterprise Linux 6.x: インストールおよびアップグレード)](https://launchpad.support.sap.com/#/notes/1496410)。
- [SAP Support Note #2002167 - Red Hat Enterprise Linux 7.x: Installation and Upgrade (SAP サポート ノート #2002167 - Red Hat Enterprise Linux 7.x: インストールおよびアップグレード)](https://launchpad.support.sap.com/#/notes/2002167)。

<a id="time-synchronization" class="xliff"></a>

## 時刻同期

SAP は、SAP システムを構成するさまざまなコンポーネントの時間のずれに非常に敏感です。 SAP (Basis) を長く使ってきた経験がある場合、ZDATE\_LARGE\_TIME\_DIFF というエラー タイトルで発生する SAP ABAP のショート ダンプは見慣れたものかもしれません。というのも、これらのショート ダンプは、異なるサーバーまたは VM のシステム時刻のずれが大きくなりすぎたときに発生するからです。

SAP HANA on Azure (L インスタンス) の場合、Azure で行われる時刻同期は、L インスタンス スタンプ内のコンピューティング ユニットには適用されません。 Azure (VM 上) でネイティブに SAP アプリケーションを実行する場合は、これは該当しません。Azure ではシステム時刻が適切に同期されるためです。 結果として、別のタイム サーバーを、Azure VM 上で実行される SAP アプリケーション サーバーと、HANA L インスタンス上で実行される SAP HANA データベース インスタンスで使用できるように設定する必要があります。 L インスタンス スタンプ内のストレージ インフラストラクチャの時刻は NTP サーバーと同期されます。





