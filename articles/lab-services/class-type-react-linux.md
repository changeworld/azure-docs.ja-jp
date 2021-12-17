---
title: Azure Lab Services を使用して Linux 上の React でラボを設定する
description: React 開発クラスのラボを設定する方法について説明します。
author: emaher
ms.topic: how-to
ms.date: 05/16/2021
ms.author: enewman
ms.openlocfilehash: 8cd635b5b46e42e59901e0326188b955ecde9b79
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176664"
---
# <a name="set-up-lab-for-react-on-linux"></a>Linux 上の React のラボを設定する

[React](https://reactjs.org/) は、ユーザー インターフェイス (UI) を構築するための一般的な JavaScript ライブラリです。 React は、Web サイト用に再利用可能なコンポーネントを作成するための宣言型の方法です。  JavaScript ベースのフロントエンド開発には、他にも多くの一般的なライブラリがあります。  ラボを作成する際に、これらのライブラリのいくつかを使用します。  [Redux](https://redux.js.org/) は、JavaScript アプリ用の予測可能な状態コンテナーを提供するライブラリであり、多くの場合、React を補完するために使用されます。 [JSX](https://reactjs.org/docs/introducing-jsx.html) は、UI の外観を記述するために React でよく使用される、JavaScript のライブラリ構文拡張機能です。  [NodeJS](https://nodejs.org/) は、React アプリケーション用の Web サーバーを実行する便利な方法です。

この記事では、React Web 開発クラスに必要な、開発環境用の [Visual Studio Code](https://code.visualstudio.com/)、ツール、およびライブラリをインストールする方法について説明します。

## <a name="lab-configuration"></a>ラボの構成

このラボを設定するには、Azure サブスクリプションとラボ アカウントが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 Azure サブスクリプションを取得したら、Azure Lab Services で新しいラボ アカウントを作成できます。 新しいラボ アカウントの作成の詳細については、[ラボ アカウントの設定方法](./tutorial-setup-lab-account.md)に関するチュートリアルを参照してください。 既存のラボ アカウントを使用することもできます。

### <a name="lab-account-settings"></a>ラボ アカウントの設定

次の表の説明に従って、ラボ アカウントの設定を有効にします。 Azure Marketplace イメージを有効にする方法の詳細については、「[ラボ作成者が利用できる Azure Marketplace イメージを指定する](./specify-marketplace-images.md)」を参照してください。

| ラボ アカウントの設定 | Instructions |
| ----------- | ------------ |  
| マーケットプレースの画像 | ラボ アカウント内で使用する "Ubuntu Server 18.04 LTS" イメージを有効にします。 |

### <a name="lab-settings"></a>ラボの設定

仮想マシン (VM) の推奨サイズは、学生が実行する必要があるワークロードの種類によって異なります。  

| ラボの設定 | 値と説明 |
| ------------ | ------------------ |
| 仮想マシンのサイズ | **Small**。|

ワークロードをテストして、より大きなサイズが必要かどうかを確認することをお勧めします。  各サイズの詳細については、「[VM のサイズ設定](administrator-guide.md#vm-sizing)」のセクションを参照してください。

## <a name="template-machine-configuration"></a>テンプレート マシンの構成

このセクションの手順では、テンプレート VM を設定するために以下の操作を行う方法を示します。

1. 開発ツールをインストールします。
1. Web ブラウザー用のデバッガー拡張機能をインストールします。
1. ファイアウォール設定を更新します。

### <a name="install-development-tools"></a>開発ツールをインストールする

1. 任意の Web ブラウザーをインストールします。  
1. [Node.js](https://nodejs.org) をインストールします。

    ```bash
    sudo apt install nodejs
    ```

1. [ノード パッケージ マネージャー](https://www.npmjs.com/)をインストールします。これは、React、Redux、および JSX のインストールに使用されます。

    ```bash
    sudo apt install npm
    ```

1. [Visual Studio Code](https://code.visualstudio.com/docs/setup/linux) をインストールします。
1. [Visual Studio Code 用の React ネイティブ ツール拡張機能](https://marketplace.visualstudio.com/items?itemName=msjsdiag.vscode-react-native)をインストールします。
1. 必要に応じて、[Redux](https://marketplace.visualstudio.com/search?term=Redux&target=VSCode&category=All%20categories&sortBy=Relevance) と [JSX](https://marketplace.visualstudio.com/search?term=JSX&target=VSCode&category=All%20categories&sortBy=Relevance) を使用する開発用の拡張機能をインストールします。

「[Create React App (React アプリを作成する)](https://create-react-app.dev/)」は、ReactApp を作成するために正式にサポートされている方法であり、npm 5.2 以降を使用している場合は、追加の構成は必要ありません。  「Create React App (React アプリを作成する)」を使用する手順については、[入門](https://create-react-app.dev/docs/getting-started)ドキュメントを参照してください。

React ベースの Web サイトに必要なその他のコンポーネントは、NPM を使用して特定のアプリケーションにインストールされます。 たとえば、Redux ライブラリと JSX ライブラリをインストールするには、次のコマンドを入力します。

```bash
npm install react-redux
npm install react-jsx
```

### <a name="install-debugger-extensions"></a>デバッガー拡張機能をインストールする

ブラウザー用に React Developer Tools 拡張機能をインストールして、React コンポーネントの調査とパフォーマンス情報の記録ができるようにします。  

- [React Developer Tools Edge アドオン](https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil)
- [React Developer Tools Chrome 拡張機能](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
- [React Developer Tools FireFox アドオン](https://addons.mozilla.org/firefox/addon/react-devtools/)

開発モードでアプリを実行するには、`npm start` 組み込みコマンドを使用します。  コマンドの出力に、ローカル URL とネットワーク URL が一覧表示されます。  HTTP の代わりに HTTPS を使用するには、[開発時に https を使用して React アプリを作成する](https://create-react-app.dev/docs/using-https-in-development)方法に関するページを参照してください。

### <a name="update-firewall-settings"></a>ファイアウォール設定の更新

Ubuntu の公式ビルドには [iptables](https://help.ubuntu.com/community/IptablesHowTo) がインストールしてあり、既定ですべての受信トラフィックを許可します。  ですが、VM のファイアウォールでそれより強い通信制限を行っている場合は、NodeJS サーバーへのトラフィックを許可する受信規則を追加します。  下の例では [iptables](https://help.ubuntu.com/community/IptablesHowTo) で 3000 番ポートへの通信を許可しています。

```bash
sudo iptables -I INPUT -p tcp -m tcp --dport 3000 -j ACCEPT
```

>[!IMPORTANT]
>インストラクターは、学生の Web サイトにアクセスするために、テンプレート VM または別のラボ VM を使用する必要があります。

## <a name="cost"></a>コスト

このクラスのコスト見積もりの例について説明します。  クラスの学生数は 25 名とします。 各学生には、20 時間の授業時間が予定されています。  予定された授業時間の他に、宿題や課題を行うための時間として、追加の 10 時間が各学生に割り当てられます。  選択した仮想マシンのサイズは **Small** で、20 ラボ ユニットです。

- 25 人の学生 &times; (予定された 20 時間 + 割り当てられた 10 時間) &times; 20 ラボ ユニット &times; 0.01 USD/時間 = 150.00 USD

> [!IMPORTANT]
> このコスト見積もりは、例を示すためだけのものです。  現在の価格の詳細については、「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」を参照してください。

## <a name="next-steps"></a>次のステップ

テンプレート イメージをラボに発行できるようになります。 手順の詳細については、「[テンプレート VM を発行する](how-to-create-manage-template.md#publish-the-template-vm)」セクションを参照してください。

ラボを設定するときは、次の記事を参照してください。

- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)
