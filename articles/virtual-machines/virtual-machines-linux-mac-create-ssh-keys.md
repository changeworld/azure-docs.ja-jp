---
title: "Linux と Mac の SSH キーを作成する | Microsoft Docs"
description: "Azure で、リソース マネージャーと従来のデプロイメント モデル用に、Linux と Mac の SSH 鍵を生成し、使用します。"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: 
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/14/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: b80fb49929ce53f33b9a4064ba6ad14d0108d690
ms.openlocfilehash: c879231fac37ae3c69bac4d201b01469ca18240c


---
# <a name="create-ssh-keys-on-linux-and-mac-for-linux-vms-in-azure"></a>Azure の Linux VM と Mac for Linux VM に SSH キーを作成する
SSH キー ペアを使用すると、既定で認証に SSH キーを使用する仮想マシンを Azure に作成できます。そのため、ログインするためのパスワードが不要になります。  パスワードは推測できる場合もあるため、VM が執拗なブルート フォース攻撃にさらされる危険性が生じかねません。 Azure テンプレートまたは `azure-cli` で作成された VM には、デプロイの一部として SSH 公開キーを含めることができるため、デプロイ後の構成を行わずに済みます。  Windows から Linux VM に接続している場合は、[Windows での SSH キーの作成](virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。


## <a name="quick-commands"></a>クイック コマンド
次のコマンドで、例を独自の値に置き換えます。

SSH キーは既定で `.ssh` ディレクトリに保持されます。  

```bash
cd ~/.ssh/
```

`~/.ssh` ディレクトリがない場合、`ssh-keygen` コマンドにより適切なアクセス許可で作成されます。

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

`~/.ssh/` ディレクトリに保存されるファイルの名前を入力します。

```bash
id_rsa
```

id_rsa のパスフレーズを入力します。

```bash
correct horse battery staple
```

これで、`id_rsa` および `id_rsa.pub` SSH キー ペアが `~/.ssh` ディレクトリに配置されました。

```bash
ls -al ~/.ssh
```

新しく作成したキーを Linux および Mac の `ssh-agent` に追加します (OSX のキーチェーンにも追加されます)。

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

SSH の公開キーを Linux サーバーにコピーします。

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub myusername@myserver
```

パスワードの代わりにキーを使用してログイン テストを実行します。

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/id_rsa myusername@myserver
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## <a name="detailed-walkthrough"></a>詳細なチュートリアル
Linux サーバーにログインするための最も簡単な方法は、SSH の公開キーと秘密キーを使用する方法です。 [公開キー暗号化](https://en.wikipedia.org/wiki/Public-key_cryptography) を使用した方が安全です。 公開キーはだれとでも共有できますが、秘密キーを所有するのは自分 (またはローカル セキュリティ インフラストラクチャ) だけです。  SSH 秘密キーには、それを保護するための、[セキュリティで高度に保護されたパスワード](https://www.xkcd.com/936/) (ソース: [xkcd.com](https://xkcd.com)) が必要です。  このパスワードは、秘密 SSH キーにアクセスするためだけのものであり、ユーザー アカウント パスワード **ではありません** 。  SSH キーにパスワードを追加すると、そのパスワードで秘密キーが暗号化されるため、ロックを解除するパスワードなしでは秘密キーを使用できなくなります。  攻撃者によって盗まれた秘密キーにパスワードがないと、その秘密キーが使用され、対応する公開キーを持つ任意のサーバーにログインされてしまいます。  秘密キーがパスワードで保護されている場合、攻撃者は秘密キーを使用できないため、Azure 上のインフラストラクチャにセキュリティ レイヤーが追加されたことになります。

この記事では、*ssh-rsa* 形式のキー ファイルを作成します。このファイルは、Resource Manager でのデプロイに推奨されています。  *ssh-rsa* キーは、クラシック デプロイと Resource Manager デプロイの両方の[ポータル](https://portal.azure.com)

## <a name="create-the-ssh-keys"></a>SSH キーの作成
Azure では、長さ 2,048 ビット以上の ssh-rsa 形式の公開キーと秘密キーを必須としています。 このキーを作成するには、`ssh-keygen` を使用します。一連の質問に答えることによって、秘密キーと対応する公開キーが出力されます。 Azure VM が作成されると、公開キーが `~/.ssh/authorized_keys` にコピーされます。  `~/.ssh/authorized_keys` の SSH キーは、クライアントが SSH ログイン接続の対応する秘密キーと一致することを確認するために使用されます。

## <a name="using-ssh-keygen"></a>ssh-keygen の使用
次に示すのは、2,048 ビット RSA を使用してパスワードで保護された (暗号化された) SSH キー ペアを作成するコマンドです。識別しやすいようにコメントを付けています。  

最初に、ディレクトリを変更すると、すべての SSH キーがそのディレクトリに作成されます。

```bash
cd ~/.ssh
```

`~/.ssh` ディレクトリがない場合、`ssh-keygen` コマンドにより適切なアクセス許可で作成されます。

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

*コマンドの説明*

`ssh-keygen` = キーの作成に使用するプログラム。

`-t rsa` = 作成するキーの種類。ここでは [RSA 形式](https://en.wikipedia.org/wiki/RSA_(cryptosystem)を指定します。

`-b 2048` = キーのビット数。

`-C "myusername@myserver"` = 識別しやすいように公開キー ファイルの末尾に追記されたコメント。  通常は電子メール アドレスがコメントとして使用されますが、インフラストラクチャに最適な他の文字列を使用してもかまいません。

### <a name="using-pem-keys"></a>PEM キーの使用
クラシック デプロイメント モデル (Azure クラシック ポータルまたは Azure サービス管理 CLI `asm`) を使用している場合は、Linux VM にアクセスするために PEM 形式の SSH キーを使用しなければならないことがあります。  既存の SSH 公開キーと既存の x509 証明書から PEM キーを作成する方法は、次のとおりです。

既存の SSH 公開キーから PEM 形式のキーを作成するには、次のコマンドを使用します。

```bash
ssh-keygen -f ~/.ssh/id_rsa.pub -e > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>ssh-keygen の例
```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 myusername@myserver
The key's randomart image is:
+--[ RSA 2048]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+
```

保存するキー ファイル:

`Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa`

この記事で使用するキー ペアの名前です。  既定では、キー ペアの名前に **id_rsa** が割り当てられます。秘密キーのファイル名として **id_rsa** を想定しているツールもあるため、そのようにすることをお勧めします。 ディレクトリ `~/.ssh/` は、SSH キー ペアと SSH 構成ファイルの既定の場所です。

```bash
ls -al ~/.ssh
-rw------- 1 myusername staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 myusername staff   410 Aug 25 18:04 rsa.pub
```
`~/.ssh` ディレクトリの一覧です。 `ssh-keygen` は、`~/.ssh` ディレクトリが存在しない場合は作成したうえで、適切な所有権とファイル モードを設定します。

キーのパスワード:

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` では、パスワードのことを "パスフレーズ" と言います。  キー ペアにはパスワードを追加することを " *強く* " お勧めします。 キー ペアを保護するパスワードがないと、秘密キー ファイルを持っている人は、そのファイルを使用して、対応する公開キーを持つ任意のサーバーにログインすることができます。 パスワードを追加すれば、第三者に秘密キー ファイルへのアクセスを許してしまった場合でも、認証用のキーを変更する時間ができるので、保護のレベルが上がります。

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>ssh-agent を使用して秘密キーのパスワードを格納する
SSH ログインのたびに秘密キー ファイルのパスワードを入力しなくて済むように、 `ssh-agent` を使用して秘密キー ファイルのパスワードをキャッシュできます。 Mac を使用している場合、 `ssh-agent`を呼び出すと、秘密キーのパスワードは OSX キーチェーンによって安全に保存されます。

最初に、`ssh-agent` が実行されていることを確認します。

```bash
eval "$(ssh-agent -s)"
```

ここで、`ssh-add` コマンドを使用して、`ssh-agent` に秘密キーを追加します。

```bash
ssh-add ~/.ssh/id_rsa
```

これで、秘密キーのパスワードが `ssh-agent` に格納されました。

## <a name="create-and-configure-an-ssh-config-file"></a>SSH 構成ファイルの作成と構成
ログインを高速化し、SSH クライアントの動作を最適化するために、`~/.ssh/config` ファイルを作成して構成することをお勧めします。

標準的な構成の例を次に示します。

### <a name="create-the-file"></a>ファイルの作成
```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>ファイルを編集して新しい SSH 構成を追加する
```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>`~/.ssh/config` ファイルの例:
```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User myusername
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

この SSH 構成には、サーバーごとのセクションがあり、各サーバーに専用のキー ペアを割り当てることができます。 既定の設定 (`Host *`) は、構成ファイルで特定のどのホストとも一致しないすべてのホスト用です。

### <a name="config-file-explained"></a>構成ファイルについて
`Host` = 端末で呼び出すホストの名前。  `ssh fedora22` は、`Host fedora22` という名称の設定ブロック内の値を使用するよう `SSH` に伝えています。注: このラベルは、サーバーの実際のホスト名を表すものではありません。実際の用途に合っていれば、どのような名称を使用してもかまいません。

`Hostname 102.160.203.241` = アクセスされるサーバーの IP アドレスまたは DNS 名。

`User myusername` = サーバーにログインするときに使用するリモート ユーザー アカウント。

`PubKeyAuthentication yes` = SSH キーを使ってログインする意思を SSH に伝えています。

`IdentityFile /home/myusername/.ssh/id_id_rsa` = 認証に使用する SSH 秘密キーおよび対応する公開キー。

## <a name="ssh-into-linux-without-a-password"></a>パスワードを使わずに SSH で Linux にログインする
SSH キー ペアを作成し、SSH 構成ファイルを構成したので、すばやく安全に Linux VM にログインできるようになりました。 SSH キーを使用して初めてサーバーにログインするとき、キー ファイルのパスフレーズを入力するように求められます。

```bash
ssh fedora22
```

### <a name="command-explained"></a>コマンドの説明
`ssh fedora22` が実行されると SSH はまず、`Host fedora22` ブロックの設定を見つけて読み込んだ後、最後のブロック `Host *` から残りの設定をすべて読み込みます。

## <a name="next-steps"></a>次のステップ
次のステップでは、新しい SSH 公開キーを使用して Azure Linux VM を作成します。  Azure VM は、SSH 公開キーをログインとして作成した方が、既定のログイン方法であるパスワードを使って作成するよりも高いセキュリティが得られます。  SSH キーを使用して作成された Azure VM は、既定ではパスワードが無効にされます。そのため、推測によるブルート フォース攻撃を回避できます。

* [Azure テンプレートを使用して安全な Linux VM を作成する](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure ポータルを使用して安全な Linux VM を作成する](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI を使用して安全な Linux VM を作成する](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Nov16_HO3-->


