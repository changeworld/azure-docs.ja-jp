---
title: SAP HANA on Azure Large Instances の インストール | Microsoft Docs
description: SAP HANA on Azure Large Instances をインストールして構成する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 7/16/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 877ab2ab354b070fa5d56d7917379912e1b29e1e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261502"
---
# <a name="install-and-configure-sap-hana-large-instances-on-azure"></a>SAP HANA on Azure (L インスタンス) のインストールと構成

この記事では、SAP HANA on Azure Large Instances (HLI) (BareMetal インフラストラクチャとも呼ばれます) の検証、構成、およびインストールについて説明します。

## <a name="prerequisites"></a>前提条件

この記事を読む前に、次の内容について理解しておいてください。
- [HANA Large Instances のコンピューティング](hana-know-terms.md)
- [HANA Large Instances SKU](hana-available-skus.md).

こちらもご覧ください。
- [HANA L インスタンスへの Azure VM の接続](hana-connect-azure-vm-large-instances.md)
- [HANA Large Instances に仮想ネットワークを接続する](hana-connect-vnet-express-route.md)

## <a name="planning-your-installation"></a>インストールの計画

SAP HANA のインストールはユーザーが行う必要があります。 Azure 仮想ネットワークと HANA L インスタンス ユニット間の接続を確立したら、新しい SAP HANA on Azure (L インスタンス) サーバーのインストールを開始できます。 

> [!Note]
> SAP ポリシーに従い、SAP HANA のインストールは、SAP HANA インストールの認定試験である Certified SAP Technology Associate の合格者または SAP 認定システム インテグレーター (SI) によって実施される必要があります。

