---
title: Azure Sentinel SAP ソリューションの詳細な SAP 要件 | Microsoft Docs
description: Azure Sentinel SAP ソリューションの詳細な SAP システム要件について説明します。
author: batamig
ms.author: bagold
ms.service: azure-sentinel
ms.topic: reference
ms.custom: mvc
ms.date: 06/09/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: 63ca9c2a76b5daaf576992f715a7ec112ac98b25
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681526"
---
# <a name="azure-sentinel-sap-solution-detailed-sap-requirements-public-preview"></a>Azure Sentinel SAP ソリューションの詳細な SAP 要件 (パブリック プレビュー)

[Azure Sentinel SAP ソリューションをデプロイするための既定の手順](sap-deploy-solution.md)には、必要な SAP 変更要求と SAP ノートが含まれ、すべての必要なアクセス許可を備えた組み込みロールが用意されています。

この記事では、必要な SAP 変更要求、ノート、アクセス許可について詳しく説明します。

管理者の場合、または [SAP ソリューションを手動でデプロイする](sap-solution-deploy-alternate.md)場合は、この記事を参照してください。 この記事の対象は、上級 SAP ユーザーです。


> [!IMPORTANT]
> Azure Sentinel SAP ソリューションは、現在プレビュー段階です。 [Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)には、ベータ版、プレビュー版、またはまだ一般提供されていない Azure 機能に適用される追加の法律条項が含まれています。
>

> [!NOTE]
> セキュリティ保護された SNC 接続を使用して SAP データ コネクタをデプロイしている場合は、追加の要件が示されます。 詳細については、「[SNC を使用して Azure Sentinel SAP データ コネクタを展開する](sap-solution-deploy-snc.md)」を参照してください。
>
## <a name="recommended-virtual-machine-sizing"></a>仮想マシンの推奨されるサイズ設定

次の表では、使用目的に応じた、仮想マシンの推奨されるサイズ設定について説明します。

|使用方法  |推奨されるサイズ設定  |
|---------|---------|
|**最小仕様** (ラボ環境など)     |   *Standard_B2s* VM      |
|**標準コネクタ** (既定)     |   次のような *DS2_v2* VM: <br>- 2 コア<br>- 8 GB メモリ      |
|**複数コネクタ**     |次のような *Standard_B4ms* VM: <br>- 4 コア<br>- 16 GB メモリ         |
|     |         |

## <a name="required-sap-log-change-requests"></a>必要な SAP ログ変更要求

SAP Basis のバージョンに応じて、SAP ソリューションには次の SAP ログ変更要求が必要です。

