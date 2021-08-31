---
title: Azure Sentinel における脅威インテリジェンスについて | Microsoft Docs
description: データの分析、脅威の検出、アラートの強化を行うために、Azure Sentinel で脅威インテリジェンス フィードを接続、管理、使用する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2021
ms.author: yelevin
ms.openlocfilehash: 6ab9ecbe3b67ec933604ab1d8f6efdc7dbd8a5af
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737523"
---
# <a name="understand-threat-intelligence-in-azure-sentinel"></a>Azure Sentinel の脅威インテリジェンスについて

## <a name="introduction-to-threat-intelligence"></a>脅威インテリジェンスの概要

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

サイバー脅威インテリジェンス (CTI) は、システムとユーザーに対する既存または潜在的な脅威について説明する情報です。 この種の情報には、特定の脅威アクターの動機、インフラストラクチャ、手法を詳細に説明したレポートから、既知のサイバー脅威に関連する IP アドレス、ドメイン、ファイル ハッシュ、その他の成果物に関する具体的な観測結果まで、さまざまな形式があります。 CTI は、通常とは異なるアクティビティに対して重要なコンテキストを提供するために組織によって使用されます。これにより、セキュリティ担当者は、ユーザー、情報、その他の資産を保護する措置を迅速に講じることができます。 CTI は、オープンソースのデータ フィード、脅威インテリジェンス共有コミュニティ、商用インテリジェンス フィード、組織内のセキュリティ調査の過程で集められたローカル インテリジェンスなど、多くのソースから取得できます。

Azure Sentinel のようなセキュリティ情報イベント管理 (SIEM) ソリューションで最も一般的に使用される CTI の形式は、セキュリティ侵害のインジケーター (IoC) とも呼ばれる脅威インジケーターです。 脅威インジケーターは、URL、ファイル ハッシュ、IP アドレスなどの観測された成果物を、フィッシング、ボットネット、マルウェアなどの既知の脅威アクティビティに関連付けるデータです。 この形式の脅威インテリジェンスは、組織に対する潜在的な脅威を検出して、それらから保護するために、セキュリティ製品と自動化に大規模に適用できるため、戦術的脅威インテリジェンスと呼ばれることがよくあります。 Azure Sentinel では、脅威インジケーターを使用して、お使いの環境で観察された悪意のあるアクティビティを検出し、セキュリティ調査担当者にコンテキストを提供して、応答の決定に役立てることができます。

次のアクティビティを通して脅威インテリジェンス (TI) を Azure Sentinel に統合できます。

- さまざまな TI [プラットフォーム](connect-threat-intelligence-tip.md)および [フィード](connect-threat-intelligence-taxii.md)に対して **データ コネクタ** を有効化して、Azure Sentinel に **脅威インテリジェンスをインポートします**。
- インポートした脅威インテリジェンスを Azure Sentinel の **[ログ]** および **[脅威インテリジェンス]** のブレードに **表示して、管理します**。
- **脅威を検出し**、インポートした脅威インテリジェンスに基づいて、組み込みの **分析** ルール テンプレートを使用してセキュリティのアラートとインシデントを生成します。
- Azure Sentinel で、**脅威インテリジェンス ブック** を使用して、インポートした脅威インテリジェンスに関する **重要な情報を視覚化します**。

また、脅威インテリジェンスによって、 **[ハンティング]** や **[ノートブック]** などの他の Azure Sentinel エクスペリエンスにも有用なコンテキストが提供されます。これらのエクスペリエンスについては、この記事では取り上げていませんが、Notebooks 内での CTI の使用について書かれた、[Azure Sentinel での Jupyter Notebooks](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239) に関する Ian Hellen による優れたブログ記事で取り上げられています。

## <a name="import-threat-intelligence-with-data-connectors"></a>データ コネクタを使用して脅威インテリジェンスをインポートする

