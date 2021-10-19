---
title: cURL を使用して Azure Healthcare API にアクセスする
description: この記事では、cURL を使用して医療 API にアクセスする方法について説明します
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 07/19/2021
ms.author: ginle
ms.openlocfilehash: ddc7b291ba6ecd86e4915523345c40054794a993
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787498"
---
# <a name="access-the-healthcare-apis-preview-with-curl"></a>cURL を使用して医療 API (プレビュー) にアクセスする 

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、cURL を使用して Azure Healthcare API にアクセスする方法について説明します。

## <a name="prerequisites"></a>前提条件

### <a name="powershell"></a>PowerShell

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/)。
* コードをローカルで実行する場合は [、PowerShell](/powershell/module/powershellget/) と [Azure Az PowerShell をインストールします](/powershell/azure/install-az-ps)。
* 必要に応じて、Rest クライアント拡張機能を使用Visual Studio Codeスクリプトを実行できます。 詳細については [、「Rest クライアントドキュメントへのリンクを作成する」を参照してください](using-rest-client.md)。
* cURL をダウンロード [してインストールします](https://curl.se/download.html)。

### <a name="cli"></a>CLI

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/)。
* コードをローカルで実行する場合は、 をインストール[Azure CLI。](/cli/azure/install-azure-cli) 
* 必要に応じて、Git Bash などの Bash シェルをインストールします。このシェルは、Git に含まれている[Windows。](https://gitforwindows.org/)
* 必要に応じて、Rest クライアント拡張機能をVisual Studio Codeでスクリプトを実行します。 詳細については [、「Rest クライアントドキュメントへのリンクを作成する」を参照してください](using-rest-client.md)。
* cURL をダウンロード [してインストールします](https://curl.se/download.html)。

## <a name="obtain-azure-access-token"></a>Azure アクセス トークンを取得する

医療 API にアクセスする前に、ユーザーまたはクライアント アプリに適切なアクセス許可を付与する必要があります。 アクセス許可を付与する方法の詳細については、「医療 API の承認 [」を参照してください](authentication-authorization.md)。

医療 API の Azure アクセス トークンを取得するには、いくつかの異なる方法があります。 

> [!NOTE]
> Azure にログインし、Healthcare API インスタンスをデプロイした Azure サブスクリプションとテナントにログインしている必要があります。

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell-interactive
### check Azure environment and PowerShell versions
Get-AzContext 
Set-AzContext -Subscription <subscriptionid>
$PSVersionTable.PSVersion
Get-InstalledModule -Name Az -AllVersions
curl --version

### get access token for the FHIR service
$fhirservice="https://<fhirservice>.fhir.azurehealthcareapis.com"
$token=(Get-AzAccessToken -ResourceUrl $fhirservice).Token

### Get access token for the DICOM service
$dicomtokenurl= "https://dicom.healthcareapis.azure.com/"
$token=$( Get-AzAccessToken -ResourceUrl $dicomtokenurl).Token
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
### check Azure environment and CLI versions
az account show --output table
az account set --subscription <subscriptionid>
cli –version
curl --version

### get access token for the FHIR service
$fhirservice=https://<fhirservice>.fhir.azurehealthcareapis.com
token=$(az account get-access-token --resource=$fhirservice --query accessToken --output tsv)

### get access token for the DICOM service
dicomtokenurl= https://dicom.healthcareapis.azure.com/
token=$(az account get-access-token --resource=$dicomtokenurl --query accessToken --output tsv)
```

---

## <a name="access-data-in-the-fhir-service"></a>FHIR サービスのデータにアクセスする

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell-interactive
$fhirservice="https://<fhirservice>.fhir.azurehealthcareapis.com"
```

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
fhirservice="https://<fhirservice>.fhir.azurehealthcareapis.com"
```

---

`curl -X GET --header "Authorization: Bearer $token" $fhirservice/Patient`

[![curl スクリプトを使用して FHIR サービスのデータにアクセスします。 ](media/curl-fhir.png) ](media/curl-fhir.png#lightbox)

## <a name="access-data-in-the-dicom-service"></a>DICOM サービスのデータにアクセスする

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```powershell-interactive
$dicomservice="https://<dicomservice>.dicom.azurehealthcareapis.com"
```
# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
dicomservice="https://<dicomservice>.dicom.azurehealthcareapis.com"
```
---

`curl -X GET --header "Authorization: Bearer $token" $dicomservice/changefeed?includemetadata=false`

[![curl スクリプトを使用して DICOM サービスのデータにアクセスします。 ](media/curl-dicom.png) ](media/curl-dicom.png#lightbox)

## <a name="next-steps"></a>次の手順

この記事では、cURL を使用して医療 API データにアクセスする方法について説明しました。

の REST クライアント拡張機能を使用して Healthcare API データにアクセスする方法については、Visual Studio Codeしてください。 

>[!div class="nextstepaction"]
>[REST クライアントを使用して医療 API にアクセスする](using-rest-client.md)