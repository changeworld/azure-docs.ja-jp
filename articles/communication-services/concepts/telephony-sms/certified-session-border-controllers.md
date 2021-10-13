---
title: Azure ダイレクト ルーティング認定のセッション ボーダー コントローラー - Azure Communication Services
description: Azure Communication Services ダイレクト ルーティングの認定を受けたセッション ボーダー コントローラーの一覧と既知の制限事項
author: boris-bazilevskiy
manager: nmurav
services: azure-communication-services
ms.author: bobazile
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: pstn
ms.openlocfilehash: 2270e29cc9b279348467ca2a6f6593269e37eafb
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659813"
---
# <a name="list-of-session-border-controllers-certified-for-azure-communication-services-direct-routing"></a>Azure Communication Services ダイレクト ルーティングの認定を受けたセッション ボーダー コントローラーの一覧
このドキュメントには、Azure Communication Services ダイレクト ルーティングの認定を受けたセッション ボーダー コントローラーの一覧が含まれています。 また、既知の制限事項も含まれています。

Microsoft は、Teams ダイレクト ルーティングの認定を受けた一部のセッション ボーダー コントローラー (SBC) ベンダーと協力して、Azure ダイレクト ルーティングとの連携を進めています。 その進行状況をこのページで確認できます。 Teams ダイレクト ルーティングの認定を受けた SBC は Azure ダイレクト ルーティングと連携できますが、このページに掲載されるまで、その SBC にワークロードを配置しないことをお勧めします。 また、認定されていない SBC もサポートしていません。 Azure ダイレクト ルーティングは Teams ダイレクト ルーティングと同じバックエンド上に構築されていますが、いくつかの違いがあります。 この認定は、Azure ダイレクト ルーティングの SBC の包括的な検証を対象としています。

Microsoft は、各ベンダーと協力して次のことを行っています。
- SIP 相互接続プロトコルについて共同作業します。
- サードパーティーのラボを使用して徹底的なテストを実行します。 テストに合格したデバイスのみが認定されます。
- すべての認定デバイスを運用環境と運用前環境で毎日テストします。 運用前環境でデバイスを検証することで、クラウドの Azure Communication Services コードの新バージョンが認定 SBC と連携することが保証されます。
- SBC ベンダーとの共同サポート プロセスを確立します。

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

メディアのバイパスは、Azure Communication Services ではまだサポートされていません。 初期メディアは、Web ベースのクライアントではサポートされていません。
次の表は、Azure Communication Services ダイレクト ルーティングの認定を受けたデバイスの一覧です。

Communication Services ダイレクト ルーティングの SBC 認定プログラムについてご不明な点がある場合は、acsdrcertification@microsoft.com にお問い合わせください。

## <a name="certified-sbc-vendors"></a>認定 SBC ベンダー

|ベンダー|Product|ソフトウェア バージョン|
|:--- |:--- |:--- 
|[AudioCodes](https://www.audiocodes.com/media/lbjfezwn/mediant-sbc-with-microsoft-azure-communication-services.pdf)|Mediant SBC|7.40A
|[Metaswitch](https://manuals.metaswitch.com/Perimeta/V4.9/AzureCommunicationServicesIntegrationGuide/Source/notices.html)|Perimeta SBC|4.9|
|[Oracle](https://www.oracle.com/technical-resources/documentation/acme-packet.html)|Oracle Acme Packet SBC|8.4|
|リボン コミュニケーションズ|[SBC SWe / SBC 5400 / SBC 7000](https://support.sonus.net/display/ALLDOC/Ribbon+Configurations+with+Azure+Communication+Services+Direct+Routing)|9.02|
||[SBC SWe Lite / SBC 1000 / SBC 2000](https://support.sonus.net/display/UXDOC90/Best+Practice+-+Configure+SBC+Edge+for+Azure+Communication+Services+Direct+Routing)|9.0

メジャー バージョンに付与されている認定に注意してください。 つまり、SBC のファームウェアで、メジャー バージョンに続く任意の数字を持つファームウェアはサポートされます。

## <a name="next-steps"></a>次の手順

### <a name="conceptual-documentation"></a>概念説明のドキュメント

- [Azure Communication Services での電話番号の種類](./plan-solution.md)
- [Azure 直接ルーティングを計画する](./direct-routing-infrastructure.md)
- [セッション ボーダー コントローラーをペアリングし、音声ルーティングを構成する](./direct-routing-provisioning.md)
- [料金](../pricing.md)

### <a name="quickstarts"></a>クイックスタート

- [電話の呼び出し](../../quickstarts/voice-video-calling/pstn-call.md)
