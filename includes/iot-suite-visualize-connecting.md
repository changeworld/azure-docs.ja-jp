## <a name="view-device-telemetry"></a>デバイス テレメトリを表示する

デバイスから送信されたテレメトリは、ソリューションの **[デバイス]** ページで表示できます。

1. プロビジョニングしたデバイスを、**[デバイス]** ページのデバイスの一覧から選択します。 パネルには、デバイス テレメトリのプロットなど、デバイスに関する情報が表示されます。

    ![デバイスの詳細を確認する](media/iot-suite-visualize-connecting/devicesdetail.png)

1. **[気圧]** を選択して、テレメトリの表示を変更します。

    ![気圧テレメトリを表示する](media/iot-suite-visualize-connecting/devicespressure.png)

1. デバイスに関する診断情報を表示するには、下へスクロールして、**[診断]** に移動します。

    ![デバイスの診断を表示する](media/iot-suite-visualize-connecting/devicesdiagnostics.png)

## <a name="act-on-your-device"></a>デバイスを操作する

デバイスでメソッドを呼び出すには、リモート監視ソリューションの **[デバイス]** ページを使用します。 たとえば、リモート監視ソリューションで、**Chiller** デバイスが **Reboot** メソッドを実装しています。

1. **[デバイス]** を選択して、ソリューションの **[デバイス]** ページに移動します。

1. プロビジョニングしたデバイスを、**[デバイス]** ページのデバイスの一覧から選択します。

    ![物理デバイスを選択する](media/iot-suite-visualize-connecting/devicesselect.png)

1. デバイスで呼び出すことができるメソッドの一覧を表示するには、**[スケジュール]** を選択します。 複数のデバイスで実行するメソッドのスケジュールを設定するために、一覧から複数のデバイスを選択することができます。 **[スケジュール]** パネルには、選択したすべてのデバイスに共通のメソッドの型が表示されます。

1. **[再起動]** を選択し、ジョブ名を **RebootPhysicalChiller** に変更して、**[適用]** を選択します。

    ![再起動のスケジュールを設定する](media/iot-suite-visualize-connecting/deviceschedule.png)

1. デバイスがメソッドを処理しているとき、デバイス コードを実行しているコンソールにメッセージが表示されます。

> [!NOTE]
> ソリューションでジョブの状態を追跡するには、**[表示]** を選択します。

## <a name="next-steps"></a>次のステップ

[「Customize the remote monitoring preconfigured solution (リモート監視構成済みソリューションをカスタマイズする)](../articles/iot-suite/iot-suite-remote-monitoring-customize.md)」では、構成済みソリューションをカスタマイズする方法をいくつか説明します。