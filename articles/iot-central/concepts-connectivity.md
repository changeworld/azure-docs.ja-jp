---
title: Azure IoT Central のデバイス接続機能 | Microsoft Docs
description: この記事では、Azure IoT Central のデバイス接続機能に関連する主な概念を紹介します。
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 7e90fb6bcfa1bfab59177cbc6c717fefc163a67a
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52960098"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Azure IoT Central のデバイス接続機能

この記事では、Microsoft Azure IoT Central のデバイス接続機能に関連する主な概念を紹介します。

Azure IoT Central は [Azure IoT Hub Device Provisioning Service (DPS)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps) を使用しているので、大量のデバイスのオンボードと接続をサポートできます。

-   ユーザーは、デバイスの資格情報を生成して、デバイスをオフラインで構成でき、最初に IoT Central でデバイスを登録する必要はありません
-   IoT Central は、業界推奨の X509 証明書ベースの接続でのデバイス接続をサポートしながら、Shared Access Signature (SAS) 接続のサポートと向上を続けます
-   IoT Central のユーザーは、自分のデバイス ID を使用して IoT Central でデバイスを登録し、既存のバックオフィス システムとのシンプルな統合を可能にします
-   IoT Central にデバイスを接続するには 1 つの一貫した方法があります 

>[!NOTE]
>IoT Central は、すべてのデバイスの登録と接続の基礎として Azure IoT Device Provisioning Service (DPS) を使用します。[DPS の詳細](https://docs.microsoft.com/azure/iot-dps/about-iot-dps)

IoT Central にデバイスを接続する方法については、該当するユース ケースを選択してください
1. [1 つのデバイスを簡単に接続する (Shared Access Signature を使用)](#connect-a-single-device)
1. [Shared Access Signature (SAS) を使用して大量のデバイスを接続する](#connect-devices-at-scale-using-shared-access-signatures)
1. [X509 証明書を使用して大量のデバイスを接続する](#connect-devices-using-x509-certificates) (**運用ワークロードに推奨**)
1. [最初にデバイスを登録しないで接続する](#connect-without-first-registering-devices) 


>[!NOTE]
>デバイスの接続とプロビジョニングのグローバル エンドポイントは **global.azure-devices-provisioning.net** です。

## <a name="connect-a-single-device"></a>1 つのデバイスを接続する
SAS を使用して 1 つのデバイスを IoT Central に接続するのは簡単でほんの数ステップです 
1. Device Explorer から**実際のデバイス**を追加し、**[+ 新規] > [Real]\(リアル\)** をクリックして実際のデバイスを追加します。
    * デバイス ID **<span style="color:Red">(小文字のみ)</span>** を入力するか、推奨デバイス ID を使います。
    * デバイス名を入力するか、推奨名を使います   
    ![デバイスの追加](media/concepts-connectivity/add-device.png)
1. デバイス ページの **[接続]** をクリックして、追加したデバイスの **スコープ ID、デバイス ID、プライマリ キー**などの接続の詳細を取得します。
    * **[スコープ ID](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope)** は IoT Central アプリごとであり、DPS によって生成され、アプリ内でデバイス ID を確実に一意にするために使用されます。
    * **デバイス ID** はアプリごとに一意であり、デバイスは登録呼び出しの一部としてデバイス ID を送信する必要があります。   
    * **プライマリ キー**は SAS トークンであり、この特定のデバイスに対して IoT Central によって生成されます。 
    ![接続の詳細情報](media/concepts-connectivity/device-connect.PNG)
1. これらの接続の詳細 (**デバイス ID、デバイス名、デバイスのプライマリ キー**) をデバイス コードで使用して、デバイスをプロビジョニングして接続し、すぐに通過するデータの表示を始めます。 MxChip デバイスを使用している場合は、[こちら](howto-connect-devkit.md#add-a-real-device)の「**DevKit デバイスを準備する**」セクション以降の手順に従います。   

    他の言語を使用する場合の参照を以下に示します。

    *   **C 言語:** C を使う場合は、[この C サンプル デバイス クライアント](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)に従ってサンプル デバイスを接続します。 サンプルの次の設定を使います。   

         ```
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
         
         static const char* const SYMMETRIC_KEY_VALUE = "Enter Primary Symmetric key here";

         static const char* const REGISTRATION_NAME = "Enter Device Id here";
        ```

    *   **Node.js:** Node.js を使う場合は、[こちら](tutorial-add-device.md#prepare-the-client-code)の「**クライアント コードの準備**」セクション以降の手順に従います。



## <a name="connect-devices-at-scale-using-shared-access-signatures"></a>Shared Access Signature を使用して大量のデバイスを接続する

SAS を使用して IoT Central で大量のデバイスを接続するには、2 つの手順が関係します 
1. **デバイスの登録**: CSV ファイルを使用して IoT Central にインポートしてデバイスを登録し、デバイスと接続の詳細をエクスポートして、デバイスの接続に使用します
1. **デバイスのセットアップ**: 接続の詳細 (**スコープ ID、デバイス ID、プライマリ キー**) を使用してデバイスをプログラミングし、電源を入れたときに、プロビジョニング サービスを呼び出して接続情報/IoT Central アプリの割り当てを取得できるようにします。

>[!NOTE]
>高度なオプションもあり、最初に IoT Central でデバイスを登録することなしに、デバイスを接続できます ([詳細はこちら](https://docs.microsoft.com/azure/iot-dps/about-iot-dps))。

**デバイスを登録する**

アプリケーションに多数のデバイスを接続するために、Azure IoT Central では、CSV ファイルを使用してデバイスを一括インポートする機能が提供されています。 

CSV ファイルの要件:CSV ファイルには次の列 (とヘッダー) が必要です
1.  IOTC_DeviceID **<span style="color:Red">(小文字にする必要があります)</span>**
1.  IOTC_DeviceName (省略可能)



アプリケーションでデバイスをインポートして登録します
1.  左側のナビゲーション メニューで **[エクスプローラー]** を選択します。
1.  左側のパネルで一括作成するデバイスのデバイス テンプレートを選択します。 
1.  **[インポート]** をクリックし、インポートするデバイス ID の一覧を含む CSV ファイルを選択します。
CSV ファイルには次の列 (とヘッダー) が必要です
    *   IOTC_DeviceID **<span style="color:Red">(小文字にする必要があります)</span>**
    *   IOTC_DeviceName (省略可能)
1.  インポートが完了すると、デバイス グリッドに完了メッセージが表示されます。

デバイスをエクスポートして接続の詳細を取得します。エクスポートでは、デバイス ID、デバイス名、デバイス キーを含む CSV ファイルが作成されます。 これらの詳細を使用して、IoT Central にデバイスを接続します。
アプリからデバイスを一括でエクスポートするには:
1.  左側のナビゲーション メニューで **[エクスプローラー]** を選択します。
1.  エクスポートするデバイスを選択して、**エクスポート** アクションをクリックします。
1.  エクスポートが完了すると、生成されたファイルをダウンロードするリンクと共に成功メッセージが表示されます。
1.  成功メッセージをクリックし、ディスク上のローカル フォルダーにファイルをダウンロードします。
1.  エクスポートされた CSV ファイルでは、次の列情報が得られます。**デバイス ID、デバイス名、デバイスのプライマリ/セカンダリ キー、およびプライマリ/セカンダリ証明書の拇印**
    *   IOTC_DEVICEID
    *   IOTC_DEVICENAME
    *   IOTC_SASKEY_PRIMARY
    *   IOTC_SASKEY_SECONDARY
    *   IOTC_X509THUMBPRINT_PRIMARY 
    *   IOTC_X509THUMBPRINT_SECONDARY


**デバイスのセットアップ**

接続の詳細 (**デバイス ID (IOTC_DEVICEID)、デバイスのプライマリ キー(IOTC_SASKEY_PRIMARY)、スコープ ID**) をデバイス コードで使用して、デバイスをプロビジョニングおよび接続します。 まだの場合、IoT Central アプリの **[管理] > [デバイスの接続] > [スコープ ID]** から**スコープ ID** を取得します。
**MxChip** デバイスを使用して接続している場合は、[こちら](howto-connect-devkit.md#add-a-real-device)の「**DevKit デバイスを準備する**」セクション以降の手順に従います。   

他の言語を使用する場合の参照を以下に示します。

   *   **C 言語:** C を使う場合は、[この C サンプル デバイス クライアント](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)に従ってサンプル デバイスを接続します。 サンプルの次の設定を使います。   
         ```
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         static const char* const SYMMETRIC_KEY_VALUE = "Enter Primary Symmetric key here";
         static const char* const REGISTRATION_NAME = "Enter Device Id here";
        ```
    * **Node.js:** Node.js を使う場合は、[こちら](tutorial-add-device.md#prepare-the-client-code)の「**クライアント コードの準備**」セクション以降の手順に従います。


## <a name="connect-devices-using-x509-certificates"></a>X509 証明書を使用してデバイスを接続する

X.509 証明書を構成証明メカニズムとして使用することは、**運用**環境を拡張し、デバイスのプロビジョニングを簡素化するための優れた方法です。 X.509 証明書は通常、信頼する証明書チェーンに配置されます。証明書チェーンでは、チェーン内の各証明書が次の上位証明書の秘密キーによって署名され、最後は自己署名ルート証明書で終わります。 これにより、信頼されたルート証明機関 (CA) によって生成されるルート証明書から、各中間 CA 証明書や デバイスにインストールされたエンドエンティティ "リーフ" 証明書に至る、信頼する委任チェーンが確立されます。 詳細については、「[X.509 CA 証明書を使用したデバイス認証](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview)」をご覧ください。 

X509 証明書を使用してデバイスを IoT Central に接続するには、次の 3 つの主要手順が関係します 
1. IoT Central アプリで、デバイス証明書の生成に使用される X509 ルート/中間証明書を追加/確認することで、**接続の設定を構成**します。  X509 証明書の接続設定を構成するには 2 つの手順があります。  

    *   リーフ デバイス証明書の生成に使用している **X509 ルートまたは中間証明書を追加**します。 [管理] > [デバイスの接続] > [証明書] に移動します。 
    
        ![接続の設定](media/concepts-connectivity/connection-settings.PNG)
    *   **証明書の検定:** 証明書の所有権を検証して、証明書のアップロード者が証明書の秘密キーを所有していることを確認します。 証明書を検証するには
        *  確認コード フィールドの横にあるボタンをクリックして、確認コードを生成します。 
        *  確認コードで X.509 検証証明書を作成し、.cer ファイルとして証明書を保存します。 
        *  署名された検証証明書をアップロードして検証をクリックします。

        ![接続の設定](media/concepts-connectivity/verify-cert.png)
    *   **セカンダリ証明書:** IoT ソリューションのライフサイクルの間に、証明書を展開する必要があります。 証明書を展開する主な理由としては、セキュリティ違反および証明書の期限切れの 2 つが挙げられます。 セカンダリ証明書は、プライマリ証明書を更新するときに、プロビジョニングを試行するデバイスのダウンタイムを短縮するために使用されます。

    **テスト目的のみ** 
    
    以下のユーティリティ コマンド ライン ツールを使用して CA 証明書とデバイス証明書を生成できます。

    * MxChip を使用している場合は、この[コマンド ライン ユーティリティ](https://aka.ms/iotcentral-docs-dicetool)を使用して CA 証明書を生成し、IoT Central アプリに追加して、証明書を検証します。 

    *   この[コマンド ライン ツール](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md )を使用して以下を行います
        * 証明書チェーンを作成します (GitHub のドキュメントの手順 2)。 
         証明書を .cer ファイルとして保存し、IoT Central にアップロードします (プライマリ)。   
        * IoT Central アプリから確認コードを取得して、証明書を生成し (GitHub のドキュメントの手順 3)、アップロードして検証します。 
        * ツールへのパラメーターとしてデバイス ID を使用してリーフ証明書を作成します (手順 4)。 証明書を保存し、デバイスで使用します。     

1. CSV ファイルを介して IoT Central にインポートして**デバイスを登録**します。

1. **デバイスのセットアップ**:アップロードしたルート証明書を使用してリーフ証明書を生成します。 リーフ証明書の CNAME として**デバイス ID** を使用していて**小文字**であることを確認します。 **テスト目的のみ**にリーフ/デバイス証明書を生成するにはこちらの[コマンド ライン ツール](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md )です。

    スイッチを入れたときに接続詳細と IoT Central アプリ割り当てを取得するようにプロビジョニング サービス情報でデバイスをプログラムします。    

    **詳細参照** 
    *   [RaspberryPi](https://aka.ms/iotcentral-docs-Raspi-releases) のサンプル実装。  

    *   [C のサンプル デバイス クライアント](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)。

>[!NOTE]
>デバイスのリーフ証明書を生成するときに CNAME として**デバイス ID** を使用します。

>[!NOTE]
>**デバイス ID** は小文字でなければなりません 
 
## <a name="connect-without-first-registering-devices"></a>最初にデバイスを登録しないで接続する
IoT Central によって有効になる主要シナリオの 1 つは、OEM がデバイスを大量に製造し、資格情報を生成し、先に IoT Central に登録しないで工場で構成する場合です。 デバイスがオンになって IoT Central に接続されると、オペレーターはデバイスが IoT Central アプリに接続するのを承認します。

この機能でデバイスを接続するフローを次に示します

![接続の設定](media/concepts-connectivity/device-connection-flow.PNG)


デバイス認証方式 (X509/SAS) の選択に基づく手順に従います

1. **接続の設定** 
    * **X509 証明書:** [ルート/中間証明書を追加および検証](#connect-devices-using-x509-certificates)し、次の手順でそれを使用してデバイス証明書を生成します。
    * **SAS:** プライマリ キー (このキーは、この IoT Central アプリケーションのグループ SAS キーです) をコピーし、次の手順でそれを使用してデバイス SAS キーを生成します。 
![接続設定 SAS](media/concepts-connectivity/connection-settings-sas.png)

1. **デバイスの資格情報を生成します** 
    *   **証明書 X509:** このアプリに追加したルート/中間証明書を使用して、デバイスのリーフ証明書を生成します。 リーフ証明書の CNAME として**デバイス ID** を使用していて**<span style="color:Red">小文字</span>** であることを確認します。 テスト用のリーフ/デバイス証明書を生成するにはこちらの[コマンド ライン ツール](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md )です。
    *   **SAS** デバイス SAS キーは、この[コマンド ライン ツール](https://www.npmjs.com/package/dps-keygen)を使用して生成できます。 前の手順のプライマリ SAS キー (グループ SAS キー) を使用します。 デバイス ID が**<span style="color:Red">小文字で</span>** であることを確認します。

        以下の手順を使用してデバイス SAS キーを生成します           

        ```
        npm i -g dps-keygen
        ```
    
        **使用方法**
                        
        ```
        dps-keygen <Primary_Key(GroupSAS)> <device_id>
        ```

1. **デバイスのセットアップ** 
    
     **スコープ ID、デバイス ID、デバイス証明書/SAS キー**を使用してデバイスをフラッシュし、デバイスをオンにして IoT Central アプリに接続します。

1. **IoT Central へのデバイスの接続:** オンになったデバイスは登録のために DPS/IoT Central に接続します。

1. **テンプレートをデバイスに関連付け:** 接続されたデバイスは、**Device Explorer** の **[UnAssociated Devices]\(関連付けられていないデバイス\)** の下に表示されます。 デバイスのプロビジョニング状態は **[登録済み]** です。 デバイスを適切なデバイス テンプレートに**関連付け**て、デバイスが IoT Central アプリに接続するのを承認します。 デバイスは IoT Central アプリに対する接続の詳細を取得し、接続して、データの送信を開始します。 デバイスのプロビジョニングが完了し、*[プロビジョニング状態]* が **[プロビジョニング済み]** になります。

## <a name="device-provisioning-status"></a>デバイスのプロビジョニング状態
実際のデバイスが Azure IoT Central に接続するときは、一連の手順があります 
1. **登録済み**: デバイスは最初に**登録済み**になります。これは、デバイスが IoT Central で作成され、デバイスのデバイス ID があることを意味します。
デバイスは次のときに登録済みになります  
    *   1 台の新しい実際のデバイスが **Explorer** で追加された
    *   一連のデバイスが **Explorer** で **[インポート]** を使用して追加された
    *   デバイスは登録されていないが、有効な資格情報で接続しており、**関連付けられていない**デバイスに表示されている 

    上記のすべての場合に、*プロビジョニング状態*は**登録済み**になります

1. **プロビジョニング済み**: 次のステップは、デバイスが有効な資格情報で接続し、IoT Central がプロビジョニング手順を完了したときです (IoT Hub でデバイスを作成して)。 その後、接続してデータ送信を開始するための接続文字列をデバイスに返します。 デバイスの*プロビジョニング状態*は**登録済み**から**プロビジョニング済み**に変わります。

1.  **ブロック済み**: オペレーターはデバイスをブロックすることができ、ブロックされたデバイスは IoT Central にデータを送信できず、リセットする必要があります。 ブロックされたデバイスの*プロビジョニング状態*は**ブロック済み**になります。 オペレーターは、デバイスをブロック解除することもできます。 デバイスをブロック解除すると、*プロビジョニング状態*は前の*状態*に戻ります (登録済み、またはプロビジョニング済み)。 

## <a name="getting-device-connection-string"></a>デバイスの接続文字列の取得
次の手順を使用して、Azure IoT Hub への IoT Hub デバイスの接続文字列を取得できます 
1. デバイス ページから、**スコープ ID、デバイス ID、デバイス プライマリ キー**などの接続の詳細を取得します (デバイス ページで [接続] をクリック) 

    ![接続の詳細](media/concepts-connectivity/device-connect.PNG)

1. 以下のコマンド ライン ツールを使用してデバイスの接続文字列を取得します。
    デバイスの接続文字列を取得するには次の命令を使用します  

    ```cmd/sh
    npm i -g dps-keygen
    ```
    **使用方法**

    接続文字列を作成するには、bin/ フォルダーでバイナリを見つけます
    ```cmd/sh
    dps_cstr <scope_id> <device_id> <Primary Key(for device)>
    ```
    詳しくは、[こちらの dps-keygen ツール](https://www.npmjs.com/package/dps-keygen)をご覧ください。

## <a name="sdk-support"></a>SDK のサポート

Azure Device SDK を使用すると、Azure IoT Central アプリケーションに接続するデバイス上にコードを簡単に実装できます。 次のデバイス SDK が使用できます。

- [Azure IoT SDK for C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK for Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK for Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK for Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK for .NET](https://github.com/azure/azure-iot-sdk-csharp)

各デバイスは、デバイスを識別する一意の接続文字列を使用して接続します。 デバイスは登録されている IoT ハブにのみ接続できます。 Azure IoT Central アプリケーションで実際のデバイスを作成すると、アプリケーションは、使用する接続文字列を生成します。

## <a name="sdk-features-and-iot-hub-connectivity"></a>SDK の機能と IoT Hub の接続機能

IoT Hub を使用するすべてのデバイス通信では、次の IoT Hub 接続オプションを使用します。

- [デバイスからクラウドへのメッセージ](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [デバイス ツイン](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

次の表では、Azure IoT Central デバイスの機能が IoT Hub の機能にどのように対応しているかをまとめたものです。

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| 測定:テレメトリ | デバイスからクラウドへのメッセージ |
| デバイスのプロパティ | デバイス ツインの報告されるプロパティ |
| 設定 | デバイス ツインの目的および報告されるプロパティ |

デバイス SDK の使用の詳細については、次に関連する記事のコード例を参照してください。

- [汎用の Node.js クライアント を Azure IoT Central アプリケーションに接続する](howto-connect-nodejs.md)
- [Raspberry Pi デバイスを Azure IoT Central アプリケーションに接続する ](howto-connect-raspberry-pi-python.md)
- [DevDiv キット デバイスを Azure IoT Central アプリケーションに接続する ](howto-connect-devkit.md)


## <a name="protocols"></a>プロトコル

デバイス SDK は、次のネットワーク プロトコルを使った IoT Hub への接続をサポートしています。

- MQTT
- AMQP
- HTTPS

これらプロトコルの相違点や、プロトコルを選択する上でのガイダンスについては、「[通信プロトコルの選択](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols)」を参照してください。

デバイスでサポートされているプロトコルのいずれも使用できない場合は、Azure IoT Edge を使用して、プロトコルを変換します。 IoT Edge は、Azure IoT Central アプリケーションの処理負荷を軽減するための他のエッジ上のインテリジェンス シナリオをサポートします。

## <a name="security"></a>セキュリティ

デバイスと、Azure IoT Central 間で交換されるすべてのデータは暗号化されます。 IoT Hub は IoT Hub エンドポイントに接続するデバイスからのすべての要求を認証します。 有線上で資格情報を交換することを避けるため、デバイスでは、認証に署名入りトークンが使用されます。 詳細については、「[IoT Hub へのアクセスの制御](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security)」を参照してください。

> [!NOTE]
> 現時点では、Azure IoT Central に接続するデバイスは、SAS トークンを使用する必要があります。 Azure IoT Central に接続するデバイスでは、X.509 証明書はサポートされていません。

## <a name="next-steps"></a>次の手順

ここでは、Azure IoT Central のデバイス接続機能について説明しました。推奨される次の手順は次のとおりです。

- [DevKit デバイスを準備して接続する](howto-connect-devkit.md)
- [Raspberry Pi を準備して接続する](howto-connect-raspberry-pi-python.md)
- [汎用の Node.js クライアント を Azure IoT Central アプリケーションに接続する](howto-connect-nodejs.md)
