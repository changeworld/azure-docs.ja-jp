---
title: Azure Industrial IoT Platform
description: この記事では、Industrial IoT Platform とそのコンポーネントの概要について説明します。
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 24932b17c630190cb3121d5310a865758fa6a920
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801555"
---
# <a name="what-is-the-industrial-iot-iiot-platform"></a>Industrial IoT (IIoT) Platform の概要

Azure Industrial IoT Platform は、Azure にデプロイされる Microsoft のモジュールとサービスのスイートです。 これらのモジュールとサービスでは、開放性を完全に採用しています。 具体的には、Azure のサービスとしてのマネージド プラットフォーム (PaaS) オファリング、MIT ライセンスによってライセンスが付与されたオープンソース ソフトウェア、通信 (OPC UA、AMQP、MQTT、HTTP) とインターフェイス (OpenAPI) の国際的なオープン標準、エッジおよびクラウドでのオープン産業データ モデル (OPC UA) を適用します。

## <a name="enabling-shopfloor-connectivity"></a>ショップフロア接続を有効にする 

Azure Industrial IoT Platform は、ショップフロア資産 (OPC UA が有効な資産の検出を含む) の産業用接続を対象として、データを OPC UA 形式に正規化し、OPC UA PubSub 形式で Azure に資産の利用統計情報を送信します。 そこでは、利用統計情報がクラウド データベースに格納されます。 さらに、プラットフォームにより、クラウドから OPC UA 経由でショップフロア資産に安全にアクセスできます。 デバイス管理機能 (セキュリティ構成を含む) も組み込まれています。 OPC UA 機能は、デプロイと管理が容易になる Docker コンテナー テクノロジを使用して構築されています。 OPC UA が有効になっていない資産については、業界をリードする産業用接続プロバイダーとの提携により、Azure IoT Edge への OPC UA アダプター ソフトウェアの移植を支援しました。 これらのアダプターは、Azure Marketplace で入手できます。

## <a name="industrial-iot-components-iot-edge-modules-and-cloud-microservices"></a>Industrial IoT コンポーネント: IoT Edge モジュールとクラウド マイクロサービス

エッジ サービスは Azure IoT Edge モジュールとして実装され、オンプレミスで実行されます。 クラウド マイクロサービスは、REST インターフェイスを備える ASP.NET マイクロサービスとして実装され、マネージド Azure Kubernetes Services で、または Azure App Service 上でのスタンドアロンで実行されます。 当社では、Microsoft Container Registry (MCR) に事前に構築された Docker コンテナーを提供することにより、エッジ サービスとクラウド サービスの両方で、この顧客向け手順を削除しました。 エッジ サービスとクラウド サービスは互いを利用しており、一緒に使用する必要があります。 また、1 つのコマンドでプラットフォーム全体をデプロイできる、使いやすいデプロイ スクリプトも用意されています。

## <a name="next-steps"></a>次のステップ

Azure Industrial IoT Platform の概要を学んだので、OPC Publisher について確認できます。

> [!div class="nextstepaction"]
> [OPC Publisher とは](overview-what-is-opc-publisher.md)