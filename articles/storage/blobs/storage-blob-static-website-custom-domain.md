---
title: チュートリアル:Azure の静的な Web サイトのカスタム ドメインと SSL を有効にする
description: 静的な Web サイト ホスティングのためにカスタム ドメインを構成する方法について説明します。
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 893ac53dc9f0b6b162c5ec22e478cd15706e50fb
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327494"
---
# <a name="tutorial-enable-custom-domain--ssl-for-a-static-website-in-azure"></a>チュートリアル:Azure の静的な Web サイトのカスタム ドメインと SSL を有効にする

このチュートリアルは、シリーズの第 2 部です。 ここでは、静的な Web サイトに対して SSL を使ってカスタム ドメイン エンドポイントを有効にする方法について説明します。 

このチュートリアルでは、[Azure CDN](../../cdn/cdn-overview.md) を使用して、静的な Web サイト用のカスタム ドメイン エンドポイントを構成する方法を示します。 Azure CDN では、カスタム SSL 証明書をプロビジョニングし、カスタム ドメインを使用して、同時にすべてのカスタム書き換えルールを構成することができます。 Azure CDN を構成すると追加料金が発生しますが、世界中のあらゆる場所から Web サイトへの一貫した低待機時間が提供されます。 また、Azure CDN では、独自の証明書での SSL 暗号化が提供されます。 Azure CDN の価格については、[Azure CDN の価格](https://azure.microsoft.com/pricing/details/cdn/)に関するページを参照してください。

シリーズの第 2 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * 静的な Web サイト エンドポイントで CDN エンドポイントを作成する
> * カスタム ドメインと SSL を有効にする

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、第 1 部の「[Tutorial: Host a static website on Blob Storage](storage-blob-static-website-host.md)」 (チュートリアル: Blob Storage で静的な Web サイトをホストする) を完了します。 

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure ポータル](https://portal.azure.com/)にサインインして、作業を開始します。

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>静的な Web サイト エンドポイントで CDN エンドポイントを作成する

1. Azure portal で自分のストレージ アカウントを探して、アカウントの概要を表示します。
1. **[Blob service]** メニューで **[Azure CDN]** を選択して、Azure CDN を構成します。
1. **[CDN のプロファイル]** セクションで、新規または既存の CDN プロファイルを指定します。 詳細については、「[クイック スタート: Azure CDN プロファイルとエンドポイントの作成](../../cdn/cdn-create-new-endpoint.md)」を参照してください。
1. CDN エンドポイントの価格レベルを指定します。 このチュートリアルでは、"**Standard Akamai**" 価格レベルを使用します。これは通常、数分以内にすばやく伝達されるからです。 その他の価格レベルは伝達に時間がかかることがありますが、別の利点もあります。 詳細については、「[Azure CDN 製品の機能を比較する](../../cdn/cdn-features.md)」を参照してください。
1. **[CDN エンドポイント名]** フィールドに、CDN エンドポイントの名前を指定します。 CDN エンドポイントは Azure 全体で一意である必要があります。
1. **[配信元のホスト名]** フィールドに、静的な Web サイト エンドポイントを指定します。 静的な Web サイト エンドポイントを見つけるには、ストレージ アカウントの **[静的な Web サイト]** 設定に移動します。 プライマリ エンドポイントをコピーし、CDN 構成に貼り付けます。プロトコル識別子 ("HTTPS" *など*) は削除します。

    次の図に、エンドポイント構成の例を示します。

    ![サンプルの CDN エンドポイント構成を示すスクリーンショット](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. CDN エンドポイントを作成し、伝達を待機します。
1. CDN エンドポイントが正しく構成されていることを確認するには、エンドポイントをクリックして設定に移動します。 ストレージ アカウントの CDN の概要からエンドポイントのホスト名を探し、次の図に示すように、エンドポイントに移動します。 CDN エンドポイントの形式は、`https://staticwebsitesamples.azureedge.net` のようになります。

    ![CDN エンドポイントの概要を示すスクリーンショット](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    CDN エンドポイントの伝達が完了した後、CDN エンドポイントに移動すると、静的な Web サイトに以前アップロードした index.html ファイルの内容が表示されます。

1. CDN エンドポイントの配信元の設定を確認するには、CDN エンドポイントの **[設定]** セクションにある **[配信元]** に移動します。 **[配信元の種類]** フィールドが *[カスタムの配信元]* に設定され、 **[配信元のホスト名]** フィールドに静的な Web サイト エンドポイントが表示されていることを確認できます。

    ![CDN エンドポイントの配信元の設定を示すスクリーンショット](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>カスタム ドメインと SSL を有効にする

1. ドメイン名プロバイダーで CNAME レコードを作成し、カスタム ドメインを CDN エンドポイントにリダイレクトします。 *www* サブドメインの CNAME レコードは、次のようになります。

    ![www サブドメインの CNAME レコードを指定する](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. Azure portal で、CDN エンドポイントの設定を表示します。 **[設定]** の **[カスタム ドメイン]** に移動して、カスタム ドメインと SSL 証明書を構成します。
1. **[カスタム ドメインの追加]** を選択し、ドメイン名を入力してから **[追加]** をクリックします。
1. 新しいカスタム ドメインのマッピングを選択し、SSL 証明書をプロビジョニングします。
1. **[カスタム ドメイン HTTPS]** を **[オン]** に設定し、 **[保存]** をクリックします。 カスタム ドメインの構成には数時間かかる場合があります。 ポータルでは、次の図に示すように進行状況が表示されます。

    ![カスタム ドメイン構成の進行状況を示すスクリーンショット](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. カスタム ドメインの URL にアクセスして、静的な Web サイトのカスタム ドメインへのマッピングをテストします。

カスタム ドメインの HTTPS の有効化の詳細については、「[チュートリアル:Azure CDN カスタム ドメインで HTTPS を構成する](../../cdn/cdn-custom-ssl.md)」を参照してください。

## <a name="next-steps"></a>次の手順

このチュートリアルの第 2 部では、静的な Web サイト用に Azure CDN で SSL を使用してカスタム ドメインを構成する方法について説明しました。

Azure CDN の構成と使用方法の詳細については、[Azure CDN の概要](../../cdn/cdn-overview.md)に関するページを参照してください。