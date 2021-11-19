---
title: Microsoft Sentinel とは | Microsoft Docs
description: スケーラブルでクラウドネイティブ型のセキュリティ情報イベント管理 (SIEM) およびセキュリティ オーケストレーション自動応答 (SOAR) ソリューションである Microsoft Sentinel について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 40cfdc1d23b57bbda16e79907d226134e0052134
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523488"
---
# <a name="what-is-microsoft-sentinel"></a>Microsoft Sentinel とは

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Sentinel は、スケーラブルでクラウドネイティブの **セキュリティ情報イベント管理 (SIEM)** および **セキュリティ オーケストレーション自動応答 (SOAR)** ソリューションです。 Microsoft Sentinel は、高度なセキュリティ分析と脅威インテリジェンスを企業全体で実現し、アラートの検出、脅威の可視性、予防的な捜索、および脅威への対応のための 1 つのソリューションを提供します。

Microsoft Sentinel を使用すると、ますます巧妙化する攻撃、増加するアラート、解決までに長い期間がかかることに伴うストレスを軽減し、企業全体を俯瞰的に見ることができます。

- **クラウドの規模でデータを収集** します。オンプレミスと複数のクラウド内の両方ですべてのユーザー、デバイス、アプリケーション、インフラストラクチャにわたって収集します。

- **過去に検出されたことのない脅威を検出** します。Microsoft の分析と類を見ない脅威インテリジェンスを使用して、[誤判定を最小限に抑え](false-positives.md)ます。

- **人工知能を使用して脅威を調査** します。Microsoft の長年にわたるサイバー セキュリティ業務を活用しながら、大規模に疑わしいアクティビティを捜索します。

- **インシデントに迅速に対応** します。一般的なタスクの組み込みのオーケストレーションとオートメーションを使用します。

![Microsoft Sentinel のコア機能](./media/overview/core-capabilities.png)

Microsoft Sentinel は、既存のさまざまな Azure サービスを基に構築され、Log Analytics や Logic Apps などの実績のある基盤をネイティブに組み込んでいます。 Microsoft Sentinel は、AI による調査と検出を強化し、Microsoft の脅威インテリジェンス ストリームを提供しています。また、ユーザーが独自の脅威インテリジェンスを取り入れることもできます。

## <a name="connect-to-all-your-data"></a>すべてのデータに接続する

Microsoft Sentinel をオンボードするには、まず[セキュリティ ソースに接続する](connect-data-sources.md)必要があります。 

Microsoft Sentinel には、Microsoft 365 Defender (旧 Microsoft Threat Protection) ソリューションや、Office 365、Azure AD、Microsoft Defender for Identity (旧 Azure ATP)、Microsoft Defender for Cloud Apps を含む Microsoft 365 ソースなど、すぐに使用できる、かつリアルタイムの統合を提供する Microsoft ソリューション用コネクタが多数付属しています。 さらに、Microsoft 以外のソリューション用のより広範なセキュリティ エコシステムへの組み込みコネクタがあります。 一般的なイベント形式 (Syslog や REST-API) を使用して、データ ソースを Microsoft Sentinel に接続することもできます。 

詳細については、「[データ コネクタを見つける](data-connectors-reference.md)」を参照してください。

![データ コレクター](./media/collect-data/collect-data-page.png)

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="workbooks"></a>Workbooks

Microsoft Sentinel に[データ ソースを接続](quickstart-onboard.md)したら、Microsoft Sentinel と Azure Monitor ブックの統合を使用してデータを監視できます。これにより、多用途のカスタム ブックを作成できます。

ブックの表示は Microsoft Sentinel では異なりますが、[Azure Monitor ブックを使用して対話型レポートを作成する](../azure-monitor/visualize/workbooks-overview.md)方法を確認すると役立つ場合があります。 Microsoft Sentinel を使用すると、データ全体に対してカスタム ブックを作成できます。また、用意されている組み込みのブック テンプレートを使用してデータ ソースに接続すると、すぐにデータ全体の分析情報をすばやく得ることもできます。

![ダッシュボード](./media/tutorial-monitor-data/access-workbooks.png)

- ブックは、あらゆる階層の SOC エンジニアとアナリストを対象としており、データの視覚化に使用されます。

- ブックは、Microsoft Sentinel データの概念図に最適であり、コーディングの知識は必要ありませんが、ブックと外部データを統合することはできません。

## <a name="analytics"></a>分析

ノイズを減らし、確認および調査する必要があるアラートの数を最小限に抑えるために、Microsoft Sentinel は[分析を使用してアラートをインシデントに関連付けます](detect-threats-built-in.md)。 **インシデント** は、調査して解決できる対応可能な脅威をまとめた、関連するアラートのグループです。 組み込みの相関関係ルールをそのまま使用するか、独自のルールを作成する際の出発点として使用します。 Microsoft Sentinel には、ネットワークの動作をマップし、リソース全体の異常を探すための機械学習ルールも用意されています。 これらの分析では、さまざまなエンティティに関する信頼度の低いアラートを組み合わせて信頼度の高いセキュリティ インシデントにすることで、点を結び付けます。

![インシデント](./media/tutorial-investigate-cases/incident-severity.png)


## <a name="security-automation--orchestration"></a>セキュリティのオートメーションとオーケストレーション

一般的なタスクを自動化し、Azure サービスと既存のツールと統合する[プレイブックを使用してセキュリティ オーケストレーションを簡略化](tutorial-respond-threats-playbook.md)します。

