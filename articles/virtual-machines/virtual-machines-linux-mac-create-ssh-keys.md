<properties
	pageTitle="Linux と Mac の SSH キーを作成する | Microsoft Azure"
	description="Azure で、リソース マネージャーと従来のデプロイメント モデル用に、Linux と Mac の SSH 鍵を生成し、使用します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/12/2016"
	ms.author="v-livech"/>

# Azure の Linux VM と Mac for Linux VM に SSH キーを作成する

パスワードで保護された SSH の公開キーと秘密キーを作成するには、ワークステーションでターミナルを開く必要があります。SSH キーを作成した後は、既定でそのキーを使用して新しい VM を作成したり、Azure CLI と Azure テンプレートの両方を使用して既存の VM に公開キーを追加したりできます。

## 基本コマンド一覧

以下のコマンド例の &lt; と &gt; で囲まれた値は、実際の環境の値に置き換えてください。

```bash
[ahmet@fedora ~]$ ssh-keygen -t rsa -b 2048 -C "<your_user@yourdomain.com>"

#Enter the name of the file that will be saved in the `~/.ssh/` directory.
<azure_fedora_id_rsa>

#Enter passphrase for azure_fedora_id_rsa:
<correct horse battery staple>

#Add the newly created key to `ssh-agent` on Linux and Mac (also added to OSX Keychain).
[ahmet@fedora ~]$ eval "$(ssh-agent -s)"
[ahmet@fedora ~]$ ssh-add ~/.ssh/azure_fedora_id_rsa

#Copy the SSH public key to your Linux Server.
[ahmet@fedora ~]$ ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>

#Test the login using keys instead of a password.
[ahmet@fedora ~]$ ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>

Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
[ahmet@fedora ~]$

```

## はじめに

