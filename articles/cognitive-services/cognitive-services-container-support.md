---
title: Azure Cognitive Services でのコンテナーのサポート
titleSuffix: Azure Cognitive Services
description: Docker コンテナーを使用して Cognitive Services をデータに近い場所に配置する方法について説明します。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: b66d6bc93f9d2df3eccabf0f0f9edb0384bab70f
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634944"
---
# <a name="container-support-in-azure-cognitive-services"></a>Azure Cognitive Services でのコンテナーのサポート

Azure Cognitive Services でのコンテナーのサポートを使用すると、開発者は Azure で利用できるものと同じリッチな API を使用できますが、それに加えて [Docker コンテナー](https://www.docker.com/what-container)の持つ柔軟性を享受できます。 現在、コンテナー サポートは、[Computer Vision](Computer-vision/Home.md)、[Face](Face/Overview.md)、[Text Analytics](text-analytics/overview.md) など、Azure Cognitive Services のサブセットに対するプレビューで使用できます。

コンテナー化とは、アプリケーションまたはサービスとその依存関係や構成をコンテナー イメージとしてパッケージ化するソフトウェア配布のアプローチです。 コンテナー イメージは、ほとんどまたはまったく変更せずに、コンテナー ホストにデプロイできます。 コンテナーは、相互および基になるオペレーティング システムから分離され、仮想マシンよりもフット プリントが小さくなります。 短期間のタスクに対してコンテナーをコンテナー イメージからインスタンス化し、不要になったら削除できます。

こちらの[クイック ビデオ](https://azure.microsoft.com/resources/videos/containers-support-of-cognitive-services)でデモをご覧ください。

[Computer Vision](Computer-vision/Home.md)、[Face](Face/Overview.md)、[Text Analytics](text-analytics/overview.md) サービスは、[Microsoft Azure](https://azure.microsoft.com) で使用できます。 これらのサービス用の Azure リソースを作成して調べるには、[Azure portal](https://portal.azure.com/) にサインインしてください。

## <a name="features-and-benefits"></a>機能とメリット

- **データの制御**: お客様は、Cognitive Services を使用してそのデータを完全に制御できます。 これは、クラウドにデータを送信することはできないが、Cognitive Services テクノロジにアクセスする必要があるお客様にとって重要です。 ハイブリッド環境でデータ、管理、ID、セキュリティの整合性をサポートします。
- **モデルの更新の制御**: お客様は、ソリューションにデプロイされているモデルのバージョン管理と更新を柔軟に行うことができます。
- **移植可能なアーキテクチャ**: クラウド、オンプレミス、エッジにデプロイできる移植可能なアプリケーション アーキテクチャを作成できます。 コンテナーは、[Azure Kubernetes Service](/azure/aks/)、[Azure Container Instances](/azure/container-instances/)、または [Azure Stack](/azure/azure-stack/) にデプロイされた [Kubernetes](https://kubernetes.io/) クラスターに直接デプロイすることもできます。 詳しくは、「[Kubernetes を Azure Stack にデプロイする](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)」をご覧ください。
- **高スループット/低待機時間**: お客様は、アプリケーションのロジックとデータに物理的に近い Azure Kubernetes Service で Cognitive Services を実行できるようにすることで、高いスループットと低待機時間の要件に合わせてスケーリングできます。


## <a name="containers-in-azure-cognitive-services"></a>Azure Cognitive Services でのコンテナー

Azure Cognitive Services のコンテナーでは次の Docker コンテナー セットが提供され、各コンテナーには Azure Cognitive Services のサービスの機能のサブセットが含まれます。

| Service | コンテナー| 説明 |
|---------|----------|-------------|
|[Computer Vision](Computer-vision/computer-vision-how-to-install-containers.md) |**Recognize Text** |レシート、ポスター、名刺など、さまざまな表面や背景を持ついろいろなオブジェクトのイメージから、印刷されたテキストを抽出します。<br/><br/>**重要:** Recognize Text コンテナーは現在のところ、英語でのみ機能します。<br>[アクセスの要求](Computer-vision/computer-vision-how-to-install-containers.md#request-access-to-the-private-container-registry)|
|[Face](Face/face-how-to-install-containers.md) |**Face** |Face には、画像中の人の顔を検出し、顔のパーツ (鼻や目など)、性別、年齢のほか、マシンが予測するその他の顔の特徴などの属性を識別します。 検出に加えて、Face では、同じ画像または異なる画像中の 2 つの顏が同じかどうかを信頼スコアを使って確認したり、データベースと顏を比較して、似ている顏や同一の顔が既に存在するかどうかを調べたりできます。 また、同じ視覚的特徴を使用して、似た顔をグループに分けて整理することもできます。<br>[アクセスの要求](Face/face-how-to-install-containers.md#request-access-to-the-private-container-registry) |
|[Text Analytics](text-analytics/how-tos/text-analytics-how-to-install-containers.md) |**キー フレーズ抽出** ([イメージ](https://go.microsoft.com/fwlink/?linkid=2018757)) |主なポイントを識別するキー フレーズを抽出します。 たとえば、「食べ物はおいしくて、すばらしいスタッフがいた」というテキストを入力すると、この API は話題の中心として "食べ物" と "すばらしいスタッフ" を返します。 |
|[Text Analytics](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|**言語検出** ([イメージ](https://go.microsoft.com/fwlink/?linkid=2018759)) |最大 120 の言語に対して、入力テキストが書かれている言語を検出し、要求で送信されたドキュメントごとに 1 つの言語コードを報告します。 言語コードは、評価値の強度を示すスコアと組みになります。 |
|[Text Analytics](text-analytics/how-tos/text-analytics-how-to-install-containers.md)|**感情分析** ([イメージ](https://go.microsoft.com/fwlink/?linkid=2018654)) |肯定的または否定的な感情の手がかりを探して未加工のテキストを分析します。 この API はドキュメントごとに 0 から 1 までの感情スコアを返します。1 が最も肯定的となります。 分析モデルは、広範囲にわたるテキスト本文と Microsoft の自然言語技術を利用して事前トレーニングされています。 [一部の言語](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages.md)については、この API はユーザーが指定したあらゆる未加工テキストを分析し、評価し、呼び出し元のアプリケーションに結果を直接返すことができます。 |

## <a name="container-availability-in-azure-cognitive-services"></a>Azure Cognitive Services でのコンテナーの使用可能性

Azure Cognitive Services コンテナーはお使いの Azure サブスクリプションを通じてパブリックに使用でき、Docker コンテナー イメージは Microsoft Container Registry または Docker Hub からプルできます。 適切なレジストリからコンテナー イメージをダウンロードするには、[docker pull](https://docs.docker.com/engine/reference/commandline/pull/) コマンドを使用できます。

> [!IMPORTANT]
> 現在、[Face](Face/face-how-to-install-containers.md) と [Recognize Text](Computer-vision/computer-vision-how-to-install-containers.md) にアクセスするにはサインアップ プロセスを完了する必要があります。サインアップでは、お客様自身、お客様の会社、コンテナーを実装したいユース ケースに関するアンケートに答えて送信します。 アクセスを許可されて資格情報を提供されると、Azure Container Registry によってホストされているプライベート コンテナー レジストリから、Face コンテナーと Recognize Text コンテナーのコンテナー イメージをプルできます。

## <a name="prerequisites"></a>前提条件

Azure Cognitive Services コンテナーを使用するには、次の前提条件を満たしている必要があります。

**Docker エンジン**: Docker エンジンをローカルにインストールしている必要があります。 Docker には、[macOS](https://docs.docker.com/docker-for-mac/)、[Linux](https://docs.docker.com/engine/installation/#supported-platforms)、[Windows](https://docs.docker.com/docker-for-windows/) 上で Docker 環境を構成するパッケージが用意されています。 Windows では、Linux コンテナーをサポートするように Docker を構成する必要があります。 Docker コンテナーは、[Azure Kubernetes Service](/azure/aks/) または [Azure Container Instances](/azure/container-instances/) に直接デプロイできます。

コンテナーが Azure に接続して課金データを送信できるように、Docker を構成する必要があります。

**Microsoft Container Registry と Docker に関する知識**: レジストリ、リポジトリ、コンテナー、コンテナー イメージなどの Microsoft Container Registry と Docker の両方の概念の基本的な理解に加えて、基本的な `docker` コマンドの知識が必要です。

Docker やコンテナーの基礎に関する入門情報については、「[Docker overview](https://docs.docker.com/engine/docker-overview/)」(Docker の概要) を参照してください。

個々のコンテナーには、サーバーやメモリ割り当ての要件など、独自の要件もある場合があります。

## <a name="developer-samples"></a>開発者向けサンプル

開発者向けサンプルは、[GitHub リポジトリ](https://github.com/Azure-Samples/cognitive-services-containers-samples)から入手できます。

## <a name="next-steps"></a>次の手順

Azure Cognitive Services のコンテナーによって提供される機能をインストールして試してください。

* [Computer Vision コンテナーをインストールして使用する](Computer-vision/computer-vision-how-to-install-containers.md)
* [Face コンテナーをインストールして使用する](Face/face-how-to-install-containers.md)
* [Text Analytics コンテナーをインストールして使用する](text-analytics/how-tos/text-analytics-how-to-install-containers.md)
