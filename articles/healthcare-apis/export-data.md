---
title: Azure API for FHIR で $export コマンドを呼び出してエクスポートを実行する
description: この記事では、匿名化エクスポートを設定して使用する方法について説明します。
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/26/2020
ms.author: matjazl
ms.openlocfilehash: 83509b5f452ab7cf88774561c12d7aa2cf3b46cf
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2020
ms.locfileid: "89482319"
---
# <a name="how-to-export-fhir-data"></a>FHIR データをエクスポートする方法

エクスポート設定を構成し、Azure ストレージ アカウントを作成する方法については、[ここ](configure-export-data.md)を参照してください。

## <a name="exporting-fhir-resources-using-export-command"></a>$export コマンドを使用して FHIR リソースをエクスポートする

エクスポート用に Azure API for FHIR を構成したら、$export コマンドを使用して、エクスポートの構成に指定したストレージ アカウントにサービスからデータをエクスポートできます。 FHIR サーバーで $export コマンドを呼び出す方法については、$export の仕様に関するドキュメント([https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html)) を参照してください。 

Azure API for FHIR の $export コマンドでは、省略可能な _\_conatiner_ パラメーターを使用して、データのエクスポート先として構成されているストレージ アカウント内のコンテナーを指定できます。

`https://<<FHIR service base URL>>/$export?_container=<<container_name>>`

> [!IMPORTANT]
> 現時点では、Azure API for FHIR では、エクスポート仕様 ([https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html)) に定義されているシステム レベルのエクスポートのみがサポートされていることに注意してください。 また、現時点では _\_since_ クエリ パラメーターのみがサポートされています。

## <a name="exporting-de-identified-data-preview"></a>匿名化データをエクスポートする (プレビュー)

$export コマンドを使用して、FHIR サーバーから匿名化データをエクスポートすることもできます。 [FHIR 匿名化ツール](https://github.com/microsoft/FHIR-Tools-for-Anonymization)の匿名化エンジンが使用され、クエリ パラメーターで匿名化構成の詳細が取得されます。 独自の匿名化構成ファイルを作成することも、HIPAA Safe Harbor メソッドの[サンプル構成ファイル](https://github.com/microsoft/FHIR-Tools-for-Anonymization#sample-configuration-file-for-hipaa-safe-harbor-method)を出発点として使用することもできます。 

 `https://<<FHIR service base URL>>/$export?_container=<<container_name>>&_anonymizationConfig=<<config file name>>&_anonymizationConfigEtag=<<ETag on storage>>`

|Query parameter (クエリ パラメーター)            | 例 |選択肢| 説明|
|---------------------------|---------|-----------|------------|
| _\_anonymizationConfig_   |DemoConfig.json|匿名化エクスポートで必須 |構成ファイルの名前です。 構成ファイル形式については、[ここ](https://github.com/microsoft/FHIR-Tools-for-Anonymization#configuration-file-format)を参照してください。 このファイルは、エクスポート先として構成されている場所と同じ Azure ストレージ アカウント内の**匿名化**という名前のコンテナー内に保存する必要があります。 |
| _\_anonymizationConfigEtag_|"0x8D8494A069489EC"|匿名化エクスポートで任意|これは構成ファイルの Etag です。 Azure Storage Explorer を使用して、BLOB プロパティから Etag を取得できます。|

> [!IMPORTANT]
> 生エクスポートと匿名化エクスポートではどちらも、エクスポート構成の一部として指定されたときと同じ Azure ストレージ アカウントに書き込まれることに注意してください。 別の匿名化構成に対応する別のコンテナーを使用し、コンテナー レベルでユーザー アクセスを管理することをお勧めします。

## <a name="next-steps"></a>次の手順

この記事では、$export コマンドを使用して、識別されていないデータを含め、FHIR リソースをエクスポートする方法について説明しました。 次は、エクスポート データを構成します。
 
>[!div class="nextstepaction"]
>[エクスポート データを構成する](configure-export-data.md)
