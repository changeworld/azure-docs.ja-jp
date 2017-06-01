---
title: "mbed で C を使用してデバイスを接続する | Microsoft Docs"
description: "C で記述され、mbed デバイスで実行されるアプリケーションを使用して、デバイスを Azure IoT Suite 構成済みリモート監視ソリューションに接続する方法について説明します。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 9551075e-dcf9-488f-943e-d0eb0e6260be
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/22/2017
ms.author: dobett
ROBOTS: NOINDEX
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: ef7b78f85a787f8fbe22c0e26aa34f0cd1685d58
ms.contentlocale: ja-jp
ms.lasthandoff: 05/25/2017

---
# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-mbed"></a>デバイスをリモート監視構成済みソリューションに接続する (mbed)

## <a name="scenario-overview"></a>シナリオの概要
このシナリオでは、次のテレメトリをリモート監視[構成済みソリューション][lnk-what-are-preconfig-solutions]に送信するデバイスを作成します。

* 外部温度
* 内部温度
* 湿度

わかりやすくするために、デバイス上のコードではサンプル値を生成しますが、デバイスに実際のセンサーを接続し、実際のテレメトリを送信して、サンプルを拡張することをお勧めします。

このデバイスでは、ソリューション ダッシュボードから呼び出されたメソッドと、ソリューション ダッシュボードで設定されている必要なプロパティ値に応答することもできます。

このチュートリアルを完了するには、アクティブな Azure アカウントが必要になります。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、[Azure の無料試用版サイト][lnk-free-trial]をご覧ください。

## <a name="before-you-start"></a>開始する前に
デバイス用のコードを作成する前に、リモート監視構成済みソリューションをプロビジョニングし、そのソリューションに新しいカスタム デバイスをプロビジョニングする必要があります。

### <a name="provision-your-remote-monitoring-preconfigured-solution"></a>リモート監視構成済みソリューションをプロビジョニングする
このチュートリアルで作成するデバイスは、[リモート監視][lnk-remote-monitoring]構成済みソリューションのインスタンスにデータを送信します。 リモート監視構成済みソリューションを Azure アカウントにまだプロビジョニングしていない場合は、次の手順を使用します。

1. <https://www.azureiotsuite.com/> ページで、**+** をクリックしてソリューションを作成します。
2. **[リモート監視]** パネルで **[選択]** をクリックして、ソリューションを作成します。
3. **[Create Remote monitoring solution (リモート監視ソリューションの作成)]** ページで任意の**ソリューション名**を入力し、デプロイ先の**リージョン**を選択したら、使用する Azure サブスクリプションを選択します。 その後、 **[ソリューションの作成]**をクリックします。
4. プロビジョニング プロセスが完了するまで待機します。

> [!WARNING]
> 構成済みソリューションでは、課金対象の Azure サービスを使用します。 不必要な課金を避けるために、使用が済んだら、必ずサブスクリプションから構成済みソリューションを削除してください。 <https://www.azureiotsuite.com/> ページで、構成済みのソリューションをサブスクリプションから完全に削除できます。
> 
> 

リモート監視ソリューションのプロビジョニング プロセスが完了したら、 **[起動]** をクリックしてブラウザーでソリューション ダッシュボードを開きます。

![ソリューションのダッシュボード][img-dashboard]

### <a name="provision-your-device-in-the-remote-monitoring-solution"></a>リモート監視ソリューションでデバイスをプロビジョニングする
> [!NOTE]
> ソリューションにデバイスを既にプロビジョニングしている場合は、この手順を省略して構いません。 クライアント アプリケーションを作成するときに、デバイスの資格情報が必要です。
> 
> 

デバイスが構成済みソリューションに接続するには、有効な資格情報を使用して IoT Hub に対してデバイス自身の ID を証明する必要があります。 デバイスの資格情報は、ソリューション ダッシュボードから取得できます。 このチュートリアルの後半で、クライアント アプリケーションにデバイスの資格情報を含めます。

デバイスをリモート監視ソリューションに追加するには、ソリューション ダッシュボードで次の手順を実行します。

