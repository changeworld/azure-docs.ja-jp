---
title: Microsoft Azure IoT Hub Device Provisioning Service の概要
description: Device Provisioning Service (DPS) と IoT Hub を使用した Azure へのデバイス プロビジョニングについて説明します
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/09/2021
ms.topic: overview
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 8c469fbcf4ee8300586a0f3dd354ad66cf7cf685
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490631"
---
# <a name="what-is-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning Service とは

Microsoft Azure には、すべての IoT ソリューションのニーズに合う豊富な統合されたパブリック クラウド サービス セットがあります。 IoT Hub Device Provisioning Service (DPS) は、IoT Hub のヘルパー サービスです。適切な IoT Hub へのゼロタッチの Just-In-Time プロビジョニングを人間の介入を必要とせずに行うことができます。 DPS を使用すると、膨大な数のデバイスを、安全かつスケーラブルな方法でプロビジョニングすることができます。

## <a name="when-to-use-device-provisioning-service"></a>Device Provisioning Service を使用する場合

デバイスを IoT Hub に接続し、構成する際に DPS が最適な選択肢になるプロビジョニング シナリオは多数あります。次に例を示します。

* 工場 (初期設定) で IoT Hub 接続情報をハードコーディングすることなく、ゼロタッチで単一の IoT ソリューションにプロビジョニングできます
* 複数のハブ間でデバイスの負荷を分散します
* 販売トランザクション データに基づいて、デバイスをデバイス所有者の IoT ソリューションに接続します (マルチテナント)
* ユースケースに応じて特定の IoT ソリューションにデバイスを接続します (ソリューションの分離)
* 最低限の待機時間でデバイスを IoT ハブに接続します (geo シャーディング)
* デバイスの変化に基づいて再プロビジョニングします
* デバイスが IoT Hub に接続するときに使用するキーをローリングします (接続に X.509 証明書を使用しない場合)

