---
title: 統合ソリューションの公開 - Microsoft コマーシャル マーケットプレース
description: Microsoft AppSource と Azure Marketplace に統合されたソリューションを公開するための要件と手順について学習します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.date: 06/22/2021
ms.openlocfilehash: 71088777412bd99e6c306ef4da0cf06766562fbf
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232330"
---
# <a name="publish-an-integrated-solution"></a>統合ソリューションを公開する

この記事では、統合されたソリューションをコマーシャル マーケットプレースに発行するための全体的なプロセスと要件について説明します。 単一のオファーにバンドルされた統合ソリューションでは、ソフトウェア ベンダーのポイント ソリューション テクノロジと、サービス パートナーの展開とサポートを組み合わせます。 サービス パートナーは、このオファーを Microsoft AppSource または Azure Marketplace に[コンサルティング サービス](./plan-consulting-service-offer.md)として一覧表示します。

統合ソリューションは、特定の顧客のニーズやシナリオに対応することを目的としています。 プロバイダーは最初に、業界の課題や問題点を把握します。 その後、2 つ以上の最善のテクノロジーを統合して、お客様のために一元管理された再現性のあるデプロイ エクスペリエンスを作成します。 プロバイダーは、お客様の迅速な稼働を支援します。 お客様に対して単一の問い合わせ窓口を提供し、関連するカスタマイズ、コンサルティング、およびソリューションのサポートを提供します。 その成果が、テクノロジとサービスを含む統合ソリューションです。

## <a name="benefits-of-integrated-solutions-in-the-marketplace"></a>マーケットプレースにある統合ソリューションのベネフィット

* お客様は、エンドツーエンドのビジネスの課題を解決するパッケージ ソリューションをマーケットプレースを通じて見つけることができます。 テクノロジが事前に統合されているため、価値実現までの時間が短縮されます。 お客様は、断片的にデプロイされた複数のアプリを個別に取り扱わなければならない状態を回避できます。
* IP は、テクノロジの価値を高める他のソリューションやサービスと共にパッケージ化され、エキスパートのサービス パートナーによりデリバリーされます。
* サービス パートナーは、より多くの顧客アカウントでのスケーリング、ランディング、展開を実現するための最善のテクノロジを使用した、事前に定義および統合されたソリューションを作成します。
* Microsoft では、これらの事前に統合された、業界に合わせたソリューションを使用して、より簡単にお客様への共同販売を行うことができます。 価格と使用条件は事前に分類されています。

## <a name="eligibility-criteria"></a>対象となる条件

* 参加するサービス プロバイダーは、[共同販売の準備](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status)ができている必要があります。
* 参加するソフトウェア ベンダーは、[IP 共同販売の準備](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status)ができている必要があります。
* 少なくとも 1 社の参加パートナーが、次のいずれかの分野で Gold コンピテンシーを取得している必要があります。
    - アプリケーション開発
    - アプリケーションの統合
    - アプリケーション ライフサイクル管理
    - クラウド プラットフォーム
    - データ分析
    - データセンター
    - データ プラットフォーム
    - DevOps

* ソリューションに少なくとも 2 つの取引先組織が含まれ、完全な顧客ソリューションとエクスペリエンスのために IP とサービスが統合されている必要があります。
* パートナー IP は、Azure、Dynamics 365、または Power Platform に構築する必要があります。
* 統合ソリューションの IP は、単なるソフトウェア ベンダー IP やサービス プロバイダーの基本オファリングとは区別する必要があります。
* すべてのパートナーは、Microsoft AppSource、Azure Marketplace、またはその両方に一覧されて最終的に処理される、反復可能なオファリングを作成するという目標を共有します。

## <a name="best-practices-when-developing-an-integrated-solution"></a>統合ソリューションを開発するときのベスト プラクティス

1. 主要なビジネスおよび技術に関するディスカッションをワークショップ形式で実施し、ソリューションと統合へのアプローチを定義します。

    ソリューションについてのディスカッションでは、次の点を検討する必要があります。
    * Scope
    * 対象の顧客
    * 価値の提案
    * 価格
    * ライセンス
    * 使用条件
    * サポート方法
    * マーケティング活動
    * 販売プロセス
    * Activities

    統合方法についてのディスカッションでは、次の点を検討する必要があります。
    * 統合ポイント
    * ソリューションのアーキテクチャ
    * 認証
    * ユーザー インターフェイス
    * 監視
    * 展開のメカニズム
    * API
    * 統合データ リポジトリ

2. ビジネスおよび技術に関するワークショップで策定したプロジェクト計画とタイムラインを推進するために、クロスパートナーのプロジェクト マネージャーを特定します。

3. ソリューションの完全な技術的統合を開発します。

4. ソリューションの価格および Microsoft AppSource または Azure Marketplace に掲載する単一の価格ポイントを決定します。

5. Microsoft AppSource または Azure Marketplace への掲載用に、必要なマーケティング資料を完成させます。

## <a name="publish-your-integrated-solution"></a>統合ソリューションを公開する

技術統合とマーケティング資料が完了したら、[Microsoft AppSource と Azure Marketplace のコンサルティング サービス](./plan-consulting-service-offer.md)の発行元ガイドを参照して、ソリューションが Microsoft AppSource または Azure Marketplace で公開されるかどうかを判断します。 このガイドを使用して、公開成果物を準備し、公開プロセスを完了することをお勧めします。

コンサルティング サービス オファーには 5 種類のサービスが用意されていますが、統合ソリューションは概念実証または完全な実装のいずれかである必要があります。

## <a name="go-to-market-with-your-integrated-solution"></a>統合ソリューションで市場に参入する

ソリューションが Microsoft AppSource または Azure Marketplace に表示されるようになったら、マーケティング活動を通じて認知度と需要を促進することができます。 マーケットプレースで公開するパートナーのための[市場投入のベネフィット](./gtm-your-marketplace-benefits.md#list-trial-and-consulting-benefits)を利用します。

## <a name="next-steps"></a>次のステップ

- [マーケットプレースの報酬](marketplace-rewards.md)をアクティブにする
