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
	ms.date="03/13/2016"
	ms.author="v-livech"/>

# Azure の Linux VM と Mac for Linux VM に SSH キーを作成する

このトピックの内容:

1. パスワードで保護された公開キーと秘密キーのペアを `ssh-keygen` で作成します。
2. `~/.ssh/config` ファイルを作成して、ログイン時間を短縮し、セキュリティと構成上、重要となる既定の設定を有効にします。
3. Azure の Linux VM または BSD VM に SSH でログインします。

## 前提条件

Azure アカウント ([無料試用版の入手](https://azure.microsoft.com/pricing/free-trial/)) と SSH ツールキットがインストールされている端末 (Linux または Mac)「`azure config mode arm`」と入力して、CLI をリソース モードにしてください。

## はじめに

Linux サーバーにログインするための手段として最も安全性が高く、**かつ**簡単な方法は、SSH の公開キーと秘密キーを使用することです。Azure の Linux VM や BSD VM へのログインには、総当たり攻撃で簡単に突破される可能性のあるパスワードよりも、[公開キー暗号化](https://en.wikipedia.org/wiki/Public-key_cryptography)を使用した方が安全です。公開キーは、だれとでも共有することができるのに対し、秘密キーは自分 (またはローカル セキュリティ インフラストラクチャ) だけが所有します。

この記事では、*ssh-rsa* 形式のキー ファイルを作成します。リソース マネージャーへのデプロイに推奨される形式であり、クラシック デプロイとリソース マネージャー デプロイの両方の[ポータル](https://portal.azure.com)で必須となっています。

> [AZURE.NOTE] Windows コンピューターから VM にログオンする場合は、「[Use SSH keys on Windows (Windows で SSH キーを使用する)](virtual-machines-linux-ssh-from-windows.md)」を参照してください。

## 基本コマンド一覧

以下のコマンド例の &lt; と &gt; で囲まれた値は、実際の環境の値に置き換えてください。

```bash
[username@fedora22 ~]$ ssh-keygen -t rsa -b 4096 -C "<your_user@yourdomain.com>"

#Enter the name of the file that will be saved in the `~/.ssh/` directory.

azure_fedora_id_rsa

#Enter (twice) a [secure](https://www.xkcd.com/936/) password for the SSH key.

#Enter passphrase for github_id_rsa:

correct horse battery staple

#Add the newly created key to `ssh-agent` on Linux and Mac (also added to OSX Keychain).

[username@fedora22 ~]$ eval "$(ssh-agent -s)"

[username@fedora22 ~]$ ssh-add ~/.ssh/azure_fedora_id_rsa

#Copy the SSH public key to your Linux Server.

[username@fedora22 ~]$ ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>

#Test the login using keys instead of a password.

[username@fedora22 ~]$ ssh -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>

Last login: Tue Dec 29 07:07:09 2015 from 66.215.21.201
[username@fedora22 ~]$

```

## SSH キーの作成

Azure では、長さ 2,048 ビット以上の ssh-rsa 形式の公開キーと秘密キーを必須としています。キー ペアを作成するには `ssh-keygen` を使用します。一連の質問に答えることによって、秘密キーおよび対応する公開キーが出力されます。皆さんが Azure VM を作成する際、公開キーの内容を指定します。そのデータが Linux VM にコピーされ、ローカルに安全に保管されている秘密キーと組み合わされて、ログイン時の本人確認が行われます。



### `ssh-keygen` の使用

次に示すのは、2,048 ビット RSA を使用して SSH キー ペアを作成するコマンドです。識別しやすいようにコメントを付けています。

    username@macbook$ ssh-keygen -t rsa -b 4096 -C "username@fedoraVMAzure"

##### コマンドの説明

`ssh-keygen` = キーの作成に使用されたプログラム

`-t rsa` = 作成するキーの種類。ここでは [RSA 形式](https://en.wikipedia.org/wiki/RSA_(cryptosystem))を指定します。

`-b 2048` = キーのビット数

`-C "username@fedoraVMAzure"` = 識別しやすいようにするためのキーのコメント。コメントは、公開キー ファイルの最後に追記されます。一般に、コメントとしては電子メール アドレスが使用されますが、この記事では複数の SSH キーの使用に対応するために汎用的なコメントを記載しています。

#### `ssh-keygen` のチュートリアル

```bash
username@macbook$ ssh-keygen -t rsa -b 4096 -C "username@fedoraVMAzure"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/steve/.ssh/id_rsa): azure_fedora_id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in azure_fedora_id_rsa.
Your public key has been saved in azure_fedora_id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:79:ad:25:cc:65:e9:0c:07:e5:d1:a9:08 username@fedoraVMAzure
The key's randomart image is:
+--[ RSA 4096]----+
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

username@macbook$ ls -al ~/.ssh
-rw------- 1 username staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 username staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```

`Enter file in which to save the key (/Users/steve/.ssh/id_rsa): azure_fedora_id_rsa` この記事で使用するキー ペアの名前。既定では、キー ペアの名前に **id\_rsa** が割り当てられます。秘密キーのファイル名として **id\_rsa** を想定しているツールもあるため、そのようにすることをお勧めします (`~/.ssh/` は、すべての SSH キー ペアおよび SSH 構成ファイルで標準的に使用される既定の場所)。

`Enter passphrase (empty for no passphrase):` キー ペアにはパスワード (`ssh-keygen` では、これを "パスフレーズ" といいます) を追加するよう強くお勧めします。キー ペアを保護するパスワードがないと、秘密キー ファイルのコピーがあればだれでも、そのファイルを使用して、対応する公開キーのある (あなたの) サーバーにログインすることができます。パスワードを追加すれば、第三者に秘密キー ファイルへのアクセスを許してしまった場合でも、認証用のキーを変更する時間ができるので保護のレベルが上がります。

`username@macbook$ ls -al ~/.ssh` これは、新しいキー ペアとそのアクセス許可を示しています。`ssh-keygen` は、`~/.ssh` ディレクトリを作成 (存在しない場合) し、適切な所有権とファイル モードを設定します。

## SSH 構成ファイルの作成と構成

Linux VM の立ち上げと実行に必須というわけではありませんが、`~/.ssh/config` ファイルを作成して構成することをお勧めします。確実にパスワードなしで VM にログオンしたり、Azure VM ごとに異なるキー ペアが自動的に使用されるようにしたり、複数のサーバーをターゲットとするように他のプログラム (**git** など) を構成したりすることができます。

標準的な構成の例を次に示します。

### ファイルの作成

```bash
username@macbook$ touch ~/.ssh/config
```

### ファイルを編集して新しい SSH 構成を追加する

```bash
username@macbook$ vim ~/.ssh/config

#Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User username
  PubkeyAuthentication yes
  IdentityFile /Users/steve/.ssh/azure_fedora_id_rsa
# ./Azure Keys
# GitHub keys
Host github.com
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /Users/steve/.ssh/github_id_rsa
Host github.private
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /Users/steve/.ssh/private_repo_github_id_rsa
# ./Github Keys
# Default Settings
Host *
  PubkeyAuthentication=no
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath /Users/steve/.ssh/Connections/ssh-%r@%h:%p
  ControlPersist 4h
  StrictHostKeyChecking=no
  IdentityFile /Users/steve/.ssh/id_rsa
  UseRoaming=no
```

この SSH 構成には、サービスごとのセクションがあり、各サービスに専用のキー ペアを割り当てることができます。既定の設定は、ログインしたホストが、先行するいずれのグループとも合致しない場合に適用されます。SSH 構成によって [GitHub](https://github.com) のログインを 2 つ用意し、パブリックな作業用と、自分の作業全般で使用するプライベートなリポジトリ用として使い分けることができます。


##### 構成ファイルについて

`Host` = 端末で呼び出すホストの名前。`ssh fedora22` は、`Host fedora22` という名称の設定ブロック内の値を使用するよう `SSH` に伝えています。注: このラベルは、サーバーの実際のホスト名を表すものではありません。実際の用途に合っていれば、どのような名称を使用してかまいません。

`Hostname 102.160.203.241` = ログインするサーバーの IP アドレスまたは DNS 名。これは、サーバーへのルーティングに使用されます。内部 IP に対応する外部 IP を指定できます。

`User git` = Azure VM にログインするときに使用するリモート ユーザー アカウント。

`PubKeyAuthentication yes` = SSH キーを使ってログインしたいという意思を SSH に伝えています。

`IdentityFile /Users/steve/.ssh/azure_fedora_id_rsa` = ログインを認証するサーバーにどのキー ペアを提示するかを SSH に伝えています。


## パスワードを使わずに SSH で Linux VM にログインする

SSH キー ペアを作成し、SSH 構成ファイルに必要な変更を加えたら、すばやく安全に Linux VM にログインすることができます。SSH キーを使用して初めてサーバーにログインするとき、キー ファイルのパスフレーズを入力するよう求められます。

`username@macbook$ ssh fedora22`

##### コマンドの説明

`username@macbook$ ssh fedora22` が実行されると SSH はまず、`Host fedora22` ブロックの設定を見つけて読み込んだ後、最後のブロック `Host *` から残りの設定をすべて読み込みます。

## 次のステップ

実際にキー ファイルを使ってみましょう。[テンプレートを使用して安全な Linux VM を Azure 内に作成](virtual-machines-linux-create-ssh-secured-vm-from-template.md)します。

<!---HONumber=AcomDC_0323_2016-->