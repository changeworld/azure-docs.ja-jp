---
title: IoT GitHub関連するプロジェクト - Azure Healthcare API
description: IoT コネクタのすべてのオープン ソース (GitHub) リポジトリを一覧表示する
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.topic: reference
ms.date: 10/18/2021
ms.author: jasteppe
ms.openlocfilehash: 0092482af2f9f4f6f9a090a00cc387ae901e5b68
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130187779"
---
# <a name="open-source-projects"></a>オープンソース プロジェクト

IoT コネクタを使用してさまざまな用途にサービスをデプロイするソース コードと手順を提供する GitHub のオープンソース プロジェクトを確認してください。 

## <a name="iot-connector-github-projects"></a>IoT コネクタとGitHubプロジェクト

#### <a name="integration-with-iot-hub-and-iot-central"></a>IoT Hub および IoT Central との統合

* [microsoft/iomt-fhir:](https://github.com/microsoft/iomt-fhir)IoT Hub または IoT Central から 高速ヘルスケア相互運用性リソース (FHIR&#174;) への統合と、正規化されたデータのデータ正規化と FHIR 変換
* 正規化: デバイス データ情報は、追加処理のために一般的な形式に抽出されます
* FHIR 変換: 正規化され、グループ化されたデータは FHIR にマップされます。 構成されたテンプレートに従って観察が作成または更新され、デバイスと患者にリンクされます。

#### <a name="device-and-fhir-destination-mappings-authoring-and-troubleshooting"></a>デバイスマッピングと FHIR 変換先マッピングの作成とトラブルシューティング

* [デバイスと FHIR の変換先マッピング](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)の構築に役立つツール: デバイス入力データを正規化するためのマッピング構成を視覚化し、FHIR リソースに変換します。 開発者は、このツールを使用して、デバイス マッピングと FHIR 変換先マッピングを編集およびテストできます。 デバイスの IoT コネクタにアップロードするためにエクスポートAzure portal。

#### <a name="healthkit-and-fhir-integration"></a>HealthKit と FHIR の統合

* [microsoft/healthkit-on-fhir:](https://github.com/microsoft/healthkit-on-fhir)Apple HealthKit データの FHIR サービスへのエクスポートを自動化する Swift ライブラリ。

## <a name="next-steps"></a>次の手順
デバイスに IoT コネクタをデプロイする方法についてAzure portal

>[!div class="nextstepaction"]
>[IoT コネクタマネージド サービスをデプロイする](deploy-iot-connector-in-azure.md)

(FHIR&#174;) は HL7 の商標であり [、HL7](https://hl7.org/fhir/) の許可を得て使用されます。