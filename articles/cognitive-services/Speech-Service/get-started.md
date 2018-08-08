---
title: Speech Service を無料で試す
description: Speech Service を無料で試す方法について説明します。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/17/2018
ms.author: v-jerkin
ms.openlocfilehash: ff9a258d42f25ded82545909cdeade119548148e
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325209"
---
# <a name="try-the-speech-service-for-free"></a>Speech Service を無料で試す

Speech Service は、低コストで簡単に使い始めることができます。 30 日間の無料試用版でサービスの機能を確認し、アプリケーションのニーズに適しているかどうかを判断できます。

さらに多くの時間が必要な場合は、Microsoft Azure アカウントにサインアップします。Azure アカウントには、最大 30 日間、有料の Speech Service サブスクリプションに充当できる 200 ドルのサービス クレジットが付属しています。

最後に、Speech Service では、アプリケーションの開発に適した無料の低ボリューム レベルが提供されます。 サービス クレジットの有効期限が切れた後も、この無料サブスクリプションを保持できます。

## <a name="free-trial"></a>無料試用版

30 日間の無料試用版では、限られた期間、S0 Standard 価格レベルにアクセスできます。 30 日間の無料試用版にサインアップするには、次の手順に従います。

1. 「[Cognitive Services を試す](https://azure.microsoft.com/try/cognitive-services/)」ページに移動します。

1. Speech タブに切り替え、[Speech Services] の横にある **[API キーの取得]** をクリックします。

   ![Speech Services タブ](media/index/try-speech-api-free-trial1.png)<br>
   ![API キー](media/index/try-speech-api-free-trial2.png)

3. 使用条件に同意し、ドロップダウン メニューからロケールを選択します。

   ![使用条件に同意する](media/index/try-speech-api-free-trial3.png)

4. Microsoft、Facebook、LinkedIn、または GitHub アカウントを使用してサインインします。 または、無料の Microsoft アカウントにサインアップすることもできます。

    * [Microsoft アカウント ポータル](https://account.microsoft.com/account)に移動します。
    * **[Microsoft アカウントでサインイン]** をクリックします。

    ![[サインイン]](media/index/try-speech-api-free-trial4.png)

    * サインインを求められたら、[作成できます] をクリックします。

    ![新しいアカウントを作成します](media/index/try-speech-api-free-trial5.png)

    * 以降の手順では、電子メール アドレスまたは電話番号を入力し、パスワードを割り当て、手順に従って新しい Microsoft アカウントを確認します。

サインインすると、無料試用版が開始されます。 表示される Web ページには、現在の試用版サブスクリプションの対象となるすべての Cognitive Services が一覧表示されます。 [Speech Services] の横に、2 つのサブスクリプション キーが表示されます。 アプリケーションでどちらのキーを使用しても構いません。

> [!NOTE]
> 無料試用版サブスクリプションはすべて米国西部リージョンにあります。 要求を行うときは、現在のリージョンに対応するエンドポイントを必ず使用してください。

## <a name="new-azure-account"></a>新しい Azure アカウント

新しい Azure アカウントでは、最大 30 日間の 200 ドルのサービス クレジットが提供されます。 このクレジットを使用して、Speech Service をさらに探索したり、アプリケーション開発を開始したりできます。

新しい Azure アカウントにサインアップするには、次の手順に従います。

1. [Azure サインアップのページ](https://azure.microsoft.com/free/ai/)に移動します。 

1. **[無料で始める]** をクリックします。

    ![無料で始める](media/index/try-speech-api-new-azure1.png)

3. Microsoft アカウントでサインインします。 アカウントがない場合は、次の手順に従います。

    * [Microsoft アカウント ポータル](https://account.microsoft.com/account)に移動します。
    * **[Microsoft アカウントでサインイン]** をクリックします。
    * サインインを求められたら、[作成できます] をクリックします。
    * 以降の手順では、電子メール アドレスまたは電話番号を入力し、パスワードを割り当て、手順に従って新しい Microsoft アカウントを確認します。

1. アカウントにサインアップするために必要な残りの情報を入力します。 国と氏名を指定し、電話番号と電子メール アドレスを入力します。

    ![情報を入力する](media/index/try-speech-api-new-azure2.png)

    電話番号とクレジット カード番号による本人確認を行った後、Azure ユーザー契約に同意します  (クレジット カードには課金されません)。

    ![契約に同意する](media/index/try-speech-api-new-azure3.png)

無料の Azure アカウントが作成されます。 次のセクションの手順に従って、Speech Service のサブスクリプションを開始します。

## <a name="create-a-speech-resource-in-azure"></a>Azure で Speech リソースを作成する

Speech Service リソースを Azure アカウントに追加するには、次の手順に従います。

1. Microsoft アカウントを使用して、[Azure portal](https://ms.portal.azure.com/) にログインします。

1. ポータルの左上にある **[リソースの作成]** (緑色の **+** アイコン) をクリックします。

    ![リソースを作成する](media/index/try-speech-api-create-speech1.png)

1. 新しいウィンドウで、Speech を検索します。

    ![Speech をクリックする](media/index/try-speech-api-create-speech2.png)

1. 検索結果で、[Speech (プレビュー)] をクリックします。

1. Speech Service パネルの下部にある **[作成]** をクリックします。

    ![Click Create](media/index/try-speech-api-create-speech3.png)

1. [作成] パネルで、次のように入力します。

    * 新しいリソースの名前。 この名前は、同じサービスの複数のサブスクリプションを区別するのに役立ちます。
    * 新しいリソースが関連付けられている Azure サブスクリプションを選択して、料金の課金方法を決定します。
    * リソースを使用するリージョンを選択します。 現時点では、Speech Service は、東アジア、北ヨーロッパ、米国西部の各リージョンで利用できます。
    * 価格レベルとして、F0 (制限付きの無料サブスクリプション) または S0 (Standard サブスクリプション) を選択します。 **[価格の詳細を表示]** をクリックすると、各レベルの価格と使用量クォータの詳細を確認できます。
    * この Speech サブスクリプションの新しいリソース グループを作成するか、既存のリソース グループに割り当てます。 リソース グループは、さまざまな Azure サブスクリプションを整理しておくのに役立ちます。
    * 今後、サブスクリプションに簡単にアクセスできるように、**[ダッシュボードにピン留めする]** チェック ボックスをオンにします。
    * **[Create (作成)]** をクリックします。

    ![パネルで [作成] をクリックする](media/index/try-speech-api-create-speech4.png)

    新しい Speech リソースを作成して展開するまでに少し時間がかかることがあります。 新しいリソースに関する情報を示すクイック スタート パネルが表示されます。

    ![クイック スタート パネル](media/index/try-speech-api-create-speech5.png)

1. クイック スタート パネルの手順 1 の下の **[キー]** リンクをクリックして、サブスクリプション キーを表示します。 各サブスクリプションには 2 つのキーがあります。アプリケーションでどちらを使用しても構いません。 各キーの横のボタンをクリックして、クリップボードにコピーし、コードに貼り付けます。

> [!NOTE]
> Standard レベルのサブスクリプションは、1 つまたは複数のリージョンにいくつでも作成できます。 ただし、Free レベルのサブスクリプションは 1 つしか作成できません。

## <a name="next-steps"></a>次の手順

10 分間のクイック スタートのいずれかを行うか、SDK のサンプルを確認します。

> [!div class="nextstepaction"]
> [クイック スタート: C# で音声を認識する](quickstart-csharp-dotnet-windows.md)
> [Speech SDK サンプル](speech-sdk.md#get-the-samples)
