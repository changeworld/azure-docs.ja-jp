---
title: Azure Applied AI Services とは
titleSuffix: Azure Applied AI Services
description: Applied AI Services の説明。
keywords: AI サービスの適用、人工知能、適用された AI、AI サービス、コグニティブ検索、適用された AI 機能
author: jeffme
manager: nitinme
ms.service: applied-ai-services
ms.subservice: ''
ms.topic: overview
ms.date: 05/04/2021
ms.author: jeffme
ms.openlocfilehash: 102e8717b1bb6072eb0a52efc6ac3ecdd3e9c165
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837349"
---
# <a name="what-are-azure-applied-ai-services"></a>Azure Applied AI Services とは

Azure Applied AI Services は、重要業務に AI を導入することで、開発者が素早く効果的にデータを活用するための高度なサービスです。  Azure Cognitive Services の AI API を利用して開発された Azure Applied AI Services は、メトリクスの異常の監視と分析、ドキュメント中の情報の抽出、文字起こしデータの分析による顧客の満足度の向上、教室での読み書き能力の向上、文章理解など、さまざまな重要作業で優れた効果を発揮するサービスです。 企業では従来、異なる AI スキルを持つ人材を調整したり、ビジネス ロジックを追加したり、 UI を作成したりすることで、自社の状況に合わせて、システムの開発から展開までの作業を行う必要がありました。これらは皆、多くの時間、専門知識、リソースが必要な作業です。"会社ごとの状況" に合わせたサービスを提供すれば、開発者は、こうしたものから得られる恩恵を "即座に" 手に入れることができます。

## <a name="azure-form-recognizer"></a>Azure From Recognizer

ドキュメント上の潜在的な情報を利用して生産性を高め、業務を自動化し、知識とインサイトを手に入れるための、あらゆる業界の組織を対象としたサービスです。  Azure Form Recognizer は、機械学習を利用した自動データ処理ソフトウェアを作るためのサービスです。 ドキュメントに存在するテキスト、キーと値の組み合わせ、選択マーク、表、構造を特定、抽出します。 このサービスは、ファイル、バウンディング ボックス、機密情報などの元データの要素同士の関係を含む、構造化されたデータを出力します。 手作業による操作やデータ サイエンスに関する専門知識があまりなくても、特定のコンテンツに合わせた正確な結果がすばやく得られます。 Form Recognizer でアプリケーションへのデータ入力を自動化し、ドキュメント検索機能を強化してください。  Azure Form Recognizer は、Azure Cognitive Services の OCR、Text Analytics、Custome Text を利用して開発されています。

Form Recognizer は、カスタム ドキュメント処理モデルと、請求書、レシート、ID、名刺の事前構築済みモデル、およびレイアウト モデルで構成されています。 

[Azure Form Recognizer について詳しく知る](./form-recognizer/index.yml)

## <a name="azure-metrics-advisor"></a>Azure Metrics Advisor

業務、サービス、物理的資産のメトリクスから得た知識に基づいて正しい意思決定を行うことで、組織の成長を保証します。  Azure Metrics Advisor では、AI を使用して、データ監視と時系列データの異常検出を行います。 このサービスは、データにモデルを適用するプロセスを自動化すると共に、機械学習の知識を得なくてもデータ インジェスト、異常検出、診断を行うことができる一連の API と Web ベースのワークスペースを備えています。 開発者はこのサービスを基礎にして、AIOps、予測的メンテナンス、業務監視のアプリケーションを開発できます。  Azure Metrics Advisor は、Azure Cognitive Services の Anomaly Detector を利用して開発されています。

[Azure Metrics Advisor について詳しく知る](./metrics-advisor/index.yml)

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