SSH 公開キーと秘密キーを使用することは、Linux サーバーにログインするための最も簡単な方法ですが、[公開キー暗号化](https://en.wikipedia.org/wiki/Public-key_cryptography)を併せて利用することで、パスワードを使うよりもはるかに安全に Azure の Linux VM または BSD VM にログインできるようになります。パスワードはブルート フォース攻撃を受けやすくなっています。公開キーはだれとでも共有できますが、秘密キーを所有するのは自分 (またはローカル セキュリティ インフラストラクチャ) だけです。作成した SSH 秘密キーにはそれを保護するための[セキュリティ保護されたパスワード](https://www.xkcd.com/936/)がありますが、このパスワードは SSH 秘密キーにアクセスするためだけのものであり、ユーザー アカウントのパスワードでは**ありません**。パスワードなしの秘密キーを所有しているすべてのユーザーは、公開キーがインストールされているすべてのサーバーにアクセスできます。パスワードがない場合、秘密キーは使用できません。


この記事では、*ssh-rsa* 形式のキー ファイルを作成します。このファイルは、Resource Manager へのデプロイに推奨されており、クラシック デプロイと Resource Manager デプロイの両方の[ポータル](https://portal.azure.com)で必須となっています。


## SSH キーの作成

Azure では、長さ 2,048 ビット以上の ssh-rsa 形式の公開キーと秘密キーを必須としています。このキー ペアを作成するには `ssh-keygen` を使用します。一連の質問に答えることによって、秘密キーと対応する公開キーが出力されます。皆さんが Azure VM を作成する際、公開キーの内容を指定します。そのデータが Linux VM にコピーされ、ローカルに安全に保管されている秘密キーと組み合わされて、ログイン時の本人確認が行われます。

### `ssh-keygen` の使用

次に示すのは、2,048 ビット RSA を使用してパスワードで保護された SSH キー ペアを作成するコマンドです。識別しやすいようにコメントを付けています。

```
ahmet@fedora$ ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
```

##### コマンドの説明

`ssh-keygen` = キーの作成に使用されたプログラム。

`-t rsa` = 作成するキーの種類。ここでは [RSA 形式](https://en.wikipedia.org/wiki/RSA_(cryptosystem)) を指定します。

`-b 2048` = キーのビット数。

`-C "ahmet@fedoraVMAzure"` = 識別しやすいように公開キー ファイルの最後に追記されたコメント。通常は電子メール アドレスがコメントとして使用されますが、インフラストラクチャに最適な他のものを使用してもかまいません。

#### `ssh-keygen` のチュートリアル

```bash
ahmet@fedora$ ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in azure_fedora_id_rsa.
Your public key has been saved in azure_fedora_id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@fedoraVMAzure
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

ahmet@fedora$ ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa` この記事で使用するキー ペアの名前。既定では、キー ペアの名前に **id\_rsa** が割り当てられます。秘密キーのファイル名として **id\_rsa** を想定しているツールもあるため、そのようにすることをお勧めします (`~/.ssh/` は、通常、すべての SSH キー ペアと SSH 構成ファイルの既定の場所です)。

`Enter passphrase (empty for no passphrase):` キー ペアにはパスワード (`ssh-keygen` では、これを "パスフレーズ" と呼びます) を追加するよう強くお勧めします。キー ペアを保護するパスワードがないと、秘密キー ファイルのコピーがあればだれでも、そのファイルを使用して、対応する公開キーのある (あなたの) サーバーにログインすることができます。パスワードを追加すれば、第三者に秘密キー ファイルへのアクセスを許してしまった場合でも、認証用のキーを変更する時間ができるので保護のレベルが上がります。

`ahmet@fedora$ ls -al ~/.ssh` これは、新しいキー ペアとそのアクセス許可を示しています。`ssh-keygen` は、`~/.ssh` ディレクトリを作成 (存在しない場合) し、適切な所有権とファイル モードを設定します。

## ssh-agent を使用して秘密キーのパスワードを格納する

SSH ログインのたびに秘密キー ファイルのパスワードを入力しなくて済むように、`ssh-agent` を使用して秘密キー ファイルのパスワードをキャッシュし、Linux VM に迅速かつ安全にログインできます。OSX を使用している場合、`ssh-agent` を呼び出すと、秘密キーのパスワードは Keychain によって安全に保存されます。

最初に、`ssh-agent` が動いていることを確認します。

`[ahmet@fedora ~]$ eval "$(ssh-agent -s)"`

`ssh-add` コマンドを使用して秘密キーを `ssh-agent` に追加します。OSX で Keychain が起動されて、資格情報が格納されます。

`[ahmet@fedora ~]$ ssh-add ~/.ssh/azure_fedora_id_rsa`

秘密キーのパスワードが保存されたので、SSH でログインするたびにキーのパスワードを入力する必要はありません。

## SSH 構成ファイルの作成と構成

Linux VM を稼働させるために必須というわけではありませんが、`~/.ssh/config` ファイルを作成して構成することをお勧めします。これにより、誤ったパスワードを使用して VM にログオンすることを防ぎ、Azure VM ごとに異なるキー ペアが自動的に使用されるようにしたり、複数のサーバーをターゲットとするように他のプログラム (**git** など) を構成したりすることができます。

標準的な構成の例を次に示します。

### ファイルの作成

```bash
ahmet@fedora$ touch ~/.ssh/config
```

### ファイルを編集して新しい SSH 構成を追加する

```bash
ahmet@fedora$ vim ~/.ssh/config

#Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
  PubkeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa
# ./Azure Keys
# GitHub keys
Host github.com
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa
Host github.private
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/private_repo_azure_fedora_id_rsa
# ./Github Keys
# Default Settings
Host *
  PubkeyAuthentication=no
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath /home/ahmet/.ssh/Connections/ssh-%r@%h:%p
  ControlPersist 4h
  StrictHostKeyChecking=no
  IdentityFile /home/ahmet/.ssh/id_rsa
  UseRoaming=no
```

この SSH 構成には、サービスごとのセクションがあり、各サービスに専用のキー ペアを割り当てることができます。既定の設定は、ログインしたホストが、先行するいずれのグループとも合致しない場合に適用されます。SSH 構成によって [GitHub](https://github.com) のログインを 2 つ用意し、パブリックな作業用と、自分の作業全般で使用するプライベートなリポジトリ用として使い分けることができます。


##### 構成ファイルについて

`Host` = 端末で呼び出すホストの名前。`ssh fedora22` は、`Host fedora22` という名称の設定ブロック内の値を使用するよう `SSH` に伝えています。注: このラベルは、サーバーの実際のホスト名を表すものではありません。実際の用途に合っていれば、どのような名称を使用してもかまいません。

`Hostname 102.160.203.241` = ログインするサーバーの IP アドレスまたは DNS 名。これは、サーバーへのルーティングに使用されます。内部 IP に対応する外部 IP を指定できます。

`User git` = Azure VM にログインするときに使用するリモート ユーザー アカウント。

`PubKeyAuthentication yes` = SSH キーを使ってログインしたいという意思を SSH に伝えています。

`IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa` = ログインを認証するサーバーにどのキー ペアを提示するかを SSH に伝えています。


## パスワードを使わずに SSH で Linux VM にログインする

SSH キー ペアを作成し、SSH 構成ファイルに必要な変更を加えたら、すばやく安全に Linux VM にログインすることができます。SSH キーを使用して初めてサーバーにログインするとき、キー ファイルのパスフレーズを入力するよう求められます。

`ahmet@fedora$ ssh fedora22`

##### コマンドの説明

`ahmet@fedora$ ssh fedora22` が実行されると SSH はまず、`Host fedora22` ブロックの設定を見つけて読み込んだ後、最後のブロック `Host *` から残りの設定をすべて読み込みます。

## 次のステップ

SSH キー ファイルを使用して次のことができます。

- [Azure テンプレートを使用して安全な Linux VM を作成する](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Azure ポータルを使用して安全な Linux VM を作成する](virtual-machines-linux-quick-create-portal.md)
- [Azure CLI を使用して安全な Linux VM を作成する](virtual-machines-linux-quick-create-cli.md)

<!---HONumber=AcomDC_0420_2016-->