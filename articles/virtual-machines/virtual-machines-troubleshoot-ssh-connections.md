<properties
	pageTitle="SSH で Azure VM に接続できない | Microsoft Azure"
	description="Linux を実行している Azure virtual machine に対する Secure Shell (SSH) 接続のトラブルシューティング。"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/25/2015"
	ms.author="dkshir"/>

# Linux ベースの Azure virtual machine に対する Secure Shell (SSH) 接続のトラブルシューティング

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]この記事では、クラシック デプロイメント モデルまたはリソース マネージャーのデプロイト モデルを使用して作成された仮想マシンでの SSH 接続のトラブルシューティングについて説明します。


Linux ベースの Azure virtual machine に対する SSH エラーには、さまざまな原因が考えられます。この記事は、原因を特定してエラーを修正するために役立ちます。

> [AZURE.NOTE]この記事は、Linux を実行する Azure virtual machines にのみ適用されます。Windows を実行する Azure virtual machines に対する接続のトラブルシューティングを行う場合は、[この記事](virtual-machines-troubleshoot-remote-desktop-connections.md)を参照してください。

## Azure カスタマー サポートへの問い合わせ

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](http://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。

または、Azure サポート インシデントを送信できます。その場合は、[Azure サポートのサイト](http://azure.microsoft.com/support/options/)に移動して、**[サポートの要求]** をクリックします。Azure サポートの使用方法の詳細については、「[Azure Support FAQ (Microsoft Azure サポートに関する FAQ)](http://azure.microsoft.com/support/faq/)」を参照してください。


## 基本的な手順

一般的な SSH 接続エラーを解決するには、次の手順を試します。

1. [Azure ポータル](https://portal.azure.com)からリモート アクセスをリセットします。**[すべて参照]**、**[仮想マシン (クラシック)]**、ご使用の Windows 仮想マシン、**[リモート アクセスのリセット]** の順にクリックします。

	![リモート アクセスのリセット](./media/virtual-machines-troubleshoot-ssh-connections/Portal-SSH-Reset-Windows.png)

2. 仮想マシンを再起動します。[Azure プレビュー ポータル](https://portal.azure.com)から、**[すべて参照]**、**[仮想マシン (クラシック)]**、ご使用の Windows 仮想マシン、**[再起動]** の順にクリックします。[Microsoft Azure 管理ポータル](https://manage.windowsazure.com)から、**[仮想マシン]**、**[インスタンス]** の順に開いて、**[再起動]** をクリックします。

3. [仮想マシンのサイズを変更します](https://msdn.microsoft.com/library/dn168976.aspx)。

4. 「[Linux 仮想マシンのパスワードまたは SSH をリセットする方法](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)」の指示に従って、仮想マシンで以下の操作を行います。

	- パスワードまたは SSH キーをリセットする
	- 新しい sudo ユーザー アカウントを作成する
	- SSH 構成をリセットする

## 詳細なトラブルシューティング

それでも SSH クライアントが仮想マシン上の SSH サービスに到達できない場合は、いくつかの理由が考えられます。関連のあるコンポーネントを次に示します。

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot1.png)

次のセクションは、エラーのソースを独立させて、解決策と回避策を見つけ出すのに役立ちます。

### トラブルシューティングする前の手順

まず、Azure ポータルで仮想マシンの状態を確認します。

[Microsoft Azure 管理ポータル](https://manage.windowsazure.com)の操作:

1. **[仮想マシン]** > *VM 名*をクリックします。
2. VM の **[ダッシュ ボード]** をクリックして、状態を確認します。
3. コンピューティング、記憶域、およびネットワーク リソースの最近のアクティビティを確認するには、**[監視]** をクリックします。
4. SSH トラフィックのエンドポイントがあることを確認するには、**[エンドポイント]** をクリックします。

[Azure プレビュー ポータル](https://portal.azure.com)の操作:

1. **[参照]** > **[仮想マシン]** > *VM 名*をクリックします。Azure リソース マネージャーで作成された仮想マシンの場合は、**[参照]**、**[仮想マシン (v2)]**、*VM 名*の順にクリックします。仮想マシンの状態ウィンドウには、"**実行中**" と表示されます。コンピューティング、記憶域、およびネットワーク リソースの最近のアクティビティを確認するには、下にスクロールします。
2. エンドポイント、IP アドレスなどの設定を確認するには、**[設定]** をクリックします。

ネットワーク接続を確認するには、構成されているエンドポイントを確認します。また、HTTP などの別のプロトコルや他のサービスを使用して、VM に到達できるかどうかを確認します。

以上の手順を完了したら、SSH 接続を再試行してください。


### トラブルシューティングの手順

お使いのコンピューター上の SSH クライアントは、以下の問題または誤構成が原因で Azure virtual machine の SSH サービスに到達できない場合があります。

- SSH クライアント コンピューター
- 組織の境界デバイス
- クラウド サービス エンドポイントとアクセス制御リスト (ACL)
- ネットワーク セキュリティ グループ
- Linux ベースの Azure virtual machine

#### ソース 1: SSH クライアント コンピューター

エラーのソースであるコンピューターを排除するには、そのコンピューターから他のオンプレミスの Linux ベース コンピューターに SSH 接続できることを確認します。

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot2.png)

接続できない場合は、そのコンピューターで以下を確認してください。

- 受信または送信の SSH トラフィック (TCP 22) をブロックしているローカル ファイアウォールの設定
- SSH 接続を妨げている、ローカルでインストールされたクライアント プロキシのソフトウェア
- SSH 接続を妨げている、ローカルでインストールされたネットワーク監視ソフトウェア
- トラフィックを監視する、あるいは特定種類のトラフィックを許可または禁止する別の種類のセキュリティ ソフトウェア

いずれの場合も、該当ソフトウェアを一時的に無効にし、オンプレミス コンピューターへの SSH 接続を試すことで、原因を確認してください。次に、ネットワーク管理者とともに、SSH 接続が可能になるようにそのソフトウェアの設定を修正してください。

証明書認証を使用している場合は、ホーム ディレクトリの .ssh フォルダーに対してこれらのアクセス許可があることを確認します。

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id\_rsa (またはプライベート キーが格納されている他のファイル)
- Chmod 644 ~/.ssh/known\_hosts (SSH 経由で接続したホストが含まれます)

#### ソース 2: 組織のエッジ デバイス

エラーのソースである組織のエッジ デバイスを排除するには、インターネットに直接接続されているコンピューターから Azure VM に SSH 接続できるかを確認します。サイト間 VPN または ExpressRoute 接続で VM にアクセスする場合は、「[ソース 4: ネットワーク セキュリティ グループ](#nsg)」に進みます。

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot3.png)

インターネットに直接接続されているコンピューターがない場合は、新しい Azure virtual machine をリソース グループまたはクラウド サービス内に簡単に作成して使用できます。詳細については、「[Azure 上で Linux を実行する仮想マシンの作成](virtual-machines-linux-tutorial.md)」を参照してください。テストの完了後に、そのリソース グループまたは仮想マシンと、クラウド サービスを削除します。

インターネットに直接接続されているコンピューターとの SSH 接続を作成できるのなら、組織のエッジ デバイスで以下を確認してください。

- インターネットでの SSH トラフィックをブロックしている内部ファイアウォール
- SSH 接続を妨げているプロキシ サーバー
- SSH 接続を妨げている、エッジ ネットワーク内のデバイスで実行されている侵入検出またはネットワーク監視のソフトウェア

ネットワーク管理者と協力して、インターネットでの SSH トラフィックを許可するように組織のエッジ デバイスの設定を修正します。

#### ソース 3: クラウド サービス エンドポイントと ACL

エラーのソースであるクラウド サービス エンドポイントと ACL を排除するには、[クラシック デプロイメント モデル](../resource-manager-deployment-model.md)を使用して作成された VM については、同じ仮想ネットワーク内にある別の Azure VM から自身の VM に SSH 接続できるかを確認します。

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot4.png)

> [AZURE.NOTE]リソース マネージャーを使用して作成された仮想マシンについては、「[ソース 4: ネットワーク セキュリティ グループ](#nsg)」に進みます。

同じ仮想ネットワーク内に別の VM がない場合、新しい VM を簡単に作成することができます。詳細については、「[Azure 上で Linux を実行する仮想マシンの作成](virtual-machines-linux-tutorial.md)」を参照してください。テストの完了後に、追加した VM を削除してください。

同じ仮想ネットワーク内にある VM に対して SSH 接続を作成できる場合は、次の点を確認します。

- ターゲットの VM での SSH トラフィック向けエンドポイントの構成。エンドポイントのプライベート TCP ポートは、VM 上の SSH サービスがリッスンする TCP ポートと一致する必要があります (既定値は 22 です)。リソース マネージャーのデプロイメント モデルでテンプレートを使用して作成した VM の場合は、Azure プレビュー ポータルで **[参照]**、**[仮想マシン (v2)]**、*VM 名*、**[設定]**、**[エンドポイント]** の順に選択して、SSH TCP ポート番号を確認します。
- ターゲットの仮想マシンでの、SSH トラフィック向けエンドポイントの ACL。ACL を使用すると、発信元 IP アドレスに基づいて、インターネットからの受信トラフィックを許可または拒否するかを指定できます。ACL が正しく構成されていないと、そのエンドポイントへの SSH 受信トラフィックを受け取れない場合があります。ご利用になっているプロキシのパブリック IP アドレスからの受信トラフィック、または他のエッジ サーバーからの受信トラフィックが許可されているかを ACL で確認してください。詳細については、「[ネットワーク アクセス制御リスト (ACL) について](../virtual-network/virtual-networks-acl.md)」を参照してください。

問題のソースであるエンドポイントを排除するには、現在のエンドポイントを削除し、新しいエンドポイントを作成して、**SSH** 名を指定します (パブリックとプライベートのポート番号には TCP ポート 22)。詳細については、「[Azure での仮想マシンに対するエンドポイントの設定](virtual-machines-set-up-endpoints.md)」をご覧ください

<a id="nsg"></a>
#### ソース 4: ネットワーク セキュリティ グループ

ネットワーク セキュリティ グループでは、許可された受信トラフィックと送信トラフィックをより細かく制御できます。Azure virtual network 内のサブネットまたはクラウド サービスの全体に適用されるルールを作成することができます。ネットワーク セキュリティ グループ ルールで、インターネットからの SSH トラフィックが許可されていることを確認します。詳細については、「[ネットワーク セキュリティ グループについて](../traffic-manager/virtual-networks-nsg.md)」を参照してください。

#### ソース 5: Linux ベースの Azure virtual machine

最後に考えられる問題のソースは、Azure virtual machine 自体に関連するものです。

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot5.png)

[Linux 仮想マシンのパスワードまたは SSH をリセットする方法](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)をまだ実行していない場合は、指示に従って仮想マシンを操作します。

もう一度、コンピューターから接続を試みてください。まだ接続できない場合は、以下の問題が考えられます。

- SSH サービスがターゲット仮想マシンで実行されていない。
- SSH サービスが TCP ポート 22 をリッスンしていない。これをテストするには、ローカル コンピューターに telnet クライアントをインストールし、"telnet *cloudServiceName*.cloudapp.net 22" を実行します。これで、SSH エンドポイントに対する受信と送信の接続が VM で許可されているかどうかを確認できます。
- ターゲット仮想マシンのローカル ファイアウォールに、受信または送信の SSH トラフィックを妨げているルールがある。
- Azure virtual machine で実行されている侵入検出ソフトウェアまたはネットワーク監視ソフトウェアが、SSH 接続を妨げている。


## その他のリソース

[Linux 仮想マシンのパスワードまたは SSH をリセットする方法](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)

[Windows ベースの Azure virtual machine への Windows リモート デスクトップ接続に関するトラブルシューティング](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Azure virtual machine で実行されているアプリケーションへのアクセスに関するトラブルシューティング](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=Oct15_HO1-->