Azure Sentinel の他のすべてのイベント データと同様に、脅威インジケーターはデータ コネクタを使用してインポートされます。 Azure Sentinel には、脅威インジケーター用に特別に用意された 2 つのデータ コネクタがあります。業界標準の STIX/TAXII フィード用の **脅威インテリジェンス - TAXII** と、統合およびキュレーションされた TI フィード用の **脅威インテリジェンス プラットフォーム** です。 組織の脅威インジケーターの取得元となる場所に応じて、どちらかのデータ コネクタを単独で使用することも、両方のコネクタを一緒に使用することもできます。 

Azure Sentinel で利用できる、[脅威インテリジェンスの統合](threat-intelligence-integration.md)に関するカタログを参照してください。

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>脅威インテリジェンス プラットフォーム データ コネクタを使用して Azure Sentinel に脅威インジケーターを追加する

多くの組織では、脅威インテリジェンス プラットフォーム (TIP) ソリューションを使用して、さまざまなソースからの脅威インジケーター フィードを集約し、プラットフォーム内のデータをキュレーションしてから、ネットワーク デバイス、EDR/XDR ソリューション、Azure Sentinel のような SIEM など、さまざまなセキュリティ ソリューションに適用する脅威インジケーターを選択します。 [統合された TIP ソリューション](connect-threat-intelligence-tip.md)を組織で使用している場合は、**脅威インテリジェンス プラットフォーム データ コネクタ** でその TIP を使用して、Azure Sentinel に脅威インジケーターをインポートできます。 

TIP データ コネクタは [Microsoft Graph Security tiIndicators API](/graph/api/resources/tiindicator) と連携してこれを実現するため、tiIndicators API とやり取りする任意のカスタム脅威インテリジェンス プラットフォームで使用して、Azure Sentinel (および Microsoft 365 Defender などの他の Microsoft セキュリティ ソリューション) にインジケーターを送信することもできます。

:::image type="content" source="media/understand-threat-intelligence/threat-intel-import-path.png" alt-text="脅威インテリジェンスのインポート パス":::

