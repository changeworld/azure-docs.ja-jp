---
title: Azure Lab Services を使用して Windows 上の R と RStudio でラボを設定する
description: Windows で RStudio を使用して R を教えるためにラボを設定する方法について説明します
author: emaher
ms.topic: how-to
ms.date: 08/26/2021
ms.author: enewman
ms.openlocfilehash: 8e748a3c1af845ddd9c29e71de5b319b4b4b0603
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176569"
---
# <a name="set-up-a-lab-to-teach-r-on-windows"></a>Windows で R を教えるためにラボを設定する

[R](https://www.r-project.org/about.html) は、統計に関するコンピューティングとグラフィックスで使用されるオープン ソースの言語です。  これは、遺伝学の統計分析を始め、自然言語処理、財務データの分析において使用されています。  R には、[対話型のコマンドライン](https://cran.r-project.org/doc/manuals/r-release/R-intro.html#Invoking-R-from-the-command-line) エクスペリエンスが用意されています。  [RStudio](https://www.rstudio.com/products/rstudio/) は、R 言語で使用できる対話型開発環境 (IDE) です。  無料版には、コード編集ツール、統合されたデバッグ エクスペリエンス、パッケージ開発ツールが用意されています。

この記事では、RStudio と R の、統計的コンピューティングを使用する必要があるクラスの構成要素としての側面のみを取り上げます。  [ディープ ラーニング](class-type-deep-learning-natural-language-processing.md)や、[Python と Jupyter Notebooks](class-type-jupyter-notebook.md) を扱う種類のクラスでは、RStudio の設定方法が異なります。  各記事では、マーケットプレース イメージの [Data Science Virtual Machine for Linux (Ubuntu)](https://azuremarketplace.microsoft.com/en-US/marketplace/apps/microsoft-dsvm.ubuntu-1804) を使用する方法について説明しています。それには、プレインストールされた RStudio など、多くの[データ サイエンス関連ツール](../machine-learning/data-science-virtual-machine/tools-included.md)が含まれています。  

## <a name="lab-account-configuration"></a>ラボ アカウントの構成

このラボを設定するには、Azure サブスクリプションとラボ アカウントが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 Azure サブスクリプションを取得したら、Azure Lab Services で新しいラボ アカウントを作成できます。 新しいラボ アカウントの作成の詳細については、[ラボ アカウントの設定方法](./tutorial-setup-lab-account.md)に関するチュートリアルを参照してください。 既存のラボ アカウントを使用することもできます。

### <a name="lab-account-settings"></a>ラボ アカウントの設定

次の表の説明に従って、ラボ アカウントの設定を有効にします。

| ラボ アカウントの設定 | Instructions |
| -------------------- | ----- |
| [ピア仮想ネットワークの有効化](how-to-connect-peer-virtual-network.md) | 次の場合に有効にします。<ul><li>クラスに共有の Microsoft R Server が必要。</li><li>クラスに、学生用 VM ではなく外部に格納するための大きなデータ ファイルが必要。</li></ul> |

> [!IMPORTANT]
> ピア仮想ネットワークを有効にすることを選ぶ場合は、ラボを作成する前にそれを行う必要があります。

## <a name="lab-configuration"></a>ラボの構成

新しいラボを作成し、必要な設定を適用する手順については、「[チュートリアル: クラスルーム ラボを設定する](tutorial-setup-classroom-lab.md)」を参照してください。  ラボを作成するときには、以下の設定を適用します。

| ラボの設定 | 値と説明 |
| ------------ | ------------------ |
| 仮想マシンのサイズ | Small GPU (Compute)|
| VM イメージ | Windows 10 Pro。 バージョン 2004 |

## <a name="external-resource-configuration"></a>外部リソースの構成

クラスによっては、大きなデータ ファイルなどのファイルを外部に保存する必要があります。  オプションとセットアップ手順については、[Azure Lab Services で外部ファイル ストレージを使用する](how-to-attach-external-storage.md)ことに関するページを参照してください。

学生のために共有の RStudio Server を用意する場合は、ラボを作成する前にサーバーをセットアップしておく必要があります。  共有サーバーをセットアップする方法の詳細については、「[Azure Lab Services で共有リソースを使用してラボを作成する方法](how-to-create-a-lab-with-shared-resource.md)」を参照してください。  RStudio Server を作成する手順については、「[Debian および Ubuntu 用 RStudio Server のダウンロード](https://www.rstudio.com/products/rstudio/download-server/debian-ubuntu/)」と「[オープンソース版 Rstudio Server へのアクセス](https://support.rstudio.com/hc/en-us/articles/200552306-Getting-Started)」を参照してください。

## <a name="template-configuration"></a>テンプレートの構成

テンプレート マシンが作成されたら、マシンを起動し、そこに接続して [R をインストール](https://docs.rstudio.com/resources/install-r/)し、[RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) をインストールします。  

### <a name="install-r"></a>R をインストールする

1. [Windows 用 R の最新のインストーラー](https://cran.r-project.org/bin/windows/base/release.html)をダウンロードします。  使用可能なバージョンの完全な一覧については、[Windows 用 R のダウンロード ページ](https://cran.r-project.org/bin/windows/base/)を参照してください。
2. インストーラーを実行します。
    1. **[セットアップ言語の選択]** プロンプトで、希望する言語を選択し、 **[OK]** を選択します
    2. インストーラーの **[情報]** ページで、使用許諾契約書の内容を確認します。  **[次へ]** を選択して契約に同意し、続行します。
    3. **[インストール先の場所の選択]** ページで、既定のインストール場所をそのまま使用し、 **[次へ]** をクリックします。
    4. **[コンポーネントの選択]** ページで、必要に応じて **[32 ビット ファイル]** オプションをオフにします。  R の 32 ビット バージョンと 62 ビット バージョンの両方を実行することの詳細については、「[同じマシンに 32 ビットと 64 ビットの両方の R をインストールできますか?](https://cran.r-project.org/bin/windows/base/rw-FAQ.html#Can-both-32_002d-and-64_002dbit-R-be-installed-on-the-same-machine_003f)」というよく寄せられる質問を参照してください。
    5. **[スタートアップ オプション]** ページで、スタートアップ オプションを **[いいえ (既定値を受け入れる)]** のままにします。  R のグラフィカル ユーザー インターフェイス (GUI) で別個のウィンドウ (SDI) やプレーンテキストのヘルプを使用する場合は、 **[はい (スタートアップをカスタマイズする)]** ラジオ ボタンを選択し、ウィザードの後続のページでスタートアップ オプションを変更します。
    6. **[スタート メニューのフォルダーの選択]** ページで、**[次へ]** を選択します。
    7. **[追加のタスクの選択]** ページで、必要に応じて **[デスクトップ ショートカットを作成する]** を選択します。  **[次へ]** を選択します。
    8. **[インストール中]** ページで、インストールの完了を待ちます。
    9. **[Windows 用 R の完了]** ページで、 **[完了]** をクリックします。

PowerShell を使用して R のインストールを実行することもできます。  このコード例は、32 ビット コンポーネントなしで R をインストールし、最新バージョンの R 用のデスクトップ アイコンを追加する方法を示しています。インストーラーのコマンドライン オプションの完全な一覧については、[セットアップのコマンドライン パラメーター](https://jrsoftware.org/ishelp/index.php?topic=setupcmdline)に関するページを参照してください。

```powershell
#Avoid prompt to setup Internet Explorer if we must parse download page
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Internet Explorer\Main" -Name "DisableFirstRunCustomize" -Value 2

$outputfile = "R-win.exe"

$result = Invoke-WebRequest "https://cran.r-project.org/bin/windows/base/release.html" -OutFile $outputfile -PassThru

#Check if we need to parse the result ourselves, to find the latest version of R
if ($result.StatusCode -eq '200' -and $result.Headers["Content-Type"] -eq 'text/html')
{
    $metaTag = $result.ParsedHtml.Head.children | Where-Object {$_.nodeName -eq 'META'}
    if ($metaTag.content  -match "R-\d+\.\d+\.\d+-win.exe"){
        $outputfile = $Matches.0

        #Download latest version
        Invoke-WebRequest "https://cran.r-project.org/bin/windows/base/$outputfile" -OutFile $outputfile
    }else{
        Write-Error "Unable to find latest version of R installer.  Go to https://cran.r-project.org/bin/windows/base/release.html to download manually."
    }
}

#Install Silently
$installPath = Get-Item -Path $outputfile
Start-Process -FilePath $installPath.FullName -ArgumentList "/VERYSILENT /LOG=r-install.log /NORESTART /COMPONENTS=""main,x64,translations"" /MERGETASKS=""desktopicon"" /LANG=""en""" -NoNewWindow -Wait
```

### <a name="install-rstudio"></a>RStudio をインストールする

R をローカルにインストールしたので、RStudio IDE をインストールできます。  RStudio Desktop の無料バージョンをインストールします。  使用可能なすべてのバージョンについては、[RStudio のダウンロード](https://www.rstudio.com/products/rstudio/download/)に関するページを参照してください。

1. Windows 10 用の [R Studio のインストーラー](https://www.rstudio.com/products/rstudio/download/#download)をダウンロードします。  インストーラー ファイルは、`rstudio-{version}.exe` という形式になります。  
2. RStudio インストーラーを実行します。
    1. **RStudio セットアップ** ウィザードの **[RStudio セットアップへようこそ]** ページで、 **[次へ]** を選択します。
    2. **[インストール場所の選択]** ページで、 **[次へ]** を選択します。
    3. **[スタート メニュー フォルダーの選択]** ページで、 **[インストール]** を選択します。
    4. **[インストール中]** ページで、インストールの完了を待ちます。
    5. **[RStudio セットアップの完了]** ページで、 **[完了]** を選択します。

PowerShell を使用して RStudio のインストール手順を実行するには、以下のコマンドを実行します。  コマンドを実行する前に、[RStudio のダウンロード](https://www.rstudio.com/products/rstudio/download/)に関するページを参照して、その RStudio バージョンが使用可能であることを確認してください。

```powershell
$rstudiover="1.4.1717"
$outputfile = "RStudio-$rstudiover.exe"

#Download installer executable
Invoke-WebRequest "https://download1.rstudio.org/desktop/windows/RStudio-$rstudiover.exe" -OutFile $outputfile

#Install RStudio silently
$installPath = Get-Item -Path $outputfile
Start-Process -FilePath $installPath.FullName -ArgumentList "/S" -NoNewWindow -Wait
```

### <a name="cran-packages"></a>CRAN パッケージ

「[便利な R パッケージのクイック リスト](https://support.rstudio.com/hc/articles/201057987-Quick-list-of-useful-R-packages)」という記事に示されているように、R の対話型セッションで `install.packages(“package name”)` コマンドを使用します。  または、RStudio で [ツール]-> [パッケージのインストール] メニュー項目を使用します。

パッケージを見つける際に情報が必要な場合は、[タスク別のパッケージの一覧](https://cran.r-project.org/web/views/)や[アルファベット順のパッケージの一覧](https://cloud.r-project.org/web/packages/available_packages_by_name.html)を参照してください。

## <a name="cost"></a>コスト

このクラスのコスト見積もりの例について説明します。  クラスの学生数は 25 名とします。 各学生には、20 時間の授業時間が予定されています。  予定された授業時間の他に、宿題や課題を行うための時間として、追加の 10 時間が各学生に割り当てられます。  ここで選択した仮想マシンのサイズは **Small GPU (Compute)** で、139 ラボ ユニットです。

25 人の学生 &times; (予定時間の 20 時間 + クォータ時間の 10 時間) &times; 139 ラボ ユニット &times; 0.01 米国ドル/時間 = 1042.5 米国ドル

> [!IMPORTANT]
> このコスト見積もりは、例を示すためだけのものです。  現在の価格の詳細については、「[Azure Lab Services の価格](https://azure.microsoft.com/pricing/details/lab-services/)」を参照してください。

## <a name="next-steps"></a>次のステップ

テンプレート イメージをラボに発行できるようになります。 手順の詳細については、「[テンプレート VM を発行する](how-to-create-manage-template.md#publish-the-template-vm)」セクションを参照してください。

ラボを設定するときは、次の記事を参照してください。

- [ユーザーの追加](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [クォータを設定する](how-to-configure-student-usage.md#set-quotas-for-users)
- [スケジュールを設定する](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [登録リンクを学生に電子メールで送る](how-to-configure-student-usage.md#send-invitations-to-users)