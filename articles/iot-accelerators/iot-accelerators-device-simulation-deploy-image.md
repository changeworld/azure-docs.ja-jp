---
title: カスタムのデバイス シミュレーション イメージのデプロイ - Azure | Microsoft Docs
description: この攻略ガイドでは、デバイス シミュレーション ソリューションのカスタム Docker イメージを Azure にデプロイする方法について説明します。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "61448403"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>カスタムのデバイス シミュレーション Docker イメージをデプロイする

デバイス シミュレーション ソリューションを変更して、カスタム機能を追加することができます。 たとえば、記事「[Serialize telemetry using Protocol Buffers](iot-accelerators-device-simulation-protobuf.md)」では、Protocol Buffers (Protobuf) を使用して利用統計情報を送信するソリューションにカスタムのデバイスを追加する方法を説明しています。 ローカルで変更をテストしたら、次に、変更を Azure 内のデバイス シミュレーション インスタンスにデプロイします。 このタスクを完了するには、変更したサービスを含む Docker イメージを作成してデプロイする必要があります。

この攻略ガイドの手順では、以下の方法を説明します。

1. 開発環境を準備する
1. 新しい Docker イメージの生成
1. 新しい Docker イメージを使用するためのデバイス シミュレーションの構成
1. 新しいイメージを使用したシミュレーションの実行

## <a name="prerequisites"></a>前提条件

この攻略ガイドの手順を完了するには、次が必要です。

* デプロイ済み[デバイス シミュレーション](quickstart-device-simulation-deploy.md)インスタンス。
* Docker。 お使いのプラットフォーム向けの [Docker Community Edition](https://www.docker.com/products/docker-engine#/download) をダウンロードします。
* Docker イメージをアップロードすることができる [Docker Hub のアカウント](https://hub.docker.com/)。 Docker Hub アカウントで、**device-simulation** という名前のパブリック リポジトリを作成します。
* ローカル コンピューター上の変更およびテスト済みの[デバイス シミュレーション ソリューション](https://github.com/Azure/device-simulation-dotnet/archive/master.zip)。 たとえば、[Protocol Buffers を使用して利用統計情報をシリアル化](iot-accelerators-device-simulation-protobuf.md)するように、ソリューションを変更できます。
* SSH を実行できるシェル。 Git For Windows をインストールしている場合は、インストールに含まれる **bash** シェルを使用できます。 [Azure Cloud Shell](https://shell.azure.com/) を使用することもできます。

この記事の手順では、Windows の使用を想定しています。 別のオペレーティング システムを使用している場合は、環境に合わせてファイル パスとコマンドの一部を調整することが必要になる場合があります。

## <a name="create-a-new-docker-image"></a>新しい Docker イメージの作成

独自の変更をデバイス シミュレーション サービスにデプロイするには、**scripts\docker** フォルダー内のビルドとデプロイ スクリプトを編集して、コンテナーを Docker Hub アカウントにアップロードする必要があります

### <a name="modify-the-docker-scripts"></a>Docker のスクリプトの変更

**scripts\docker** フォルダー内にある Docker の **build.cmd**、**publish.cmd**、および **run.cmd** スクリプトを Docker Hub のリポジトリ情報で変更します。 これらの手順は、**device-simulation** という名前のパブリック リポジトリを作成していることを前提としています。

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

次のように **docker-compose.yml** ファイルを更新します。

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>新しいファイルを含めるための、ソリューションの構成

新しいデバイス モデル ファイルを追加した場合は、ソリューション内に明示的に含める必要があります。 含める追加ファイルごとに、**services/services.csproj** にエントリを追加します。 たとえば、[Protocol Buffers を使用した利用統計情報のシリアル化](iot-accelerators-device-simulation-protobuf.md)の手順を完了している場合は、次のエントリを追加します。

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>新しい Docker イメージの生成と Docker Hub へのプッシュ

Docker Hub アカウントを使用して、新しい Docker イメージを Docker Hub に公開します。

1. コマンド プロンプトを開き、デバイス シミュレーションのリポジトリのローカル コピーに移動します。

1. **docker** フォルダーに移動します。

    ```cmd
    cd scripts\docker
    ```

1. 次のコマンドを実行して、Docker イメージをビルドします。

    ```cmd
    build.cmd
    ```

1. 次のコマンドを実行して、Docker イメージを Docker Hub リポジトリに公開します。 Docker Hub の資格情報を使用して Docker にサインインします。

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>サービスの更新

デバイス シミュレーションのコンテナーを更新してカスタム イメージを使用するには、次の手順を完了します。

* SSH を使用して、デバイス シミュレーションのインスタンスをホストしている仮想マシンに接続します。 前のセクションでメモした IP アドレスとパスワードを使用します。

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* **/app** ディレクトリに移動します。

    ```sh
    cd /app
    ```

* **docker-compose.yml** ファイルを編集します。

    ```sh
    sudo nano docker-compose.yml
    ```

    **image** を変更して、Docker Hub リポジトリにアップロードしたカスタムの **device-simulation** イメージをポイントします。

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    変更を保存します。

* 次のコマンドを実行して、マイクロサービスを再起動します。

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>シミュレーションの実行

カスタマイズしたデバイス シミュレーション ソリューションを使用して、シミュレーションを実行できるようになりました。

1. [Microsoft Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators#dashboard) からデバイス シミュレーション Web UI を起動します。

1. Web UI を使用して、シミュレーションを構成して実行します。 以前に [Protocol Buffers を使用した利用統計情報のシリアル化](iot-accelerators-device-simulation-protobuf.md)を完了している場合は、カスタム デバイス モデルを使用できます。

## <a name="next-steps"></a>次のステップ

カスタムのデバイス シミュレーション イメージをデプロイする方法を習得したので、[デバイス シミュレーション ソリューション アクセラレータで既存の IoT Hub を使用する](iot-accelerators-device-simulation-choose-hub.md)方法について学ぶことができます。