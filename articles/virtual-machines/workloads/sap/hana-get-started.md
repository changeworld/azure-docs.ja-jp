---
title: "クイックスタート ガイド: Azure VM への単一インスタンスの SAP HANA の手動インストール | Microsoft Docs"
description: "Azure VM に単一インスタンスの SAP HANA を手動でインストールするためのクイックスタート ガイド"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.translationtype: Human Translation
ms.sourcegitcommit: 9568210d4df6cfcf5b89ba8154a11ad9322fa9cc
ms.openlocfilehash: e95d3a57594d55b7d7176d873cde8abfa6e12ecd
ms.contentlocale: ja-jp
ms.lasthandoff: 05/15/2017


---
# <a name="quick-start-guide-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>クイックスタート ガイド: Azure VM への単一インスタンスの SAP HANA の手動インストール
## <a name="introduction"></a>はじめに
このクイックスタート ガイドは、SAP NetWeaver 7.5 と SAP HANA 1.0 SP12 を手動でインストールする場合に、Azure Virtual Machines (VM) に単一インスタンスの SAP HANA プロトタイプまたはデモ システムを設定するのに役立ちます。このガイドは SAP のマニュアルに代わるものではなく、Azure へのデプロイを中心に説明します。

>[!Note]
>このガイドは、Azure VM への SAP HANA のデプロイに関するものであるという点にご注意ください。 HANA Large インスタンスへの SAP HANA のデプロイに関するすべての考慮事項は、別のドキュメント (https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) に記載されています。
 

このガイドは、次のような Azure IaaS の基本事項について理解していることを前提としています。
 * Azure Portal または PowerShell を使って仮想マシンまたは仮想ネットワークをデプロイする方法。
 * JSON (JavaScript Object Notification) テンプレートを使うためのオプションなど、Azure クロスプラットフォーム コマンドライン ツール (CLI)。

このガイドでは、SAP HANA と SAP NetWeaver、またそれらをオンプレミスでインストールする方法について理解していることも想定しています。

Azure 上に SAP HANA および SAP アプリケーション インスタンスをインストールして操作する場合、以下のような知識が必要です。

VNet の計画と Azure Storage の使用を含む Azure への SAP デプロイの計画については、「[Azure Virtual Machines (VM) への SAP NetWeaver の導入 – 計画/導入ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)」を参照してください。

デプロイの原則と Azure で VM をデプロイする方法については、「[SAP のための Azure Virtual Machines のデプロイ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)」を参照してください。

Azure 上の SAP NetWeaver ASCS、SCS、および ERS の高可用性の側面については、「[Azure VM での SAP NetWeaver の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide)」を参照してください。

「[SAP NetWeaver マルチ SID 構成の作成](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid)」では、Azure での ASCS/SCS のマルチ SID インストールを活用する効率を向上させる方法について詳しく説明します。

Azure で Linux ベースの VM に基づいて SAP NetWeaver を実行する場合の原則については、「[Microsoft Azure SUSE Linux VM での SAP NetWeaver の実行](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)」を参照してください。 このガイドは、ぜひお読みください。このガイドには、Azure VM で Linux を使用する際のいくつかの特定の設定や、Azure ストレージ ディスクを Linux VM に正しく接続する方法が詳しく解説されています。

Azure VM はこれまでのところ、SAP HANA スケールアップ構成でのみ SAP の認定を受けています。 SAP HANA ワークロードを含むスケールアウト構成はまだサポートされていません。 スケールアップ構成の場合の SAP HANA の高可用性については、「[Azure Virtual Machines (VM) 上の SAP HANA の高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)」を参照してください。


Azure VM 上の SAP HANA データベースのバックアップに関する推奨事項と可能性は、これらのガイドに記載されています。

