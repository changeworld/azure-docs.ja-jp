---
title: Azure Stack のサービスとしての検証の概要 | Microsoft Docs
description: Azure Stack のサービスとしての検証の概要。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 12/20/2018
ROBOTS: NOINDEX
ms.openlocfilehash: c0694a3839ae6b843abd1ddc8b67d1f3e3322e0e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246043"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>Azure Stack のサービスとしての検証の概要

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

サービスとしての検証 (VaaS) は、Azure Stack のサービスを Microsoft と共同開発しているソリューション パートナー向けに設計されたネイティブの Azure サービスです。 ソリューション パートナーはサービスを使用して、ソリューションが Microsoft の要件を満たし、Azure Stack で期待どおりに動作することを確認できます。

VaaS の主な用途は次のとおりです。

- 新しい Azure Stack ソリューションの検証
- Azure Stack ソフトウェアに加えられた変更の検証
- デプロイ時に使用されるソリューション パートナーのパッケージへのデジタル署名
- VaaS の 2 次テストのプレビュー

## <a name="validate-a-new-azure-stack-solution"></a>新しい Azure Stack ソリューションの検証

パートナーは、**ソリューション検証**ワークフローを使用して、新しい Azure Stack ソリューションを検証します。 ソリューションは、必須の Hardware Lab Kit (HLK) Azure Stack コンポーネント テストに合格する必要があります。 さまざまなハードウェア構成を認定するために、新しいソリューションごとに、このワークフローを 2 回 (最小構成と最大構成に対してそれぞれ 1 回) 実行する必要があります。

詳細については、「[Validate a new Azure Stack solution (新しい Azure Stack ソリューションの検証)](azure-stack-vaas-validate-solution-new.md)」を参照してください。

## <a name="validate-changes-to-the-azure-stack-software"></a>Azure Stack ソフトウェアに加えられた変更の検証

パートナーは、**ソリューション検証**ワークフローを使用して、最新の Azure Stack ソフトウェア更新プログラムでソリューションが動作することを確認します。 ソリューション検証ワークフローは、修正プログラムと更新プログラム (P&U) を使用して更新が適用された、Microsoft が推奨するハードウェア環境で実行する必要があります。 また、ベースライン ビルドでもこのワークフローを実行することをお勧めします。

詳細については、「[Validate software updates from Microsoft (Microsoft のソフトウェア更新プログラムの検証)](azure-stack-vaas-validate-microsoft-updates.md)」を参照してください。

## <a name="get-digitally-signed-solution-partner-packages"></a>デジタル署名されたソリューション パートナーのパッケージの取得

Azure Stack 更新プログラムの検証に加え、パートナーは**ソリューション検証**ワークフローを使用して、OEM カスタマイズ パッケージの更新プログラムを検証します。このパッケージには、Azure Stack ソフトウェアのデプロイ時に使用される Azure Stack パートナー固有のドライバー、ファームウェア、その他のソフトウェアが含まれています。 少なくとも、サポートが予定されている最小サイズのソリューションを使用して、Azure Stack ソフトウェアの最新バージョンで検証するパッケージをデプロイします。 パッケージは、テストを実行する前に VaaS に送信されます。 テストが成功した場合は、パッケージのテストが完了し、Azure Stack デジタル署名でデジタル署名する必要があることを [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) に通知します。 Microsoft がパッケージに署名し、パッケージが VaaS ポータルでダウンロード可能であることを Azure Stack パートナーに通知します。

詳細については、「[Validate OEM packages (OEM パッケージの検証)](azure-stack-vaas-validate-oem-package.md)」を参照してください。

## <a name="preview-vaas-test-collateral"></a>VaaS の 2 次テストのプレビュー

Microsoft は、定期的に Azure Stack で新機能を提供しています。 これらの機能を市場に投入するための開発プロセスの一環として、**テスト成功**ワークフローで新しい 2 次テストが利用できるようになりました。 テスト成功ワークフローには、他のワークフローからの 2 次テストが含まれ、非公式のテストを実行できます。 承認を求めて結果を提出するときには、テスト成功ワークフローを使用しないでください。 ソリューションの正式な承認を得るには、ソリューション検証とソリューション検証ワークフローを使用します。

詳細については、「[クイック スタート: サービスとしての検証ポータルを使用して初めてのテストをスケジュールする方法](azure-stack-vaas-schedule-test-pass.md)に関するクイック スタートを参照してください。

## <a name="validation-workflow-tests-summary"></a>検証ワークフロー テストの概要

| 検証ワークフロー | 必須のテスト |
|----|------------|
| [新しいソリューションの検証](azure-stack-vaas-validate-solution-new.md) | Cloud Simulation Engine (クラウド シミュレーション エンジン)<br>Compute SDK Operational Suite (コンピューティング SDK 操作スイート)<br>Disk Identification Test (ディスク識別テスト)<br>KeyVault Extension SDK Operational Suite (KeyVault 拡張機能 SDK 操作スイート)<br>KeyVault SDK Operational Suite (KeyVault SDK 操作スイート)<br>Network SDK Operational Suite (Network SDK 操作スイート)<br>Storage Account SDK Operational Suite (ストレージ アカウント SDK 操作スイート)<br> |
| [OEM パッケージの検証](azure-stack-vaas-validate-oem-package.md) | OEM Extension Package Verification (OEM 拡張機能パッケージの検証)<br>Cloud Simulation Engine (クラウド シミュレーション エンジン) |
| [毎月の更新プログラムの検証](azure-stack-vaas-validate-microsoft-updates.md) | Monthly Azure Stack Update Verification (月次 Azure Stack 更新プログラムの検証)<br>Cloud Simulation Engine (クラウド シミュレーション エンジン)<br> |

## <a name="next-steps"></a>次の手順

- [サービスとしての検証のリソースを設定する](azure-stack-vaas-set-up-resources.md)
- [サービスとしての検証の主要概念](azure-stack-vaas-key-concepts.md)を確認する