1. ダッシュボードの左下隅にある **[デバイスの追加]**をクリックします。
   
   ![デバイスを追加する][1]
2. **[カスタム デバイス]** パネルで、**[新規追加]** をクリックします。
   
   ![カスタム デバイスを追加する][2]
3. **[デバイス ID を自分で定義する]** を選択します。 **mydevice** などのデバイス ID を入力します。**[ID の確認]** をクリックして、その名前がまだ使用されていないことを確認し、**[作成]** をクリックしてデバイスをプロビジョニングします。
   
   ![デバイス ID を追加する][3]
4. デバイスの資格情報 (デバイス ID、IoT Hub ホスト名、デバイス キー) を書き留めておきます。 クライアント アプリケーションがリモート監視ソリューションに接続する際に、この値が必要になります。 次に、 **[Done]**をクリックします。
   
    ![デバイスの資格情報を表示する][4]
5. ソリューション ダッシュボードのデバイスの一覧でデバイスを選択します。 次に、**[デバイスの詳細]** パネルで、**[デバイスの有効化]** をクリックします。 現在、デバイスの状態は **[実行中]** です。 リモート監視ソリューションはデバイスからテレメトリを受信し、デバイス上でメソッドを呼び出すことができます。

[img-dashboard]: ./media/iot-suite-connecting-devices-mbed/dashboard.png
[1]: ./media/iot-suite-connecting-devices-mbed/suite0.png
[2]: ./media/iot-suite-connecting-devices-mbed/suite1.png
[3]: ./media/iot-suite-connecting-devices-mbed/suite2.png
[4]: ./media/iot-suite-connecting-devices-mbed/suite3.png