Azure Logic Apps を基に構築された Microsoft Sentinel のオートメーションおよびオーケストレーション ソリューションは、新しいテクノロジや脅威が発生したときにスケーラブルなオートメーションを可能にする高度に拡張可能なアーキテクチャを備えています。 Azure Logic Apps を使用してプレイブックを作成する際は、成長を続けている組み込みプレイブックのギャラリーから選択できます。 これらには、Azure Functions などのサービス用に [200 以上のコネクタ](../connectors/apis-list.md)が含まれます。 コネクタを使用すると、コード、ServiceNow、Jira、Zendesk、HTTP 要求、Microsoft Teams、Slack、Windows Defender ATP、および Defender for Cloud Apps に任意のカスタム ロジックを適用できます。

たとえば、ServiceNow チケット発行システムを使用している場合は、提供されているツールを使用して、Azure Logic Apps を使用してワークフローを自動化し、特定のイベントが検出されるたびに ServiceNow でチケットを開くことができます。

![プレイブック](./media/tutorial-respond-threats-playbook/logic-app.png)

- プレイブックは、あらゆる階層の SOC エンジニアとアナリストを対象としており、データ インジェスト、エンリッチメント、調査、修復などのタスクの自動化と簡略化に使用されます。

- プレイブックは、1 つの反復可能なタスクに最適であり、コーディングの知識は必要ありません。 プレイブックは、アドホックまたは複雑なタスク チェーンや、証拠の文書化と共有には適していません。


## <a name="investigation"></a>調査

現在プレビュー段階にある Microsoft Sentinel の[詳細調査](investigate-cases.md)ツールを使用して、潜在的なセキュリティの脅威の範囲を把握し、根本的な原因を見つけるために役立てることができます。 対話型グラフ上のエンティティを選択し、特定のエンティティについて興味がある質問をして、そのエンティティとその関連性を掘り下げて脅威の根本的な原因を突き止めることができます。 

![調査](./media/tutorial-investigate-cases/map-timeline.png)


## <a name="hunting"></a>検出

MITER フレームワークに基づく Microsoft Sentinel の[強力な捜索検索およびクエリ ツール](hunting.md)を使用すると、アラートがトリガーされる前に、組織のデータ ソース全体でセキュリティの脅威を予防的に捜索することができます。 攻撃の可能性について重要な分析情報を提供する捜索クエリがわかったら、クエリに基づいてカスタム検出ルールを作成し、セキュリティ インシデントの対応者へのアラートとしてそのような分析情報を表示することができます。 捜索中に興味深いイベントのブックマークを作成し、後で戻って他のユーザーと共有し、他の関連イベントとグループ化して、説得力のある調査のインシデントを作成することができます。

![ハンティング機能の概要](./media/overview/hunting.png)

## <a name="notebooks"></a>ノートブック

Microsoft Sentinel は、機械学習、視覚化、およびデータ分析のための完全なライブラリを含む、Azure Machine Learning ワークスペースの Jupyter ノートブックをサポートしています。

[Microsoft Sentinel でノートブックを使用して](notebooks.md)、Microsoft Sentinel データで実行できることのスコープを拡張します。 たとえば、Microsoft Sentinel に組み込まれていない分析を実行する (一部の Python の機械学習機能など)、Microsoft Sentinel に組み込まれていないデータの視覚化を作成する (カスタムのタイムラインやプロセス ツリーなど)、Microsoft Sentinel の外部のデータソースを統合する (オンプレミスのデータ セットなど) ことができます。

:::image type="content" source="media/notebooks/sentinel-notebooks-on-machine-learning.png" alt-text="AML ワークスペースの Sentinel ノートブックのスクリーンショット。":::

- Microsoft Sentinel ノートブックは、脅威の検出担当者、層 2 から 3 のアナリスト、インシデントの調査担当者、データ サイエンティスト、セキュリティ研究者を対象としています。

- ノートブックは、Microsoft Sentinel と外部データの両方へのクエリ、データ エンリッチメント、調査、視覚化、検出、機械学習、ビッグ データ分析のための機能を備えています。

- ノートブックは、反復可能なタスクのより複雑なチェーン、アドホックな手続き型制御、機械学習とカスタム分析に最適であり、データの操作と視覚化のための豊富な Python ライブラリをサポートし、分析の証拠の文書化と共有に役立ちます。

- ノートブックには、より高度な学習曲線とコーディングの知識が必要であり、自動化のサポートは限られています。


## <a name="community"></a>コミュニティ

Microsoft Sentinel コミュニティは、脅威の検出とオートメーションのための強力なリソースです。 Microsoft のセキュリティ アナリストは、新しいブック、プレイブック、捜索クエリなどを常に作成して追加し、お客様の環境で使用できるようにそれらをコミュニティに投稿しています。 プライベート コミュニティの GitHub [リポジトリ](https://aka.ms/asicommunity)からサンプル コンテンツをダウンロードして、Microsoft Sentinel 用のカスタム ブック、捜索クエリ、ノートブック、プレイブックを作成できます。

![ユーザー コミュニティを探索する](./media/overview/community.png)

## <a name="next-steps"></a>次のステップ

- Microsoft Sentinel を使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがない場合は、 [無料試用版](https://azure.microsoft.com/free/)にサインアップできます。
- [データを Microsoft Sentinel にオンボード](quickstart-onboard.md)し、[データや潜在的な脅威を視覚化する](get-visibility.md)方法を確認します。
