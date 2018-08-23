---
title: Azure File Sync のデプロイの計画 | Microsoft Docs
description: Azure Files のデプロイを計画するときの考慮事項について説明します。
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: a98c8ac65de930eabcedea2a009769ed6d245216
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617194"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Azure File Sync のデプロイの計画
Azure File Sync を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を維持したまま Azure Files で組織のファイル共有を一元化できます。 Azure File Sync により、ご利用の Windows Server が Azure ファイル共有の高速キャッシュに変わります。 SMB、NFS、FTPS など、Windows Server 上で利用できるあらゆるプロトコルを使用して、データにローカルにアクセスできます。 キャッシュは、世界中にいくつでも必要に応じて設置することができます。

この記事では、Azure File Sync をデプロイする際の重要な考慮事項について説明します。 「[Azure Files のデプロイの計画](storage-files-planning.md)」も読むことをお勧めします。 

## <a name="azure-file-sync-terminology"></a>Azure File Sync の用語
Azure File Sync のデプロイの計画の詳細な説明に入る前に、用語を理解することが重要です。

### <a name="storage-sync-service"></a>ストレージ同期サービス
ストレージ同期サービスは、Azure File Sync の最上位レベルの Azure リソースです。ストレージ同期サービス リソースは、ストレージ アカウント リソースのピアであり、同じように Azure リソース グループにデプロイできます。 ストレージ アカウント リソースの個別の最上位レベルのリソースが必要です。ストレージ同期サービスは、複数の同期グループを介して複数のストレージ アカウントとの同期関係を作成する可能性があるためです。 1 つのサブスクリプションで、複数の ストレージ同期サービス リソースをデプロイすることができます。

### <a name="sync-group"></a>同期グループ
同期グループは、一連のファイルの同期トポロジを定義します。 同期グループ内のエンドポイントは、相互に同期を維持されます。 たとえば、Azure File Sync で管理するファイル セットが 2 組ある場合、2 つの同期グループを作成し、各同期グループに別のエンドポイントを追加します。 ストレージ同期サービスは、必要に応じて任意の数の同期グループをホストできます。  

### <a name="registered-server"></a>登録済みサーバー
登録済みサーバー オブジェクトは、サーバー (またはクラスター) とストレージ同期サービス間の信頼関係を表します。 ストレージ同期サービス インスタンスには、任意の数のサーバーを登録できます。 ただし、1 つのサーバー (またはクラスター) は、同時に 1 つのストレージ同期サービスのみに登録できます。

### <a name="azure-file-sync-agent"></a>Azure ファイル同期エージェント
Azure File Sync エージェントは、Windows Server を Azure ファイル共有と同期できるようにするダウンロード可能なパッケージです。 Azure File Sync エージェントには、3 つの主要コンポーネントがあります。 
- **FileSyncSvc.exe**: バックグラウンドの Windows サービス。サーバー エンドポイントの変更の監視と、Azure に対する同期セッションの開始を担当します。
- **StorageSync.sys**: Azure File Sync ファイル システム フィルターであり、Azure Files に対するファイルの階層化を担当します (クラウドの階層化が有効な場合)。
- **PowerShell 管理コマンドレット**: Microsoft.StorageSync Azure リソース プロバイダーと対話するために使用する PowerShell コマンドレット。 これらは、次の (既定の) 場所で見つけることができます。
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>サーバー エンドポイント
サーバー エンドポイントは、登録済みサーバー上の特定の場所を表します。たとえば、サーバー ボリュームのフォルダーなどです。 名前空間が重複していなければ、同じボリューム上に複数のサーバー エンドポイントが存在できます (たとえば `F:\sync1` と `F:\sync2`)。 各サーバー エンドポイントについて、クラウドの階層化ポリシーを個別に構成することができます。 

マウント ポイントを使用してサーバー エンドポイントを作成できます。 サーバー エンドポイント内のマウントポイントはスキップされることに注意してください。  

