> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-get-started.md)
> 
> 

この記事では、[Azure IoT Edge][lnk-iot-edge] アーキテクチャの基本的なコンポーネントを表す [Hello World サンプル コード][lnk-helloworld-sample]の詳細なチュートリアルを提供します。 このサンプルでは、Azure IoT Edge を使用して、5 秒ごとに "hello world" というメッセージをファイルに記録する単純なゲートウェイを作成します。

このチュートリアルでは、次の項目について説明します。

* **Hello World サンプル アーキテクチャ**: [Azure IoT Edge アーキテクチャの概念][lnk-edge-concepts]が Hello World サンプルにどのように適用されるかについて、また、コンポーネントがどのように機能するかについて説明します。
* **サンプルのビルド方法**: サンプルをビルドするために必要な手順を説明します。
* **サンプルの実行方法**: サンプルを実行するために必要な手順を説明します。 
* **標準的な出力**: サンプルを実行したときに想定される出力の例を示します。
* **コード スニペット**: Hello World サンプルでの主要な IoT Edge ゲートウェイ コンポーネントの実装方法を示す、コード スニペットのコレクションです。


## <a name="hello-world-sample-architecture"></a>Hello World サンプル アーキテクチャ
Hello World サンプルでは、前のセクションで説明した概念を示します。 また、次の 2 つの IoT Edge モジュールで構成されるパイプラインを持つ IoT Edge ゲートウェイを実装します。

* *hello world* モジュール: 5 秒ごとにメッセージを作成し、それを logger モジュールに渡します。
* *logger* モジュール: 受け取ったメッセージをファイルに書き込みます。

![Azure IoT Edge で構築された Hello World サンプルのアーキテクチャ][4]

前のセクションで説明したように、Hello World モジュールはメッセージを logger モジュールに直接渡すことはせず、 5 秒ごとにブローカーに発行します。

logger モジュールは、ブローカーからメッセージを受信し、それに基づいて動作して、ファイルにメッセージの内容を書き込みます。

logger モジュールはブローカーからメッセージを受信するだけで、ブローカーに新しいメッセージを発行することはありません。

![ブローカーが Azure IoT Edge のモジュール間でメッセージをルーティングする方法][5]

前の図は、Hello World サンプルのアーキテクチャと、サンプル内の各部分を[リポジトリ][lnk-iot-edge]に実装するソース ファイルへの相対パスを示しています。 自分でコードを調べてみるか、この記事のコード スニペットをガイドとして使用してください。

<!-- Images -->
[4]: media/iot-hub-iot-edge-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-iot-edge-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/iot-edge/tree/master/samples/hello_world
[lnk-iot-edge]: https://github.com/Azure/iot-edge
[lnk-edge-concepts]: ../articles/iot-hub/iot-hub-iot-edge-overview.md