>[!NOTE]
>**データ所在地に関する考慮事項:**
>
>DPS は、すべてのプロビジョニング サービス インスタンスに対して同じ[デバイス プロビジョニング エンドポイント](concepts-service.md#device-provisioning-endpoint)を使用し、使用可能な最も近いサービス エンドポイントへのトラフィック負荷分散を実行します。 このため、認証シークレットは、DPS インスタンスが最初に作成されたリージョンの外部に一時的に転送される可能性があります。 ただし、デバイスが接続されると、デバイス データは DPS インスタンスの元のリージョンに直接送られます。
>
>DPS インスタンスが作成されたリージョンからデータが離れないようにするには、プライベート エンドポイントを使用します。  プライベート エンドポイントを設定する方法については、[仮想ネットワーク向けの Azure IoT Device Provisioning Service (DPS) サポート](virtual-network-support.md#private-endpoint-limitations)を参照してください。


## <a name="behind-the-scenes"></a>バックグラウンド処理

前のセクションに記載したすべてのシナリオは、ゼロタッチ プロビジョニングの DPS を使用して、同じフローで実行できます。 プロビジョニングに従来必要だった手動の手順の多くは DPS で自動化されるので、IoT デバイスのデプロイにかかる時間を短縮し、手動によるエラーのリスクを軽減できます。 次のセクションでは、デバイスをプロビジョニングする処理の背後でどのような処理が行われているかについて説明します。 最初の手順は手動ですが、残りのすべての手順は自動です。

![プロビジョニングの基本的な流れ](./media/about-iot-dps/dps-provisioning-flow.png)

1. デバイスの製造元は、デバイスの登録情報を Azure Portal の登録一覧に追加しています。
2. デバイスは、工場で DPS エンドポイント セットに接続しています。 デバイスは、DPS に識別情報を渡し、ID を証明しています。
3. DPS は、nonce チャレンジ ([トラステッド プラットフォーム モジュール](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/)) または標準の X.509 検証 (X.509) を使用して、登録一覧のエントリに対して登録 ID とキーを確認することで、デバイスの ID を検証します。
4. DPS はデバイスを IoT ハブに登録し、デバイスの[望ましいツイン状態](../iot-hub/iot-hub-devguide-device-twins.md)を設定します。
5. IoT ハブは、デバイスの ID 情報を DPS に返します。
6. DPS は IoT ハブの接続情報をデバイスに返します。 これで、デバイスは IoT ハブにデータを直接送信できるようになります。
7. デバイスは IoT ハブに接続します。
8. デバイスは、IoT ハブのデバイス ツインから望ましい状態を取得します。

## <a name="provisioning-process"></a>プロビジョニング プロセス

DPS が関係するデバイスのデプロイ プロセスには 2 つの手順があり、個別に実行できます。

* **製造手順** では、工場でデバイスが作成され、準備されます。
* **クラウドのセットアップ手順** では、Device Provisioning Service が自動プロビジョニング用に構成されます。

これらの手順はどちらも、既存の製造プロセスとデプロイ プロセスにシームレスに適応します。 デバイスで接続情報を取得するために手動作業が必要な一部のデプロイ プロセスが、DPS によってさらに簡易化されます。

### <a name="manufacturing-step"></a>製造手順

この手順は、製造ラインで実行されるすべての作業です。 この手順にかかわる役割には、シリコン デザイナー、シリコンの製造元、インテグレーター、デバイスの最終製造元などがあります。 この手順は、ハードウェア自体の作成に関係しています。

DPS によって製造プロセスに新しい手順が加わることはありませんが、初期ソフトウェアと (理想的には) HSM をデバイスにインストールする既存の手順に関係しています。 この手順ではデバイス ID は作成されず、プロビジョニング サービス情報がデバイスにプログラミングされます。これにより、デバイスの電源を入れたときに、プロビジョニング サービスを呼び出して接続情報や IoT ソリューションの割り当てを取得することができます。

また、この手順では、製造元がデバイスのデプロイ担当者やオペレーターに識別キー情報を提供します。 この情報提供は、デバイスのデプロイ担当者やオペレーターによって指定された署名証明書から生成された X.509 証明書がすべてのデバイスにインストールされていることを確認するだけの単純な処理であることもあれば、各 TPM デバイスから TPM 保証キーの公開部分を抽出するほどの複雑な処理であることもあります。 現在、多くのシリコン製造元がこれらのサービスを提供しています。

### <a name="cloud-setup-step"></a>クラウドのセットアップ手順

これは、適切な自動プロビジョニングのためにクラウドを構成する手順です。 通常、クラウドのセットアップ手順には 2 種類のユーザーが関係します。初期段階でデバイスをどのようにセットアップする必要があるかを把握しているユーザー (デバイス オペレーター) と、デバイスを IoT ハブ間で分割する方法を把握しているユーザー (ソリューション オペレーター) です。

実行する必要があるプロビジョニングの 1 回限りの初期セットアップがあります。通常、これはソリューション オペレーターが処理します。 プロビジョニング サービスを構成した後は、ユース ケースが変わらない限り、構成を変更する必要はありません。

自動プロビジョニング用にサービスを構成した後は、デバイスの登録を準備する必要があります。 この手順は、デバイス オペレーターが実行します。デバイス オペレーターは、デバイスの望ましい構成を把握し、IoT ハブを検索するときにプロビジョニング サービスがデバイスの ID を適切に証明できるようにする処理を担当します。 デバイス オペレーターは、製造元から識別キー情報を取得し、登録一覧に追加します。 以降、新しいエントリが追加されたときや、デバイスに関する最新情報があり、既存のエントリが更新されたときに、登録が更新される可能性があります。

## <a name="registration-and-provisioning"></a>登録とプロビジョニング

*プロビジョニング* とは、この用語が使用される業界によって意味する処理が異なります。 IoT デバイスをクラウド ソリューションにプロビジョニングするという文脈では、プロビジョニングには 2 つのプロセスがあります。

1. 最初のプロセスでは、デバイスを登録することで、デバイスと IoT ソリューション間の初期接続を確立します。
2. 2 つ目のプロセスでは、登録先のソリューションが持つ特定の要件に基づいて、デバイスに適切な構成を適用します。

この 2 つのステップを両方とも完了すると、デバイスが完全にプロビジョニングされたことになります。 一部のクラウド サービスは、プロビジョニング プロセスの最初の手順 (デバイスを IoT ソリューション エンドポイントに登録する) のみを提供していますが、初期構成は提供していません。 DPS では、デバイスにシームレスなプロビジョニング エクスペリエンスを提供するために、両方のステップを自動化しています。

## <a name="features-of-the-device-provisioning-service"></a>Device Provisioning Service の機能

DPS は多くの機能を備えているため、デバイスのプロビジョニングに最適です。

* **安全な構成証明**。X.509 と TPM ベースの ID の両方をサポートしています。
* **登録一覧**。任意の時点で登録できるデバイスまたはデバイス グループの完全なレコードが含まれます。 登録一覧には、デバイスが登録されたときのデバイスの望ましい構成に関する情報が含まれています。いつでも更新することができます。
* **複数の割り当てポリシー**。実際のシナリオに合わせて DPS がデバイスを IoT ハブに割り当てる方法を制御できます。最短待ち時間、均等に重み付けされた分布 (既定)、および登録リストによる静的な構成。 待ち時間は [Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md#performance) と同じ方法を使用して決定されます。
* **監視および診断ログ**。すべての機能が正常に動作していることを確認できます。
* **マルチハブのサポート**。DPS でデバイスを複数の IoT ハブに割り当てることができます。 DPS は、複数の Azure サブスクリプションにわたってハブと対話できます。
* **リージョン間のサポート**。DPS でデバイスを他のリージョンの IoT ハブに割り当てることができます。
* **保存データの暗号化**。DPS 内のデータは、利用可能な最強のブロック暗号の 1 つである 256 ビット AES 暗号化を使って透過的に暗号化および暗号化解除され、FIPS 140-2 に準拠しています。

デバイス プロビジョニングに関連する概念と機能の詳細については、[DPS の用語](concepts-service.md)に関するトピック、および同じセクション内の他の概念に関するトピックをご確認ください。

## <a name="cross-platform-support"></a>クロスプラットフォームのサポート

DPS は、他のあらゆる Azure IoT サービスとまったく同じように、多様なオペレーティング システムとクロスプラットフォームで動作します。 Azure ではさまざまな[言語](https://github.com/Azure/azure-iot-sdks)でオープン ソース SDK が提供されており、容易にデバイスを接続して、サービスを管理できます。 DPS は、次のプロトコルを使ったデバイスの接続をサポートしています。

* HTTPS
* AMQP
* Web ソケット経由の AMQP
* MQTT
* WebSocket 経由の MQTT

DPS は、サービス操作の HTTPS 接続のみをサポートしています。

## <a name="regions"></a>リージョン

DPS は多数のリージョンで利用できます。 すべてのサービスの既存リージョンと新規発表リージョンの最新の一覧は、「[Azure リージョン](https://azure.microsoft.com/regions/)」にあります。 Device Provisioning Service の状態は、[[Azure の状態]](https://azure.microsoft.com/status/) ページで確認できます。

> [!NOTE]
> DPS は、1 つの場所に固定されず、グローバルです。 ただし、DPS プロファイルに関連付けられたメタデータが存在するリージョンを指定する必要があります。

## <a name="availability"></a>可用性

DPS では、99.9% のサービス レベル アグリーメントが維持されています。[SLA を参照](https://azure.microsoft.com/support/legal/sla/iot-hub/)してください。 完全な [Azure SLA](https://azure.microsoft.com/support/legal/sla/) では、全体としての Azure の可用性の確保について説明します。

## <a name="quotas-and-limits"></a>クォータと制限

各 Azure サブスクリプションには既定のクォータ制限が設けられており、IoT ソリューションの範囲に影響する可能性があります。 サブスクリプションごとの現在の上限は、サブスクリプションあたり 10 個の Device Provisioning Service です。

クォータ制限に関連する制限については、「[Azure サブスクリプション サービスの制限](../azure-resource-manager/management/azure-subscription-service-limits.md)」を参照してください。

[!INCLUDE [azure-iotdps-limits](../../includes/iot-dps-limits.md)]

## <a name="billable-service-operations-and-pricing"></a>課金されるサービス操作と価格

DPS での各 API 呼び出しは、1 回の "*操作*" として課金されます。 これには、すべてのサービス API とデバイス登録 API が含まれます。

次の表は、各 DPS サービス API 操作の現在の課金対象の状態を示しています。 価格に詳細については、「IoT Hub Device Provisioning Service」セクションの「[Azure Hub の価格](https://azure.microsoft.com/pricing/details/iot-hub/)」を参照してくだい。

| API | 操作 | 課金対象? |
| --------------- | -------  | -- |
|  デバイス API | [デバイスの登録状態の検索](/api/iot-dps/device/runtime-registration/device-registration-status-lookup) | No|
|  デバイス API | [操作の状態の検索](/api/iot-dps/device/runtime-registration/operation-status-lookup)| No |
|  デバイス API | [Register Device](/api/iot-dps/device/runtime-registration/register-device) | はい |
| DPS サービス API (登録の状態)  | [削除](/api/iot-dps/service/device-registration-state/delete) | はい|
| DPS サービス API (登録の状態)  | [Get](/api/iot-dps/service/device-registration-state/get) | はい|
| DPS サービス API (登録の状態)  | [クエリ](/api/iot-dps/service/device-registration-state/query) | はい|
| DPS サービス API (登録グループ) | [Create or Update](/api/iot-dps/service/enrollment-group/create-or-update) | はい|
| DPS サービス API (登録グループ) | [削除](/api/iot-dps/service/enrollment-group/delete) | はい|
| DPS サービス API (登録グループ) | [Get](/api/iot-dps/service/enrollment-group/get) | はい|
| DPS サービス API (登録グループ) | [構成証明メカニズムの取得](/api/iot-dps/service/enrollment-group/get-attestation-mechanism)| はい|
| DPS サービス API (登録グループ) | [クエリ](/api/iot-dps/service/enrollment-group/query) | はい|
| DPS サービス API (登録グループ) | [一括操作の実行](/api/iot-dps/service/enrollment-group/run-bulk-operation) | はい|
| DPS サービス API (個別の登録) | [Create or Update](/api/iot-dps/service/individual-enrollment/create-or-update)  | はい|
| DPS サービス API (個別の登録)| [削除](/api/iot-dps/service/individual-enrollment/delete) | はい|
| DPS サービス API (個別の登録)| [Get](/api/iot-dps/service/individual-enrollment/get) | はい|
| DPS サービス API (個別の登録)| [構成証明メカニズムの取得](/api/iot-dps/service/individual-enrollment/get-attestation-mechanism) | はい|
| DPS サービス API (個別の登録)| [クエリ](/api/iot-dps/service/individual-enrollment/query)  | はい|
| DPS サービス API (個別の登録)| [一括操作の実行](/api/iot-dps/service/individual-enrollment/run-bulk-operation)  | はい|
|  DPS 証明書 API|  [Create or Update](/api/iot-dps/dps-certificate/create-or-update) | No |
|  DPS 証明書 API| [削除](/api/iot-dps/dps-certificate/delete) | No |
|  DPS 証明書 API| [確認コードの生成](/api/iot-dps/dps-certificate/generate-verification-code)|No  |
|  DPS 証明書 API| [Get](/api/iot-dps/dps-certificate/get) | No |
|  DPS 証明書 API| [リスト](/api/iot-dps/dps-certificate/list) |No  |
|  DPS 証明書 API| [証明書の確認](/api/iot-dps/dps-certificate/verify-certificate) | No |
|  IoT DPS リソース API| [プロビジョニング サービス名の可用性の確認](/api/iot-dps/iot-dps-resource/check-provisioning-service-name-availability)  | No |
|  IoT DPS リソース API| [Create or Update](/api/iot-dps/iot-dps-resource/create-or-update)  | No |
|  IoT DPS リソース API| [削除](/api/iot-dps/iot-dps-resource/delete) |  No|
|  IoT DPS リソース API| [Get](/api/iot-dps/iot-dps-resource/get) | No |
|  IoT DPS リソース API| [操作の結果を取得します。](/api/iot-dps/iot-dps-resource/get-operation-result)| No |
|  IoT DPS リソース API| [List By Resource Group](/api/iot-dps/iot-dps-resource/list-by-resource-group) |No  |
|  IoT DPS リソース API| [List By Subscription](/api/iot-dps/iot-dps-resource/list-by-subscription) |No  |
|  IoT DPS リソース API| [キー別の一覧表示](/api/iot-dps/iot-dps-resource/list-keys) |No  |
|  IoT DPS リソース API| [キー名のキーの一覧表示](/api/iot-dps/iot-dps-resource/list-keys-for-key-name) |No  |
|  IoT DPS リソース API| [有効な SKU の一覧表示](/api/iot-dps/iot-dps-resource/list-valid-skus) |No  |
|  IoT DPS リソース API| [アップデート](/api/iot-dps/iot-dps-resource/update) |  No|

## <a name="related-azure-components"></a>関連する Azure のコンポーネント

DPS は Azure IoT Hub を使用してデバイス プロビジョニングを自動化しています。 詳細については、「[IoT Hub のドキュメント](../iot-hub/index.yml)」を参照してください。

## <a name="next-steps"></a>次のステップ

ここでは、Azure で IoT デバイスをプロビジョニングする方法の概要について説明しました。 次の手順では、エンドツーエンドの IoT シナリオを試します。

[Azure portal で IoT Hub Device Provisioning Service を設定する](quick-setup-auto-provision.md)

[シミュレートされたデバイスの作成とプロビジョニング](quick-create-simulated-device-tpm.md)

[プロビジョニングするデバイスを設定する](tutorial-set-up-device.md)