---
title: Azure Percept Studio でコードなしのビジョン ソリューションを作成する
description: Azure Percept Studio でコードなしのビジョン ソリューションを作成し、それを Azure Percept DK にデプロイする方法について説明します
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 49e5db729dab7abaa440b1adf6a61e9e52a1efbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023131"
---
# <a name="create-a-no-code-vision-solution-in-azure-percept-studio"></a>Azure Percept Studio でコードなしのビジョン ソリューションを作成する

Azure Percept Studio では、コーディングを必要とせずに、カスタム コンピューター ビジョン ソリューションを構築してデプロイできます。 この記事では、次のことについて説明します。

- [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) でビジョン プロジェクトを作成する
- 開発キットを使用してトレーニング画像を収集する
- [Custom Vision](https://www.customvision.ai/) でトレーニング画像にラベルを付ける
- カスタム物体検出または分類モデルをトレーニングする
- 自分のモデルを開発キットにデプロイする
- 再トレーニングを設定してモデルを改善する

このチュートリアルは、AI の実務経験が少ないまたはない開発者や、Azure Percept の使用を開始したばかりの方に適しています。

## <a name="prerequisites"></a>前提条件

- Azure Percept DK (開発キット)
- [Azure サブスクリプション](https://azure.microsoft.com/free/)
- Azure Percept DK セットアップ エクスペリエンス: 開発キットを Wi-Fi ネットワークに接続し、IoT ハブを作成して、開発キットを IoT ハブに接続済みであること

## <a name="create-a-vision-prototype"></a>ビジョン プロトタイプの作成

1. ブラウザーを起動し、[Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) に移動します。

1. [概要] ページで **[デモ & チュートリアル** ] タブをクリックします。  :::image type="content" source="./media/tutorial-nocode-vision/percept-studio-overview-inline.png" alt-text="Azure Percept Studio の概要画面。" lightbox="./media/tutorial-nocode-vision/percept-studio-overview.png":::

1. **[Vision tutorials and demos]\(ビジョンのチュートリアルとデモ\)** で **[Create a vision prototype]\(ビジョン プロトタイプの作成\)** をクリックします。

    :::image type="content" source="./media/tutorial-nocode-vision/vision-tutorials-and-demos-inline.png" alt-text="Azure Percept Studio のデモとチュートリアルの画面。" lightbox="./media/tutorial-nocode-vision/vision-tutorials-and-demos.png":::

1. **[New Azure Percept Custom Vision prototype]\(新しい Azure Percept Custom Vision プロトタイプ\)** ページで、以下を実行します。

    1. **[プロジェクト名]** ボックスに、自分のビジョン プロトタイプの名前を入力します。

    1. **[プロジェクトの説明]** ボックスに、ビジョン プロトタイプの説明を入力します。

    1. **[デバイスの種類]** ドロップダウン メニューで **[Azure Percept DK]** を選択します。

    1. **[リソース]** ドロップダウン メニューでリソースを選択するか、 **[新しいリソースを作成する]** をクリックします。 新しいリソースを作成する場合、 **[作成]** ウィンドウで以下を実行します。
        1. 新しいリソースの名前を入力します。
        1. Azure サブスクリプションを選択します。
        1. リソース グループを選択するか、新しく作成します。
        1. 任意のリージョンを選択します。
        1. 価格レベルを選択します (S0 が推奨)。
        1. ウィンドウの下部にある **[作成]** をクリックします。

        :::image type="content" source="./media/tutorial-nocode-vision/create-resource.png" alt-text="リソースの作成ウィンドウ。":::

    1. **[プロジェクト タイプ]** では、自分のビジョン プロジェクトで物体検出と画像分類のどちらを実行するのかを選択します。 プロジェクト タイプの詳細を確認するには、 **[選択に関する詳細]** をクリックします。

    1. **[最適化]** で、自分のプロジェクトでの最適化の目標を、正確性、ネットワークの低遅延、両方のバランスから選択します。

    1. **[作成]** ボタンをクリックします。

        :::image type="content" source="./media/tutorial-nocode-vision/create-prototype.png" alt-text="Custom Vision プロトタイプの作成画面。":::

## <a name="connect-a-device-to-your-project-and-capture-images"></a>プロジェクトへのデバイスの接続と画像のキャプチャ

ビジョン ソリューションの作成後、自分の開発キットとその対応する IoT ハブをそれに追加する必要があります。

1. 開発キットの電源をオンにします。

1. **[IoT Hub]** ドロップダウン メニューで、OOBE 中に自分の開発キットが接続された IoT ハブを選択します。

1. **[デバイス]** ドロップダウン メニューで、自分の開発キットを選択します。

次に、自分の AI モデルのトレーニング用に、画像を読み込むか画像をキャプチャする必要があります。 タグの種類ごとに少なくとも 30 個の画像をアップロードすることをお勧めします。 たとえば、犬と猫を検出する機能を構築したい場合は、少なくとも犬の画像を 30 個、猫の画像を 30 個アップロードする必要があります。 開発キットのビジョン SoM を使用して画像をキャプチャするには、以下を実行します。

1. **[Image capture]\(画像のキャプチャ\)** ウィンドウで **[View device stream]\(デバイス ストリームの表示\)** を選択し、ビジョン SoM のビデオ ストリームを表示します。

1. ビデオ ストリームをチェックして、ビジョン SoM のカメラがトレーニング画像を撮影できるように正しく調整されていることを確かめます。 必要に応じて調整を行います。

1. **[Image capture]\(画像のキャプチャ\)** ウィンドウで、 **[Take photo]\(写真の撮影\)** をクリックします。

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture.png" alt-text="画像のキャプチャ画面。":::

1. または、 **[Automatic image capture]\(自動画像キャプチャ\)** チェック ボックスをオンにすることで、自動画像キャプチャを設定して一度に大量の画像を収集します。 **[Capture rate]\(キャプチャ レート\)** で希望する画像化レートを、 **[ターゲット]** で収集したい画像の総数を選択します。 **[Set automatic capture]\(自動キャプチャの設定\)** をクリックし、自動画像キャプチャ プロセスを開始します。

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture-auto.png" alt-text="自動画像キャプチャのドロップダウン メニュー。":::

写真が十分にある場合は、画面の下部にある **[Next: Tag images and model training]\(次へ: 画像のタグ付けとモデルのトレーニング\)** をクリックします。 すべての画像は [Custom Vision](https://www.customvision.ai/) に保存されます。

> [!NOTE]
> トレーニング画像を Custom Vision に直接アップロードする場合は、画像ファイルのサイズを 6 MB より大きくできない点に注意してください。

## <a name="tag-images-and-train-your-model"></a>画像のタグ付けとモデルのトレーニング

モデルをトレーニングする前に、画像にラベルを付けます。

1. **[Tag images and model training]\(画像のタグ付けとモデルのトレーニング\)** ページで、 **[Open project in Custom Vision]\(Custom Vision でプロジェクトを開く\)** をクリックします。

1. **[Custom Vision]** ページの左側にある **[タグ]** で **[タグなし]** をクリックし、前の手順で収集したばかりの画像を表示します。 タグの付いていない画像を 1 つまたは複数選択します。

1. **[Image Detail]\(画像の詳細\)** ウィンドウで、画像をクリックしてタグ付けを開始します。 物体検出を自分のプロジェクト タイプとして選択した場合は、タグ付けしたい特定のオブジェクトを[境界ボックス](../cognitive-services/custom-vision-service/get-started-build-detector.md#upload-and-tag-images)で囲む必要もあります。 必要に応じて境界ボックスを調整します。 オブジェクトのタグを入力し、 **[+]** をクリックしてタグを適用します。 たとえば、店舗の棚の補充が必要な場合に通知するビジョン ソリューションを作成していたのなら、空の棚の画像に "空の棚" タグを追加します。また、在庫で埋まった棚には "埋まった棚" タグを追加します。 タグ付けされていないすべての画像で繰り返します。

    :::image type="content" source="./media/tutorial-nocode-vision/image-tagging.png" alt-text="Custom Vision における画像のタグ付け画面。":::

1. 画像へのタグ付け後、ウィンドウの右上隅にある **[X]** アイコンをクリックします。 **[タグ]** の **[タグ付き]** をクリックし、新しくタグ付けした画像をすべて表示します。

1. 画像にラベルを付けたら、AI モデルをトレーニングする準備が整います。 これを行うには、ページの上部付近にある **[トレーニング]** をクリックします。 モデルをトレーニングするには、タグの種類ごとに少なくとも 15 個の画像が必要です (最低 30 個の使用が推奨)。 トレーニングは通常、約 30 分かかります。ただし、画像セットが非常に大きい場合は、もっと長い時間がかかる可能性があります。

    :::image type="content" source="./media/tutorial-nocode-vision/train-model.png" alt-text="[トレーニング] ボタンが強調表示された状態のトレーニング画像の選択。":::

1. トレーニングが完了したら、自分のモデルのパフォーマンスが画面に表示されます。 これらの結果の評価について詳しくは、[モデルの評価に関するドキュメント](../cognitive-services/custom-vision-service/get-started-build-detector.md#evaluate-the-detector)を参照してください。 トレーニング後は必要に応じて、追加の画像に基づいて[モデルをテスト](../cognitive-services/custom-vision-service/test-your-model.md)することや、再トレーニングすることもできます。 モデルはトレーニングのたびに新しい反復として保存されます。 モデルのパフォーマンスを改善する方法の詳細については、[Custom Vision のドキュメント](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)を参照してください。

    :::image type="content" source="./media/tutorial-nocode-vision/iteration.png" alt-text="モデルのトレーニング結果。":::

    > [!NOTE]
    > Custom Vision で追加の画像に基づいてモデルをテストする場合は、テストする画像ファイルのサイズが 4 MB 以下に収まるように注意してください。

自分のモデルのパフォーマンスに満足したら、ブラウザーのタブを閉じて Custom Vision を閉じます。

## <a name="deploy-your-ai-model"></a>AI モデルのデプロイ

1. Azure Percept Studio のタブに戻って、画面の下部にある **[Next: Evaluate and deploy]\(次へ: 評価とデプロイ\)** をクリックします。

1. **[Evaluate and deploy]\(評価とデプロイ\)** ウィンドウには、自分が選択したモデルの反復のパフォーマンスが表示されます。 自分の開発キットにデプロイしたい反復を **[Model iteration]\(モデルの反復\)** ドロップダウン メニューで選択し、画面の下部にある **[Deploy model]\(モデルのデプロイ\)** をクリックします。

    :::image type="content" source="./media/tutorial-nocode-vision/deploy-model-inline.png" alt-text="モデルのデプロイ画面。" lightbox="./media/tutorial-nocode-vision/deploy-model.png":::

1. モデルのデプロイ後、デバイスのビデオ ストリームを表示して、自分のモデルの推論が動作していることを確認します。

    :::image type="content" source="./media/tutorial-nocode-vision/view-device-stream.png" alt-text="動作中のヘッドホン検出機能を示すデバイス ストリーム。":::

このウィンドウを閉じた後は、Azure Percept Studio のホームページの **[AI プロジェクト]** にある **[ビジョン]** をクリックして自分のビジョン プロジェクトの名前を選択することで、いつでも自分のビジョン プロジェクトに戻って編集することができます。

:::image type="content" source="./media/tutorial-nocode-vision/vision-project-inline.png" alt-text="ビジョン プロジェクトのページ。" lightbox="./media/tutorial-nocode-vision/vision-project.png":::

## <a name="improve-your-model-by-setting-up-retraining"></a>再トレーニングを設定してモデルを改善する

モデルのトレーニングとデバイスへのデプロイが完了したら、再トレーニングのパラメーターを設定して、より多くのトレーニング データをキャプチャすることで、モデルのパフォーマンスを向上させることができます。 この機能は、確率範囲に基づいて画像をキャプチャできるようにして、トレーニング済みのモデルのパフォーマンスを向上させるために使用されます。 たとえば、確率が低い場合にのみトレーニング画像をキャプチャするようにデバイスを設定できます。 画像の追加とトレーニング データのバランスに関する[その他のガイダンス](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)を次に示します。

1. 再トレーニングを設定するには、 **[プロジェクト]** に戻り、 **[プロジェクトの概要]** に戻ります。
1. **[画像キャプチャ]** タブで、 **[自動画像キャプチャ]** と **[再トレーニングの設定]** を選択します。
1. **[自動画像キャプチャ]** チェック ボックスをオンにすることで、自動画像キャプチャを設定して一度に大量の画像を収集します。
1. **[Capture rate]\(キャプチャ レート\)** で希望する画像化レートを、 **[ターゲット]** で収集したい画像の総数を選択します。
1. **[再トレーニングの設定]** セクションで、トレーニング データをさらにキャプチャするイテレーションを選択し、次に確率範囲を選択します。 確率レートを満たす画像のみがプロジェクトにアップロードされます。

    :::image type="content" source="./media/tutorial-nocode-vision/vision-image-capture.png" alt-text="画像のキャプチャ。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで新しい Azure リソースを作成したものの、今後自分のビジョン ソリューションを開発したり使用したりするつもりがない場合は、次の手順に従ってリソースを削除してください。

1. [Azure ポータル](https://ms.portal.azure.com/)にアクセスします。
1. **[すべてのリソース]** をクリックします。
1. このチュートリアルで作成したリソースの横にあるチェック ボックスをオンにします。 リソースの種類は **[Cognitive Services]** として表示されます。
1. 画面の上部近くにある **[削除]** アイコンをクリックします。

## <a name="video-walkthrough"></a>ビデオ チュートリアル

上記の手順を視覚的に説明するチュートリアルについては、次の動画をご覧ください。

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

</br>

## <a name="next-steps"></a>次の手順

次は、Azure Percept Studio のその他のビジョン ソリューション機能について、ビジョンのハウツー記事をご確認ください。

<!--
Add links to how-to articles and oobe article.
-->