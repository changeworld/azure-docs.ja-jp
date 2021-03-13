---
title: Azure API for FHIR で $export コマンドを呼び出してエクスポートを実行する
description: この記事では、$export を使用して FHIR データをエクスポートする方法について説明します
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 2/19/2021
ms.author: cavoeg
ms.openlocfilehash: 9ed78baed35312b9a33c71a3e49b7e9dca22eb9f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019151"
---
# <a name="how-to-export-fhir-data"></a>FHIR データをエクスポートする方法


一括エクスポート機能を使用すると、[FHIR 仕様](https://hl7.org/fhir/uv/bulkdata/export/index.html)に従って FHIR サーバーからデータをエクスポートできます。 

$export を使用する前に、Azure API for FHIR がそれを使用するように構成されていることを確認する必要があります。 エクスポート設定を構成し、Azure ストレージ アカウントを作成する方法については、[データのエクスポートの構成ページ](configure-export-data.md)を参照してください。

## <a name="using-export-command"></a>$export コマンドの使用

エクスポート用に Azure API for FHIR を構成すると、$export コマンドを使用して、サービスからデータをエクスポートできます。 データは、エクスポートの構成時に指定したストレージ アカウントに格納されます。 FHIR サーバーで $export コマンドを呼び出す方法については、[HL7 FHIR $export の仕様](https://hl7.org/Fhir/uv/bulkdata/export/index.html)に関するドキュメントを参照してください。 

Azure API For FHIR では、次のレベルでの $export がサポートされています。
* [システム](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export): `GET https://<<FHIR service base URL>>/$export>>`
* [患者](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients): `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [患者のグループ*](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients) - Azure API for FHIR では、関連するすべてのリソースがエクスポートされますが、グループの特性はエクスポートされません: `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`

データがエクスポートされると、リソースの種類ごとに個別のファイルが作成されます。 エクスポートされるファイルが大きくなりすぎないようにするために、エクスポートされた 1 つのファイルのサイズが 64 MB を超えたら新しいファイルを作成します。 この結果、リソースの種類ごとに複数のファイルが取得され、列挙されます (つまり、Patient-1.ndjson、Patient-2.ndjson)。 


> [!Note] 
> リソースが複数のリソースから成るコンパートメント内にある場合、または複数のグループに存在する場合は、`Patient/$export` と `Group/[ID]/$export` によって重複するリソースがエクスポートされることがあります。

さらに、キュー登録中に場所ヘッダーによって返された URL を介したエクスポートの状態の確認と、現行のエクスポート ジョブのキャンセルもサポートされています。

### <a name="exporting-fhir-data-to-adls-gen2"></a>ADLS Gen2 に FHIR データをエクスポートしています

現時点では、ADLS Gen2 有効なストレージアカウントの $export がサポートされていますが、次の制限があります。

- ユーザーは [階層的な名前空間](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace) をまだ活用できません。コンテナー内の特定のサブディレクトリへのエクスポートをターゲットにすることはできません。 特定のコンテナーをターゲットにする機能を提供するだけです (エクスポートごとに新しいフォルダーを作成します)。

- エクスポートが完了したら、そのフォルダーには何もエクスポートされません。これは、同じコンテナーへの後続のエクスポートが新しく作成されたフォルダー内にあるためです。


## <a name="settings-and-parameters"></a>設定とパラメーター

### <a name="headers"></a>ヘッダー
$export ジョブに設定する必要がある必須のヘッダー パラメーターが 2 つあります。 値は、現在の [$export 仕様](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers)で定義されています。
* **Accept** - application/fhir+json
* **Prefer** - respond-async

### <a name="query-parameters"></a>クエリ パラメーター
Azure API for FHIR では、次のクエリ パラメーターがサポートされています。 これらのパラメーターはすべて省略可能です。

|Query parameter (クエリ パラメーター)        | FHIR 仕様で定義されている    |  説明|
|------------------------|---|------------|
| \_outputFormat | はい | 現在、FHIR 仕様に合わせた 3 つの値 (application/fhir+ndjson、application/ndjson、または単にndjson) がサポートされています。 すべてのエクスポート ジョブは `ndjson` を返し、渡された値はコードの動作に影響を与えません。 |
| \_since | はい | 指定された時間以降に変更されたリソースのみをエクスポートできます |
| \_type | はい | どの種類のリソースを含めるかを指定できます。 たとえば、\_type=Patient とすると、患者のリソースのみが返されます|
| \_typefilter | はい | よりきめ細かいフィルター処理を要求するには、\_typefilter を \_type パラメーターと共に使用します。 _TypeFilter パラメーターの値は、結果をさらに限定する FHIR クエリのコンマ区切りリストです。 |
| \_container | いいえ |  データのエクスポート先となる、構成済みストレージ アカウント内のコンテナーを指定します。 コンテナーが指定されている場合は、その名前を持つ新しいフォルダー内のそのコンテナーにデータがエクスポートされます。 コンテナーが指定されていない場合は、タイムスタンプとジョブ ID を使用して新しいコンテナーにエクスポートされます。 |

## <a name="secure-export-to-azure-storage"></a>Azure Storage へのセキュリティで保護されたエクスポート

Azure API for FHIR は、セキュリティで保護されたエクスポート操作をサポートしています。 セキュリティで保護されたエクスポートを実行する1つのオプションは、Azure API に関連付けられた特定の IP アドレスに対して、FHIR が Azure ストレージアカウントにアクセスすることを許可することです。 ストレージアカウントが、FHIR の Azure API と同じ場所にあるか別の場所にあるかによって、構成が異なります。

### <a name="when-the-azure-storage-account-is-in-a-different-region"></a>Azure ストレージアカウントが別のリージョンにある場合

ポータルから Azure ストレージアカウントの [ネットワーク] ブレードを選択します。 

   :::image type="content" source="media/export-data/storage-networking.png" alt-text="ネットワーク設定を Azure Storage します。" lightbox="media/export-data/storage-networking.png":::
   
[選択されたネットワーク] を選択し、[ファイアウォールの追加] セクションの下の [ **アドレス範囲** ] ボックスに ip アドレスを指定して、 \| インターネットまたはオンプレミスのネットワークからのアクセスを許可します。 次の表では、Azure API for FHIR サービスがプロビジョニングされている Azure リージョンの IP アドレスを確認できます。

|**Azure リージョン**         |**パブリック IP アドレス** |
|:----------------------|:-------------------|
| オーストラリア東部       | 20.53.44.80       |
| カナダ中部       | 20.48.192.84      |
| 米国中部           | 52.182.208.31     |
| 米国東部              | 20.62.128.148     |
| 米国東部 2            | 20.49.102.228     |
| 米国東部 2 EUAP       | 20.39.26.254      |
| ドイツ北部        | 51.116.51.33      |
| ドイツ中西部 | 51.116.146.216    |
| Japan East           | 20.191.160.26     |
| 韓国中部        | 20.41.69.51       |
| 米国中北部     | 20.49.114.188     |
| 北ヨーロッパ         | 52.146.131.52     |
| 南アフリカ北部   | 102.133.220.197   |
| 米国中南部     | 13.73.254.220     |
| 東南アジア       | 23.98.108.42      |
| スイス北部    | 51.107.60.95      |
| 英国南部             | 51.104.30.170     |
| 英国西部              | 51.137.164.94     |
| 米国中西部      | 52.150.156.44     |
| 西ヨーロッパ          | 20.61.98.66       |
| 米国西部 2            | 40.64.135.77      |

### <a name="when-the-azure-storage-account-is-in-the-same-region"></a>Azure ストレージアカウントが同じリージョンにある場合

構成プロセスは上記と同じですが、CIDR 形式の特定の IP アドレス範囲が使用されます。ただし、100.64.0.0/10 を使用します。 100.64.0.0 –100.127.255.255 を含む IP アドレス範囲を指定する必要がある理由は、サービスで使用される実際の IP アドレスは異なるためですが、各 $export 要求の範囲内にあるためです。

> [!Note] 
> 代わりに、10.0.2.0/24 の範囲内のプライベート IP アドレスを使用することができます。 その場合、$export 操作は成功しません。 $Export 要求を再試行することはできますが、100.64.0.0/10 の範囲内の IP アドレスが次回使用されるという保証はありません。 これは設計上の既知のネットワーク動作です。 別の方法として、別のリージョンにストレージアカウントを構成する方法があります。
    
## <a name="next-steps"></a>次の手順

この記事では、$export コマンドを使用して、FHIR リソースをエクスポートする方法について説明しました。 次は、匿名化データをエクスポートする方法を確認します。
 
>[!div class="nextstepaction"]
>[匿名化データをエクスポートする](de-identified-export.md)
