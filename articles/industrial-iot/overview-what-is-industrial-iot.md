---
title: Azure Industrial IoT の概要
description: この記事では、産業用 IoT の概要を示します。 IIoT における作業現場の接続とセキュリティのコンポーネントについて説明します。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 3/22/2021
ms.openlocfilehash: 902cd7c9cf0f682541be86e82a1d51525ad5b3f9
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110678033"
---
# <a name="what-is-industrial-iot-iiot"></a>産業用 IoT (IIoT) の概要

![産業用 IoT](media/overview-what-is-Industrial-IoT/icon-255-px.png)

Microsoft Azure Industrial Internet of Things (IIoT) は、製造工業の作業現場にクラウドの機能を統合する Azure のモジュールとサービスのスイートです。 [Open Platform Communications Unified Architecture (OPC UA)](https://opcfoundation.org/about/opc-technologies/opc-ua/) など、業界標準のオープン インターフェイスが使用されている Azure IIoT を通じて、資産やセンサー (既に製造施設で稼動しているものも含む) からのデータを Azure クラウドに統合することができます。 データをクラウドに置くことで、そのデータをビジネスや産業における斬新なプロセスを開発するためのフィードバックとして、より迅速かつ柔軟に使用できるようになります。

## <a name="discover-register-and-manage-your-industrial-assets-with-azure"></a>Azure を使用して産業用資産を検出、登録、管理する

Azure Industrial IoT プラットフォームを使用すると、工場のオペレーターは、製造施設のネットワーク内の OPC UA 対応サーバーを検出し、Azure IoT Hub に登録できます。 運用担当者は、製造施設で発生するイベントをサブスクライブして、世界中のどこからでも、それらのイベントに対応することができます。また、アラートやアラームを受信し、リアルタイムで対応することも可能です。

IIoT には、OPC UA の機能を実装する一連のマイクロサービスが備わっています。 マイクロサービスの REST API は、OPC UA サービスのエッジ側をミラー化します。 これにより、クラウド アプリケーションで、サーバーのアドレス空間の参照、変数の読み取りまたは書き込み、HTTPS と単純な OPC UA JSON ペイロードを使用したメソッドの実行が可能になります。 エッジ サービスは Azure IoT Edge モジュールとして実装され、オンプレミスで実行されます。 クラウド マイクロサービスは、REST インターフェイスを備える ASP.NET マイクロサービスとして実装され、マネージド Azure Kubernetes Services で、または Azure App Service 上でのスタンドアロンで実行されます。 エッジとクラウド サービスの両方に対して、IIoT では、Microsoft Container Registry (MCR) に事前に構築された Docker コンテナーを用意しています。 エッジ サービスとクラウド サービスは互いを利用しており、一緒に使用する必要があります。 IIoT には、1 つのコマンドでプラットフォーム全体をデプロイできる、使いやすいデプロイ スクリプトも用意されています。

加えて REST API は、その公開されている Open API 仕様 (Swagger) を通じて、あらゆるプログラミング言語から使用することができます。 つまり、OPC UA をクラウド管理ソリューションに統合する際、開発者は、そのスキルや関心、アーキテクチャの選択にマッチしたテクノロジを自由に選ぶことができます。 たとえば、アラームやイベント ダッシュボードに対応したアプリケーションを開発するフル スタックの Web 開発者は、OPC UA SDK、C、C++、Java、C# に習熟しなくても、イベントに反応するロジックを JavaScript や TypeScript で記述できます。

## <a name="manage-certificates-and-trust-groups"></a>証明書と信頼グループを管理する

OPC UA クライアントと OPC UA サーバーの間の通信にセキュリティを確保するために、製造施設の機械や制御システムの OPC UA アプリケーションの証明書と信頼リストが Azure Industrial IoT によって管理されます。 これによって、サーバーと通信を行うことのできるクライアントが制限されます。 秘密キーの保管と証明書の署名は、ハードウェア ベースのセキュリティ (HSM) をサポートする Azure Key Vault を利用して実現されています。

## <a name="industrial-iot-components"></a>産業用 IoT のコンポーネント

Azure IIoT のソリューションは、特定のコンポーネントから構築されています。 その例を次に示します。

- **少なくとも 1 つの Azure IoT Hub。**
- **IoT Edge デバイス。**
- **産業用エッジ モジュール。**

### <a name="iot-hub"></a>IoT Hub
[Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) は、IoT アプリケーションとそれによって管理されるデバイスとの間で行われる安全な双方向通信のための中央メッセージ ハブとして機能します。 これは、オープンソースの SDK と複数のプロトコルをサポートするオープンで柔軟な、サービスとしてのクラウド プラットフォーム (PaaS) です。 

産業データやビジネス データを IoT ハブに集約することで、データを安全に保管し、そのデータに基づいてビジネス分析や効率分析を行い、それからレポートを生成することができます。 統合されたデータに対して、[Power BI](https://powerbi.microsoft.com) など、Microsoft Azure のサービスやツールを適用することもできます。

### <a name="iot-edge-devices"></a>IoT Edge デバイス
[エッジ サービス](https://azure.microsoft.com/services/iot-edge/)は Azure IoT Edge モジュールとして実装され、オンプレミスで実行されます。 クラウド マイクロサービスは、REST インターフェイスを備える ASP.NET マイクロサービスとして実装され、マネージド Azure Kubernetes Services で、または Azure App Service 上でのスタンドアロンで実行されます。 当社では、Microsoft Container Registry (MCR) に事前に構築された Docker コンテナーを提供することにより、エッジ サービスとクラウド サービスの両方で、この顧客向け手順を削除しました。 エッジ サービスとクラウド サービスは互いを利用しており、一緒に使用する必要があります。 また、1 つのコマンドでプラットフォーム全体をデプロイできる、使いやすいデプロイ スクリプトも用意されています。

IoT Edge デバイスは、Edge ランタイムと Edge モジュールで構成されています。
- "**Ege モジュール**" は、OPC Publisher や OPC Twin など、評価の最小単位である Docker コンテナーです。 
- "**Edge デバイス**" は、このようなモジュールのデプロイに使用され、クラウド内の OPC UA サーバーと IoT Hub の間の仲介役として機能します。

### <a name="industrial-edge-modules"></a>産業用エッジ モジュール
- **OPC Publisher**: OPC Publisher は IoT Edge 内で実行されます。 これは、OPC UA サーバーに接続し、これらのサーバーから JSON でエンコードされた利用統計情報を OPC UA "Pub/Sub" 形式で Azure IoT Hub に発行します。 Azure IoT Hub クライアント SDK でサポートされているすべてのトランスポート プロトコル (つまり、HTTPS、AMQP、MQTT) を使用できます。
- **OPC Twin**: OPC Twin は、Azure IoT Edge と IoT Hub を使用してクラウドと工場のネットワークとを接続するマイクロサービスから成ります。 OPC Twin は、REST API を通じて、産業用デバイスの検出、登録、リモート制御を実現します。 OPC Twin には、OPC Unified Architecture (OPC UA) SDK は不要です。 これは、プログラミング言語に依存せず、サーバーレス ワークフローに組み込むことができます。
- **Discovery**: 探索プログラム ID によって表される検出モジュールは、エッジに検出サービス (OPC UA サーバー検出を含む) を提供します。 検出が構成され、有効になっている場合、このモジュールにより、スキャン プローブの結果が IoT Edge および IoT Hub テレメトリ パスを介してオンボード サービスに送信されます。 このサービスで、結果が処理され、レジストリ内のすべての関連する ID が更新されます。

## <a name="next-steps"></a>次のステップ
Industrial IoT の概要を学習したので、OPC Publisher の詳細を確認するか、IIoT プラットフォームのデプロイを開始できます。

> [!div class="nextstepaction"]
> [OPC Publisher とは](overview-what-is-opc-publisher.md)

> [!div class="nextstepaction"]
> [Industrial IoT プラットフォームをデプロイする](tutorial-deploy-industrial-iot-platform.md)