---
title: インクルード ファイル
description: インクルード ファイル
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: ea57f8cdf5e1b2460f396445c67cfcab28f07525
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2020
ms.locfileid: "75840641"
---
この記事では、Azure の IoT インフラストラクチャを保護する詳細事項の次のレベルを説明します。 各コンポーネントを設定し、デプロイする実装レベルの詳細へリンクします。 また、さまざまな方法の比較や選択肢についても説明しています。

Azure IoT デプロイの保護は、次の 3 つのセキュリティ領域に分類できます。

* **デバイスのセキュリティ**:デプロイされている間に、IoT デバイスをセキュリティ保護。

* **接続のセキュリティ**:IoT デバイスと IoT Hub の間で送信されるすべてのデータの保護、改ざん防止。

* **クラウド セキュリティ**:クラウド経由での移動、またクラウド格納によりデータを保護する手段の提供。

![次の 3 つのセキュリティ領域](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>安全なデバイス プロビジョニングと認証

IoT ソリューション アクセラレータでは、次の 2 つの方法を使って IoT デバイスをセキュリティで保護します。

* デバイスで IoT Hub と通信するために使用される各デバイスの一意の id キー (セキュリティ トークン) の提供。

* IoT Hub にデバイスを認証させる手段として、オンデバイスの [X.509 証明書](https://www.itu.int/rec/T-REC-X.509-201210-S)と秘密キーの使用。 この認証メソッドでは、デバイスの秘密キーがデバイス以外では分からなくなるため、常に高レベルのセキュリティ保護を提供できます。

セキュリティ トークンの方法では、各呼び出しに対称キーを関連付けることによって、IoT Hub にデバイスで実行する各呼び出しの認証を提供します。 X.509 ベースの認証は、TLS 接続の確立の一部として物理層に IoT デバイスの認証を許可します。 安全性が低いパターンである X.509 認証を行わず、セキュリティ トークン ベースのメソッドを使用できます。 2 つの方法の選択は主に、いかに安全なデバイス認証が必要であるか、また (秘密キーを安全に格納して) デバイスにセキュリティで保護された記憶域の可用性によって決まります。

## <a name="iot-hub-security-tokens"></a>IoT Hub のセキュリティ トークン

IoT Hub では、デバイスとサービスの認証にセキュリティ トークンを使用することにより、ネットワーク経由でのキーの送信を回避します。 さらに、セキュリティ トークンには、有効期間とスコープの制限があります。 Azure IoT SDK を使用すると、自動的にトークンを生成できます。その際、特別な構成は必要ありません。 ただし、一部のシナリオでは、ユーザーがセキュリティ トークンを直接生成して使用する必要があります。 これらのシナリオには、MQTT、AMQP、または HTTP サーフェスの直接的な使用や、トークン サービス パターンの実装が含まれます。

セキュリティ トークンとその使用状況の構造の詳細については、次の記事で説明します。

* [セキュリティ トークンの構造](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [デバイスとして SAS トークンを使用する](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

各 IoT Hub には、転送中の cloud-to-device メッセージを含むキューなど、サービス内にデバイスごとのリソースを作成したり、デバイス向けのエンドポイントへのアクセスを許可したりするために使用できる [ID レジストリ](../articles/iot-hub/iot-hub-devguide-identity-registry.md)があります。 IoT Hub ID レジストリでは、ソリューションのデバイス ID とセキュリティ キーのセキュリティで保護されたストレージが提供されます。 デバイス ID は個別に、またはまとめて許可リストあるいはブロック リストに追加できるため、デバイスへのアクセスを完全に制御できます。 次の記事では、ID レジストリの構造とサポートされている操作について詳しく説明します。

[IoT Hub では、MQTT、AMQP、HTTP などのプロトコルがサポートされます](../articles//iot-hub/iot-hub-devguide-security.md)。 各プロトコルは、IoT デバイスから IoT Hub まで異なる方法でセキュリティ トークンを使用します。

* AMQP:SASL PLAIN および AMQP 要求ベースのセキュリティ (IoT Hub レベルのトークンの場合、`{policyName}@sas.root.{iothubName}`。デバイス スコープのトークンの場合、`{deviceId}`)。

* MQTT:CONNECT パケットでは、`{ClientId}`、 **[ユーザー名]** フィールドの `{IoThubhostname}/{deviceId}`、および **[パスワード]** フィールドの SAS トークンとして `{deviceId}` が使用されます。

* HTTP:有効なトークンは、承認要求ヘッダーにあります。

IoT Hub ID レジストリを使用して、デバイスごとのセキュリティ資格情報とアクセス制御を構成できます。 ただし、IoT ソリューションで既に[カスタム デバイス ID レジストリや認証スキーム](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication)にかなり投資している場合、トークン サービスを作成することで、この既存のインフラストラクチャに IoT Hub を統合できます。

### <a name="x509-certificate-based-device-authentication"></a>X.509 証明書ベースのデバイスの認証

[デバイスベースの X.509 証明書](../articles/iot-hub/iot-hub-devguide-security.md)およびそれに関連付けられている秘密キーと公開キーの組を使用することにより、物理層で追加の認証が可能になります。 秘密キーは、デバイスに安全に格納され、デバイスの外側では検出されません。 X.509 証明書には、デバイス ID など、デバイスとその他の組織の詳細に関する情報が含まれています。 証明書の署名は、秘密キーを使用して生成されます。

高度なデバイスのプロビジョニング フロー:

* デバイスの製造または有効化中に、デバイスに関連付けられた物理デバイス - デバイス ID や X.509 証明書に対する識別子を結び付けます。

* IoT Hub – デバイス ID や IoT Hub ID レジストリの関連するデバイス情報に対応する ID のエントリを作成します。

* IoT Hub ID レジストリの X.509 証明書の拇印を安全に格納します。

### <a name="root-certificate-on-device"></a>デバイス上のルート証明書

IoT Hub でセキュリティで保護された TLS 接続を確立している間は、IoT デバイスは、デバイスの SDK の一部であるルート証明書を使用して IoT Hub を認証します。 C クライアントの SDK では、証明書はリポジトリのルートの下の、"\\c\\certs" フォルダーの下にあります。 これらのルート証明書の有効期間が長期間であるとしても、それが切れるまたは取り消される可能性があります。 デバイスで証明書を更新できない場合、デバイスは IoT Hub (またはその他のクラウド サービス) に、後で接続することできません。 IoT デバイスをデプロイした後に、ルート証明書を更新するための手段を持つと、このようなリスクを効果的に軽減します。

## <a name="securing-the-connection"></a>接続のセキュリティ保護

IoT デバイスと IoT Hub の間でのインターネット接続は、トランスポート層セキュリティ (TLS) 規格を使用してセキュリティ保護されます。 Azure IoT では [TLS 1.2](https://tools.ietf.org/html/rfc5246)、TLS 1.1、TLS 1.0 の順序でサポートされます。 TLS 1.0 のサポートは、旧バージョンとの互換性を保つために提供されます。 最高のセキュリティを提供するため、可能な場合は TLS 1.2 を使用するように[ハブを構成](../articles/iot-hub/iot-hub-tls.md)してください。

## <a name="securing-the-cloud"></a>クラウドを保護する

Azure IoT Hub ではセキュリティ キーごとに[アクセス制御ポリシー](../articles/iot-hub/iot-hub-devguide-security.md)の定義付けが行われます。 それは次の一連のアクセス許可を使用して、各 IoT Hub のエンドポイントへのアクセスを許可します。 次のアクセス許可により、機能に応じて IoT Hub へのアクセスを制限します。

* **RegistryRead**。 ID レジストリへの読み取りアクセスを許可します。 詳細については、[ID レジストリ](../articles/iot-hub/iot-hub-devguide-identity-registry.md)に関するページを参照してください。

* **RegistryReadWrite**。 ID レジストリへの読み取りと書き込みアクセスを許可します。 詳細については、[ID レジストリ](../articles/iot-hub/iot-hub-devguide-identity-registry.md)に関するページを参照してください。

* **ServiceConnect**。 クラウド サービス向けの通信エンドポイントと監視エンドポイントへのアクセスを許可します。 たとえば、D2C メッセージの受信、C2D メッセージの送信、対応する配信確認メッセージの取得のアクセス許可をバックエンド クラウド サービスに付与します。

* **DeviceConnect**。 デバイス向けのエンドポイントへのアクセスを許可します。 たとえば、D2C メッセージの送信と、C2D メッセージの受信のアクセス許可を付与します。 このアクセス許可はデバイスによって使用されます。

IoT Hub と[セキュリティ トークン](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)を使用して **DeviceConnect** アクセス許可を取得するには、デバイス ID キーを使用する方法と共有アクセス キーを使用する方法の 2 とおりがあります。 さらに、デバイスからアクセスできるすべての機能は、仕様により、 `/devices/{deviceId}`というプレフィックスを持つエンドポイントで公開されることに注意することが重要です。

[サービス コンポーネントでは、適切なアクセス許可を付与する共有アクセス ポリシーを使用した場合にのみセキュリティ トークンを生成できます](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components)。

Azure IoT Hub とソリューションの一部として使用するその他のサービスは、Azure Active Directory を使用してユーザーを管理できます。

Azure IoT Hub によって取り込まれたデータは、Azure Stream Analytics や Azure Blob Storage などのさまざまなサービスで使用できます。 これらのサービスでは、管理アクセスが可能になります。 これらのサービスと利用可能なオプションの詳細については、以下をご覧ください。

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/):属性、構成、セキュリティ プロパティなど、プロビジョニングするデバイスのメタデータを管理する半構造化データ用に完全にインデックス付けされたスケーラブル データベース サービス。 Azure Cosmos DB では、高パフォーマンスで高スループットの処理、スキーマに依存しないデータのインデックス付けと豊富な SQL クエリ インターフェイスが提供されます。

* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/):クラウドにおけるリアルタイム ストリーム処理。これにより、デバイス、センサー、インフラストラクチャ、アプリケーションからリアルタイムの分析情報を得られるようにする低コストの分析ソリューションを迅速に開発してデプロイすることができます。 この完全に管理されたサービスからのデータは、高スループット、低待機時間、および回復性を維持した状態で任意のボリュームにスケーリングできます。

* [Azure App Services](https://azure.microsoft.com/services/app-service/):データがクラウドとオンプレミスのどちらにあっても接続できる強力な Web アプリとモバイル アプリをビルドするためのクラウド プラットフォームです。 iOS、Android、Windows 用の魅力的なモバイル アプリをビルドします。 多数のクラウド ベース サービスとエンタープライズ アプリケーションへのすぐに利用可能な接続により、使用しているサービスとしてのソフトウェア (SaaS) およびエンタープライズ アプリケーションと統合します。 お気に入りの言語や IDE (.NET、Node.js、PHP、Python、または Java) でコードを作成し、Web アプリと API をこれまで以上に迅速にビルドできます。

* [Logic Apps](https://azure.microsoft.com/services/app-service/logic/):Azure App Service の Logic Apps 機能は、既存の基幹業務システムに IoT ソリューションを統合し、ワークフロー プロセスを自動化するのに役立ちます。 開発者は Logic Apps を使用することで、トリガーで開始され、一連の手順 (つまり、ビジネス プロセスに統合するために強力なコネクタを使用するルールとアクション) を実行するワークフローを設計できます。 Logic Apps では、SaaS、クラウド ベース、およびオンプレミス アプリケーションの広範なエコシステムにすぐに接続できます。

* [Azure Blob Storage](https://azure.microsoft.com/services/storage/): デバイスからクラウドに送信されるデータに対応する信頼性と経済性に優れたクラウド ストレージ。

## <a name="conclusion"></a>まとめ

この記事では、Azure IoT を使って IoT インフラストラクチャの設計や導入について実装レベルの詳細の概要を説明します。 各コンポーネントをセキュリティで保護して構成することが、IoT インフラストラクチャ全体をセキュリティ保護するポイントです。 Azure IoT で利用可能な設計についての選択肢は、ある程度の柔軟性と選択の幅を持たせますが、セキュリティに影響が及ぶことがあります。 これらの選択肢がリスクやコストから評価されることをお勧めします。
