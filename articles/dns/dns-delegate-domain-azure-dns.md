---
title: チュートリアル - Azure DNS でドメインとサブドメインをホストする
description: このチュートリアルでは、Azure DNS を構成して DNS ゾーンをホストする方法を示します。
services: dns
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: tutorial
ms.date: 6/13/2018
ms.author: victorh
ms.openlocfilehash: 44f5bf9a28d56e85bae1d50136c50868ec96eb4e
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205443"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>チュートリアル: Azure DNS でドメインをホストする

Azure DNS を使用して DNS ドメインをホストし、DNS レコードを管理することができます。 Azure でドメインをホストすることで、その他の Azure サービスと同じ資格情報、API、ツール、課金情報を使用して DNS レコードを管理できます。 

ドメイン contoso.net をドメイン名登録業者から購入し、その後 Azure DNS で contoso.net という名前のゾーンを作成するとします。 自分がドメインの所有者であるので、ドメインのネーム サーバー (NS) レコードを構成するオプションが登録業者から提供されます。 登録業者は NS レコードを .net の親ゾーンに格納します。 インターネット ユーザーが世界のどこにいても、contoso.net 内の DNS レコードを解決しようとした時点で、Azure DNS ゾーン内のドメインに転送されます。


このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * DNS ゾーンの作成
> * ネーム サーバーの一覧の取得
> * ドメインの委任
> * 委任が機能していることの確認


Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="create-a-dns-zone"></a>DNS ゾーンの作成

1. Azure ポータルにサインインします。
1. 左上で、**[リソースの作成]** > **[ネットワーキング]** > **[DNS ゾーン]** の順に選択し、**[DNS ゾーンの作成]** ページを開きます。

   ![[DNS ゾーン]](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. **[DNS ゾーンの作成]** ページで次の値を入力してから、**[作成]** を選択します。

   | **設定** | **値** | **詳細** |
   |---|---|---|
   |**名前**|[ドメイン名] |購入したドメイン名。 このチュートリアルでは、例として contoso.net を使用します。|
   |**サブスクリプション**|<該当するサブスクリプション>|ゾーンの作成先となるサブスクリプションを選択します。|
   |**リソース グループ**|**[新規作成]**: contosoRG|リソース グループを作成します。 選択したサブスクリプション内で一意となるリソース グループ名を使用してください。 |
   |**場所**|米国東部||

> [!NOTE]
> リソース グループの場所は、DNS ゾーンには影響しません。 DNS ゾーンの場所は常に "グローバル" であり、それは表示されません。

## <a name="retrieve-name-servers"></a>ネーム サーバーの取得

DNS ゾーンを Azure DNS に委任するには、ゾーンのネーム サーバーを把握する必要があります。 Azure DNS は、ゾーンが作成されるたびに、プールからネーム サーバーを割り当てます。

1. DNS ゾーンが作成されたら、Azure Portal の **[お気に入り]** ウィンドウで **[すべてのリソース]** を選択します。 **[すべてのリソース]** ページで DNS ゾーンを選択します。 選択したサブスクリプションに既存のリソースがいくつもある場合は、**[名前でフィルター]** ボックスに自身のドメイン名を入力すると、アプリケーション ゲートウェイがすぐに見つかります。 

1. [DNS ゾーン] ページでネーム サーバーを取得します。 この例では、ゾーン contoso.net に、ネーム サーバー *ns1-01.azure-dns.com*、*ns2-01.azure-dns.net*、*ns3-01.azure-dns.org*、*ns4-01.azure-dns.info* が割り当てられています。

   ![ネーム サーバーの一覧](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

割り当てられたネーム サーバーのゾーンに、権限のある NS レコードが Azure DNS によって自動的に作成されます。


## <a name="delegate-the-domain"></a>ドメインの委任

DNS ゾーンを作成してネーム サーバーを取得したところで、Azure DNS ネーム サーバーを使用して親ドメインを更新する必要があります。 各レジストラーは独自の DNS 管理ツールを所有していて、ドメインのネーム サーバー レコードを変更します。 登録業者の DNS 管理ページで、NS レコードを編集し、その NS レコードを、Azure DNS のネーム サーバーに置き換えます。

ドメインを Azure DNS に委任する場合、Azure DNS によって提供されるネーム サーバーを使用する必要があります。 対象のドメインの名前に関係なく、4 つのネーム サーバーすべてを使用することをお勧めします。 ドメインの委任では、対象のドメインと同じトップレベル ドメインがネーム サーバーに使用される必要はありません。

独自のゾーンのネーム サーバー ("*バニティ ネーム サーバー*" と呼ばれることもあります) を使用した委任は、現在、Azure DNS ではサポートされていません。

## <a name="verify-that-the-delegation-is-working"></a>委任が機能していることの確認

委任が完了したら、*nslookup* などのツールを使用してゾーンの Start of Authority (SOA) レコードを照会することで、それが動作していることを確認できます。 SOA レコードは、ゾーンの作成時に自動的に作成されます。 機能していることを確認するには、委任が完了した後に 10 以上待つ必要がある場合があります。 DNS システムに変更が反映されるまで少し時間がかかる場合があります。

Azure DNS ネーム サーバーを指定する必要がありません。 委任が正しく設定されている場合は、通常の DNS 解決プロセスで自動的にネーム サーバーが検出されます。

コマンド プロンプトで、次のような nslookup コマンドを入力します。

```
nslookup -type=SOA contoso.net
```

前のコマンドから返される応答のサンプルがこちらです。

```
Server: ns1-04.azure-dns.com
Address: 208.76.47.4

contoso.net
primary name server = ns1-04.azure-dns.com
responsible mail addr = msnhst.microsoft.com
serial = 1
refresh = 900 (15 mins)
retry = 300 (5 mins)
expire = 604800 (7 days)
default TTL = 300 (5 mins)
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

次のチュートリアルに進む場合は、**contosoRG** リソース グループをそのまま保持できます。 それ以外の場合は、**contosoRG** リソース グループを削除してこのチュートリアルで作成したリソースを削除します。 これを行うには、**contosoRG** リソース グループをクリックして、**[リソース グループの削除]** をクリックします。 

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ドメインの DNS ゾーンを作成し、それを Azure DNS に委任しました。 Azure DNS と Web アプリについて詳しくは、Web アプリのチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [カスタム ドメインにおける Web アプリの DNS レコードの作成](./dns-web-sites-custom-domain.md)