システム ボリュームでサーバー エンドポイントを作成することはできますが、その場合、次の 2 つの制限があります。
* クラウド階層化を有効にすることはできません。
* 名前空間の迅速な復元 (システムが名前空間全体をすばやく停止した後、起動してコンテンツを呼び戻す) は実行されません。


> [!Note]  
> 非リムーバブル ボリュームのみがサポートされます。  サーバー エンドポイントのパスでは、リモート共有からマップされたドライブはサポートされていません。  また、システム ボリュームでは、クラウド階層経由の Windows システム ボリューム上にあるサーバー エンドポイントはサポートされていません。

既存の一連のファイルを含むサーバーの場所をサーバー エンドポイントとして同期グループに追加すると、それらのファイルは、同期グループの他のエンドポイント上に既にある他のファイルと統合されます。

### <a name="cloud-endpoint"></a>クラウド エンドポイント
クラウド エンドポイントは、同期グループに含まれる Azure ファイル共有です。 Azure ファイル共有の同期全体と 1 つの Azure ファイル共有は、1 つのクラウド エンドポイントのみのメンバーになることができます。 したがって、Azure ファイル共有は、1 つの同期グループのみのメンバーになることができます。 既存の一連のファイルを含む Azure ファイル共有をクラウド エンドポイントとして同期グループに追加すると、既存のファイルは、同期グループの他のエンドポイント上に既にある他のファイルと統合されます。

