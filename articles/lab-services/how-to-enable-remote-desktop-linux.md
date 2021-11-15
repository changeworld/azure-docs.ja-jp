---
title: Azure Lab Services の Linux 向けグラフィカル リモート デスクトップを有効にする | Microsoft Docs
description: Azure Lab Services のラボで Linux 仮想マシン向けリモート デスクトップを有効にする方法について説明します。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: aaeec2cc74e24061c9c022fdc4526732030c1f97
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232681"
---
# <a name="enable-graphical-remote-desktop-for-linux-virtual-machines-in-azure-lab-services"></a>Azure Lab Services の Linux 仮想マシンでグラフィカル リモート デスクトップを有効にする
この記事では、次のタスクの手順について説明します。

- Linux VM 向けのグラフィカル リモート デスクトップ セッションを有効にする
- RDP (リモート デスクトップ プロトコル) または X2Go リモート デスクトップ クライアントを使用して Linux VM に接続する方法

## <a name="set-up-graphical-remote-desktop-solution"></a>グラフィカル リモート デスクトップ ソリューションを設定する
ラボが **Linux** イメージから作成された場合、講師が SSH を使用してコマンド ラインからテンプレート VM に接続できるように、**SSH** (Secure Shell) アクセスが自動的に構成されます。  同様に、テンプレート VM が発行された際には、生徒も SSH を使用して VM に接続することができます。

**GUI** (グラフィカル ユーザー インターフェイス) を使用して Linux VM に接続するには、**RDP** または **X2Go** を使用することをお勧めします。  この記事の残りの部分では、ラボのテンプレート VM で RDP または X2Go を設定する手順について説明します。

