---
title: Azure Percept Vision AI モジュール
description: Azure Percept DK での AI ビジョン ワークロードの実行を担当するモジュールである azureeyemodule の概要。
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: overview
ms.date: 08/09/2021
ms.custom: template-overview
ms.openlocfilehash: b09c5eb0dc2fa977c544d7da8178408dadcd08a6
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222376"
---
# <a name="azure-percept-vision-ai-module"></a>Azure Percept Vision AI モジュール

azureeyemodule は、Azure Percept DK での AI ビジョン ワークロードの実行を担当するエッジ モジュールの名前です。 これは、エッジ モジュールの Azure IoT スイートの一部であり、[セットアップ エクスペリエンス](./quickstart-percept-dk-set-up.md)中に Azure Percept DK にデプロイされます。 この記事では、このモジュールとそのアーキテクチャの概要を示します。

## <a name="architecture"></a>アーキテクチャ

:::image type="content" source="media/azureeyemodule-overview/azureeyemodule-architecture.png" alt-text="azureeyemodule のアーキテクチャを示す図。":::

Azure Percept DK 上の Azure Percept ワークロードは、azureeyemodule Docker コンテナー内で実行される C++ アプリケーションです。 画像処理とモデルの実行には、OpenCV GAPI が使用されます。 azureeyemodule は、Azure Percept DK で実行されるモジュールの Azure IoT スイートの一部として、Mariner オペレーティング システムで実行されます。

Azure Percept ワークロードは、イメージを取り込み、イメージとメッセージを出力するように意図されています。 出力画像は、境界ボックス、セグメンテーション マスク、結合部、ラベルなどの描画でマークされている場合があります。 出力メッセージは、ダウンストリーム タスクで取り込んで使用できる推論結果の JSON ストリームです。
結果は、デバイスのポート 8554 で使用できる RTSP ストリームとして提供されます。 また、この結果は、デバイスで実行されている別のモジュールにも送信されます。それは、ポート 3000 で実行されている、HTTP サーバーにラップされた RTSP ストリームを提供します。 どちらの場合も、それらはローカル ネットワークでのみ表示できます。

> [!CAUTION]
> RTSP フィードに関して暗号化や認証は "*ありません*"。 Web ブラウザーまたは RTSP メディア プレーヤーに正しいアドレスを入力すれば、ローカル ネットワーク上の誰もが、Azure Percept Vision で映し出されているものとまったく同じものを見ることができます。

Azure Percept ワークロードでは、エンド ユーザーが利用できるいくつかの機能が有効になります。
- オブジェクトの分類や一般的な物体検出など、一般的なコンピューター ビジョンのユース ケースを対象としたコード不要ソリューション。
- 開発者が、独自の (カスケードされている可能性がある) トレーニング済みモデルをデバイスに取り込んで実行し、おそらくは、デバイス上で実行されている自分で作成した別の IoT モジュールに結果を渡すことができる、高度なソリューション。
- デバイスから定期的にイメージを取得し、クラウド内のモデルを再トレーニングしてから、新しくトレーニングしたモデルをデバイスに戻す再トレーニング ループ。 オンザフライでモデルを更新およびスワップするデバイスの機能を使用します。

## <a name="ai-workload-details"></a>AI ワークロードの詳細
ワークロード アプリケーションは、Azure Percept Advanced の高度な開発の [GitHub リポジトリ](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app)内でオープンソース化されており、多くの小規模な C++ モジュールと、次に示すより重要なもので構成されています。
- [main.cpp](https://github.com/microsoft/azure-percept-advanced-development/blob/main/azureeyemodule/app/main.cpp): すべてを設定してから、メイン ループを実行します。
- [iot](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app/iot): このフォルダーには、Azure IoT Edge Hub との間の送受信メッセージを処理するモジュールと、ツイン更新メソッドが含まれています。
- [model](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app/model): このフォルダーには、コンピューター ビジョン モデルのクラス階層のモジュールが含まれています。
- [kernels](https://github.com/microsoft/azure-percept-advanced-development/tree/main/azureeyemodule/app/kernels): このフォルダーには、G-API カーネル、ops、および C++ ラッパー関数のモジュールが含まれています。

開発者は、このワークロード アプリケーションを使用してカスタム モジュールをビルドしたり、現在の azureeyemodule をカスタマイズしたりできます。 

## <a name="next-steps"></a>次のステップ

- azureeyemodule と Azure Percept ワークロードの詳細を確認したので、[こちらのチュートリアル](https://github.com/microsoft/azure-percept-advanced-development/blob/main/tutorials/README.md)のいずれかに従って、独自のモデルまたはパイプラインを使用してみてください。
- または、既成の [機械学習ノートブック](https://github.com/microsoft/azure-percept-advanced-development/tree/main/machine-learning-notebooks)のいずれかを使用して、**転移学習** を試してください。

