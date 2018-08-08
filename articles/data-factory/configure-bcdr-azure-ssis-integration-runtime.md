---
title: Azure-SSIS 統合ランタイムの事業継続とディザスター リカバリー (BCDR) に関する推奨事項 | Microsoft Docs
description: この記事では、Azure-SSIS 統合ランタイムの事業継続とディザスター リカバリーに関する推奨事項について説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/26/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 37347df2d543116085f52fed76c692b60fac2ad6
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39295310"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-recommendations-for-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムの事業継続とディザスター リカバリー (BCDR) に関する推奨事項

ディザスター リカバリーのために、現在実行中のリージョンで Azure-SSIS 統合ランタイムを停止し、別のリージョンに切り替えて再び開始できます。 この目的では、[Azure のペアになっているリージョン](../best-practices-availability-paired-regions.md)を使用することをお勧めします。

## <a name="prerequisites"></a>前提条件

- Azure SQL Database サーバーが同時に停止した場合に備えて、サーバーのディザスター リカバリーを有効にしていることを確認します。 詳細情報については、「[Azure SQL Database によるビジネス継続性の概要](../sql-database/sql-database-business-continuity.md)」を参照してください。

- 現在のリージョンで仮想ネットワークを使用している場合は、新しいリージョン内の別の仮想ネットワークを使用して、Azure-SSIS 統合ランタイムを接続する必要があります。 詳細情報については、「[Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる](join-azure-ssis-integration-runtime-virtual-network.md)」を参照してください。

- カスタム セットアップを使用している場合は、カスタム セットアップ スクリプトと関連ファイルを格納する BLOB コンテナー用の別のSAS URI を準備して、停止中も引き続きアクセスできるようにする必要があります。 詳細については、「[Azure-SSIS 統合ランタイムの設定のカスタマイズ](how-to-configure-azure-ssis-ir-custom-setup.md)」をご覧ください。

## <a name="steps"></a>手順

Azure-SSIS IR を停止し、IR を新しいリージョンに切り替え、再び開始するには、以下の手順を実行します。

1. 元のリージョンで IR を停止します。

2. PowerShell で次のコマンドを呼び出して、IR を更新します。

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    この PowerShell コマンドの詳細については、「[Azure Data Factory で Azure-SSIS 統合ランタイムを作成する](create-azure-ssis-integration-runtime.md)」を参照してください。

3. IR を再び開始します。

## <a name="next-steps"></a>次の手順

Azure SSIS IR の以下のその他の構成オプションを検討してください。

- [Azure-SSIS 統合ランタイムを高パフォーマンス用に構成する](configure-azure-ssis-integration-runtime-performance.md)

- [Azure-SSIS 統合ランタイムの設定をカスタマイズする](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Azure-SSIS 統合ランタイムの Enterprise Edition をプロビジョニングする](how-to-configure-azure-ssis-ir-enterprise-edition.md)
