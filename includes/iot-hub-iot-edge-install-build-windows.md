## <a name="install-the-prerequisites"></a>前提条件をインストールする

1. [Visual Studio 2015 または 2017](https://www.visualstudio.com) をインストールします。 ライセンスの要件を満たしている場合は、無料の Community Edition を使うことができます。 Visual C++ と NuGet Package Manager を必ず含めてください。

1. [git](http://www.git-scm.com) をインストールし、コマンド ラインから git.exe を実行できることを確認します。

1. [CMake](https://cmake.org/download/) をインストールし、コマンド ラインから cmake.exe を実行できることを確認します。 CMake バージョン 3.7.2 以降をお勧めします。 Windows では **.msi** が最も簡単なオプションです。 インストーラーで要求されたら、少なくとも現在のユーザーの PATH にCMake を追加します。

1. [Python 2.7](https://www.python.org/downloads/release/python-27) をインストールします。 `PATH` 環境変数に Python が追加されていることを確認します。 **[コントロール パネル]** > **[システムとセキュリティ]** > **[システム]** > **[システムの詳細設定]** > **[環境変数]** に移動します。 `C:\Python27` をパスに追加します。 

1. コマンド プロンプトで、次のコマンドを実行して、Azure IoT Edge GitHub リポジトリをローカル コンピューターに複製します。

    ```cmd
    git clone https://github.com/Azure/iot-edge.git
    ```

## <a name="how-to-build-the-sample"></a>サンプルをビルドする方法

これで、IoT Edge ランタイムとサンプルをローカル コンピューターでビルドできるようになります。

1. バージョンに応じて、**開発者コマンド プロンプト for VS 2015** または **開発者コマンド プロンプト for VS 2017** コマンド プロンプトを開きます。

1. **iot-edge** レポジトリのローカル コピーのルート フォルダーに移動します。

1. 次のようにビルド スクリプトを実行します。

    ```cmd
    tools\build.cmd --disable-native-remote-modules
    ```

このスクリプトでは、Visual Studio ソリューション ファイルを作成し、ソリューションをビルドします。 Visual Studio ソリューションは、**iot-edge** レポジトリのローカル コピーの **build** フォルダーにあります。 ビルドして単体テストを実行する場合は、`--run-unittests` パラメーターを追加します。 ビルドしてエンド ツー エンド テストを実行する場合は、`--run-e2e-tests` を追加します。

> [!NOTE]
> **build.cmd** スクリプトを実行するたびに **build** フォルダーが削除され、**iot-edge** レポジトリのローカル コピーのルート フォルダーに再作成されます。