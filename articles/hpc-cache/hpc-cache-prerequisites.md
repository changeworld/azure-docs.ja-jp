---
title: Azure HPC Cache の前提条件
description: Azure HPC Cache を使用するための前提条件
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 11/03/2021
ms.author: rohogue
ms.openlocfilehash: 1929677370ddf6f505f2425f61bfdaf4466223bb
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137759"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Azure HPC Cache の前提条件

Azure portal を使用して新しい Azure HPC Cache を作成する前に、ご利用の環境が要件を満たしていることを確認します。

## <a name="video-overviews"></a>ビデオの概要

これらのビデオを見ると、システムのコンポーネントの概要と、それらの連携に必要なものを理解できます

(ビデオ画像またはリンクをクリックすると見ることができます)。

* [しくみ](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) - Azure HPC Cache がストレージおよびクライアントとどのように連携するかについて説明します

  [![ビデオのサムネイル画像:Azure HPC Cache:しくみ (クリックするとビデオ ページに移動します)](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [前提条件](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) - NAS ストレージ、Azure Blob Storage、ネットワーク アクセス、およびクライアント アクセスの要件について説明します

  [![ビデオのサムネイル画像:Azure HPC Cache:前提条件 (クリックするとビデオ ページに移動します)](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

具体的な推奨事項については、この記事の残りの部分を参照してください。

## <a name="azure-subscription"></a>Azure サブスクリプション

有料サブスクリプションをお勧めします。

## <a name="network-infrastructure"></a>ネットワーク インフラストラクチャ

キャッシュを使用するには、ネットワークに関連した次の 2 つの前提条件を事前に設定しておく必要があります。

* Azure HPC Cache インスタンスの専用サブネット
* DNS サポート (キャッシュがストレージや他のリソースにアクセスできるようにするため)

### <a name="cache-subnet"></a>キャッシュのサブネット

Azure HPC Cache には、次の条件を満たした専用のサブネットが必要です。

* サブネットには、使用できる IP アドレスが少なくとも 64 個必要です。
* そのサブネットで他の VM をホストすることはできません。クライアント マシンなど、関連サービス用の VM であっても同様です。
* 複数の Azure HPC Cache インスタンスを使用する場合、各インスタンスに独自のサブネットが必要です。

ベスト プラクティスとして、キャッシュごとに新しいサブネットを作成することをお勧めします。 キャッシュを作成する過程で新しい仮想ネットワークとサブネットを作成できます。

### <a name="dns-access"></a>DNS アクセス

キャッシュからその仮想ネットワークの外部のリソースにアクセスするためには、DNS が必要です。 使用するリソースによっては、カスタマイズした DNS サーバーをセットアップし、そのサーバーと Azure DNS サーバーとの間の転送を構成する必要があります。

* Azure Blob Storage のエンドポイントなど内部のリソースにアクセスするには、Azure ベースの DNS サーバーが必要です。
* オンプレミスのストレージにアクセスするためには、ストレージのホスト名を解決できるカスタム DNS サーバーを構成する必要があります。 キャッシュを作成する前に、これを実行する必要があります。

Blob Storage だけを使用するのであれば、Azure に用意されている既定の DNS サーバーをキャッシュに使用できます。 一方、Azure 外部のストレージまたは他のリソースにアクセスする必要がある場合は、カスタム DNS サーバーを作成し、Azure 固有の解決要求は Azure DNS サーバーに転送するようそのカスタム DNS サーバーを構成する必要があります。

カスタム DNS サーバーを使用するには、キャッシュを作成する前に、次のセットアップ手順を行う必要があります。

* Azure HPC Cache がホストされる仮想ネットワークを作成します。
* DNS サーバーを作成します。
* DNS サーバーをキャッシュの仮想ネットワークに追加します。

  Azure portal の仮想ネットワークに DNS サーバーを追加するには、次の手順に従います。

  1. Azure portal で仮想ネットワークを開きます。
  1. サイドバーの [設定] メニューで [DNS サーバー] を選択します。
  1. [カスタム] を選択します。
  1. フィールドに DNS サーバーの IP アドレスを入力します。

単純な DNS サーバーを使用して、利用可能なすべてのキャッシュ マウント ポイント間でクライアント接続を負荷分散することもできます。

Azure 仮想ネットワークと DNS サーバーの構成について詳しくは、「[Azure 仮想ネットワーク内のリソースの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)」を参照してください。

### <a name="ntp-access"></a>NTP アクセス

HPC Cache は、通常の運用のために NTP サーバーへのアクセスを必要とします。 仮想ネットワークからの送信トラフィックを制限する場合は、少なくとも 1 つの NTP サーバーへのトラフィックを許可してください。 既定のサーバーは time.windows.com で、キャッシュはこのサーバーに UDP ポート 123 で接続します。

NTP サーバーへの送信トラフィックを許可するルールをキャッシュネットワークの[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)に作成します。 この規則では、単に UDP ポート 123 のすべての送信トラフィックを許可するか、またはより多くの制限を設定することができます。

この例では、IP アドレス 168.61.215.74 への送信トラフィックを明示的に開きます。これは、time.windows.com によって使用されるアドレスです。

| Priority | 名前 | Port | Protocol | source | 到着地   | アクション |
|----------|------|------|----------|--------|---------------|--------|
| 200      | NTP  | Any  | UDP      | Any    | 168.61.215.74 | Allow  |

NTP ルールの優先順位が、送信アクセスを幅広く拒否するルールよりも高いことを確認します。

NTP アクセスに関するその他のヒント:

* HPC Cache と NTP サーバーの間にファイアウォールがある場合は、これらのファイアウォールでも NTP アクセスが許可されていることを確認してください。

* **ネットワーク** ページで、HPC Cache で使用する NTP サーバーを構成できます。 詳細については、「[追加の設定の構成](configuration.md#customize-ntp)」を参照してください。

## <a name="permissions"></a>アクセス許可

アクセス許可に関連したこれらの前提条件は、キャッシュの作成を始める前に確認しておいてください。

* キャッシュ インスタンスで仮想ネットワーク インターフェイス (NIC) を作成できる必要があります。 キャッシュを作成するユーザーは、NIC を作成できるだけの権限をサブスクリプションにおいて有している必要があります。

* Blob Storage を使用する場合、Azure HPC Cache には、ストレージ アカウントにアクセスするための承認が必要です。 Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、Blob Storage へのアクセス権をキャッシュに与えます。 2 つのロールが必要です。ストレージ アカウント共同作成者とストレージ BLOB データ共同作成者です。

  ロールを追加するには、[ストレージ ターゲットを追加](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)する手順に従ってください。

## <a name="storage-infrastructure"></a>ストレージ インフラストラクチャ
<!-- heading is linked in create storage target GUI as aka.ms/hpc-cache-prereq#storage-infrastructure - make sure to fix that if you change the wording of this heading -->

キャッシュは、Azure BLOB コンテナー、NFS ハードウェア ストレージのエクスポート、および NFS でマウントされた ADLS BLOB コンテナーをサポートしています。 キャッシュを作成した後で、ストレージ ターゲットを追加します。

キャッシュのサイズによって、サポート可能なストレージ ターゲットの数が決まります。ほとんどのキャッシュで最大で 10 個のストレージ ターゲット、最大のサイズで最大で 20 個になります。 詳しくは、「[ストレージ ターゲットをサポートするようキャッシュを正しいサイズに設定する](hpc-cache-add-storage.md#size-your-cache-correctly-to-support-your-storage-targets)」をご覧ください。

ストレージの種類ごとに特定の前提条件があります。

### <a name="blob-storage-requirements"></a>Blob Storage の要件

お使いのキャッシュで Azure Blob Storage の使用を希望する場合、互換性のあるストレージ アカウントに加え、空の BLOB コンテナーか、Azure HPC Cache フォーマットのデータが事前設定されたコンテナーが必要です ([Azure Blob Storage にデータを移動する](hpc-cache-ingest.md)方法を参照)。

> [!NOTE]
> NFS でマウントされた Blob Storage には、異なる要件が適用されます。 詳細については、[ADLS-NFS ストレージの要件](#nfs-mounted-blob-adls-nfs-storage-requirements)に関するセクションを参照してください。

ストレージ ターゲットを追加する前にアカウントを作成してください。 ターゲットを追加するときに、新しいコンテナーを作成できます。

互換性のあるストレージ アカウントを作成するには、次のいずれかの組み合わせを使用します。

| パフォーマンス | Type | レプリケーション | アクセス層 |
|--|--|--|--|
| Standard | StorageV2 (汎用 v2)| ローカル冗長ストレージ (LRS) またはゾーン冗長ストレージ (ZRS) | ホット |
| Premium | ブロック blob | ローカル冗長ストレージ (LRS) | ホット |

ストレージ アカウントは、キャッシュのプライベート サブネットからアクセスできる必要があります。 アカウントでプライベート エンドポイントまたは特定の仮想ネットワークに制限されているパブリック エンドポイントを使用している場合は、キャッシュのサブネットからのアクセスを有効にする必要があります。 (オープンなパブリック エンドポイントは推奨されません。)

キャッシュと同じ Azure リージョンにあるストレージ アカウントの使用をお勧めします。

また、「[アクセス許可](#permissions)」で前述のように、キャッシュ アプリケーションには、Azure Storage アカウントへのアクセス権を与える必要があります。 「[ストレージ ターゲットの追加](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)」の手順に従い、必要なアクセス ロールをキャッシュに与えます。 ストレージ アカウント所有者ではない場合は、この手順を所有者に依頼してください。

### <a name="nfs-storage-requirements"></a>NFS ストレージの要件
<!-- linked from configuration.md and add storage -->

NFS ストレージ システム (たとえば、オンプレミスのハードウェア NAS システム) を使用している場合は、次の要件を満たしていることを確認します。 これらの設定を確認するには、ストレージ システム (またはデータ センター) のネットワーク管理者またはファイアウォール マネージャーとの協力が必要な場合があります。

> [!NOTE]
> キャッシュが NFS ストレージ システムに対して十分なアクセス権を持っていない場合、ストレージ ターゲットの作成は失敗します。

詳細については「 [NAS 構成と NFS ストレージ ターゲットの問題のトラブルシューティング](troubleshoot-nas.md) 」をご参照ください。

* ネットワーク接続: Azure HPC Cache には、キャッシュ サブネットと NFS システムのデータ センター間の高帯域幅ネットワーク アクセスが必要です。 [ExpressRoute](../expressroute/index.yml) または同様のアクセスが推奨されます。 VPN を使用している場合は、TCP MSS を 1350 でクランプして大きなパケットがブロックされないように構成する必要があります。 VPN 設定のトラブルシューティングについて詳しくは、[VPN のパケット サイズの制限](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions)に関する追加ヘルプをご覧ください。

* ポート アクセス: キャッシュには、ストレージ システム上の特定の TCP/UDP ポートへのアクセスが必要です。 ストレージの種類によってポート要件は異なります。

  ストレージ システムの設定を確認するには、次の手順を実行します。

  * ストレージ システムに `rpcinfo` コマンドを発行して、必要なポートを確認します。 次のコマンドを使って、ポートを一覧表示し、関連する結果の書式を設定します ( *<storage_IP>* という表現の部分にはシステムの IP アドレスを使用してください)。

    このコマンドは、NFS インフラストラクチャがインストールされている任意の Linux クライアントから発行できます。 クラスター サブネット内のクライアントを使用する場合は、サブネットとストレージ システム間の接続を検証するためにも役立ちます。

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  ``rpcinfo`` クエリによって返されるすべてのポートで、Azure HPC Cache のサブネットからの無制限のトラフィックが許可されていることを確認します。

  * `rpcinfo` コマンドを使用できない場合は、次の一般的に使用されるポートで受信および送信トラフィックが許可されていることを確認します。

    | Protocol | Port  | サービス  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | mountd   |
    | TCP/UDP  | 4047  | status   |

    一部のシステムでは、これらのサービスに別のポート番号を使用しています。確認するには、ストレージ システムのドキュメントを参照してください。

  * ファイアウォール設定で、これらのすべての必要なポートでトラフィックが許可されていることを確認します。 Azure で使用されているファイアウォールとデータ センターのオンプレミス ファイアウォールを必ず確認してください。

* ルート アクセス (読み取り/書き込み): キャッシュからは、ユーザー ID 0 としてバックエンド システムに接続されます。 ストレージ システムで次の設定を確認します。
  
  * `no_root_squash`を有効にする: このオプションを選択すると、リモート ルート ユーザーは、ルートによって所有されているファイルにアクセスできるようになります。

  * エクスポート ポリシーをチェックして、キャッシュのサブネットからのルート アクセスに制限がないことを確認してください。

  * ストレージに別のエクスポートのサブディレクトリであるエクスポートがある場合、キャッシュにパスの最下位セグメントへのルート アクセスがあることをご確認ください。 詳細については、NFS ストレージ ターゲットのトラブルシューティングに関するアーティクルに記載されている[ディレクトリ パスのルートアクセス](troubleshoot-nas.md#allow-root-access-on-directory-paths) をご参照ください。

* NFS バックエンド ストレージは、互換性のあるハードウェア プラットフォームおよびソフトウェア プラットフォームである必要があります。 詳細については、Azure HPC Cache チームにお問い合わせください。

### <a name="nfs-mounted-blob-adls-nfs-storage-requirements"></a>NFS でマウントされた Blob (ADLS-NFS) Storage の要件

Azure HPC Cache では、NFS プロトコルでマウントされた BLOB コンテナーをストレージ ターゲットとして使用することもできます。

この機能の詳細については、[Azure Blob Storage での NFS 3.0 プロトコルのサポート](../storage/blobs/network-file-system-protocol-support.md)に関するページを参照してください。

ストレージ アカウントの要件は、ADLS-NFS Blob Storage ターゲットと標準 Blob Storage ターゲットで異なります。 [ネットワーク ファイル システム (NFS) 3.0 プロトコルを使用した Blob Storage のマウント](../storage/blobs/network-file-system-protocol-support-how-to.md)に関するページの手順に従って、NFS 対応のストレージ アカウントを作成および構成します。

ここでは、手順の概要について説明します。 これらの手順は変更される可能性があるため、現在の詳細については、常に [ADLS-NFS の手順](../storage/blobs/network-file-system-protocol-support-how-to.md)に関するページを参照してください。

1. 必要な機能が、作業を予定しているリージョンで利用できることを確認します。

1. サブスクリプションに対して NFS プロトコル機能を有効にします。 ストレージ アカウントを作成する *前に*、この手順を実行します。

1. ストレージ アカウント用のセキュリティで保護された仮想ネットワーク (VNet) を作成します。 NFS が有効なストレージ アカウントと Azure HPC Cache には、同じ仮想ネットワークを使用する必要があります。 (キャッシュと同じサブネットは使用しないでください。)

1. ストレージ アカウントを作成します。

   * 標準 Blob Storage アカウント用のストレージ アカウント設定を使用する代わりに、[操作方法に関するドキュメント](../storage/blobs/network-file-system-protocol-support-how-to.md)に記載されている手順に従ってください。 サポートされているストレージ アカウントの種類は、Azure リージョンによって異なる場合があります。

   * [ネットワーク] セクションで、作成したセキュリティで保護された仮想ネットワーク内のプライベート エンドポイントを選択します (推奨)。または、セキュリティで保護された VNet からのアクセスが制限されているパブリック エンドポイントを選択します。

   * NFS アクセスを有効にする場合は、必ず [詳細設定] セクションを完了してください。

   * 「[アクセス許可](#permissions)」で前述のように、キャッシュ アプリケーションには、Azure Storage アカウントへのアクセス権を与えます。 ストレージ ターゲットを初めて作成するときに、この操作を行うことができます。 「[ストレージ ターゲットの追加](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)」の手順に従い、必要なアクセス ロールをキャッシュに与えます。

     ストレージ アカウント所有者ではない場合は、この手順を所有者に依頼してください。

Azure HPC Cache での ADLS-NFS ストレージ ターゲットの使用の詳細については、[Azure HPC Cache での NFS でマウントされた BLOB ストレージの使用](nfs-blob-considerations.md)に関する記事を参照してください。

## <a name="set-up-azure-cli-access-optional"></a>Azure CLI アクセスを設定する (省略可能)

Azure コマンドライン インターフェイス (Azure CLI) から Azure HPC Cache を作成または管理する場合は、CLI ソフトウェアと HPC キャッシュ拡張機能をインストールする必要があります。 「[Azure HPC Cache 向けに Azure CLI を設定する](az-cli-prerequisites.md)」の手順に従います。

## <a name="next-steps"></a>次のステップ

* Azure portal から [Azure HPC Cache インスタンスを作成](hpc-cache-create.md)する