> [!NOTE]
> Linux では、[Xrdp](https://en.wikipedia.org/wiki/Xrdp) と呼ばれるオープンソース バージョンの RDP を使用します。  わかりやすくするために、この記事では RDP という用語を使用します。

### <a name="rdp-setup"></a>RDP の設定
RDP を使用するには、講師が次のことを行う必要があります。
  - リモート デスクトップ接続を有効にします。これは、VM のポートを RDP 用に開くために必要です。
  - RDP リモート デスクトップ サーバーをインストールします。
  - Linux のグラフィカル デスクトップ環境をインストールします。

### <a name="x2go-setup"></a>X2Go の設定
X2Go を使用するには、講師が次のことを行う必要があります。
- X2Go リモート デスクトップ サーバーをインストールします。
- Linux のグラフィカル デスクトップ環境をインストールします。

X2Go では、SSH 用に既に有効になっているのと同じポートが使用されます。  そのため、VM で X2Go 用にポートを開くために、追加の構成を行う必要はありません。

> [!NOTE]
> Ubuntu LTS 18.04 など、場合によっては、X2Go の方が高いパフォーマンスが得られます。  RDP を使用する場合で、グラフィカル デスクトップ環境とのやりとりの際に待機時間が発生する場合は、X2Go を試すことを検討してください。パフォーマンスが向上する可能性があります。

> [!IMPORTANT]
>  一部のマーケットプレース イメージには、グラフィカル デスクトップ環境とリモート デスクトップ サーバーが既にインストールされています。  たとえば、[Linux 用 Data Science Virtual Machine (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) には、既に [XFCE と X2Go Server がインストールされており、クライアント接続を受け入れるように構成されています](../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#x2go)。

## <a name="enable-remote-desktop-connection-for-rdp"></a>RDP のリモート デスクトップ接続を有効にする

この手順は、RDP を使用して接続する場合にのみ必要です。  X2Go を使用する場合は、次のセクションに進んで結構です (X2Go では SSH ポートが使用されるため)。

1.  ラボの作成時に、講師は必要に応じて **リモート デスクトップ接続を有効にする** ことができます。  RDP リモート デスクトップ セッションに必要な Linux VM 上のポートを開くには、講師がこのオプションを **有効** にする必要があります。  このオプションを **無効** のままにすると、SSH 用のポートのみが開かれます。
  
    ![[New lab]\(新しいラボ\) ウィンドウと [Enable Remote Desktop Connection]\(リモート デスクトップ接続の有効化\) のスクリーンショット。](./media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

2. **[Enabling Remote Desktop Connection]\(リモート デスクトップ接続の有効化\)** メッセージ ボックスで、 **[Continue with Remote Desktop]\(リモート デスクトップで続行\)** を選択します。 

    ![Linux イメージのリモート デスクトップ接続を有効にする](./media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

## <a name="install-rdp-or-x2go"></a>RDP または X2Go のインストール

ラボを作成した後、講師は、グラフィカル デスクトップ環境とリモート デスクトップ サーバーがテンプレート VM にインストールされていることを確認する必要があります。  講師は、まず SSH を使用してテンプレート VM に接続し、以下のものに対応したパッケージをインストールする必要があります。
- RDP または X2Go リモート デスクトップ サーバー。
- グラフィカル デスクトップ環境 ([GNOME](https://www.gnome.org/)、[MATE](https://mate-desktop.org/)、[XFCE](https://www.xfce.org/)、[Xubuntu](https://xubuntu.org/) など)。

この設定が完了したら、講師は **Microsoft リモート デスクトップ (RDP)** クライアントまたは **X2Go** クライアントを使用してテンプレート VM に接続できるようになります。

テンプレート VM を設定するには、次の手順に従います。

1. ツール バーに **[Customize template]\(テンプレートのカスタマイズ\)** が表示されていれば、それを選択します。 次に、 **[Customize template]\(テンプレートのカスタマイズ\)** ダイアログ ボックスで **[続行]** を選択します。 この操作によりテンプレート VM が開始されます。  

    ![テンプレートのカスタマイズ](./media/how-to-enable-remote-desktop-linux/customize-template.png)
1. テンプレート VM が起動したら、ツール バーで **[Connect template]\(接続テンプレート\)** を選択し、それから **[Connect via SSH]\(SSH 経由で接続する\)** を選択できます。 

    ![ラボの作成後に RDP 経由でテンプレートに接続する](./media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
1. **[仮想マシンに接続する]** ダイアログ ボックスが表示されます。 テキスト ボックスの横にある **[コピー]** ボタンを選択して、クリップボードにコピーします。 SSH 接続情報を保存します。 仮想マシンに接続するには、SSH ターミナル ([Putty](https://www.putty.org/) など) からこの接続情報を使用します。
 
    ![SSH 接続文字列](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

1. 最後に、任意のグラフィカル デスクトップ環境と共に、RDP または X2Go をインストールします。

  最適なパフォーマンスを得るには、通常 XFCE グラフィカル デスクトップを使用し、ユーザーは X2Go を使用してデスクトップに接続することをお勧めします。  Ubuntu で X2Go を使用して XFCE を設定するには、次の手順に従います。
    - [X2Go のインストールと構成](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/LinuxGraphicalDesktopSetup/XFCE_Xubuntu/ReadMe.md)

  代わりに、ユーザーが RDP を使用してグラフィカル デスクトップに接続する必要がある場合は、Ubuntu の次の手順に従います。
    - [RDP のインストールと構成](../virtual-machines/linux/use-remote-desktop.md)

### <a name="using-gnome-or-mate-graphical-desktops"></a>GNOME または MATE グラフィカル デスクトップを使用する

GNOME または MATE グラフィカル デスクトップ環境の場合、VM が Azure Lab Services で正常に動作するために必要な、Azure Linux エージェントとのネットワーク競合が発生する可能性があります。  Ubuntu 18.04 LTS に GNOME と MATE のどちらかをインストールして使用するときに、このネットワーク競合が起こると、次の副作用が発生します。
 - イメージを使用して Lab を作成しようとすると、次のメッセージが出て失敗します: **“Communication could not be established with the VM agent. Please verify that the VM agent is enabled and functioning.”** (VM エージェントとの通信を確立できませんでした。VM エージェントが有効になり、機能していることを確認してください。)  
 - 自動シャットダウン設定を有効にしている場合に、学生 VM を発行しようとすると応答が停止します。
 - 学生 VM のパスワードをリセットしようとすると応答が停止します。

次の手順に従って、Ubuntu で GNOME または MATE グラフィカル デスクトップを設定することをお勧めします。  この手順には、Ubuntu 18.04 LTS に存在するネットワーク競合に対する修正が含まれています。  これは、GNOME と MATE を Ubuntu 20.04 LTS と 21.04 LTS にインストールする場合にも対応しています。
 - [GNOME/RDP と合致/X2go をインストールして構成する](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/LinuxGraphicalDesktopSetup/GNOME_MATE/ReadMe.md)

## <a name="connect-to-the-template-vm-via-the-gui"></a>GUI を使用してテンプレート VM に接続する

テンプレート VM が設定されたら、講師は **Microsoft リモート デスクトップ (RDP)** クライアントまたは **X2Go** クライアントを使用して、GUI 経由で接続できるようになります。  使用するクライアントは、テンプレート VM でリモート デスクトップ サーバーとして構成されているのが、RDP なのか、それとも X2Go なのかによって決まります。  

### <a name="microsoft-remote-desktop-rdp-client"></a>Microsoft リモート デスクトップ (RDP) クライアント

RDP (Microsoft リモート デスクトップ) クライアントは、RDP が構成されているテンプレート VM に接続するために使用されます。  リモート デスクトップ クライアントは、Windows、Chromebook、Mac などで使用できます。  詳細については、[リモート デスクトップ クライアントに関する記事](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)を参照してください。

テンプレート VM への接続に使用するコンピューターの種類に応じて、次の手順に従います。

- Windows
  1. ラボのツールバーで **[テンプレートへの接続]** をクリックし、 **[RDP 経由で接続]** を選択してテンプレート VM に接続します。 
  1. RDP ファイルを保存し、それを使用してリモート デスクトップ クライアント経由でテンプレート VM に接続します。 
  1. 通常、リモート デスクトップ クライアントは Windows で既にインストールされ、構成されています。  そのため、RDP ファイルをクリックして開き、リモート セッションを開始するだけで済みます。

- Mac
  1. ラボのツールバーで **[テンプレートへの接続]** をクリックし、 **[RDP 経由で接続]** を選択して RDP ファイルを保存します。  
  1. その後、ハウツー記事「[MAC から RDP を使用して VM に接続する](connect-virtual-machine-mac-remote-desktop.md)」を参照します。

- Chromebook
  1. ラボのツールバーで **[テンプレートへの接続]** をクリックし、 **[RDP 経由で接続]** を選択して RDP ファイルを保存します。  
  1. その後、ハウツー記事「[Chromebook から RDP を使用して VM に接続する](connect-virtual-machine-chromebook-remote-desktop.md)」を参照します。

### <a name="x2go-client"></a>X2Go クライアント

X2Go クライアントは、X2Go が構成されているテンプレート VM に接続するために使用されます。  テンプレート VM の SSH 接続情報を使用して、ハウツー記事「[X2Go を使用して VM に接続する](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-x2go)」の手順に従ってください。

## <a name="next-steps"></a>次のステップ
講師がテンプレート VM で RDP または X2Go を設定し、発行したら、生徒は GUI リモート デスクトップまたは SSH を使用して VM に接続できるようになります。

詳細については、次を参照してください。
 - [Linux VM に接続する](how-to-use-remote-desktop-linux-student.md)
