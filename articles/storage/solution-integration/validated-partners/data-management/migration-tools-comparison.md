---
title: Azure Storage 移行ツールの比較 - 非構造化データ
description: 非構造化データの移行に使用されるツールの基本的な機能と比較
author: dukicn
ms.author: nikoduki
ms.topic: conceptual
ms.date: 08/04/2021
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: d266f059869bb0f25df10dcc4fad317d3d3da7c3
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426708"
---
# <a name="comparison-matrix"></a>比較表

次の比較表は、非構造化データの移行に使用できるさまざまなツールの基本的な機能を示しています。 

## <a name="supported-azure-services"></a>サポート対象の Azure サービス

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **[ソリューション名]**  | [Azure File Sync](../../../file-sync/file-sync-deployment-guide.md) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview)              | [Data Mobility and Migration](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligent Data Management](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **Azure Files のサポート (すべてのレベル)** | はい                          | はい                      | はい            | はい                            |
| **Azure NetApp Files のサポート**      | いいえ                           | はい                      | はい            | はい                            |
| **Azure Blob ホット/クールのサポート**   | いいえ                           | はい (NFS プレビュー経由)    | はい            | はい                            |
| **Azure Blob アーカイブ層のサポート** | いいえ                           | いいえ                       | いいえ             | はい (移行先として) |
| **Azure Data Lake Storage のサポート** | いいえ                           | いいえ                       | いいえ             | いいえ                             |
| **サポートされているソース**      | Windows Server 2012 R2 以降 | NAS およびクラウド ファイル システム | 任意の NAS および S3 | NAS、Blob、S3                  |

## <a name="supported-protocols-source--destination"></a>サポートされているプロトコル (ソース/宛先)

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **[ソリューション名]**   | [Azure File Sync](../../../file-sync/file-sync-deployment-guide.md) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Data Mobility and Migration](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligent Data Management](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **SMB 2.1**       | はい | はい | はい | はい |
| **SMB 3.0**       | はい | はい | はい | はい |
| **SMB 3.1**       | はい | はい | はい | はい |
| **NFS v3**        | いいえ  | はい | はい | はい |
| **NFS v4.1**      | いいえ  | はい | いいえ  | はい |
| **Blob REST API** | いいえ  | いいえ  | はい | はい |
| **S3**            | いいえ  | はい | はい | はい |

## <a name="extended-features"></a>拡張された機能

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
|  **[ソリューション名]**  | [Azure File Sync](../../../file-sync/file-sync-deployment-guide.md) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Data Mobility and Migration](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligent Data Management](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **UID / SID の再マッピング**                   | いいえ  | はい                        | はい | いいえ                             |
| **プロトコル ACL の再マッピング**                | いいえ  | いいえ                         | いいえ  | いいえ                             |
| **DFS のサポート**                           | はい | はい                        | はい | はい                            |
| **調整のサポート**                    | はい | はい                        | はい | はい                            |
| **ファイル パターンの除外**               | いいえ  | はい                        | はい | はい (コピー機能を使用) |
| **選択的ファイル属性のサポート** | はい | はい                        | はい | はい (拡張属性の場合)  |
| **伝達の削除**                   | はい | はい                        | はい | はい                            |
| **NTFS 接合ポイントの追跡**                 | いいえ  | はい                        | いいえ  | はい                            |
| **SMB 所有者とグループ所有者のオーバーライド**    | はい | はい                        | はい | いいえ                             |
| **証拠保全の一貫性のレポート**            | いいえ  | はい                        | いいえ  | はい                            |
| **代替データ ストリームのサポート**    | いいえ  | はい                        | はい | いいえ                             |
| **移行のスケジュール設定**              | いいえ  | はい                        | はい | はい                            |
| **ACL の保持**                        | はい  | はい                        | はい | はい                            |
| **DACL のサポート**                          | はい | はい                        | はい | はい                            |
| **SACL のサポート**                          | はい | はい                        | はい | いいえ                             |
| **アクセス時刻の保持**                | はい | はい                        | はい | はい                            |
| **変更時刻の保持**              | はい | はい                        | はい | はい                            |
| **作成時刻の保持**              | はい  | はい                        | はい | はい                            |
| **Azure Data Box のサポート**       | はい | はい                        | いいえ  | いいえ                             |
| **スナップショットの移行**                | いいえ  | マニュアル                     | はい | いいえ                             |
| **シンボリック リンクのサポート**                 | いいえ  | はい                        | いいえ  | はい                            |
| **ハード リンクのサポート**                     | いいえ  | 別個のファイルとして移行 | はい | はい                            |
| **オープンまたはロックされたファイルのサポート**       | はい | はい                        | はい | はい                            |
| **段階的な移行**                 | はい | はい                        | はい | はい                            |
| **切り替えのサポート**                    | いいえ  | はい                        | はい | いいえ (手動のみ)               |
| **[その他の機能](#other-features)**         | [リンク](#azure-file-sync)| [リンク](#datadobi-dobimigrate) | [リンク](#data-dynamics-data-mobility-and-migration) | [リンク](#komprise-intelligent-data-management)                |

## <a name="assessment-and-reporting"></a>評価とレポート

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **[ソリューション名]**   | [Azure File Sync](../../../file-sync/file-sync-deployment-guide.md) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Data Mobility and Migration](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligent Data Management](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **[容量]**                        | いいえ      | はい | はい | はい            |
| **ファイル/フォルダーの数**            | いいえ      | はい | はい | はい            |
| **時系列の年齢分布**      | いいえ      | はい | はい | はい            |
| **アクセス時刻**                     | いいえ      | はい | はい | はい            |
| **変更時刻**                   | いいえ      | はい | はい | はい            |
| **作成時刻**                   | いいえ      | はい | はい | はい            |
| **ファイル/オブジェクトごとのレポートの状態** | Partial | はい | はい | はい            |

## <a name="licensing"></a>ライセンス

|    | [Microsoft](https://www.microsoft.com/) | [Datadobi](https://www.datadobi.com) | [Data Dynamics](https://www.datadynamicsinc.com/) | [Komprise](https://www.komprise.com/) |
|--- |-----------------------------------------|--------------------------------------|---------------------------------------------------|---------------------------------------|
| **[ソリューション名]**   | [Azure File Sync](../../../file-sync/file-sync-deployment-guide.md) | [DobiMigrate](https://azuremarketplace.microsoft.com/marketplace/apps/datadobi1602192408529.datadobi-dobimigrate?tab=Overview )              | [Data Mobility and Migration](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=PlansAndPrice)      | [Intelligent Data Management](https://azuremarketplace.microsoft.com/marketplace/apps/komprise_inc.intelligent_data_management?tab=Overview)    |
| **BYOL**             | 該当なし | はい | はい | はい |
| **Azure コミットメント** | はい   | はい | はい | はい |

## <a name="other-features"></a>その他の機能

### <a name="azure-file-sync"></a>Azure File Sync

- 内部ハッシュ検証

> [!TIP]
> Azure File Sync は、いくつかの Azure ファイル共有のオンプレミス キャッシュおよび同期のための永続的なハイブリッド ソリューションであることが意図されています。 この機能では、ダウンタイムなしのクラウド移行が提供されます。 オンプレミスで Azure ファイル共有をキャッシュすることを予定していない場合、Azure File Sync は推奨される移行ツールではありません。 [Azure ファイル共有の移行の概要](../../../files/storage-files-migration-overview.md)またはこの記事に記載されているその他のパートナー ツールを参照してください。

### <a name="datadobi-dobimigrate"></a>Datadobi の DobiMigrate

- 移行の事前チェック
- 移行の計画
- カットオーバー テストのためのドライ ラン
- カットオーバー前のターゲット側ユーザー アクティビティの検出とアラート通知
- ポリシー主導型の移行
- スケジュールされたコピー反復
- ルート ディレクトリのセキュリティを処理するための構成可能なオプション
- オンデマンドの検証の実行
- ソースおよび宛先でのデータ読み取り確認
- グラフィカルな対話型エラー処理ワークフロー
- 削除や更新などの特定の操作が伝達されるのを制限する機能
- ソースへのアクセス時刻を保持する機能 (宛先に加えて)
- 移行の切り替え中にソースへのロールバックを実行する機能
- 選択した SMB ファイル属性を移行する機能
- NTFS セキュリティ記述子を消去する機能
- NFSv3 のアクセス許可をオーバーライドし、ターゲットに新しいモードのビットを書き込む機能
- NFSv3 POSIX ドラフト ACL を NFSv4 ACL に変換する機能
- SMB 1 (CIFS)
- 24 時間 365 日のサポート

### <a name="data-dynamics-data-mobility-and-migration"></a>Data Dynamics の Data Mobility and Migration

- ハッシュ検証

### <a name="komprise-intelligent-data-management"></a>Komprise の Intelligent Data Management

- プロジェクト/ディレクトリ ベースの移行
- エラーの自動再試行
- 評価/レポート: ファイルの種類、ファイル サイズ、プロジェクト ベース
- 評価/レポート: カスタム メタデータベースの検索
- アーカイブ、レプリケーション、分析のための完全なデータ ライフサイクル管理ソリューション
- Blob、S3 データでのアクセス時刻ベースの分析
- タグ付け
- 24 時間 365 日のサポート
- ハッシュ検証

*リストが最後に検証されたのは 2021 年 3 月 31 日です。*

## <a name="see-also"></a>関連項目

- [Storage の移行の概要](../../../common/storage-migration-overview.md)
- [データ転送用の Azure ソリューションを選択する](../../../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Azure ファイル共有に移行する](../../../files/storage-files-migration-overview.md)
- [WANdisco LiveData Platform for Azure を使用して Data Lake Storage に移行する](../../../blobs/migrate-gen2-wandisco-live-data-platform.md)
- [AzCopy を使用して Azure Storage にデータをコピーまたは移動する](../../../common/storage-use-azcopy-v10.md)
- [AzReplicate (サンプル アプリケーション) を使用して大規模なデータセットを Azure Blob Storage に移行する](/samples/azure/azreplicate/azreplicate/)