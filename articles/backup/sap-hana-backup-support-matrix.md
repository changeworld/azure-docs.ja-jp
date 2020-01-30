---
title: SAP HANA バックアップのサポート マトリックス
description: この記事では、Azure Backup を使用して Azure VM 上の SAP HANA データベースをバックアップするときにサポートされるシナリオと制限事項について説明します。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: a0a7c25ec718dcd6a903d2149a8b3930fb25941e
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514294"
---
# <a name="support-matrix-for-backup-of-sap-hana-databases-on-azure-vms"></a>Azure VM 上の SAP HANA データベースのバックアップに関するサポート マトリックス

Azure Backup では、Azure への SAP HANA データベースのバックアップがサポートされます。 この記事では、Azure Backup を使用して Azure VM 上の SAP HANA データベースをバックアップするときにサポートされるシナリオと適用される制限事項についてまとめます。

## <a name="onboard-to-the-public-preview"></a>パブリック プレビューにオンボードする

以下のように、パブリック プレビューにオンボードします。

* ポータルで、[この記事に従って](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors#solution-3---azure-portal)、Recovery Services サービス プロバイダーにご利用のサブスクリプション ID を登録します。
* PowerShell では、このコマンドレットを実行します。 "登録済み" として完了するはずです。

```PowerShell
Register-AzProviderFeature -FeatureName "HanaBackup" –ProviderNamespace Microsoft.RecoveryServices
```

> [!NOTE]
> ログ バックアップの頻度を最大 15 分ごとに設定できるようになりました。 ログ バックアップでは、データベースの完全バックアップが正常に完了した後にのみ、フローが開始されます。

## <a name="scenario-support"></a>シナリオのサポート

| **シナリオ**               | **サポートされている構成**                                | **サポートされていない構成**                              |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| **トポロジ**               | Azure Linux VM 実行されている SAP HANA のみ                    | HANA ラージ インスタンス (HLI)                                   |
| **地域**                   | オーストラリア南東部、オーストラリア東部、ブラジル南部、カナダ中部、カナダ東部、東南アジア、東アジア、米国東部、米国東部 2、米国中西部、米国西部、米国西部 2、米国中北部、米国中部、米国中南部、インド中部、インド南部、東日本、西日本、韓国中部、韓国南部、北ヨーロッパ、西ヨーロッパ、英国南部、英国西部 | オーストラリア中部、オーストラリア中部 2、中国東部、中国北部、中国東部 2、中国北部 2、インド西部、フランス中部、フランス南部、ドイツ北部、ドイツ中西部、スイス北部、スイス西部、南アフリカ北部、南アフリカ西部、アラブ首長国連邦北部、アラブ首長国連邦中部、Azure Government のリージョン |
| **OS のバージョン**            | SLES 12 SP2、SP3、SP4           | SLES 15、RHEL                                                |
| **HANA のバージョン**          | HANA 1.x の場合は SDC、SPS04 Rev 44 以下の HANA 2.x の場合は MDC           | -                                                            |
| **HANA のデプロイメント**       | 単一の Azure VM 上の SAP HANA - スケールアップのみ               | スケールアウト                                                    |
| **HANA インスタンス**         | 単一の Azure VM 上の単一の SAP HANA インスタンス – スケールアップのみ | 単一の VM 上の複数の SAP HANA インスタンス                  |
| **HANA データベースの種類**    | 1\.x の場合は SDC (Single Database Container)、2.x の場合は MDC (Multi-Database Container) | HANA 1.x での MDC                                              |
| **HANA データベースのサイズ**     | HANA によって報告された 2 TB の完全バックアップサイズ |                                                              |
| **バックアップの種類**           | 完全バックアップ、差分バックアップ、ログ バックアップ                           | 増分、スナップショット                                       |
| **復元の種類**          | サポートされている復元の種類については、SAP HANA ノート [1642148](https://launchpad.support.sap.com/#/notes/1642148) を参照してください |                                                              |
| **バックアップの制限**          | SAP HANA インスタンスごとに最大 2 TB の完全バックアップ サイズ  |                                                              |
| **特殊な構成** |                                                              | SAP HANA + Dynamic Tiering <br>  LaMa を使用した複製            |

------

> [!NOTE]
> SAP HANA ネイティブ クライアント (SAP HANA Studio/Cockpit/DBA Cockpit) からのバックアップおよび復元操作は、現在サポートされていません。



## <a name="next-steps"></a>次のステップ

* [Azure VM で稼働している SAP HANA データベースをバックアップする](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)方法を学習する
* [Azure VM で稼働している SAP HANA データベースを復元する](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)方法を学習する
* [Azure Backup を使用してバックアップされた SAP HANA データベースを管理する](sap-hana-db-manage.md)方法を学習する
* [SAP HANA データベースのバックアップ時に発生する一般的な問題をトラブルシューティングする](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot)方法を学習する
