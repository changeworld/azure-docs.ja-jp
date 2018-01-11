> [!div class="op_single_selector"]
> * [Windows 上の C](../articles/iot-suite/iot-suite-connecting-devices.md)
> * [Linux 上の C](../articles/iot-suite/iot-suite-connecting-devices-linux.md)
> * [Node.js (汎用)](../articles/iot-suite/iot-suite-connecting-devices-node.md)
> * [Raspberry Pi の Node.js](../articles/iot-suite/iot-suite-connecting-pi-node.md)
> * [Raspberry Pi の C](../articles/iot-suite/iot-suite-connecting-pi-c.md)

このチュートリアルでは、次のテレメトリを、リモート監視の[構成済みソリューション](../articles/iot-suite/iot-suite-what-are-preconfigured-solutions.md)に送信する **Chiller** デバイスを実装します。

* 気温
* 圧力
* 湿度

わかりやすくするために、コードでは、**Chiller** に対してサンプル テレメトリ値を生成します。 このサンプルを拡張するには、実際のセンサーをデバイスに接続し、実際のテレメトリを送信します。

サンプル デバイスでは、次の操作も行います。

* メタデータをソリューションに送信して、機能を記述する。
* ソリューションの **[デバイス]** ページからトリガーされたアクションに応答する。
* ソリューションの **[デバイス]** ページから送信された構成変更に応答する。

このチュートリアルを完了するには、アクティブな Azure アカウントが必要になります。 アカウントがない場合は、無料試用アカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](http://azure.microsoft.com/pricing/free-trial/)を参照してください。

## <a name="before-you-start"></a>開始する前に

デバイス用のコードを作成する前に、リモート監視構成済みソリューションをプロビジョニングし、そのソリューションに新しいカスタム デバイスをプロビジョニングする必要があります。

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>リモート監視構成済みソリューションをプロビジョニングする

このチュートリアルで作成する **Chiller** デバイスは、[リモート監視](../articles/iot-suite/iot-suite-remote-monitoring-explore.md)構成済みソリューションのインスタンスにデータを送信します。 リモート監視構成済みソリューションを Azure アカウントにまだプロビジョニングしていない場合は、「[Deploy the remote monitoring preconfigured solution (リモート監視構成済みソリューションをデプロイする)](../articles/iot-suite/iot-suite-remote-monitoring-deploy.md)」を参照してください

リモート監視ソリューションのプロビジョニング プロセスが完了したら、 **[起動]** をクリックしてブラウザーでソリューション ダッシュボードを開きます。

![ソリューション ダッシュボード](media/iot-suite-selector-connecting/dashboard.png)

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>リモート監視ソリューションでデバイスをプロビジョニングする

> [!NOTE]
> ソリューションにデバイスを既にプロビジョニングしている場合は、この手順を省略して構いません。 クライアント アプリケーションを作成するときに、デバイスの資格情報が必要です。

デバイスが構成済みソリューションに接続するには、有効な資格情報を使用して IoT Hub に対してデバイス自身の ID を証明する必要があります。 デバイスの資格情報は、ソリューションの **[デバイス]** ページから取得できます。 このチュートリアルの後半で、クライアント アプリケーションにデバイスの資格情報を含めます。

デバイスをリモート監視ソリューションに追加するには、ソリューションの **[デバイス]** ページで次の手順を実行します。

1. **[プロビジョニング]** を選択し、**[デバイスの種類]** で **[物理]** を選択します。

    ![物理デバイスをプロビジョニングする](media/iot-suite-selector-connecting/devicesprovision.png)

1. デバイス ID として「**Physical-chiller**」と入力します。 **[対称キー]** オプションと **[キーの自動生成]** オプションを選択します。

    ![デバイス オプションを選択する](media/iot-suite-selector-connecting/devicesoptions.png)

デバイスが構成済みソリューションへの接続に使用する資格情報を見つけるには、ブラウザーで Azure Portal に移動します。 サブスクリプションにサインインします。

1. リモート監視ソリューションが使用する Azure サービスが含まれるリソース グループを見つけます。 リソース グループの名前は、プロビジョニングしたリモート監視ソリューションの名前と同じです。

1. このリソース グループの IoT ハブに移動します。 次に **IoT デバイス**を選択します。

    ![デバイス エクスプローラー](media/iot-suite-selector-connecting/deviceexplorer.png)

1. リモート監視ソリューションの **[デバイス]** ページで作成した **デバイス ID** を選択します。

1. **デバイス ID** と**主キー**の値をメモします。 この値は、デバイスに接続するためのコードを、ソリューションに追加するときに使用します。

リモート監視構成済みソリューションで、物理デバイスをプロビジョニングできました。 以降のセクションでは、デバイスの資格情報を使用してソリューションに接続するクライアント アプリケーションを実装します。

クライアント アプリケーションでは、組み込みの **Chiller** デバイス モデルが実装されます。 構成済みソリューションのデバイス モデルは、デバイスについて次の情報を指定します。

* デバイスがソリューションにレポートするプロパティ。 たとえば、**Chiller** デバイスは、そのファームウェアと位置に関する情報をレポートします。
* デバイスがソリューションに送信するテレメトリの種類。 たとえば、**Chiller** デバイスは、温度、湿度、および気圧の値を送信します。
* ソリューションからスケジュールしてデバイスで実行できるメソッド。 たとえば、**Chiller** デバイスは、**Reboot**、**FirmwareUpdate**、**EmergencyValveRelease**、および **IncreasePressuree** メソッドを実装する必要があります。