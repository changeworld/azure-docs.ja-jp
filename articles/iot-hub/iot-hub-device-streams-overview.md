---
title: Azure IoT Hub デバイス ストリーム | Microsoft Docs
description: さまざまな cloud-to-device 通信シナリオのセキュリティで保護された双方向 TCP トンネルを支援する、Azure IoT Hub デバイス ストリームの概要について説明します。
author: eross-msft
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: lizross
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
ms.openlocfilehash: 2bedbb4e14886e1e16d765f91ad86f7b20989a34
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556004"
---
# <a name="iot-hub-device-streams-preview"></a>IoT Hub デバイス ストリーム (プレビュー)

Azure IoT Hub *デバイス ストリーム* によって、さまざまな cloud-to-device 通信シナリオのセキュリティで保護された双方向 TCP トンネルの作成が容易になります。 デバイス ストリームは、デバイスとサービス エンドポイント間のプロキシとして機能する、IoT Hub *ストリーミング エンドポイント* によって仲介されます。 下の図に示されているこのセットアップは、デバイスがネットワーク ファイアウォールの背後にある場合や、プライベート ネットワーク内にある場合に特に便利です。 そのため、IoT Hub デバイス ストリームは、ファイアウォール フレンドリな方法で、着信または発信ネットワーク ファイアウォール ポートを広範に開くことなく、IoT デバイスにアクセスしたいという、お客様のニーズに応えるのに役立ちます。

