---
title: 'チュートリアル: Azure IoT Edge デバイスを構成する - IoT Edge での Machine Learning'
description: このチュートリアルでは、Linux を実行している Azure 仮想マシンを、透過的なゲートウェイとして機能する Azure IoT Edge デバイスとして構成します。
author: kgremban
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: 103a3004f6bf4ec6e536d87b7fe72484f2bff974
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130259114"
---
# <a name="tutorial-configure-an-azure-iot-edge-device"></a>チュートリアル: Azure IoT Edge デバイスを構成する

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

この記事では、Linux を実行している Azure 仮想マシンを、透過的なゲートウェイとして機能する Azure IoT Edge デバイスになるように構成します。 透過的なゲートウェイの構成では、デバイスはゲートウェイの存在を知らなくても、ゲートウェイを介して Azure IoT Hub に接続できます。 同時に、IoT Hub 内のデバイスを操作するユーザーは中間のゲートウェイ デバイスを意識しません。 最終的には、この透過的なゲートウェイに IoT Edge モジュールを追加することによって、エッジ分析をシステムに追加します。

>[!NOTE]
>このチュートリアルの概念は IoT Edge のすべてのバージョンに適用されますが、シナリオを試すために作成したサンプル デバイスでは、IoT Edge バージョン 1.1 が実行されます。

この記事の手順は通常、クラウド開発者によって実行されます。

チュートリアルのこのセクションで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * ゲートウェイ デバイスがダウンストリーム デバイスに安全に接続できるようにするための証明書を作成する。
> * IoT Edge デバイスを作成する。
> * IoT Edge デバイスをシミュレートする Azure 仮想マシンを作成する。

## <a name="prerequisites"></a>前提条件

この記事は、IoT Edge 上で Azure Machine Learning を使用するためのチュートリアルのシリーズの一部です。 シリーズの各記事は、前の記事の作業に基づいています。 この記事に直接アクセスしている場合は、シリーズの[最初の記事](tutorial-machine-learning-edge-01-intro.md)を参照してください。

## <a name="create-certificates"></a>証明書の作成

デバイスがゲートウェイとして機能するためには、ダウンストリーム デバイスに安全に接続できる必要があります。 IoT Edge では、公開キー基盤 (PKI) を使用して、デバイス間にセキュリティで保護された接続を設定することができます。 この場合、透過的なゲートウェイとして機能する IoT Edge デバイスにダウンストリームの IoT デバイスが接続できるようにします。 妥当なセキュリティを維持するには、ダウン ストリーム デバイスで IoT Edge デバイスの ID を確認してください。 IoT Edge デバイスで証明書を使用するしくみの詳細については、[Azure IoT Edge 証明書の使用の詳細](iot-edge-certs.md)に関するページを参照してください。

このセクションでは、Docker イメージを使用して自己署名証明書を作成した後、そのイメージをビルドして実行します。 Docker イメージを使用してこの手順を完了することを選択した理由は、それによって、Windows 開発用マシンで証明書を作成するために必要な手順の数が削減されるからです。 Docker イメージを使用して自動化した内容については、「[IoT Edge デバイスの機能をテストするためのデモ用の証明書を作成する](how-to-create-test-certificates.md)」を参照してください。

1. 開発用の VM にサインインします。
1. **c:\edgeCertificates** というフォルダー名とパスを使用して、新しいフォルダーを作成します。

1. **Docker for Windows** をまだ実行していない場合は、Windows の [スタート] メニューから起動します。

1. Visual Studio Code を開きます。

1. **[ファイル]**  >  **[フォルダーを開く]** を選択し、**C:\\source\\IoTEdgeAndMlSample\\CreateCertificates** を選択します。

1. **[エクスプローラー]** ペインで **[dockerfile]** を右クリックし、 **[イメージのビルド]** を選択します。

