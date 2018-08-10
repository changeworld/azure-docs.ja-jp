---
title: Azure Storage Explorer トラブルシューティング ガイド | Microsoft Docs
description: Azure の 2 つのデバッグ機能の概要
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.component: common
ms.openlocfilehash: eb72d92496addacd82e4d30df625b4f9c0c823e5
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521032"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Storage Explorer トラブルシューティング ガイド

Microsoft Azure Storage Explorer は、Windows、macOS、Linux で Azure Storage データを容易に操作できるスタンドアロン アプリです。 このアプリは、Azure、National Clouds、および Azure Stack でホストされているストレージ アカウントに接続できます。

このガイドでは、Storage Explorer で確認されている一般的な問題の解決方法を紹介しています。

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>エラー: 証明書チェーンの自己署名証明書 (および同様のエラー)

証明書のエラーは、次の 2 つの状況のいずれかで発生します。

1. アプリが “透過型プロキシ” を介して接続されています。すなわち、サーバー (会社のサーバーなど) によって HTTPS トラフィックが遮断され、暗号化が解除され、その後自己署名証明書を使用して暗号化されています。
2. 受信した HTTPS メッセージに自己署名の SSL 証明書を挿入するアプリケーションが実行されています。 証明書を挿入するアプリケーションの例には、ウイルス対策およびネットワーク トラフィック検査ソフトウェアが含まれています。

Storage Explorer に自己署名証明書または信頼されない証明書が表示された場合、受信した HTTPS メッセージが変更されたかどうかを知ることができなくなります。 自己署名証明書のコピーがある場合は、次のステップを実行することによって、Storage Explorer にそのコピーを信頼させることができます。

1. Base-64 でエンコードされた X.509 (.cer) 証明書のコピーを取得します
2. **[編集]** > **[SSL 証明書]** > **[証明書のインポート]** の順にクリックし、ファイル ピッカーを使用して、.cer ファイルを検索して選択し、開きます

この問題は、複数の証明書 (ルート証明書および中間証明書) があることが原因になっている場合もあります。 エラーを解決するには両方の証明書を追加する必要があります。

証明書の取得先がわからない場合は、次の手順を実行して取得先を見つけることができます。

1. Open SSL をインストールします。

    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (Light バージョンのいずれかで十分です)
    * Mac と Linux: オペレーティング システムに付属
