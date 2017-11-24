---
title: "Azure File Sync (プレビュー) のデプロイの計画 | Microsoft Docs"
description: "Azure Files のデプロイを計画するときの考慮事項について説明します。"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 241b744f5c5e89f53addb4d41d732245d76ef9a3
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2017
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>Azure File Sync (プレビュー) のデプロイの計画
Azure File Sync (プレビュー) を使用して、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を維持したまま、Azure Files で組織のファイル共有を一元化します。 Azure File Sync により、ご利用の Windows Server が Azure ファイル共有の高速キャッシュに変わります。 SMB、NFS、FTPS など、Windows Server 上で利用できるあらゆるプロトコルを使用して、データにローカルにアクセスできます。 キャッシュは、世界中にいくつでも必要に応じて設置することができます。

この記事では、Azure File Sync をデプロイする際の重要な考慮事項について説明します。 「[Azure Files のデプロイの計画](storage-files-planning.md)」も読むことをお勧めします。 

## <a name="azure-file-sync-terminology"></a>Azure File Sync の用語
Azure File Sync のデプロイの計画の詳細な説明に入る前に、用語を理解することが重要です。

### <a name="storage-sync-service"></a>ストレージ同期サービス
ストレージ同期サービスは、Azure File Sync の最上位レベルの Azure リソースです。ストレージ同期サービス リソースは、ストレージ アカウント リソースのピアであり、同じように Azure リソース グループにデプロイできます。 ストレージ アカウント リソースの個別の最上位レベルのリソースが必要です。ストレージ同期サービスは、複数の同期グループを介して複数のストレージ アカウントとの同期関係を作成する可能性があるためです。 1 つのサブスクリプションで、複数の ストレージ同期サービス リソースをデプロイすることができます。

### <a name="sync-group"></a>同期グループ
同期グループは、一連のファイルの同期トポロジを定義します。 同期グループ内のエンドポイントは、相互に同期状態が維持されます。 たとえば、Azure File Sync で管理する一連のファイルが 2 組ある場合、2 つの同期グループを作成し、各同期グループに別のエンドポイントを追加します。 ストレージ同期サービスは、必要に応じて任意の数の同期グループをホストできます。  

### <a name="registered-server"></a>登録済みサーバー
登録済みサーバー オブジェクトは、サーバー (またはクラスター) とストレージ同期サービス間の信頼関係を表します。 ストレージ同期サービス インスタンスには、任意の数のサーバーを登録できます。 ただし、1 つのサーバー (またはクラスター) は、同時に 1 つのストレージ同期サービスのみに登録できます。

### <a name="azure-file-sync-agent"></a>Azure File Sync エージェント
Azure File Sync エージェントは、Windows Server を Azure ファイル共有と同期できるようにするダウンロード可能なパッケージです。 Azure File Sync エージェントには、3 つの主要コンポーネントがあります。 
- **FileSyncSvc.exe**: バックグラウンドの Windows サービス。サーバー エンドポイントの変更の監視と、Azure に対する同期セッションの開始を担当します。
- **StorageSync.sys**: Azure File Sync ファイル システム フィルターであり、Azure Files に対するファイルの階層化を担当します (クラウドの階層化が有効な場合)。
- **PowerShell 管理コマンドレット**: Microsoft.StorageSync Azure リソース プロバイダーと対話するために使用する PowerShell コマンドレット。 これらは、次の (既定の) 場所で見つけることができます。
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>サーバー エンドポイント
サーバー エンドポイントは、登録済みサーバー上の特定の場所を表します。たとえば、サーバー ボリュームのフォルダーや、ボリュームのルートなどです。 名前空間が重複していなければ、同じボリューム上に複数のサーバー エンドポイントが存在することが可能です (たとえば F:\sync1 と F:\sync2)。 各サーバー エンドポイントについて、クラウドの階層化ポリシーを個別に構成することができます。 既存の一連のファイルを含むサーバーの場所をサーバー エンドポイントとして同期グループに追加すると、それらのファイルは、同期グループの他のエンドポイント上に既にある他のファイルと統合されます。