[lnk-what-are-preconfig-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

## <a name="build-and-run-the-c-sample-solution"></a>C のサンプル ソリューションをビルドして実行する

次に、[mbed 対応の Freescale FRDM-K64F][lnk-mbed-home] デバイスをリモート監視ソリューションに接続する手順を説明します。

### <a name="connect-the-mbed-device-to-your-network-and-desktop-machine"></a>mbed デバイスをネットワークおよびデスクトップ コンピューターに接続する

1. イーサネット ケーブルを使用して mbed デバイスをネットワークに接続する方法を説明します。 サンプル アプリケーションでは、インターネットへのアクセスが必要なため、この手順は必須です。

1. mbed デバイスをデスクトップ PC に接続する方法の詳細については、「[mbed の概要][lnk-mbed-getstarted]」を参照してください。

1. デスクトップ PC で Windows を実行している場合は、「[PC の構成][lnk-mbed-pcconnect]」を参照して、mbed デバイスへのシリアル ポート アクセスを構成します。

### <a name="create-an-mbed-project-and-import-the-sample-code"></a>mbed プロジェクトを作成してサンプル コードをインポートする

mbed プロジェクトにサンプル コードをいくつか追加するには、次の手順に従います。 リモート監視のスタート プロジェクトをインポートし、AMQP プロトコルではなく MQTT プロトコルを使用するようにプロジェクトを変更します。 現時点では、IoT Hub のデバイス管理機能を使用するには MQTT プロトコルを使用する必要があります。

1. Web ブラウザーで、mbed.org の [開発者向けサイト](https://developer.mbed.org/)に移動します。 サインアップしていない場合は、アカウントを作成するオプションが表示されます (アカウントの作成は無料です)。 既にサインアップしている場合は、アカウントの資格情報を使用してログインします。 次に、ページの右上隅の **[Compiler]** をクリックします。 このアクションにより、*ワークスペース* インターフェイスが表示されます。

1. 使用しているハードウェア プラットフォームがウィンドウの右上隅に表示されていることを確認するか、右上隅にあるアイコンをクリックしてハードウェア プラットフォームを選択します。

1. メイン メニューの **[インポート]** をクリックします。 **[Click here to import from URL (URL からインポートするにはここをクリック)]** をクリックします。
   
    ![mbed ワークスペースへのインポートを開始する][6]

1. ポップアップ ウィンドウで、サンプル コードのリンク「 https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ 」を入力し、**[インポート]** をクリックします。
   
    ![mbed ワークスペースにサンプル コードをインポートする][7]

1. mbed コンパイラのウィンドウで、このプロジェクトをインポートしたことでさまざまなライブラリもインポートされたことを確認できます。 ライブラリには、Azure IoT チームが提供および管理するライブラリ ([azureiot_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/)、[iothub_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/)、[iothub_amqp_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/)、[azure_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)) もあれば、mbed ライブラリ カタログで入手可能なサードパーティのライブラリもあります。
   
    ![mbed プロジェクトを表示する][8]

1. **[Program Workspace (プログラム ワークスペース)]** で、**iothub\_amqp\_transport** ライブラリを右クリックし、**[削除]**、**[OK]** の順にクリックして確定します。

1. **[Program Workspace (プログラム ワークスペース)]** で、**azure\_amqp\_c** ライブラリを右クリックし、**[削除]**、**[OK]** の順にクリックして確定します。

1. **[Program Workspace (プログラム ワークスペース)]** で **remote_monitoring** プロジェクトを右クリックし、**[インポート ライブラリ]**、**[URL から]** の順に選択します。
   
    ![mbed ワークスペースへのライブラリのインポートを開始する][6]

1. ポップアップ ウィンドウで、MQTT トランスポート ライブラリのリンク「https://developer.mbed.org/users/AzureIoTClient/code/iothub\_mqtt\_transport/」を入力し、**[インポート]** をクリックします。
   
    ![mbed ワークスペースにライブラリをインポートする][12]

1. 前の手順を繰り返して、https://developer.mbed.org/users/AzureIoTClient/code/azure\_umqtt\_c/ から MQTT ライブラリを追加します。

1. ワークスペースは、次のようになります。

    ![mbed ワークスペースを表示する][13]

1. remote\_monitoring\remote_monitoring.c ファイルを開き、既存の `#include` ステートメントを次のコードで置き換えます。

    ```c
    #include "iothubtransportmqtt.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer_devicetwin.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    #include "parson.h"

    #ifdef MBED_BUILD_TIMESTAMP
    #include "certs.h"
    #endif // MBED_BUILD_TIMESTAMP
    ```
1. remote\_monitoring\remote\_monitoring.c ファイルの残りのコードをすべて削除します。

[!INCLUDE [iot-suite-connecting-code](../../includes/iot-suite-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>サンプルのビルドと実行

**remote\_monitoring\_run** 関数を呼び出すコードを追加し、デバイス アプリケーションをビルドして実行します。

1. **remote\_monitoring\_run** 関数を呼び出す次のコードを備えた **main** 関数を remote\_monitoring.c ファイルの末尾に追加します。
   
    ```c
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

1. **[コンパイル]** をクリックしてプログラムをビルドします。 警告は無視してかまいません。ただし、ビルドでエラーが発生する場合は、続行する前にそのエラーを修正してください。

1. ビルドが成功した場合、mbed コンパイラの Web サイトによって、プロジェクト名の付いた .bin ファイルが生成され、ローカル コンピューターにダウンロードされます。 .bin ファイルをデバイスにコピーします。 .bin ファイルをデバイスに保存すると、デバイスが再起動し、.bin ファイルに含まれるプログラムが実行されます。 mbed デバイスのリセット ボタンを押すことで、いつでもプログラムを手動で再起動することができます。

1. PuTTY などの SSH クライアント アプリケーションを使用して、デバイスに接続します。 Windows デバイス マネージャーを確認すると、デバイスで使用されているシリアル ポートを特定できます。
   
    ![][11]

1. PuTTY で、接続タイプとして **[シリアル]** をクリックします。 通常、デバイスは 9600 ボーで接続するため、 **[Speed]** ボックスに「9600」と入力します。 次に、 **[Open]**をクリックします。

1. プログラムの実行が開始されます。 接続時にプログラムが自動的に開始されない場合は、ボードのリセットが必要になることがあります (Ctrl キーを押しながら Break キーを押すか、ボードのリセット ボタンを押します)。
   
    ![][10]

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png
[12]: ./media/iot-suite-connecting-devices-mbed/mbed7.png
[13]: ./media/iot-suite-connecting-devices-mbed/mbed8.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration

