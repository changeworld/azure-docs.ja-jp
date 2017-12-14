---
title: "Azure Migrate の評価の設定をカスタマイズする | Microsoft Docs"
description: "Azure Migration Planner を使用して VMware VM を Azure に移行するための、評価の設定と実行方法について説明します。"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: ce47790f6214864afdba33eb5cbe3a9e49b81cd5
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2017
---
# <a name="customize-an-assessment"></a>評価のカスタマイズ

[Azure Migrate](migrate-overview.md) は、既定の設定で評価を作成します。 評価を作成した後は、この記事の手順に従って、これらの既定の設定を変更できます。


## <a name="edit-assessment-values"></a>評価値を編集します。

1. Azure Migrate プロジェクトの [**評価]** ページで、評価を選択し **[プロパティの編集]** をクリックします。
2. 次の表に従って設定を変更します。

    **設定** | **詳細** | **既定値**
    --- | --- | ---
    **ターゲットの場所** | Azure 上の移行先となる場所。 |  既定の場所は、米国西部 2 です。
    **記憶域冗長** | 移行後に Azure VM で使用されるストレージの種類。 | 現在サポートされるレプリケーションは [[ローカル冗長ストレージ (LRS)]](../storage/common/storage-redundancy.md#locally-redundant-storage) のみです。
    **快適性係数** | 快適性係数は、評価中に使用されるバッファーです。 季節特有の使用方法、パフォーマンス履歴が短い、使用量が将来的に増加する可能性などを考慮に入れるために使用します。 | 既定の設定は 1.3x です。
    **パフォーマンス履歴** | パフォーマンス履歴を評価するために使用された時間です。 | 既定値は 1 か月です。
    **百分位数の使用率** | パフォーマンス履歴で考慮すべきパーセンタイル値です。 | 既定値は 95% です。
    **[価格レベル]** | VM の[価格レベル](https://azure.microsoft.com/blog/basic-tier-virtual-machines-2/) を指定することができます。  | 既定では [Standard](../virtual-machines/windows/sizes-general.md) レベルが使用されます。
    **プラン** | 適用される [Azure プラン](https://azure.microsoft.com/support/legal/offer-details/)。 | [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)が既定値です。
    **通貨** | 請求通貨です。 | 既定値は、米ドルです。
    **割引率 (%)** | プランに適用される任意のサブスクリプション固有の割引です。 | 既定の設定は 0% です。
    **Azure Hybrid 利用特典** | [Azure Hybrid 利用特典](https://azure.microsoft.com/pricing/hybrid-use-benefit/)に登録されているかどうかを示します。 [はい] に設定すると、Windows VM に Windows Azure 以外の価格は考慮されません。 | 既定値は Yes です。

3. **[保存]** をクリックして評価を更新します。


## <a name="next-steps"></a>次のステップ

評価の計算方法の[詳細を確認](concepts-assessment-calculation.md)します。
