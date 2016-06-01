<properties
	pageTitle="Azure VM への SSH 接続のトラブルシューティング | Microsoft Azure"
	description="Linux を実行する Azure 仮想マシンに対する SSH 接続の失敗や拒否などの SSH エラーをトラブルシューティングして修正します。"
	keywords="拒否されたSSH 接続,SSH エラー,Azure SSH,失敗した SSH 接続"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="support-article"
	ms.date="04/12/2016"
	ms.author="iainfou"/>

# Linux ベースの Azure 仮想マシンに対する Secure Shell 接続のトラブルシューティング

Linux ベースの Azure 仮想マシンに接続しようとしたときに、さまざまな理由で Secure Shell (SSH) エラーが発生することがあります。この記事は、問題を特定して修正するために役立ちます。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](http://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。または、Azure サポート インシデントを送信できます。その場合は、[Azure サポートのサイト](http://azure.microsoft.com/support/options/)に移動して、**[サポートの要求]** をクリックします。Azure サポートの使用方法の詳細については、「[Microsoft Azure サポートに関する FAQ](http://azure.microsoft.com/support/faq/)」を参照してください。


## 一般的な SSH エラーの修正

このセクションでは、一般的な SSH 接続の問題をすばやく修正する方法を示します。

### クラシック デプロイ モデルを使用して作成された仮想マシンのトラブルシューティング

クラシック デプロイ モデルを使用して作成された仮想マシンの一般的な SSH 接続エラーを解決するには、次の手順を試してください。各手順を実行した後、仮想マシンに再接続してみてください。

- [Azure ポータル](https://portal.azure.com)からリモート アクセスをリセットします。Azure ポータルから、**[参照]**、**[仮想マシン (クラシック)]**、*ご使用の Linux 仮想マシン*、**[リモートのリセット...]** の順にクリックします。

- 仮想マシンを再起動します。[Azure ポータル](https://portal.azure.com)から、**[参照]**、**[仮想マシン (クラシック)]**、*ご使用の Linux 仮想マシン*、**[再起動]** の順にクリックします。

	- または -

	[Azure クラシック ポータル](https://manage.windowsazure.com)で、**[仮想マシン]**、**[インスタンス]**、**[再起動]** の順にクリックします。

- 仮想マシンを新しい Azure ノードに再デプロイします。これを行う方法については、「[新しい Azure ノードへの仮想マシンの再デプロイ](virtual-machines-windows-redeploy-to-new-node.md)」を参照してください。

	この操作を行うと、一時ディスクのデータが失われ、仮想マシンに関連付けられている動的 IP アドレスが更新されることに注意してください。

- 「[Linux 仮想マシンのパスワードまたは SSH をリセットする方法](virtual-machines-linux-classic-reset-access.md)」の指示に従って仮想マシンを操作します。
	- パスワードまたは SSH キーをリセットする
	- 新しい _sudo_ ユーザー アカウントを作成する。
	- SSH 構成をリセットする

- プラットフォームの問題の有無について VM のリソースの状態を確認します。<br> **[参照]**、**[Virtual Machines (クラシック)]**、*ご使用の Linux 仮想マシン*、**[設定]**、**[正常性の確認]** の順に選択します。


### Resource Manager デプロイ モデルを使用して作成した仮想マシンのトラブルシューティング

Azure Resource Manager デプロイ モデルを使用して作成された仮想マシンの一般的な SSH 問題を解決するには、次の手順を試してください。

#### SSH 接続をリセットする
Azure CLI を使用して、[Microsoft Azure Linux エージェント](virtual-machines-linux-agent-user-guide.md)のバージョン 2.0.5 以降がインストールされていることを確認します。

まだインストールしていない場合は、[Azure CLI をインストールし、Azure サブスクリプションに接続します](../xplat-cli-install.md)。`azure login` コマンドを使用してサインインします。次のコマンドを使用して、Resource Manager モードになっていることを確認します: ```
	azure config mode arm
	```

次のいずれかの方法を使用して、SSH 接続をリセットします。

**方法 1:** 次の例のように、`vm reset-access` コマンドを使用します。これにより、`VMAccessForLinux` 拡張機能が仮想マシンにインストールされます。

	```
	azure vm reset-access -g YourResourceGroupName -n YourVirtualMachineName -r
	```



**方法 2:** 次の内容を含む PrivateConf.json という名前のファイルを作成します。

	```
	{  
		"reset_ssh":"True"
	}
	```

その後、`VMAccessForLinux` 拡張機能を手動で実行して SSH 接続をリセットします。

	```
	azure vm extension set "YourResourceGroupName" "YourVirtualMachineName" VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
	```

#### SSH 資格情報をリセットする

**方法 1:** `vm reset-access` コマンドを実行して任意の SSH 資格情報を設定します。

	```
	azure vm reset-access TestRgV2 TestVmV2 -u NewUser -p NewPassword
	```

コマンド ラインで `azure vm reset-access -h` を入力すると、このコマンドの詳細が表示されます。

**方法 2:** 次の内容を含む PrivateConf.json という名前のファイルを作成します。

	```
	{
		"username":"NewUsername", "password":"NewPassword", "expiration":"2016-01-01", "ssh_key":"", "reset_ssh":false, "remove_user":""
	}
	```

次に、今説明したファイル名 (PrivateConf.json) を使用して、Linux の拡張機能を実行します。

	```
	$azure vm extension set "testRG" "testVM" VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
	```

「[Linux 仮想マシンのパスワードまたは SSH をリセットする方法](virtual-machines-linux-classic-reset-access.md)」と同様の手順に従って、他のトラブルシューティング方法を試すこともできます。必ず Resource Manager モード用に Azure CLI の命令を変更してください。


## SSH エラーの詳細なトラブルシューティング

SSH クライアントは、さまざまな理由で仮想マシン上の SSH サービスに到達できない可能性があります。次の図は、関係しているコンポーネントを示しています。

![SSH サービスのコンポーネントを示す図](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot1.png)

次の手順は、エラーのソースを分離して、解決策と回避策を見つけ出すのに役立ちます。

### 準備作業を行う

まず、ポータルで仮想マシンの状態を確認します。

[Azure クラシック ポータル](https://manage.windowsazure.com)で、クラシック デプロイ モデルを使用して作成された仮想マシンについて、次の手順を実行します。

1. **[仮想マシン]**、*VM 名*の順に選択します。
2. VM の**ダッシュボード**を選択し、その状態を確認します。
3. **[監視]** を選択して、コンピューティング、記憶域、およびネットワーク リソースの最近のアクティビティを確認します。
4. **[エンドポイント]** を選択して、SSH トラフィックのエンドポイントがあることを確認します。

[Azure ポータル](https://portal.azure.com)で次の操作を行います。

1. クラシック デプロイ モデルを使用して作成された仮想マシンの場合は、**[参照]**、**[仮想マシン (クラシック)]**、*VM 名*の順に選択します。

	- または -

	Resource Manager デプロイ モデルを使用して作成された仮想マシンの場合は、**[参照]**、**[仮想マシン]**、*VM 名*の順に選択します。

	仮想マシンの状態ウィンドウには、"**実行中**" と表示されます。コンピューティング、ストレージ、およびネットワーク リソースの最近のアクティビティを確認するには、下にスクロールします。

2. エンドポイント、IP アドレスなどの設定を確認するには、**[設定]** を選択します。

	Resource Manager を使用して作成された仮想マシンのエンドポイントを識別するには、[ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)が定義されていることを確認します。ネットワーク セキュリティ グループにルールが適用され、サブネットで参照されていることも確認します。

ネットワーク接続を確認するには、構成されているエンドポイントを確認します。また、HTTP などの別のプロトコルや他のサービスを使用して、VM に到達できるかどうかを確認します。

以上の手順を完了したら、SSH 接続を再試行してください。


### 問題のソースを見つける

お使いのコンピューター上の SSH クライアントは、以下に問題または誤構成があるために Azure 仮想マシンの SSH サービスに到達できないことがあります。

- SSH クライアント コンピューター
- 組織の境界デバイス
- クラウド サービス エンドポイントとアクセス制御リスト (ACL)
- ネットワーク セキュリティ グループ
- Linux ベースの Azure 仮想マシン

#### ソース 1: SSH クライアント コンピューター

エラーのソースであるコンピューターを排除するには、そのコンピューターから他のオンプレミスの Linux コンピューターに SSH 接続できることを確認します。

![SSH クライアント コンピューターのコンポーネントを示す図](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot2.png)

接続できない場合は、そのコンピューターで以下を確認してください。

- 受信または送信の SSH トラフィック (TCP 22) をブロックしているローカル ファイアウォールの設定
- SSH 接続を妨げている、ローカルでインストールされたクライアント プロキシのソフトウェア
- SSH 接続を妨げている、ローカルでインストールされたネットワーク監視ソフトウェア
- トラフィックを監視する、あるいは特定種類のトラフィックを許可または禁止する別の種類のセキュリティ ソフトウェア

これらの条件のいずれかに該当する場合は、ソフトウェアを一時的に無効にし、オンプレミス コンピューターへの SSH 接続を試して、ご使用のコンピューターで接続がブロックされている理由を調べます。次に、ネットワーク管理者とともに、SSH 接続が可能になるようにソフトウェアの設定を修正してください。

証明書認証を使用している場合は、ホーム ディレクトリの .ssh フォルダーに対してこれらのアクセス許可があることを確認します。

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id\_rsa (またはプライベート キーが格納されている他のファイル)
- Chmod 644 ~/.ssh/known\_hosts (SSH 経由で接続したホストが含まれます)

#### ソース 2: 組織のエッジ デバイス

エラーのソースである組織のエッジ デバイスを排除するには、インターネットに直接接続されているコンピューターから Azure VM に SSH 接続できることを確認します。サイト間 VPN 接続または Azure ExpressRoute 接続を介して VM にアクセスする場合は、「[ソース 4: ネットワーク セキュリティ グループ](#nsg)」に進みます。

![組織の境界デバイスを示す図](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot3.png)

インターネットに直接接続されているコンピューターがない場合は、新しい Azure 仮想マシンをリソース グループまたはクラウド サービス内に簡単に作成して使用できます。詳細については、「[Azure 上で Linux を実行する仮想マシンの作成](virtual-machines-linux-quick-create-cli.md)」を参照してください。テストの完了後に、そのリソース グループまたは仮想マシンと、クラウド サービスを削除します。

インターネットに直接接続されているコンピューターとの SSH 接続を作成できる場合は、組織のエッジ デバイスで以下を確認してください。

- インターネットでの SSH トラフィックをブロックしている内部ファイアウォール
- SSH 接続を妨げているプロキシ サーバー
- SSH 接続を妨げている、エッジ ネットワーク内のデバイスで実行されている侵入検出またはネットワーク監視ソフトウェア

ネットワーク管理者と協力して、インターネットでの SSH トラフィックを許可するように組織のエッジ デバイスの設定を修正します。

#### ソース 3: クラウド サービス エンドポイントと ACL

> [AZURE.NOTE] このソースは、クラシック デプロイメント モデルを使用して作成された仮想マシンにのみ適用されます。リソース マネージャーを使用して作成された仮想マシンについては、「[ソース 4: ネットワーク セキュリティ グループ](#nsg)」に進みます。

エラーのソースであるクラウド サービス エンドポイントと ACL を排除するには、同じ仮想ネットワーク内の別の Azure VM からご使用の VM に SSH 接続できることを確認します。

![クラウド サービス エンドポイントと ACL を示す図](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot4.png)

同じ仮想ネットワーク内に別の VM がない場合、新しい VM を簡単に作成することができます。詳細については、「[CLI を使用した Azure での Linux VM の作成](virtual-machines-linux-quick-create-cli.md)」を参照してください。テストの完了後に、追加した VM を削除してください。

同じ仮想ネットワーク内にある VM に対して SSH 接続を作成できる場合は、次の点を確認します。

- **ターゲットの VM での SSH トラフィック向けエンドポイントの構成。** エンドポイントのプライベート TCP ポートは、VM 上の SSH サービスがリッスンする TCP ポートと一致する必要があります (既定のポートは 22 です)。Resource Manager デプロイ モデルを使用して作成された VM では、Azure ポータルで **[参照]**、**[仮想マシン (v2)]**、*VM 名*、**[設定]**、**[エンドポイント]** の順に選択して、SSH TCP ポート番号を確認します。

- **ターゲットの仮想マシンでの、SSH トラフィック向けエンドポイントの ACL。** ACL を使用すると、発信元 IP アドレスに基づいて、インターネットからの受信トラフィックを許可または拒否するかを指定できます。ACL が正しく構成されていないと、そのエンドポイントへの SSH 受信トラフィックを受け取れない場合があります。プロキシまたは他のエッジ サーバーのパブリック IP アドレスからの受信トラフィックが ACL で許可されていることを確認します。詳細については、[ネットワーク アクセス制御リスト (ACL) の概要](../virtual-network/virtual-networks-acl.md)に関するページを参照してください。

問題のソースであるエンドポイントを排除するには、現在のエンドポイントを削除し、新しいエンドポイントを作成して、SSH 名を指定します (パブリックとプライベートのポート番号には TCP ポート 22)。詳細については、「[Azure での仮想マシンに対するエンドポイントの設定](virtual-machines-windows-classic-setup-endpoints.md)」をご覧ください

<a id="nsg"></a>
#### ソース 4: ネットワーク セキュリティ グループ

ネットワーク セキュリティ グループでは、許可された受信トラフィックと送信トラフィックをより細かく制御できます。Azure 仮想ネットワーク内のサブネットまたはクラウド サービスの全体に適用されるルールを作成することができます。ネットワーク セキュリティ グループ ルールで、インターネットからの SSH トラフィックが許可されていることを確認します。詳細については、「[ネットワーク セキュリティ グループについて](../virtual-network/virtual-networks-nsg.md)」をご覧ください。

#### ソース 5: Linux ベースの Azure 仮想マシン

最後に考えられる問題のソースは、Azure 仮想マシン自体に関連するものです。

![Linux ベースの Azure 仮想マシンを示す図](./media/virtual-machines-linux-troubleshoot-ssh-connection/ssh-tshoot5.png)

[Linux 仮想マシンのパスワードまたは SSH をリセットする](virtual-machines-linux-classic-reset-access.md)手順をまだ実行していない場合は、仮想マシンでその手順に従います。

もう一度、コンピューターから接続を試みてください。まだ接続できない場合は、以下の問題が考えられます。

- SSH サービスがターゲット仮想マシンで実行されていない。
- SSH サービスが TCP ポート 22 をリッスンしていない。これをテストするには、ローカル コンピューターに telnet クライアントをインストールし、"telnet *cloudServiceName*.cloudapp.net 22" を実行します。これで、SSH エンドポイントに対する受信と送信の接続が仮想マシンで許可されているかどうかを確認できます。
- ターゲット仮想マシンのローカル ファイアウォールに、受信または送信の SSH トラフィックを妨げているルールがある。
- Azure 仮想マシンで実行されている侵入検出ソフトウェアまたはネットワーク監視ソフトウェアが、SSH 接続を妨げている。


## その他のリソース

- クラシック デプロイ モデルを使用して作成された仮想マシンのトラブルシューティングの詳細については、「[Linux 仮想マシンのパスワードまたは SSH をリセットする方法](virtual-machines-linux-classic-reset-access.md)」を参照してください。

- Resource Manager デプロイ モデルを使用して作成された仮想マシンのトラブルシューティングの詳細については、以下を参照してください。
	- [Windows ベースの Azure 仮想マシンへの Windows リモート デスクトップ接続に関するトラブルシューティング](virtual-machines-windows-troubleshoot-rdp-connection.md)
	-	[Azure 仮想マシンで実行されているアプリケーションへのアクセスに関するトラブルシューティング](virtual-machines-linux-troubleshoot-app-connection.md)

<!---HONumber=AcomDC_0518_2016-->