### <a name="cloud-endpoint"></a>クラウド エンドポイント
クラウド エンドポイントは、同期グループに含まれる Azure ファイル共有です。 Azure ファイル共有の同期全体と 1 つの Azure ファイル共有は、1 つのクラウド エンドポイントのみのメンバーになることができます。 したがって、Azure ファイル共有は、1 つの同期グループのみのメンバーになることができます。 既存の一連のファイルを含む Azure ファイル共有をクラウド エンドポイントとして同期グループに追加すると、既存のファイルは、同期グループの他のエンドポイント上に既にある他のファイルと統合されます。

> [!Important]  
> Azure File Sync は、Azure ファイル共有に対する直接的な変更をサポートします。 ただし、Azure ファイル共有に対して行われた変更は、まず Azure File Sync の変更検出ジョブによって認識される必要があります。 クラウド エンドポイントに対する変更検出ジョブは、24 時間に 1 回のみ起動されます。 詳細については、「[Azure Files についてよく寄せられる質問 (FAQ)](storage-files-faq.md#afs-change-detection)」を参照してください。

### <a name="cloud-tiering"></a>クラウドの階層化 
クラウドの階層化は、使用頻度やアクセス頻度が低いファイルを Azure Files に階層化できる、Azure File Sync のオプション機能です。 ファイルを階層化すると、Azure File Sync ファイル システム フィルター (StorageSync.sys) がローカルでファイルをポインターと置き換えるか、ポイントを再解析します。 再解析ポイントは Azure Files 内のファイルの URL を表します。 階層化されたファイルは NTFS 内に "オフライン" 属性が設定されるため、サード パーティ アプリケーションは階層化されたファイルを特定できます。 ユーザーが階層化されたファイルを開くと、Azure File Sync がファイル データを Azure Files からシームレスに再呼び出しします。ユーザーは、ファイルがシステムにローカルに保存されていないことを知る必要はありません。 この機能は、階層型ストレージ管理 (HSM) とも呼ばれます。

## <a name="azure-file-sync-interoperability"></a>Azure File Sync の相互運用性 
このセクションでは、Windows Server の機能とロール、およびサード パーティ ソリューションとの Azure File Sync の相互運用性について説明します。

### <a name="supported-versions-of-windows-server"></a>サポートされている Windows Server のバージョン
現時点では、Azure File Sync では次の Windows Server バージョンがサポートされています。

| バージョン | サポートされている SKU | サポートされているデプロイ オプション |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter および Standard | フル (UI ありのサーバー) |
| Windows Server 2012 R2 | Datacenter および Standard | フル (UI ありのサーバー) |

Windows Server の今後のバージョンは、それらがリリースされたときに追加されます。 Windows の以前のバージョンは、ユーザーからのフィードバックに基づいて追加される場合があります。

> [!Important]  
> Azure File Sync で使用するすべてのサーバーは、Windows Update の最新の更新プログラムを使用して常に最新の状態を保つことをお勧めします。 

### <a name="file-system-features"></a>ファイル システムの機能
| 機能 | サポートの状態 | メモ |
|---------|----------------|-------|
| アクセス制御リスト (ACL) | 完全にサポートされます | Windows ACL は、Azure File Sync に保持され、サーバー エンドポイント上の Windows Server によって適用されます。 クラウドでファイルに直接アクセスする場合、Azure Files は Windows ACL を (まだ) サポートしていません。 |
| ハード リンク | Skipped | |
| シンボリック リンク | Skipped | |
| マウント ポイント | 部分的にサポート | マウント ポイントは、サーバー エンドポイントのルートの場合がありますが、サーバー エンドポイントの名前空間に含まれている場合はスキップされます。 |
| 接合 | Skipped | |
| 再解析ポイント | Skipped | |
| NTFS 圧縮 | 完全にサポートされます | |
| スパース ファイル | 完全にサポートされます | スパース ファイルは同期されます (ブロックされません) が、完全なファイルとしてクラウドと同期されます。 クラウド内 (または他のサーバー上) のファイルの内容が変更され、変更がダウンロードされると、ファイルはスパースではなくなります。 |
| 代替データ ストリーム (ADS) | 保持されますが、同期されません | |

> [!Note]  
> NTFS ボリュームのみがサポートされます。

### <a name="failover-clustering"></a>フェールオーバー クラスタリング
Windows Server フェールオーバー クラスタリングは、Azure File Sync の "汎用ファイル サーバー" デプロイ オプションでサポートされています。 フェールオーバー クラスタリングは、"アプリケーション データ用のスケールアウト ファイル サーバー" (SOFS) またはクラスター共有ボリューム (CSV) ではサポートされていません。

> [!Note]  
> 同期が適切に機能するには、フェールオーバー クラスターのすべてのノードに Azure File Sync エージェントをインストールする必要があります。

### <a name="data-deduplication"></a>データ重複除去
クラウドの階層化が有効ではないボリュームでは、Azure File Sync は、そのボリュームでの Windows Server のデータ重複除去の有効化をサポートします。 現時点では、クラウドの階層化が有効な Azure File Sync とデータ重複除去間の相互運用性はサポートされていません。

### <a name="antivirus-solutions"></a>ウイルス対策ソリューション
ウイルス対策は、ファイルをスキャンして既知の悪意のあるコードを検索することで機能するので、ウイルス対策製品によって階層化されたファイルの再呼び出しが発生することがあります。 階層化されたファイルには "オフライン" 属性が設定されているため、オフライン ファイルの読み取りをスキップするようにソリューションを構成する方法について、ソフトウェア ベンダーと相談することをお勧めします。 

次のソリューションは、オフライン ファイルのスキップをサポートすることが確認されています。

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee EndPoint Security](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (この PDF の 90 ページの「Scan only what you need to」(必要なファイルのみをスキャンする) セクションを参照してください)
- [Kaspersky Anti-Virus](https://support.kaspersky.com/4684)
- [Sophos Endpoint Protection](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>バックアップ ソリューション
ウイルス対策ソリューションと同様に、バックアップ ソリューションでも階層化されたファイルの再呼び出しが発生することがあります。 Azure ファイル共有をバックアップするには、オンプレミスのバックアップ製品ではなく、クラウド バックアップ ソリューションを使用することをお勧めします。

### <a name="encryption-solutions"></a>暗号化ソリューション
暗号化ソリューションのサポートは、実装方法によって変わります。 Azure File Sync は次のソリューションと連携することが確認されています。

- BitLocker 暗号化
- Azure Rights Management サービス (Azure RMS) (および従来の Active Directory RMS)

Azure File Sync は、次のソリューションと連携しないことが確認されています。

- NTFS 暗号化ファイル システム (EFS)

通常、Azure File Sync は、ファイル システムの下に位置する暗号化ソリューション (BitLocker など) と、ファイル形式で実装されるソリューション (BitLocker など) との相互運用性をサポートします。 ファイル システムの上に位置するソリューション (NTFS EFS など) に対する特別な相互運用性はありません。

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>その他の階層型ストレージ管理 (HSM) ソリューション
その他の HSM ソリューションを Azure File Sync で使用することはできません。

## <a name="region-availability"></a>利用可能なリージョン
Azure File Sync のプレビューは、次のリージョンでのみ利用できます。

| リージョン | データ センターの場所 |
|--------|---------------------|
| 米国西部 | カリフォルニア州 (米国) |
| 西ヨーロッパ | オランダ |
| 東南アジア | シンガポール |
| オーストラリア東部 | ニュー サウス ウェールズ州 (オーストラリア) |

プレビューでは、ストレージ同期サービスと同じリージョンの Azure ファイル共有との同期のみをサポートしています。

## <a name="azure-file-sync-agent-update-policy"></a>Azure File Sync エージェントの更新ポリシー
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>次のステップ
* [Azure Files のデプロイの計画](storage-files-planning.md)
* [Azure Files をデプロイする](storage-files-deployment-guide.md)
* [Azure File Sync をデプロイする](storage-sync-files-deployment-guide.md)
