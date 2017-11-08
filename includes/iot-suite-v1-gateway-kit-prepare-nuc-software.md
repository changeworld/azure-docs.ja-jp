## <a name="build-iot-edge"></a>IoT Edge を構築する

このチュートリアルでは、カスタムの IoT Edge モジュールを使用して事前構成済みのリモート監視ソリューションと通信します。 そのため、カスタム ソース コードから IoT Edge モジュールを構築する必要があります。 次のセクションでは、IoT Edge をインストールし、カスタム IoT Edge モジュールを構築する方法について説明します。

### <a name="install-iot-edge"></a>IoT Edge をインストールする

次の手順では、Intel NUC にコンパイル済みの IoT Edge ソフトウェアをインストールする方法について説明します。

1. Intel NUC で次のコマンドを実行して、必要なスマート パッケージ リポジトリを構成します。

    ```bash
    smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
    smart channel --add WR_Repo type=rpm-md baseurl=https://distro.windriver.com/release/idp-3-xt/public_feeds/WR-IDP-3-XT-Intel-Baytrail-public-repo/RCPL13/corei7_64/
    ```

    **このチャネルを含める**ように求められたら、`y` を入力します。

1. 次のコマンドを実行して、スマート パッケージ マネージャーを更新します。

    ```bash
    smart update
    ```

1. 次のコマンドを実行して、Azure IoT Edge パッケージをインストールします。

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```

1. 次の "Hello World" サンプルを実行して、インストールを確認します。 このサンプルは、5 秒ごとに log.txT ファイルに "hello world" というメッセージを書き込みます。 次のコマンドで "Hello World" サンプルを実行します。

    ```bash
    cd /usr/share/azureiotgatewaysdk/samples/hello_world/
    ./hello_world hello_world.json
    ```

    サンプルが停止したら、**無効な引数**を示すメッセージは無視します。

    次のコマンドを使用して、ログ ファイルの内容を表示します。

    ```bash
    cat log.txt | more
    ```

### <a name="troubleshooting"></a>トラブルシューティング

"No package provides util-linux-dev" (util-linux-dev を提供するパッケージがありません) というエラーが表示されたら、Intel NUC を再起動してください。
