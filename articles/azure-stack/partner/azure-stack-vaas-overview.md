---
title: Azure Stack のサービスとしての検証の概要 | Microsoft Docs
description: Azure Stack のサービスとしての検証に関する既知の問題の概要。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: c96e7385356354d398108ad69492603d38667e46
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234536"
---
# <a name="what-is-validation-as-a-service-for-azure-stack"></a>Azure Stack のサービスとしての検証の概要

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

サービスとしての検証 (VaaS) とは、Microsoft と共同で Azure Stack のサービスを開発しているソリューション パートナー用に設計されたネイティブの Azure サービスです。 ソリューション パートナーはサービスを使用して、ソリューションが Microsoft の要件を満たし、Azure Stack で期待どおりに動作することを確認できます。

VaaS の主な用途は次のとおりです。

- 新しい Azure Stack ソリューションの検証
- Azure Stack ソフトウェアに加えられた変更の検証
- デプロイ時に使用されるデジタル署名されたソリューション パートナーのパッケージの取得
- Azure Stack の 2 次検証のプレビュー

## <a name="validate-new-azure-stack-solution"></a>新しい Azure Stack ソリューションの検証

パートナーは、ソリューションの検証ワークフローを使用して、新しい Azure Stack ソリューションを確認します。 ソリューションは、必須の Hardware Lab Kit (HKL) Azure Stack テストのコンポーネント テストに合格する必要があります。 ワークフローは、新しいソリューションごとに 2 回 (最小構成と最大構成に対してそれぞれ 1 回) 実行するだけで構いません。

詳細については、「[Validate a new Azure Stack solution (新しい Azure Stack ソリューションの検証)](azure-stack-vaas-validate-solution-new.md)」を参照してください。

## <a name="validate-changes-to-the-azure-stack-software"></a>Azure Stack ソフトウェアに加えられた変更の検証

パートナーは、パッケージ検証ワークフローを使用して、最新の Azure Stack ソフトウェア更新プログラムでソリューションが動作することを確認します。 少なくとも、Microsoft が推奨するハードウェア環境、および更新プログラムの適用にパッチと更新プログラム (P&U) が使用されたソリューションで、パッケージ検証ワークフローを実行する必要があります。 パッケージ検証はベースライン ビルドで実行する必要があります。

詳細については、「[Validate software updates from Microsoft (Microsoft のソフトウェア更新プログラムの検証)](azure-stack-vaas-validate-microsoft-updates.md)」を参照してください。

## <a name="get-digitally-signed-solution-partner-packages"></a>デジタル署名されたソリューション パートナーのパッケージの取得

Azure Stack 更新プログラムの検証に加えて、パッケージ検証ワークフローを使用して OEM カスタマイズ パッケージに対する更新プログラムを確認できます。このパッケージには Azure Stack ソフトウェアのデプロイ時に使用される Azure Stack パートナー固有のドライバー、ファームウェア、その他のソフトウェアが含まれています。 少なくとも、サポートが予定されている最小サイズのソリューションを使用して、Azure Stack ソフトウェアの最新バージョン上で確認するパッケージをデプロイします。 更新したパッケージは、テストを開始する前にサービスにアップロードする必要があります。 テストが成功した場合は、vaashelp@microsoft.com にお知らせください。 パッケージのテストが完了し、Azure Stack デジタル署名でデジタル署名する必要があることを Azure Stack パートナーに知らせます。 Microsoft はパッケージに署名し、Azure Stack パートナーに対し、パッケージがポータルでダウンロード可能であることを通知します。

詳細については、「[Validate OEM packages (OEM パッケージの検証)](azure-stack-vaas-validate-oem-package.md)」を参照してください。

## <a name="preview-azure-stack-validation-collateral"></a>Azure Stack の 2 次検証のプレビュー

Microsoft は、定期的に Azure Stack で新機能を提供しています。 これらの機能を市場に投入するための開発プロセスの一環として、テスト合格ワークフローで新しい 2 次テストが利用できるようになりました。 テスト合格ワークフローには、他のワークフローからの 2 次テストが含まれ、非公式なテストを実行できます。 承認を求めて結果を提出するときに、テスト合格ワークフローを使用しないでください。 ソリューションの正式な承認を得るには、ソリューション検証とパッケージ検証のワークフローを使用します。

## <a name="next-steps"></a>次の手順

- 作業を開始して、[サービスとしての検証アカウントを設定する](azure-stack-vaas-validate-solution-new.md)
