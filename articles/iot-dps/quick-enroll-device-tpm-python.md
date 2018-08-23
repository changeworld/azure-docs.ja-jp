---
title: Python を使用して TPM デバイスを Azure Device Provisioning Service に登録する | Microsoft Docs
description: Azure クイック スタート - Python Provisioning Service SDK を使用して TPM デバイスを Azure IoT Hub Device Provisioning Service に登録する
author: wesmc7777
ms.author: wesmc
ms.date: 01/26/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: ff6200abd88144a530a243b508fd4878126fdb4b
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234069"
---
# <a name="enroll-tpm-device-to-iot-hub-device-provisioning-service-using-python-provisioning-service-sdk"></a>Python Provisioning Service SDK を使用して TPM デバイスを IoT Hub Device Provisioning Service に登録する
[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

以下の手順では、[Python Provisioning Service SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client) とサンプル Python アプリケーションを使用して、Azure IoT Hub Device Provisioning Service への TPM デバイスの個々の登録をプログラムで作成する方法について説明します。 Python Service SDK は Windows マシンと Linux マシンの両方で動作しますが、この記事では Windows 開発マシンを使用して登録プロセスの手順を説明します。

事前に、[Azure Portal での IoT Hub Device Provisioning Service の設定](./quick-setup-auto-provision.md)を済ませておいてください。


<a id="prepareenvironment"></a>

## <a name="prepare-the-environment"></a>環境の準備 

1. [Python 2.x または 3.x](https://www.python.org/downloads/) をダウンロードしてインストールします。 必ず、セットアップに必要な 32 ビットまたは 64 ビットのインストールを使用してください。 インストール中に求められた場合は、プラットフォーム固有の環境変数に Python を追加します。 

1. 次のいずれかのオプションを選択します。

    - **Azure IoT Python SDK** をビルドしてコンパイルします。 [これらの手順](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md)に従って、Python パッケージをビルドします。 Windows OS を使用している場合は、Python からネイティブ DLL を使用できるように [Visual C++ 再配布可能パッケージ](http://www.microsoft.com/download/confirmation.aspx?id=48145)もインストールします。

    - [Python パッケージ管理システムである *pip* をインストールまたはアップグレードし](https://pip.pypa.io/en/stable/installing/)、次のコマンドを使用してパッケージをインストールします。

        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

1. デバイスの保証キーが必要です。 [シミュレートされたデバイスの作成とプロビジョニング](quick-create-simulated-device.md)のクイックスタートに従ってシミュレートされた TPM デバイスを作成した場合は、そのデバイス用に作成されたキーを使用します。 それ以外の場合は、SDK に付属している次の保証キーを使用できます。

    ```
    AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAtW6MOyCu/Nih47atIIoZtlYkhLeCTiSrtRN3q6hqgOllA979No4BOcDWF90OyzJvjQknMfXS/Dx/IJIBnORgCg1YX/j4EEtO7Ase29Xd63HjvG8M94+u2XINu79rkTxeueqW7gPeRZQPnl1xYmqawYcyzJS6GKWKdoIdS+UWu6bJr58V3xwvOQI4NibXKD7htvz07jLItWTFhsWnTdZbJ7PnmfCa2vbRH/9pZIow+CcAL9mNTNNN4FdzYwapNVO+6SY/W4XU0Q+dLMCKYarqVNH5GzAWDfKT8nKzg69yQejJM8oeUWag/8odWOfbszA+iFjw3wVNrA5n8grUieRkPQ==
    ```


## <a name="modify-the-python-sample-code"></a>Python のサンプル コードの変更

ここでは、TPM デバイスのプロビジョニングの詳細をサンプル コードに追加する方法について説明します。 

1. テキスト エディターを使用して、新しい **TpmEnrollment.py** ファイルを作成します。

1. **TpmEnrollment.py** ファイルの先頭に次の `import` ステートメントと変数を追加します。 その後、`dpsConnectionString` は、**Azure Portal** の **[Device Provisioning Service]** にある **[共有アクセス ポリシー]** で見つかった接続文字列に置き換えます。 `endorsementKey` は、「[環境の準備](quick-enroll-device-tpm-python.md#prepareenvironment)」で既にメモした値に置き換えます。 最後に、一意の `registrationid` を作成し、それが小文字の英数字とハイフンのみで構成されていることを確認します。  
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism

    CONNECTION_STRING = "{dpsConnectionString}"

    ENDORSEMENT_KEY = "{endorsementKey}"

    REGISTRATION_ID = "{registrationid}"
    ```

1. 次の関数と関数呼び出しを追加して、グループ登録の作成を実装します。
   
    ```python
    def main():
        print ( "Starting individual enrollment..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)

        att = AttestationMechanism.create_with_tpm(ENDORSEMENT_KEY)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)

        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

1. **TpmEnrollment.py** ファイルを保存して閉じます。
 

## <a name="run-the-sample-tpm-enrollment"></a>サンプルの TPM 登録の実行

1. コマンド プロンプトを開き、スクリプトを実行します。

    ```cmd/sh
    python TpmEnrollment.py
    ```

1. 登録が成功したかどうか、出力を確認します。

1. Azure Portal でプロビジョニング サービスに移動します。 **[登録を管理します]** をクリックします。 TPM デバイスが **[個々の登録]** タブに、前に作成した `registrationid` という名前で表示されていることがわかります。 

    ![ポータルで TPM の登録が正常に完了したことを確認する](./media/quick-enroll-device-tpm-python/1.png)  


## <a name="clean-up-resources"></a>リソースのクリーンアップ
Java Service のサンプルを調べる予定の場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 使用する予定がない場合は、次の手順を使用して、このクイックスタートで作成したすべてのリソースを削除してください。

1. マシンに表示されている Python サンプルの出力ウィンドウを閉じます。
1. シミュレートされた TPM デバイスを作成した場合は、TPM シミュレーター ウィンドウを閉じます。
1. Azure Portal で [デバイス プロビジョニング サービス] に移動し、**[登録を管理します]** をクリックし、**[個々の登録]** タブを選択します。このクイックスタートを使用して作成した登録エントリの*登録 ID* を選択し、ブレードの上部の **[削除]** ボタンをクリックします。  


## <a name="next-steps"></a>次の手順
このクイックスタートでは、TPM デバイスの個々の登録エントリをプログラムで作成し、TPM のシミュレートされたデバイスをマシンに作成し、Azure IoT Hub Device Provisioning Service を使って IoT ハブにプロビジョニングしました。 Device Provisioning に関する理解をさらに深めるには、Azure Portal における Device Provisioning Service の設定に関するチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure IoT Hub Device Provisioning Service のチュートリアル](./tutorial-set-up-cloud.md)