Azure Sentinel と統合された TIP ソリューションの詳細については、「[統合された脅威インテリジェンス プラットフォーム製品](threat-intelligence-integration.md#integrated-threat-intelligence-platform-products)」を参照してください。

統合された TIP またはカスタム脅威インテリジェンス ソリューションから Azure Sentinel に脅威インジケーターをインポートするために実行する必要がある主要な手順を以下に示します。

1. Azure Active Directory から **アプリケーション ID** と **クライアント シークレット** を取得する

1. この情報を TIP ソリューションまたはカスタム アプリケーションに入力する

1. Azure Sentinel で脅威インテリジェンス プラットフォーム データ コネクタを有効にする

これらの各手順の詳細については、[Azure Sentinel への脅威インテリジェンス プラットフォームの接続](connect-threat-intelligence-tip.md)に関する記事を参照してください。


### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>脅威インテリジェンス - TAXII データ コネクタを使用して Azure Sentinel に脅威インジケーターを追加する

脅威インテリジェンスの送信用に最も広く採用されている業界標準は、[STIX データ形式と TAXII プロトコルの組み合わせ](https://oasis-open.github.io/cti-documentation/)です。 組織が現在の STIX/TAXII バージョン (2.0 または 2.1) をサポートするソリューションから脅威インジケーターを取得している場合、**脅威インテリジェンス - TAXII** データ コネクタを使用して、脅威インジケーターを Azure Sentinel に取り込むことができます。 脅威インテリジェンス - TAXII データ コネクタを使用すると、Azure Sentinel に組み込みの TAXII クライアントで、TAXII 2.x サーバーから脅威インテリジェンスをインポートできます。

:::image type="content" source="media/understand-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="TAXII のインポート パス":::
 
TAXII サーバーから Azure Sentinel に STIX 形式の脅威インジケーターをインポートするには、次の手順に従います。

1. TAXII サーバー API ルートとコレクション ID を取得する

1. Azure Sentinel で脅威インテリジェンス - TAXII データ コネクタを有効にする

これらの各手順の詳細については、[STIX/TAXII 脅威インテリジェンス フィードへの Azure Sentinel の接続](connect-threat-intelligence-taxii.md)に関する記事を参照してください。

## <a name="view-and-manage-your-threat-indicators"></a>脅威インジケーターを表示および管理する

使用するソース フィードやコネクタに関係なく、正常にインポートされた脅威インジケーターを、 **[ログ]** (ここにすべての Azure Sentinel イベント データが格納されています) の **[ThreatIntelligenceIndicator]** テーブル ( **[Azure Sentinel]** グループの下) に表示できます。 このテーブルは、他の Azure Sentinel 機能 (分析やブックなど) によって実行される脅威インテリジェンス クエリの基礎となります。

結果は、次に示す脅威インジケーターの例のようになります。

:::image type="content" source="media/understand-threat-intelligence/threat-intel-sample-query.png" alt-text="サンプル クエリ データ":::

また、Azure Sentinel のメイン メニューからアクセスできる新しい **[脅威インテリジェンス]** ブレードで、インジケーターを表示し、管理することもできます。 Log Analytics クエリを作成しなくても、インポートされた脅威インジケーターの並べ替え、フィルター処理、検索を行うことができます。 この機能では、Azure Sentinel インターフェイスで直接脅威インジケーターを作成できます。また、インジケーターのタグ付けやセキュリティ調査に関連した新しいインジケーターの作成という最も一般的な 2 つの脅威インテリジェンス管理タスクを実行することもできます。

タグ付けは、脅威インジケーターをグループ化して見つけやすくする簡単な方法です。 通常は、特定のインシデントに関連するインジケーター、または特定の既知のアクターや既知の攻撃キャンペーンからの脅威を表すインジケーターにタグを適用することができます。 脅威インジケーターは個別にタグ付けすることも、インジケーターを複数選択してすべて一度にタグ付けすることもできます。 複数のインジケーターにインシデント ID をタグ付けする例を次に示します。 タグ付けは自由形式であるため、脅威インジケーター タグの標準的な名前付け規則を作成することをお勧めします。 各インジケーターに複数のタグを適用できます。

:::image type="content" source="media/understand-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="脅威インジケーターにタグを適用する" lightbox="media/understand-threat-intelligence/threat-intel-tagging-indicators.png":::

脅威インジケーターの表示と管理の詳細については、[Azure Sentinel での脅威インジケーターの操作](work-with-threat-indicators.md#view-your-threat-indicators-in-azure-sentinel)に関する記事を参照してください。

## <a name="detect-threats-with-threat-indicator-based-analytics"></a>脅威インジケータに基づく分析を使用して脅威を検出する

Azure Sentinel のような SIEM ソリューションでの脅威インジケーターの最も重要なユース ケースは、脅威を検出するための分析ルールの強化です。 このようなインジケーターベースのルールでは、データ ソースで発生した未加工のイベントが脅威インジケーターと比較され、組織内のセキュリティ上の脅威が検出されます。 Azure Sentinel の **[分析]** で、スケジュールに従って実行され、セキュリティ アラートを生成する分析ルールを作成します。 ルールは、ルールを実行する頻度、セキュリティ アラートとインシデントを生成するクエリ結果の種類、それに応じてトリガーされる自動化 (ある場合) を決定する構成とともに、クエリによって駆動されます。

新しい分析ルールを最初から作成することは常に可能ですが、Azure Sentinel には、Microsoft セキュリティ エンジニアが作成した組み込みのルール テンプレートのセットが用意されていて、それらをそのまま使用することも、ニーズに合わせて変更することもできます。 脅威インジケーターを使用するルール テンプレートは、すべて "**TI map**..." (TI で ... にマップする) で始まるタイトルが付いているため、簡単に特定できます。 これらのルール テンプレートはすべて同じように動作しますが、使用される脅威インジケーターの種類 (ドメイン、電子メール、ファイル ハッシュ、IP アドレス、または URL) と照合するイベントの種類が異なります。 各テンプレートには、ルールが機能するために必要なデータ ソースが一覧表示されます。そのため、必要なイベントが Azure Sentinel に既にインポートされているかどうかを一目で確認できます。 既存のルール テンプレートを編集して保存するか、新しいルールを作成すると、それは既定で有効になります。

Azure Sentinel の **[分析]** セクションにある **[アクティブな規則]** タブで、有効になっているルールを確認できます。 そこからアクティブなルールの編集、有効化、無効化、複製、または削除を行うことができます。 新しいルールはアクティブ化すると直ちに実行され、それ以降は定義したスケジュールで実行されます。

既定の設定に従うと、ルールがスケジュールに従って実行されるたびに、結果が見つかった場合はセキュリティ アラートが生成されます。 Azure Sentinel のセキュリティ アラートは、Azure Sentinel の **[ログ]** セクションで、**Azure Sentinel** グループの下にある **SecurityAlert** テーブルで確認できます。

Azure Sentinel では、分析ルールから生成されたアラートでもセキュリティ インシデントが生成され、Azure Sentinel メニューの **[脅威管理]** にある **[インシデント]** で確認できます。 インシデントは、セキュリティ運用チームが、適切な応答アクションを判断するためにトリアージして調査する対象です。 詳細については、こちらの「[チュートリアル: Azure Sentinel でインシデントを調査する](./investigate-cases.md)」を参照してください。

分析ルールでの脅威インジケーターの使用の詳細については、[Azure Sentinel での脅威インジケーターの操作](work-with-threat-indicators.md#detect-threats-with-threat-indicator-based-analytics)に関する記事を参照してください。

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>ブックによって脅威インテリジェンスに関する分析情報を提供する

ブックには、Azure Sentinel のあらゆる側面に関する分析情報を提供する、強力な対話型ダッシュボードが用意されており、脅威インテリジェンスも例外ではありません。 組み込みの **脅威インテリジェンス ブック** を使用して、脅威インテリジェンスに関する重要な情報を視覚化したり、ビジネス ニーズに応じて簡単にブックをカスタマイズしたりすることができます。 多くのさまざまなデータソースを組み合わせて、新しいダッシュボードを作成することもできます。これにより、独自の方法でデータを視覚化できます。 Azure Sentinel ブックは Azure Monitor ブックに基づいているため、広範なドキュメントが既にあり、さらに多くのテンプレートを利用できます。 [Azure Monitor ブックを使用した対話型レポートの作成](../azure-monitor/visualize/workbooks-overview.md)の方法に関するこの記事から読み始めることをお勧めします。 

また、[GitHub 上に Azure Monitor ブック](https://github.com/microsoft/Application-Insights-Workbooks)の活発なコミュニティがあり、追加のテンプレートをダウンロードしたり、独自のテンプレートを投稿したりすることができます。

脅威インテリジェンス ブックの使用とカスタマイズの詳細については、[Azure Sentinel での脅威インジケーターの操作](work-with-threat-indicators.md#workbooks-provide-insights-about-your-threat-intelligence)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、脅威インテリジェンス ブレードを含め、Azure Sentinel の脅威インテリジェンス機能について説明しました。 Azure Sentinel の脅威インテリジェンス機能の使用に関する実用的なガイダンスについては、次の記事を参照してください。

- Azure Sentinel を [STIX/TAXII 脅威インテリジェンス フィード](./connect-threat-intelligence-taxii.md)に接続する。
- Azure Sentinel に[脅威インテリジェンス プラットフォームを接続する](./connect-threat-intelligence-tip.md)。
- Azure Sentinel と容易に統合できる [TIP プラットフォーム、TAXII フィード、エンリッチメント](threat-intelligence-integration.md)を確認する。
- Azure Sentinel エクスペリエンス全体で[脅威インジケーターを操作する](work-with-threat-indicators.md)。
- Azure Sentinel で[組み込み](./detect-threats-built-in.md)または[カスタム](./detect-threats-custom.md)の分析ルールを使用して脅威を検出する
- Azure Sentinel で[インシデントを調査する](./investigate-cases.md)。
