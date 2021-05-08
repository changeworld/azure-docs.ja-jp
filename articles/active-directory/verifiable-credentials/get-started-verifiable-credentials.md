---
title: チュートリアル - サンプル アプリを使用して Azure Active Directory Verifiable Credentials の作業を開始する (プレビュー)
description: このチュートリアルでは、Microsoft のサンプル アプリとテスト テナントを使用して検証可能な資格情報を発行する方法について説明します
ms.service: identity
ms.subservice: verifiable-credentials
author: barclayn
ms.author: barclayn
ms.topic: tutorial
ms.date: 04/01/2021
ms.openlocfilehash: deebaf31197d8b7335f887ae447f05add45278b2
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222887"
---
#  <a name="tutorial---get-started-with-azure-active-directory-verifiable-credentials-using-a-sample-app-preview"></a>チュートリアル - サンプル アプリを使用して Azure Active Directory Verifiable Credentials の作業を開始する (プレビュー)

このチュートリアルでは、最初の検証可能な資格情報 (検証済み資格情報エキスパート カード) を発行するために必要な手順について説明します。 このカードを使用して、自分がデジタル資格証明の技術に秀でた検証済み資格情報エキスパートであることを検証者に証明できます。 Verifiable Credentials のサンプル アプリを使用して Azure Active Directory Verifiable Credentials の作業を開始し、最初の検証可能な資格情報を発行しましょう。

![これはサンプルのカードの画像です](media/get-started-verifiable-credentials/verifiedcredentialexpert-card.png)

> [!IMPORTANT]
> Azure Active Directory Verifiable Credentials は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

