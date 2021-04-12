---
title: Azure Percept セキュリティの概要
description: Azure Percept セキュリティの詳細
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: template-concept
ms.openlocfilehash: 93884fb87f87651054ffff0a04c4910de634a5eb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567646"
---
# <a name="azure-percept-security-overview"></a>Azure Percept セキュリティの概要

Azure Percept デバイスはハードウェア Root of Trust (信頼の基点) に基づいて設計されています。 この組み込みセキュリティは、推論データと機密性の高いセンサー (カメラやマイクなど) との保護に役立つほか、Azure Percept Studio サービスでのデバイスの認証と認可を可能にします。

> [!NOTE]
> Azure Percept DK は、開発およびテスト環境での使用向けにライセンスされています。

## <a name="devices"></a>デバイス

### <a name="azure-percept-dk"></a>Azure Percept DK

Azure Percept DK にはトラステッド プラットフォーム モジュール (TPM) バージョン 2.0 が搭載されています。これを使用して、セキュリティが強化された Azure デバイス プロビジョニング サービス (DPS) にデバイスを接続できます。 TPM は、業界で広く採用されている Trusted Computing Group の ISO 標準です。 TPM 2.0 の完全な詳細または ISO/IEC 11889 仕様については、[Trusted Computing Group の Web サイト](https://trustedcomputinggroup.org/resource/tpm-library-specification/)を参照してください。DPS でデバイスを安全にプロビジョニングする方法の詳細については、[Azure IoT Hub Device Provisioning Service - TPM の構成証明](../iot-dps/concepts-tpm-attestation.md)に関するページを参照してください。

### <a name="azure-percept-system-on-modules-soms"></a>Azure Percept システム オン モジュール (SoM)

Azure Percept Vision システム オン モジュール (SoM) と Azure Percept Audio SoM のどちらにも、埋め込み AI センサーへのアクセスを保護するためのマイクロコントローラー ユニット (MCU) が搭載されています。 ブートのたびに、MCU ファームウェアによって Azure Percept Studio サービスに対する AI アクセレータの認証と承認が行われます。その際、デバイス識別子コンポジション エンジン (DICE) アーキテクチャが使用されます。 DICE は、ブートをレイヤーに分割し、レイヤーおよび構成ごとに一意のデバイス シークレット (UDS) を作成することによって動作します。 チェーン内のいずれかの時点で異なるコードまたは構成がブートされると、シークレットが異なります。 DICE の詳細については、[DICE ワークグループの仕様](https://trustedcomputinggroup.org/work-groups/dice-architectures/)を参照してください。Azure Percept Studio と必須サービスへのアクセスを構成するには、[Azure Percept DK のファイアウォールの構成](concept-security-configuration.md)に関する記事を参照してください。

Azure Percept デバイスでは、ハードウェア Root of Trust (信頼の基点) を使用してファームウェアが保護されます。 ブート ROM によって ROM とオペレーティング システム (OS) 読み込みプログラムの間でファームウェアの整合性が確保されます。それにより、その他のソフトウェア コンポーネントの整合性が確保され、信頼チェーンが実現します。

## <a name="services"></a>サービス

### <a name="iot-edge"></a>IoT Edge

Azure Percept DK では、トランスポート層セキュリティ (TLS) プロトコルを使用して、セキュリティが強化された Azure Percept Studio  と、その他の Azure サービスに接続します。 Azure Percept DK は Azure IoT Edge に対応したデバイスです。 IoT Edge ランタイムは、デバイスを IoT Edge デバイスに変えるプログラムのコレクションです。 これらの IoT Edge ランタイム コンポーネントを使用することにより、IoT Edge デバイスは、エッジで実行するコードを受信し、結果を通信できます。 Azure Percept DK では、Docker コンテナーを利用してホスト オペレーティング システムとエッジ対応アプリケーションから IoT Edge ワークロードを分離します。 Azure IoT Edge セキュリティ フレームワークについて詳しくは、[IoT Edge セキュリティ マネージャー](../iot-edge/iot-edge-security-manager.md)に関する記事をご覧ください。

### <a name="device-update-for-iot-hub"></a>Device Update for IoT Hub

Device Update for IoT Hub を使用すると、Azure Percept デバイスでの再生可能なセキュリティを実現する無線更新をさらに安全、スケーラブル、確実に行うことができます。 分析情報を通じて豊富な管理コントロールを実施し、更新プログラムのコンプライアンスを確保できます。 Azure Percept DK にはデバイス更新ソリューションがあらかじめ統合されていて、回復性がある方法でファームウェアから OS のレイヤーに更新 (A/B) できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ファイアウォール構成とセキュリティに関する推奨事項についての詳細情報を確認する](concept-security-configuration.md)

> [!div class="nextstepaction"]
> [Microsoft のオンライン ストアで Azure Percept DK を購入する](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
