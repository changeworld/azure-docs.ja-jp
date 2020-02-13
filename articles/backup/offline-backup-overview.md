---
title: オフライン バックアップの概要
description: Azure Data Box ベースのオフライン バックアップや Azure Import/Export ベースのオフライン バックアップなど、オフライン バックアップのさまざまなコンポーネントについて説明します。
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 35bc15488624f3648bdc765a36d10607b4ca2de9
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026491"
---
# <a name="overview-of-offline-backup"></a>オフライン バックアップの概要

この記事では、オフライン バックアップの概要を説明します。

Azure への初回完全バックアップでは、通常、大量のデータがオンラインで転送され、増分変更のみを転送する後続のバックアップと比べて多くのネットワーク帯域幅が必要になります。 特定地域のリモート オフィスやデータセンターには、常に十分なネットワーク帯域幅があるわけではありません。 このため、これらの初期バックアップには数日かかり、この期間中は、オンプレミス データセンターで実行されているアプリケーション用にプロビジョニングされたのと同じネットワークが継続的に使用されます。

Azure Backup では "オフライン バックアップ" がサポートされています。これにより、ネットワーク帯域幅を使用せずに、初期バックアップ データをオフラインで転送することができます。 バックアップ データを物理ストレージ デバイスにコピーし、それが近くの Azure データセンターに発送され、Recovery Services コンテナーにアップロードされるというメカニズムが提供されます。 このプロセスにより、ネットワーク帯域幅を使用せずにバックアップ データを確実に転送できます。

## <a name="offline-backup-options"></a>オフライン バックアップのオプション

オフライン バックアップは、ストレージ デバイスの所有権に基づいて 2 つのモードで提供されます。

1. Azure Data Box ベースのオフライン バックアップ (プレビュー)
2. Azure Import/Export ベースのオフライン バックアップ

## <a name="azure-data-box-based-offline-backup-preview"></a>Azure Data Box ベースのオフライン バックアップ (プレビュー)

