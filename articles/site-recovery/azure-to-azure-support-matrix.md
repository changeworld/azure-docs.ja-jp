---
title: Azure Site Recovery を使用した Azure VM のディザスター リカバリーのサポート マトリックス
description: Azure Site Recovery を使用したセカンダリ リージョンへの Azure VM ディザスター リカバリーのサポートの概要を説明します。
ms.topic: article
ms.date: 11/29/2020
author: Sharmistha-Rai
ms.author: sharrai
ms.openlocfilehash: 64197f1ef9c2797f6d021ab7ca8dea6e7bc05995
ms.sourcegitcommit: 1a0fe16ad7befc51c6a8dc5ea1fe9987f33611a1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131867268"
---
# <a name="support-matrix-for-azure-vm-disaster-recovery-between-azure-regions"></a>Azure リージョン間での Azure VM ディザスター リカバリーに関するサポート マトリックス

この記事は、[Azure Site Recovery](site-recovery-overview.md) サービスを使用して、Azure リージョン間での Azure VM のディザスター リカバリーに対するサポートと前提条件をまとめたものです。


## <a name="deployment-method-support"></a>デプロイ方法のサポート

**デプロイ** |  **サポート**
--- | ---
**Azure Portal** | サポートされています。
**PowerShell** | サポートされています。 [詳細情報](azure-to-azure-powershell.md)
**REST API** | サポートされています。
**CLI** | 現在、サポートされていません


## <a name="resource-support"></a>リソースのサポート

**リソースのアクション** | **詳細**
--- | ---
**リソース グループ間の資格情報コンテナーの移動** | サポートされていません
**リソース グループ間のコンピューティング、ストレージ、およびネットワークの移動** | サポートされていません。<br/><br/> VM のレプリケート後に VM や関連コンポーネント (ストレージやネットワークなど) を移動する場合は、その VM のレプリケーションを無効にしてから、再度有効にする必要があります。
**ディザスター リカバリーのためにあるサブスクリプションから別のサブスクリプションに Azure VM をレプリケートする** | 同じ Azure Active Directory テナント内でサポートされます。
**サポートされている地理的なクラスター内 (サブスクリプション内外) のさまざまなリージョンにわたって VM を移行する** | 同じ Azure Active Directory テナント内でサポートされます。
**同じリージョン内の VM の移行** | サポートされていません。
**Azure 専用ホスト** | サポートされていません。

## <a name="region-support"></a>リージョンのサポート

Azure Site Recovery を使用すると、グローバルなディザスター リカバリーを実行できます。 世界中の任意の 2 つの Azure リージョン間で VM をレプリケートして、復旧できます。 データ主権に関する懸念事項がある場合は、特定の地理クラスター内でレプリケーションを制限することを選択できます。 さまざまな地理クラスターを以下に示します。

**地理クラスター** | **Azure リージョン**
-- | --
America | カナダ東部、カナダ中部、米国中南部、米国中西部、米国東部、米国東部 2、米国西部、米国西部 2、米国西部 3、米国中部、米国中北部
ヨーロッパ | 英国西部、英国南部、北ヨーロッパ、西ヨーロッパ、南アフリカ西部、南アフリカ北部、ノルウェー東部、フランス中部、スイス北部、ドイツ中西部、アラブ首長国連邦北部、アラブ首長国連邦中部 (アラブ首長国連邦はヨーロッパ geo クラスターの一部として扱われます)
アジア | インド南部、インド中部、インド西部、東南アジア、東アジア、東日本、西日本、韓国中部、韓国南部
JIO | JIO インド西部
オーストラリア    | オーストラリア東部、オーストラリア南東部、オーストラリア中部、オーストラリア中部 2
Azure Government    | 米国政府バージニア、US Gov アイオワ、米国政府アリゾナ、米国政府テキサス、米国防総省東部、米国防総省中部
ドイツ    | ドイツ中部、ドイツ北東部
中国 | 中国東部、中国北部、中国北部 2、中国東部 2
ブラジル | ブラジル南部
国内のディザスター リカバリーのために予約されている、制限されたリージョン |スイス西部はスイス北部用に予約されており、フランス南部はフランス中部用に予約されており、ノルウェー西部はノルウェー東部のお客様用に制限されており、JIO インド中部は JIO インド西部のお客様用に制限されており、ブラジル南東部はブラジル南部のお客様用に制限されており、南アフリカ西部は南アフリカ北部のお客様用に制限されており、ドイツ北部はドイツ中西部のお客様用に制限されています。

>[!NOTE]
>
> - **ブラジル南部** の場合、次のリージョンにレプリケートおよびフェールオーバーできます: ブラジル南東部、米国中南部、米国中西部、米国東部、米国東部 2、米国西部、米国西部 2、および米国中北部。
> - ブラジル南部は、Site Recovery を使用して VM をレプリケートするソース リージョンとしてのみ使用できます。 ターゲット リージョンとしては動作できません。 ソース リージョンとしてのブラジル南部からターゲットにフェールオーバーする場合は、ターゲット リージョンからブラジル南部へのフェールバックがサポートされていることに注意してください。 ブラジル南東部はターゲット リージョンとしてのみ使用できます。
> - コンテナーの作成を希望しているリージョンが表示されない場合は、お使いのサブスクリプションが、そのリージョン内にリソースを作成するアクセス権を持っていることを確認します。
> - レプリケーションを有効にしても地理的クラスター内にリージョンが表示されない場合は、お使いのサブスクリプションにそのリージョンに VM を作成するアクセス許可があることを確認します。

## <a name="cache-storage"></a>キャッシュ ストレージ

この表は、レプリケーション中に Site Recovery によって使用されるキャッシュ ストレージ アカウントのサポートをまとめたものです。

