## <a name="automatic-device-management"></a>自動デバイス管理
Azure IoT Hub の自動デバイス管理は、大規模なデバイス集団のライフサイクル全体を管理する複雑な反復タスクの多くを自動化します。 自動デバイス管理を利用すると、プロパティに基づいて対象のデバイス セットを設定し、必要な構成を定義して、デバイスがスコープ内になったときにいつでも IoT Hub がデバイスを更新できるようにすることができます。  [自動デバイス構成](../articles/iot-hub/iot-hub-auto-device-config.md)と [IoT Edge の自動展開](../articles/iot-edge/how-to-deploy-monitor.md)で構成されます。

## <a name="iot-edge"></a>IoT Edge
Azure IoT Edge は、Azure サービスのクラウド中心のデプロイと、オンプレミスのデバイスに対するソリューション固有のコードを可能にします。 IoT Edge デバイスでは、他のデバイスからデータを集計して、データがクラウドに送信される前にコンピューティングと分析を実行できます。 詳細については、[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) に関するページを参照してください。

## <a name="iot-edge-agent"></a>IoT Edge エージェント
IoT Edge ランタイムの、モジュールのデプロイと監視を担当する部分です。

## <a name="iot-edge-device"></a>IoT Edge デバイス
IoT Edge デバイスには IoT Edge ランタイムがインストールされており、デバイスの詳細で **IoT Edge デバイス**としてフラグが付けられています。 「[Linux のシミュレートされたデバイスに Azure IoT Edge をデプロイする - プレビュー](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)」をご覧ください。

## <a name="iot-edge-automatic-deployment"></a>IoT Edge の自動デプロイ
IoT Edge の自動デプロイでは、一連の IoT Edge モジュールを実行する IoT Edge デバイスの対象のセットを構成します。 各デプロイでは、新しいデバイスが作成されたかターゲット条件と一致するよう変更されたときでも、対象の条件と一致するすべてのデバイスが特定のモジュールのセットを実行することが継続的に保証されます。 各 IoT Edge デバイスは、そのターゲット条件を満たす最優先のデプロイのみを受け取ります。 詳細については、[IoT Edge の自動デプロイ](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)に関するページをご覧ください。

## <a name="iot-edge-deployment-manifest"></a>IoT Edge のデプロイ マニフェスト
一連のモジュール、ルート、関連するモジュールに必要なプロパティをデプロイするために、1 つ以上の IoT Edge デバイスのモジュール ツインにコピーされる情報を含んだ Json ドキュメントです。

## <a name="iot-edge-gateway-device"></a>IoT Edge ゲートウェイ デバイス
ダウンストリーム デバイスを備える IoT Edge デバイスです。 ダウンストリーム デバイスには、IoT Edge デバイスと非 IoT Edge デバイスのどちらでも指定できます。

## <a name="iot-edge-hub"></a>IoT Edge ハブ
IoT Edge ランタイムの、モジュール間通信、(IoT Hub への) アップストリーム通信、(IoT Hub からの) ダウンストリーム通信を担当する部分です。 

## <a name="iot-edge-leaf-device"></a>IoT Edge リーフ デバイス
ダウンストリーム デバイスを備えない IoT Edge デバイスです。 

## <a name="iot-edge-module"></a>IoT Edge モジュール
IoT Edge モジュールは、IoT Edge デバイスにデプロイできる Docker コンテナーです。 デバイスからのメッセージの取り込み、メッセージの変換、IoT Hub へのメッセージの送信など、特定のタスクを実行します。 他のモジュールと通信し、IoT Edge ランタイムにデータを送信します。 「[IoT Edge モジュールを開発するための要件と ツールについて理解する - プレビュー](https://docs.microsoft.com/azure/iot-edge/module-development)」をご覧ください。

## <a name="iot-edge-module-identity"></a>IoT Edge モジュール ID
Edge ハブか IoT Hub で認証するためにモジュールに使用される存在とセキュリティ資格情報が記載された、IoT Hub モジュール ID レジストリ内のレコードです。

## <a name="iot-edge-module-image"></a>IoT Edge モジュール イメージ
モジュール インスタンスをインスタンス化するために IoT Edge ランタイムによって使用される Docker イメージです。

## <a name="iot-edge-module-twin"></a>IoT Edge モジュール ツイン
IoT Hub 内で保持される、モジュール インスタンスの状態情報を格納する Json ドキュメントです。

## <a name="iot-edge-priority"></a>IoT Edge の優先度
2 つの IoT Edge のデプロイが同じデバイスを対象にしている場合は、優先度の高いデプロイが適用されます。 2 つのデプロイの優先順位が同じだった場合は、作成日が新しいデプロイが適用されます。 [優先順位](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority)について詳しくご覧ください。

## <a name="iot-edge-runtime"></a>IoT Edge ランタイム
IoT Edge ランタイムには、Microsoft が配布する、IoT Edge デバイスにインストールされるすべてのものが含まれます。 これには、Edge エージェント、Edge ハブ、IoT Edge セキュリティ デーモンが含まれます。

## <a name="iot-edge-set-modules-to-a-single-device"></a>IoT Edge モジュールを単一のデバイスに設定
1 つのデバイスのモジュール ツイン上の IoT Edge マニフェストの内容をコピーする操作です。 基礎になる API は汎用的な 'apply configuration' です。これは単純に IoT Edge マニフェストを入力として受け取ります。

## <a name="iot-edge-target-condition"></a>IoT Edge のターゲット条件
IoT Edge のデプロイにおけるターゲット条件は、デプロイの対象となるデバイスを選択するための、デバイス ツインのタグに関するブール条件です。例: **tag.environment = prod**。ターゲット条件は、要求を満たす新しいデバイスを含めたり、要求を満たさなくなったデバイスを削除したりするために、継続的に評価されます。 [ターゲット条件](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)について詳しくご覧ください。