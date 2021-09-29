---
title: カスタム CA 証明書を追加する - Azure API Management | Microsoft Docs
description: Azure API Management でカスタム CA 証明書を追加する方法について説明します。 証明書を削除する手順についても確認できます。
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 06/01/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7e92d4a6c81376a9dbd004ac7fee978fa4d67e9a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128628821"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Azure API Management でカスタム CA 証明書を追加する方法

Azure API Management を使用すると、信頼できるルート証明書ストアと中間証明書ストア内のマシンに CA 証明書をインストールできます。 サービスにカスタム CA 証明書が必要な場合、この機能を使用する必要があります。

この記事では、Azure portal の Azure API Management サービス インスタンスの CA 証明書を管理する方法について説明します。 たとえば、自己署名クライアント証明書を使用する場合は、カスタムの信頼されたルート証明書を API Management にアップロードできます。 

API Management にアップロードされた CA 証明書は、マネージド API Management ゲートウェイによる証明書の検証にのみ使用できます。 [セルフホステッド ゲートウェイ](self-hosted-gateway-overview.md)を使用する場合は、この記事の後半で説明する[セルフホステッド ゲートウェイ 用のカスタム CAを作成する](#create-custom-ca-for-self-hosted-gateway)方法をご覧ください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>CA 証明書をアップロードする

:::image type="content" source="media/api-management-howto-ca-certificates/00.png" alt-text="Azure portal での CA 証明書":::

新しい CA 証明書をアップロードするには、次の手順を実行します。 API Management サービス インスタンスをまだ作成していない場合は、[API Management サービス インスタンスの作成](get-started-create-service-instance.md)に関するチュートリアルを参照してください。

1. Azure portal で Azure API Management サービス インスタンスに移動します。

1. メニューの **[セキュリティ]** で、 **[証明書] > [CA 証明書] > [+ 追加]** を選択します。

1. 証明書 .cer ファイルを参照し、証明書ストアを指定します。 公開キーのみが必要なので、パスワードは省略可能です。

    :::image type="content" source="media/api-management-howto-ca-certificates/02.png" alt-text="Azure portal で CA 証明書を追加する"::: 

1. **[保存]** を選択します。 この操作には数分かかることがあります。

> [!NOTE]
> `New-AzApiManagementSystemCertificate` PowerShell コマンドを使用して CA 証明書をアップロードすることもできます。

## <a name="delete-a-ca-certificate"></a><a name="step1a"> </a>CA 証明書を削除する

証明書を選択し、コンテキスト メニュー ( **[...]** ) で **[削除]** を選択します。

## <a name="create-custom-ca-for-self-hosted-gateway"></a>セルフホステッド ゲートウェイ用のカスタム CA を作成する 

[セルフホステッド ゲートウェイ](self-hosted-gateway-overview.md)を使用する場合、API Management サービスにアップロードされた CA ルート証明書を使用したサーバーおよびクライアントの証明書の検証はサポートされていません。 信頼を確立するには、ゲートウェイによってカスタム証明機関として信頼されるように特定のクライアント証明書を構成します。

セルフホステッド ゲートウェイ用のカスタム CA を作成および管理するには、[Gateway Certificate Authority](/rest/api/apimanagement/2021-01-01-preview/gateway-certificate-authority) REST API を使用します。 カスタム CA を作成するには:

1. API Management インスタンスに[証明書.pfx ファイルを追加](api-management-howto-mutual-certificates.md)します。
1. [Gateway Certificate Authority - Create または Update](/rest/api/apimanagement/2021-01-01-preview/gateway-certificate-authority/create-or-update) REST API を使用して、証明書を自己管理型ゲートウェイに関連付けます。

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
