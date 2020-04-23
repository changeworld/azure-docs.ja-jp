---
title: Azure HPC Cache の概要
description: ハイパフォーマンス コンピューティングのためのファイル アクセス アクセラレータ ソリューションである Azure HPC Cache について説明します。
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: 084c33874b474fc1789df93e088d3cec4263eac9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536643"
---
# <a name="what-is-azure-hpc-cache"></a>Azure HPC Cache とは

ハイパフォーマンス コンピューティング (HPC) タスクには、Azure HPC Cache を使用することでデータへのアクセスを高速化できます。 Azure HPC Cache で Azure 内のファイルをキャッシュすることで、クラウド コンピューティングのスケーラビリティを既存のワークフローでも実現します。 このサービスは、ローカル データセンターのネットワーク接続ストレージ (NAS) 環境などの WAN リンクにデータが格納されているワークフローでも使用できます。

Azure HPC Cache は、Azure portal から簡単に立ち上げて監視することができます。 既にある NFS ストレージや新しい BLOB コンテナーをその集約された名前空間に追加できるため、バックエンドのストレージ ターゲットを変更する場合でも、クライアント アクセスはシンプルになります。

## <a name="use-cases"></a>ユース ケース

Azure HPC Cache による生産性の向上は、次のようなワークフローで最大限に発揮されます。

* 読み取り負荷の高いファイル アクセス ワークフロー
* NFS でアクセスできるストレージ、Azure BLOB、またはその両方に格納されるデータ
* CPU のコア数が 75,000 個までのコンピューティング ファーム

Azure HPC Cache は、業種を越えてさまざまなワークフローに取り入れることができます。 多数のマシンが一連のファイルに低遅延で、かつ大規模にアクセスする必要のあるすべてのシステムで、このサービスは利便性を発揮します。 以降のセクションで具体的な例を示します。

### <a name="visual-effects-vfx-rendering"></a>視覚効果 (VFX) のレンダリング

メディアとエンターテイメントの分野では、タイムクリティカルなレンダリング プロジェクトのデータ アクセスを Azure HPC Cache によってスピードアップできます。 VFX レンダリング ワークフローでは、多数のコンピューティング ノードによる最終処理が必要になることが少なくありません。 そうしたワークフローに使用されるデータは通常、オンプレミスの NAS 環境に置かれます。 Azure HPC Cache では、そのファイル データをクラウドにキャッシュできるため、待ち時間が減り、柔軟性が向上して、オンデマンド レンダリングが実現します。

### <a name="life-sciences"></a>ライフ サイエンス

多くのライフ サイエンス ワークフローは、スケールアウト ファイル キャッシュの恩恵を受けることができます。

ゲノム解析ワークフローを Azure に移植したいと考えている研究施設は、Azure HPC Cache を使用することで簡単にシフトすることができます。 キャッシュには POSIX ファイル アクセスが用意されているため、既存のクライアント ワークフローをクラウドで実行するためにクライアント側の変更は必要ありません。

二次解析、薬理学的シミュレーション、AI による画像分析などのタスクについても、Azure HPC Cache を活用して効率を高めることができます。

### <a name="financial-services-analytics"></a>金融サービスの分析

Azure HPC Cache のデプロイは定量分析計算やリスク分析ワークロード、モンテカルロ シミュレーションをスピードアップするために役立ちます。これにより、金融サービス会社はより優れた分析情報を得て戦略的な決定を行えます。

## <a name="region-availability"></a>利用可能なリージョン

Azure HPC Cache は、次の Azure リージョンで使用できます。

| 北米      | ヨーロッパ         | アジア            | オーストラリア      |
|--------------------|----------------|-----------------|----------------|
| 米国東部            | 北ヨーロッパ   | 韓国中部   | オーストラリア東部 |
| 米国東部 2          | 西ヨーロッパ    | 東南アジア  |               |
| 米国中南部 | | | |
| 米国西部 2        | | | |

[カスタマーマネージド キー機能](customer-keys.md)は、次のリージョンでのみサポートされます。

* 米国東部
* 米国中南部
* 米国西部 2

最新情報については、[Azure HPC Cache の製品ページ](https://azure.microsoft.com/services/hpc-cache)をご覧ください。

## <a name="next-steps"></a>次のステップ

* [Azure HPC Cache の製品ページ](https://azure.microsoft.com/services/hpc-cache)を参照して、その機能を詳しく確認する
* 製品の[前提条件](hpc-cache-prereqs.md)を確認する
* Azure portal から [Azure HPC Cache を作成](hpc-cache-create.md)する
