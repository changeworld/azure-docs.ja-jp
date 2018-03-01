---
title: "Azure Migrate の評価の設定をカスタマイズする | Microsoft Docs"
description: "Azure Migration Planner を使用して VMware VM を Azure に移行するための、評価の設定と実行方法について説明します。"
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 06/02/2017
ms.author: raynew
ms.openlocfilehash: 8babdbc30e062c7b289e90a674cec3222943e48d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="customize-an-assessment"></a>評価のカスタマイズ

[Azure Migrate](migrate-overview.md) は、既定のプロパティで評価を作成します。 評価を作成した後は、この記事の手順に従って、既定のプロパティを変更できます。


## <a name="edit-assessment-properties"></a>評価のプロパティの編集

1. 移行プロジェクトの **[評価]** ページで、評価を選択し、**[プロパティの編集]** をクリックします。
2. 次の表に従ってプロパティを変更します。

    **設定** | **詳細** | **既定値**
    --- | --- | ---
    **ターゲットの場所** | Azure 上の移行先となる場所。 |  既定の場所は、米国西部 2 です。
    **ストレージ冗長** | 移行後に Azure VM で使用されるストレージ冗長の種類。 | [ローカル冗長ストレージ (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) が既定値です。 Azure Migrate では管理ディスク ベースの評価だけがサポートされ、管理ディスクでは LRS だけがサポートされています。そのため、現在、プロパティには LRS オプションだけがあります。 
    **サイズ変更の設定基準** | Azure 用に VM を適切なサイズにするために Azure Migrate によって使用される基準。 "*パフォーマンスに基づく*" サイズ変更、またはパフォーマンスの履歴を考慮しない "*オンプレミスとしての*" VM のサイズ変更を実行できます。 | パフォーマンスに基づくサイズ変更が既定のオプションです。
    **パフォーマンス履歴** | VM のパフォーマンスを評価するために考慮する期間。 このプロパティは、サイズ変更の基準が "*パフォーマンスに基づくサイズ変更*" の場合にのみ適用されます。 | 既定値は 1 日です。
    **百分位数の使用率** | 適切なサイズ設定のために考慮するパフォーマンス サンプル セットのパーセンタイル値。 このプロパティは、サイズ変更の基準が "*パフォーマンスに基づくサイズ変更*" の場合にのみ適用されます。  | 既定は 95 パーセンタイルです。
    **[価格レベル]** | ターゲット Azure VM の[価格レベル (Basic/Standard)](../virtual-machines/windows/sizes-general.md) を指定できます。 たとえば、運用環境の移行を計画している場合は、Standard レベルを検討するかもしれません。この場合、VM の待ち時間は短くなりますが、コストは高くなります。 一方、開発/テスト環境の場合は、Basic レベルを検討するかもしれません。この場合、VM の待ち時間は長くなり、コストは安くなります。 | 既定では [Standard](../virtual-machines/windows/sizes-general.md) レベルが使用されます。
    **快適性係数** | Azure Migrate では、評価時にバッファー (快適性係数) が考慮されます。 VM のマシン使用率データ (CPU、メモリ、ディスク、ネットワーク) に加えて、このバッファーが適用されます。 快適性係数は、季節ごとの使用量、短期間のパフォーマンス履歴、将来に使用量が増える可能性などの問題に相当します。<br/><br/> たとえば、使用率 20% の 10 コア VM の結果は、通常 2 コア VM になります。 一方、快適性係数を 2.0x とした場合は、結果が 4 コア VM になります。 | 既定の設定は 1.3x です。
    **プラン** | 登録されている [Azure プラン](https://azure.microsoft.com/support/legal/offer-details/)。 | [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/)が既定値です。
    **通貨** | 請求通貨です。 | 既定値は、米ドルです。
    **割引率 (%)** | Azure プランに適用される任意のサブスクリプション固有の割引です。 | 既定の設定は 0% です。
    **Azure ハイブリッド特典** | ソフトウェア アシュアランスがあり、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-use-benefit/)を受ける資格があるかどうかを指定します。 [はい] に設定すると、Windows VM に Windows Azure 以外の価格は考慮されません。 | 既定値は Yes です。

3. **[保存]** をクリックして評価を更新します。


## <a name="next-steps"></a>次の手順

評価の計算方法の[詳細を確認](concepts-assessment-calculation.md)します。
