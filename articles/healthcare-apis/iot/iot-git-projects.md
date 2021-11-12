---
title: IoT コネクタの関連 GitHub プロジェクト-Azure の医療 api
description: IoT コネクタのすべてのオープンソース (GitHub) リポジトリの一覧表示
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.topic: reference
ms.date: 10/28/2021
ms.author: jasteppe
ms.openlocfilehash: 76bf145ea76ea44d65e2c5dc3873cb51707840a5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309849"
---
# <a name="open-source-projects"></a>オープンソース プロジェクト

ソースコードを提供し、IoT コネクタを使用したさまざまな用途のためにサービスをデプロイするための手順を示す GitHub で、オープンソースプロジェクトを確認してください。 

## <a name="iot-connector-github-projects"></a>IoT コネクタ GitHub プロジェクト

#### <a name="fhir-integration"></a>FHIR の統合

* [microsoft/iomt-fhir](https://github.com/microsoft/iomt-fhir): Azure の医療 api IoT コネクタで管理されているサービスのオープンソースバージョン。 [Fhir R7](https://www.hl7.org/implement/standards/product_brief.cfm?product_id=491)をサポートする高速の医療相互運用性リソース (fhir&#174;) サービスで使用でき&#174;

#### <a name="device-and-fhir-destination-mappings"></a>デバイスおよび FHIR の宛先マッピング

* [IoMT Connector Data マッパー](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper): IoT コネクタデバイスと FHIR の宛先マッピングの編集、テスト、トラブルシューティングを行うためのツールです。 Azure portal の IoT コネクタにアップロードするためのマッピングをエクスポートするか、オープンソースのバージョンと共に使用します。

#### <a name="wearables-integration"></a>ウェアラブルの統合

Fitbit

* [microsoft/fitbit-fhir](https://github.com/microsoft/FitbitOnFHIR): fitbit&#174; データを fhir サービスに取り込みます。

HealthKit

* [microsoft/healthkit](https://github.com/microsoft/healthkit-on-fhir): Apple&#174; healthkit&#174; データを fhir サービスに移動します。

* [microsoft/healthkit](https://github.com/microsoft/healthkit-to-fhir): hkobjects から Fhir リソースを簡単に作成する方法を提供します。

FHIR の Google Fit

* [microsoft/googlefit](https://github.com/microsoft/googlefit-on-fhir): fhir サービスに Google Fit&#174; データを取り込みます。

正常性データ同期

* [microsoft/health-data sync](https://github.com/microsoft/health-data-sync): HealthKit データの外部ストアへのエクスポートを簡略化および自動化する Swift&#174; ライブラリです。

## <a name="next-steps"></a>次のステップ
Azure portal に IoT コネクタをデプロイする方法について説明します

>[!div class="nextstepaction"]
>[IoT コネクタの管理されたサービスのデプロイ](deploy-iot-connector-in-azure.md)

(FHIR&#174;) [HL7](https://hl7.org/fhir/) の登録商標であり、HL7 のアクセス許可と共に使用されます。