- **SAP Basis バージョン 7.50 以降** では、NPLK900144 をインストールします
- **それより前のバージョンの場合は**、NPLK900146 をインストールします
- サポートされている SAP Basis バージョンに **必要な承認を持つ SAP ロールを作成するには**、NPLK900140 をインストールします。 詳細については、「[SAP システムを構成する](sap-deploy-solution.md#configure-your-sap-system)」と「[必要な ABAP 承認](#required-abap-authorizations)」を参照してください。

> [!NOTE]
> 必要な SAP ログ変更要求では、コネクタに必要なカスタム RFC VM が公開され、標準オブジェクトやカスタム オブジェクトは変更されません。
>

## <a name="required-sap-notes"></a>必要な SAP ノート

SAP Basis バージョンが 7.50 以前の場合は、次の SAP ノートをインストールします。

|SAP Basis のバージョン  |必要なノート |
|---------|---------|
|- 750 SP01 から SP12<br>- 751 SP01 から SP06<br>- 752 SP01 から SP03     |  2641084: セキュリティ監査ログ データのための標準化された読み取りアクセス       |
|- 700 から 702<br>- 710 から 711、730、731、740、750     | 2173545: CD: CHANGEDOCUMENT_READ_ALL        |
|- 700 から 702<br>- 710 から 711、730、731、740<br>- 750 から 752     | 2502336: CD (ドキュメントの変更): RSSCD100 - データベースからではなく、アーカイブからのみ読み取る        |
|     |         |

[SAP サポート Launchpad サイト](https://support.sap.com/en/index.html)から SAP ノートにアクセスします。

## <a name="required-abap-authorizations"></a>必要な ABAP 承認

次の表は、バックエンド SAP ユーザーが Azure Sentinel を SAP ログに接続するために必要な ABAP 承認の一覧です。 詳細については、「[SAP システムを構成する](sap-deploy-solution.md#configure-your-sap-system)」を参照してください。

必要な承認の一覧は、ログの種類別に示されています。 Azure Sentinel に取り込む予定のログの種類に対して示されている承認だけが必要です。

> [!TIP]
> 必要なすべての承認を備えたロールを作成するには、SAP システムに SAP 変更要求 [NPLK900114](#required-sap-log-change-requests) をデプロイします。 この変更要求により、 **/MSFTSEN/SENTINEL_CONNECTOR** ロールが作成され、Azure Sentinel に接続する ABAP ユーザーにそのロールが割り当てられます。
>

| 承認オブジェクト | フィールド | 値 |
| -------------------- | ----- | ----- |
| **すべての RFC ログ** | | |
| S_RFC | FUGR | /OSP/SYSTEM_TIMEZONE |
| S_RFC | FUGR | ARFC |
| S_RFC | FUGR | STFC |
| S_RFC | FUGR | RFC1 |
| S_RFC | FUGR | SDIFRUNTIME  |
| S_RFC | FUGR | SMOI |
| S_RFC | FUGR | SYST |
| S_RFC | FUGR/FUNC | SRFC/RFC_SYSTEM_INFO |
| S_RFC | FUGR/FUNC | THFB/TH_SERVER_LIST |
| S_TCODE | TCD | SM51 |
| **ABAP アプリケーション ログ**  | | |
| S_APPL_LOG | ACTVT | 表示 |
| S_APPL_LOG | ALG_OBJECT | * |
| S_APPL_LOG | ALG_SUBOBJ | * |
| S_RFC | FUGR | SXBP_EXT |
| S_RFC | FUGR | /MSFTSEN/_APPLOG |
| **ABAP ドキュメント変更ログ** | | |
| S_RFC | FUGR | /MSFTSEN/_CHANGE_DOCS |
| **ABAP CR ログ** | | |
| S_RFC | FUGR | CTS_API |
| S_RFC | FUGR | /MSFTSEN/_CR |
| S_TRANSPRT | ACTVT | 表示 |
| S_TRANSPRT | TTYPE | * |
| **ABAP DB テーブル データ ログ** | | |
| S_RFC | FUGR | /MSFTSEN/_TD |
| S_TABU_DIS | ACTVT | 表示 |
| S_TABU_DIS | DICBERCLS | &NC& |
| S_TABU_DIS | DICBERCLS | + ログに必要なすべてのオブジェクト |
| S_TABU_NAM | ACTVT | 表示 |
| S_TABU_NAM | TABLE | + ログに必要なすべてのオブジェクト |
| S_TABU_NAM | TABLE | DBTABLOG |
| **ABAP ジョブ ログ** | | |
| S_RFC | FUGR | SXBP |
| S_RFC | FUGR | /MSFTSEN/_JOBLOG |
| **ABAP ジョブ ログ、ABAP アプリケーション ログ** | | |
| S_XMI_PRD | INTERFACE | XBP |
| **ABAP セキュリティ監査ログ - XAL** | | |
| すべての RFC | S_RFC | FUGR |
| S_ADMI_FCD | S_ADMI_FCD | AUDD |
| S_RFC | FUGR | SALX |
| S_USER_GRP | ACTVT | 表示 |
| S_USER_GRP | CLASS | * |
| S_XMI_PRD | INTERFACE | XAL |
| **ABAP セキュリティ監査ログ - XAL、ABAP ジョブ ログ、ABAP アプリケーション ログ** | | |
| S_RFC | FUGR | SXMI |
| S_XMI_PRD | EXTCOMPANY | Microsoft |
| S_XMI_PRD | EXTPRODUCT | Azure Sentinel |
| **ABAP セキュリティ監査ログ - SAL** | | |
| S_RFC | FUGR | RSAU_LOG |
| S_RFC | FUGR | /MSFTSEN/_AUDITLOG |
| **ABAP スプール ログ、ABAP スプール出力ログ** | | |
| S_RFC | FUGR | /MSFTSEN/_SPOOL |
| **ABAP ワークフロー ログ** | | |
| S_RFC | FUGR | SWRR |
| S_RFC | FUGR | /MSFTSEN/_WF |
| | |

## <a name="next-steps"></a>次のステップ

詳細については、次を参照してください。

- [SAP 用 Azure Sentinel ソリューションをデプロイする](sap-deploy-solution.md)
- [SNC を使用して Azure Sentinel SAP データ コネクタをデプロイする](sap-solution-deploy-snc.md)
- [エキスパートの構成オプション、オンプレミス デプロイ、SAPControl のログ ソース](sap-solution-deploy-alternate.md)
- [Azure Sentinel SAP ソリューション ログ リファレンス](sap-solution-log-reference.md)
- [Azure Sentinel SAP ソリューション: 利用できるセキュリティ コンテンツ](sap-solution-security-content.md)
- [Azure Sentinel SAP ソリューションのデプロイのトラブルシューティング](sap-deploy-troubleshoot.md)
