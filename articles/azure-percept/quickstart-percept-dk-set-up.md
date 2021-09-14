---
title: Azure Percept DK デバイスを設定する
description: Azure Percept DK を設定し、Azure IoT Hub に接続する
author: WendyAnn0079
ms.author: amiyouss
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/17/2021
ms.custom: template-quickstart
ms.openlocfilehash: 4e37be9e121d634e0d4a83fdd5f786805dedf132
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427212"
---
# <a name="set-up-the-azure-percept-dk-device"></a>Azure Percept DK デバイスを設定する

Azure Percept DK のセットアップ エクスペリエンスを完了して開発キットを構成します。 ご使用の Azure アカウントが Azure Percept に適合していることを確認したら、次の作業を行います。

- Azure Percept DK セットアップ エクスペリエンスを起動する
- 開発キットを Wi-Fi ネットワークに接続する
- 開発キットへのリモート アクセス用の SSH ログインを設定する
- Azure IoT Hub に新しいデバイスを作成する

このプロセスの過程で問題が発生した場合に実行できる解決策については、[セットアップのトラブルシューティング ガイド](./how-to-troubleshoot-setup.md)を参照してください。

> [!NOTE]
> セットアップ エクスペリエンス Web サービスは、30 分間使用されない状態が続くと自動的にシャットダウンされます。 開発キットに接続できない場合や Wi-Fi アクセス ポイントが表示されない場合は、デバイスを再起動してください。

## <a name="prerequisites"></a>前提条件

