---
title: Azure Percept DK を設定する
description: 開発キットを Azure に接続して、最初の AI モデルをデプロイする
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/15/2021
ms.custom: template-quickstart
ms.openlocfilehash: 3540204d66bb589c567514f92a9a8acb2159e343
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664319"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Azure Percept DK を設定し、最初の AI モデルをデプロイする

Azure Percept DK と Azure Percept Studio の使用を開始するには、Azure Percept DK セットアップ エクスペリエンスを使用して、デバイスを Azure に接続し、最初の AI モデルをデプロイします。 Azure アカウントが Azure Percept Studio に対応していることを確認したら、セットアップ エクスペリエンスを完了して、Edge AI の概念実証を作成するように Azure Percept DK を構成するようにします。

このクイック スタート中に問題が発生した場合は、[トラブルシューティング](./troubleshoot-dev-kit.md) ガイドを参照して、考えられる解決方法がないか調べてください。

## <a name="prerequisites"></a>前提条件

- Azure Percept DK。
- Wi-Fi 機能および Web ブラウザーを備えた Windows、Linux、OS X ベースのホスト コンピューター。
- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成する](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure アカウントには、サブスクリプションの "所有者" または "共同作成者" ロールが必要です。 Azure ロールの定義についての詳細情報

### <a name="prerequisite-check"></a>前提条件のチェック

Azure アカウントがサブスクリプションの "所有者" または "共同作成者" であるかどうかを確認するには、次の手順に従ってください。

1. Azure portal にアクセスし、Azure Percept Studio で使用するのと同じ Azure アカウントでログインします。 

    > [!NOTE]
    > 複数の Azure アカウントがある場合、ブラウザーでは別のアカウントの資格情報がキャッシュされることがあります。 正しいアカウントでサインインしていることを確認する方法の詳細については、トラブルシューティング ガイドを参照してください。

1. 画面の左上隅にあるメイン メニューを展開して [サブスクリプション] をクリックするか、ホーム ページのアイコンのメニューで [サブスクリプション] を選択します。 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-01-subscription.png" alt-text="supscription icon in Azure portal.":::
    --->
1. 一覧からご利用のサブスクリプションを選択します。 一覧にサブスクリプションが表示されない場合は、正しい Azure アカウントでサインインしているかどうかを確認してください。 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-02-sub-list.png" alt-text="supscription list in Azure portal.":::
    --->
新しいサブスクリプションを作成するには、[これらの手順](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription)に従います。

1. サブスクリプション メニューで、[アクセス制御 (IAM)] を選択します
1. [View my Access]\(アクセス権の表示\) ボタンをクリックします
1. ロールをチェックします
    - "閲覧者" の役割が表示されている場合、またはロールを表示するアクセス許可がないというメッセージが表示される場合は、組織で必要なプロセスに従ってアカウント ロールを昇格させる必要があります。
    - ロールが "所有者" または "共同作成者" と表示されている場合、アカウントは Azure Percept Studio で動作します。 

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Azure Percept DK セットアップ エクスペリエンスを開始する

<!---
> [!NOTE]
> Connecting over ethernet? See [this how-to guide](<link needed>) for detailed instructions.
--->
1. ホスト コンピューターを開発キットの Wi-Fi アクセス ポイントに直接接続します。 これは、他の Wi-Fi ネットワークに接続するのと同じように行います。
    - **ネットワーク名**: scz-xxxx ("xxxx" は開発キットの MAC ネットワーク アドレスの下 4 桁)
    - **パスワード**: 開発キットに付属のウェルカム カードに記載されています

    > [!WARNING]
    > Azure Percept DK Wi-Fi のアクセス ポイントに接続している間は、ホスト コンピューターのインターネット接続が一時的に失われます。 アクティブなビデオ会議通話や Web ストリーミングといったネットワークベースのエクスペリエンスは、Azure Percept DK のセットアップ エクスペリエンスの手順 3. が完了するまで中断されます。

1. 開発キットの Wi-Fi アクセス ポイントに接続すると、ホスト デバイスでは Azure Percept DK セットアップ エクスペリエンスが新しいブラウザー ウィンドウに自動的に起動されます。 自動的に開かない場合、ブラウザー ウィンドウを開いて [http://10.1.1.1](http://10.1.1.1) に移動することによって、手動で起動できます。 

1. Azure Percept セットアップ エクスペリエンスが開始されたら、セットアップ エクスペリエンスを進めることができます。 

    > [!NOTE]
    > Azure Percept DK セットアップ エクスペリエンスの Web サービスは、30 分間使用されなくなったとき、およびセットアップ エクスペリエンスが完了したときにシャットダウンされます。 このような場合は、開発キットの Wi-Fi アクセス ポイントでの接続の問題を回避するために、開発キットを再起動することをお勧めします。

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Azure Percept DK セットアップ エクスペリエンスを完了する

1. はじめに - ようこそ画面で、 **[次へ]** をクリックします。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="ウェルカム ページ。":::

1. Wi-Fi に接続する - [ネットワーク接続] ページで、 **[新しい Wi-Fi ネットワークに接続する]** をクリックして開発キットを Wi-Fi ネットワークに接続します。

    この開発キットを Wi-Fi ネットワークに既に接続している場合、または Wi-Fi ネットワーク経由で Azure Percept DK のセットアップ エクスペリエンスに既に接続している場合は、 **[スキップ]** リンクをクリックします。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-02-connect-to-wi-fi.png" alt-text="Wi-Fi に接続します。":::

1. 使用可能な接続から Wi-Fi ネットワークを選択して接続します。 (ローカルの Wi-Fi パスワードが必要です)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-03-select-wi-fi.png" alt-text="Wi-Fi ネットワークを選択します。"::: 

1. IP アドレスをコピーする - 開発キットが任意のネットワークに正常に接続されたら、ページに表示される **IPv4 アドレス** を書き留めます。 **この IP アドレスは、このクイック スタート ガイドの後半で必要になります**。 

    > [!NOTE]
    > この IP アドレスは、デバイスを起動するたびに変わる可能性があります。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="IP アドレスをコピーします。":::

1. 使用許諾契約書を確認して同意する - 使用許諾契約書を読み、 **[使用許諾契約書を読んで同意しました]** を選択し (契約の一番下までスクロールする必要があります)、 **[次へ]** をクリックします。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="EULA に同意します。":::

1. SSH ログイン アカウントを作成する - 開発キットへのリモート アクセス用の SSH を設定します。 SSH のユーザー名とパスワードを作成します。 

    > [!NOTE]
    > SSH (Secure Shell) は、ホスト デバイスと開発キットの間の通信をセキュリティで保護するために使用されるネットワーク プロトコルです。 SSH の詳細については、[こちらの記事](https://en.wikipedia.org/wiki/SSH_(Secure_Shell))を参照してください。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-06-ssh-login.png" alt-text="SSH アカウントを作成します。"::: 

1. 開発キットの接続プロセスを開始する - 次の画面で **[Connect with a new device]\(新しいデバイスと接続する\)** をクリックして、開発キットを Azure IoT Hub に接続するプロセスを開始します。 

    <!---
    Connecting with an existing IoT Edge device connection string? See this [how-to guide](<link needed>) for reference.
    --->
    :::image type="content" source="./media/quickstart-percept-dk-setup/main-07-connect-device.png" alt-text="Azure に接続します。"::: 

1. デバイス コードをコピーする - **[コピー]** リンクをクリックして、デバイス コードをコピーします。 次に、 **[Azure にログイン]** をクリックします。 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="デバイス コードをコピーします。"::: 

1. デバイス コードを貼り付ける - 新しいブラウザー タブが開き、コピーしたデバイス コードを要求するウィンドウが表示されます。 ウィンドウにコードを貼り付け、 **[次へ]** をクリックします。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="デバイス コードを入力します。"::: 

1. Azure にサインインする - 次のウィンドウでは、クイック スタートの開始時に確認した Azure アカウントでサインインする必要があります。 これらのログイン資格情報を入力し、 **[次へ]** をクリックします。 

    > [!NOTE]
    > ブラウザーによって、他の資格情報が自動的にキャッシュされる場合もあります。 正しいアカウントでサインインしていることを再確認してください。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-10-azure-sign-in.png" alt-text="Azure にサインインします。":::
 
1. このステップでセットアップ エクスペリエンスのブラウザー タブを閉じないでください。サインインすると、サインインしたことを示す画面が表示されます。 ウィンドウを閉じてもかまわないと表示されますが、**ウィンドウを閉じないようにすることをお勧めします**。 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-11-sign-in-success.png" alt-text="サインインの成功。"::: 

1. セットアップ エクスペリエンスをホストしているブラウザー タブに戻ります。
1. IoT Hub オプションを選択します
    - 既に IoT Hub があり、このページの一覧に表示されている場合は、それを選択して **ステップ 17 に進む** ことができます。
    - IoT Hub がない場合、または新しいものを作成する場合は、一覧の下部に移動して、 **[Create a new Azure IoT Hub]\(新しい Azure IoT Hub を作成する\)** をクリックします。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="IoT Hub の選択を選択します。"::: 

1. 新しい IoT Hub を作成する - このページのすべてのフィールドに入力します。 
    - Azure Percept Studio で使用する Azure サブスクリプションを選択します
    - 既存のリソース グループを選択します。 存在しない場合は、[新規作成] をクリックし、画面の指示に従います。 
    - Azure リージョンを選択します。 
    - 新しい IoT Hub 名を付けます
    - 価格レベルを選択します (通常はサブスクリプションに合わせます)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-14-create-iot-hub.png" alt-text="新しい IoT Hub を作成します。"::: 

1. IoT Hub がデプロイされるまで待ちます。数分かかる場合があります

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-15-iot-hub-deploy.png" alt-text="IoT Hub をデプロイします。"::: 

1. 開発キットを登録する - デプロイが完了したら、 **[登録]** ボタンをクリックします

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="IoT Hub が正常にデプロイされました。"::: 

1. 開発キットの名前を指定する - 開発キットのデバイス名を入力し、 **[次へ]** をクリックします。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-17-device-name.png" alt-text="デバイスに名前を付けます。"::: 

1. 既定の AI モデルがダウンロードされるまで待ちます。これには数分かかります

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="セットアップの終了処理を行います。"::: 

1. 動作中のビジョン AI を確認する - 開発キットは Azure IoT Hub に正常にリンクされ、既定のビジョン AI 物体検出モデルを受け取りました。 Azure Percept ビジョン カメラでは、クラウドに接続しなくても物体検出を推論できるようになりました。 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-2-preview-video-output.png" alt-text="[ビデオ出力のプレビュー] をクリックします。"::: 
       
    > [!NOTE]
    > オンボード プロセスと、ホスト コンピューターへのデバイスの Wi-Fi アクセスの接続は、この時点でシャットダウンされますが、開発キットはインターネットに接続されたままになります。   開発キットを再起動してオンボード エクスペリエンスを再起動することができます。これにより、オンボードを元に戻し、同じまたは別の Azure サブスクリプションに関連付けられている別の IOT ハブにデバイスを再接続することができます。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="セットアップ エクスペリエンスの切断に関する警告。"::: 

1. Azure ポータルに進む - セットアップ エクスペリエンス ウィンドウに戻り、 **[Azure portal に進む]** ボタンをクリックして、Azure Percept Studio でカスタム AI モデルの作成を開始します。

    > [!NOTE]
    > ホスト コンピューターが Wi-Fi 設定の開発キット アクセス ポイントに接続されていないこと、およびローカル Wi-Fi に再接続されていることを確認します。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Azure Percept Studio に移動します。"::: 

## <a name="view-your-device-in-the-azure-percept-studio-and-deploy-common-prebuilt-sample-apps"></a>Azure Percept Studio でデバイスを表示し、一般的な事前構築済みサンプル アプリをデプロイする

1. Azure Percept の [概要] ページからデバイスの一覧を表示します。 Azure Percept の [概要] ページは、初歩および高度の両方の AI エッジ モデルとソリューション開発のための、多くのさまざまなワークフローにアクセスするための開始点となります

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="デバイス一覧を表示します。":::
    
1. 先ほど作成したデバイスが接続されていることを確認し、それをクリックします。

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="デバイスを選択します。":::

1. デバイス ストリームを表示し、開発キット カメラに映っている内容を確認します。 既定の物体検出モデルが追加設定なしでデプロイされ、これによっていくつかの一般的な物体が検出されます。

    > [!NOTE]
    > 新しいデバイスで初めてこれを行うと、新しいモジュールがデプロイされていることを示す通知が右上隅に表示されます。  これが完了すると、カメラのビデオ ストリームを使用したウィンドウを起動できるようになります。 
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="ビデオ ストリームを表示します。":::
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="物体検出を確認します。":::

1. 事前構築済みのサンプル AI モデルについて説明します。   Azure Precept Studio には、1 回のクリックでデプロイできる、事前構築済みのいくつかの一般的なサンプルが用意されています。   これらを表示およびデプロイするには、[サンプル モデルのデプロイ] を選択します。

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="事前構築済みモデルについて説明します。":::
    
1. 接続されているデバイスに、新しい事前構築済みのサンプルをデプロイします。 ライブラリからサンプルを選択し、[デバイスへのデプロイ] をクリックします

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-1-select-prebuilt.png" alt-text="事前構築済みのものを選択します。":::
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="動作中の物体検出を確認します。":::

## <a name="next-steps"></a>次のステップ

同様のフローに従って、[事前構築済み音声モデル](./tutorial-no-code-speech.md)を試すことができます。