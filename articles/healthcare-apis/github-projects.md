---
title: Azure Healthcare APIs に関連する GitHub プロジェクト
description: すべてのオープン ソース (GitHub) リポジトリを一覧表示する
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: reference
ms.date: 10/18/2021
ms.author: ginle
ms.openlocfilehash: 63e3ff442ab45801d079e4f61b8dae98a9b010f4
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166365"
---
# <a name="github-projects"></a>GitHub プロジェクト

さまざまな用途向けにサービスをデプロイするためのソース コードと説明を提供するオープンソース プロジェクトが GitHub に多数用意されています。 いつでも GitHub リポジトリにアクセスし、機能と製品を学び、試すことができます。 

## <a name="healthcare-apis-samples"></a>医療 API のサンプル

* このレポジター[](https://github.com/microsoft/healthcare-apis-samples)には、高速ヘルスケア相互運用性リソース (FHIR&#174;)、DICOM、IoT コネクタ、データ関連サービスなど、医療 API のサンプルが含まれています。

## <a name="fhir-server"></a>FHIR サーバー

* [microsoft/fhir-server](https://github.com/microsoft/fhir-server/): オープンソースの FHIR サーバーで、FHIR サービスの基礎です
* 最新のリリースについては、「リリース ノート」 [を参照してください。](https://github.com/microsoft/fhir-server/releases)
* [microsoft/fhir-server-samples](https://github.com/microsoft/fhir-server-samples): サンプル環境

## <a name="data-conversion--anonymization"></a>データ変換と匿名化

#### <a name="fhir-converter"></a>FHIR コンバーター

* [microsoft/FHIR-Converter](https://github.com/microsoft/FHIR-Converter): レガシ データ形式を FHIR に変換する変換ユーティリティです
* $convert-data 操作形式で、FHIR サービスおよび Azure 用の FHIR サーバーと統合されています
* OSS の継続的な改善と、FHIR サーバーへの継続的な統合
 
#### <a name="fhir-converter---vs-code-extension"></a>FHIR コンバーター - VS Code 拡張機能

* [microsoft/FHIR-Tools-for-Anonymization](https://github.com/microsoft/FHIR-Tools-for-Anonymization): データ (FHIR 形式) の匿名化を支援するための一連のツール
* ‘匿名化エクスポート’ 形式で、FHIR サービスおよび Azure 用の FHIR サーバーと統合されています

#### <a name="fhir-tools-for-anonymization"></a>匿名化用の FHIR ツール

* [microsoft/vscode-azurehealthcareapis-tools](https://github.com/microsoft/vscode-azurehealthcareapis-tools): Azure Healthcare APIs を使用するためのツールのコレクションを含む VS Code 拡張機能です
* Visual Studio Marketplace にリリースされています
* FHIR コンバーターで使用される Liquid テンプレートの作成に使用されます

## <a name="iot-connector"></a>IoT コネクタ

#### <a name="integration-with-iot-hub-and-iot-central"></a>IoT Hub および IoT Central との統合

* [microsoft/iomt-fhir](https://github.com/microsoft/iomt-fhir): データ正規化を使用した IoT Hub または IoT Central から FHIR への統合および正規化されたデータの FHIR 変換です
* 正規化: デバイス データ情報は、追加処理のために一般的な形式に抽出されます
* FHIR 変換: 正規化され、グループ化されたデータは FHIR にマップされます。 構成されたテンプレートに従って観察が作成または更新され、デバイスと患者にリンクされます。
* [会話マップの構築に役立つツール](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper): デバイス入力データを正規化するためのマッピング構成を視覚化し、FHIR リソースに変換します。 開発者は、このツールを使用して、Device と FHIR の変換先マッピングを編集してテストし、Azure portal の IoT コネクタにアップロードするためにエクスポートできます。

#### <a name="healthkit-and-fhir-integration"></a>HealthKit と FHIR の統合

* [microsoft/healthkit-on-fhir](https://github.com/microsoft/healthkit-on-fhir): Apple HealthKit データの FHIR サーバーへのエクスポートを自動化する Swift ライブラリです

>[!div class="nextstepaction"]
>[Azure Healthcare API の概要](healthcare-apis-overview.md)

(FHIR&#174;) は HL7 の商標であり [、HL7](https://hl7.org/fhir/) の許可を得て使用されます。 