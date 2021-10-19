---
title: Azure API for FHIR のデータ変換
description: $convert-data エンドポイントとコンバーターのカスタマイズ テンプレートを使用して、Azure API for FHIR のデータを変換します。
services: healthcare-apis
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 05/11/2021
ms.author: ranku
ms.openlocfilehash: b5f71dc21dc5d7e7f38cc35440273b61953e8e85
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121786061"
---
# <a name="how-to-convert-data-to-fhir-preview"></a>データを FHIR に変換する方法 (プレビュー)

> [!IMPORTANT]
> この機能はパブリックプレビュー段階であり、サービスレベルアグリーメントなしで提供されます。 運用環境のワークロードにはお勧めしません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

FHIR サービスの $convert データカスタムエンドポイントは、さまざまなデータ型から FHIR へのデータ変換を目的としています。 既定のテンプレートとして [FHIR Converter](https://github.com/microsoft/FHIR-Converter) プロジェクトのテンプレートと Liquid テンプレート エンジンが使用されます。 これらの変換テンプレートは必要に応じてカスタマイズすることができます。 現時点では、 **C-CDA から FHIR** への変換と **HL7v2 から fhir へ** の変換の2種類がサポートされています。

## <a name="use-the-convert-data-endpoint"></a>$convert-data エンドポイントを使用する

操作は、 `$convert-data` サービスの一部として実行するために、FHIR サービスに統合されます。 サーバーへの API 呼び出しを行って、データを FHIR に変換することができます。

`https://<<FHIR service base URL>>/$convert-data`

### <a name="parameter-resource"></a>パラメーターリソース

$convert-データは、次の表に示すように、要求本文で [パラメーター](http://hl7.org/fhir/parameters.html) リソースを受け取ります。 API 呼び出し要求本文には、次のパラメーターを含めることができます。

| パラメーター名      | 説明 | 指定可能な値 |
| ----------- | ----------- | ----------- |
| inputData      | 変換の対象となるデータ。 | 有効な JSON 文字列|
| inputDataType   | 入力のデータ型。 | ```HL7v2```, ``Ccda`` |
| templateCollectionReference | [Azure Container Registry (ACR)](https://azure.microsoft.com/services/container-registry/)での[OCI イメージ](https://github.com/opencontainers/image-spec)テンプレートコレクションへの参照。 変換に使用する液体テンプレートを含むイメージです。 既定のテンプレートまたは FHIR サービス内に登録されているカスタムテンプレートイメージへの参照を指定できます。 テンプレートのカスタマイズ、ACR でのホスティング、および FHIR サービスへの登録の詳細については、以下を参照してください。 | **HL7v2** の既定のテンプレートの場合: <br>```microsofthealth/fhirconverter:default``` <br>``microsofthealth/hl7v2templates:default``<br><br>**CDA** の既定のテンプレートの場合:``microsofthealth/ccdatemplates:default`` <br>\<RegistryServer\>/\<imageName\>@\<imageDigest\>, \<RegistryServer\>/\<imageName\>:\<imageTag\> |
| rootTemplate | データの変換中に使用するルート テンプレート。 | **HL7v2** の場合:<br>```ADT_A01```, ```OML_O21```, ```ORU_R01```, ```VXU_V04```<br><br> **C-CDA** の場合:<br>```CCD```, `ConsultationNote`, `DischargeSummary`, `HistoryandPhysical`, `OperativeNote`, `ProcedureNote`, `ProgressNote`, `ReferralNote`, `TransferSummary` |

> [!WARNING]
> 既定のテンプレートは MIT ライセンス下でリリースされ、Microsoft サポートではサポートされて **いません** 。
>
> 既定のテンプレートは、すぐに作業を開始できるようにするためだけに用意されています。 これらは、FHIR 用の Azure API のバージョンを更新すると更新される可能性があります。 したがって、変換動作を検証し、 **独自のテンプレートのコピー** を Azure Container Registry でホストし、それらを fhir 用の azure api に登録し、API 呼び出しでを使用して、異なるバージョンの Azure API FOR fhir 間で一貫性のあるデータ変換動作を行う必要があります。


**要求のサンプル:**

```json
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "inputData",
            "valueString": "MSH|^~\\&|SIMHOSP|SFAC|RAPP|RFAC|20200508131015||ADT^A01|517|T|2.3|||AL||44|ASCII\nEVN|A01|20200508131015|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|\nPID|1|3735064194^^^SIMULATOR MRN^MRN|3735064194^^^SIMULATOR MRN^MRN~2021051528^^^NHSNBR^NHSNMBR||Kinmonth^Joanna^Chelsea^^Ms^^CURRENT||19870624000000|F|||89 Transaction House^Handmaiden Street^Wembley^^FV75 4GJ^GBR^HOME||020 3614 5541^HOME|||||||||C^White - Other^^^||||||||\nPD1|||FAMILY PRACTICE^^12345|\nPV1|1|I|OtherWard^MainRoom^Bed 183^Simulated Hospital^^BED^Main Building^4|28b|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|||CAR|||||||||16094728916771313876^^^^visitid||||||||||||||||||||||ARRIVED|||20200508131015||"
        },
        {
            "name": "inputDataType",
            "valueString": "Hl7v2"
        },
        {
            "name": "templateCollectionReference",
            "valueString": "microsofthealth/fhirconverter:default"
        },
        {
            "name": "rootTemplate",
            "valueString": "ADT_A01"
        }
    ]
}
```

**応答のサンプル:**

```json
{
  "resourceType": "Bundle",
  "type": "transaction",
  "entry": [
    {
      "fullUrl": "urn:uuid:9d697ec3-48c3-3e17-db6a-29a1765e22c6",
      "resource": {
        "resourceType": "Patient",
        "id": "9d697ec3-48c3-3e17-db6a-29a1765e22c6",
        ...
        ...
      "request": {
        "method": "PUT",
        "url": "Location/50becdb5-ff56-56c6-40a1-6d554dca80f0"
      }
    }
  ]
}
```

## <a name="customize-templates"></a>テンプレートをカスタマイズする

テンプレートは、Visual Studio Code 用の [FHIR Converter 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter)を使用して、必要に応じてカスタマイズすることができます。 この拡張機能は、対話型の編集エクスペリエンスを提供するものであり、Microsoft が公開しているテンプレートやサンプル データを簡単にダウンロードできるようになっています。 詳細については、拡張機能のドキュメントを参照してください。

## <a name="host-and-use-templates"></a>テンプレートをホストして使用する

ACR で独自のテンプレートのコピーをホストすることを強くお勧めします。 独自のテンプレートのコピーをホストし、それらを $convert データ操作で使用するには、次の4つの手順が必要です。

1. Azure Container Registry にテンプレートをプッシュします。
1. Azure API for FHIR インスタンスでマネージド ID を有効にします。
1. Azure API for FHIR マネージド ID に ACR のアクセス権を付与します。
1. Azure API for FHIR に ACR サーバーを登録します。
1. オプションで、セキュリティで保護されたアクセス用に ACR ファイアウォールを構成します。

### <a name="push-templates-to-azure-container-registry"></a>Azure Container Registry にテンプレートをプッシュする

ACR インスタンスの作成後、[FHIR Converter 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter)の _FHIR Converter:Push Templates_ コマンドを使用して、カスタマイズしたテンプレートを ACR にプッシュします。 同じことは、[Template Management CLI ツール](https://github.com/microsoft/FHIR-Converter/blob/main/docs/TemplateManagementCLI.md)を使用して行うこともできます。

### <a name="enable-managed-identity-on-azure-api-for-fhir"></a>Azure API for FHIR でマネージド ID を有効にする

Azure portal で Azure API for FHIR サービスのインスタンスを参照し、[ **id** ] ブレードを選択します。
Azure API for FHIR でマネージド ID を有効にするには、状態を **[オン]** に変更します。

![マネージド ID の有効化](media/convert-data/fhir-mi-enabled.png)

### <a name="provide-access-of-the-acr-to-azure-api-for-fhir"></a>Azure API for FHIR に ACR のアクセス権を付与する

1. [ **アクセス制御 (IAM)** ] ブレードに移動します。

1. [ **追加**] を選択し、[ **ロールの割り当ての追加** ] を選択して [ロールの割り当ての追加] ページを開きます。

1. [Acrpull](../../role-based-access-control/built-in-roles.md#acrpull)ロールを割り当てます。 

   ![[ロールの割り当ての追加] ページ](../../../includes/role-based-access-control/media/add-role-assignment-page.png) 

Azure portal でロールに割り当てる方法の詳細については [Azure 組み込みロール](../../role-based-access-control/role-assignments-portal.md)に関するページを参照してください。

### <a name="register-the-acr-servers-in-azure-api-for-fhir"></a>Azure API for FHIR に ACR サーバーを登録する

Azure portal を使用するか、CLI を使用して、ACR サーバーを登録できます。

#### <a name="registering-the-acr-server-using-azure-portal"></a>Azure portal を使用した ACR サーバーの登録
Azure API for fhir インスタンスで、[**データの変換**] の [ **Artifacts** ] ブレードに移動します。 現在登録されている ACR サーバーの一覧が表示されます。 [ **追加**] を選択し、ドロップダウンメニューからレジストリサーバーを選択します。 登録を有効にするには、[ **保存** ] を選択する必要があります。 変更を適用してインスタンスを再起動するまでに数分かかる場合があります。

#### <a name="registering-the-acr-server-using-cli"></a>CLI を使用した ACR サーバーの登録
Azure API for FHIR には、最大20個の ACR サーバーを登録できます。

必要に応じて Azure PowerShell から医療 api CLI をインストールします。

```powershell
az extension add -n healthcareapis
```

以下の例に従って、Azure API for FHIR に ACR サーバーを登録します。

##### <a name="register-a-single-acr-server"></a>1 台の ACR サーバーを登録する

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

##### <a name="register-multiple-acr-servers"></a>複数台の ACR サーバーを登録する

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io fhiracr2020.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```
### <a name="configure-acr-firewall"></a>ACR ファイアウォールを構成する

ポータルから Azure ストレージ アカウントの **[ネットワーク]** を選択します。

   :::image type="content" source="media/convert-data/networking-container-registry.png" alt-text="コンテナーレジストリ。":::


**[選択されたネットワーク]** を選択します。 

[ **ファイアウォール** ] セクションで、[ **アドレス範囲** ] ボックスに IP アドレスを指定します。 インターネットまたはオンプレミスのネットワークからのアクセスを許可する IP 範囲を追加します。 

次の表では、Azure API for FHIR サービスがプロビジョニングされている Azure リージョンの IP アドレスを確認できます。

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
| 東日本           | 20.191.160.26     |
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


> [!NOTE]
> 上記の手順は、「FHIR データをエクスポートする方法」のドキュメントで説明されている構成手順に似ています。 詳細については、「 [Azure Storage への安全なエクスポート](../data-transformation/export-data.md#secure-export-to-azure-storage)」を参照してください。

### <a name="verify"></a>確認

templateCollectionReference パラメーターにテンプレート参照を指定して $convert-data API を呼び出します。

`<RegistryServer>/<imageName>@<imageDigest>`