HANA 2.0 のインストールを計画している場合は、「[SAP support note #2235581 - SAP HANA: Supported operating systems](https://launchpad.support.sap.com/#/notes/2235581/E)」 (SAP サポート ノート #2235581 - SAP HANA: サポートされているオペレーティング システム) を参照してください。 インストールする SAP HANA リリースでオペレーティング システム (OS) がサポートされていることを確認してください。 HANA 2.0 でサポートされる OS は、HANA 1.0 でサポートされる OS より限られています。 目的の OS リリースが特定の HANA Large Instance でサポートされていることを確認します。 この[一覧](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)を使用します。HLI を選択すると、そのユニットでサポートされている OS リストの詳細が表示されます。 

HANA のインストールを開始する前に、以下について検証します。
- [HLI ユニット](#validate-the-hana-large-instance-units)
- [オペレーティング システムの構成](#operating-system)
- [ネットワーク構成](#networking)
- [ストレージの構成](#storage)


## <a name="validate-the-hana-large-instance-units"></a>HANA L インスタンス ユニットの検証

Microsoft から HANA Large Instances を受け取ったら、それらへのアクセスと接続を確立します。 その後、次の設定を検証し、必要に応じて調整します。

1. インスタンスが正しい SKU と OS で表示されているかどうかを、Azure portal で確認します。 詳細については、「[Azure portal を介した Azure HANA Large Instances の制御](./hana-li-portal.md)」を参照してください。

2. インスタンスの OS を OS プロバイダーに登録します。 この手順には、Azure の VM にデプロイされている SUSE Subscription Management Tool (SMT) のインスタンスに SUSE Linux OS を登録することが含まれます。 

    HANA Large Instance は、この SMT インスタンスに接続できます。 (詳細については、[SUSE Linux 用 SMT サーバーの設定方法](hana-setup-smt.md)に関するページを参照してください。) Red Hat OS を使用している場合は、それを接続先の Red Hat Subscription Manager に登録する必要があります。 詳細については、[SAP HANA on Azure (L インスタンス) とは何であるか](./hana-overview-architecture.md?toc=/azure/virtual-machines/linux/toc.json)に関するページの説明を参照してください。 

    この手順は、OS に修正プログラムを適用する際に必要で、ユーザー側で実行する必要があります。 SUSE の場合は、[SMT のインストールと構成](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html)に関するドキュメントを参照してください。

3. 特定の OS リリースまたはバージョンの新しいパッチと修正プログラムの有無をチェックします。 HANA Large Instance に最新のパッチが適用されていることを確認します。 最新のパッチが含まれていない場合があるため、必ず確認してください。

4. 特定の OS リリースまたはバージョンでの SAP HANA のインストールと構成に関連した SAP ノートを確認してください。 Microsoft は常に HLI を完全に構成するとは限りません。 個々のシナリオに応じて推奨事項や SAP ノートまたは構成の変更を変更すると、それが不可能になる可能性があります。  

    そのため、ご使用の Linux リリースの SAP HANA に関連する SAP ノートを必ずお読みください。 また、OS リリース/バージョンの構成を確認し、まだ適用していない構成設定を適用します。

    具体的には、以下のパラメーターを確認し、最終的に次のように調整してください。

    - net.core.rmem_max = 16777216
    - net.core.wmem_max = 16777216
    - net.core.rmem_default = 16777216
    - net.core.wmem_default = 16777216
    - net.core.optmem_max = 16777216
    - net.ipv4.tcp_rmem = 65536 16777216 16777216
    - net.ipv4.tcp_wmem = 65536 16777216 16777216

    SLES12 SP1 および Red Hat Enterprise Linux (RHEL) 7.2 以降では、これらのパラメーターを /etc/sysctl.d ディレクトリにある構成ファイルで設定する必要があります。 たとえば、91-NetApp-HANA.conf という名前の構成ファイルを作成する必要があります。 それより前にリリースされた SLES と RHEL では、これらのパラメーターを /etc/sysctl.conf で設定する必要があります。

    RHEL 6.3 以降のすべての RHEL リリースについては、次のことに留意してください。 
    - /etc/modprobe.d/sunrpc-local.conf で、sunrpc.tcp_slot_table_entries = 128 パラメーターを設定する必要があります。 このファイルが存在しない場合は、最初に次のエントリを追加してファイルを作成します。 
        - options sunrpc tcp_max_slot_table_entries=128

5. HANA Large Instance のシステム時刻をチェックします。 インスタンスは、システムのタイム ゾーンでデプロイされます。 このタイム ゾーンは、HANA L インスタンスのスタンプが位置する Azure リージョンの場所を表します。 インスタンスのシステム時刻やタイム ゾーンは、ユーザーが自由に変更できます。 

    テナントにインスタンスを追加注文する場合は、新たに提供されるインスタンスのタイム ゾーンを調整する必要があります。 Microsoft では、提供後にお客様がインスタンスに設定したシステム タイム ゾーンについては情報を持っていません。 そのため、新たにデプロイされたインスタンスのタイム ゾーンは、お客様が変更したタイム ゾーンと異なる場合があります。 必要に応じて、渡されたインスタンスのタイム ゾーンを調整する必要があります。 

6. etc/hosts を確認します。 ブレードが提供されるときには、さまざまな目的のために割り当てられた、さまざまな IP アドレスが設定されています。 ユニットが既存のテナントに追加されたときに、etc/hosts ファイルを確認することが重要です。 新しくデプロイされたシステムの etc/hosts ファイルは、以前に提供されたシステムの IP アドレスを使用して正しくメンテナンスされていない場合があります。 新しくデプロイされたインスタンスが、テナント内の以前にデプロイしたユニットの名前を解決できることを確認してください。 


## <a name="operating-system"></a>オペレーティング システム

提供される OS イメージのスワップ領域は、「[SAP support note #1999997 - FAQ: SAP HANA memory (SAP サポート ノート #1999997 - FAQ: SAP HANA のメモリ)](https://launchpad.support.sap.com/#/notes/1999997/E)」に従って、2 GB に設定されています。 異なる設定が必要な場合は、ご自身で設定する必要があります。

[SAP アプリケーション用の SUSE Linux Enterprise Server 12 SP1](https://www.suse.com/products/sles-for-sap/download/) は、SAP HANA on Azure (L インスタンス) 用にインストールされる Linux ディストリビューションです。 このディストリビューションにより、SAP on SLES を効果的に実行するための事前設定されたパラメーターなど、SAP 固有の機能が提供されます。

SAP HANA on SLES のデプロイに関連するいくつかの便利なリソースについては、以下を参照してください。
- SUSE Web サイトの[リソース ライブラリまたはホワイト ペーパー](https://www.suse.com/products/sles-for-sap/resource-library#white-papers)。
- SAP Community Network (SCN) の [SAP on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)。

これらのリソースには、高可用性の設定、SAP 操作に固有のセキュリティ強化などに関する情報が含まれています。

SAP on SUSE のその他のリソースは次のとおりです。

- [SUSE Linux での SAP HANA に関するサイト](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [SAP のベスト プラクティス: エンキュー レプリケーション – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.scribd.com/document/351887168/SLES4SAP-NetWeaver-ha-guide-EnqRepl-12-color-en-pdf)。
- [ClamSAP – SAP に対する SLES でのウイルス保護](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (SAP アプリケーション用の SLES 12 を含む)

次のドキュメントは、SAP HANA on SLES 12 の実装に適用できる SAP サポート ノートです。

- [SAP Support Note #1944799 – SAP HANA Guidelines for SLES Operating System Installation (SAP サポート ノート #1944799 – SLES オペレーティング システムのインストールに関する SAP HANA ガイドライン)](http://service.sap.com/sap/support/notes/1944799)
- [SAP Support Note #2205917 – SAP HANA DB Recommended OS Settings for SLES 12 for SAP Applications (SAP サポート ノート #2205917 – SAP HANA DB: 推奨される SLES 12 for SAP Applications 向け OS 設定)](https://launchpad.support.sap.com/#/notes/2205917/E)
- [SAP Support Note #1984787 – SUSE Linux Enterprise Server 12:  Installation Notes (SAP サポート ノート #1984787 – SUSE Linux Enterprise Server 12: インストールに関する注意事項)](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP support note #171356 – SAP software on Linux:General Information (SAP サポート ノート #171356 – SAP Software on Linux: 一般情報)](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP Support Note #1391070 – Linux UUID Solutions (SAP サポート ノート #1391070 – Linux UUID ソリューション)](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) は、HANA L インスタンスで SAP HANA を実行するためのもう 1 つのプランです。 RHEL 7.2 および 7.3 のリリースが利用でき、サポートされます。 SAP on Red Hat の詳細については、[SAP HANA on Red Hat Linux サイト](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat)を参照してください。

次のドキュメントは、SAP HANA on Red Hat の実装に適用できる SAP サポート ノートです。

- [SAP Support Note #2009879 – SAP HANA Guidelines for Red Hat Enterprise Linux (RHEL) Operating System (SAP サポート ノート #2009879 – Red Hat Enterprise Linux (RHEL) オペレーティング システムに関する SAP HANA ガイドライン)](https://launchpad.support.sap.com/#/notes/2009879/E)
- [SAP support note #2292690 - SAP HANA DB:Recommended OS settings for RHEL 7 (SAP HANA DB: RHEL 7 に推奨される OS 設定)](https://launchpad.support.sap.com/#/notes/2292690)
- [SAP Support Note #1391070 – Linux UUID Solutions (SAP サポート ノート #1391070 – Linux UUID ソリューション)](https://launchpad.support.sap.com/#/notes/1391070)
- [SAP support note #2228351 - Linux:SAP HANA Database SPS 11 revision 110 (or higher) on RHEL 6 or SLES 11 (SAP サポート ノート #2228351 - Linux: RHEL 6 または SLES 11 の SAP HANA データベース SPS 11 リビジョン 110 以上)](https://launchpad.support.sap.com/#/notes/2228351)
- [SAP support note #2397039 - FAQ: SAP on RHEL (SAP サポート ノート #2397039 - FAQ: SAP on RHEL)](https://launchpad.support.sap.com/#/notes/2397039)
- [SAP support note #2002167 - Red Hat Enterprise Linux 7.x: Installation and Upgrade (SAP サポート ノート #2002167 - Red Hat Enterprise Linux 7.x: インストールとアップグレード)](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>時刻同期

SAP NetWeaver アーキテクチャを基盤とする SAP アプリケーションは、SAP システムのコンポーネントの時刻のずれの影響を受けやすくなっています。 おそらく、ZDATE\_LARGE\_TIME\_DIFF というエラー タイトルが付いた SAP ABAP のショート ダンプを見たことがあるでしょう。 これらのショート ダンプが表示されるのは、異なるサーバーまたは仮想マシン (VM) のシステム時刻が、あまりにも違っているためです。

SAP HANA on Azure Large Instances の場合、Azure で行われる時刻同期は、Large Instance スタンプ内のコンピューティング ユニットには適用されません。 Azure ではシステム時刻が正しく同期されるため、これはネイティブの Azure VM で SAP アプリケーションを実行する場合にも適用されません。 

その結果、別のタイム サーバーを設定する必要があります。 このサーバーは、Azure VM 上で実行される SAP アプリケーション サーバーによって使用されます。 また、HANA Large Instances で実行されている SAP HANA データベース インスタンスでも使用されます。 Large Instance スタンプ内のストレージ インフラストラクチャの時刻は Network Time Protocol (NTP) サーバーと同期されます。


## <a name="networking"></a>ネットワーク
Azure 仮想ネットワークを設計し、それらの仮想ネットワークを HANA Large Instances に接続する場合は、必ず次の推奨事項に従ってください。

- [SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ](./hana-overview-architecture.md)
- [SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md)

単一ユニットのネットワーキングに関して、触れておくべき注意点がいくつかあります。 どの HANA Large Instance ユニットにも 2個または 3 個の IP アドレスが備わっており、それらは 2 個または 3 個のネットワーク インターフェイス コント ローラー (NIC) ポートに割り当てられています。 HANA スケールアウト構成と HANA システム レプリケーション シナリオでは、3 つの IP アドレスが使用されます。 ユニットの NIC に割り当てられている IP アドレスの 1 つは、[SAP HANA on Azure Large Instances の概要とアーキテクチャ](./hana-overview-architecture.md)に関するページで説明されているサーバー IP プールから割り当てられたものです。

アーキテクチャのイーサネットの詳細については、[HLI でサポートされるシナリオ](hana-supported-scenario.md)についてのページを参照してください。

## <a name="storage"></a>記憶域

SAP HANA Large Instances のストレージ レイアウトは、SAP が推奨するガイドラインに従って、SAP HANA on Azure サービス管理によって構成されます。 これらのガイドラインについては、「[SAP HANA Storage Requirements (SAP HANA ストレージ要件)](https://archive.sap.com/kmuuid2/70c8e423-c8aa-3210-3fae-e043f5c1ca92/SAP%20HANA%20TDI%20-%20Storage%20Requirements.pdf)」に記載されています。 

各種 HANA L インスタンス SKU で提供されるさまざまなボリュームのおおよそのサイズについては、「[SAP HANA on Azure (L インスタンス) の概要とアーキテクチャ](hana-overview-architecture.md)」をご覧ください。

次の表に、ストレージ ボリュームの名前付け規則を示します。

| ストレージの使用法 | マウント名 | ボリューム名 | 
| --- | --- | ---|
| HANA データ | /hana/data/SID/mnt0000\<m> | Storage IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA ログ | /hana/log/SID/mnt0000\<m> | Storage IP:/hana_log_SID_mnt00001_tenant_vol |
| HANA ログ バックアップ | /hana/log/backups | Storage IP:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA 共有 | /hana/shared/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | Storage IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*SID* は、HANA インスタンスのシステム ID です。 

*Tenant* は、テナント配置時の操作の内部列挙です。

HANA usr/sap は、同じボリュームを共有します。 マウント ポイントの命名には、HANA インスタンスのシステム ID とマウント番号が含まれます。 スケールアップ配置では、mnt00001 のように、マウントは 1 つしか存在しません。 スケールアウト配置では、使用している worker およびプライマリ のノードと同じ数のマウントが表示されます。 

スケールアウト環境では、データ、ログ、ログ バックアップの各ボリュームが共有され、スケールアウト構成の各ノードに接続されます。 複数の SAP インスタンスである構成の場合、異なるボリューム セットが作成され、HANA Large Instance に接続されます。 実際のシナリオのストレージ レイアウトの詳細については、[HLI でサポートされるシナリオ](hana-supported-scenario.md)に関する記事を参照してください。

HANA Large Instances には、HANA/data 用の十分なディスク ボリュームとボリューム HANA/log/backup が付属しています。 ストレージ スナップショットでも同じディスク ボリュームが使用されるため、HANA/data を非常に大きくしました。 実行するストレージ スナップショットが多いほど、割り当てられたストレージ ボリューム内のスナップショットによって消費される領域が多くなります。 

HANA/log/backup ボリュームが、データベース バックアップ用のボリュームになることは想定されていません。 そのサイズは、HANA トランザクション ログ バックアップ用のバックアップ ボリュームとして使用される設定となっています。 詳細については、「[Azure での SAP HANA (L インスタンス) の高可用性とディザスター リカバリー](hana-overview-high-availability-disaster-recovery.md)」をご覧ください。 

ストレージを増やすには、容量を 1 TB 単位で追加購入します。 この追加ストレージは、新しいボリュームとして HANA Large Instance に追加できます。

SAP HANA on Azure サービス管理によるオンボード時に、sidadm ユーザーのユーザー ID (UID) と sapsys グループのグループ ID (GID) を指定します (例: 1000,500)。 SAP HANA システムのインストール時には、これらと同じ値を使用する必要があります。 1 つのユニットに複数の HANA インスタンスをデプロイするために、複数のボリューム セット (インスタンスごとに 1 セット) を取得します。 そのため、デプロイ時に以下を定義する必要があります。

- 各 HANA インスタンスの SID (sidadm はそれからから派生されます)。
- 各 HANA インスタンスのメモリ サイズ。 インスタンスごとのメモリ サイズによって、個々のボリューム セットのボリュームのサイズが定義されます。

ストレージ プロバイダーの推奨事項に基づいて、マウントされるすべてのボリューム (ブート LUN を除く) に対して次のマウント オプションが構成されます。

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

次のスクリーンショットに示すように、これらのマウント ポイントは /etc/fstab に構成されます。

![HANA Large Instance ユニットにマウントされたボリュームの fstab を示すスクリーンショット。](./media/hana-installation/image1_fstab.PNG)

S72m HANA Large Instance に対する df -h コマンドの出力は次のようになります。

![HANA Large Instance に対するコマンドの出力を示すスクリーンショット。](./media/hana-installation/image2_df_output.PNG)


L インスタンス スタンプ内のストレージ コントローラーとノードは NTP サーバーに同期されます。 NTP サーバーに対して SAP HANA on Azure Large Instances と Azure VM を同期することが重要です。 これにより、Azure または Large Instance スタンプのインフラストラクチャとコンピューティング ユニットの間の大幅な時間の誤差がなくなります。

内部で使用されているストレージに対して SAP HANA を最適化するため、以下の SAP HANA 構成パラメーターを設定する必要があります。

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
SAP HANA 1.0 バージョンから SPS12 までは、これらのパラメーターが SAP HANA データベースのインストール中に設定されます (「[SAP Note #2267798 - Configuration of the SAP HANA Database (SAP ノート #2267798 - SAP HANA データベースの構成)](https://launchpad.support.sap.com/#/notes/2267798)」を参照)。

SAP HANA データベースのインストール後に、hdbparam フレームワークを使ってパラメーターを構成することもできます。 

HANA L インスタンスで使用されるストレージには、ファイル サイズの制限があります。 ファイルごとの[サイズの制限は 16 TB です](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html)。 EXT3 ファイル システムのファイル サイズ制限とは異なり、HANA は、HANA L インスタンスのストレージによって強制されるストレージ制限を暗黙的に認識しません。 そのため HANA では、ファイルサイズが 16 TB の上限に達したとき、自動的に新しいデータ ファイルを作成することはありません。 HANA は 16 TB を超えるサイズへファイルを拡大しようとするため、HANA でエラーが報告され、最終的にはインデックス サーバーがクラッシュします。

> [!IMPORTANT]
> HANA Large Instance ストレージの 16 TB のファイル サイズ制限を超えて、HANA がデータ ファイルの拡大を試行するのを防ぐには、SAP HANA の global.ini 構成ファイルで以下のパラメーターを設定します。
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - SAP Note [#2400005](https://launchpad.support.sap.com/#/notes/2400005) も参照してください
> - SAP Note [#2631285](https://launchpad.support.sap.com/#/notes/2631285) に注意してください


SAP HANA 2.0 では、hdbparam フレームワークが非推奨となりました。 そのため、これらのパラメーターは SQL コマンドを使用して設定する必要があります。 詳しくは、「[SAP Note #2399079: Elimination of hdbparam in HANA 2 (SAP ノート #2399079: HANA 2 で hdbparam を廃止)](https://launchpad.support.sap.com/#/notes/2399079)」をご覧ください。

アーキテクチャのストレージ レイアウトの詳細については、[HLI でサポートされるシナリオ](hana-supported-scenario.md)に関する記事を参照してください。


## <a name="next-steps"></a>次のステップ

SAP HANA on Azure (Large Instances) のインストール手順について説明します。

> [!div class="nextstepaction"]
> [SAP HANA on Azure (Large Instances) に HANA をインストールする](hana-example-installation.md)
