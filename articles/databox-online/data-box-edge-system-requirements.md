---
title: Microsoft Azure Data Box Edge のシステム要件 | Microsoft Docs
description: Azure Data Box Edge のソフトウェア要件とネットワーキング要件について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 11/06/2018
ms.author: alkohli
ms.openlocfilehash: 7f03953739eebc63c57b30750e15329f24a00537
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263447"
---
# <a name="azure-data-box-edge-system-requirements-preview"></a>Azure Data Box Edge のシステム要件 (プレビュー)

この記事では、Microsoft Azure Data Box Edge ソリューション、および Azure Data Box Edge に接続するクライアントのシステム要件のうち、重要なものについて説明します。 この情報を慎重に確認してから Data Box Edge を展開することをお勧めします。 展開中およびその後の操作中に、必要に応じてこの情報を参照できます。

Data Box Edge のシステム要件は次のとおりです。

- **ホストのソフトウェア要件** - サポートされているプラットフォーム、ローカル構成 UI 用のブラウザー、SMB クライアント、およびデバイスにアクセスするクライアントのその他の要件について説明します。
- **デバイスのネットワーク要件** - 物理デバイスの操作のためのネットワーク要件について説明します。

> [!IMPORTANT]
> Edge はプレビュー段階です。 このソリューションをデプロイする前に、「[プレビューの使用条件に関するページ](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を確認してください。

## <a name="supported-os-for-clients-connected-to-device"></a>デバイスに接続されるクライアントでサポートされている OS

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>デバイスにアクセスするクライアントでサポートされるプロトコル

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>サポートされるストレージ アカウント

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>サポートされるストレージの種類

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>ローカル Web UI 用にサポートされているブラウザー

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="port-configuration-for-data-box-edge"></a>Data Box Edge のポート構成

SMB、クラウド、または管理トラフィックを許可するためにファイアウォールで開く必要があるポートを次の表に示します。 この表では、"*イン*" ("*受信*") は、着信クライアント要求がデバイスにアクセスする方向を意味します。 "*アウト*" ("*送信*") は Data Box Edge デバイスがデプロイを超えて外部に (たとえば、インターネットに) データを送信する方向を意味します。

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="port-configuration-for-iot-edge"></a>IoT Edge のポート構成

Azure IoT Edge では、サポートされている IoT Hub プロトコルを使用した、オンプレミスの Edge デバイスから Azure クラウドへのアウトバウンド通信が許可されています。 インバウンド通信が必要なのは、Azure IoT Hub がメッセージを Azure IoT Edge デバイスにプッシュ ダウンする必要がある特定のシナリオのみです (たとえば、クラウドからデバイスへのメッセージング)。

Azure IoT Edge ランタイムをホストするサーバーのポート構成には、次の表を使用します。

| ポート番号 | インまたはアウト | ポート範囲 | 必須 | ガイダンス |
|----------|-----------|------------|----------|----------|
| TCP 5671 (AMQP)| アウト       | WAN        | [はい]      | IoT Edge の既定の通信プロトコル。 Azure IoT Edge が他のサポートされているプロトコル用に構成されていない場合、または AMQP が望ましい通信プロトコルである場合は、オープンにする必要があります。 <br>AMQP での 5672 は、IoT Edge ではサポートされていません。 <br>Azure IoT Edge が、IoT Hub でサポートされているのとは異なるプロトコルを使用する場合は、このポートをブロックします。 |
| TCP 443 (HTTPS)| アウト       | WAN        | [はい]      | IoT Edge のプロビジョニングのため、送信用に開きます。 メソッド要求を送信することがあるリーフ デバイスを備えた透過的なゲートウェイがある場合。 この場合、ポート 443 は、IoT Hub に接続したり Azure IoT Edge を通じて IoT Hub サービスを提供したりするために外部ネットワークに対してオープンにする必要はありません。 そのため、受信規則は内部ネットワークからのオープンなインバウンドだけに制限することができます。 |
| TCP 5671 (AMQP) | 場所        |            | いいえ        | インバウンド接続はブロックする必要があります。|
| TCP 443 (HTTPS) | 場所        |            | 場合によっては、コメントを参照してください | インバウンド接続が以下の特定のシナリオだけでオープンになるようにする必要があります。 AMQP、MQTT などの非 HTTP プロトコルを構成できない場合は、ポート 443 を使用してメッセージを WebSockets 経由で送信できます。 |

詳細は、[IoT Edge デプロイのファイアウォール規則とポート構成規則](https://docs.microsoft.com/azure/iot-edge/troubleshoot)を参照してください。

## <a name="url-patterns-for-firewall-rules"></a>ファイアウォール ルールの URL パターン

多くの場合、ネットワーク管理者は、受信トラフィックと送信トラフィックをフィルターする URL パターンに基づいて、高度なファイアウォール ルールを構成できます。 Data Box Edge デバイスとサービスは、Azure Service Bus、Azure Active Directory Access Control、ストレージ アカウント、Microsoft Update サーバーなど、他の Microsoft アプリケーションに依存しています。 その Microsoft アプリケーションと関連付けられた URL パターンを使用してファイアウォール ルールを構成できます。 Microsoft アプリケーションに関連付けられた URL パターンは変化する可能性がある点を理解することが重要です。 この変更のため、ネットワーク管理者は必要に応じて Data Box Edge のファイアウォール ルールを監視し更新する必要があります。

ほとんどの場合、送信トラフィックのファイアウォール ルールは Data Box Edge 固定 IP アドレスに基づいて設定することが推奨されます。 ただし、次の情報を使用して、セキュリティで保護された環境を作成するのにために必要な高度なファイアウォール ルールを設定することもできます。

> [!NOTE]
> - デバイスの (送信元) IP は、常にすべてのクラウド対応ネットワーク インターフェイスに合わせて設定します。
> - 宛先 IP は、[Azure データセンターの IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)に合わせて設定するようにします。

|     URL パターン                                                                                                                                                                                                                                                                                                                                                                                                                                       |     コンポーネントまたは機能                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
|    https://*.databoxedge.azure.com/*<br>https://*.servicebus.windows.net/*<br>https://login.windows.net                                                                                                                                                                                                                                                                                                        |    Edge サービス<br>Azure Service Bus<br>認証サービス    |
|    http://*.backup.windowsazure.com                                                                                                                                                                                                                                                                                                                                                                                                                   |    デバイスのアクティブ化                                                                                    |
|    http://crl.microsoft.com/pki/*   http://www.microsoft.com/pki/*                                                                                                                                                                                                                                                                                                                                                                                    |    証明書の失効                                                                               |
|    https://*.core.windows.net/*   https://*.data.microsoft.com   http://*.msftncsi.com                                                                                                                                                                                                                                                                                                                                                                |    Azure ストレージ アカウントと監視                                                                |
|    http://windowsupdate.microsoft.com<br>http://*.windowsupdate.microsoft.com<br>https://*.windowsupdate.microsoft.com<br>http://*.update.microsoft.com<br>https://*.update.microsoft.com<br>http://*.windowsupdate.com<br>http://download.microsoft.com<br>http://*.download.windowsupdate.com<br>http://wustat.windows.com<br>http://ntservicepack.microsoft.com<br>http://*.ws.microsoft.com<br>https://*.ws.microsoft.com<br>http://*.mp.microsoft.com        |    Microsoft Update サーバー                                                                             |
|    http://*.deploy.akamaitechnologies.com                                                                                                                                                                                                                                                                                                                                                                                                             |    Akamai CDN                                                                                           |
|    https://*.partners.extranet.microsoft.com/*                                                                                                                                                                                                                                                                                                                                                                                                        |    サポート パッケージ                                                                                      |
|    http://*.data.microsoft.com                                                                                                                                                                                                                                                                                                                                                                                                                        |    Windows のテレメトリ サービス (「顧客満足度及び診断テレメトリのための更新プログラム」を参照)      |
|                                                                                                                                                                                                                                                                                                                                                                                                                                                       |                                                                                                         |

## <a name="internet-bandwidth"></a>インターネット帯域幅

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>次のステップ

* [Azure Data Box Edge をデプロイする](data-box-Edge-deploy-prep.md)
