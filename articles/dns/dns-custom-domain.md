---
title: Azure DNS を Azure リソースと統合する - Azure DNS
description: この記事では、Azure DNS を使用して Azure リソースに DNS を提供する方法を説明します。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 05/06/2021
ms.author: rohink
ms.openlocfilehash: c09c1840c9976a2ac14cb5ef73f8c4e15000c54f
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2021
ms.locfileid: "109634939"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Azure DNS を使用して Azure サービス用のカスタム ドメイン設定を提供する

Azure DNS は、カスタム ドメインをサポートする、または完全修飾ドメイン名 (FQDN) を持つ Azure リソースに対して名前解決を提供します。 たとえば、ユーザーが `contoso.com` または `www.contoso.com` を FQDN として使用してアクセスする必要がある Azure Web アプリがあるとします。 この記事では、カスタム ドメインを使用するために Azure サービスと Azure DNS を構成する方法について説明します。

## <a name="prerequisites"></a>前提条件

カスタム ドメインに Azure DNS を使用するには、まずドメインを Azure DNS に委任する必要があります。 ネーム サーバーを委任のために構成する方法については、[Azure DNS へのドメインの委任](./dns-delegate-domain-azure-dns.md)に関するページを参照してください。 Azure DNS ゾーンにドメインを委任したら、必要な DNS レコードを構成できるようになります。

Azure Function App、パブリック IP アドレス、App Service (Web Apps)、BLOB ストレージ、および Azure CDN 用のバニティまたはカスタム ドメインを構成できます。

## <a name="azure-function-app"></a>Azure Function App

Azure 関数アプリに対してカスタム ドメインを構成するために、CNAME レコードがその関数アプリ自体で作成および構成されます。
 
1. **[Function App]** に移動して関数アプリを選択します。 *[設定]* で、 **[カスタム ドメイン]** を選択します。 *[割り当てられたカスタム ドメイン]* の下の **現在の URL** をメモしてください。このアドレスは、作成される DNS レコードの別名として使用されます。

    :::image type="content" source="./media/dns-custom-domain/function-app.png" alt-text="関数アプリの [カスタム ドメイン] のスクリーンショット。":::

1. DNS ゾーンに移動して **[+ レコード セット]** を選択します。 **[レコード セットの追加]** ページで次の情報を入力し、 **[OK]** を選択して作成します。

    :::image type="content" source="./media/dns-custom-domain/function-app-record.png" alt-text="関数アプリの [レコードセットの追加] ページのスクリーンショット。":::

    |プロパティ  |値  |説明  |
    |---------|---------|---------|
    | 名前 | myfunctionapp | この値とドメイン名ラベルを合わせたものが、カスタム ドメイン名の FQDN です。 |
    | Type | CNAME | CNAME レコードを別名として使用します。 |
    | TTL | 1 | 1 時間には 1 を使用します  |
    | TTL の単位 | 時間 | 時間数は時間の単位として使用されます  |
    | エイリアス | contosofunction.azurewebsites.net | 別名を作成している DNS 名。この例では、既定で関数アプリに指定される contosofunction.azurewebsites.net という DNS 名です。        |
    
1. 関数アプリに戻り、 *[設定]* で **[カスタム ドメイン]** を選択します。 **[+ カスタム ドメインの追加]** を選択します。

    :::image type="content" source="./media/dns-custom-domain/function-app-add-domain.png" alt-text="関数アプリの [カスタム ドメインの追加] ボタンのスクリーンショット。":::    

1. **[カスタム ドメインの追加]** ページで、 **[カスタム ドメイン]** テキスト フィールドに CNAME レコードを入力し、 **[検証]** を選択します。 そのレコードが見つかった場合、 **[カスタム ドメインの追加]** ボタンが表示されます。 別名を追加するには、 **[カスタム ドメインの追加]** を選択します。

    :::image type="content" source="./media/dns-custom-domain/function-app-cname.png" alt-text="関数アプリの [カスタム ドメインの追加] ページのスクリーンショット。":::

## <a name="public-ip-address"></a>パブリック IP アドレス

Application Gateway、Load Balancer、Cloud Service、Resource Manager VM、Classic VM などの、パブリック IP アドレスを使用するサービス用のカスタム ドメインを構成するために、A レコードが使用されます。

1. パブリック IP リソースに移動し、 **[構成]** を選択します。 表示されている IP アドレスをメモします。

    :::image type="content" source="./media/dns-custom-domain/public-ip.png" alt-text="パブリック IP の[構成] ページのスクリーンショット。":::