このモードは、現在、MARS エージェントでサポートされています (プレビュー段階)。 このオプションでは、[Azure Data Box サービス](https://azure.microsoft.com/services/databox/)を利用し、USB コネクタを使用して Microsoft 独自の安全で改ざん防止機能を備えた転送アプライアンスをデータセンターやリモート オフィスに転送し、バックアップ データを直接それらのデバイスに書き込みます。 **このオプションを使用すると、Azure と互換性のあるディスクやコネクタを自分で調達したり、一時的ストレージをステージング場所としてプロビジョニングしたりするために必要な労力を節約できます。** さらに、Microsoft がエンドツーエンドの転送ロジスティックを処理し、ユーザーは Azure portal を通じてそれを追跡できます。 このオプションを使用したバックアップ データの移動について説明するアーキテクチャを次に示します。

![Azure Backup Data Box のアーキテクチャ](./media/offline-backup-overview/azure-backup-databox-architecture.png)

アーキテクチャの概要は次のとおりです。

1. Azure Backup は、事前に構成されたこれらのデバイスにバックアップ データを直接コピーします。
2. 次に、ユーザーはそれらのデバイスを Azure データセンターに返送できます。
3. Azure Data Box サービスが、お客様が所有するストレージ アカウントにデータをコピーします。
4. Azure Backup がバックアップ データをストレージ アカウントから指定された Recovery Services コンテナーに自動的にコピーし、増分オンライン バックアップがスケジュールされます。

Azure Data Box ベースのオフライン バックアップを使用するには、[こちらの記事](offline-backup-azure-data-box.md)を参照してください。

## <a name="azure-importexport-based-offline-backup"></a>Azure Import/Export ベースのオフライン バックアップ

このオプションは、Azure Backup Server (MABS)/DPM-A/MARS エージェントでサポートされています。 [Azure Import/Export サービス](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)が利用されます。それにより、Azure と互換性のある独自のディスクとコネクタを使用して、初期バックアップ データを Azure に転送することができます。 この方法では、**ステージング場所**と呼ばれる一時的ストレージをプロビジョニングし、事前に構築されたユーティリティを使用してバックアップ データをフォーマットし、顧客が所有するディスクにコピーする必要があります。 このオプションを使用したバックアップ データの移動について説明したアーキテクチャを次に示します。

![Azure Backup Import/Export のアーキテクチャ](./media/offline-backup-overview/azure-backup-import-export.png)

アーキテクチャの概要は次のとおりです。

1. ネットワーク経由でバックアップ データを送信する代わりに、Azure Backup はステージング場所にバックアップ データを書き込みます。
2. ステージング場所のデータは、カスタム ユーティリティを使用して 1 つ以上の SATA ディスクに書き込まれます。
3. ユーティリティは、準備作業の一環として Azure Import ジョブを作成します。 SATA ドライブが最も近い Azure データセンターに発送され、インポート ジョブを参照してアクティビティを接続します。
4. Azure データセンターでは、ディスク上のデータが Azure ストレージ アカウントにコピーされます。
5. Azure Backup によってストレージ アカウントから Recovery Services コンテナーにバックアップ データがコピーされ、増分バックアップがスケジュールされます。

Azure Import/Export ベースのオフライン バックアップを MARS エージェントで使用するには、[こちらの記事](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)を参照してください。

同じものを MABS/DPM-A で使用するには、[こちらの記事](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-)を参照してください。

## <a name="offline-backup-support-summary"></a>オフライン バックアップのサポートの概要

次の表では、使用可能な 2 つのオプションを比較し、シナリオに応じて適切な選択を行うことができます。

| **考慮事項**                                            | **Azure Data Box ベースのオフライン バックアップ**                     | **Azure Import/Export ベースのオフライン バックアップ**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure Backup のデプロイ モデル                              | MARS エージェント (プレビュー)                                              | MARS エージェント、Azure Backup Server (MABS)、DPM-A                                           |
| サーバー (MARS) 1 台あたり、または保護グループ (MABS、DPM-A) 1 つあたりの最大バックアップ データ | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) - 7.2 TB <br> [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) - 80 TB       | 80 TB (それぞれ 8 TB のディスクが最大 10 個)                          |
| セキュリティ (データ、デバイスおよびサービス)                           | [データ](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) - AES-256 ビットで暗号化 <br> [デバイス](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) - 堅牢なケースで、データをコピーするための独自の資格情報ベースのインターフェイス <br> [サービス](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) - Azure セキュリティ機能によって保護 | データ - BitLocker で暗号化                                 |
| 一時的なステージング場所のプロビジョニング                     | 不要                                                | バックアップ データの推定サイズ以上        |
| サポートされているリージョン                                           | [Azure Data Box Disk のリージョン](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Azure Data Box のリージョン](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Azure Import/Export のリージョン](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| 国外への発送*                                     | **サポート対象外**  <br>    "*Azure データセンターの発送元の住所と宛先は同一国内でなければなりません*" | サポートされています                                                    |
| 転送ロジスティック (配送、輸送、集荷)           | Microsoft が完全に管理                                     | お客様による管理                                            |
| 価格                                                      | [Azure Data Box の価格](https://azure.microsoft.com/pricing/details/databox/) <br> [Azure Data Box Disk の価格](https://azure.microsoft.com/pricing/details/databox/disk/) | [Azure Import/Export の価格](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* *お住まいの国に Azure データセンターがない場合は、別の国の Azure データセンターにディスクを発送する必要があります。*

## <a name="next-steps"></a>次のステップ

* [MARS エージェントの Azure Data Box ベースのオフライン バックアップ](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [MARS エージェントの Azure Import/Export ベースのオフライン バックアップ](backup-azure-backup-import-export.md)  
* [MABS/DPM-A の Azure Import/Export ベースのオフライン バックアップ](backup-azure-backup-server-import-export-.md)