!["IoT Hub デバイス ストリームの概要"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

IoT Hub デバイス ストリームを使用することで、デバイスは安全に保たれます。デバイスでは、ポート 443 経由で IoT ハブのストリーミング エンドポイントへのアウトバウンド TCP 接続を開くだけで済みます。 ストリームが確立されると、サービス側とデバイス側のアプリケーションはそれぞれ、WebSocket クライアント オブジェクトにプログラムでアクセスし、RAW 型バイトに対する送受信を行うことができます。 このトンネルによって提供される信頼性と順序の保証は、TCP の場合と同様です。

## <a name="benefits"></a>メリット

IoT Hub デバイス ストリームには次の利点があります。

* **ファイアウォール フレンドリなセキュア接続:** デバイスまたはネットワーク境界でインバウンド ファイアウォール ポートを開かずに、サービス エンドポイントから IoT デバイスにアクセスできます (ポート 443 を経由する必要があるのは、IoT Hub へのアウトバウンド接続のみです)。

* **認証:** トンネルのデバイス側とサービス側の両方で、対応する資格情報を使って、IoT Hub を認証する必要があります。

* **暗号化:** 既定では、IoT Hub デバイス ストリームで TLS 対応の接続が使用されます。 そのため、アプリケーションで暗号化を使用するかどうかに関係なく、トラフィックは常に暗号化されます。

* **接続の簡素化:** 多くの場合、デバイス ストリームを使うことによって、IoT デバイスに接続できるようにするための仮想プライベート ネットワークの複雑なセットアップの必要がなくなります。

* **TCP/IP スタックとの互換性:** IoT Hub デバイス ストリームでは、TCP/IP アプリケーション トラフィックに対応できます。 つまり、広範な専用プロトコルおよび標準ベースのプロトコルで、この機能を利用できます。

* **プライベート ネットワーク セットアップの簡単な使用:** サービスは、デバイスの IP アドレスではなく、デバイス ID を参照することで、デバイスと通信できます。 これは、デバイスがプライベート ネットワーク内にあり、プライベート IP アドレスを持っているか、その IP アドレスが動的に割り当てられており、サービス側に認識されていない場合に便利です。

## <a name="device-stream-workflows"></a>デバイス ストリームのワークフロー

デバイス ID を提供してデバイスに接続するように、サービスによって要求されたときに、デバイス ストリームが開始されます。 このワークフローは特に、ユーザーが SSH または RDP クライアント プログラムを使用して、デバイス上で実行されている SSH または RDP サーバーにリモートで接続しようとしている (SSH および RDP を含む) クライアント/サーバー通信モデルに適しています。

デバイス ストリームの作成プロセスには、デバイス、サービス、IoT ハブのメインおよびストリーミング エンドポイントの間のネゴシエーションが含まれます。 IoT ハブのメイン エンドポイントでデバイス ストリームの作成が調整されている間に、ストリーミング エンドポイントでは、サービスとデバイスの間を流れるトラフィックが処理されます。

### <a name="device-stream-creation-flow"></a>デバイス ストリームの作成フロー

SDK を使用するデバイス ストリームのプログラムによる作成には、下の図にも示されている、次の手順が含まれます。

!["デバイス ストリームのハンドシェイク プロセス"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. デバイス アプリケーションでは、デバイスに対して新しいデバイス ストリームが開始されたときに通知されるように、事前にコールバックが登録されます。 この手順は、通常、デバイスが起動し、IoT Hub に接続されたときに行われます。

2. サービス側のプログラムでは、(IP アドレス _ではなく_) デバイス ID を提供することで、必要なときにデバイス ストリームを開始します。

3. IoT ハブでは、手順 1 で登録したコールバックを呼び出して、デバイス側のプログラムに通知します。 デバイスでは、ストリームの開始要求を受け入れるか拒否する場合があります。 このロジックは、アプリケーションのシナリオに固有である可能性があります。 ストリーム要求がデバイスによって拒否された場合、IoT Hub で適宜、サービスに通知します。それ以外の場合は、以下の手順が続きます。

4. デバイスでは、ポート 443 経由のストリーミング エンドポイントへの安全なアウトバウンド TCP 接続が作成され、接続が WebSocket にアップグレードされます。 ストリーミング エンドポイントの URL と、認証に使用する資格情報は両方とも、手順 3 で送信された要求の一部として IoT Hub によってデバイスに提供されます。

5. サービスは、ストリームを受け入れるデバイスの結果の通知を受け取り、ストリーミング エンドポイントへの独自の WebSocket クライアントの作成に進みます。 同様に、IoT Hub からのストリーミング エンドポイントの URL と認証情報を受信します。

上記のハンドシェイク プロセスの場合:

* ハンドシェイク プロセスは 60 秒以内に完了する必要があります (手順 2 から 5)。そうしないと、ハンドシェイクがタイムアウトになって失敗し、適宜、サービスに通知されます。

* 上記のストリームの作成フローの完了後、ストリーミング エンドポイントはプロキシとして機能し、サービスとデバイス間のトラフィックをそれぞれの WebSocket 経由で転送します。

* デバイスとサービスの両方に、ポート 443 経由のストリーミング エンドポイントと IoT Hub のメイン エンドポイントへのアウトバウンド接続が必要です。 これらのエンドポイントの URL は、IoT Hub のポータルの *[概要]* タブで確認できます。

* 確立されたストリームの信頼性と順序の保証は、TCP の場合と同様です。

* IoT Hub とストリーミング エンドポイントへのすべての接続では TLS が使用され、これらの接続は暗号化されます。

### <a name="termination-flow"></a>終了フロー

ゲートウェイへの TCP 接続のいずれかが (サービスまたはデバイスによって) 切断されたときに、確立されたストリームが終了します。 これは、デバイスまたはサービスのプログラムで WebSocket を閉じることで自発的に、あるいはネットワーク接続がタイムアウトになったか、プロセスが失敗した場合に非自発的に行われる可能性があります。 ストリーミング エンドポイントへのデバイスまたはサービスの接続の終了時に、他の TCP 接続も (強制的に) 終了され、サービスとデバイスでは、必要に応じて、ストリームを再作成することになります。

## <a name="connectivity-requirements"></a>接続の要件

デバイス ストリームのデバイス側とサービス側の両方で、IoT Hub とそのストリーミング エンドポイントへの TLS 対応の接続を確立できる必要があります。 これには、これらのエンドポイントへのポート 443 経由でのアウトバウンド接続が必要です。 これらのエンドポイントに関連付けられているホスト名は、下の図のように、IoT Hub の *[概要]* タブで確認できます。

!["デバイス ストリーム エンドポイント"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

または、`property.hostname` および `property.deviceStreams` キーなど、ハブのプロパティ セクションの下にある Azure CLI を使用して、エンドポイント情報を取得することができます。

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

出力は、ハブのデバイスとサービスが、状況によってはデバイス ストリームを確立するために接続する必要があるすべてのエンドポイントの JSON オブジェクトです。

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Azure CLI バージョン 2.0.57 以降がインストールされていることを確認してください。 最新バージョンは、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」ページからダウンロードできます。
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>デバイス ストリーミング エンドポイントへの送信接続を許可する

この記事の先頭で説明したように、デバイスでは、デバイス ストリームの開始プロセス中に IoT Hub ストリーミング エンドポイントへのアウトバウンド接続が作成されます。 デバイスまたはそのネットワーク上のファイアウォールでは、ポート 443 経由のストリーミング ゲートウェイへのアウトバウンド接続 (TLS を使用して暗号化される WebSocket 接続によって通信が行われることに注意してください) を許可する必要があります。

デバイス ストリーミング エンドポイントのホスト名は、Azure IoT Hub ポータルの [概要] タブで確認できます。!["デバイス ストリーム エンドポイント"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

または、Azure CLI を使用して、この情報を見つけることができます。

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Azure CLI バージョン 2.0.57 以降がインストールされていることを確認してください。 最新バージョンは、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」ページからダウンロードできます。
>

## <a name="troubleshoot-via-device-streams-resource-logs"></a>デバイス ストリーム リソース ログを使用してトラブルシューティングを行う

Azure Monitor を設定して、IoT Hub によって出力される[デバイス ストリームのリソース ログ](monitor-iot-hub-reference.md#device-streams-preview)を収集することができます。 これは、トラブルシューティング シナリオで非常に役立つ場合があります。

次の手順に従って、IoT Hub のデバイス ストリーム ログを Azure Monitor ログに送信するための診断設定を作成します。

1. Azure portal で、お使いの IoT ハブに移動します。 左側のウィンドウの **[モニター]** の下で、 **[診断設定]** を選択します。 次に **[診断設定を追加する]** を選択します。

2. 診断設定の名前を指定し、ログの一覧から **[DeviceStreams]** を選択します。 次に **[Log Analytics への送信]** を選択します。 既存の Log Analytics ワークスペースを選ぶか、新しいものを作成するよう指示されます。

    :::image type="content" source="media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png" alt-text="デバイス ストリーム ログを有効にする":::

3. デバイス ストリーム ログを Log Analytics ワークスペースに送信するための診断設定を作成した後、Azure portal で IoT ハブの左側のウィンドウにある **[モニター]** の **[ログ]** を選択することでログにアクセスできます。 デバイス ストリーム ログは、`AzureDiagnostics` テーブルに表示されます。ログには `Category=DeviceStreams` が含まれます。 操作後、ログがテーブルに表示されるまでに数分かかる場合があることに注意してください。

   以下のように、ターゲット デバイスの ID と操作の結果もログで確認できます。

   !["デバイス ストリーム ログにアクセスする"](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

IoT Hub での Azure Monitor の使用の詳細については、[IoT Hub の監視](monitor-iot-hub.md)に関するページを参照してください。 IoT Hub で使用できるすべてのリソース ログ、メトリック、およびテーブルの詳細については、[Azure IoT Hub のデータ監視のリファレンス](monitor-iot-hub-reference.md)に関するページを参照してください。

## <a name="regional-availability"></a>リージョン別の提供状況

パブリック プレビュー中は、米国中部、米国中部 EUAP、北ヨーロッパ、東南アジア リージョンで IoT Hub デバイス ストリームを利用できます。 これらのリージョンのいずれかでハブを作成するようにしてください。

## <a name="sdk-availability"></a>SDK の可用性

(デバイス側とサービス側の) 各ストリームの 2 つの側で IoT Hub SDK を使用して、トンネルを確立します。 パブリック プレビュー中は、お客様は次の SDK 言語から選ぶことができます。

* C および C# の SDK では、デバイス側のデバイス ストリームがサポートされます。

* NodeJS および C# の SDK では、サービス側のデバイス ストリームがサポートされます。

## <a name="next-steps"></a>次のステップ

以下のリンクを使用して、デバイス ストリームについてさらに詳しく学習します。

> [!div class="nextstepaction"]
> [IoT でのデバイス ストリームのショー (Channel 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
