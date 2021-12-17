---
title: Azure Lab Services を使用して、Windows 上の React を使ったフロントエンド開発を教えるためのラボを立ち上げる
description: React を使用したフロントエンド開発を教えるためのラボの設定方法について説明します。
author: emaher
ms.topic: how-to
ms.date: 05/16/2021
ms.author: enewman
ms.openlocfilehash: f7cd012e609b253269d1aa04137e8e7e94e0ffde
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176684"
---
# <a name="set-up-lab-for-react-on-windows"></a>Windows 上の React のラボを設定する

[React](https://reactjs.org/) は、ユーザー インターフェイス (UI) を構築するための一般的な JavaScript ライブラリです。 React は、Web サイト用に再利用可能なコンポーネントを作成するための宣言型の方法です。  JavaScript ベースのフロントエンド開発には、他にも多くの一般的なライブラリがあります。  ラボを作成する際に、これらのライブラリのいくつかを使用します。  [Redux](https://redux.js.org/) は、JavaScript アプリ用の予測可能な状態コンテナーを提供するライブラリであり、多くの場合、React を補完するために使用されます。 [JSX](https://reactjs.org/docs/introducing-jsx.html) は、UI の外観を記述するために React でよく使用される、JavaScript のライブラリ構文拡張機能です。  [NodeJS](https://nodejs.org/) は、React アプリケーション用の Web サーバーを実行する便利な方法です。

この記事では、React Web 開発クラスに必要な、開発環境用の [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)、およびツールやライブラリをインストールする方法について説明します。

## <a name="lab-configuration"></a>ラボの構成

このラボを設定するには、Azure サブスクリプションとラボ アカウントが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 Azure サブスクリプションを取得したら、Azure Lab Services で新しいラボ アカウントを作成できます。 新しいラボ アカウントの作成の詳細については、[ラボ アカウントの設定方法](./tutorial-setup-lab-account.md)に関するチュートリアルを参照してください。 既存のラボ アカウントを使用することもできます。

### <a name="lab-account-settings"></a>ラボ アカウントの設定

次の表の説明に従って、ラボ アカウントの設定を有効にします。 Azure Marketplace イメージを有効にする方法の詳細については、「[ラボ作成者が利用できる Azure Marketplace イメージを指定する](./specify-marketplace-images.md)」を参照してください。

| ラボ アカウントの設定 | Instructions |
| -------------------- | ----- |
| Marketplace イメージ | ラボ アカウント内で使用するために、'Visual Studio 2019 Community (最新リリース) on Windows Server 2019 (x64)' イメージを有効にします。 |

### <a name="lab-settings"></a>ラボの設定

仮想マシン (VM) の推奨サイズは、学生が実行する必要があるワークロードの種類によって異なります。  

| ラボの設定 | 値と説明 |
| ------------ | ------------------ |
| 仮想マシンのサイズ | **中** |

ワークロードをテストして、より大きなサイズが必要かどうかを確認することをお勧めします。  各サイズの詳細については、「[VM のサイズ設定](administrator-guide.md#vm-sizing)」のセクションを参照してください。

## <a name="template-machine-configuration"></a>テンプレート マシンの構成

このセクションの手順では、テンプレート VM を設定するために以下の操作を行う方法を示します。

1. 開発ツールをインストールします。
1. Web ブラウザー用のデバッガー拡張機能をインストールします。
1. ファイアウォール設定を更新します。

### <a name="install-development-tools"></a>開発ツールのインストール

'Visual Studio 2019 Community (最新リリース) on Windows Server 2019 (x64)' イメージには、[Visual Studio 2019](https://visualstudio.microsoft.com/vs/) に必要な [**Node.js の開発** ワークロード](/visualstudio/javascript/tutorial-nodejs-with-react-and-jsx?view=vs-2019&preserve-view=true#prerequisites)がすでにインストールされています。

1. お好みの Web ブラウザーをインストールします。  イメージには、既定で Internet Explorer がインストールされています。
1. [Node.js](https://nodejs.org) の Web サイトにアクセスし、**ダウンロード** ボタンを選択します。  最新の長期サービス (LTS) バージョン、最新の機能が備わった現在のバージョン、または以前のリリースを使用できます。  NodeJS をインストールすると、[ノード パッケージ マネージャー](https://www.npmjs.com/)もインストールされます。これは、React、Redux、および JSX のインストールに使用されます。
1. 必要に応じて、[Visual Studio 2019 を最新のリリースに更新](/visualstudio/install/update-visual-studio?view=vs-2019&preserve-view=true)します。

React ベースの Web サイトに必要なその他のコンポーネントは、NPM を使用して特定のアプリケーションにインストールされます。  NPM パッケージを追加するには、[Visual Studio での NPM パッケージの管理](/visualstudio/javascript/npm-package-management?view=vs-2019&preserve-view=true#add-npm-packages)に関するページを参照してください。  

たとえば、プロジェクトで [Node.js 対話型ウィンドウ](/visualstudio/javascript/nodejs-interactive-repl?view=vs-2019&preserve-view=true)を使用する場合は、次のコマンドを入力して React、Redux、および JSX ライブラリをインストールします。

```bash
.npm install react
.npm install react-dom
.npm install react-redux
.npm install react-jsx
```

Visual Studio で初めての React を使用した Node.js アプリを作成するには、「[チュートリアル: Visual Studio で Node.js と React のアプリを作成する](/visualstudio/javascript/tutorial-nodejs-with-react-and-jsx?view=vs-2019&preserve-view=true)」を参照してください。

### <a name="install-debugger-extensions"></a>デバッガー拡張機能をインストールする

ブラウザー用に React Developer Tools 拡張機能をインストールして、React コンポーネントの調査とパフォーマンス情報の記録ができるようにします。  

- [React Developer Tools Edge アドオン](https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil)
- [React Developer Tools Chrome 拡張機能](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
- [React 開発者ツール FireFox アドオン](https://addons.mozilla.org/firefox/addon/react-devtools/)

### <a name="update-firewall-settings"></a>ファイアウォール設定の更新

既定では、Node.js サーバーへの受信トラフィックはブロックされます。  実行中の学生の Web サイトにアクセスしたい場合は、インバウンドのファイアウォール規則を追加して、トラフィックを許可してください。  デバッグ中に使用されるポートを確認するには、**アプリケーション ポート** プロジェクト プロパティを参照してください。  次の例では、[New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule?view=windowsserver2019-ps&preserve-view=true) PowerShell コマンドレットを使用して、ポート 1337 へのアクセスを許可しています。  

```powershell
New-NetFirewallRule -DisplayName "Allow access to Port 1337" -Direction Inbound -LocalPort 1337 -Protocol TCP -Action Allow
```

>[!IMPORTANT]
>インストラクターは、学生の Web サイトにアクセスするために、テンプレート VM または別のラボ VM を使用する必要があります。

## <a name="cost"></a>コスト

このクラスのコスト見積もりの例について説明します。  クラスの学生数は 25 名とします。 各学生には、20 時間の授業時間が予定されています。  予定された授業時間の他に、宿題や課題を行うための時間として、追加の 10 時間が各学生に割り当てられます。  ここで選択した仮想マシンのサイズは、55 ラボ ユニットの **中** です。

- 25 人の学生 &times; (予定された 20 時間 + 割り当てられた 10 時間) &times; 55 ラボ ユニット &times; 0.01 USD/時間 = 412.50 USD

> [!IMPORTANT]
> このコスト見積もりは、例を示すためだけのものです。  現在の価格の詳細については、「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」を参照してください。

## <a name="next-steps"></a>次のステップ

テンプレート イメージをラボに発行できるようになります。 手順の詳細については、「[テンプレート VM を発行する](how-to-create-manage-template.md#publish-the-template-vm)」セクションを参照してください。

ラボを設定するときは、次の記事を参照してください。

- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)
