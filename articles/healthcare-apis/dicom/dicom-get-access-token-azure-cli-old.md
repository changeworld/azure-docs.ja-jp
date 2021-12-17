---
title: Azure CLI を使用してアクセストークンを取得する - DICOM サービス用の Azure Healthcare APIs
description: この記事では、Azure CLI を使用して DICOM サービスのアクセス トークンを取得する方法について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: 1fe6fe950d11f30079d9b0d9ea3b91b5edf0711d
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814701"
---
# <a name="get-access-token-for-the-dicom-service-using-azure-cli"></a>Azure CLI を使用して、DICOM サービスのアクセス トークンを取得する

> [!IMPORTANT]
> Azure Healthcare APIs は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

この記事では、Azure CLI を使用して DICOM サービスのアクセス トークンを取得する方法を学びます。 [DICOM サービスをデプロイする](deploy-dicom-services-in-azure.md)とき、サービスにアクセスできるユーザーまたはサービス プリンシパルのセットを構成します。 ユーザー オブジェクト ID が許可されているオブジェクト ID の一覧にある場合は、Azure CLI を使用して取得したトークンを使用して、サービスにアクセスすることができます。

## <a name="prerequisites"></a>前提条件

Azure Cloud Shell で Bash 環境を使用します。


[ ![Azure Cloud Shell を起動します。](media/launch-cloud-shell.png) ](media/launch-cloud-shell.png#lightbox)

必要に応じて、Azure CLI を[インストール](/cli/azure/install-azure-cli)して、CLI リファレンス コマンドを実行します。

* ローカル インストールを使用する場合は、[az login](/cli/azure/reference-index#az_login) コマンドを使用して Azure CLI にサインインします。 認証プロセスを完了するには、ターミナルに表示される手順に従います。 その他のサインイン オプションについては、[Azure CLI でのサインイン](/cli/azure/authenticate-azure-cli)に関するページを参照してください。
* 初回使用時にインストールを求められたら、Azure CLI 拡張機能をインストールします。 拡張機能の詳細については、[Azure CLI で拡張機能を使用する方法](/cli/azure/azure-cli-extensions-overview)に関するページを参照してください。
* [az version](/cli/azure/reference-index#az_version) を実行し、インストールされているバージョンおよび依存ライブラリを検索します。 最新バージョンにアップグレードするには、[az upgrade](/cli/azure/reference-index#az_upgrade) を実行します。

## <a name="obtain-a-token"></a>トークンを取得する

DICOM サービス では、DICOM サーバーの URI (`https://dicom.healthcareapis.azure.com`) と同じ URI で `resource` または `Audience` を使用します。 次のコマンドを使用して、トークンを取得し、変数 (`$token`) に格納することができます。


```Azure CLICopy
Try It
$token=$(az account get-access-token --resource=https://dicom.healthcareapis.azure.com --query accessToken --output tsv)
```

## <a name="use-with-the-dicom-service"></a>DICOM サービスで使用する

```Azure CLICopy
Try It
curl -X GET --header "Authorization: Bearer $token"  https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com/v<version of REST API>/changefeed
```

## <a name="next-steps"></a>次のステップ

この記事では、Azure CLI を使用して DICOM サービスのアクセス トークンを取得する方法について学びました。 

>[!div class="nextstepaction"]
>[DICOM サービスの概要](dicom-services-overview.md)
