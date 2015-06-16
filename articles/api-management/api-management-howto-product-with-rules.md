<properties 
	pageTitle="Azure API Management で成果物を作成して詳細設定を行う方法" 
	description="クォータとレート制限のポリシーを使用して成果物を構成する方法について説明します。" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2/11/2015" 
	ms.author="sdanie"/>

# Azure API Management で成果物を作成して詳細設定を行う方法

Azure API Management (プレビュー) では、API 発行者の要件に応じて成果物を細かく設定することができます。このトピックでは、成果物に対してレート制限ポリシーやクォータ ポリシーなど、いくつかの詳細設定を行う方法について説明します。

このチュートリアルでは、呼び出しのレート制限 (1 分間に最大 10 回、1 週間に最大 200 回) を設けた無料評価版の成果物を作成し、発行して、そのレート制限ポリシーをテストします。

## このトピックの内容

-   [成果物の作成][成果物の作成]
-   [成果物への API の追加][成果物への API の追加]
-   [呼び出しレート制限ポリシーとクォータ ポリシーの構成][呼び出しレート制限ポリシーとクォータ ポリシーの構成]
-   [成果物の発行][成果物の発行]
-   [成果物のサブスクリプションを開発者アカウントに追加する][成果物のサブスクリプションを開発者アカウントに追加する]
-   [操作の呼び出しとレート制限のテスト][操作の呼び出しとレート制限のテスト]
-   [次のステップ][次のステップ]

## <a name="create-product"> </a>成果物の作成

この手順では、サブスクリプションの承認が不要な無料評価版の成果物を作成します。

最初に、ご利用の API Management サービスの Azure ポータルで **[管理コンソール]** をクリックします。API Management の管理ポータルが表示されます。

> まだ API Management サービス インスタンスを作成していない場合は、「[Azure API Management の使用][Azure API Management の使用]」チュートリアルの「[API Management インスタンスの作成][API Management インスタンスの作成]」を参照してください。

![API Management console][API Management console]

左側の **[API Management]** メニューにある **[成果物]** をクリックして、**[成果物]** ページを表示します。

![Add product][Add product]

**[成果物の追加]** をクリックして **[新しい成果物の追加]** ポップアップ ウィンドウを表示します。

![Add new product][Add new product]

**[タイトル]** ボックスに「**Free Trial**」と入力します。

**[説明]** ボックスに「**Subscribers will be able to run 10 calls/minute up to a maximum of 200 calls/week after which access is denied.**」と入力します。

この成果物に対するサブスクリプションの申し込みを管理者の審査の下で承認または拒否する場合は、**[サブスクリプションの承認を必須とする]** チェック ボックスをオンにします。チェック ボックスがオフの場合、サブスクリプションの申し込みは自動承認されます。この例では、サブスクリプションを自動的に承認するため、チェック ボックスはオフにしてください。

すべての値を入力したら、**[保存]** をクリックして成果物を作成します。

![Product added][Product added]

**Administrators** グループのユーザーには、新しい成果物が既定で表示されます。ここでは、**Developers** グループを追加します。**[無料評価版]** をクリックし、**[表示]** タブを選択します。

> API Management では、開発者に成果物の表示を許可するかどうかが、グループを使用して管理されます。成果物の表示の可否はグループに対して付与されます。開発者は、自分が所属するグループから見える成果物を表示してサブスクライブすることができます。詳細については、「[How to create and use groups in Azure API Management (Azure API Management でグループを作成して使用する方法)][How to create and use groups in Azure API Management (Azure API Management でグループを作成して使用する方法)]」を参照してください。

![Add developers group][Add developers group]

**[Developers]** グループのチェック ボックスをオンにし、**[保存]** をクリックします。

## <a name="add-api"> </a>成果物への API の追加

このチュートリアル ステップでは、新しい無料評価版の成果物に Echo API を追加します。

> それぞれの API Management サービス インスタンスには、Echo API があらかじめ構成されています。API Management を体験、学習する目的で使用することができます。詳細については、「[Azure API Management の使用][Azure API Management の使用]」を参照してください。

成果物を構成するには、左側の **[API Management]** メニューにある **[成果物]** をクリックし、**[無料評価版]** をクリックします。

![Configure product][Configure product]

**[成果物への API の追加]** をクリックします。

![Add API to product][Add API to product]