* [Azure Virtual Machines 上の SAP HANA のバックアップ ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [ファイル レベルの SAP HANA Azure バックアップ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [ストレージ スナップショットに基づいた SAP HANA のバックアップ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

SAP Cloud アプライアンス ライブラリを使用して S/4HANA または BW/4HANA をデプロイする方法については、「[Microsoft Azure に SAP S/4HANA または BW/4HANA をデプロイする](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)」を参照してください。

SAP HANA でサポートされるオペレーティング システムは、「[SAP サポート ノート #2235581 - SAP HANA: サポートされているオペレーティング システム](https://launchpad.support.sap.com/#/notes/2235581/E)」に記載されています。 Azure VM では、これらのオペレーティング システムのサブセットのみがサポートされます。 具体的には、Azure への SAP HANA のデプロイについては、以下の OS がサポートされています。 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

SAP HANA およびさまざまな Linux オペレーティング システムに関する SAP の追加ドキュメントを次に示します。

* [SAP サポート ノート #171356 – SAP Software on Linux: 一般情報](https://launchpad.support.sap.com/#/notes/1984787)
* [SAP サポート ノート #1944799 – SLES オペレーティング システムのインストールに関する SAP HANA ガイドライン](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
* [SAP サポート ノート #2205917 – SAP HANA DB: 推奨される SLES 12 for SAP Applications 向け OS 設定](https://launchpad.support.sap.com/#/notes/2205917/E)
* [SAP サポート ノート #1984787 – SUSE Linux Enterprise Server 12: インストールに関する注意事項](https://launchpad.support.sap.com/#/notes/1984787)
* [SAP サポート ノート #1391070 – Linux UUID ソリューション](https://launchpad.support.sap.com/#/notes/1391070)
* [SAP サポート ノート #2009879 – SAP HANA Guidelines for Red Hat Enterprise Linux (RHEL) Operating System (Red Hat Enterprise Linux (RHEL) オペレーティング システムに関する SAP HANA ガイドライン)](https://launchpad.support.sap.com/#/notes/2009879)
* [2292690 - SAP HANA DB: Recommended OS settings for RHEL 7 (SAP HANA DB: RHEL 7 に推奨される OS 設定)](https://launchpad.support.sap.com/#/notes/2292690/E)

さらに、Azure での SAP の監視に関連する次の 2 つの SAP Note も考慮してください。

* Azure 上の Linux VM を使用した SAP の "拡張された監視機能" についての SAP Note: [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)。
* Linux での SAPOSCOL に関する情報が記載された SAP Note: [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)。
* Microsoft Azure 上の SAP 向けの主要な監視メトリック: [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)。


Azure VM の種類と SAP HANA に関連して SAP でサポートされるワークロード シナリオは、「[SAP certified IaaS Platforms (SAP の認定 IaaS プラットフォーム)](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)」に記載されています。 SAP NetWeaver または S/4HANA アプリケーション層で使用でき、SAP によって認定されている Azure VM の種類は、SAP のドキュメント「[SAP Note 1928533 - SAP Applications on Azure: Supported Products and Azure VM types (Azure 上の SAP アプリケーション: サポートされる製品と Azure VM の種類)](https://launchpad.support.sap.com/#/notes/1928533/E)」に記載されています。

>[!Note]
>SAP-Linux-Azure は、Azure Resource Manager (ARM) でのみサポートされ、クラシック デプロイメント モデル (ASM) ではサポートされません。 


このガイドでは、Azure VM に SAP HANA を手動でインストールする方法を 2 とおり説明します。

* NetWeaver 分散インストールの一環として、"データベース インスタンスのインストール" の手順で SAP Software Provisioning Manager (SWPM) を使う。
* HANA ライフサイクル管理ツール hdblcm を使い、その後 NetWeaver をインストールする。

[このページ](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/)に説明されているように、SWPM を使ってすべてのコンポーネント (SAP HANA、SAP アプリケーション サーバー、ASCS インスタンス) を単一の VM にインストールすることもできます。 このガイドではこのオプションについて説明しませんが、考慮する必要のある事項は同じです。

インストールを開始する前に、SAP HANA のインストールに関する 2 つのチェックリストの直後にある「SAP HANA を手動でインストールするための Azure VM の準備」を必ずお読みください。 そうすると、既定の Azure VM 構成のみを使う場合に発生する可能性のあるいくつかの基本的なミスを防ぐのに役立ちます。

## <a name="checklist-for-sap-hana-installation-using-sap-swpm"></a>SAP SWPM を使った SAP HANA のインストールのチェックリスト
ここでは、SAP SWPM を使って SAP NetWeaver 7.5 の分散インストールを実行するときに、単一インスタンスの SAP HANA を手動でインストールするための主要な手順を示します。 個々の項目については、ガイドの中で示されているスクリーンショットでより詳しく説明しています。

* 2 つのテスト VM が含まれる Azure 仮想ネットワークを作成する。
* Azure Resource Manager モデルに従って、OS (この例では、SLES/SLES-for-SAP Applications 12 SP1) を備えた 2 つの Azure VM をデプロイする。
* Standard または Premium の Azure ストレージ ディスク (たとえば 75 GB または 500 GB のディスク) を 2 つ、アプリケーション サーバー VM にアタッチする。
* Premium Storage ディスクを HANA DB サーバー VM にアタッチする。 詳細については、「ディスクのセットアップ」セクションを参照してください。
* サイズまたはスループットの要件に応じて、複数のディスクをアタッチし、VM 内部の OS レベルで論理ボリューム管理 (LVM) または複数デバイス管理ユーティリティ (mdadm) を使ってストライプ ボリュームを作成する。
* アタッチされたディスク/論理ボリューム上に XFS ファイル システムを作成する。
* 新しい XFS ファイル システムを OS レベルでマウントする。 一方のファイル システムは SAP ソフトウェア全体を保持するために使います。もう一方のファイル システムは /sapmnt ディレクトリに使うほか、場合によってはバックアップに使います。 SAP HANA DB サーバーで、Premium Storage ディスクに XFS ファイル システムを /hana および /usr/sap としてマウントします。 このプロセスは、Linux Azure VM では容量が少ないルート ファイル システムがいっぱいになるのを防ぐために必要です。
* テスト VM のローカル IP アドレスを /etc/hosts に入力する。
* /etc/fstab に nofail パラメーターを入力する。
* HANA と使用している Linux OS のリリースに関連する SAP Note に従って、Linux カーネル パラメーターを設定する。 詳しくは、「カーネルパラメーター」のセクションをご覧ください。
* スワップ領域を追加する。
* 必要に応じて、テスト VM にグラフィカル デスクトップをインストールする。 インストールしない場合は、リモート SAPinst インストールを使います。
* SAP サービス マーケットプレースで SAP ソフトウェアをダウンロードする。
* SAP ASCS インスタンスをアプリ サーバー VM にインストールする。
* NFS を使って、テスト VM 間で /sapmnt ディレクトリを共有する。 アプリケーション サーバー VM は NFS サーバーです。
* SWPM を使って、HANA が含まれたデータベース インスタンスを DB サーバー VM にインストールする。
* アプリケーション サーバー VM に、プライマリ アプリケーション サーバー (PAS) をインストールする。
* SAP 管理コンソール (MC) を起動し、たとえば SAP GUI/HANA Studio に接続する。

## <a name="checklist-for-sap-hana-installation-using-hdblcm"></a>hdblcm を使った SAP HANA のインストールのチェックリスト
ここでは、SAP hdblcm を使って SAP NetWeaver 7.5 の分散インストールを実行するときに、デモやプロトタイプ作成の目的で単一インスタンスの SAP HANA を手動でインストールするための主要な手順を示します。 個々の項目については、ガイドの中で示されているスクリーンショットでより詳しく説明しています。

* 2 つのテスト VM が含まれる Azure 仮想ネットワークを作成する。
* Azure Resource Manager モデルに従って、OS (この例では、SLES/SLES-for-SAP Applications 12 SP1) を備えた 2 つの Azure VM をデプロイする。
* Standard または Premium の Azure ストレージ ディスク (たとえば 75 GB または 500 GB のディスク) を 2 つ、アプリ サーバー VM にアタッチする。
* Premium Storage ディスクを HANA DB サーバー VM にアタッチする。 詳細については、「ディスクのセットアップ」セクションを参照してください。
* サイズまたはスループットの要件に応じて、複数のディスクをアタッチし、VM 内部の OS レベルで論理ボリューム管理 (LVM) または複数デバイス管理ユーティリティ (mdadm) を使ってストライプ ボリュームを作成する。
* アタッチされたディスク/論理ボリューム上に XFS ファイル システムを作成する。
* 新しい XFS ファイル システムを OS レベルでマウントする。 一方のファイル システムは SAP ソフトウェア全体を保持するために使います。もう一方のファイル システムは /sapmnt ディレクトリに使うほか、場合によってはバックアップに使います。 SAP HANA DB サーバーで、Premium Storage ディスクに XFS ファイル システムを /hana および /usr/sap としてマウントします。 このプロセスは、Linux Azure VM では容量が少ないルート ファイル システムがいっぱいになるのを防ぐために必要です。
* テスト VM のローカル IP アドレスを /etc/hosts に入力する。
* /etc/fstab に nofail パラメーターを入力する。
* HANA と使用している Linux OS のリリースに関連する SAP Note に従って、カーネル パラメーターを設定する。 詳しくは、「カーネルパラメーター」のセクションをご覧ください。
* スワップ領域を追加する。
* 必要に応じて、テスト VM にグラフィカル デスクトップをインストールする。 インストールしない場合は、リモート SAPinst インストールを使います。
* SAP サービス マーケットプレースで SAP ソフトウェアをダウンロードする。
* HANA DB サーバー VM で、グループ ID が 1001 のグループ "sapsys" を作成する。
* HANA データベース ライフサイクル マネージャー (HDBLCM) を使って、DB サーバー VM に SAP HANA をインストールする。
* SAP ASCS インスタンスをアプリ サーバー VM にインストールする。
* NFS を使って、テスト VM 間で /sapmnt ディレクトリを共有する。 アプリケーション サーバー VM は NFS サーバーです。
* SWPM を使って、HANA が含まれたデータベース インスタンスを HANA DB サーバー VM にインストールする。
* アプリケーション サーバー VM に、プライマリ アプリケーション サーバー (PAS) をインストールする。
* SAP MC を起動し、SAP GUI/HANA Studio を使って接続する。

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>SAP HANA を手動でインストールするための Azure VM の準備
このセクションは、次のトピックで構成されています。

* OS の更新
* ディスクのセットアップ
* カーネル パラメーター
* ファイル システム
* /etc/hosts
* /etc/fstab

### <a name="os-updates"></a>OS の更新
セキュリティを維持し、操作を円滑にするための OS の更新プログラムと修正が Linux OS のディストリビューターから提供されています。そのため、ソフトウェアを追加でインストールする前に、利用可能な更新プログラムがないかどうかを確認するのが賢明です。
システムが現時点でのパッチ レベルであれば、サポートに何度も問い合わせることを回避できます。 次のソフトウェアを使用していることを確認してください。
* SUSE Linux Enterprise Server for SAP application
* Red Hat Enterprise Linux for SAP Application または Red Hat Enterprise Linux for SAP HANA 

Linux ベンダーの Linux サブスクリプションに OS のデプロイを登録します (この作業がまだの場合のみ)。 SUSE の場合、既にサービスが含まれた、自動的に登録される SAP アプリケーション用の SUSE OS イメージもあります。

例:  SUSE Linux の場合、次の方法で使用可能なパッチを簡単にチェックできます。

 `sudo zypper list-patches`

不具合の種類に基づいて、パッチはカテゴリ別および重大度別に分類されます。

一般的に使用されるカテゴリの値は、セキュリティ、推奨、オプション、機能、ドキュメント、または YaST です。

一般的に使用される重大度の値は、重大、重要、中、低、または未指定です。

zypper はインストールされているパッケージに必要な更新プログラムのみを検索します。

以下はコマンド例です。

`sudo zypper patch  --category=security,recommended --severity=critical,important`

*--dry-run* パラメーターを追加する場合は、更新プログラムのテストは可能ですが、実際のシステムの更新は行われません。


### <a name="disk-setup"></a>ディスクのセットアップ
Azure 上の Linux VM のルート ファイル システムには、サイズの制限があります。 このため、SAP を実行するための Azure VM に追加のディスク領域をアタッチする必要があります。 SAP アプリケーション サーバーの Azure VM の場合、Azure Standard Storage ディスクの使用は許容される可能性があります。 ただし、SAP HANA DBMS Azure VM の場合、運用環境と非運用環境のどちらの実装にも Azure Premium Storage ディスクの使用が必須です。

[SAP HANA TDI ストレージ要件](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)に基づいて、次の Azure Premium Storage ストレージ構成をお勧めします。 

| VM の SKU | RAM |  /hana/data and /hana/log <br /> (LVM または MDAADM によるストライピング) | /hana/shared | /root ボリューム | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

推奨されるディスク構成では、HANA データ ボリュームとログ ボリュームは、LVM または MDADM を使用してストライピングされている Azure Premium Storage ディスクの同じセットに配置されます。 Azure Premium Storage では冗長性を確保するためにディスクのイメージが 3 つ保持されるため、RAID 冗長レベルを定義する必要はありません。 別のディスク構成にすることもできます。 ただし、「[SAP HANA TDI storage Requirements (SAP HANA TDI ストレージ要件)](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)」および「[SAP HANA Server Installation and Update Guide (SAP HANA サーバー インストールおよびアップデート ガイド)](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)」を参照して、構成したストレージが十分であるかどうかを確認してください。 また、Azure Premium Storage ディスクの種類に応じて異なる VHD スループット ボリュームも考慮してください。ディスクの種類とスループットの関係は、[Premium Storage ディスクの制限](https://docs.microsoft.com/azure/storage/storage-premium-storage)に関するページに記載されています。 

必要に応じて、データベースまたはトランザクション ログのバックアップを格納するために、HANA DBMS VM に Premium Storage ディスクを追加することができます。

ストライピングを構成するための 2 つの主要なツールについて詳しくは、次の記事をご覧ください。

* [Linux でのソフトウェア RAID の構成](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure で Linux VM の LVM を構成する](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

ゲスト OS として Linux を実行している Azure VM にディスクをアタッチする方法については、[Linux VM にディスクをアタッチする方法](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページを参照してください。

Azure Premium Storage では、ディスクのキャッシュ モードを定義できます。 /hana/data と /hana/log を保持するストライプ セットでは、ディスク キャッシュを無効にする必要があります。 その他のボリューム (ディスク) では、キャッシュ モードを READONLY に設定する必要があります。

詳しくは、「[Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../../../storage/storage-premium-storage.md)」をご覧ください。

VM 作成用の JSON のサンプル テンプレートを検索するには、「[Azure Quickstart Templates](https://github.com/Azure/azure-quickstart-templates)」(Azure クイック スタート テンプレート) をご覧ください。
"vm-simple-sles" テンプレートは、100 GB の追加のデータ ディスクがあるストレージ セクションを含む基本テンプレートを示しています。 このテンプレートはベースとして使用することができます。 特定の構成に合わせてテンプレートを調整してください。

>[!Note]
>「[Microsoft Azure SUSE Linux VM での SAP NetWeaver の実行](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」に記載されているように、UUID を使用して Azure Storage ディスクをアタッチすることが重要です。


テスト環境で、次のスクリーンショットに示すように 2 つの Azure Standard Storage ディスクが SAP アプリ サーバー VM にアタッチされています。 1 つのディスクには、インストールで SAP ソフトウェア全体が格納されます (NetWeaver 7.5、SAP GUI、SAP HANA など)。 2 つ目のディスクは、追加の要件 (バックアップとテスト データなど) に対して十分な空き領域を使用可能にするため、また同じ SAP ランドスケープに属するすべての VM 間で /sapmnt ディレクトリ (つまり、SAP プロファイル) を共有するためのものです。

![2 つのデータ ディスクとそのサイズを表示している SAP HANA アプリ サーバーの [ディスク] ウィンドウ](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>カーネル パラメーター
SAP HANA では、標準的な Azure ギャラリー イメージに含まれていない、手動で設定する必要のある特定の Linux カーネル設定が必要です。 パラメーターは、SUSE と Red Hat とで異なる場合があります。 前述の SAP Note には、これらのパラメーターに関する情報が記載されています。 そこに掲載されていたスクリーンショットでは、SUSE Linux 12 SP1 が使用されていました。 

SLES-for-SAP Applications 12 GA と SP1 には、古い sapconf ユーティリティを置き換える新しいツールがあります。 これは tuned-adm というツールで、特殊な SAP HANA プロファイルを利用できます。 ルート ユーザーとして SAP HANA のシステムを調整する場合は、'tuned-adm profile sap-hana' と入力するだけです。

tuned-adm について詳しくは、以下の SUSE ドキュメントをご覧ください。

* [tuned-adm profile sap-hana に関する SLES-for-SAP Applications 12 SP1 ドキュメント](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)

次のスクリーンショットでは、必要とされる SAP HANA の設定に応じて transparent_hugepage と numa_balancing の値が tuned-adm によって変更されているようすがわかります。

![tuned-adm ツールは、必要な SAP HANA 設定に従って値を変更します](./media/hana-get-started/image005.jpg)

SAP HANA カーネル設定を永続的な設定にするには、SLES 12 で grub2 を使う必要があります。 grub2 について詳しくは、[SUSE ドキュメントの「Configuration File Structure」(ファイル構造の構成) セクション](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)をご覧ください。

次のスクリーンショットでは、構成ファイルでカーネル設定が変更されており、grub2-mkconfig を使ってコンパイルされていることがわかります。

![構成ファイルで変更され、grub2-mkconfig を使ってコンパイルされたカーネル設定](./media/hana-get-started/image006.jpg)

このほか、YaST で設定を変更し、**[ブート ローダー]** > **[カーネル パラメーター]** 設定を変更するという方法もあります。

![YaST ブート ローダーの [カーネル パラメーター] 設定タブ](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>ファイル システム
次のスクリーンショットは、SAP アプリ サーバー VM にアタッチされた 2 つの Azure Standard Storage ディスクに作成された 2 つのファイル システムを示しています。 どちらのファイル システムの種類も XFS であり、/sapdata と /sapsoftware にマウントされています。

この方法でファイル システムを構成することは必須ではありません。 ディスク領域を構築するための他のオプションもあります。 最も重要な考慮事項は、ルート ファイル システムの空き領域の不足を防ぐことです。

![SAP アプリ サーバー VM に作成された 2 つのファイル システム](./media/hana-get-started/image008.jpg)

SAP HANA DB VM に関しては、データベースのインストール時に SAPinst (SWPM) と単純で "一般的な" インストール オプションを使用した場合、すべてが /hana と /usr/sap にインストールされるという点を覚えておくことが重要です。 既定の設定では、SAP HANA ログ バックアップは /usr/sap に格納されます。 この場合もルート ファイル システムの記憶域スペースの不足を防ぐことが重要であるため、SWPM を使って SAP HANA をインストールする前に /hana と /usr/sap に十分な空き領域があることを確認します。

SAP HANA の標準のファイルシステム レイアウトの説明は、「[SAP HANA Server Installation and Update Guide](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)」(SAP HANA サーバーのインストールと更新のガイド) をご覧ください。
![SAP アプリ サーバー VM に作成された追加のファイル システム](./media/hana-get-started/image009.jpg)

標準的な SLES/SLES-for-SAP Applications 12 Azure ギャラリー イメージに SAP NetWeaver をインストールすると、スワップ領域がないことを示すメッセージが表示されます。 このメッセージを消すために、dd、mkswap、swapon を使ってスワップ ファイルを手動で追加できます。 その方法については、[SUSE ドキュメントの「Using the YaST Partitioner」(YaST パーティショナーの使用) セクション](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)で手動でのスワップ ファイルの追加に関する説明を検索してください。

別の方法としては、Linux VM エージェントを使ったスワップ領域の構成があります。 詳しくは、「[Azure Linux エージェント ユーザー ガイド](../../linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。

![十分なスワップ領域がないことを示すポップアップ メッセージ](./media/hana-get-started/image010.jpg)


### <a name="etchosts"></a>/etc/hosts
SAP のインストールを開始する前に念頭におく必要のあるもう 1 つの重要な点は、SAP VM のホスト名と IP アドレスを /etc/hosts ファイルに含めておくことです。 1 つの Azure 仮想ネットワーク内ですべての SAP VM をデプロイし、内部 IP アドレスを使います。

![/etc/hosts ファイルに表示される SAP VM のホスト名と IP アドレス](./media/hana-get-started/image011.jpg)

### <a name="etcfstab"></a>/etc/fstab

テスト フェーズ中に、nofail パラメーターを fstab に追加すると良いということがわかっています。 ディスクに何らかの問題がある場合でも VM は起動し、起動プロセス中に停止することはありません。 ただし、このシナリオと同様に、追加のディスク領域を使用できず、プロセスでルート ファイル システムがいっぱいになることがあるため、特に注意を払ってください。 /hana がない場合、SAP HANA は起動しません。

![nofail パラメーターを fstab に追加](./media/hana-get-started/image000c.jpg)

## <a name="install-graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>SLES 12/SLES-for-SAP Applications 12 への Gnome グラフィカル デスクトップのインストール
このセクションは、次のトピックで構成されています。

* SLES 12/SLES-for-SAP Applications 12 への Gnome デスクトップと xrdp のインストール
* SLES 12/SLES-for-SAP Applications 12 への Firefox を使った Java ベースの SAP MC の実行

Xterminal や VNC などの代替手段を使うこともできますが、2016年 9 月の時点では、ガイドでは xrdp についてのみ説明しています。

### <a name="installing-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>SLES 12/SLES-for-SAP Applications 12 への Gnome デスクトップと xrdp のインストール
Microsoft Windows に関する知識がある場合は、SAP Linux VM 内でグラフィカルなデスクトップを直接使って Firefox、Sapinst、SAP GUI、SAP MC、HANA Studio のいずれかを実行し、Windows コンピューターから RDP を介して VM に接続することは簡単です。 この手順は、運用データベース サーバーには適していない場合もありますが、純粋なプロトタイプ/デモ環境で使うには問題ありません。 Azure SLES 12/SLES-for-SAP Applications 12 VM に Gnome デスクトップをインストールする方法は、次のとおりです。

(putty ウィンドウなどで) 次のコマンドを入力して gnome デスクトップをインストールします。

`zypper in -t pattern gnome-basic`

RDP を使って VM に接続できるように xrdp をインストールします。

`zypper in xrdp`

/etc/sysconfig/windowmanager を編集し、既定のウィンドウ マネージャーを Gnome に設定します。

`DEFAULT_WM="gnome"`

再起動後に xrdp が自動的に起動するように、chkconfig を実行します。

`chkconfig -level 3 xrdp on`

RDP 接続に関する問題がある場合は (おそらく putty ウィンドウから) 再起動を試みます。

`/etc/xrdp/xrdp.sh restart`

上記の xrdp の再起動がうまくいかない場合、.pid ファイルがないかチェックし、削除します。

`check /var/run` を実行し、`xrdp.pid` を検索

それを削除し、再起動を試みます。

### <a name="sap-mc"></a>SAP MC
Gnome デスクトップをインストールした後で、Azure SLES 12/SLES-for-SAP Applications 12 VM を実行している Firefox からグラフィカルな Java ベース SAP MC を開始すると、Java ブラウザー プラグインがないためエラーが表示されることがあります。

SAP MC を開始するための URL は、<server>:5<instance_number>13 です。

詳しくは、「[Starting the Web-Based SAP Management Console](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)」(Web ベースの SAP 管理コンソールの起動) をご覧ください。

次のスクリーンショットは、Java ブラウザー プラグインが見つからない場合に表示されるエラー メッセージを示しています。

![見つからない Java ブラウザー プラグインを示すエラー メッセージ](./media/hana-get-started/image013.jpg)

問題を解決する方法の 1 つは、次のスクリーン ショットに示すように、YaST を使って見つからないプラグインをインストールすることです。

![YaST を使った、見つからないプラグインのインストール](./media/hana-get-started/image014.jpg)

SAP 管理コンソールの URL を繰り返すと、プラグインのアクティブ化を求めるダイアログ ボックスが表示されます。

![プラグインのアクティブ化を要求するダイアログ ボックス](./media/hana-get-started/image015.jpg)

発生する可能性のある 1 つの別の問題は、見つからないファイル javafx.properties に関するエラー メッセージです。 これは、SAP GUI 7.4 の Oracle Java 1.8 の要件に関連しています。 [SAP Note 2059429 をご覧ください。](https://launchpad.support.sap.com/#/notes/2059424)IBM Java バージョンにも、SLES/SLES-for-SAP Applications 12 と共に配信された openjdk パッケージにも、必要な javafx は含まれていません。 解決策は、Oracle から Java SE8 をダウンロードしてインストールすることです。

OpenSUSE と openjdk の同様の問題に関する記事は「[SAPGui 7.4 Java for openSUSE 42.1 Leap (openSUSE 42.1 Leap の SAPGui 7.4 Java)](https://scn.sap.com/thread/3908306)」をご覧ください。

## <a name="install-sap-hana-manually-by-using-swpm-as-part-of-a-netweaver-75-installation"></a>NetWeaver 7.5 インストールの一環として実行する、SWPM を使った SAP HANA の手動インストール
このセクションの一連のスクリーンショットは、SWPM (SAPinst) を使う場合に SAP NetWeaver 7.5 と SAP HANA SP12 をインストールするための主要な手順を示しています。 NW 7.5 インストールの一環として、SWPM では HANA データベースを単一インスタンスとしてインストールすることもできます。

サンプルのテスト環境で、ABAP (Advanced Business Application Programming) アプリ サーバーを 1 つだけインストールしました。 次のスクリーンショットに示すように、[分散システム] オプションを使って、1 つの Azure VM に ASCS インスタンスとプライマリ アプリケーション サーバー インスタンスをインストールし、別の Azure VM に SAP HANA をデータベース システムとしてインストールしました。

![[分散システム] オプションを使ってインストールされた ASCS インスタンスとプライマリ アプリケーション サーバー インスタンス](./media/hana-get-started/image012.jpg)

ASCS インスタンスがアプリ サーバー VM にインストールされ、SAP 管理コンソールで "緑色" に変わったら、SAP プロファイル ディレクトリなどが含まれた /sapmnt ディレクトリを SAP HANA DB サーバー VM と共有させる必要があります。 DB のインストール手順では、この情報へのアクセスが必要になります。 アクセスを可能にする最善の方法は、YaST を使って構成できる NFS を使うことです。

![ASCS インスタンスがアプリ サーバー VM にインストールされ、"緑色" に変わった SAP 管理コンソール](./media/hana-get-started/image016.jpg)

アプリ サーバー VM で、**rw** オプションと **no_root_squash** オプションを使って NFS で /sapmnt ディレクトリを共有する必要があります。 既定では "ro" と "root_squash" になっており、データベース インスタンスをインストールする際に問題が生じる可能性があります。

!["rw" オプションと "no_root_squash" オプションを使った NFS による /sapmnt ディレクトリの共有](./media/hana-get-started/image017b.jpg)

次のスクリーンショットに示すように、SAP HANA DB サーバー VM で、(YaST などを利用して) **NFS クライアント** を使ってアプリ サーバー VM からの /sapmnt 共有を構成する必要があります。

!["NFS クライアント" を使って構成された /sapmnt 共有](./media/hana-get-started/image018b.jpg)

分散 NetWeaver 7.5 インストールを実行するには、次のスクリーンショットに示すように、**[データベース インスタンス]** で SAP HANA DB サーバー VM にサインインし、SWPM を開始します。

![SAP HANA DB サーバー VM へのサインインと SWPM の開始によるデータベース インスタンスのインストール](./media/hana-get-started/image019.jpg)

"標準" のインストールと、インストール メディアへのパスを選んだ後で、DB SID、ホスト名、インスタンス番号、DB システム管理者のパスワードを入力します。

![SAP HANA データベース システム管理者のサインイン ページ](./media/hana-get-started/image035b.jpg)

DBACOCKPIT スキーマのパスワードを入力します。

![DBACOCKPIT スキーマのパスワード入力ボックス](./media/hana-get-started/image036b.jpg)

SAPABAP1 スキーマ パスワードの質問を入力します。

![SAPABAP1 スキーマ パスワードの質問の入力](./media/hana-get-started/image037b.jpg)

タスクの完了後に、DB インストール プロセスの各フェーズの横に緑色のチェック マークが表示されます。 [メッセージ ボックス] ウィンドウに、"Execution of ...Database Instance has completed (データベース インスタンスの...実行が完了しました)" というメッセージが表示されます。

![確認メッセージが表示されたタスク完了ウィンドウ](./media/hana-get-started/image023.jpg)

インストールの成功後、SAP 管理コンソールでは DB インスタンスが "緑色" で表示され、SAP HANA プロセス (hdbindexserver、hdbcompileserver など) の完全な一覧が表示されます。

![SAP HANA プロセスの一覧が表示された SAP 管理コンソール ウィンドウ](./media/hana-get-started/image024.jpg)

次のスクリーンショットでは、HANA のインストール中に SWPM によって作成された /hana/shared のファイル構造が部分的に示されています。 別のパスを指定するオプションがなかったため、ルート ファイル システムの空き領域の不足を防ぐために、SAP HANA インストールの前に SWPM を使って /hana にある追加のディスク領域をマウントすることが重要です。

![HANA のインストール時に作成された /hana/shared ファイル構造](./media/hana-get-started/image025.jpg)

このスクリーン ショットは、/usr/sap ディレクトリのファイル構造を示しています。

![/usr/sap ディレクトリのファイル構造](./media/hana-get-started/image026.jpg)

ABAP 分散インストールの最後の手順は、"プライマリ アプリケーション サーバー インスタンス" です。

![最後の手順としてプライマリ アプリケーション サーバー インスタンスを表示している ABAP インストール](./media/hana-get-started/image027b.jpg)

プライマリ アプリケーション サーバー インスタンスと SAP GUI をインストールした後で、トランザクション "dbacockpit" を使って SAP HANA インストールが正常に完了したことを確認します。

![正常なインストールを確認する [DBA Cockpit (DBA コックピット)] ウィンドウ](./media/hana-get-started/image028b.jpg)

最後の手順として、SAP HANA Studio を SAP アプリ サーバー VM にインストールし、DB サーバー VM で実行されている SAP HANA インスタンスに接続できます。

![SAP アプリ サーバー VM への SAP HANA Studio のインストール](./media/hana-get-started/image038b.jpg)

## <a name="install-sap-hana-manually-by-using-the-hana-lifecycle-management-tool-hdblcm"></a>HANA ライフサイクル管理ツール (hdblcm) を使った SAP HANA の手動インストール
SWPM を使って SAP HANA を分散インストールの一部としてインストールすることに加えて、hdblcm を使って HANA スタンドアロンを最初にインストールできます。 その後、たとえば SAP NetWeaver 7.5 をインストールできます。 次のスクリーンショットは、このプロセスのしくみを示しています。

HANA hdblcm ツールに関する 3 つの情報源を次に示します。

* [タスクに適した SAP HANA HDBLCM の選択](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)
* [SAP HANA ライフサイクル管理ツール](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)
* [SAP HANA サーバーのインストールと更新のガイド](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)

(hdblcm ツールによって作成される) \<HANA SID\>adm ユーザーの既定のグループ ID 設定に関する問題を回避するために、hdblcm を使って SAP HANA をインストールする前に、グループ ID 1001 を使って "sapsys" という新しいグループを定義します。

![グループ ID 1001 を使って定義された新しいグループ "sapsys"](./media/hana-get-started/image030.jpg)

hdblcm を初めて開始するときに、シンプルなスタート メニューが表示されます。 次のスクリーンショットに示すように項目 1、**[Install new system (新しいシステムのインストール)]** を選びます。

![hdblcm スタート ウィンドウの [Install new system (新しいシステムのインストール)] オプション](./media/hana-get-started/image031.jpg)

次のスクリーンショットには、前に選んだすべての主要なオプションが表示されています。

> [!IMPORTANT]
> HANA ログ ボリュームとデータ ボリュームに指定されたディレクトリに加えて、インストール パス (このサンプルでは /hana/shared) と /usr/sap はルート ファイル システムの一部にしないでください。 ディレクトリは、Azure VM のセットアップ セクションで説明しているように、VM にアタッチされている Azure データ ディスクに属します。 この方法は、ルート ファイル システム領域が不足するリスクを回避するために役立ちます。 HANA 管理者のユーザー ID が 1005 であり、インストール前に定義した sapsys グループ (ID 1001) に属していることもわかります。

![前に選んだすべての主要 SAP HANA コンポーネントの一覧](./media/hana-get-started/image032.jpg)

/etc/passwd で、HANA \<HANA SID\>adm (次のスクリーンショットでは azdadm) ユーザーの詳細を確認できます。

![/etc/passwd に示されている HANA \<HANA SID\>adm ユーザーの詳細](./media/hana-get-started/image033.jpg)

hdblcm を使って SAP HANA をインストールした後で、SAP HANA Studio のファイル構造を確認できます。 すべての SAP NetWeaver テーブルが含まれる SAPABAP1 スキーマは、まだ使用できません。

![SAP HANA Studio の SAP HANA ファイル構造](./media/hana-get-started/image034.jpg)

SAP HANA をインストールしたら、その SAP HANA に SAP NetWeaver をインストールできます。 このスクリーンショットに示すように、インストールは (前のセクションで説明したように) SWPM を使って "分散インストール" として実行されました。 SWPM を使ってデータベース インスタンスをインストールする場合は、hdblcm を使って同じデータを入力します (ホスト名、HANA SID、インスタンス番号など) 。 SWPM は、HANA の既存のインストールを使い、スキーマをさらに追加します。

![SWPM を使って実行される分散インストール](./media/hana-get-started/image035b.jpg)

次のスクリーンショットは、DBACOCKPIT スキーマに関するデータを入力する SWPM インストール手順を示しています。

![DBACOCKPIT スキーマ データが入力された SWPM インストール手順](./media/hana-get-started/image036b.jpg)

SAPABAP1 スキーマに関するデータを入力します。

![SAPABAP1 スキーマに関するデータの入力](./media/hana-get-started/image037b.jpg)

SWPM データベース インスタンスのインストールが完了したら、HANA Studio で SAPABAP1 スキーマを確認できます。

![SAP HANA studio の SAPABAP1 スキーマ](./media/hana-get-started/image038b.jpg)

最後に、SAP アプリ サーバーと SAP GUI のインストールの完了後に、"dbacockpit" トランザクションを使って、HANA DB インスタンスを確認できます。

!["dbacockpit" トランザクションで確認された HANA DB インスタンス](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>SAP ソフトウェアのダウンロード
次のスクリーンショットに示すように、SAP Service Marketplace からソフトウェアをダウンロードできます。

* Linux/HANA 用の NetWeaver 7.5 のダウンロード:

 ![NetWeaver 7.5 をダウンロードするための [SAP Service Installation and Upgrade (SAP サービスのインストールとアップグレード)] ウィンドウ](./media/hana-get-started/image001.jpg)
* HANA SP12 Platform Edition のダウンロード:

 ![HANA SP12 Platform Edition をダウンロードするための [SAP Service Installation and Upgrade (SAP サービスのインストールとアップグレード)] ウィンドウ](./media/hana-get-started/image002.jpg)