1. ダイアログ ボックスで、イメージ名とタグは既定値 **createcertificates:latest** のままにします。

    ![Visual Studio Code で証明書を作成する方法を示すスクリーンショット。](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

1. ビルドが完了するまで待ちます。

    > [!NOTE]
    > 公開キーが見つからないという警告が表示される場合があります。 この警告は無視しても問題ありません。 同様に、イメージに対するアクセス許可を確認またはリセットすることを推奨するセキュリティ警告が表示されますが、このイメージに関しては無視しても問題ありません。

1. Visual Studio Code ターミナル ウィンドウで、createcertificates コンテナーを実行します。

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

1. Docker が **c:\\** ドライブへのアクセスを求めます。 **[Share it]\(共有する\)** を選択します。

1. メッセージが表示されたら、資格情報を入力します。

1. コンテナーの実行が終了したら、**c:\\edgeCertificates** で、次のファイルを確認します。

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Azure Key Vault に証明書をアップロードする

証明書を安全に保存し、複数のデバイスからアクセスできるようにするために、証明書を Azure キー コンテナーにアップロードします。 前の一覧からわかるように、PFX と PEM という 2 種類の証明書ファイルがあります。 PFX ファイルは、キー コンテナーにアップロードされる Key Vault 証明書として扱います。 PEM ファイルはプレーンテキストであり、キー コンテナー シークレットとして扱います。 [Jupyter Notebook](tutorial-machine-learning-edge-04-train-model.md#run-the-jupyter-notebooks) を実行して作成した、Azure Machine Learning ワークスペースに関連付けられているキー コンテナー インスタンスを使用します。

1. [Azure portal](https://portal.azure.com) から、Azure Machine Learning ワークスペースに移動します。

1. Machine Learning ワークスペースの概要ページで、**キー コンテナー** の名前を見つけます。

    ![キー コンテナー名をコピーする方法を示すスクリーンショット。](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

1. 開発用コンピューターで、証明書を Key Vault にアップロードします。 **\<subscriptionId\>** と **\<keyvaultname\>** は、実際のリソース情報に置き換えてください。

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

1. サインインを求められたら、Azure にサインインします。

1. スクリプトが数分間実行され、新しいキー コンテナー エントリの一覧が出力されます。

    ![Key Vault スクリプトの出力を示すスクリーンショット。](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="register-an-iot-edge-device"></a>IoT Edge デバイスを登録する

Azure IoT Edge デバイスを IoT ハブに接続するために、まずハブにデバイスを登録します。 クラウドでデバイス ID から接続文字列を取り、それを使用して IoT Edge デバイス上でランタイムを構成します。 構成済みのデバイスがハブに接続したら、モジュールをデプロイしてメッセージを送信することができます。 対応するデバイス ID を IoT ハブで変更することによって、物理 IoT Edge デバイスの構成を変更することもできます。

このチュートリアルでは、Visual Studio Code を使用して新しいデバイス ID を登録します。 これらの手順は、Azure portal または Azure CLI を使用して完了することもできます。 どちらの方法を選択した場合でも、IoT Edge デバイスのデバイス接続文字列を必ず取得してください。 デバイス接続文字列は、Azure portal のデバイスの詳細ページにあります。

1. 開発用コンピューターで Visual Studio Code を開きます。

1. Visual Studio Code の **[エクスプローラー]** ビューから、 **[Azure IoT Hub]** フレームを展開します。

1. 省略記号を選択して、 **[Create IoT Edge Device]\(IoT Edge デバイスの作成\)** を選択します。

1. デバイスに名前を付けます。 並べ替えでデバイス一覧の一番上に表示されるように、便宜上、名前は **aaTurbofanEdgeDevice** としています。

1. 新しいデバイスがデバイス一覧に表示されます。

    ![Visual Studio Code エクスプローラーのデバイスのビューを示すスクリーンショット。](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-an-azure-virtual-machine"></a>Azure 仮想マシンをデプロイする

Azure IoT Edge ランタイムがインストールされて構成されている Ubuntu 18.04 LTS 仮想マシンを使用します。 このデプロイでは、[iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy) プロジェクト リポジトリに保持されている [Azure Resource Manager テンプレート](../azure-resource-manager/templates/overview.md)を使用します。 これにより、テンプレートに指定した接続文字列を使用して、前の手順で登録した IoT Edge デバイスをプロビジョニングします。

仮想マシンのデプロイには、Azure portal または Azure CLI を使用します。 ここでは、Azure portal での手順を説明します。 詳細については、「[Ubuntu 仮想マシン上で Azure IoT Edge を実行する](how-to-install-iot-edge-ubuntuvm.md)」を参照してください。

### <a name="deploy-using-deploy-to-azure-button"></a>[Deploy to Azure] ボタンを使用してデプロイする

1. `iotedge-vm-deploy` ARM テンプレートを使用して Ubuntu 18.04 LTS 仮想マシンをデプロイするには、下のボタンをクリックします。

    [![iotedge-vm-deploy の [Deploy to Azure] ボタン](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. 新しく開いたウィンドウで、入力可能なフォーム フィールドに入力します。

   | フィールド | 説明 |
   | - | - |
   | **サブスクリプション** | 仮想マシンをデプロイするためのアクティブな Azure サブスクリプション。 |
   | **リソース グループ** | 仮想マシンとそれに関連付けられたリソースを格納する、既存または新しく作成されたリソース グループ。 |
   | **DNS ラベル プレフィックス** | 仮想マシンのホスト名のプレフィックスとして使用される、あなたが選択した必要値。 |
   | **管理ユーザー名** | ユーザー名。デプロイ時にルート権限が与えられます。 |
   | **デバイスの接続文字列** | 目的の [IoT Hub](../iot-hub/about-iot-hub.md) 内に作成された、デバイス向けの[デバイス接続文字列](./how-to-provision-single-device-linux-symmetric.md#view-registered-devices-and-retrieve-provisioning-information)。 |
   | **VM のサイズ** | デプロイする仮想マシンの[サイズ](../cloud-services/cloud-services-sizes-specs.md)
   | **Ubuntu OS バージョン** | ベース仮想マシンにインストールする Ubuntu OS のバージョン。 |
   | **場所** | 仮想マシンをデプロイする[地理的リージョン](https://azure.microsoft.com/global-infrastructure/locations/)。選択したリソース グループの場所が規定値となります。 |
   | **認証の種類** | **sshPublicKey** または **パスワード** のどちらか好きな方を選択します。 |
   | **[管理パスワードまたはキー]** | 認証タイプの選択に応じて、SSH 公開キーの値、またはパスワードの値。 |

1. すべてのフィールドを入力したら、ページの下部にあるチェックボックスをオンにして使用条件に同意し、 **[確認と作成]** 、 **[作成]** の順に選択して、デプロイを開始します。

1. Azure portal で仮想マシンに移動します。 仮想マシンは、リソース グループを参照するか、ポータルのランディング ページで **[Azure サービス]** の下にある **[仮想マシン]** を選択すると見つかります。

1. 仮想マシンの **DNS 名** をメモします。 これは、仮想マシンにログオンする際に必要になります。

## <a name="connect-to-your-iot-edge-device"></a>IoT Edge デバイスに接続する

1. コマンド プロンプトを開き、次のコマンドを使用して仮想マシンにログオンします。 前のセクションに従い、ユーザー名と DNS 名に自身の情報を入力します。

    ```bash
    ssh <adminUsername>@<DNS_name>
    ```

1. ホストの信頼性を確認するよう求められたら、「**yes**」と入力して **Enter** キーを押します。

1. 入力を求められたら、パスワードを指定します。

1. Ubuntu のウェルカム メッセージが表示された後、`<username>@<machinename>:~$` のようなプロンプトが表示されます。

## <a name="download-key-vault-certificates"></a>Key Vault 証明書をダウンロードする

この記事の前半では、証明書を Key Vault にアップロードして、IoT Edge デバイスとリーフ デバイスで使用できるようにしました。 リーフ デバイスは、IoT Hub と通信するためのゲートウェイとして IoT Edge デバイスを使用するダウンストリーム デバイスです。

リーフ デバイスについては、チュートリアルの後半で扱います。 このセクションでは、証明書を IoT Edge デバイスにダウンロードします。

1. Linux 仮想マシン上の SSH セッションから、Azure CLI を使用して Azure にサインインします。

    ```azurecli
    az login
    ```

1. ブラウザーで [Microsoft デバイスのサインイン](https://microsoft.com/devicelogin) ページを開き、一意のコードを入力するように求められます。 これらの手順はローカル コンピューターで実行できます。 認証が完了したら、ブラウザー ウィンドウを閉じます。

1. 認証に成功すると、Linux VM がサインインして Azure サブスクリプションを一覧表示します。

1. Azure CLI コマンドで使用する Azure サブスクリプションを設定します。

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. VM 上に証明書用のディレクトリを作成します。

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. キー コンテナーに保存した証明書をダウンロードします: new-edge-device-full-chain.cert.pem、new-edge-device.key.pem、および azure-iot-test-only.root.ca.cert.pem。

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>IoT Edge デバイス構成を更新する

IoT Edge ランタイムは、ファイル /etc/iotedge/config.yaml を使用してその構成を保持します。 このファイル内の以下の 2 つの情報を更新する必要があります。

* **Certificates** (証明書): ダウンストリーム デバイスとの間で行われる接続に使用する証明書
* **Hostname** (ホスト名): VM IoT Edge デバイスの完全修飾ドメイン名 (FQDN)

config.yaml ファイルを直接編集して、証明書とホスト名を更新します。

1. config.yaml ファイルを開きます。

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. config.yaml ファイルの certificates セクションを更新します。先頭の **#** を削除してパスを設定し、ファイルの内容を次の例のようにします。

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    **certificates:** 行の前にスペースがないこと、また入れ子になった各証明書が 2 つのスペースによってインデントされていることを確認してください。

    nano 内で右クリックすると、クリップボードの内容が現在のカーソル位置に貼り付けられます。 文字列を置換するには、キーボードの矢印キーを使って置換する文字列に移動し、文字列を削除した後、右クリックしてバッファーから貼り付けます。

1. Azure portal で、お使いの仮想マシンに移動します。 **[概要]** セクションから DNS 名 (マシンの FQDN) をコピーします。

1. その FQDN を config.yml ファイルの hostname セクションに貼り付けます。 名前がすべて小文字であることを確認してください。

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

1. **Ctrl + X**、**Y**、および **Enter** キーを押してファイルを保存し、閉じます。

1. IoT Edge デーモンを再起動します。

    ```bash
    sudo systemctl restart iotedge
    ```

1. IoT Edge デーモンの状態を確認します。 コマンドの後に「 **:q**」と入力し、終了します。

    ```bash
    systemctl status iotedge
    ```

## <a name="troubleshooting"></a>トラブルシューティング

状態にエラー ("\[ERROR\]" のプレフィックスが付いた色付きのテキスト) がある場合、デーモンのログを調べて詳細なエラー情報を確認します。

   ```bash
   journalctl -u iotedge --no-pager --no-full
   ```

エラーの対処方法の詳細については、[トラブルシューティング](troubleshoot.md)のページを参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルはセットの一部であり、各記事は前の記事の作業が行われたことが前提になっています。 最後のチュートリアルを完了するまで、リソースのクリーンアップはしないでください。

## <a name="next-steps"></a>次のステップ

IoT Edge の透過的なゲートウェイとして Azure VM を構成する作業が完了しました。 まず、テスト証明書を生成してキー コンテナーにアップロードしました。 次に、スクリプトと Resource Manager テンプレート、さらに Azure Marketplace の Ubuntu Server 16.04 LTS + Azure IoT Edge ランタイム イメージを使用して VM をデプロイしました。 VM を起動し、SSH 経由で接続しました。 次に、Azure にサインインし、キー コンテナーから証明書をダウンロードしました。 config.yaml ファイルを更新して IoT Edge ランタイムの構成にいくつかの変更を加えました。

次の記事に進んで、IoT Edge モジュールをビルドします。

> [!div class="nextstepaction"]
> [カスタム IoT Edge モジュールの作成とデプロイ](tutorial-machine-learning-edge-06-custom-modules.md)
