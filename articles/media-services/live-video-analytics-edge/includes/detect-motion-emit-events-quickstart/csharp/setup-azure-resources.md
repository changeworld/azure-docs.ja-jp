---
ms.openlocfilehash: 40d2f957ce115b43a1dcc138b86e05ec9cc47384
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060592"
---
このチュートリアルでは、次の Azure リソースが必要です。

* IoT Hub
* ストレージ アカウント
* Azure Media Services アカウント
* Azure 上の Linux VM ([IoT Edge ランタイム](../../../../../iot-edge/how-to-install-iot-edge.md)がインストール済み)

このクイックスタートでは、[Live Video Analytics リソース セットアップ スクリプト](https://github.com/Azure/live-video-analytics/tree/master/edge/setup)を使用して、ご利用の Azure サブスクリプションに必要なリソースをデプロイすることをお勧めします。 これを行うには、次のステップに従います。

1. [Azure Cloud Shell](https://shell.azure.com) を開きます。
1. Cloud Shell の初回使用時には、ストレージ アカウントと Microsoft Azure Files 共有を作成するためのサブスクリプションの選択を求められます。 **[ストレージの作成]** を選択して、Cloud Shell のセッション情報用のストレージ アカウントを作成します。 このストレージ アカウントは、Azure Media Services アカウントで使用するためにスクリプトによって作成されるアカウントとは別のものです。
1. Cloud Shell ウィンドウの左側にあるドロップダウン メニューから **[Bash]** をご利用の環境として選択します。

    ![環境セレクター](../../../media/quickstarts/env-selector.png)
1. 次のコマンドを実行します。

    ```
    bash -c "$(curl -sL https://aka.ms/lva-edge/setup-resources-for-samples)"
    ```
    
    スクリプトが正常に完了すると、必要なすべてのリソースがご利用のサブスクリプションに表示されます。
1. スクリプトが完了したら、中かっこを選択してフォルダー構造を展開します。 *~/clouddrive/lva-sample* ディレクトリに、いくつかのファイルがあるのを確認できます。 このクイックスタートで注目するのは、次のものです。

     * * **~/clouddrive/lva-sample/edge-deployment/.env** - このファイルには、Visual Studio Code がエッジ デバイスにモジュールをデプロイする際に使用するプロパティが格納されています。
     _ * **~/clouddrive/lva-sample/appsetting.json** _ - Visual Studio Code では、このファイルを使用してサンプル コードが実行されます。
     
    これらのファイルは、次のセクションで Visual Studio Code に開発環境を設定するときに必要になります。 差し当たりローカル ファイルとしてコピーしておいてください。
    
    ![アプリケーション設定](../../../media/quickstarts/clouddrive.png)

> [!TIP]
> 作成された Azure リソースで問題が発生した場合は、_ *[トラブルシューティング ガイド](../../../troubleshoot-how-to.md#common-error-resolutions)* *を参照して、よく発生する問題を解決してください。