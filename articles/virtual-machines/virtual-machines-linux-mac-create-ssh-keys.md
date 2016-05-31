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
	ms.date="05/16/2016"
	ms.author="v-livech"/>

# Azure の Linux VM と Mac for Linux VM に SSH キーを作成する

パスワードで保護された SSH の公開キーと秘密キーを作成するには、ワークステーションでターミナルを開く必要があります。SSH キーを作成した後は、既定でそのキーを使用して新しい VM を作成したり、Azure CLI と Azure テンプレートの両方を使用して既存の VM に公開キーを追加したりできます。これにより、パスワードと比べてはるかに安全なキーによる認証方法を使用した、SSH 経由のパスワード不要のログインが可能になります。

## 基本コマンド一覧

以下のコマンド例の &lt; と &gt; で囲まれた値は、実際の環境の値に置き換えてください。

```bash
ssh-keygen -t rsa -b 2048 -C "<your_user@yourdomain.com>"
```

`~/.ssh/` ディレクトリに保存されるファイルの名前を入力します。

```bash
<azure_fedora_id_rsa>
```

azure\_fedora\_id\_rsa のパスフレーズを入力します。

```bash
<correct horse battery staple>
```

新しく作成したキーを Linux および Mac の `ssh-agent` に追加します (OSX のキーチェーンにも追加されます)。

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/azure_fedora_id_rsa
```

SSH の公開キーを Linux サーバーにコピーします。

```bash
ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>
```

パスワードの代わりにキーを使用してログイン テストを実行します。

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## はじめに

SSH 公開キーと秘密キーを使用することは、Linux サーバーにログインするための最も簡単な方法ですが、[公開キー暗号化](https://en.wikipedia.org/wiki/Public-key_cryptography)を併せて利用することで、パスワードを使うよりもはるかに安全に Azure の Linux VM または BSD VM にログインできるようになります。パスワードはブルート フォース攻撃を受けやすくなっています。公開キーはだれとでも共有できますが、秘密キーを所有するのは自分 (またはローカル セキュリティ インフラストラクチャ) だけです。作成した SSH 秘密キーにはそれを保護するための[セキュリティ保護されたパスワード](https://www.xkcd.com/936/)がありますが、このパスワードは SSH 秘密キーにアクセスするためだけのものであり、ユーザー アカウントのパスワードでは**ありません**。SSH キーにパスワードを追加すると、そのパスワードで秘密キーが暗号化されるため、ロックを解除するパスワードなしでは秘密キーを使用できなくなります。攻撃者が秘密キーを盗むことができた場合、パスワードが追加されていないと、その秘密キーを使用して、対応する公開キーがインストールされているサーバーにログインできます。秘密キーがパスワードで保護されている場合、攻撃者は秘密キーを使用できないため、Azure 上のインフラストラクチャにセキュリティ レイヤーが追加されたことになります。


この記事では、*ssh-rsa* 形式のキー ファイルを作成します。このファイルは、Resource Manager へのデプロイに推奨されており、クラシック デプロイと Resource Manager デプロイの両方の[ポータル](https://portal.azure.com)で必須となっています。


## SSH キーの作成

Azure では、長さ 2,048 ビット以上の ssh-rsa 形式の公開キーと秘密キーを必須としています。このキー ペアを作成するには `ssh-keygen` を使用します。一連の質問に答えることによって、秘密キーと対応する公開キーが出力されます。皆さんが Azure VM を作成する際、公開キーの内容を指定します。そのデータが Linux VM にコピーされ、ローカルに安全に保管されている秘密キーと組み合わされて、ログイン時の本人確認が行われます。

## ssh-keygen の使用

次に示すのは、2,048 ビット RSA を使用してパスワードで保護された (暗号化された) SSH キー ペアを作成するコマンドです。識別しやすいようにコメントを付けています。

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
```

_コマンドの説明_

`ssh-keygen` = キーの作成に使用するプログラム。

