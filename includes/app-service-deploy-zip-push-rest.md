---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 7771bbcab08fb84a941eedab402880fb7bc4232d
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2018
ms.locfileid: "53551080"
---
## <a name="rest"></a>REST API を使って ZIP ファイルを展開する 

[展開サービス REST API](https://github.com/projectkudu/kudu/wiki/REST-API) を使って、.zip ファイルを Azure 内のアプリに展開できます。 展開するには、POST 要求を https://<アプリ名>.scm.azurewebsites.net/api/zipdeploy に送信します。 POST 要求のメッセージの本文に .zip ファイルを含める必要があります。 アプリの展開資格情報は、HTTP 基本認証を使って要求で提供します。 詳しくは、[.zip のプッシュ展開](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)に関するページをご覧ください。 

HTTP BASIC 認証では、App Service 展開資格情報が必要です。 自分の展開資格情報を設定する方法については、「[ユーザー レベルの資格情報の設定とリセット](../articles/app-service/deploy-configure-credentials.md#userscope)」を参照してください。

### <a name="with-curl"></a>cURL を使用する

次の例は cURL ツールを使用して .zip ファイルを展開します。 プレースホルダー `<username>`、`<password>`、`<zip_file_path>`、`<app_name>` を置き換えます。 cURL によって要求されたら、パスワードを入力します。

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

この要求により、アップロードされた .zip ファイルからのプッシュ展開がトリガーされます。 次の cURL の例で示すように、`https://<app_name>.scm.azurewebsites.net/api/deployments` エンドポイントを使うことにより、現在および過去の展開を確認できます。 やはり、`<app_name>` はアプリの名前に置き換え、`<deployment_user>` は展開資格情報のユーザー名に置き換えます。

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>PowerShell の場合

次の例では、[Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod)を使って、.zip ファイルを含む要求を送信します。 プレースホルダー `<deployment_user>`、`<deployment_password>`、`<zip_file_path>`、`<app_name>` を置き換えます。

```PowerShell
#PowerShell
$username = "<deployment_user>"
$password = "<deployment_password>"
$filePath = "<zip_file_path>"
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/zipdeploy"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method POST -InFile $filePath -ContentType "multipart/form-data"
```

この要求により、アップロードされた .zip ファイルからのプッシュ展開がトリガーされます。 現在と過去の展開を確認するには、次のコマンドを実行します。 ここでも `<app_name>` プレースホルダーを置き換えます。

```bash
$apiUrl = "https://<app_name>.scm.azurewebsites.net/api/deployments"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
