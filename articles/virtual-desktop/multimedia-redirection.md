---
title: Azure Virtual Desktop のマルチメディア リダイレクト - Azure
description: Azure Virtual Desktop のマルチメディア リダイレクト (プレビュー) を使用する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 08/17/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: d479dbc34bd8c08ebc471de74be1558f6dccc6e1
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123273251"
---
# <a name="multimedia-redirection-for-azure-virtual-desktop-preview"></a>Azure Virtual Desktop のマルチメディア リダイレクト (プレビュー)

> [!IMPORTANT]
> Azure Virtual Desktop のマルチメディア リダイレクトは現在プレビュー中です。
> ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

>[!NOTE]
>現在、Azure Virtual Desktop では、Microsoft 365 Government (GCC)、GCC-High 環境、および Microsoft 365 DoD 用の Azure Virtual Desktop のマルチメディア リダイレクトはサポートされていません。
>
>Azure Virtual Desktop のマルチメディア リダイレクトは、Windows 10 マシン上の Windows デスクトップ クライアントでのみ利用できます。 マルチメディア リダイレクトには、Windows デスクトップ クライアントバージョン 1.2.2222 以降が必要です。

マルチメディア リダイレクト (MMR) を使用すると、Azure Virtual Desktop ブラウザーでビデオを視聴するときにビデオをスムーズに再生できます。 マルチメディア リダイレクトでは、メディア要素をブラウザーからローカル コンピューターにリモート処理して、処理とレンダリングを高速化します。 マルチメディア リダイレクト機能は Microsoft Edge と Google Chrome の両方でサポートされています。 ただし、Azure Virtual Desktop のマルチメディア リダイレクトのパブリック プレビュー バージョンは、YouTube の再生が制限されています。 組織のデプロイ内で YouTube をテストするには、[拡張機能を有効にする](#managing-group-policies-for-the-multimedia-redirection-browser-extension)必要があります。

## <a name="requirements"></a>要件

Azure Virtual Desktop のマルチメディア リダイレクトを使用するには、次の操作を行う必要があります。

1. [Windows PC 上の Teams のハードウェア要件](/microsoftteams/hardware-requirements-for-the-teams-app#hardware-requirements-for-teams-on-a-windows-pc/)を満たしている Windows 10 または Windows 10 IoT Enterprise デバイスに [Windows デスクトップ クライアントをインストールします](./user-documentation/connect-windows-7-10.md#install-the-windows-desktop-client)。 バージョン 1.2.2222 以降のクライアントをインストールすると、クライアント デバイスにマルチメディア リダイレクト プラグイン (MsMmrDVCPlugin.dll) もインストールされます。 更新プログラムと新しいバージョンの詳細については、「[Windows Desktop クライアントの新機能](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)」を参照してください。

2. [ユーザーのホスト プールを作成します](create-host-pools-azure-marketplace.md)。

3. ユーザーが Insiders プログラムにアクセスできるようにクライアント コンピューターを構成します。 Insider グループのクライアントを構成するには、次のレジストリ情報を設定します。

   - **キー**: HKLM\\Software\\Microsoft\\MSRDC\\Policies
   - **種類**: REG_SZ
   - **名前**: ReleaseRing
   - **データ**: insider

   Insiders プログラムの詳細については、[管理者向け Windows デスクトップ クライアント](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-admin#configure-user-groups)に関するページを参照してください。

4. [MSI インストーラー (MsMmrHostMri)](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWIzIk) を使用して、インターネット ブラウザー用のマルチメディア リダイレクト拡張機能を Azure VM にインストールします。 Azure Virtual Desktop のマルチメディア リダイレクトでは、Microsoft Edge および Google Chrome のみが現在サポートされています。

## <a name="managing-group-policies-for-the-multimedia-redirection-browser-extension"></a>マルチメディア リダイレクト ブラウザー拡張機能のグループ ポリシーの管理

マルチメディア リダイレクト MSI を使用すると、ブラウザー拡張機能がインストールされます。 ただし、このサービスはまだパブリック プレビュー中であるため、ユーザー エクスペリエンスは異なる場合があります。 既知の問題の詳細については、「[既知の問題](#known-issues-and-limitations)」を参照してください。

場合によっては、グループ ポリシーを変更してブラウザー拡張機能を管理し、ユーザー エクスペリエンスを向上させることができます。 次に例を示します。

- ユーザーの操作なしで拡張機能をインストールできます。
- マルチメディア リダイレクトを使用する Web サイトを制限できます。
- 既定では、Google Chrome で拡張機能アイコンをピン留めできます。 拡張機能アイコンは Microsoft Edge では既定でピン留めされているため、この設定は Chrome でのみ変更する必要があります。

### <a name="configure-microsoft-edge-group-policies-for-multimedia-redirection"></a>マルチメディア リダイレクト用に Microsoft Edge グループ ポリシーを構成する

グループ ポリシーを構成するには、Microsoft Edge 管理用テンプレートを編集する必要があります。 拡張機能構成オプションは、 **[管理用テンプレート] > [Microsoft Edge Extensions]\(Microsoft Edge 拡張機能\)**  >  **[Configure extension management settings]\(拡張機能管理設定の構成\)** にあります。

次のコードは、ブラウザーにマルチメディア リダイレクト拡張機能をインストールし、YouTube にマルチメディア リダイレクトの読み込みのみを許可する Microsoft Edge グループ ポリシーの例です。

```cmd
{ "joeclbldhdmoijbaagobkhlpfjglcihd": { "installation_mode": "force_installed", "runtime_allowed_hosts": [ "*://*.youtube.com" ], "runtime_blocked_hosts": [ "*://*" ], "update_url": "https://edge.microsoft.com/extensionwebstorebase/v1/crx" } }
```

グループ ポリシー構成の詳細については、[Microsoft Edge グループ ポリシー](/DeployEdge/configure-microsoft-edge)に関する記事を参照してください。

### <a name="configure-google-chrome-group-policies-for-multimedia-redirection"></a>マルチメディア リダイレクト用に Google Chrome グループ ポリシーを構成する

Google Chrome グループ ポリシーを構成するには、Google Chrome 管理用テンプレートを編集する必要があります。 拡張機能構成オプションは、 **[管理用テンプレート]**  >  **[Google]**  >  **[Google Chrome Extensions]\(Google Chrome 拡張機能\)**  >  **[Extension management settings]\(拡張機能管理設定\)** にあります。

次の例は、「[マルチメディア リダイレクト用に Microsoft Edge グループ ポリシーを構成する](#configure-microsoft-edge-group-policies-for-multimedia-redirection)」のコード例にたいへんよく似ています。 このポリシーでは、右上のメニューにピン留めされるアイコン付きでマルチメディア リダイレクト拡張機能を強制的にインストールし、YouTube へのマルチメディア リダイレクトの読み込みのみを許可します。

```cmd
{ "lfmemoeeciijgkjkgbgikoonlkabmlno": { "installation_mode": "force_installed", "runtime_allowed_hosts": [ "*://*.youtube.com" ], "runtime_blocked_hosts": [ "*://*" ], "toolbar_pin": "force_pinned", "update_url": "https://clients2.google.com/service/update2/crx" } }
```

[Google Chrome のグループ ポリシー](https://support.google.com/chrome/a/answer/187202#zippy=%2Cwindows)の構成に関する追加情報があります。

## <a name="run-the-multimedia-redirection-extension-manually-on-a-browser"></a>マルチメディア リダイレクト拡張機能をブラウザーで手動で実行する

MMR では、Microsoft Edge と Google Chrome のブラウザー用に、リモート アプリとセッション デスクトップが使用されます。 [要件](#requirements)を満たしたら、サポートされるブラウザーを開きます。 グループ ポリシーを使用してブラウザーまたは拡張機能をインストールしなかった場合、ユーザーは拡張機能を手動で実行する必要があります。 このセクションでは、現在サポートされているブラウザーの 1 つで拡張機能を手動で実行する方法について説明します。

### <a name="microsoft-edge"></a>Microsoft Edge

Microsoft Edge で拡張機能を手動で実行するには、オーバーフロー メニューの黄色の感嘆符を探します。 Azure Virtual Desktop マルチメディア リダイレクト拡張機能を有効にするように求めるメッセージが表示されているはずです。 **[拡張機能の有効化]** を選択します。

### <a name="google-chrome"></a>Google Chrome

Google Chrome で拡張機能を手動で実行するには、次のスクリーンショットに示すように、新しい拡張機能がインストールされていることを示す通知メッセージを探します。 

![Google Chrome タスク バーのスクリーンショット。 [New Extension Added]\(新しい拡張機能が追加されました\) という通知タブがあります。](media/chrome-notification.png)

ユーザーが拡張機能を有効にすることを許可する通知を選択します。 また、ユーザーは、マルチメディア リダイレクトが接続された場合にアイコンから確認できるように、拡張機能をピン留めする必要があります。

### <a name="the-multimedia-redirection-status-icon"></a>マルチメディア リダイレクトの状態アイコン

マルチメディア リダイレクトがブラウザーでアクティブになっていることを簡単に確認するために、次のアイコンの状態が追加されました。

| アイコンの状態  | 定義  |
|-----------------|-----------------|
| ![状態が適用されていない既定の Azure Virtual Desktop プログラム アイコン。](./media/icon-default.png) | 状態が適用されていない既定のアイコンの外観。 |
| ![マルチメディア リダイレクトが機能していないことを示す、赤い四角形に x が付いた Azure Virtual Desktop プログラム アイコン。](./media/icon-disconnect.png) | 内部に "X" が付いた赤い四角形は、クライアントがマルチメディア リダイレクトに接続できなかったことを意味します。 |
| ![マルチメディア リダイレクトが動作中であることを示す、内部にチェック マークが付いた緑色の四角形が表示された Azure Virtual Desktop プログラム アイコン。](./media/icon-connect.png) | 内側にチェック マークが付いた緑色の四角形は、クライアントがマルチメディア リダイレクトに正常に接続されたことを意味します。 |

アイコンを選択すると、すべての Web サイトでマルチメディア リダイレクトを有効または無効にできるチェック ボックスを含むポップアップ メニューが表示されます。 また、サービスの各コンポーネントのバージョン番号も一覧表示されます。

## <a name="support-during-public-preview"></a>パブリック プレビュー中のサポート
Microsoft サポートでは、パブリック プレビュー中のマルチメディア リダイレクトの問題を処理しません。

問題が発生した場合は、クライアントと VM ホストの両方のフィードバック ハブでお知せください。

フィードバックを送信するには、次のようにします。

1. クライアントとサーバーの両方で **フィードバック ハブ** を開きます。

2. **[問題を報告する]** を選択します。

3. 両方の問題レポートで同じタイトルを使用しますが、最初に "[Client]" または "[Host]" を入力して、レポートの送信元を指定します。

    たとえば、クライアントから問題を送信する場合は、次のように書式設定します。

    >[Client] レポートのタイトル

    ホストから問題を送信する場合は、次のように書式設定します。

    >[Host] レポートのタイトル

4. **[Explain in more detail]\(詳細を説明する\)** フィールドに、発生している問題を説明します。 問題が発生したときに視聴していたビデオの URL も含めることをお勧めします。

5. 終了したら、 **[次へ]** を選択します。

6. 次のスクリーンショットに示すように、 **[Problem]\(問題\)** バブルを選択し、2 つのドロップダウン メニューから **[Apps]\(アプリ\)** および **[Remote Desktop]\(リモート デスクトップ\)** を選択します。

    ![2\. Choose a category\(2. カテゴリの選択\) ウィンドウのスクリーンショット。 ユーザーが [Problem]\(問題\) バブルを選択し、その下のドロップダウン メニューで [Apps]\(アプリ\) と [Remote Desktop]\(リモート デスクトップ\) を選択しました。](media/problem-category.png)

7. **[次へ]** を選択します。

8. 送信する予定の問題と同様の問題が一覧にないか確認します。
   
   - アクティブなバグにリンクするバブルが表示される場合、バグの説明が、報告中の問題と一致する必要があります。 一致する場合、バブルを選択し、以下のスクリーンショットに示すように **[バグにリンク]** を選択します。

    ![3\. Find similar feedback\(3. 同様のフィードバックを見つける\) ウィンドウ。 ユーザーが [Link to bug number 32350300 Active]\(バグ 32350300 へのリンクがアクティブ\) オプションのバブルを選択しました。](media/link-to-bug.png)

    - 類似した問題がない場合、 **[Make new bug]\(新しいバグを作成する\)** を選択します。

    ![3\. Find similar feedback\(3. 同様のフィードバックを見つける\) ウィンドウ。 今回は、[Link to bug]\(バグへのリンク\) オプションはなく、ユーザーは代わりに [Make new bug]\(新しいバグを作成する\) を選択しています。](media/make-new-bug.png)

9. **[次へ]** を選択します。

10. **[詳細を追加する]** ウィンドウで、 **[Include data about Remote Desktop (Default)]\(リモート デスクトップについてのデータを含める (既定)\)** を選択し、すべての質問にできるだけ詳しく回答します。

    問題を記録したビデオを追加する場合、 **[Include data about Remote Desktop (Default)]\(リモート デスクトップについてのデータを含める (既定)\)** を選択し、 **[記録の開始]** ボタンを選択します。 記録中にリモート デスクトップを開き、問題の発生につながるプロセスを実行します。 完了したら、ブラウザーに戻り、ビデオをテストして、正しく記録されていることを確認します。

    完了したら、添付ファイルと診断を Microsoft に送信することに同意し、 **[Submit]\(送信\)** を選択します。

### <a name="known-issues-and-limitations"></a>既知の問題と制限事項

次の問題は既に認識されている問題なので、報告する必要はありません。

- マルチメディア リダイレクトは、Web クライアントではなく Windows デスクトップ クライアントでのみ機能します。

- マルチメディア リダイレクトは現在、保護されたコンテンツがサポートされないため、Pluralsight および Netflix からのビデオは動作しません。

- パブリック プレビュー中は、YouTube を除くすべてのサイトでマルチメディア リダイレクトが無効になります。 ただし、拡張機能がある場合は、すべての Web サイトでマルチメディア リダイレクトを有効にできます。 組織が会社の Web サイトで機能をテストできるように、拡張機能が追加されました。

- MSI インストーラーが内部テスト中に拡張機能をインストールできない可能性がわずかに存在します。 この問題が発生した場合は、Microsoft Edge ストアまたは Google Chrome ストアからマルチメディア リダイレクト拡張機能をインストールする必要があります。

    - [マルチメディア リダイレクト ブラウザー拡張機能 (Microsoft Edge)](https://microsoftedge.microsoft.com/addons/detail/wvd-multimedia-redirectio/joeclbldhdmoijbaagobkhlpfjglcihd)
    - [マルチメディア ブラウザー拡張機能 (Google Chrome)](https://chrome.google.com/webstore/detail/wvd-multimedia-redirectio/lfmemoeeciijgkjkgbgikoonlkabmlno)

- MSI インストーラーを使用してホスト マシンに拡張機能をインストールすると、ユーザーがブラウザーを初めて開いた場合に拡張機能を受け入れることをユーザーに求めるメッセージが表示されるか、警告またはエラー メッセージが表示されます。 ユーザーがこのプロンプトを拒否すると、拡張機能が読み込まれない可能性があります。 この問題を回避するには、[グループ ポリシーを編集](#managing-group-policies-for-the-multimedia-redirection-browser-extension)して拡張機能をインストールします。

- ビデオ ウィンドウのサイズを変更すると、ウィンドウのサイズはビデオ自体よりも速く調整されます。 また、ウィンドウを最小化および最大化するときにも、この問題が発生します。

## <a name="next-steps"></a>次のステップ

Azure Virtual Desktop の他の部分でのビデオ ストリーミングに関心がある場合は、[Azure Virtual Desktop での Teams](teams-on-avd.md) に関する記事を参照してください。
