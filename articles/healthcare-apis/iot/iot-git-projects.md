---
title: IoT GitHub関連するプロジェクト - Azure Healthcare API
description: IoT コネクタのすべてのオープン ソース (GitHub) リポジトリを一覧表示する
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.topic: reference
ms.date: 10/28/2021
ms.author: jasteppe
ms.openlocfilehash: f82e1f2bacd0ae863b4ddac19a16a45e52ff173a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131445953"
---
# <a name="open-source-projects"></a>オープンソース プロジェクト

IoT コネクタを使用してさまざまな用途にサービスをデプロイするソース コードと手順を提供する、GitHub 上のオープンソース プロジェクトを確認してください。 

## <a name="iot-connector-github-projects"></a>IoT コネクタとGitHubプロジェクト

#### <a name="fhir-integration"></a>FHIR 統合

* [microsoft/iomt-fhir:](https://github.com/microsoft/iomt-fhir)Azure Healthcare API IoT コネクタマネージド サービスのオープンソース バージョン。 [FHIR R7](https://https://www.hl7.org/implement/standards/product_brief.cfm?product_id=491)をサポート高速ヘルスケア相互運用性リソース (FHIR&#174;) サービスで使用&#174;

#### <a name="device-and-fhir-destination-mappings"></a>デバイスと FHIR の宛先マッピング

* [IoMT コネクタ データ マッパー](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper): IoT コネクタデバイスと FHIR 変換先マッピングの編集、テスト、トラブルシューティングを行うツールです。 アプリケーションで IoT コネクタにアップロードしたり、Azure portalバージョンで使用したりするためにマッピングをエクスポートします。

#### <a name="wearables-integration"></a>しつらえの統合

Fitbit

* [microsoft/fitbit-on-fhir:](https://github.com/microsoft/FitbitOnFHIR)Fitbit のデータ&#174; FHIR サービスに取り込む。

HealthKit

* [microsoft/healthkit-on-fhir:](https://github.com/microsoft/healthkit-on-fhir)Apple&#174; HealthKit&#174; FHIR サービスにデータを取り込む。

* [microsoft/healthkit-to-fhir:](https://github.com/microsoft/healthkit-to-fhir)HKObjects から FHIR リソースを作成する簡単な方法を提供します

Google Fit on FHIR

* [microsoft/googlefit-on-fhir:](https://github.com/microsoft/googlefit-on-fhir)FHIR サービスGoogle Fit&#174;データを取り込む。

正常性データ同期

* [microsoft/health-data-sync:](https://github.com/microsoft/health-data-sync)HealthKit データの外部ストアへのエクスポートを簡略化および自動化する Swift ライブラリ。

## <a name="next-steps"></a>次のステップ
デバイスに IoT コネクタをデプロイする方法についてAzure portal

>[!div class="nextstepaction"]
>[IoT コネクタマネージド サービスをデプロイする](deploy-iot-connector-in-azure.md)

(FHIR&#174;) は HL7 の商標であり [、HL7](https://hl7.org/fhir/) の許可を得て使用されます。