2. Open SSL を実行します。

    * Windows: インストール ディレクトリを開き、**/bin/** をクリックして、**openssl.exe** をダブルクリックします。
    * Mac と Linux: ターミナルで **openssl** を実行します。
3. `s_client -showcerts -connect microsoft.com:443` を実行します
4. 自己署名証明書を検索します。 どれが自己署名かわからない場合は、証明書の発行先 `("s:")` と証明書の発行者 `("i:")` が同じものを検索します。
5. 自己署名証明書が見つかったら、それぞれの証明書の **-----BEGIN CERTIFICATE-----** から **-----END CERTIFICATE-----** までのすべての内容をコピーして、新しい .cer ファイルに貼り付けます。
6. Storage Explorer を開き、**[編集]** > **[SSL 証明書]** > **[証明書のインポート]** の順にクリックし、ファイル ピッカーを使用して、作成した .cer ファイルを検索して選択し、開きます。

前述の手順で自己署名証明書が見つからない場合は、フィードバック ツールを使用して Microsoft にお問い合わせください。 また、Storage Explorer に `--ignore-certificate-errors` フラグを指定してコマンドラインから起動することも選択できます。 このフラグを指定して起動すると、Storage Explorer は証明書のエラーを無視します。

## <a name="sign-in-issues"></a>サインインの問題

### <a name="reauthentication-loop-or-upn-change"></a>再認証ループまたは UPN の変更
再認証ループに入った、またはアカウントのいずれかの UPN を変更されている場合は、以下を試してください。
1. すべてのアカウントを削除した後、Storage Explorer を閉じます。
2. コンピューターから .IdentityService フォルダーを削除します。 Windows では、このフォルダーは `C:\users\<username>\AppData\Local` にあります。 Mac と Linux では、このフォルダーは、ユーザー ディレクトリのルートで見つけることができます。
3. Mac または Linux の場合は、OS のキーストアから Microsoft.Developer.IdentityService エントリも削除する必要があります。 Mac では、キーストアは "Gnome Keychain" アプリケーションです。 Linux では、このアプリケーションは通常は "Keyring" という名前ですが、お使いのディストリビューションによっては名前が違うことがあります。

## <a name="mac-keychain-errors"></a>Mac キーチェーン エラー
macOS のキーチェーンが、Storage Explorer の認証ライブラリの問題を引き起こす状態になることがあります。 この状態からキーチェーンを取得するには、以下の手順を実行します。
1. Storage Explorer を閉じます。
2. キーチェーンを開きます (**Cmd キーを押しながら Space キー**を押し、キーチェーンを入力し、Enter キーを押します)。
3. "ログイン" キーチェーンを選択します。
4. 南京錠アイコンをクリックして、キーチェーンをロックします (完了すると、鍵がかかった南京錠に表示が切り替わりますが、開いているアプリによっては数秒かかる場合があります)。

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Storage Explorer を起動します。
6. サービス ハブがキーチェーンへのアクセスを求めている旨のメッセージを示すポップアップが表示されます。 これが表示されたら、Mac 管理者アカウント パスワードを入力し、**[常に許可]** (**[常に許可]** を使用できない場合は **[許可]**) をクリックします。
7. サインインを試します。

### <a name="general-sign-in-troubleshooting-steps"></a>サインインの一般的なトラブルシューティングの手順
* macOS 上で、[Waiting for authentication...]\(認証の完了を待機しています\) ダイアログが表示されたままサインイン ウィンドウが表示されない場合は、[こちらの手順](#Mac-Keychain-Errors)を試してください。
* Storage Explorer を再起動する
* 認証ウィンドウが空白の場合は、認証ダイアログ ボックスを閉じる前に少なくとも 1 分待機します。
* プロキシと証明書が、使用中のマシンと Storage Explorer の両方の設定用に正しく構成されていることを確認します。
* Windows を使用していて、同じマシン上で Visual Studio 2017 にアクセス権があるときにログインする場合は、Visual Studio 2017 にサインインしてみてください。 Visual Studio 2017 へのサインインが成功した後、Storage Explorer を開いてアカウント パネルでお使いのアカウントを確認できます。 

これらの方法がいずれもうまくいかない場合、[GitHub で問題をオープンしてください](https://github.com/Microsoft/AzureStorageExplorer/issues)。

### <a name="missing-subscriptions-and-broken-tenants"></a>サブスクリプションの欠落とテナントの破損

正常にサインインした後にサブスクリプションを取得できない場合は、次のトラブルシューティング方法を試してください。

* お使いのアカウントに必要なサブスクリプションへのアクセス権があることを確認します。 使用しようとしている Azure 環境のポータルにサインインして、アクセス権があることを確認することができます。
* 適切な Azure 環境 (Azure、Azure China、Azure Germany、Azure US Government、またはカスタム環境) を使用してサインインしていることを確認します。
* プロキシの内側にいる場合は、Storage Explorer のプロキシが適切に構成されていることを確認します。
* アカウントを削除してから再度追加します。
* [詳細] リンクがある場合は、障害が発生しているテナントに対してどのようなエラー メッセージが報告されているかを確認します。 エラー メッセージを見てもどうすればいいかわからない場合は、[GitHub に問題を投稿](https://github.com/Microsoft/AzureStorageExplorer/issues)してください。

## <a name="cannot-remove-attached-account-or-storage-resource"></a>接続されているアカウントまたはストレージ リソースを削除できない

接続されているアカウントまたはストレージ リソースを UI を使用して削除できない場合は、次のフォルダーを削除して、接続されているすべてのリソースを手動で削除できます。

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Applicaiton Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
>  上記のフォルダーを削除する前に、Storage Explorer エクスプローラーを閉じます。

> [!NOTE]
>  これまで SSL 証明書をインポートしたことがある場合、`certs` ディレクトリの内容をバックアップします。 後でバックアップを使用して、SSL 証明書を再インポートすることができます。

## <a name="proxy-issues"></a>プロキシの問題

最初に、入力した次の情報がすべて正しいことを確認します。

* プロキシの URL とポート番号
* プロキシで必要な場合はユーザー名とパスワード

### <a name="common-solutions"></a>一般的な解決方法

問題が引き続き発生する場合は、以下のトラブルシューティング方法を試してください。

* プロキシを使用せずにインターネットに接続できる場合は、プロキシの設定を有効にしなくても Storage Explorer が動作することを確認します。 動作する場合は、プロキシの設定に問題があると考えられます。 プロキシ管理者と連携して問題を特定してください。
* プロキシ サーバーを使用する他のアプリケーションが期待どおりに動作するかを確認します。
* 使用しようとしている Azure 環境のポータルに接続できることを確認します
* サービス エンドポイントからの応答を受信できるか確認します。 ブラウザーに、エンドポイント URL のいずれかを入力します。 接続できる場合は、InvalidQueryParameterValue または同様の XML の応答を受信します。
* プロキシ サーバーで Storage Explorer を使用しているユーザーが他にもいる場合は、それらのユーザーが接続できるかを確認します。 他のユーザーが接続できる場合は、プロキシ サーバー管理者にお問い合わせください。

### <a name="tools-for-diagnosing-issues"></a>問題診断ツール

ネットワーク ツール (Windows 用 Fiddler など) をお持ちの場合は、次のように問題を診断できる場合があります。

* プロキシ経由で作業する必要がある場合は、プロキシ経由で接続するようにネットワーク ツールを構成する必要があります。
* ネットワーク ツールで使用されるポート番号を確認します。
* Storage Explorer のプロキシ設定として、ローカル ホストの URL とネットワーク ツールのポート番号を入力します。 入力が正しく行われると、Storage Explorer によって生成された、管理とサービス エンドポイントに対するネットワーク要求のログ記録が、ネットワーク ツールによって開始されます。 たとえば、BLOB エンドポイントの場合はブラウザーに https://cawablobgrs.blob.core.windows.net/ と入力すると、リソースは存在するもののアクセスできないことを示す次のようなメッセージが表示されます。

![コード サンプル](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>プロキシ サーバー管理者に問い合わせる

プロキシの設定が正しい場合は、プロキシ サーバー管理者に問い合わせて、

* プロキシによって、Azure の管理またはリソース エンドポイントへのトラフィックがブロックされていないことを確認します。
* プロキシ サーバーで使用されている認証プロトコルを確認します。 Storage Explorer は、現在 NTLM プロキシをサポートしていません。

## <a name="unable-to-retrieve-children-error-message"></a>"子を取得できません" エラー メッセージ

プロキシ経由で Azure に接続している場合は、プロキシの設定が正しいことを確認します。 サブスクリプションまたはアカウント所有者からリソースへのアクセス権が付与された場合は、該当するリソースの読み取りまたは一覧表示の権限があることを確認してください。

## <a name="issues-with-sas-url"></a>SAS URL の問題
SAS URL を使用してサービスに接続し、上記のエラーが発生する場合は、

* リソースの読み取りまたは一覧表示に必要なアクセス許可が URL で提供されているかを確認します。
* URL の有効期限が切れていないかを確認します。
* SAS URL がアクセス ポリシーに基づいている場合は、このアクセス ポリシーが失効されていないかを確認します。

無効な SAS URL を使用して誤ってアタッチし、デタッチできない場合は、次の手順を実行します。
1.  Storage Explorer を実行している場合は、F12 キーを押して開発者ツール ウィンドウを開きます。
2.  [アプリケーション] タブをクリックし、ローカル記憶域をクリックし、左側のツリーで file:// をクリックします。
3.  問題がある SAS URI のサービスの種類と関連付けられているキーを見つけます。 たとえば、BLOB コンテナーの SAS URI が不適切である場合は、`StorageExplorer_AddStorageServiceSAS_v1_blob` というキーを探します。
4.  キーの値は JSON 配列にする必要があります。 不適切な URI に関連付けられたオブジェクトを見つけて削除します。
5.  Ctrl キーを押しながら R キーを押して、Storage Explorer をリロードします。

## <a name="linux-dependencies"></a>Linux の依存関係

Ubuntu 16.04 以外の Linux ディストリビューションの場合、いくつかの依存関係を手動でインストールすることが必要な場合があります。 一般に、次のパッケージが必要です。
* [.NET Core 2.x](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libsecret`
* `libgconf-2-4`
* 最新の GCC

ご使用のディストリビューションによっては、他のパッケージのインストールも必要な場合があります。 Storage Explorer の[リリース ノート](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409)には、一部のディストリビューションでの具体的な手順が掲載されています。

## <a name="next-steps"></a>次の手順

上記の解決策がすべてうまくいかない場合は、[GitHub で問題をオープンしてください](https://github.com/Microsoft/AzureStorageExplorer/issues)。 また、左下隅にある [Report issue to GitHub (GitHub に問題をレポート)] ボタンを使用して、GitHub にすばやくアクセスすることもできます。

![フィードバック](./media/storage-explorer-troubleshooting/feedback-button.PNG)