1. DNS ゾーンに移動して **[+ レコード セット]** を選択します。 **[レコード セットの追加]** ページで次の情報を入力し、 **[OK]** を選択して作成します。

    :::image type="content" source="./media/dns-custom-domain/public-ip-record.png" alt-text="パブリック IP のレコード セットのページのスクリーンショット。":::

    | プロパティ | 値 | 説明 |
    | -------- | ----- | ------------|
    | 名前 | webserver1 | この値とドメイン名ラベルを合わせたものが、カスタム ドメイン名の FQDN です。 |
    | Type | A | リソースは IP アドレスなので、A レコードを使用します。 |
    | TTL | 1 | 1 時間には 1 を使用します |
    | TTL の単位 | 時間 | 時間数は時間の単位として使用されます |
    | IP アドレス | `<your ip address>` | パブリック IP アドレス。 |

1. A レコードが作成されたら、`nslookup` を実行してレコードの解決を検証します。

    :::image type="content" source="./media/dns-custom-domain/public-ip-nslookup.png" alt-text="パブリック IP についての cmd での nslookup のスクリーンショット。":::

## <a name="app-service-web-apps"></a>App Service (Web Apps)

次の手順では、App Service Web アプリ用のカスタム ドメインを構成する方法を説明します。

1. **[App Service]** に移動し、カスタム ドメイン名を構成しているリソースを選択して、 *[設定]* で **[カスタム ドメイン]** を選択します。 **[割り当てられたカスタム ドメイン]** の下の *現在の URL* をメモしてください。このアドレスは、作成される DNS レコードの別名として使用されます。

    :::image type="content" source="./media/dns-custom-domain/web-app.png" alt-text="Web アプリの [カスタム ドメイン] のスクリーンショット。":::

1. DNS ゾーンに移動して **[+ レコード セット]** を選択します。 **[レコード セットの追加]** ページで次の情報を入力し、 **[OK]** を選択して作成します。

    :::image type="content" source="./media/dns-custom-domain/web-app.png" alt-text="Web アプリのレコード セットのページのスクリーンショット。":::

    | プロパティ  | 値 | 説明 |
    |---------- | ----- | ----------- |
    | 名前 | mywebserver | この値とドメイン名ラベルを合わせたものが、カスタム ドメイン名の FQDN です。 |
    | Type | CNAME | CNAME レコードを別名として使用します。 リソースが IP アドレスを使用していた場合、A レコードが使用されます。 |
    | TTL | 1 | 1 時間には 1 を使用します |
    | TTL の単位 | 時間 | 時間数は時間の単位として使用されます |
    | エイリアス | contoso.azurewebsites.net | 別名を作成している DNS 名。この例では、既定で Web アプリに指定される contoso.azurewebsites.net という DNS 名です。 |

1. Web アプリに戻り、 *[設定]* で **[カスタム ドメイン]** を選択します。 **[+ カスタム ドメインの追加]** を選択します。

    :::image type="content" source="./media/dns-custom-domain/web-app-add-domain.png" alt-text="Web アプリの [カスタム ドメインの追加] ボタンのスクリーンショット。":::  

1. **[カスタム ドメインの追加]** ページで、 **[カスタム ドメイン]** テキスト フィールドに CNAME レコードを入力し、 **[検証]** を選択します。 そのレコードが見つかった場合、 **[カスタム ドメインの追加]** ボタンが表示されます。 別名を追加するには、 **[カスタム ドメインの追加]** を選択します。

    :::image type="content" source="./media/dns-custom-domain/web-app-cname.png" alt-text="Web アプリの [カスタム ドメインの追加] ページのスクリーンショット。":::

1. プロセスが完了したら、**nslookup** を実行して、名前解決が機能していることを検証します。

    :::image type="content" source="./media/dns-custom-domain/app-service-nslookup.png" alt-text="Web アプリについての nslookup のスクリーンショット。"::: 

カスタム ドメインを App Service にマッピングする方法の詳細については、「[既存のカスタム DNS 名を Azure Web Apps にマップする](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json)」を参照してください。

アクティブな DNS 名を移行する方法については、「[Azure App Service へのアクティブな DNS 名の移行](../app-service/manage-custom-dns-migrate-domain.md)」を参照してください。

App Service 用にカスタム ドメインを購入する必要がある場合は、「[Azure Web Apps のカスタム ドメイン名を購入する](../app-service/manage-custom-dns-buy-domain.md)」を参照してください。

## <a name="blob-storage"></a>BLOB ストレージ

次の手順では、asverify の手法を使用して BLOB ストレージ アカウント用の CNAME レコードを構成する方法を説明します。 この方法はダウンタイムがないことを保証します。

1. **[ストレージ アカウント]** に移動し、ご利用のストレージ アカウントを選択し、 *[設定]* で **[ネットワーク]** を選択します。 次に、 **[カスタム ドメイン]** タブを選択します。手順 2 の FQDN を記録してください。この名前は、最初の CNAME レコードの作成に使用されます。

    :::image type="content" source="./media/dns-custom-domain/blob-storage.png" alt-text="ストレージ アカウントの [カスタム ドメイン] のスクリーンショット。":::

