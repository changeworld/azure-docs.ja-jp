---
title: インクルード ファイル
description: インクルード ファイル
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/21/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: ef2b98821b28d8a49e5f16bf1c6ac176eb8b5793
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74407259"
---
## <a name="generate-the-certificate-signing-request-file"></a>証明書の署名要求ファイルを生成する

Apple Push Notification Service (APNs) では、証明書を使用してプッシュ通知を認証します。 次の手順に従って、通知を送受信するために必要なプッシュ証明書を作成します。 これらの概念の詳細については、[Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) の公式ドキュメントを参照してください。

証明書署名要求 (CSR) ファイルを生成します。これは、Apple が署名済みのプッシュ証明書を生成するために使用します。

1. Mac で、キーチェーン アクセス ツールを実行します。 これは、Launchpad の**ユーティリティ** フォルダーまたは**その他**フォルダーから開くことができます。

1. **[キーチェーン アクセス]** を選択し、 **[証明書アシスタント]** を展開して、 **[認証局に証明書を要求]** を選択します。

    ![Keychain Access を使用して新しい証明書を要求する](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

1. **[ユーザーのメール アドレス]** を選択し、 **[通称]** の値を入力します。 **[ディスクに保存]** を指定したことを確認してから、 **[続ける]** を選択します。 **[CA のメール アドレス]** は、必要がないため空白のままにします。

    ![必要な証明書情報](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. **[名前]** に CSR ファイルの名前を入力し、 **[場所]** で保存先を選択してから、 **[保存]** を選択します。

    ![証明書のファイル名を選択する](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    このアクションにより、選択した場所に CSR ファイルが保存されます。 既定の場所は **[デスクトップ]** です。 ファイル用に選択した場所を忘れないでください。

次に、アプリを Apple に登録し、プッシュ通知を有効にして、エクスポートした CSR をアップロードしてプッシュ証明書を作成します。

## <a name="register-your-app-for-push-notifications"></a>アプリケーションをプッシュ通知に登録する

プッシュ通知を iOS アプリに送信するには、アプリケーションを Apple に登録すると共に、プッシュ通知にも登録します。  

1. ご自分のアプリをまだ登録していない場合は、Apple Developer Center の [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) に移動します。 自分の Apple ID を使用してポータルにサインインし、 **[Identifiers]\(識別子\)** を選択します。 次に、 **[+]** を選択して、新しいアプリを登録します。

    ![iOS Provisioning Portal の [App IDs]\(アプリ ID\) ページ](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. **[Register a New Identifier]\(新しい識別子の登録\)** 画面で、 **[App IDs]\(アプリ ID\)** を選択します。 その後 **[続行]** を選択します。

    ![iOS Provisioning Portal の ID の新規登録ページ](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. 新しいアプリで次の 3 つの値を更新してから、 **[Continue]\(続行\)** を選択します。

   * **説明**:アプリのわかりやすい名前を入力します。

   * **[Bundle ID]\(バンドル ID\)** : [アプリ ディストリビューション ガイド](https://help.apple.com/xcode/mac/current/#/dev91fe7130a)の説明のとおりに、 **<組織 ID>.<製品名>** の形式のバンドル ID を入力します。 <*組織 ID*> と <*製品名*> の値は Xcode プロジェクトを作成する際に使用する組織 ID と製品名に一致させる必要があります。 次のスクリーンショットでは、**NotificationHubs** という値が組織 ID として使用され、**GetStarted** という値が製品名として使用されています。 Xcode で正しい発行プロファイルが使用されるように、 **[Bundle Identifier]\(バンドル識別子\)** の値はご自分の Xcode プロジェクトの値と一致させるようにしてください。

      ![iOS Provisioning Portal のアプリ ID の登録ページ](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **[Push Notifications]\(プッシュ通知\)** : **[Capabilities]\(機能\)** セクションの **[Push Notifications]\(プッシュ通知\)** オプションをオンにします。

      ![新しいアプリ ID を登録するフォーム](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      このアクションにより、アプリ ID が生成され、その情報を確認するよう求められます。 **[Continue]\(続行\)** を選択し、 **[Register]\(登録\)** を選択して新しいアプリ ID を確認します。

      ![新しいアプリ ID を確認する](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      **[Register]\(登録\)** を選択すると、新しいアプリ ID が **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** ページに 1 行の項目として表示されます。

4. **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** ページの **[Identifiers]\(識別子\)** で、先ほど作成したアプリ ID の行項目を探し、その行を選択すると **[Edit your App ID Configuration]\(App ID 構成の編集\)** 画面が表示されます。

5. チェック マークが付いた **[Push Notifications]\(プッシュ通知\)** オプションまで下へスクロールします。証明書を作成するために **[Configure]\(構成\)** を選択します。

    ![アプリ ID の編集ページ](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

6. **[Apple Push Notification service SSL Certificates]\(Apple Push Notification Service の SSL 証明書\)** ウィンドウが表示されます。 **[Development SSL Certificate]\(開発 SSL 証明書\)** セクションで **[Create Certificate]\(証明書の作成\)** ボタンを選択します。

    ![アプリ ID の証明書の作成ボタン](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    **[Create a new Certificate]\(新しい証明書の作成\)** 画面が表示されます。

    > [!NOTE]
    > このチュートリアルでは開発証明書を使用します。 運用証明書の場合も同じ処理を行います。 通知の送信と同じ証明書の種類を使用するようにします。

1. **[Choose File]\(ファイルの選択\)** を選択して、最初のタスクで CSR ファイルを保存した場所を参照し、証明書名をダブルクリックして読み込みます。 その後 **[続行]** を選択します。

1. ポータルで証明書が作成されたら、 **[Download]\(ダウンロード\)** ボタンを選択します。 この証明書を保存し、この保存場所を覚えておいてください。

    ![生成された証明書のダウンロード ページ](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    証明書がダウンロードされ、自分のコンピューターの**ダウンロード** フォルダーに保存されます。

    ![Downloads フォルダーの証明書ファイルを見つける](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > 既定では、ダウンロードした開発証明書の名前は **aps_development.cer** になっています。

1. ダウンロードしたプッシュ証明書 **aps_development.cer** をダブルクリックします。 このアクションで、以下の図のように、新しい証明書がキーチェーンにインストールされます:

    ![新しい証明書が表示されたキーチェーン アクセス証明書リスト](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > 証明書の名前は場合によって異なりますが、名前の前には **Apple Development iOS Push Services** が付けられます。

1. Keychain Access の **[Certificates]** カテゴリで、作成した新しいプッシュ証明書を右クリックします。 **[書き出す]** を選択し、ファイルに名前を付けて、 **.p12** 形式を選択します。次に、 **[保存]** を選択します。

    ![p12 形式として証明書をエクスポートする](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    パスワードを使用して証明書を保護することもできますが、これはオプションです。 パスワードの作成を省略する場合は、 **[OK]** をクリックします。 エクスポートした .p12 証明書のファイル名と場所を書き留めます。 これらは、APNs での認証を有効にするために使用されます。

    > [!NOTE]
    > 実際の .p12 ファイルの名前と場所は、このチュートリアルの図に示されているものと異なる場合があります。

## <a name="create-a-provisioning-profile-for-the-app"></a>アプリケーションのプロビジョニング プロファイルを作成する

1. [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) に戻り、 **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** を選択します。左側のメニューから **[Profiles]\(プロファイル\)** を選択し、 **[+]** を選択して新しいプロファイルを作成します。 **[Register a New Provisioning Profile]\(新しいプロビジョニング プロファイルの登録\)** 画面が表示されます。

1. **[Development]\(開発\)** で、プロビジョニング プロファイルの種類として **[iOS App Development]\(iOS アプリ開発\)** を選択し、 **[Continue]\(続行\)** を選択します。

    ![プロビジョニング プロファイル リスト](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. 次に、 **[App ID]\(アプリ ID\)** ドロップダウン リストで、自分が作成したアプリ ID を選択し、 **[Continue]\(続行\)** を選択します。

    ![アプリ ID を選択する](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. **[Select certificates]\(証明書の選択\)** ウィンドウで、コード署名に使用する開発証明書を選択し、 **[Continue]\(続行\)** を選択します。 この証明書は、作成したプッシュ証明書ではありません。 存在しない場合は、作成する必要があります。 証明書が存在する場合は、次の手順に進みます。 開発証明書が存在しない場合に証明書を作成するには:

    1. "**No Certificates are available (利用可能な証明書がありません)** " と表示されたら、 **[Create Certificate]\(証明書の作成\)** を選択します。
    2. **[Software]\(ソフトウェア\)** セクションで、 **[Apple Development]\(Apple 開発\)** を選択します。 その後 **[続行]** を選択します。
    3. **[Create a New Certificate]\(新しい証明書の作成\)** 画面で、 **[Choose File]\(ファイルの選択\)** を選択します。
    4. 先ほど作成した**証明書署名要求**証明書を参照して選択し、 **[Open]\(開く\)** を選択します。
    5. **[続行]** をクリックします。
    6. 開発証明書をダウンロードします。この保存場所を覚えておいてください。

1. **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** ページに戻り、左側のメニューから **[Profiles]\(プロファイル\)** を選択し、 **[+]** を選択して新しいプロファイルを作成します。 **[Register a New Provisioning Profile]\(新しいプロビジョニング プロファイルの登録\)** 画面が表示されます。

1. **[Select certificates]\(証明書の選択\)** ウィンドウで、先ほど作成した開発証明書を選択します。 その後 **[続行]** を選択します。

1. 次に、テストに使用するデバイスを選択し、 **[Continue]\(続行\)** を選択します。

1. 最後に、 **[Provisioning Profile Name]\(プロビジョニング プロファイル名\)** でプロファイルの名前を選択し、 **[Generate]\(生成\)** を選択します。

    ![プロビジョニング プロファイル名を選択する](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. 新しいプロビジョニング プロファイルが作成されたら、 **[Download]\(ダウンロード\)** を選択します。 この保存場所を覚えておいてください。

1. プロビジョニング プロファイルの場所を参照し、それをダブルクリックして Xcode 開発マシンにインストールします。

## <a name="create-a-notification-hub"></a>通知ハブを作成する

このセクションでは、前に作成した .p12 プッシュ証明書を使用して、通知ハブを作成し、APNs での認証を構成します。 既に作成した通知ハブを使用する場合は、手順 5. に進んでください。

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>APNs 情報を使用して通知ハブを構成する

1. **[Notification Services]** で、 **[Apple (APNS)]** を選択します。

1. **[Certificate]** を選択します。

1. ファイル アイコンを選択します。

1. 先ほどエクスポートした .p12 ファイルを選択し、 **[Open]\(開く\)** を選択します。

1. 必要に応じて、適切なパスワードを指定します。

1. **[サンドボックス]** モードを選択します。 **[Production] (運用)** モードは、ストアからアプリを購入したユーザーにプッシュ通知を送信する場合にのみ使用します。

    ![Azure portal で APNs 証明書を構成する](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. **[保存]** を選択します。

これで、APNs での通知ハブの構成が完了しました。 接続文字列を使用してアプリを登録し、プッシュ通知を送信することもできます。
