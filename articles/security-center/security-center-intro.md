---
title: "Azure Security Center 入門 | Microsoft Docs"
description: "Azure Security Center の主な機能とそのしくみについて説明します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: terrylan
ms.openlocfilehash: 21415af0d449d639d000e07afdb4de3680a64774
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-security-center"></a>Azure Security Center 入門
Azure Security Center の主な機能とそのしくみについて説明します。

## <a name="what-is-azure-security-center"></a>Azure Security Center とは
Azure Security Center は、Azure、オンプレミス、他のクラウドで実行されているワークロードの統合セキュリティ管理と高度な脅威保護を実現します。  ハイブリッド クラウド ワークロードの可視化と制御、脅威にさらされる機会を減らす積極的防御、急速に進化するサイバー攻撃への対応に役立つインテリジェント検出などの機能が提供されます。

Security Center の [概要] では、Azure と Azure 以外のワークロードのセキュリティ対策を簡単に確認できるので、ワークロードのセキュリティを検出して評価したり、リスクを特定して軽減したりできます。

![概要](./media/security-center-intro/security-center-intro-fig1.png)

## <a name="why-use-security-center"></a>Security Center を使う意義

**統一された可視性と制御**

- **ハイブリッド ワークロード全体のセキュリティ状態を把握**します。 すべてのハイブリッド クラウド ワークロード (オンプレミス、Azure、および他のクラウド プラットフォーム) のセキュリティを 1 つのコンソールで管理できます。 組み込みのダッシュボードにより、注意が必要なセキュリティの問題に関するインサイトが即座に得られます。
- **クラウド ワークロードの可視性**。 急速に変化するクラウド ワークロードを正確に把握します。 Azure サブスクリプションで作成された新しいリソースを自動的に検出し、追加します。
- **一元化されたポリシー管理**:  すべてのハイブリッド クラウド ワークロードのセキュリティ ポリシーを一元的に管理することで、会社や規制のセキュリティ要件に確実に準拠できます。
- **さまざまなソースのセキュリティ データ**:  接続されたパートナー ソリューション (ネットワーク ファイアウォールなど) や他の Microsoft サービスなど、さまざまなソースからセキュリティ データを収集、検索、分析できます。 
- **既存のセキュリティ ワークフローとの統合**:  REST API を使用して既存のツールやプロセスに接続することで、セキュリティ情報にアクセスし、情報を統合、分析できます。
- **コンプライアンス レポート**:  セキュリティ データとインサイトを使用して、監査担当者に対してコンプライアンスを実証し、証拠を簡単に生成できます。

**アダプティブ脅威に対する防護**

- **継続的なセキュリティ評価**:  数百もの組み込みのセキュリティ評価を使用するか、独自のセキュリティ評価を作成して、マシン、ネットワーク、Azure サービスのセキュリティを監視できます。 攻撃に対して脆弱なソフトウェアと構成を特定できます。
- **実行可能な推奨事項**:  優先順位が付けられた実行可能なセキュリティの推奨事項と組み込みの自動プレイブックにより、攻撃者が悪用する前にセキュリティの脆弱性を修復できます。
- **アダプティブ アプリケーション制御**:  特定の Azure ワークロードに適応し、機械学習を活用したホワイトリスト登録の推奨事項を適用することで、マルウェアや他の望ましくないアプリケーションをブロックします。 
- **ネットワーク アクセスのセキュリティ**:  Azure VM の管理ポートへの Just-In-Time で制御されたアクセスによってネットワーク攻撃対象領域を縮小することで、ブルート フォース攻撃などのネットワーク攻撃にさらされる機会を大幅に減らします。

**インテリジェントな脅威検出と対応**

- **業界で最も広範な脅威インテリジェンス**:  Microsoft サービスや世界中のシステムからの膨大な数の信号を使用して新しい脅威や進化する脅威を特定する、Microsoft インテリジェント セキュリティ グラフを活用できます。
- **高度な脅威検出**:  組み込みの行動分析と機械学習を使用して、各種攻撃やゼロデイ攻撃を特定します。 ネットワーク、マシン、クラウド サービスに対する攻撃や侵害後のアクティビティを監視します。
- **優先順位が付けられたアラートとインシデント**。 優先順位が付けられたセキュリティ アラートと、各種アラートを 1 つの攻撃キャンペーンにマップするセキュリティ インシデントにより、最も重大な脅威にまず重点を置きます。 独自のカスタム セキュリティ アラートも作成できます。
- **調査の合理化**:  視覚的な対話型エクスペリエンスにより、攻撃の範囲と影響をすばやく評価できます。 定義済みのクエリやアドホック クエリを使用して、セキュリティ データをさらに詳しく調べことができます。 
- **状況に応じた脅威インテリジェンス**:  対話型の世界地図上で攻撃元を視覚化します。 組み込みの脅威インテリジェンス レポートを使用することで、既知の悪意のあるアクターの手法と目的に関する貴重なインサイトが得られます。

## <a name="get-started"></a>作業開始
Security Center を使用するには、Microsoft Azure のサブスクリプションが必要です。 Security Center は、Azure サブスクリプションがある場合に有効です。 サブスクリプションがない場合は、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。 

[Azure Security Center の概要](https://docs.microsoft.com/azure/security-center/security-center-get-started) に関する記事では、Security Center のセキュリティ監視とポリシー管理のコンポーネントについて簡単に説明しています。 


## <a name="next-steps"></a>次のステップ
このドキュメントでは、Security Center の概略のほか、その主な機能と使用を開始する方法を紹介しました。 詳細については、次のリソースを参照してください。

* [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md) — 組織のセキュリティ要件とクラウド管理モデルに基づいて、Security Center の利用を最適化する方法について説明します。
* [セキュリティ ポリシーの設定](https://docs.microsoft.com/azure/security-center/security-center-policies)  — Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明します。
* [セキュリティに関する推奨事項の管理](https://docs.microsoft.com/azure/security-center/security-center-recommendations) — 推奨事項に従って Azure および Azure 以外のリソースを保護する方法について説明します。
* [セキュリティ正常性の監視](https://docs.microsoft.com/azure/security-center/security-center-monitoring) — Azure および Azure 以外のリソースの正常性を監視する方法について説明します。
* [セキュリティの警告の管理と対応](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) -- セキュリティの警告の管理と対応の方法について説明します。
* [パートナー ソリューションの監視](https://docs.microsoft.com/azure/security-center/security-center-partner-solutions) -- パートナー ソリューションの正常性状態を監視する方法について説明します。
* [Security Center のよくあるご質問 (FAQ)](https://docs.microsoft.com/azure/security-center/security-center-faq) — Security Center の使用に関してよく寄せられる質問が記載されています。


