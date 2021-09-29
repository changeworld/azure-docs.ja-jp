---
title: Azure File Sync の概要 | Microsoft Docs
description: 業界標準の SMB プロトコルを使用してクラウド上でネットワーク ファイル共有を作成および使用できるようにするサービスである、Azure File Sync の概要。
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 04/19/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e20b911b16ee7ebfe1af4984c9fe701a54b855cd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664600"
---
# <a name="what-is-azure-file-sync"></a>Azure File Sync とは

Azure File Sync を使用すると、Windows ファイル サーバーの柔軟性、パフォーマンス、互換性を維持しながら、Azure Files に組織のファイル共有を一元化できます。 データの完全なコピーをローカルに保持することを選択するユーザーもいますが、Azure File Sync を使用すると、さらに Windows サーバーを Azure ファイル共有の高速キャッシュに変換することができます。 SMB、NFS、FTPS など、Windows Server 上で利用できるあらゆるプロトコルを使用して、データにローカルにアクセスできます。 キャッシュは、世界中にいくつでも必要に応じて設置することができます。

## <a name="videos"></a>ビデオ

| Azure File Sync の概要 | Azure Files と Sync (Ignite 2019)  |
|-|-|
| [![Azure File Sync の概要に関する動画のスクリーンキャスト - クリックして再生](../files/media/storage-files-introduction/azure-file-sync-video-snapshot.png)](https://www.youtube.com/watch?v=Zm2w8-TRn-o) | [![Azure Files と Sync に関するプレゼンテーションのスクリーンキャスト - クリックして再生](../files/media/storage-files-introduction/ignite-2018-video.png)](https://www.youtube.com/embed/6E2p28XwovU) |

## <a name="benefits-of-azure-file-sync"></a>Azure File Sync の利点

### <a name="cloud-tiering"></a>クラウドの階層化

クラウドの階層化を有効にすると、最も頻繁にアクセスするファイルがローカル サーバーにキャッシュされ、アクセス頻度の低いファイルがクラウドに階層化されます。 キャッシュに使用するローカル ディスク領域の容量は制御できます。 階層化されたファイルは、オンデマンドで簡単に再呼び出しできます。これにより、データの一部だけをオンプレミスに格納すればよいので、コストを削減しながらシームレスなエクスペリエンスを実現できます。 クラウドの階層化の詳細については、「[クラウドの階層化の概要](file-sync-cloud-tiering-overview.md)」を参照してください。

### <a name="multi-site-access-and-sync"></a>マルチサイト アクセスと同期

Azure File Sync は、分散アクセスのシナリオに最適です。 Azure File Sync のデプロイの一部として、オフィスごとにローカルの Windows サーバーをプロビジョニングできます。 あるオフィスでサーバーに加えられた変更は、他のすべてのオフィスのサーバーに自動的に同期されます。

### <a name="business-continuity-and-disaster-recovery"></a>事業継続とディザスター リカバリー

Azure File Sync は Azure Files によってサポートされています。これにより、高可用性ストレージを実現する複数の冗長オプションが提供されます。 Azure にはデータの回復性を保証するコピーが含まれているため、ローカル サーバーは破棄可能なキャッシュ デバイスとなり、障害が発生したサーバーからの復旧は、Azure File Sync のデプロイに新しいサーバーを追加することによって行うことができます。 ローカルのバックアップから復元するのではなく、別の Windows サーバーをプロビジョニングし、そこに Azure File Sync エージェントをインストールしてから、これを Azure File Sync のデプロイに追加します。 Azure File Sync は、データをダウンロードする前にファイルの名前空間をダウンロードします。これにより、できるだけ速やかにサーバーを稼働させることができます。 さらにすばやく回復する場合は、デプロイの一環としてウォーム スタンバイ サーバーを使用するか、Azure File Sync と Windows クラスタリングを併用することもできます。

## <a name="cloud-side-backup"></a>クラウド側のバックアップ

Azure Backup を使用してクラウドで一元的なバックアップを行うことによって、オンプレミスでのバックアップ コストを削減できます。 Azure Files SMB 共有にはネイティブなスナップショット機能があり、Azure Backup を使用することで、プロセスの自動化、バックアップのスケジュール設定、保有期間の管理を行うことができます。 Azure Backup もオンプレミス サーバーと統合されるため、クラウドに復元すると、これらの変更が Windows サーバーに自動的にダウンロードされます。

## <a name="next-steps"></a>次の手順

- [Azure File Sync のデプロイの計画](file-sync-planning.md)
- [クラウドの階層化の概要](file-sync-cloud-tiering-overview.md)
- [Azure File Sync の監視](file-sync-monitoring.md)