1. DNS ゾーンに移動して **[+ レコード セット]** を選択します。 **[レコード セットの追加]** ページで次の情報を入力し、 **[OK]** を選択して作成します。

    :::image type="content" source="./media/dns-custom-domain/storage-account-record.png" alt-text="ストレージ アカウントのレコード セットのページのスクリーンショット。":::

    | プロパティ | 値 | 説明 |
    | -------- | ----- | ----------- |
    | 名前 | asverify.mystorageaccount | この値とドメイン名ラベルを合わせたものが、カスタム ドメイン名の FQDN です。 |
    | Type | CNAME | CNAME レコードを別名として使用します。 |
    | TTL | 1 | 1 時間には 1 を使用します |
    | TTL の単位 | 時間 | 時間数は時間の単位として使用されます |
    | エイリアス | asverify.contoso.blob.core.windows.net | 別名を作成している DNS 名。この例では、既定でストレージ アカウントに指定される asverify.contoso.blob.core.windows.net という DNS 名です。 |

1. ストレージ アカウントに戻り、 **[ネットワーク]** 、 **[カスタム ドメイン]** タブの順に選択します。テキスト ボックスに、作成した別名を asverify プレフィックスなしで入力し、 **[間接 CNAME 検証を使用する]** のチェックを付け、 **[保存]** を選択します。 

    :::image type="content" source="./media/dns-custom-domain/blob-storage-add-domain.png" alt-text="ストレージ アカウントのカスタム ドメインの追加ページのスクリーンショット。":::

1. DNS ゾーンに戻り、asverify プレフィックスなしで CNAME レコードを作成します。  それ以降は、asverify プレフィックスを含む CNAME レコードを削除しても安全です。

    :::image type="content" source="./media/dns-custom-domain/storage-account-record-set.png" alt-text="asverify プレフィックスを含まないストレージ アカウント レコードのスクリーンショット。":::

1. `nslookup` を実行して DNS 解決を検証します。

カスタム ドメインを BLOB ストレージ エンドポイントにマップする方法について、詳しくは[BLOB ストレージ エンドポイントのカスタム ドメイン名の構成](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)を参照してください。

## <a name="azure-cdn"></a>Azure CDN

次の手順では、cdnverify の手法を使用して、CDN エンドポイント用の CNAME レコードを構成する方法を説明します。 この方法はダウンタイムがないことを保証します。

1. CDN プロファイルに移動し、作業するエンドポイントを選択します。 **[+ カスタム ドメイン]** を選択します。 **[Endpoint hostname] \(エンドポイントのホスト名)** の値は CNAME レコードが指すレコードであるため、これをメモします。

    :::image type="content" source="./media/dns-custom-domain/cdn.png" alt-text="CDN カスタム ドメインのページのスクリーンショット。":::

1. DNS ゾーンに移動して **[+ レコード セット]** を選択します。 **[レコード セットの追加]** ページで次の情報を入力し、 **[OK]** を選択して作成します。

    :::image type="content" source="./media/dns-custom-domain/cdn-record.png" alt-text="CDN のレコード セットのページのスクリーンショット。":::

    | プロパティ | 値 | 説明 |
    | -------- | ----- | ----------- |
    | 名前 | cdnverify.mycdnendpoint | この値とドメイン名ラベルを合わせたものが、カスタム ドメイン名の FQDN です。 |
    | Type | CNAME | CNAME レコードを別名として使用します。 |
    | TTL | 1 | 1 時間には 1 を使用します |
    | TTL の単位 | 時間 | 時間数は時間の単位として使用されます |
    | エイリアス | cdnverify.contoso.azureedge.net | 別名を作成している DNS 名。この例では、CDN エンドポイントに既定で指定される cdnverify.contoso.azureedge.net という DNS 名です。 |

1. CDN エンドポイントに戻り、 **[+ カスタム ドメイン]** を選択します。 CNAME レコードの別名を cdnverify プレフィックスなしで入力し、 **[追加]** を選択します。

    :::image type="content" source="./media/dns-custom-domain/cdn-add.png" alt-text="CDN エンドポイントの [カスタム ドメインの追加] ページのスクリーンショット。":::

1. DNS ゾーンに戻り、cdnverify プレフィックスなしで CNAME レコードを作成します。  それ以降は、cdnverify プレフィックスを含む CNAME レコードを削除しても安全です。

    :::image type="content" source="./media/dns-custom-domain/cdn-record-set.png" alt-text="cdnverify プレフィックスを含まない CDN レコードのスクリーンショット。":::

CDN の詳細と、中間登録手順なしでカスタム ドメインを構成する方法については、[カスタム ドメインへの Azure CDN コンテンツのマッピング](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json)を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure でホストされているサービスの逆引き DNS を構成する](dns-reverse-dns-for-azure-services.md)方法について説明します。