**設定** | **サポート** | **詳細**
--- | --- | ---
汎用目的 V2 ストレージ アカウント (ホット層とクール層) | サポートされています | V2 のトランザクション コストは V1 ストレージ アカウントよりかなり高いため、GPv2 の使用はお勧めできません。
Premium Storage | サポートされていません | コストの最適化のために、キャッシュ ストレージには標準のストレージ アカウントが使用されます。
リージョン |  仮想マシンと同じリージョン  | キャッシュ ストレージ アカウントは、保護されている仮想マシンと同じリージョンに存在する必要があります。
サブスクリプション  | ソース仮想マシンとは異なる場合があります。 | キャッシュ ストレージ アカウントは、ソース仮想マシンと同じサブスクリプションに存在している必要はありません。
仮想ネットワークの Azure Storage ファイアウォール  | サポートされています | ファイアウォールが有効なキャッシュ ストレージ アカウントまたはターゲット ストレージ アカウントを使用している場合は必ず、['信頼できる Microsoft サービスを許可'](../storage/common/storage-network-security.md#exceptions) してください。<br></br>また、ソース Vnet の少なくとも 1 つ以上のサブネットにアクセスできることを確認してください。<br></br>注: 仮想ネットワークへのアクセスを、Site Recovery に使用しているご自分のストレージ アカウントのみに制限しないでください。 "すべてのネットワーク" からのアクセスを許可する必要があります。
論理的な削除 | サポートされていません | 論理的な削除はサポートされていません。これをキャッシュ ストレージ　アカウントで有効にすると、コストが増加するためです。 ASR では、ログ ファイルの作成/削除が非常に頻繁に行われ、レプリケートによってコストが増加します。

下の表は、1 つのストレージ アカウントにレプリケートできるディスクの数に関する制限を示しています。

**ストレージ アカウントの種類**    |    **チャーン = ディスクあたり 4 MBps**    |    **チャーン = ディスクあたり 8 MBps**
---    |    ---    |    ---
V1 ストレージ アカウント    |    300 個のディスク    |    150 個のディスク
V2 ストレージ アカウント    |    750 個のディスク    |    375 個のディスク

ディスクの平均チャーンが増加するにつれて、ストレージ アカウントでサポートできるディスクの数が減少します。 上記の表は、プロビジョニングする必要があるストレージ アカウントの数を決定するためのガイドとして使用できます。

上記の制限は、Azure 間とゾーン間の DR シナリオに固有であることに注意してください。

## <a name="replicated-machine-operating-systems"></a>レプリケートされるマシンのオペレーティング システム

Site Recovery は、このセクションに示したオペレーティング システムを実行する Azure VM のレプリケーションをサポートしています。 既にレプリケートされているマシンがその後別のメジャーなカーネルにアップグレード (またはダウングレード) される場合は、レプリケーションを無効にして、アップグレード後にレプリケーションを再度有効にする必要があることに注意してください。

### <a name="windows"></a>Windows


**オペレーティング システム** | **詳細**
--- | ---
Windows Server 2019 | Server Core、Server with Desktop Experience でサポートされています。
Windows Server 2016  | サーバー コア、デスクトップ エクスペリエンス搭載サーバーに対してサポートされます。
Windows Server 2012 R2 | サポートされています。
Windows Server 2012 | サポートされています。
Windows Server 2008 R2 with SP1/SP2 | サポートされています。<br/><br/> Azure VM の Mobility Service 拡張機能のバージョン [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) から、Windows Server 2008 R2 SP1/SP2 を実行しているマシンに Windows [サービス スタック更新プログラム (SSU)](https://support.microsoft.com/help/4490628) と [SHA-2 更新プログラム](https://support.microsoft.com/help/4474419)をインストールする必要があります。  SHA-1 は 2019 年 9 月からはサポートされておらず、SHA-2 コード署名が有効になっていない場合、エージェント拡張機能は正常にインストールまたはアップグレードされません。 SHA-2 のアップグレードと要件についての詳細は、[こちら](https://aka.ms/SHA-2KB)でご確認ください。
Windows 10 (x64) | サポートされています。
Windows 8.1 (x64) | サポートされています。
Windows 8 (x64) | サポートされています。
Windows 7 (x64) with SP1 以降 | Azure VM の Mobility Service 拡張機能のバージョン [9.30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) から、Windows 7 with SP1 を実行しているマシンに Windows [サービス スタック更新プログラム (SSU)](https://support.microsoft.com/help/4490628) と [SHA-2 更新プログラム](https://support.microsoft.com/help/4474419)をインストールする必要があります。  SHA-1 は 2019 年 9 月からはサポートされておらず、SHA-2 コード署名が有効になっていない場合、エージェント拡張機能は正常にインストールまたはアップグレードされません。 SHA-2 のアップグレードと要件についての詳細は、[こちら](https://aka.ms/SHA-2KB)でご確認ください。



#### <a name="linux"></a>Linux

**オペレーティング システム** | **詳細**
--- | ---
Red Hat Enterprise Linux | 6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、[7.7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery)、[7.8](https://support.microsoft.com/help/4564347/)、[7.9](https://support.microsoft.com/help/4578241/)、[8.0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)、8.1、[8.2](https://support.microsoft.com/help/4570609/)、[8.3](https://support.microsoft.com/help/4597409/)
CentOS | 6.5、6.6、6.7、6.8、6.9、6.10 </br> 7.0、7.1、7.2、7.3、7.4、7.5、7.6、7.7、[7.8](https://support.microsoft.com/help/4564347/)、[7.9 GA 前バージョン](https://support.microsoft.com/help/4578241/)、7.9 GA バージョンは 9.37 ホット フィックス修正プログラムからサポートされています** </br> 8.0、8.1、[8.2](https://support.microsoft.com/help/4570609)、[8.3](https://support.microsoft.com/help/4597409/)
Ubuntu 14.04 LTS Server | すべての 14.04.*x* バージョンのサポートが含まれます ([サポートされているカーネル バージョン](#supported-ubuntu-kernel-versions-for-azure-virtual-machines))
Ubuntu 16.04 LTS Server | すべての 16.04.*x* バージョンのサポートが含まれます ([サポートされているカーネル バージョン](#supported-ubuntu-kernel-versions-for-azure-virtual-machines))<br/><br/> パスワード ベースの認証とサインインを使用しており、cloud-init パッケージを使用してクラウド VM を構成する Ubuntu サーバーでは、(cloudinit 構成に応じて) フェールオーバー時にパスワード ベースのサインインが無効になっている場合があります。 パスワード ベースのサインインは、Azure portal で、フェールオーバーされた VM の [サポート] > [トラブルシューティング] > [設定] メニューからパスワードをリセットすることで、その仮想マシンで再度有効にできます。
Ubuntu 18.04 LTS Server | すべての 18.04.*x* バージョンのサポートが含まれます ([サポートされているカーネル バージョン](#supported-ubuntu-kernel-versions-for-azure-virtual-machines))<br/><br/> パスワード ベースの認証とサインインを使用しており、cloud-init パッケージを使用してクラウド VM を構成する Ubuntu サーバーでは、(cloudinit 構成に応じて) フェールオーバー時にパスワード ベースのサインインが無効になっている場合があります。 パスワード ベースのサインインは、Azure portal で、フェールオーバーされた VM の [サポート] > [トラブルシューティング] > [設定] メニューからパスワードをリセットすることで、その仮想マシンで再度有効にできます。
Ubuntu 20.04 LTS server | すべての 20.04.*x* バージョンのサポートが含まれます ([サポートされているカーネル バージョン](#supported-ubuntu-kernel-versions-for-azure-virtual-machines))
Debian 7 | すべての 7. *x* バージョンのサポートが含まれます ([サポートされるカーネル バージョン](#supported-debian-kernel-versions-for-azure-virtual-machines))
Debian 8 | すべての 8. *x* バージョンのサポートが含まれます ([サポートされるカーネル バージョン](#supported-debian-kernel-versions-for-azure-virtual-machines))
Debian 9 | 9\.1 から 9.13 までのサポートが含まれています。 Debian 9.0 はサポートされていません。 [サポートされるカーネル バージョン](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 10 | [サポートされるカーネル バージョン](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1、SP2、SP3、SP4、SP5 ([サポートされるカーネル バージョン](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines))
SUSE Linux Enterprise Server 15 | 15、SP1、SP2 [(サポートされるカーネル バージョン)](#supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> レプリケートするマシンの SP3 から SP4 へのアップグレードはサポートされません。 レプリケートされたマシンがアップグレードされた場合は、レプリケーションを無効にして、アップグレード後にレプリケーションを再度有効にする必要があります。
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4、6.5、6.6、6.7、6.8、6.9、6.10、7.0、7.1、7.2、7.3、7.4、7.5、7.6、[7.7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)、[7.8](https://support.microsoft.com/help/4573888/)、[7.9](https://support.microsoft.com/help/4597409)、[8.0](https://support.microsoft.com/help/4573888/)、[8.1](https://support.microsoft.com/help/4573888/)、[8.2](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8)、[8.3](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8) Red Hat と互換可能なカーネルまたは Unbreakable Enterprise カーネル リリース 3、4、5、6 (UEK3、UEK4、UEK5、UEK6) を実行している<br/><br/>8.1 すべての UEK カーネルと 3.10.0-1062.* 以前の RedHat カーネルでの実行が [9.35](https://support.microsoft.com/help/4573888/) でサポートされています。 それ以外の RedHat カーネルのサポートは [9.36](https://support.microsoft.com/help/4578241/) で利用できます

> [!NOTE]
> Linux バージョンの場合、Azure Site Recovery ではカスタム OS カーネルがサポートされません。 ディストリビューションのマイナー バージョン リリース/更新の一部である stock カーネルのみがサポートされます。

** 注: 最新の Linux カーネルをリリースから 15 日以内にサポートするため、Azure Site Recovery では最新のモビリティ エージェント バージョンに加えてホットフィックス修正プログラムがロールアウトされます。 この修正は、2 つのメジャー バージョンのリリースの間にロールアウトされます。 最新バージョンのモビリティ エージェント (ホットフィックス修正プログラムを含む) に更新するには、[こちらの記事](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)で示されている手順に従います。 この修正プログラムは、現在、Azure から Azure への DR シナリオで使用されるモビリティ エージェントに対してロールアウトされます。

#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Azure 仮想マシン用のサポートされる Ubuntu カーネル バージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
14.04 LTS | [9.40](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)、[9.41](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)、[9.42](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8)、[9.43](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6)、[9.44](https://support.microsoft.com/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 3.13.0-24-generic から 3.13.0-170-generic、<br/>3.16.0-25-generic ～ 3.16.0-77-generic、<br/>3.19.0-18-generic ～ 3.19.0-80-generic、<br/>4.2.0-18-generic ～ 4.2.0-42-generic、<br/>4.4.0-21-generic から 4.4.0-148-generic、<br/>4.15.0-1023-azure から 4.15.0-1045-azure |
|||
16.04 LTS | [9.44](https://support.microsoft.com/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 4.4.0-21-generic から 4.4.0-206-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-140-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1111-azure|
16.04 LTS | [9.43](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) | 4.4.0-21-generic から 4.4.0-206-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-140-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1111-azure|
16.04 LTS | [9.42](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8) | 4.4.0-21-generic から 4.4.0-206-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-140-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1111-azure|
16.04 LTS | [9.41](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.4.0-21-generic から 4.4.0-201-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-133-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1106-azure <br/> 4.4.0-203-generic、4.4.0-204-generic、4.4.0-206-generic、4.15.0-136-generic、4.15.0-137-generic、4.15.0-139-generic、4.15.0-140-generic、4.15.0-1108-azure、4.15.0-1109-azure、4.15.0-1110-azure、4.15.0-1111-azure から 9.41 までのホット フィックス修正プログラム**|
16.04 LTS | [9.40](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.4.0-21-generic から 4.4.0-197-generic、<br/>4.8.0-34-generic ～ 4.8.0-58-generic、<br/>4.10.0-14-generic ～ 4.10.0-42-generic、<br/>4.11.0-13-generic ～ 4.11.0-14-generic、<br/>4.13.0-16-generic から 4.13.0-45-generic、<br/>4.15.0-13-generic から 4.15.0-128-generic<br/>4.11.0-1009-azure ～ 4.11.0-1016-azure、<br/>4.13.0-1005-azure から 4.13.0-1018-azure <br/>4.15.0-1012-azure から 4.15.0-1102-azure </br> 4.15.0-132-generic、4.4.0-200-generic、4.15.0-1106-azure、4.15.0-133-generic、4.4.0-201-generic から 9.40 までのホット フィックス修正プログラム**|
|||
18.04 LTS |[9.45](https://support.microsoft.com/topic/update-rollup-58-for-azure-site-recovery-kb5007075-37ba21c3-47d9-4ea9-9130-a7d64f517d5d) | 4.15.0-1123-azure </br> 5.4.0-1058-azure </br> 4.15.0-156-generic </br> 4.15.0-1125-azure </br> 4.15.0-161-generic </br> 5.4.0-1061-azure </br> 5.4.0-1062-azure </br> 5.4.0-89-generic |
18.04 LTS | [9.44](https://support.microsoft.com/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 4.15.0-20-generic から 4.15.0-140-generic </br> 4.18.0-13-generic から 4.18.0-25-generic </br> 5.0.0-15-generic から 5.0.0-65-generic </br> 5.3.0-19-generic から 5.3.0-72-generic </br> 5.4.0-37-generic から 5.4.0-70-generic </br> 4.15.0-1009-azure から 4.15.0-1111-azure </br> 4.18.0-1006-azure から 4.18.0-1025-azure </br> 5.0.0-1012-azure から 5.0.0-1036-azure </br> 5.3.0-1007-azure から 5.3.0-1035-azure </br> 5.4.0-1020-azure から 5.4.0-1043-azure </br> 4.15.0-1114-azure </br> 4.15.0-143-generic </br> 5.4.0-1047-azure </br> 5.4.0-73-generic </br> 4.15.0-1115-azure </br> 4.15.0-144-generic </br> 5.4.0-1048-azure </br> 5.4.0-74-generic </br> 4.15.0-1121-azure </br> 4.15.0-151-generic </br> 4.15.0-153-generic </br> 5.3.0-76-generic </br> 5.4.0-1055-azure </br> 5.4.0-80-generic </br> 4.15.0-147-generic </br> 4.15.0-153-generic </br> 5.4.0-1056-azure </br> 5.4.0-81-generic </br> 4.15.0-1122-azure </br> 4.15.0-154-generic |
18.04 LTS | [9.43](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) | 4.15.0-20-generic から 4.15.0-140-generic </br> 4.18.0-13-generic から 4.18.0-25-generic </br> 5.0.0-15-generic から 5.0.0-65-generic </br> 5.3.0-19-generic から 5.3.0-72-generic </br> 5.4.0-37-generic から 5.4.0-70-generic </br> 4.15.0-1009-azure から 4.15.0-1111-azure </br> 4.18.0-1006-azure から 4.18.0-1025-azure </br> 5.0.0-1012-azure から 5.0.0-1036-azure </br> 5.3.0-1007-azure から 5.3.0-1035-azure </br> 5.4.0-1020-azure から 5.4.0-1043-azure </br> 4.15.0-1114-azure </br> 4.15.0-143-generic </br> 5.4.0-1047-azure </br> 5.4.0-73-generic </br> 4.15.0-1115-azure </br> 4.15.0-144-generic </br> 5.4.0-1048-azure </br> 5.4.0-74-generic </br> 4.15.0-1121-azure </br> 4.15.0-151-generic </br> 4.15.0-153-generic </br> 5.3.0-76-generic </br> 5.4.0-1055-azure </br> 5.4.0-80-generic </br> 4.15.0-147-generic |
18.04 LTS |[9.42](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8) | 4.15.0-20-generic から 4.15.0-140-generic </br> 4.18.0-13-generic から 4.18.0-25-generic </br> 5.0.0-15-generic から 5.0.0-65-generic </br> 5.3.0-19-generic から 5.3.0-72-generic </br> 5.4.0-37-generic から 5.4.0-70-generic </br> 4.15.0-1009-azure から 4.15.0-1111-azure </br> 4.18.0-1006-azure から 4.18.0-1025-azure </br> 5.0.0-1012-azure から 5.0.0-1036-azure </br> 5.3.0-1007-azure から 5.3.0-1035-azure </br> 5.4.0-1020-azure から 5.4.0-1043-azure </br> 4.15.0-1114-azure </br> 4.15.0-143-generic </br> 5.4.0-1047-azure </br> 5.4.0-73-generic </br> 4.15.0-1115-azure </br> 4.15.0-144-generic </br> 5.4.0-1048-azure </br> 5.4.0-74-generic |
18.04 LTS | [9.41](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.15.0-20-generic から 4.15.0-135-generic </br> 4.18.0-13-generic から 4.18.0-25-generic </br> 5.0.0-15-generic から 5.0.0-65-generic </br> 5.3.0-19-generic から 5.3.0-70-generic </br> 5.4.0-37-generic から 5.4.0-59-generic</br> 5.4.0-60-generic から 5.4.0-65-generic </br> 4.15.0-1009-azure から 4.15.0-1106-azure </br> 4.18.0-1006-azure から 4.18.0-1025-azure </br> 5.0.0-1012-azure から 5.0.0-1036-azure </br> 5.3.0-1007-azure から 5.3.0-1035-azure </br> 5.4.0-1020-azure から 5.4.0-1039-azure </br> 4.15.0-136-generic、4.15.0-137-generic、4.15.0-139-generic、4.15.0-140-generic、5.3.0-72-generic、5.4.0-66-generic、5.4.0-67-generic、5.4.0-70-generic、4.15.0-1108-azure、4.15.0-1111-azure、5.4.0-1040-azure、5.4.0-1041-azure、5.4.0-1043-azure、4.15.0-1109-azure、4.15.0-1110-azure から 9.41 までのホット フィックス修正プログラム**|
18.04 LTS | [9.40](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.15.0-20-generic から 4.15.0-129-generic </br> 4.18.0-13-generic から 4.18.0-25-generic </br> 5.0.0-15-generic から 5.0.0-63-generic </br> 5.3.0-19-generic から 5.3.0-69-generic </br> 5.4.0-37-generic から 5.4.0-59-generic</br> 4.15.0-1009-azure から 4.15.0-1103-azure </br> 4.18.0-1006-azure から 4.18.0-1025-azure </br> 5.0.0-1012-azure から 5.0.0-1036-azure </br> 5.3.0-1007-azure から 5.3.0-1035-azure </br> 5.4.0-1020-azure から 5.4.0-1035-azure </br> 4.15.0-1104-azure、4.15.0-130-generic、4.15.0-132-generic、5.4.0-1036-azure、5.4.0-60-generic、5.4.0-62-generic、4.15.0-1106-azure、4.15.0-134-generic、4.15.0-135-generic、5.4.0-1039-azure、5.4.0-64-generic、5.4.0-65-generic から 9.40 までのホット フィックス修正プログラム**|
|||
20.04 LTS |[9.45](https://support.microsoft.com/topic/update-rollup-58-for-azure-site-recovery-kb5007075-37ba21c3-47d9-4ea9-9130-a7d64f517d5d) | 5.4.0-1058-azure </br> 5.4.0-84-generic </br> 5.4.0-1061-azure </br> 5.4.0-1062-azure </br> 5.4.0-89-generic |
20.04 LTS |[9.44](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) | 5.4.0-26-generic から 5.4.0-60-generic </br> 5.4.0-1010-azure から 5.4.0-1043-azure </br> 5.4.0-1047-azure </br> 5.4.0-73-generic </br> 5.4.0-1048-azure </br> 5.4.0-74-generic </br> 5.4.0-81-generic </br> 5.4.0-1056-azure |
20.04 LTS |[9.43](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) | 5.4.0-26-generic から 5.4.0-60-generic </br> 5.4.0-1010-azure から 5.4.0-1043-azure </br> 5.4.0-1047-azure </br> 5.4.0-73-generic </br> 5.4.0-1048-azure </br> 5.4.0-74-generic |
20.04 LTS |[9.42](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8)| 5.4.0-26-generic から 5.4.0-60-generic </br> 5.4.0-1010-azure から 5.4.0-1043-azure </br> 5.4.0-1047-azure </br> 5.4.0-73-generic </br> 5.4.0-1048-azure </br> 5.4.0-74-generic |
20.04 LTS |[9.41](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 5.4.0-26-generic から 5.4.0-65-generic </br> 5.4.0-1010-azure から 5.4.0-1039-azure </br> 5.4.0-66-generic、5.4.0-67-generic、5.4.0-70-generic、5.4.0-1040-azure、5.4.0-1041-azure、5.4.0-1043-azure から 9.41 までのホット フィックス修正プログラム**|
20.04 LTS |[9.40](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 5.4.0-26-generic から 5.4.0-59-generic </br> 5.4.0-1010-azure から 5.4.0-1035-azure </br> 5.4.0-1036-azure、5.4.0-60-generic、5.4.0-62-generic、5.4.0-1039-azure、5.4.0-64-generic、5.4.0-65-generic |

** 注: 最新の Linux カーネルをリリースから 15 日以内にサポートするため、Azure Site Recovery では最新のモビリティ エージェント バージョンに加えてホットフィックス修正プログラムがロールアウトされます。 この修正は、2 つのメジャー バージョンのリリースの間にロールアウトされます。 最新バージョンのモビリティ エージェント (ホットフィックス修正プログラムを含む) に更新するには、[こちらの記事](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)で示されている手順に従います。 この修正プログラムは、現在、Azure から Azure への DR シナリオで使用されるモビリティ エージェントに対してロールアウトされます。

**注: Ubuntu 20.04 では、最初にカーネル 5.8 のサポートをロールアウトしました。* ただし、このカーネルのサポートに関する問題が見つかりました。そのため、当面サポートに関する声明でこれらのカーネルは編集されています。

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Azure 仮想マシン用のサポートされる Debian カーネル バージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
Debian 7 | [9.40](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)、[9.41](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)、[9.42](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8)、[9.43](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6)、[9.44](https://support.microsoft.com/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 3.2.0-4-amd64 から 3.2.0-6-amd64、3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.40](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)、[9.41](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)、[9.42](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8)、[9.43](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6)、[9.44](https://support.microsoft.com/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 3.16.0-4-amd64 から 3.16.0-11-amd64、4.9.0-0.bpo.4-amd64 から 4.9.0-0.bpo.11-amd64 |
|||
Debian 9.1 | [9.45](https://support.microsoft.com/topic/update-rollup-58-for-azure-site-recovery-kb5007075-37ba21c3-47d9-4ea9-9130-a7d64f517d5d) | 4.19.0-0.bpo.18-amd64 </br> 4.19.0-0.bpo.18-cloud-amd64
Debian 9.1 | [9.44](https://support.microsoft.com/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 4.9.0-1-amd64 から 4.9.0-15-amd64 </br> 4.19.0-0.bpo.1-amd64 から 4.19.0-0.bpo.16-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 から 4.19.0-0.bpo.16-cloud-amd64
Debian 9.1 | [9.43](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) | 4.9.0-1-amd64 から 4.9.0-15-amd64 </br> 4.19.0-0.bpo.1-amd64 から 4.19.0-0.bpo.16-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 から 4.19.0-0.bpo.16-cloud-amd64
Debian 9.1 | [9.42](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8) | 4.9.0-1-amd64 から 4.9.0-15-amd64 </br> 4.19.0-0.bpo.1-amd64 から 4.19.0-0.bpo.16-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 から 4.19.0-0.bpo.16-cloud-amd64
Debian 9.1 | [9.41](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.9.0-1-amd64 から 4.9.0-14-amd64 </br> 4.19.0-0.bpo.1-amd64 から 4.19.0-0.bpo.14-amd64 </br> 4.19.0-0.bpo.1-cloud-amd64 から 4.19.0-0.bpo.14-cloud-amd64 </br> 4.9.0-15-amd64、4.19.0-0.bpo.16-amd64、4.19.0-0.bpo.16-cloud-amd64 から 9.41 までのホット フィックス修正プログラム**
|||
Debian 10 | [9.45](https://support.microsoft.com/topic/update-rollup-58-for-azure-site-recovery-kb5007075-37ba21c3-47d9-4ea9-9130-a7d64f517d5d) | 4.19.0-18-amd64 </br> 4.19.0-18-cloud-amd64
Debian 10 | [9.44](https://support.microsoft.com/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | 4.19.0-6-amd64 から 4.19.0-16-amd64 </br> 4.19.0-6-cloud-amd64 から 4.19.0-16-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64
Debian 10 | [9.43](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) | 4.19.0-6-amd64 から 4.19.0-16-amd64 </br> 4.19.0-6-cloud-amd64 から 4.19.0-16-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64
Debian 10 | [9.42](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8) | 4.19.0-6-amd64 から 4.19.0-16-amd64 </br> 4.19.0-6-cloud-amd64 から 4.19.0-16-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64
Debian 10 | [9.41](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.19.0-6-amd64 から 4.19.0-14-amd64 </br> 4.19.0-6-cloud-amd64 から 4.19.0-14-cloud-amd64 </br> 5.8.0-0.bpo.2-amd64 </br> 5.8.0-0.bpo.2-cloud-amd64 </br> 4.19.0-10-cloud-amd64、4.19.0-16-amd64、4.19.0-16-cloud-amd64 から 9.41 までのホット フィックス修正プログラム**

** 注: 最新の Linux カーネルをリリースから 15 日以内にサポートするため、Azure Site Recovery では最新のモビリティ エージェント バージョンに加えてホットフィックス修正プログラムがロールアウトされます。 この修正は、2 つのメジャー バージョンのリリースの間にロールアウトされます。 最新バージョンのモビリティ エージェント (ホットフィックス修正プログラムを含む) に更新するには、[こちらの記事](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)で示されている手順に従います。 この修正プログラムは、現在、Azure から Azure への DR シナリオで使用されるモビリティ エージェントに対してロールアウトされます。

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>Azure 仮想マシン用のサポートされる SUSE Linux Enterprise Server 12 カーネル バージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5) | [9.45](https://support.microsoft.com/topic/update-rollup-58-for-azure-site-recovery-kb5007075-37ba21c3-47d9-4ea9-9130-a7d64f517d5d) | すべての [SUSE 12 SP1、SP2,SP3、SP4、SP5 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.4.138-4.7-azure から 4.4.180-4.31-azure、</br>4.12.14-6.3-azure から 4.12.14-6.43-azure </br> 4.12.14-16.7-azure から 4.12.14-16.56-azure </br> 4.12.14-16.65-azure </br> 4.12.14-16.68-azure </br> 4.12.14-16.76-azure |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5) | [9.44](https://support.microsoft.com/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094) | すべての [SUSE 12 SP1、SP2,SP3、SP4、SP5 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.4.138-4.7-azure から 4.4.180-4.31-azure、</br>4.12.14-6.3-azure から 4.12.14-6.43-azure </br> 4.12.14-16.7-azure から 4.12.14-16.56-azure </br> 4.12.14-16.65-azure </br> 4.12.14-16.68-azure |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5) | [9.43](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6) | すべての [SUSE 12 SP1、SP2,SP3、SP4、SP5 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.4.138-4.7-azure から 4.4.180-4.31-azure、</br>4.12.14-6.3-azure から 4.12.14-6.43-azure </br> 4.12.14-16.7-azure から 4.12.14-16.56-azure </br> 4.12.14-16.65-azure |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5) | [9.42](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8) | すべての [SUSE 12 SP1、SP2,SP3、SP4、SP5 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.4.138-4.7-azure から 4.4.180-4.31-azure、</br>4.12.14-6.3-azure から 4.12.14-6.43-azure </br> 4.12.14-16.7-azure から 4.12.14-16.56-azure |
SUSE Linux Enterprise Server 12 (SP1、SP2、SP3、SP4、SP5) | [9.41](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | すべての [SUSE 12 SP1、SP2,SP3、SP4、SP5 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.4.138-4.7-azure から 4.4.180-4.31-azure、</br>4.12.14-6.3-azure から 4.12.14-6.43-azure </br> 4.12.14-16.7-azure から 4.12.14-16.44-azure </br> 4.12.14-16.47-azure から 9.41 までのホット フィックス修正プログラム**|

#### <a name="supported-suse-linux-enterprise-server-15-kernel-versions-for-azure-virtual-machines"></a>Azure 仮想マシン用のサポートされる SUSE Linux Enterprise Server 15 カーネル バージョン

**リリース** | **モビリティ サービス バージョン** | **カーネル バージョン** |
--- | --- | --- |
SUSE Linux Enterprise Server 15、SP1、SP2 | [9.45](https://support.microsoft.com/topic/update-rollup-58-for-azure-site-recovery-kb5007075-37ba21c3-47d9-4ea9-9130-a7d64f517d5d) | 既定で、すべての [SUSE 15、SP1、SP2 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure から 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure から 5.3.18-18.58-azure </br> 5.3.18-18.69-azure
SUSE Linux Enterprise Server 15、SP1、SP2 | [9.44](https://support.microsoft.com/topic/update-rollup-57-for-azure-site-recovery-kb5006172-9fccc879-6e0c-4dc8-9fec-e0600cf94094)  | 既定で、すべての [SUSE 15、SP1、SP2 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure から 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure から 5.3.18-18.58-azure
SUSE Linux Enterprise Server 15、SP1、SP2 | [9.43](https://support.microsoft.com/topic/update-rollup-56-for-azure-site-recovery-kb5005376-33f27950-1a07-43e5-bf40-4b380a270ef6)  | 既定で、すべての [SUSE 15、SP1、SP2 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure から 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure から 5.3.18-18.58-azure
SUSE Linux Enterprise Server 15、SP1、SP2 | [9.42](https://support.microsoft.com/topic/update-rollup-55-for-azure-site-recovery-kb5003408-b19c8190-5f88-43ea-85b1-d9e0cc5ca7e8)  | 既定で、すべての [SUSE 15、SP1、SP2 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure から 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure から 5.3.18-18.47-azure
SUSE Linux Enterprise Server 15、SP1、SP2 | [9.41](https://support.microsoft.com/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)  | 既定で、すべての [SUSE 15、SP1、SP2 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure から 4.12.14-8.55-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure から 5.3.18-18.35-azure </br> 5.3.18-18.38-azure から 9.41 までのホット フィックス修正プログラム**
SUSE Linux Enterprise Server 15、SP1、SP2 | [9.40](https://support.microsoft.com/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | 既定で、すべての [SUSE 15、SP1、SP2 ストック カーネル](https://www.suse.com/support/kb/doc/?id=000019587)がサポートされます。</br></br> 4.12.14-5.5-azure から 4.12.14-5.47-azure </br></br> 4.12.14-8.5-azure から 4.12.14-8.58-azure </br> 5.3.18-16-azure </br> 5.3.18-18.5-azure から 5.3.18-18.29-azure </br> 5.3.18-18.32-azure、4.12.14-8.58-azure から 9.40 までのホット フィックス修正プログラム**

** 注: 最新の Linux カーネルをリリースから 15 日以内にサポートするため、Azure Site Recovery では最新のモビリティ エージェント バージョンに加えてホットフィックス修正プログラムがロールアウトされます。 この修正は、2 つのメジャー バージョンのリリースの間にロールアウトされます。 最新バージョンのモビリティ エージェント (ホットフィックス修正プログラムを含む) に更新するには、[こちらの記事](service-updates-how-to.md#azure-vm-disaster-recovery-to-azure)で示されている手順に従います。 この修正プログラムは、現在、Azure から Azure への DR シナリオで使用されるモビリティ エージェントに対してロールアウトされます。

## <a name="replicated-machines---linux-file-systemguest-storage"></a>レプリケートされるマシン - Linux ファイル システム/ゲスト ストレージ

* ファイル システム: ext3、ext4、XFS、BTRFS
* ボリューム マネージャー:LVM2

> [!NOTE]
> マルチパス ソフトウェアはサポートされていません。


## <a name="replicated-machines---compute-settings"></a>レプリケートされるマシン - コンピューティングの設定

**設定** | **サポート** | **詳細**
--- | --- | ---
サイズ | 少なくとも 2 つの CPU コアと 1 GB の RAM を備えた任意の Azure VM サイズ | [Azure 仮想マシンのサイズ](../virtual-machines/sizes.md)を確認してください。
RAM | Azure Site Recovery のドライバーは、RAM の 6% を消費します。
可用性セット | サポートされています | 既定のオプションを使用して Azure VM のレプリケーションを有効にした場合は、ソース リージョンの設定に基づいて可用性セットが自動的に作成されます。 次の設定を変更できます。
可用性ゾーン | サポートされています |
Hybrid Use Benefit (HUB) | サポートされています | ソース VM で HUB ライセンスが有効になっている場合、テスト フェールオーバー VM またはフェールオーバーした VM でも HUB ライセンスが使用されます。
仮想マシン スケール セット | サポートされていません |
Azure ギャラリー イメージ - Microsoft が公開 | サポートされています | サポート対象のオペレーティング システムで VM が実行されている場合にサポートされます。
Azure ギャラリー イメージ - サード パーティが公開 | サポートされています | サポート対象のオペレーティング システムで VM が実行されている場合にサポートされます。
カスタム イメージ - サード パーティが公開 | サポートされています | サポート対象のオペレーティング システムで VM が実行されている場合にサポートされます。
Site Recovery を使用して移行された VM | サポートされています | Site Recovery を使用して Azure に移行された VMware VM または物理マシンの場合は、マシンで実行されている古いバージョンのモビリティ サービスをアンインストールし、マシンを再起動してから、それを他の Azure リージョンにレプリケートする必要があります。
Azure RBAC ポリシー | サポートされていません | VM での Azure ロールベースのアクセス制御 (Azure RBAC) ポリシーは、ターゲット リージョンのフェールオーバー VM にレプリケートされません。
拡張機能 | サポートされていません | 拡張機能は、ターゲット リージョン内のフェールオーバー VM にはレプリケートされません。 これはフェールオーバー後に手動でインストールする必要があります。
近接配置グループ | サポートされています | 近接配置グループ内にある仮想マシンは、Site Recovery を使用して保護できます。
Tags  | サポートされています | ソース仮想マシンに適用されたユーザー生成タグは、テスト フェールオーバーまたはフェールオーバー後にターゲット仮想マシンに引き継がれます。 VM 上のタグは、VM がターゲット リージョンに存在する限り、24 時間に 1 回レプリケートされます。


## <a name="replicated-machines---disk-actions"></a>レプリケートされるマシン - ディスクのアクション

**操作** | **詳細**
-- | ---
レプリケートされた VM のディスク サイズの変更 | ソース VM での、より大きなサイズへの変更はサポートされています。 ソース VM での、より小さなサイズへの変更はサポートされていません。 サイズ変更は、フェールオーバーの前に実行する必要があります。 レプリケーションを無効にしたり、もう一度有効にしたりする必要はありません。<br/><br/> フェールオーバー後にソース VM を変更した場合、変更はキャプチャされません。<br/><br/> フェールオーバー後に Azure VM のディスク サイズを変更した場合、変更は Site Recovery によってキャプチャされず、元の VM サイズにフェールバックされます。<br/><br/> 4 TB 以上にサイズ変更する場合は、[こちら](../virtual-machines/premium-storage-performance.md)のディスク キャッシュに関する Azure のガイダンスにご注意ください。 
レプリケートされた VM にディスクを追加する | サポートされています
保護されたディスクへのオフラインでの変更 | ディスクの接続を解除し、それらをオフラインで変更する場合、完全な再同期をトリガーする必要があります。
ディスク キャッシュ | 4 TiB 以上のディスクでは、ディスク キャッシュはサポートされていません。 複数のディスクが VM に接続されている場合、4 TiB 未満の各ディスクでは、キャッシュがサポートされます。 Azure ディスクのキャッシュ設定を変更すると、対象となるディスクをデタッチして再アタッチします。 オペレーティング システム ディスクの場合は、VM が再起動されます。 ディスク キャッシュの設定を変更する前に、この中断の影響を受ける可能性があるすべてのアプリケーションまたはサービスを停止します。 これらの推奨事項に従っていないと、データが破損する可能性があります。

## <a name="replicated-machines---storage"></a>レプリケートされるマシン - ストレージ

この表は、Azure VM の OS ディスク、データ ディスク、一時ディスクに対するサポートをまとめたものです。

- [マネージド ディスク](../virtual-machines/disks-scalability-targets.md)でのパフォーマンスの問題を回避するために、VM ディスクの制限とターゲットを監視することが重要です。
- 既定の設定でデプロイすると、Site Recovery によって、ソースの設定に基づいてディスクとストレージ アカウントが自動的に作成されます。
- カスタマイズする場合は、必ずガイドラインに従ってください。

**コンポーネント** | **サポート** | **詳細**
--- | --- | ---
OS ディスクの最大サイズ | 2048 GB | VM ディスクに関する[詳細を表示します](../virtual-machines/managed-disks-overview.md)。
一時ディスク | サポートされていません | 一時ディスクは常にレプリケーションから除外されます。<br/><br/> 一時ディスクに永続データを格納しないでください。 [詳細については、こちらを参照してください](../virtual-machines/managed-disks-overview.md)。
データ ディスクの最大サイズ | マネージド ディスクの場合は 32 TB<br></br>アンマネージド ディスクの場合は 4 TB|
データ ディスクの最小サイズ | 非管理対象ディスクの制限はありません。 マネージド ディスクの場合は 2 GB |
データ ディスクの最大数 | 最大 64 (特定の Azure VM サイズでのサポートに従います) | VM サイズに関する[詳細を表示します](../virtual-machines/sizes.md)。
データ ディスクの変更レート | Premium ストレージではディスクあたり最大 20 MBps。 Standard ストレージではディスクあたり最大 2 MBps。 | ディスクでの平均データ変更レートが継続的に最大値より高い場合、レプリケーションが追いつかなくなります。<br/><br/>  ただし、上限の超過が散発的である場合は、レプリケーションが追いつくことができます。しかし、復旧ポイントがわずかに遅延することもあります。
データ ディスク - Standard ストレージ アカウント | サポートされています |
データ ディスク - Premium ストレージ アカウント | サポートされています | VM のディスクが Premium ストレージ アカウントと Standard ストレージ アカウントに分散している場合は、ターゲット リージョンでのストレージ構成が確実に同じになるように、ディスクごとに異なるターゲット ストレージ アカウントを選択できます。
マネージド ディスク - Standard | Azure Site Recovery がサポートされている Azure リージョンでサポートされます。 |
マネージド ディスク - Premium | Azure Site Recovery がサポートされている Azure リージョンでサポートされます。 |
ディスク サブスクリプションの制限 | サブスクリプションあたり最大 3000 の保護されたディスク | ソースまたはターゲットのサブスクリプションの Azure Site Recovery で保護されたディスク数 (データと OS の両方) が 3000 以下であることを確認します。
Standard SSD | サポートされています |
冗長性 | LRS と GRS がサポートされます。<br/><br/> ZRS はサポートされていません。
クールおよびホット ストレージ | サポートされていません | VM ディスクは、クールおよびホット ストレージではサポートされません
記憶域スペース | サポートされています |
NVMe ストレージ インターフェイス | サポートされていません
ホストでの暗号化 | サポートされています | ホストでの暗号化を使用してエンドツーエンドでの暗号化で VM を作成する方法の[詳細については、こちらを参照してください](../virtual-machines/disks-enable-host-based-encryption-portal.md)。
保存時の暗号化 (SSE) | サポートされています | SSE はストレージ アカウントでの既定の設定です。
保存時の暗号化 (CMK) | サポートされています | マネージド ディスクでは、ソフトウェア キーと HSM キーの両方がサポートされています
保存時の二重暗号化 | サポートされています | [Windows](../virtual-machines/disk-encryption.md) および [Linux](../virtual-machines/disk-encryption.md) でサポートされているリージョンの詳細について参照してください
FIPS 暗号化 | サポートされていません
Windows OS 用 Azure Disk Encryption (ADE) | マネージド ディスクを使用する VM の場合にサポートされます。 | アンマネージド ディスクを使用する VM はサポートされていません。 <br/><br/> HSM で保護されたキーはサポートされていません。 <br/><br/> 1 つのディスク上の個々のボリュームの暗号化はサポートされていません。 |
Linux OS 用 Azure Disk Encryption (ADE) | マネージド ディスクを使用する VM の場合にサポートされます。 | アンマネージド ディスクを使用する VM はサポートされていません。 <br/><br/> HSM で保護されたキーはサポートされていません。 <br/><br/> 1 つのディスク上の個々のボリュームの暗号化はサポートされていません。 <br><br> レプリケーションの有効化に関する既知の問題。 [詳細情報。](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137) |
SAS キーのローテーション | サポートされていません | ストレージ アカウントの SAS キーがローテーションされている場合は、レプリケーションを無効にしてからもう一度有効にする必要があります。 |
ホスト キャッシュ | サポートされています
ホット アド    | サポートされています | マネージド ディスクを使用する VM では、レプリケートされた Azure VM に追加するデータ ディスクのレプリケーションの有効化がサポートされています。 <br/><br/> 一度に 1 つのディスクのみ Azure VM にホット アドできます。 複数のディスクの並列追加はサポートされていません。 |
ディスクのホット リムーブ    | サポートされていません | VM 上でデータ ディスクを削除する場合は、レプリケーションを無効にしてから、もう一度 VM に対してレプリケーションを有効にする必要があります。
ディスクの除外 | サポート。 [PowerShell](azure-to-azure-exclude-disks.md) を使用して構成する必要があります。 |    一時ディスクは既定で除外されます。
記憶域スペース ダイレクト  | クラッシュ整合性復旧ポイントに対してサポートされています。 アプリケーション整合性復旧ポイントはサポートされていません。 |
スケールアウト ファイル サーバー  | クラッシュ整合性復旧ポイントに対してサポートされています。 アプリケーション整合性復旧ポイントはサポートされていません。 |
DRBD | DRBD セットアップの一部であるディスクはサポートされていません。 |
LRS | サポートされています |
GRS | サポートされています |
RA-GRS | サポートされています |
ZRS | サポートされていません |
クールおよびホット ストレージ | サポートされていません | 仮想マシン ディスクは、クールおよびホット ストレージではサポートされません
仮想ネットワークの Azure Storage ファイアウォール  | サポートされています | ストレージ アカウントへの仮想ネットワーク アクセスを制限する場合は、[信頼できる Microsoft サービスを許可](../storage/common/storage-network-security.md#exceptions)を有効にします。
汎用目的 V2 ストレージ アカウント (ホット層とクール層の両方) | サポートされています | 汎用目的 V1 のストレージ アカウントに比べて、トランザクション コストが非常に大きくなります
第 2 世代 (UEFI ブート) | サポートされています
NVMe ディスク | サポートされていません
Azure 共有ディスク | サポートされていません
転送オプションのセキュリティ保護 | サポートされています
書き込みアクセラレータが有効になっているディスク | サポートされていません
Tags  | サポートされています | ユーザー生成タグは、24 時間ごとにレプリケートされます。
論理的な削除 | サポートされていません | 論理的な削除はサポートされていません。これをストレージ アカウントで有効にすると、コストが増加するためです。 ASR では、ログ ファイルの作成/削除が非常に頻繁に行われ、レプリケートによってコストが増加します。

>[!IMPORTANT]
> [マネージド ディスク](../virtual-machines/disks-scalability-targets.md)に関するパフォーマンスの問題を回避するために、VM ディスクのスケーラビリティとパフォーマンスのターゲットに従っていることを確認します。 既定の設定を使用する場合は、ソースの構成に基づいて、必要なディスクとストレージ アカウントが Site Recovery によって作成されます。 設定をカスタマイズして独自の設定を選択する場合は、ソース VM のディスクのスケーラビリティおよびパフォーマンスのターゲットに従います。

## <a name="limits-and-data-change-rates"></a>制限とデータ変化率

以下の表は、Site Recovery の制限をまとめたものです。

- これらの制限は、Microsoft のテストに基づいて公開されていますが、アプリケーション I/O として想定されるすべての組み合わせを網羅したものではありません。
- 実際の結果は、ご使用のアプリで発生するさまざまな I/O によって異なることが考えられます。
- ディスクごとのデータ チャーンと仮想マシンごとのデータ チャーンという考慮すべき 2 つの制限があります。
- 仮想マシンごとのデータ チャーンは、現在、サイズに関係なく 54 MB/秒に制限されています。

**ストレージ ターゲット** | **レプリケーション元の平均ディスク I/O** |**レプリケーション元ディスクの平均データ変更頻度** | **レプリケーション元ディスクの 1 日あたりのデータ変更頻度合計**
---|---|---|---
Standard Storage | 8 KB    | 2 MB/秒 | (ディスクあたり) 168 GB
Premium P10 または P15 ディスク | 8 KB    | 2 MB/秒 | (ディスクあたり) 168 GB
Premium P10 または P15 ディスク | 16 KB | 4 MB/秒 |    (ディスクあたり) 336 GB
Premium P10 または P15 ディスク | 32 KB 以上 | 8 MB/秒 | (ディスクあたり) 672 GB
Premium P20、P30、P40、または P50 ディスク | 8 KB    | 5 MB/s | (ディスクあたり) 421 GB
Premium P20、P30、P40、または P50 ディスク | 16 KB 以上 |20 MB/秒 | (ディスクあたり) 1,684 GB

## <a name="replicated-machines---networking"></a>レプリケートされるマシン - ネットワーク
**設定** | **サポート** | **詳細**
--- | --- | ---
NIC | 特定の Azure VM サイズでサポートされる最大数 | フェールオーバー中に VM が作成されるときには NIC が作成されます。<br/><br/> フェールオーバー VM 上の NIC 数は、レプリケーションが有効にされたときのソース VM 上の NIC 数によって決まります。 レプリケーションを有効にした後に NIC を追加または削除しても、フェールオーバー後、レプリケートされた VM 上にある NIC の数は影響を受けません。 <br/><br/> フェールオーバー後の NIC の順序は元の順序と同じであることが保証されないことにも注意してください。 <br/><br/> 組織の名前付け規則に基づいて、ターゲット リージョンの NIC の名前を変更できます。 NIC の名前変更は、PowerShell を使用して実行できます。
インターネット Load Balancer | サポートされていません | プライマリ リージョンでパブリック/インターネット ロード バランサーを設定できます。 しかし、パブリックおよびインターネットのロード バランサーは DR リージョンの Azure Site Recovery ではサポートされていません。
内部ロード バランサー | サポートされています | 復旧計画の Azure Automation スクリプトを使用して、構成済みロード バランサーを関連付けます。
パブリック IP アドレス | サポートされています | 既存のパブリック IP アドレスを NIC に関連付けます。 または、パブリック IP アドレスを作成し、復旧計画の Azure Automation スクリプトを使用して、それを NIC に関連付けます。
NIC 上の NSG | サポートされています | 復旧計画の Azure Automation スクリプトを使用して、NSG を NIC に関連付けます。
サブネット上の NSG | サポートされています | 復旧計画の Azure Automation スクリプトを使用して、NSG を サブネットに関連付けます。
予約済みの (静的) IP アドレス | サポートされています | ソース VM で NIC が静的 IP を使用していて、ターゲット サブネットで同じ IP アドレスを使用できる場合、そのアドレスはフェールオーバーした VM に割り当てられます。<br/><br/> ターゲット サブネットで同じ IP アドレスを使用できない場合は、そのサブネットで使用できる IP アドレスの 1 つが、その VM 用に予約されます。<br/><br/> **[レプリケートされたアイテム]**  >  **[設定]**  >  **[ネットワーク]**  >  **[ネットワーク インターフェイス]** で、固定 IP アドレスとサブネットを指定することもできます。
動的 IP アドレス | サポートされています | ソース VM で NIC に動的 IP アドレス指定が設定されている場合、フェールオーバーした VM の NIC も既定で動的になります。<br/><br/> 必要な場合は、これを固定 IP アドレスに変更できます。
複数の IP アドレス | サポートされています | 複数の IP アドレスが設定された NIC を持つ VM をフェールオーバーするときは、ソース リージョン内の NIC のプライマリ IP アドレスのみが既定で保持されます。 セカンダリ IP 構成をフェールオーバーするには、 **[ネットワーク]** ブレードに移動して、それらを構成します。
Traffic Manager     | サポートされています | トラフィックがソース リージョンのエンドポイントに定期的にルーティングされ、フェールオーバーの場合はターゲット リージョンのエンドポイントにルーティングされるように、Traffic Manager を事前に構成することができます。
Azure DNS | サポートされています |
[カスタム DNS]    | サポートされています |
非認証プロキシ | サポートされています | [詳細情報](./azure-to-azure-about-networking.md)
認証済みプロキシ | サポートされていません | VM が送信接続に認証済みプロキシを使用している場合は、Azure Site Recovery でレプリケートできません。
オンプレミスへの VPN サイト間接続<br/><br/>(ExpressRoute の有無)| サポートされています | Site Recovery トラフィックがオンプレミスにルーティングされないように、UDR と NSG が構成されていることを確認します。 [詳細情報](./azure-to-azure-about-networking.md)
VNet 間接続    | サポートされています | [詳細情報](./azure-to-azure-about-networking.md)
仮想ネットワーク サービス エンドポイント | サポートされています | ストレージ アカウントへの仮想ネットワーク アクセスを制限している場合は、信頼された Microsoft サービスがストレージ アカウントへのアクセスを許可されることを確認します。
Accelerated Networking | サポートされています | 高速ネットワークは、ソース VM で有効になっている必要があります。 [詳細については、こちらを参照してください](azure-vm-disaster-recovery-with-accelerated-networking.md)。
Palo Alto Network アプライアンス | サポートされていません | サード パーティ製のアプライアンスでは、多くの場合、仮想マシン内のプロバイダーによって課せられる制限があります。 Azure Site Recovery では、エージェント、拡張機能、送信接続を利用できるようにする必要があります。 ただし、アプライアンスでは、仮想マシン内で送信アクティビティを構成することはできません。
IPv6  | サポートされていません | IPv4 と IPv6 の両方を含む混合構成もサポートされていません。 Site Recovery 操作の前に、IPv6 の範囲のサブネットを解放してください。
Site Recovery サービスへの Private Link アクセス | サポートされています | [詳細情報](azure-to-azure-how-to-enable-replication-private-endpoints.md)
Tags  | サポートされています | NIC 上のユーザー生成タグは、24 時間ごとにレプリケートされます。



## <a name="next-steps"></a>次のステップ

- Azure VM のレプリケートに関する[ネットワークのガイダンス](./azure-to-azure-about-networking.md)を確認します。
- [Azure VM をレプリケートして](./azure-to-azure-quickstart.md)ディザスター リカバリーを展開します。
