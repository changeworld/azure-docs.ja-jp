---
title: Azure portal を使用してプライベート エンドポイント用の DNS レコードをエクスポートする
titleSuffix: Azure Private Link
description: このチュートリアルでは、Azure portal でプライベート エンドポイント用の DNS レコードをエクスポートする方法について学習します。
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: how-to
ms.date: 07/25/2021
ms.custom: how-to
ms.openlocfilehash: d4ad1fd995bfd4f1565215491037e6450bd65bf2
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713124"
---
# <a name="export-dns-records-for-a-private-endpoint-using-the-azure-portal"></a>Azure portal を使用してプライベート エンドポイント用の DNS レコードをエクスポートする

Azure のプライベート エンドポイントでは、エンドポイントの名前解決のために DNS レコードが必要です。 DNS レコードにより、構成されたリソースのエンドポイントのプライベート IP アドレスが解決されます。 エンドポイントの DNS レコードをエクスポートするには、ポータルの Private Link センターを使用します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成します](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- サブスクリプションで構成されたプライベート エンドポイント。 この記事の例では、Azure Web アプリへのプライベート エンドポイントを使用します。 Web アプリのプライベート エンドポイントの作成の詳細については、「[チュートリアル: Azure プライベート エンドポイントを使用して Web アプリに接続する](tutorial-private-endpoint-webapp-portal.md)」を参照してください。

## <a name="export-endpoint-dns-records"></a>エンドポイントの DNS レコードをエクスポートする

このセクションでは、Azure portal にサインインして Private Link センターを見つけます。

1. [Azure portal](https://portal.azure.com) にサインインします。

2. ポータルの上部にある検索ボックスに、「**Private Link**」と入力します。

3. **Private Link** を選択します。

4. Private Link センターで、 **[プライベート エンドポイント]** を選択します。

    :::image type="content" source="./media/private-endpoint-export-dns/private-link-center.png" alt-text="Private Link センターで [プライベート エンドポイント] を選択します":::

5. **[プライベート エンドポイント]** で、DNS レコードをエクスポートするエンドポイントを選択します。 **[Download host file]\(ホスト ファイルのダウンロード\)** を選択し、ホスト ファイル形式でエンドポイントの DNS レコードをダウンロードします。
    
    :::image type="content" source="./media/private-endpoint-export-dns/download-host-file.png" alt-text="エンドポイントの DNS レコードをダウンロードします":::

6. 次のようなホスト ファイル レコードがダウンロードされます。

    ```text
    # Exported from the Azure portal "2021-07-26 11:26:03Z"
    # Private IP    FQDN    Private Endpoint Id
    10.1.0.4    mywebapp8675.scm.azurewebsites.net    #/subscriptions/7cc654c6-760b-442f-bd02-1a8a64b17413/resourceGroups/myResourceGroup/providers/Microsoft.Network/privateEndpoints/mywebappendpoint
    10.1.0.4    mywebapp8675.azurewebsites.net    #/subscriptions/7cc654c6-760b-442f-bd02-1a8a64b17413/resourceGroups/myResourceGroup/providers/Microsoft.Network/privateEndpoints/mywebappendpoint
    ```

## <a name="next-steps"></a>次のステップ

Azure Private Link と DNS の詳細については、「[Azure プライベート エンドポイントの DNS 構成](private-endpoint-dns.md)」を参照してください。

Azure Private Link の詳細については、以下を参照してください。

* [Azure Private Link とは](private-link-overview.md)
* [Azure Private Link サービスとは](private-link-service-overview.md)
* [Azure Private Link のよく寄せられる質問 (FAQ)](private-link-faq.yml)