> [!Important]  
> Azure File Sync は、Azure ファイル共有に対する直接的な変更をサポートします。 ただし、Azure ファイル共有に対して行われた変更は、まず Azure File Sync の変更検出ジョブによって認識される必要があります。 クラウド エンドポイントに対する変更検出ジョブは、24 時間に 1 回のみ起動されます。 さらに、REST プロトコルで Azure ファイル共有に対して行われた変更は、SMB の最終更新時刻を更新するものではなく、同期による変更とは見なされません。詳細については、「[Azure Files についてよく寄せられる質問 (FAQ)](storage-files-faq.md#afs-change-detection)」を参照してください。

### <a name="cloud-tiering"></a>クラウドの階層化 
クラウドの階層化は、サイズが 64 KiB より大きく、使用頻度やアクセス頻度が低いファイルを Azure Files に階層化できる、Azure File Sync のオプション機能です。 ファイルを階層化すると、Azure File Sync ファイル システム フィルター (StorageSync.sys) がローカルでファイルをポインターと置き換えるか、ポイントを再解析します。 再解析ポイントは Azure Files 内のファイルの URL を表します。 階層化されたファイルは NTFS 内に "オフライン" 属性が設定されるため、サード パーティ アプリケーションは階層化されたファイルを特定できます。 ユーザーが階層化されたファイルを開くと、Azure File Sync がファイル データを Azure Files からシームレスに再呼び出しします。ユーザーは、ファイルがシステムにローカルに保存されていないことを知る必要はありません。 この機能は、階層型ストレージ管理 (HSM) とも呼ばれます。

> [!Important]  
> クラウドの階層化は、Windows システム ボリューム上のサーバー エンドポイントではサポートされません。

## <a name="azure-file-sync-interoperability"></a>Azure File Sync の相互運用性 
このセクションでは、Windows Server の機能とロール、およびサード パーティ ソリューションとの Azure File Sync の相互運用性について説明します。

### <a name="supported-versions-of-windows-server"></a>サポートされている Windows Server のバージョン
現時点では、Azure File Sync では次の Windows Server バージョンがサポートされています。

| Version | サポートされている SKU | サポートされているデプロイ オプション |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter および Standard | フル (UI ありのサーバー) |
| Windows Server 2012 R2 | Datacenter および Standard | フル (UI ありのサーバー) |

Windows Server の今後のバージョンは、それらがリリースされたときに追加されます。 Windows の以前のバージョンは、ユーザーからのフィードバックに基づいて追加される場合があります。

> [!Important]  
> Azure File Sync で使用するすべてのサーバーは、Windows Update の最新の更新プログラムを使用して常に最新の状態を保つことをお勧めします。 

### <a name="file-system-features"></a>ファイル システムの機能
| Feature | サポートの状態 | メモ |
|---------|----------------|-------|
| アクセス制御リスト (ACL) | 完全にサポートされます | Windows ACL は、Azure File Sync に保持され、サーバー エンドポイント上の Windows Server によって適用されます。 クラウドでファイルに直接アクセスする場合、Azure Files は Windows ACL を (まだ) サポートしていません。 |
| ハード リンク | Skipped | |
| シンボリック リンク | Skipped | |
| マウント ポイント | 部分的にサポート | マウント ポイントがサーバー エンドポイントのルートである場合がありますが、マウント ポイントがサーバー エンドポイントの名前空間に含まれている場合、マウント ポイントはスキップされます。 |
| 接合 | Skipped | たとえば、分散ファイル システムの DfrsrPrivate フォルダーや DFSRoots フォルダーなどです。 |
| 再解析ポイント | Skipped | |
| NTFS 圧縮 | 完全にサポートされます | |
| スパース ファイル | 完全にサポートされます | スパース ファイルは同期されます (ブロックされません) が、完全なファイルとしてクラウドと同期されます。 クラウド内 (または他のサーバー上) のファイルの内容が変更され、変更がダウンロードされると、ファイルはスパースではなくなります。 |
| 代替データ ストリーム (ADS) | 保持されますが、同期されません | たとえば、ファイル分類インフラストラクチャによって作成された分類タグは同期されません。 各サーバー エンドポイント上のファイルに既存の分類タグは、変更されません。 |

> [!Note]  
> NTFS ボリュームのみがサポートされます。 ReFS、FAT、FAT32 などのファイル システムはサポートされていません。

### <a name="files-skipped"></a>スキップされるファイル
| ファイル/フォルダー | Note |
|-|-|
| Desktop.ini | システムに固有のファイル |
| ethumbs.db$ | サムネイル用の一時ファイル |
| ~$\*.\* | Office の一時ファイル |
| \*.tmp | 一時ファイル |
| \*.laccdb | Access データベースのロック ファイル|
| 635D02A9D91C401B97884B82B3BCDAEA.* | 内部の同期ファイル|
| \\System Volume Information | ボリュームに固有のフォルダー |
| $RECYCLE.BIN| フォルダー |
| \\SyncShareState | 同期用のフォルダー |

### <a name="failover-clustering"></a>フェールオーバー クラスタリング
Windows Server フェールオーバー クラスタリングは、Azure ファイル同期の "汎用ファイル サーバー" デプロイ オプションでサポートされています。 フェールオーバー クラスタリングは、"アプリケーション データ用のスケールアウト ファイル サーバー" (SOFS) またはクラスター共有ボリューム (CSV) ではサポートされていません。

> [!Note]  
> 同期が適切に機能するには、フェールオーバー クラスターのすべてのノードに Azure File Sync エージェントをインストールする必要があります。

### <a name="data-deduplication"></a>データ重複除去
クラウドの階層化が有効ではないボリュームでは、Azure File Sync は、そのボリュームでの Windows Server のデータ重複除去の有効化をサポートします。 現時点では、クラウドの階層化が有効な Azure File Sync とデータ重複除去間の相互運用性はサポートされていません。

### <a name="distributed-file-system-dfs"></a>分散ファイル システム (DFS)
[Azure File Sync エージェント 1.2](https://go.microsoft.com/fwlink/?linkid=864522) 以降の Azure File Sync は、DFS 名前空間 (DFS-N) および DFS レプリケーション (DFS-R) との相互運用をサポートします。

**DFS 名前空間 (DFS-N)**: Azure File Sync は DFS-N サーバーで完全にサポートされます。 1 つまたは複数の DFS-N メンバーに Azure File Sync をインストールして、サーバー エンドポイントとクラウド エンドポイントの間でデータを同期できます。 詳しくは、「[DFS 名前空間の概要](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)」をご覧ください。
 
**DFS レプリケーション (DFS-R)**: DFS-R と Azure File Sync はどちらもレプリケーション ソリューションなので、ほとんどの場合は、DFS-R を Azure File Sync に置き換えることをお勧めします。ただし、DFS-R と Azure File Sync を併用するのが望ましいシナリオがいくつかあります。

- DFS-R のデプロイから Azure File Sync のデプロイに移行しているとき。 詳しくは、「[DFS レプリケーション (DFS-R) のデプロイを Azure File Sync に移行する](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)」をご覧ください。
- ファイル データのコピーが必要なオンプレミスのサーバーの中に、インターネットに直接接続することができないものが含まれる場合。
- ブランチ サーバーが単一のハブ サーバーにデータを統合しており、それに対して Azure File Sync を使いたい場合。

Azure File Sync と DFS-R を併用するには、次のことが必要です。

1. DFS-R でレプリケートされるフォルダーを含むボリュームで、Azure File Sync のクラウドの階層化を無効にする必要があります。
2. DFS-R の読み取り専用レプリケーション フォルダーには、サーバー エンドポイントを構成しないようにする必要があります。

詳しくは、[DFS レプリケーションの概要](https://technet.microsoft.com/library/jj127250)に関するページをご覧ください。

### <a name="sysprep"></a>Sysprep
Azure File Sync エージェントがインストールされているサーバー上での sysprep の使用はサポートされていません。予期しない結果になる可能性があります。 エージェントのインストールとサーバーの登録は、サーバー イメージの展開と sysprep ミニ セットアップの完了後に行われます。

### <a name="windows-search"></a>Windows Search
クラウドの階層化がサーバー エンドポイントで有効になっている場合、階層化されたファイルはスキップされ、Windows Search によってインデックスが付けられます。 階層化されていないファイルは適切にインデックスが付けられます。

### <a name="antivirus-solutions"></a>ウイルス対策ソリューション
ウイルス対策は、ファイルをスキャンして既知の悪意のあるコードを検索することで機能するので、ウイルス対策製品によって階層化されたファイルの再呼び出しが発生することがあります。 階層化されたファイルには "オフライン" 属性が設定されているため、オフライン ファイルの読み取りをスキップするようにソリューションを構成する方法について、ソフトウェア ベンダーと相談することをお勧めします。 

次のソリューションは、オフライン ファイルのスキップをサポートすることが確認されています。

- [Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus)
    - Windows Defender は、オフラインの属性が設定されているファイルの読み取りを自動的にスキップします。 Defender をテストした結果、小さな問題点がわかりました。既存の同期グループにサーバーを追加すると、新しいサーバーで 800 バイト未満のファイルが再呼び出される (ダウンロードされる) という問題です。 このようなファイルは新しいサーバー上に残り、階層化のサイズ要件 (64 KB を超えるサイズ) を満たしていないため、階層化されません。
- [System Center Endpoint Protection (SCEP)](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus)
    - SCEP は Defender と同様に動作します。前述の説明を参照してください。
- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee EndPoint Security](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (この PDF の 90 ページの「Scan only what you need to」(必要なファイルのみをスキャンする) セクションを参照してください)
- [Kaspersky Anti-Virus](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>バックアップ ソリューション
ウイルス対策ソリューションと同様に、バックアップ ソリューションでも階層化されたファイルの再呼び出しが発生することがあります。 Azure ファイル共有をバックアップするには、オンプレミスのバックアップ製品ではなく、クラウド バックアップ ソリューションを使用することをお勧めします。

オンプレミス バックアップ ソリューションを使用している場合、クラウドの階層化が無効な同期グループ内のサーバーに対してバックアップを実行する必要があります。 サーバー エンドポイント内にあるファイルを復元するときは、ファイル レベルの復元オプションを使用してください。 復元されたファイルは、同期グループ内のすべてのエンドポイントに同期され、既存のファイルはバックアップから復元されたバージョンに置き換えられます。

> [!Note]  
> アプリケーションに対応した、ボリュームレベルおよびベアメタル (BMR) 復元オプションは予期しない結果を引き起こす可能性があるため、現在サポートされていません。 これらの復元オプションは、今後のリリースでサポートされる予定です。

### <a name="encryption-solutions"></a>暗号化ソリューション
暗号化ソリューションのサポートは、実装方法によって変わります。 Azure ファイル同期は次のソリューションと連携することが確認されています。

- BitLocker 暗号化
- Azure Information Protection、Azure Rights Management サービス (Azure RMS)、および Active Directory RMS

Azure ファイル同期は、次のソリューションと連携しないことが確認されています。

- NTFS 暗号化ファイル システム (EFS)

通常、Azure File Sync は、ファイル システムの下に位置する暗号化ソリューション (BitLocker など) と、ファイル形式で実装されるソリューション (Azure Information Protection など) との相互運用性をサポートします。 ファイル システムの上に位置するソリューション (NTFS EFS など) に対する特別な相互運用性はありません。

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>その他の階層型ストレージ管理 (HSM) ソリューション
その他の HSM ソリューションを Azure File Sync で使用することはできません。

## <a name="region-availability"></a>利用可能なリージョン
Azure File Sync は、次のリージョンでのみ利用できます。

| リージョン | データ センターの場所 |
|--------|---------------------|
| オーストラリア東部 | ニュー サウス ウェールズ州 |
| オーストラリア南東部 | ビクトリア州 |
| カナダ中部 | トロント |
| カナダ東部 | ケベック シティ |
| インド中部 | プネー |
| 米国中央部 | アイオワ州 |
| 東アジア | 香港特別行政区 |
| 米国東部 | バージニア州 |
| 米国東部 2 | バージニア州 |
| 北ヨーロッパ | アイルランド |
| インド南部 | チェンナイ |
| 東南アジア | シンガポール |
| 英国南部 | ロンドン |
| 英国西部 | カーディフ |
| 西ヨーロッパ | オランダ |
| 米国西部 | カリフォルニア |

Azure File Sync は、ストレージ同期サービスと同じリージョンの Azure ファイル共有との同期のみをサポートしています。

### <a name="azure-disaster-recovery"></a>Azure ディザスター リカバリー
Azure リージョンの損失を防ぐため、Azure File Sync には [geo 冗長ストレージの冗長性](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (GRS) オプションが統合されています。 GRS ストレージは、プライマリ リージョンのストレージ (通常、操作している) と、ペアのセカンダリ リージョンとの間でブロックの非同期レプリケーションを使用することで機能します。 Azure リージョンが一時的または永続的にオフラインになる障害が発生した場合、Microsoft はペアのリージョンにストレージをフェールオーバーします。 

geo 冗長ストレージと Azure File Sync との間のフェールオーバーの統合をサポートするため、すべての Azure File Sync リージョンが、ストレージで使用されるセカンダリ リージョンと一致するセカンダリ リージョンとペアになります。 これらのペアは次のとおりです。

| プライマリ リージョン      | ペアのリージョン      |
|---------------------|--------------------|
| オーストラリア東部      | オーストラリア南東部 |
| オーストラリア南東部 | オーストラリア東部     |
| カナダ中部      | カナダ東部        |
| カナダ東部         | カナダ中部     |
| インド中部       | インド南部        |
| 米国中央部          | 米国東部 2          |
| 東アジア           | 東南アジア     |
| 米国東部             | 米国西部            |
| 米国東部 2           | 米国中央部         |
| 北ヨーロッパ        | 西ヨーロッパ        |
| インド南部         | インド中部      |
| 東南アジア      | 東アジア          |
| 英国南部            | 英国西部            |
| 英国西部             | 英国南部           |
| 西ヨーロッパ         | 北ヨーロッパ       |
| 米国西部             | 米国東部            |

## <a name="azure-file-sync-agent-update-policy"></a>Azure ファイル同期エージェントの更新ポリシー
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>次の手順
* [ファイアウォールとプロキシの設定の考慮事項](storage-sync-files-firewall-and-proxy.md)
* [Azure Files のデプロイの計画](storage-files-planning.md)
* [Azure Files をデプロイする](storage-files-deployment-guide.md)
* [Azure File Sync をデプロイする](storage-sync-files-deployment-guide.md)
