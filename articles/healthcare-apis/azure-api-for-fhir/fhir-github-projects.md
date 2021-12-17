---
title: Azure API for FHIR の関連 GitHub プロジェクト
description: Azure API for FHIR のすべてのオープン ソース (GitHub) リポジトリを一覧表示します。
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/01/2021
ms.author: ginle
ms.openlocfilehash: 9977765183bd567377592631d65f3e548bef4b34
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780857"
---
# <a name="related-github-projects"></a>関連 GitHub プロジェクト

さまざまな用途向けにサービスをデプロイするためのソース コードと説明を提供するオープンソース プロジェクトが GitHub に多数用意されています。 いつでも GitHub リポジトリにアクセスし、機能と製品を学び、試すことができます。 

## <a name="fhir-server"></a>FHIR サーバー
* [microsoft/fhir-server](https://github.com/microsoft/fhir-server/): オープンソースの FHIR サーバーで、Azure API for FHIR の基礎です
* 最新のリリースについては、[リリース ノート](https://github.com/microsoft/fhir-server/releases)を参照してください
* [microsoft/fhir-server-samples](https://github.com/microsoft/fhir-server-samples): サンプル環境

## <a name="data-conversion--anonymization"></a>データ変換と匿名化

#### <a name="fhir-converter"></a>FHIR コンバーター
* [microsoft/FHIR-Converter](https://github.com/microsoft/FHIR-Converter): レガシ データ形式を FHIR に変換する変換ユーティリティです
* $convert-data 操作形式で、Azure API for FHIR および Azure 用の FHIR サーバーと統合されています
* OSS の継続的な改善と、FHIR サーバーへの継続的な統合
 
#### <a name="fhir-converter---vs-code-extension"></a>FHIR コンバーター - VS Code 拡張機能
* [microsoft/FHIR-Tools-for-Anonymization](https://github.com/microsoft/FHIR-Tools-for-Anonymization): データ (FHIR 形式) の匿名化を支援するための一連のツール
* ‘匿名化エクスポート’ 形式で、Azure API for FHIR および Azure 用の FHIR サーバーと統合されています

#### <a name="fhir-tools-for-anonymization"></a>匿名化用の FHIR ツール
* [microsoft/vscode-azurehealthcareapis-tools](https://github.com/microsoft/vscode-azurehealthcareapis-tools): Azure Healthcare APIs を使用するためのツールのコレクションを含む VS Code 拡張機能です
* Visual Studio Marketplace にリリースされています
* FHIR コンバーターで使用される Liquid テンプレートの作成に使用されます

## <a name="iot-connector"></a>IoT コネクタ

#### <a name="integration-with-iot-hub-and-iot-central"></a>IoT Hub および IoT Central との統合
* [microsoft/iomt-fhir](https://github.com/microsoft/iomt-fhir): データ正規化を使用した IoT Hub または IoT Central から FHIR への統合および正規化されたデータの FHIR 変換です
* 正規化: デバイス データ情報は、さらに処理するために一般的な形式に抽出されます
* FHIR 変換: 正規化されたデータとグループ化されたデータは FHIR にマップされます。 観察結果は、構成されたテンプレートに従って作成または更新され、デバイスと患者にリンクされます。
* [会話マップの構築に役立つツール](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper): デバイス入力データを正規化するためのマッピング構成を視覚化し、FHIR リソースに変換します。 開発者は、このツールを使用してマッピング、デバイス マッピング、FHIR マッピングを編集およびテストし、Azure portal で IoT コネクタにアップロードするためにエクスポートできます。

#### <a name="healthkit-and-fhir-integration"></a>HealthKit と FHIR の統合
* [microsoft/healthkit-on-fhir](https://github.com/microsoft/healthkit-on-fhir): Apple HealthKit データの FHIR サーバーへのエクスポートを自動化する Swift ライブラリです

 