- テスト システムへの [NodeJS](https://nodejs.org/en/download/) バージョン 10.14 以上のインストール。
- サンプル アプリをホストするリポジトリをクローンしたい場合は [GIT](https://git-scm.com/downloads) のインストールが必要。
- [Visual Studio Code](https://code.visualstudio.com/Download)
- サンプル サイトをホストするためのシステム。
- モバイル デバイスへの Microsoft Authenticator バージョン 6.2005.3599 以上のインストール。
- 無料の [NGROK](https://ngrok.com/)。

## <a name="download-the-sample-code"></a>サンプル コードのダウンロード

検証済み資格情報エキスパート カードを自分自身に発行するには、自分のローカル コンピューター上で Web サイトを実行する必要があります。 Web サイトは、検証可能な資格情報の発行プロセスを開始するために使用されます。 Microsoft は NodeJS で記述されたシンプルな Web サイトを用意しています。このチュートリアルでは、それを使用します。

最初に、[こちら](https://github.com/Azure-Samples/active-directory-verifiable-credentials)の GitHub からサンプル コードをダウンロードするか、レポジトリを自分のローカル コンピューターにクローンします。

```terminal
git clone https://github.com/Azure-Samples/active-directory-verifiable-credentials.git
```

サンプル Web サイトのコードを詳しく確認したいと思われるかもしれません。 `issuer` フォルダーには、検証可能な資格情報を発行するために使用されるコードがすべて含まれています。 詳細については、サンプルの [Readme](https://github.com/Azure-Samples/active-directory-verifiable-credentials) をお読みください。

## <a name="run-the-issuer-website"></a>発行者の Web サイトの実行

手順は、Visual Studio Code、またはご使用のオペレーティング システムで利用可能な任意のコマンド ラインで実行できます。 

1. `issuer` フォルダーに移動します。 

    ```terminal
    cd issuer
    ```

2. そこで、必須のパッケージをすべてインストールし、サイトを開始する必要があります。

   ```terminal
    npm install
    node app.js
    ```

3. ターミナルで、自分の発行者アプリがポート 8081 をリッスンしていることを確認します。 次に、Authenticator で自分のアプリと通信できるように、Ngrok を使用してリバース プロキシを設定しましょう。 

## <a name="creating-a-reverse-proxy-with-ngrok"></a>Ngrok によるリバース プロキシの作成

サンプル Web サイトを実行するとき、デバイスは、ローカル コンピューター上で実行されている Node サーバーと通信する必要があります。 インターネット経由でローカル開発サーバーを利用可能にする簡単な方法として、[ngrok](https://ngrok.com/) の使用をお勧めします。

1.  **ngrok** をダウンロードして抽出した後、以下を実行する必要があります。

    ```terminal
     ngrok http 8081
    ```

既定では、サンプル Web サイトはポート `8081` で実行されます。 **Ngrok** によって、2 つの転送 URL が自分のサーバー用に出力されます。 `https://` プレフィックスと共に URL をコピーします。

![インターネット経由でアプリケーションのエンド ポイントを利用できるようにするのに役立つ ngrok](media/get-started-verifiable-credentials/ngrok.png)

>[!NOTE] 
> PowerShell を使用している場合、コマンドが認識されるために「`./ngrok`」の入力が必要な可能性があります。

ngrok を使用して自分のローカル ポートがインターネットに公開されると、ngrok によって生成されたホスト名がサンプル サイトで自動的に使用されます。 ブラウザーを開いて、ngrok の https 転送 URL に移動します。 サンプル サイトのホームページに正常にアクセスできるはずです。 ページが開かれると、自分のローカル サーバーで実行されているサンプル アプリとデバイスで通信できます。 これで、検証済み資格情報エキスパート カードを自分自身に発行する準備ができました。

## <a name="issue-a-credential"></a>資格情報の発行

1. Authenticator を自分のモバイル デバイスにインストールします。 Microsoft Authenticator は、自分の検証可能な資格情報を受け取り、保存して、関係者に提示するために使用されます。

2. 次に、検証可能な資格情報を自分自身に発行します。 **[Get Credential]\(資格情報の取得\)** ボタンを **クリック** します。 **[Get Credential]\(資格情報の取得\)** ボタンをクリックすると、Authenticator を使用してスキャンできる QR コードがサンプル Web サイトに表示されます。 モバイル デバイスのブラウザーでサイトを表示する場合は、 **[Get Credential]\(資格情報の取得\)** ボタンをクリックするとディープ リンクがトリガーされます。これにより、Authenticator アプリが開かれ、QR コードをスキャンする必要がありません。

   ![[Get Credential]\(資格情報の取得\) ボタン](media/get-started-verifiable-credentials/credential-expert-get.png)

3. Authenticator を使用して Web サイトの QR コードをスキャンします。または、モバイル経由で Web サイトにアクセスしている場合は、[Get Credential]\(資格情報の取得\) ボタンをクリックしてディープ リンクをトリガーします。 

   ![QR コード ](media/get-started-verifiable-credentials/credential-expert-issue.png)

4. ここでは **[追加]** ボタンが灰色表示になっているのがわかります。 カードの画像の下にある **[アカウントにサインインする]** を選択します。

   ![サインイン ](media/get-started-verifiable-credentials/add-verified-credential-expert.png)

5. 自分の資格情報エキスパート カードを取得する前に、使用中のテナントで認証情報を入力する必要があります。 チュートリアルを実行するのが初めてで、資格情報エキスパート アカウントがない場合は、B2C テナント内に新しいユーザー アカウントを作成します。

   ![続行前の認証](media/get-started-verifiable-credentials/authenticate-credential-expert.png)

6. サインイン後、 **[追加]** ボタンの灰色表示が解除されます。 **[追加]** を選択して、新しい VC を受け取ります。

   ![認証後の [追加] の選択](media/get-started-verifiable-credentials/add-verified-credential-expert-after-auth.png) 


7. おめでとうございます。 検証済み資格情報エキスパートの VC を取得できました。

   ![資格情報エキスパートの VC の追加](media/get-started-verifiable-credentials/credential-expert-add-card.png) 
 
次は、自分の資格情報を検証します。

## <a name="validate-credentials"></a>資格情報を検証

チュートリアルの発行のパートを完了し、Authenticator に検証可能な資格情報を用意できたところで、それを自分の検証者アプリで検証しましょう。

1.  ngrok 発行者サービスの実行を停止します。

    ```terminal
     control+c
    ```


2. 別のターミナル ウィンドウで検証者アプリ フォルダーを開いて、発行者アプリを実行したのと同様の方法でそれを実行します。

    ```terminal
     cd verifier
     npm install
     node app.js
    ```

3. 次に、検証者のポートを 8082 にして ngrok を実行します。

    ```terminal
    ngrok http 8082
    ```

4. ブラウザーで ngrok の https 転送 URL を開いて、 **[VERIFY CREDENTIAL]\(資格情報を検証する\)** ボタンをタップします。  

   ![資格情報エキスパートの検証ボタン](media/get-started-verifiable-credentials/prove-credential-expert.png)

5. Authenticator を開いて、QR コードをスキャンします。

   ![QR コードのスキャンによる資格情報の検証](media/get-started-verifiable-credentials/scan-verify.png)

  > [!IMPORTANT]
  > これは iOS では右上、Android では右下に表示されます。 QR コードをスキャンします。

6. Authenticator の [新しいアクセス許可の要求] 画面で、 **[許可]** を選択します。 [許可] を押すことで、自分の DID (分散化識別子) を使用して検証可能なプレゼンテーションに署名し、この検証可能な資格情報を実際に制御していることを証明します。

   ![新しいアクセス許可の要求](media/get-started-verifiable-credentials/new-permission-request.png)

    表示が成功した後、3 つの点が更新されているはずです。

   1. ここでは Web ページに、"Congratulations, <自分の名前> is a Verified Credential Expert!" と表示されます。
   
    ![おめでとうございます、再検証](media/get-started-verifiable-credentials/congratulations.png)


   2. また、検証者アプリ ターミナルでも、ログに同じメッセージが表示されます。
   
    ![コマンド プロンプトのアプリケーション アクティビティ](media/get-started-verifiable-credentials/cmd-verified-expert.png)

   3. Authenticator では、この表示の最近のアクティビティにエントリがあります。

    ![Authenticator のアクティビティ](media/get-started-verifiable-credentials/recent-activity.png)

   
>[!NOTE]
> 検証者アプリを実行していると、ngrok の動作が停止し、接続数が多すぎるというエラーが表示される場合があります。 これは、アカウントを ngrok に登録すると回避できることがわかっています。 

## <a name="next-steps"></a>次のステップ

クイック スタート ガイドを正常に完了したら、次は Azure Active Directory Verifiable Credentials サービスで独自の分散化識別子を作成し、独自の検証可能な資格情報を発行してみましょう。

>[!div class="nextstepaction"] 
>[検証可能な資格情報のサンプル アプリを使用して独自の発行者を構成する](./enable-your-tenant-verifiable-credentials.md)
