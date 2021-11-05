---
title: Microsoft Defender for Cloud のワークロード保護ダッシュボードとその機能
description: Microsoft Defender for Cloud のワークロード保護ダッシュボードの機能について説明します
author: memildin
ms.author: memildin
ms.date: 11/02/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: bd34ab6aed35ab9798552837112830e77595795a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131463546"
---
# <a name="the-workload-protections-dashboard"></a>ワークロード保護のダッシュボード

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

このダッシュボードには、次の機能があります。

- さまざまなリソースの種類にわたる Microsoft Defenderの対象範囲の可視化
- 高度な脅威防止機能を構成するためのリンク
- オンボード状態とエージェントのインストール
- 脅威検出アラート 

ワークロード保護ダッシュボードにアクセスするには、Defender for Cloud のメニューの [クラウド セキュリティ] セクションから **[ワークロード保護]** を選択します。

## <a name="whats-shown-on-the-dashboard"></a>ダッシュボードに表示される内容

:::image type="content" source="./media/workload-protections-dashboard/sample-defender-dashboard-numbered.png" alt-text="Defender for Cloud のワークロード保護ダッシュボードの例" lightbox="./media/workload-protections-dashboard/sample-defender-dashboard-numbered.png":::

ダッシュボードには次のセクションがあります。

1. **[Microsoft Defender coverage]** - ここでは、サブスクリプション内にあり、Defender for Cloudによる保護の対象となるリソースの種類を確認できます。 必要に応じて、アップグレードすることもできます。 対象となる可能性のあるすべてのリソースをアップグレードする場合は、 **[すべてをアップグレード]** を選択します。

2. **[セキュリティのアラート]** - Defender for Cloud によって環境のいずれかの領域で脅威が検出されると、セキュリティ アラートが生成されます。 これらのアラートでは、影響を受けるリソースや推奨される修復手順の詳細と、場合によっては、ロジック アプリを応答でトリガーするオプションが示されます。 このグラフ内の任意の場所を選択すると、 **[セキュリティのアラート] ページ** が開きます。

3. **[高度な保護]** - Defender for Cloud には、仮想マシン、SQL データベース、コンテナー、Web アプリケーション、ネットワークなどを対象とする多数の高度な脅威防止機能が用意されています。 この [高度な保護] セクションでは、これらの各保護について、選択したサブスクリプション内のリソースの状態を確認できます。 それらのいずれかを選択して、その保護の種類の構成領域に直接移動します。

4. **[Insights]\(インサイト\)** - ニュース、お勧めの記事、優先度の高いアラートのこのローリング ペインでは、お客様とお客様のサブスクリプションに関連する緊急のセキュリティの問題に関するDefender for Cloudの分析情報が提供されます。 脆弱性分析ツールによって VM で検出された重大度の高い CVE の一覧も、Defender for Cloud チームのメンバーによる新しいブログ記事も、 インサイト パネルで確認できます。




## <a name="next-steps"></a>次の手順

この記事では、ワークロード保護ダッシュボードについて学習しました。 

> [!div class="nextstepaction"]
> [強化された保護を有効にする](enable-enhanced-security.md)

Microsoft Defender の高度な保護の詳細については、[Microsoft Defender for Cloud の 概要」を参照してください。](defender-for-cloud-introduction.md)