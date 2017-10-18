## <a name="install-the-prerequisites"></a>前提条件をインストールする

このチュートリアルの手順では、Ubuntu Linux を実行しているものと仮定します。

前提条件のパッケージをインストールするには、シェルを開き、次のコマンドを実行します。

```bash
sudo apt-get update
sudo apt-get install curl build-essential libcurl4-openssl-dev git cmake pkg-config libssl-dev uuid-dev valgrind libglib2.0-dev libtool autoconf
```

シェルで、次のコマンドを実行して、Azure IoT Edge GitHub リポジトリをローカル コンピューターに複製します。

```bash
git clone https://github.com/Azure/iot-edge.git
```

## <a name="how-to-build-the-sample"></a>サンプルをビルドする方法

これで、IoT Edge ランタイムとサンプルをローカル コンピューターでビルドできるようになります。

1. シェルを開きます。

1. **iot-edge** レポジトリのローカル コピーのルート フォルダーに移動します。

1. 次のようにビルド スクリプトを実行します。

    ```sh
    tools/build.sh --disable-native-remote-modules
    ```

このスクリプトでは、**cmake** ユーティリティを使用して、**iot-edge** レポジトリのローカル コピーのルート フォルダーに **build** という名前のフォルダーを作成し、Makefile を生成します。 スクリプトは、次にソリューションをビルドし、単体テストとエンド ツー エンド テストはスキップします。 ビルドして単体テストを実行する場合は、`--run-unittests` パラメーターを追加します。 ビルドしてエンド ツー エンド テストを実行する場合は、`--run-e2e-tests` を追加します。

> [!NOTE]
> **build.sh** スクリプトを実行するたびに **build** フォルダーが削除され、**iot-edge** レポジトリのローカル コピーのルート フォルダーに再作成されます。