> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-get-started.md)
> 
> 

この記事では、[Azure IoT Edge][lnk-iot-edge] アーキテクチャの基本的なコンポーネントを表す [Hello World サンプル コード][lnk-helloworld-sample]の詳細なチュートリアルを提供します。 このサンプルでは、Azure IoT Edge を使用して、5 秒ごとに "hello world" というメッセージをファイルに記録する単純なゲートウェイを作成します。

このチュートリアルでは、次の項目について説明します。

* **概念**: IoT Edge で作成するゲートウェイを構成するコンポーネントの概念について大まかに説明します。
* **Hello World サンプル アーキテクチャ**: 上記の概念が Hello World サンプルにどのように適用されるかについて、また、コンポーネントがどのように機能するかについて説明します。
* **サンプルのビルド方法**: サンプルをビルドするために必要な手順を説明します。
* **サンプルの実行方法**: サンプルを実行するために必要な手順を説明します。 
* **標準的な出力**: サンプルを実行したときに想定される出力の例を示します。
* **コード スニペット**: Hello World サンプルでの主要な IoT Edge ゲートウェイ コンポーネントの実装方法を示す、コード スニペットのコレクションです。

## <a name="azure-iot-edge-concepts"></a>Azure IoT Edge の概念
サンプルのコードを調べたり、IoT Edge を使用して独自のフィールド ゲートウェイを作成したりする前に、IoT Edge のアーキテクチャの基礎となる主要な概念を理解する必要があります。

### <a name="iot-edge-modules"></a>IoT Edge のモジュール
Azure IoT Edge でゲートウェイを構築するには、*IoT Edge モジュール*を作成してアセンブルします。 モジュールは、 *メッセージ* を使用して互いにデータを交換します。 モジュールがメッセージを受信すると、そのメッセージに対して何らかのアクションを実行し、必要に応じて新しいメッセージに変換したうえで、他のモジュールが処理できるように発行します。 モジュールの中には、新しいメッセージを生成するだけで、受け取ったメッセージを処理しないものもあります。 モジュールのチェーンによってデータ処理のパイプラインが作られ、このパイプライン上のそれぞれの時点で、各モジュールがデータの変換を行います。

![Azure IoT Edge で構築されたゲートウェイのモジュールのチェーン][1]

IoT Edge には次のものが含まれます。

* 一般的なゲートウェイ機能を実行する記述済みのモジュール。
* 開発者がカスタム モジュールを記述する際に使用できるインターフェイス。
* 一連のモジュールをデプロイして実行するために必要なインフラストラクチャ。

Gateway SDK が提供する抽象化レイヤーによって、さまざまなオペレーティング システムとプラットフォームで実行するゲートウェイの作成が可能になります。

![Azure IoT Edge の抽象化レイヤー][2]

### <a name="messages"></a>メッセージ
ゲートウェイの機能を概念化する上で、モジュール同士がメッセージを交換すると考えると簡単ですが、正確にはそうではありません。 IoT Edge モジュールはブローカーを使用して互いに通信し、メッセージをブローカー (バス、pubsub、その他のメッセージング パターン) に発行して、ブローカーがメッセージを接続されているモジュールにルーティングできるようにします。

モジュールは、**Broker_Publish** 関数を使用してブローカーにメッセージを発行します。 ブローカーは、コールバック関数を呼び出すことでモジュールにメッセージを配信します。 メッセージは、一連のキー/値のプロパティと、メモリのブロックとして渡されるコンテンツで構成されます。

![Azure IoT Edge でのブローカーの役割][3]

### <a name="message-routing-and-filtering"></a>メッセージのルーティングとフィルター処理
メッセージを適切な IoT Edge モジュールにルーティングする方法は、2 つあります。 一連のリンクをブローカーに渡せば、ブローカーはモジュールごとに source と sink を把握できます。また、モジュールではメッセージのプロパティでフィルター処理することもできます。 モジュールは、そのモジュール向けのメッセージに基づいて動作する必要があります。 リンクとメッセージのフィルター処理により、メッセージのパイプラインを効果的に作成できます。

## <a name="hello-world-sample-architecture"></a>Hello World サンプル アーキテクチャ
Hello World サンプルでは、前のセクションで説明した概念を示します。 また、次の 2 つの IoT Edge モジュールで構成されるパイプラインを持つ IoT Edge ゲートウェイを実装します。

* *hello world* モジュール: 5 秒ごとにメッセージを作成し、それを logger モジュールに渡します。
* *logger* モジュール: 受け取ったメッセージをファイルに書き込みます。

![Azure IoT Edge で構築された Hello World サンプルのアーキテクチャ][4]

前のセクションで説明したように、Hello World モジュールはメッセージを logger モジュールに直接渡すことはせず、 5 秒ごとにブローカーに発行します。

logger モジュールは、ブローカーからメッセージを受信し、それに基づいて動作して、ファイルにメッセージの内容を書き込みます。

logger モジュールはブローカーからメッセージを受信するだけで、ブローカーに新しいメッセージを発行することはありません。

![ブローカーが Azure IoT Edge のモジュール間でメッセージをルーティングする方法][5]

上の図は、Hello World サンプルのアーキテクチャと、サンプル内の各部分を[リポジトリ][lnk-iot-edge]に実装するソース ファイルへの相対パスを示しています。 自分でコードを調べてみるか、以下のコード スニペットをガイドとして使用してください。

<!-- Images -->
[1]: media/iot-hub-iot-edge-getstarted-selector/modules.png
[2]: media/iot-hub-iot-edge-getstarted-selector/modules_2.png
[3]: media/iot-hub-iot-edge-getstarted-selector/messages_1.png
[4]: media/iot-hub-iot-edge-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-iot-edge-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/iot-edge/tree/master/samples/hello_world
[lnk-iot-edge]: https://github.com/Azure/iot-edge