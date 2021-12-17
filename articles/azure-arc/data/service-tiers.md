---
title: Azure Arc 対応 SQL Managed Instance のサービス レベル
description: Azure Arc 対応 SQL Managed Instance デプロイで使用可能なサービス レベルについて説明します。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: overview
ms.openlocfilehash: f0196d2a763b27c43fedb6371668321460f09c6f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121786186"
---
# <a name="azure-arc-enabled-sql-managed-instance-service-tiers"></a>Azure Arc 対応 SQL Managed Instance のサービス レベル

Azure SQL 製品ファミリの一部として、Azure Arc 対応 SQL Managed Instance は 2 つの[仮想コア](../../azure-sql/database/service-tiers-vcore.md)のサービス レベルで利用できます。

- **General Purpose** は、一般的なパフォーマンスと可用性の機能を持つほとんどのワークロード向けに設計された、予算を抑えたレベルです。
- **Business Critical** レベルは、高い可用性機能を持つパフォーマンスに依存するワークロード用に設計されています。

現時点では、Business Critical サービス レベルは[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)です。 General Purpose サービス レベルは一般提供されています。 

Azure では、ストレージとコンピューティングは Microsoft によって提供され、各サービス レベルでのパフォーマンス、スループット、可用性などのサービス レベル アグリーメント (SLA) が保証されます。 Azure Arc 対応データ サービスでは、お客様がストレージとコンピューティングを提供します。 そのため、Azure Arc 対応データ サービスでは、保証された SLA がお客様に提供されません。 ただし、お客様はサービス レベルに関係なく、パフォーマンスの高い独自のハードウェアを柔軟に使用できます。 

## <a name="service-tier-comparison"></a>サービス レベルの比較

次に、Azure Arc 対応データ サービスの 2 つのサービス レベルで使用できるさまざまな機能について説明します。


領域 | Business Critical (プレビュー)* | 汎用
----------|-----------------|------------------
機能セット | SQL Server Enterprise Edition と同じ | SQL Server Standard Edition と同じ
CPU 制限/インスタンス | 無制限  | 24 コア
メモリ制限/インスタンス | 無制限 | 128 GB
高可用性 | 可用性グループ | Kubernetes の再デプロイと共有ストレージを使用した単一インスタンス。
読み取りスケールアウト | 可用性グループ | なし
価格の IP コンポーネントの AHB 換算レート | 1:1 Enterprise Edition <br> 4:1 Standard Edition | 1:4 Enterprise Edition <br> 1:1 Standard Edition 
Dev/Test 価格 | 無料 | 無料

\* 現在、Business Critical サービス レベルはプレビュー段階であり、このプレビュー期間中の使用に対して料金は発生しません。 一般提供に近づくにつれて、一部の機能が変更される可能性があります。

## <a name="how-to-choose-between-the-service-tiers"></a>サービス レベルの選択方法

お客様はビジネス ニーズに基づき、パフォーマンスと可用性の要件を備えた独自のハードウェアを持ち込むため、サービス レベル間の主な差別化要因は、ソフトウェア レベルで提供されるサービスになります。 

### <a name="choose-general-purpose-if"></a>General Purpose を選択すべき場合

- CPU およびメモリの要件が、General Purpose サービス レベルを満たすか、または制限の範囲内にある
- Kubernetes によって提供される高可用性オプション (ポッドの再デプロイなど) がワークロードに対して十分である
- アプリケーションに読み取りスケールアウトが必要でない
- アプリケーションでは、Business Critical サービス レベルにある機能がどれも必要ない (SQL Server Enterprise Edition と同じ)

### <a name="choose-business-critical-if"></a>Business Critical を選択すべき場合

- CPU およびメモリの要件が、General Purpose サービス レベルの制限を超えている
- アプリケーションでは、Kubernetes によって提供されるよりも高いレベルの高可用性 (アプリケーションのフェールオーバーを処理する組み込みの可用性グループなど) が必要である。 
- アプリケーションでは、読み取りスケールアウトを利用して読み取りワークロードをセカンダリ レプリカにオフロードできる
- アプリケーションで、Business Critical サービス レベルにのみ存在する機能が必要である (SQL Server Enterprise Edition と同じ)