**[Echo API]** のチェック ボックスをオンにし、**[保存]** をクリックします。

![Add Echo API][Add Echo API]

## <a name="policies"> </a>呼び出しレート制限ポリシーとクォータ ポリシーの構成

レート制限とクォータは、ポリシー エディターで構成します。左側の **[API Management]** メニューの **[ポリシー]** をクリックし、**[ポリシーの対象となる成果物]** ボックスの一覧の **[無料評価版]** を選択します。

![Product policy][Product policy]

**[ポリシーの追加]** をクリックしてポリシー テンプレートをインポートし、レート制限とクォータ ポリシーの作成を開始します。

![Add policy][Add policy]

ポリシーを挿入し、ポリシー テンプレートの **inbound** または **outbound** セクションにカーソルを置きます。レート制限ポリシーとクォータ ポリシーは inbound ポリシーなので、カーソルを inbound 要素に置きます。

![Policy editor][Policy editor]

このチュートリアルでは、**[呼び出しレート制限]** と **[使用量クォータの設定]** という 2 つのポリシーを追加します。

![Policy statements][Policy statements]

**inbound** ポリシー要素にカーソルを置いたら、**[呼び出しレート制限]** の横の矢印をクリックしてそのポリシー テンプレートを挿入します。

    <rate-limit calls="number" renewal-period="seconds">
    <api name="name" calls="number">
    <operation name="name" calls="number" />
    </api>
    </rate-limit>

**[呼び出しレート制限]** は、成果物レベルのほか、API レベルや個々の操作名レベルで使用することもできます。このチュートリアルで使用するのは、成果物レベルのポリシーだけです。**api** 要素と **operation** 要素は **rate-limit** 要素から削除してください。その例を次に示します。

    <rate-limit calls="number" renewal-period="seconds">
    </rate-limit>

この**無料評価版**成果物で許容される呼び出しレートは 1 分間に最大 10 回です。calls 属性の値に「**10**」、**renewal-period** 属性には「**60**」と入力します。

    <rate-limit calls="10" renewal-period="60">
    </rate-limit>

**[使用量クォータの設定]** ポリシーを構成するには、**inbound** 要素内に新しく追加した **rate-limit** 要素のすぐ下にカーソルを置き、**[使用量クォータの設定]** の左側の矢印をクリックします。

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="name" calls="number" bandwidth="kilobytes">
    <operation name="name" calls="number" bandwidth="kilobytes" />
    </api>
    </quota>

このポリシーも成果物レベルで適用するためのものなので、**api** と **operation** の name 要素は削除してください。その例を次に示します。

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    </quota>

クォータは、一定時間あたりの呼び出し回数、帯域幅、またはその両方を基準にすることができます。このチュートリアルでは、帯域幅に基づく帯域幅調整は行いません。**bandwidth** 属性は削除してください。

    <quota calls="number" renewal-period="seconds">
    </quota>

この**無料評価版**の成果物には、呼び出し回数を 1 週間あたり 200 回とするクォータを割り当てます。calls 属性の値として「**200**」を指定し、renewal-period の値には「**604800**」を指定してください。

    <quota calls="200" bandwidth="kilobytes" renewal-period="604800">
    </quota>

> ポリシーの間隔は秒単位で指定します。1 週間の秒数は、日数 (7) に 1 日の時間数 (24)、1 時間 (60) の分数、1 分間の秒数 (60) を掛けて求めることができます (7 * 24 * 60 * 60 = 604800)。

完成したポリシーは、次のようになります。

    <policies>
        <inbound>
            <rate-limit calls="10" renewal-period="60">
            </rate-limit>
            <quota calls="200" renewal-period="604800">
            </quota>
            <base />
        
    </inbound>
    <outbound>
        
        <base />
        
        </outbound>
    </policies>

必要なポリシーが完成したら、**[保存]** をクリックします。

![Save policy][Save policy]

## <a name="publish-product"> </a> 成果物の発行

API を追加し、ポリシーを構成したら、準備は完了です。その成果物は、開発者が使用できる状態にあります。成果物を発行すると、開発者がそれを利用できるようになります。成果物を構成するには、左側の **[API Management]** メニューにある **[成果物]** をクリックし、**[無料評価版]** をクリックします。

![Configure product][Configure product]

**[発行]** をクリックし、**[はい。発行します]** をクリックして確定します。

![Publish product][Publish product]

