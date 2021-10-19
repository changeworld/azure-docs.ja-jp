---
title: インクルード ファイル
description: インクルード ファイル
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/08/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 978e703285317c7a644f6d0c459a1f1d572b9179
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2021
ms.locfileid: "129801150"
---
## <a name="create-a-namespace-in-the-azure-portal"></a>Azure Portal での名前空間の作成
Azure の Service Bus メッセージング エンティティを使用するには、Azure 全体で一意となる名前を備えた名前空間を最初に作成しておく必要があります。 名前空間は、アプリケーション内で Service Bus リソースをアドレス指定するためのスコープ コンテナーを提供します。

名前空間を作成するには:

1. [Azure ポータル](https://portal.azure.com)
2. ポータルの左側のナビゲーション ウィンドウで、 **[+ リソースの作成]** 、 **[統合]** 、 **[Service Bus]** の順に選択します。

    :::image type="content" source="./media/service-bus-create-namespace-portal/create-resource-service-bus-menu.png" alt-text="メニューでの [リソースの作成]、[統合]、[Service Bus] の選択を示す画像。":::
3. **[名前空間の作成]** ページの **[基本]** タブで、こちらの手順を実行します。 
    1. **[サブスクリプション]** で、名前空間を作成する Azure サブスクリプションを選択します。
    1. **[リソース グループ]** で、名前空間を追加する既存のリソース グループを選択するか、新しいリソース グループを作成します。      
    1. **名前空間の名前** を入力します。 その名前が使用できるかどうかがすぐに自動で確認されます。 名前空間の名前付け規則の一覧については、[名前空間の作成の REST API](/rest/api/servicebus/create-namespace) に関する記事を参照してください。
    1. **[場所]** で、名前空間をホストするリージョンを選択します。
    1. **[価格レベル]** で、名前空間の価格レベル (Basic、Standard、Premium) を選択します。 このクイック スタートでは、 **[Standard]** を選択します。 
    
        [トピックとサブスクリプション](../service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)を使用する場合は、Standard または Premium を選択してください。 Basic 価格レベルでは、トピックとサブスクリプションはサポートされていません。 

        **[Premium]** 価格レベルを選択した場合は、**メッセージング ユニット** の数を指定します。 Premium レベルでは、各ワークロードが分離した状態で実行されるように、CPU とメモリのレベルでリソースが分離されます。 このリソースのコンテナーをメッセージング ユニットと呼びます。 Premium 名前空間には、少なくとも 1 つのメッセージング ユニットがあります。 Service Bus の Premium 名前空間ごとに、1 個、2 個、または 4 個のメッセージング ユニットを選択できます。 詳細については、[Service Bus の Premium メッセージング](../service-bus-premium-messaging.md)に関するページをご覧ください。
    1. **[Review + create]\(レビュー + 作成\)** を選択します。 これで、システムによってサービス名前空間が作成され、有効になります。 システムがアカウントのリソースを準備し 終わるまでに、数分間かかる場合があります。
   
        :::image type="content" source="./media/service-bus-create-namespace-portal/create-namespace.png" alt-text="[名前空間の作成] ページを示す画像":::
    1. **[確認および作成]** ページで、設定を確認し、 **[作成]** を選択します。 
4. デプロイ ページで **[リソースに移動]** を選択します。 

    :::image type="content" source="./media/service-bus-create-namespace-portal/deployment-alert.png" alt-text="[リソースに移動] リンクが表示された、デプロイが成功したページを示す画像。":::
6. Service Bus 名前空間のホーム ページが表示されます。 

    :::image type="content" source="./media/service-bus-create-namespace-portal/service-bus-namespace-home-page.png" alt-text="作成された Service Bus 名前空間のホーム ページを示す画像。" :::

## <a name="get-the-connection-string"></a>接続文字列を取得する 
新しい名前空間を作成すると、主キーとセカンダリ キー、およびそれぞれが名前空間のすべての側面を完全に制御できるプライマリとセカンダリの接続文字列を使用して、Shared Access Signature (SAS) の初期ポリシーが自動的に生成されます。 通常の送信者と受信者を対象に、より権限を制限した規則を作成する方法については、「[Service Bus の認証と承認](../service-bus-authentication-and-authorization.md)」をご覧ください。 

名前空間のプライマリ接続文字列をコピーするには、次の手順に従います。 

1. **[Service Bus 名前空間]** ページで、左側のメニューの **[共有アクセス ポリシー]** を選択します。
3. **[共有アクセス ポリシー]** ページで、 **[RootManageSharedAccessKey]** を選択します。
   
    :::image type="content" source="./media/service-bus-create-namespace-portal/connection-info.png" alt-text="スクリーンショットでは、[共有アクセス ポリシー] ウィンドウでポリシーが強調表示されています。":::
4. **[ポリシー:RootManageSharedAccessKey]** ウィンドウで、 **[プライマリ接続文字列]** の横にあるコピー ボタンをクリックし、後で使用するために接続文字列をクリップボードにコピーします。 この値をメモ帳などに一時的に貼り付けます。
   
    :::image type="content" source="./media/service-bus-create-namespace-portal/connection-string.png" alt-text="スクリーンショットには、キーと接続文字列を含む、RootManageSharedAccessKey という名前の S A S ポリシーが示されています。":::

    このページを使用して、主キー、セカンダリ キー、およびセカンダリ接続文字列をコピーできます。 