あらゆるコンテンツから価値のある潜在的情報を抽出し、それを行動や意思決定に利用します。  Azure Cognitive Search は、あらゆる種類の情報をエンリッチしてコンテンツを大規模に把握、探索するための組み込み AI 機能を持つ、唯一のクラウド検索サービスです。 視覚情報、言語、音声の認識能力、またはカスタム機械学習モデルを使用して、あらゆる種類のコンテンツからインサイトを引き出します。 Azure Cognitive Search にはセマンティック検索機能もあります。この機能では、高度な機械学習技術によってユーザーの意図を理解し、関連するほとんどの検索結果を文脈に基づいてランク付けします。 イノベーションに時間をかけ、複雑なクラウド検索ソリューションの保守にかける時間を減らすことができます。  Azure Cognitive Search は、Azure Cognitive Services の Computer Vision と Text Analytics を利用して開発されています。

[Azure Cognitive Search の詳細情報](../search/index.yml)

## <a name="azure-immersive-reader"></a>Azure Immersive Reader

AI で読書理解を深め、読書力を高めます。 Azure Immersive Reader は、実績に裏打ちされた技術によって、読書を始めた方、言語学習者、失読症などの学習困難がある方の読書理解を深める、インクルーシブなツールです。 Immersive Reader のクライアント ライブラリがあれば、Microsoft Word と Microsoft OneNote で使用されている技術を利用して、Web アプリケーションを強化できます。 Azure Immersive Reader は、Azure Cognitive Services の Tranlation と Text to Speech を利用して開発されています。

[Azure Immersive Reader について詳しく知る](./immersive-reader/index.yml)

## <a name="azure-bot-service"></a>Azure Bot Service

あらかじめ用意されている会話部品を利用して、すぐにビジネスに生かせる、カスタマイズ可能で洗練された会話サービスを素早く作成します。  Azure Bot Service Composer は、開発者や分野横断的なチームがボット開発を行うための、オープンソースのグラフィカルな作成キャンバスです。 Composer には LUIS や QnA Maker などの言語理解サービスが組み込まれており、言語生成技術を利用して洗練されたボット応答を作成できます。 Azure Bot Service は、Azure Cognitive Services の Speech/Telephony、LUIS、QnA Maker を利用して開発されています。

[Azure Bot Service について詳しく知る](/composer/)

## <a name="azure-video-analyzer"></a>Azure Video Analyzer 

動画や AI の専門家でなくても、ビデオ インテリジェンスによって、自動化された業務用アプリを作成できます。  Azure Video Analyzer は、AI を利用した動画ソリューションおよびアプリケーションを作るためのサービスです。 ソースに近い位置でデータを処理し、好みの AI を使用して、ビデオ ストリームからリアルタイムでビジネス インサイトを生成できます。 エッジまたはクラウドで興味のある動画を記録し、それを他のデータと組み合わせて、ビジネスの意思決定に利用します。  Azure Video Analyzer は、Azure Cognitive Services の Spatial Analysis を利用して開発されています。  Azure Video Analyzer for Media は、Azure Cognitive Services の Face、Speech, Translation, Text Analytics、Custom Vision、Textual Content Moderation を利用して開発されています。  

[Azure Video Analytics について詳しく知る](../azure-video-analyzer/index.yml)

## <a name="certifications-and-compliance"></a>認定資格とコンプライアンス

Applied AI Services は CSA STAR Certification、FedRAMP Moderate、HIPAA BAA などの認定を受けています。 自分で監査したり、セキュリティを確認する目的で認定資格を[ダウンロード](/samples/browse/?redirectedfrom=TechNet-Gallery "download")できます。

プライバシーやデータ管理は[セキュリティ センター](https://servicetrust.microsoft.com/ "トラスト センター")で確認できます。

## <a name="support"></a>サポート

Applied AI Services では、インテリジェント アプリケーション作成のサポートについて、複数のオプションを提供しています。 Applied AI Services には、独自の質問にも対応してもらえる、開発者の頼れるコミュニティがあります。 利用できるすべてのオプションについては、次をご覧ください。

- [UserVoice にフィードバックを送信する](https://aka.ms/AppliedAIUserVoice)
- [Microsoft Q&A で質問をする](/answers/topics/azure-applied-ai-services.html)
- [StackOverflow でトラブルシューティングをする](https://aka.ms/AppliedAIStackOverflow)