## <a name="subscribe-account"> </a>成果物のサブスクリプションを開発者アカウントに追加する

発行された成果物は、開発者がサブスクライブして使用できます。

> API Management インスタンスの管理者には、すべての成果物に対するサブスクリプションが自動的に設定されます。このチュートリアル ステップでは、いずれかの開発者アカウント (管理者以外) に、無料評価版の成果物のサブスクリプションを追加します。ご利用の開発者アカウントが Administrators ロールに属し、既にサブスクリプションが存在する場合でも、この手順に従ってかまいません。

左側の **[API Management]** メニューにある **[開発者]** をクリックし、目的の開発者アカウントの名前をクリックします。この例では、**Clayton Gragg** という開発者アカウントを使用します。

![Configure developer][Configure developer]

**[サブスクリプションの追加]** をクリックします。

![Add subscription][Add subscription]

**[無料評価版]** の横のチェック ボックスをオンにし、**[サブスクライブ]** をクリックします。

![Add subscription][1]

## <a name="test-rate-limit"> </a>操作の呼び出しとレート制限のテスト

無料評価版成果物の構成と発行は以上で完了です。今度は、いくつかの操作を呼び出して、レート制限ポリシーをテストしてみましょう。右上のメニューにある **[開発者ポータル]** をクリックして開発者ポータルに切り替えてください。

![開発者ポータル][開発者ポータル]

上部のメニューで **[API]** をクリックし、**[Echo API]** を選択します。

![開発者ポータル][2]

> アカウントに対して構成されている (またはアカウントから見える) API が 1 つしかない場合、[API] をクリックすると、その API の操作に直接誘導されます。

**[GET Resource]** 操作を選択し、**[コンソールを開く]** をクリックします。

![Open console][Open console]

既定のパラメーターの値はそのままにし、対象となる**無料評価版**の成果物のサブスクリプション キーを選択します。

![Subscription key][Subscription key]

> 複数のサブスクリプションがある場合は、必ず、**[無料評価版]** のキーを選択してください。そうしないと、これまでの手順で構成したポリシーが有効になりません。

**[HTTP Get]** をクリックして応答を確認します。**[応答のステータス]** が "**200 OK**" と表示されることに注目してください。

![Operation results][Operation results]

呼び出しレート制限ポリシー (1 分間に 10 回) を超える頻度で **[HTTP Get]** をクリックします。レート制限ポリシーを超えると、応答ステータスとして "**429 要求が多すぎます**" が返されます。

![Operation results][3]

**[応答ヘッダー]** と **[応答コンテンツ]** は、再試行が可能になるまでの残り時間を示します。

1 分間に 10 回という呼び出しレート制限ポリシーが有効であるとき、レート制限超過となった連続 10 回の呼び出しの 1 回目から 60 秒が経過するまで、その成果物に対する以降の呼び出しはエラーになります。この例でいうと、残り時間は 43 秒です。

## <a name="next-steps"> </a>次のステップ

-   「[Azure API Management の詳細な構成について][Azure API Management の詳細な構成について]」チュートリアルにあるその他のトピックもチェックしてください。

  [成果物の作成]: #create-product
  [成果物への API の追加]: #add-api
  [呼び出しレート制限ポリシーとクォータ ポリシーの構成]: #policies
  [成果物の発行]: #publish-product
  [成果物のサブスクリプションを開発者アカウントに追加する]: #subscribe-account
  [操作の呼び出しとレート制限のテスト]: #test-rate-limit
  [次のステップ]: #next-steps
  [Azure API Management の使用]: ../api-management-get-started
  [API Management インスタンスの作成]: ../api-management-get-started/#create-service-instance
  [API Management console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
  [Add product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
  [Add new product]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
  [Product added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
  [How to create and use groups in Azure API Management (Azure API Management でグループを作成して使用する方法)]: ../api-management-howto-create-groups
  [Add developers group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
  [Configure product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
  [Add API to product]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
  [Add Echo API]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
  [Product policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
  [Add policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
  [Policy editor]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
  [Policy statements]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
  [Save policy]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
  [Publish product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
  [Configure developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
  [Add subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
  [1]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
  [開発者ポータル]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
  [2]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
  [Open console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
  [Subscription key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
  [Operation results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
  [3]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
  [Azure API Management の詳細な構成について]: ../api-management-get-started-advanced

<!--HONumber=46--> 
 