`-t rsa` = 作成するキーの種類。ここでは [RSA 形式](https://en.wikipedia.org/wiki/RSA_(cryptosystem) を指定します。

`-b 2048` = キーのビット数。

`-C "ahmet@fedoraVMAzure"` = 識別しやすいように公開キー ファイルの最後に追記されたコメント。通常は電子メール アドレスがコメントとして使用されますが、インフラストラクチャに最適な他のものを使用してもかまいません。

## ssh-keygen のチュートリアル

以降、各手順について詳しく説明します。最初に `ssh-keygen` を実行しましょう。

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@fedoraVMAzure"
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
```

保存するキー ファイル:

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): azure_fedora_id_rsa`

この記事で使用するキー ペアの名前です。既定では、キー ペアの名前に **id\_rsa** が割り当てられます。秘密キーのファイル名として **id\_rsa** を想定しているツールもあるため、そのようにすることをお勧めします (`~/.ssh/` は、通常、すべての SSH キー ペアと SSH 構成ファイルの既定の場所です)。

```bash
ahmet@fedora$ ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```
これは、新しいキー ペアとそのアクセス許可を示しています。`ssh-keygen` は、`~/.ssh` ディレクトリを作成 (存在しない場合) し、適切な所有権とファイル モードを設定します。

キーのパスワード:

`Enter passphrase (empty for no passphrase):`

キー ペアにはパスワード (`ssh-keygen` では、これを "パスフレーズ" と呼びます) を追加するよう強くお勧めします。キー ペアを保護するパスワードがないと、秘密キー ファイルのコピーがあればだれでも、そのファイルを使用して、対応する公開キーのある (あなたの) サーバーにログインすることができます。パスワードを追加すれば、第三者に秘密キー ファイルへのアクセスを許してしまった場合でも、認証用のキーを変更する時間ができるので保護のレベルが上がります。

## ssh-agent を使用して秘密キーのパスワードを格納する

SSH ログインのたびに秘密キー ファイルのパスワードを入力しなくて済むように、`ssh-agent` を使用して秘密キー ファイルのパスワードをキャッシュし、Linux VM に迅速かつ安全にログインできます。OSX を使用している場合、`ssh-agent` を呼び出すと、秘密キーのパスワードは Keychain によって安全に保存されます。

最初に、`ssh-agent` が動いていることを確認します。

```bash
eval "$(ssh-agent -s)"
```

`ssh-add` コマンドを使用して秘密キーを `ssh-agent` に追加します。OSX で Keychain が起動されて、資格情報が格納されます。

```bash
ssh-add ~/.ssh/azure_fedora_id_rsa
```

秘密キーのパスワードが保存されたので、SSH でログインするたびにキーのパスワードを入力する必要はありません。

## SSH 構成ファイルの作成と構成

Linux VM を稼働させるために必須というわけではありませんが、`~/.ssh/config` ファイルを作成して構成することをお勧めします。これにより、誤ったパスワードを使用して VM にログオンすることを防ぎ、Azure VM ごとに異なるキー ペアが自動的に使用されるようにしたり、複数のサーバーをターゲットとするように他のプログラム (**git** など) を構成したりすることができます。

標準的な構成の例を次に示します。

### ファイルの作成

```bash
touch ~/.ssh/config
```

### ファイルを編集して新しい SSH 構成を追加する

```bash
vim ~/.ssh/config
```

### `~/.ssh/config` ファイルの例:

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
  PubkeyAuthentication yes
  IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa
# ./Azure Keys
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

この SSH 構成には、サービスごとのセクションがあり、各サービスに専用のキー ペアを割り当てることができます。既定の設定は、ログインしたホストが、先行するいずれのグループとも合致しない場合に適用されます。


### 構成ファイルについて

`Host` = 端末で呼び出すホストの名前。`ssh fedora22` は、`Host fedora22` という名称の設定ブロック内の値を使用するよう `SSH` に伝えています。注: このラベルは、サーバーの実際のホスト名を表すものではありません。実際の用途に合っていれば、どのような名称を使用してもかまいません。

`Hostname 102.160.203.241` = ログインするサーバーの IP アドレスまたは DNS 名。これは、サーバーへのルーティングに使用されます。内部 IP に対応する外部 IP を指定できます。

`User git` = Azure VM にログインするときに使用するリモート ユーザー アカウント。

`PubKeyAuthentication yes` = SSH キーを使ってログインしたいという意思を SSH に伝えています。

`IdentityFile /home/ahmet/.ssh/azure_fedora_id_rsa` = ログインを認証するサーバーにどのキー ペアを提示するかを SSH に伝えています。


## パスワードを使わずに SSH で Linux にログインする

SSH キー ペアを作成し、SSH 構成ファイルに必要な変更を加えたら、すばやく安全に Linux VM にログインすることができます。SSH キーを使用して初めてサーバーにログインするとき、キー ファイルのパスフレーズを入力するよう求められます。

```bash
ssh fedora22
```

### コマンドの説明

`ssh fedora22` が実行されると SSH はまず、`Host fedora22` ブロックの設定を見つけて読み込んだ後、最後のブロック `Host *` から残りの設定をすべて読み込みます。

## 次のステップ

次のステップでは、新しい SSH 公開キーを使用して Azure Linux VM を作成します。Azure VM は、SSH 公開キーをログインとして作成した方が、既定のログイン方法であるパスワードを使って作成するよりも高いセキュリティが得られます。ログインに SSH キーを使用する Azure VM は、パスワードによるログインを無効とするように既定で構成されているため、総当たり攻撃によって突破されることはありません。

- [Azure テンプレートを使用して安全な Linux VM を作成する](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
- [Azure ポータルを使用して安全な Linux VM を作成する](virtual-machines-linux-quick-create-portal.md)
- [Azure CLI を使用して安全な Linux VM を作成する](virtual-machines-linux-quick-create-cli.md)

<!---HONumber=AcomDC_0525_2016-->