- Azure Percept DK (開発キット)。
- Wi-Fi 機能と Web ブラウザーを備えた Windows、Linux、OS X ベースのホスト コンピューター。
- 有効な Azure サブスクリプション [アカウントは無料で作成できます。](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- ユーザーには、サブスクリプション内の **所有者** または **共同作成者** ロールが必要です。 Azure アカウントのロールを確認するには、以下の手順に従います。 Azure ロールの定義の詳細については、[Azure ロールベースのアクセス制御に関するドキュメント](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)を参照してください。

    > [!CAUTION]
    > すべてのブラウザー ウィンドウを閉じ、[Azure portal](https://portal.azure.com/) 経由でサブスクリプションにログインしてから、セットアップ エクスペリエンスを開始します。 正しいアカウントでサインインしていることを確認する方法の詳細については、[セットアップのトラブルシューティング ガイド](./how-to-troubleshoot-setup.md)を参照してください。

### <a name="check-your-azure-account-role"></a>Azure アカウント ロールを確認する

Azure アカウントがサブスクリプションにおける "所有者" または "共同作成者" であるかどうかを確認するには、これらの手順に従ってください。

1. [Azure portal](https://portal.azure.com/) にアクセスし、Azure Percept で使用するのと同じ Azure アカウントでログインします。

1. **[サブスクリプション]** アイコン (黄色の鍵の形のアイコン) を選択します。

1. 一覧からご利用のサブスクリプションを選択します。 ご自分のサブスクリプションが表示されない場合は、正しい Azure アカウントでサインインしているかどうかを確認してください。 新しいサブスクリプションを作成するには、[これらの手順](../cost-management-billing/manage/create-subscription.md)に従います。

1. サブスクリプション メニューで、 **[アクセス制御 (IAM)]** を選択します。
1. **[アクセス権の表示]** をクリックします。
1. ロールをチェックします。
    - 自分のロールが **閲覧者** として表示されている場合、またはロールを表示するアクセス許可がないというメッセージが表示される場合は、組織で必要なプロセスに従ってアカウント ロールを昇格させる必要があります。
    - 自分のロールが **所有者** または **共同作成者** として表示される場合は、ご使用のアカウントが Azure Percept に対応しており、セットアップ エクスペリエンスに進むことができます。

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Azure Percept DK セットアップ エクスペリエンスを開始する

1. ホスト コンピューターを開発キットの Wi-Fi アクセス ポイントに接続します。 次のネットワークを選択し、メッセージが表示されたら Wi-Fi のパスワードを入力します。

    - **ネットワーク名**: **scz-xxxx** または **apd-xxxx** (ここで、**xxxx** は、開発キットの MAC アドレスの最後の 4 桁です)
    - **パスワード**: 開発キットに付属のウェルカム カードに記載されています

    > [!WARNING]
    > Azure Percept DK の Wi-Fi アクセス ポイントに接続している間は、ホスト コンピューターのインターネット接続が一時的に失われます。 アクティブなビデオ会議通話や Web ストリーミングといったネットワークベースのエクスペリエンスは中断されます。

1. 開発キットの Wi-Fi アクセス ポイントに接続すると、ホスト コンピューターで自動的に新しいブラウザー ウィンドウのアドレス バーに「**your.new.device/** 」が入力され、セットアップ エクスペリエンスが起動します。 タブが自動的に開かない場合は、Web ブラウザーで [http://10.1.1.1](http://10.1.1.1) に移動してセットアップ エクスペリエンスを起動してください。 Azure Percept で使用する予定の Azure アカウント資格情報でブラウザーがサインインしていることを確認してください。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-welcome.png" alt-text="ウェルカム ページ。":::

    > [!NOTE]
    > **Mac ユーザー** - Mac でセットアップ エクスペリエンスを実行すると、最初にキャプティブ ポータル ウィンドウが開き、セットアップ エクスペリエンスを完了できません。 このウィンドウを閉じ、Web ブラウザーで https://10.1.1.1 を開きます。これにより、セットアップ エクスペリエンスを完了できます。

## <a name="connect-your-dev-kit-to-a-wi-fi-network"></a>開発キットを Wi-Fi ネットワークに接続する

1. **[ようこそ]** 画面で、 **[次へ]** を選択します。

1. **[ネットワーク接続]** ページで **[新しい Wi-Fi ネットワークに接続する]** を選択します。

    既に開発キットを Wi-Fi ネットワークに接続している場合は、 **[スキップ]** を選択します。

1. 使用できるネットワークの一覧から自分の Wi-Fi ネットワークを選択し、 **[接続]** を選択します。 確認を求められたらネットワーク パスワードを入力します。

    > [!NOTE]
    > このネットワークを "優先ネットワーク" として設定する (Mac) か、[自動的に接続する] チェックボックスをオンにする (Windows) ことをお勧めします。  これにより、このプロセス中に接続が中断された場合、ホスト コンピューターは開発キットの Wi-Fi アクセス ポイントに再接続できるようになります。

1. 開発キットが正常に接続されると、開発キットに割り当てられた IPv4 アドレスがページに表示されます。 **ページに表示される IPv4 アドレスを書き留めてください。** トラブルシューティングやデバイスの更新を目的として開発キットに SSH で接続する際、この IP アドレスが必要になります。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-success-wi-fi.png" alt-text="IP アドレスをコピーします。":::

    > [!NOTE]
    > この IP アドレスは、デバイスを起動するたびに変わる可能性があります。

1. 使用許諾契約書を読み (契約の一番下までスクロールする必要があります)、 **[使用許諾契約書を読んで同意しました]** を選択し、 **[次へ]** を選択します。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-eula.png" alt-text="EULA に同意します。":::

## <a name="set-up-an-ssh-login-for-remote-access-to-your-dev-kit"></a>開発キットへのリモート アクセス用の SSH ログインを設定する

1. SSH アカウント名と、公開キーとパスワードを作成し、 **[次へ]** を選択します。

    SSH アカウントを既に作成済みの場合は、この手順をスキップしてください。

    **後で使用するために、ログイン情報を書き留めておいてください**。

    > [!NOTE]
    > SSH (Secure Shell) は、ホスト コンピューターを介してリモートで開発キットに接続することを可能にするネットワーク プロトコルです。

##  <a name="create-a-new-device-in-azure-iot-hub"></a>Azure IoT Hub に新しいデバイスを作成する

1. **[新しいデバイスとしてセットアップ]** を選択して、Azure アカウント内に新しいデバイスを作成します。

    これで、デバイス コードが Azure から取得されます。

1. **[コピー]** を選択します。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-copy-code.png" alt-text="デバイス コードをコピーします。":::

    > [!NOTE]
    > 次の手順でデバイス コードを使用しているときにエラーが発生した場合、またはデバイス コードが表示されない場合は、[トラブルシューティングの手順](./how-to-troubleshoot-setup.md)で詳細を確認してください。 

1. **[Azure にログイン]** を選択します。

1. 新しいブラウザー タブが開いて、 **[コードの入力]** というウィンドウが表示されます。 ウィンドウにコードを貼り付け、 **[次へ]** を選択します。 セットアップ エクスペリエンスがある **[ようこそ]** タブは閉じないでください。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-enter-code.png" alt-text="デバイス コードを入力します。":::

1. 開発キットで使用する Azure アカウントの資格情報を使用して Azure Percept にサインインします。 完了してもブラウザー タブは開いたままにしておいてください。

    > [!CAUTION]
    > ブラウザーによって、他の資格情報が自動的にキャッシュされる場合もあります。 正しいアカウントでサインインしていることを再確認してください。

    デバイス上で Azure Percept に正常にサインインしたら、 **[許可]** を選択します。 
    
    **[ようこそ]** タブに戻り、セットアップ エクスペリエンスを続行します。

1. **[ようこそ]** タブに **[デバイスを Azure IoT ハブに割り当てる]** ページが表示されたら、次のいずれかの操作を実行します。

    - このページに IoT Hub がリストされている場合は、「**Azure IoT Hub を選択します**」に進んでください。
    - IoT ハブがない場合、または新しいものを作成する場合は、 **[新しい Azure IoT ハブを作成する]** を選択します。

    > [!IMPORTANT]
    > IoT ハブはあるものの、一覧に表示されていない場合、間違った資格情報で Azure Percept にサインインしている可能性があります。 ヘルプについては、[セットアップのトラブルシューティング ガイド](./how-to-troubleshoot-setup.md)を参照してください。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-iot-hub-select.png" alt-text="IoT Hub の選択を選択します。":::

1. 新しい IoT Hub を作成するには、次のようにします。

    - Azure Percept で使用する Azure サブスクリプションを選択します。
    - 既存のリソース グループを選択します。 存在しない場合は、 **[新規作成]** を選択し、画面の指示に従います。
    - 物理的に最も近い Azure リージョンを選択します。
    - 新しい IoT ハブに名前を付けます。
    - **S1 (Standard) 価格レベル** を選択します。

    > [!NOTE]
    > IoT Hub のデプロイが完了するまで数分かかる場合があります。 エッジ AI アプリケーションの[メッセージ スループット](../iot-hub/iot-hub-scaling.md#message-throughput)を引き上げる必要が生じた場合は、Azure portal でいつでも [IoT Hub を上位の Standard レベルにアップグレード](../iot-hub/iot-hub-upgrade.md)できます。 B および F レベルでは、Azure Percept はサポートされません。

1. デプロイが完了したら、 **[登録]** を選択します。

1. Azure IoT Hub を選択します

1. 開発キットのデバイス名を入力し、 **[次へ]** を選択します。

1. これで、デバイス モジュールがデバイスにデプロイされます。 これには数分かかることがあります。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-finalize.png" alt-text="セットアップの終了処理を行います。":::

1. **デバイスのセットアップが完了しました**。 開発キットが IoT Hub に正常にリンクされ、すべてのモジュールがデプロイされました。

    > [!NOTE]
    > 完了すると、開発キットの Wi-Fi アクセス ポイントが自動的に切断され、セットアップ エクスペリエンス Web サービスが終了されて、2 つの通知が生成されます。

    > [!NOTE]
    > このセットアップ プロセスの一環として構成される IoT Edge コンテナーでは、90 日後に期限切れになる証明書を使用します。 証明書は、IoT Edge を再起動することによって自動的に再生成できます。 詳細については、「[IoT Edge デバイスで証明書を管理する](../iot-edge/how-to-manage-device-certificates.md)」を参照してください。

1. 開発キットを接続した Wi-Fi ネットワークにホスト コンピューターを接続します。

1. **[Azure portal に進む]** を選択します。

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-Azure-portal-continue.png" alt-text="Azure Percept Studio に移動します。":::

### <a name="video-walk-through"></a>ビデオ チュートリアル 
前述の手順を視覚的に確認するには、次の動画をご覧ください。
> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>次のステップ

開発キットが設定されたので、次はビジョン AI の動作を確認します。
- [開発キットのビデオ ストリームを表示する](./how-to-view-video-stream.md)
- [ビジョン AI モデルを開発キットにデプロイする](./how-to-deploy-model.md)
