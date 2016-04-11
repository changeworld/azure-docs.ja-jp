<properties
		pageTitle="Azure の Linux VM へのユーザーの追加 | Microsoft Azure"
		description="Azure 上の Linux VM にユーザーを追加します。"
		services="virtual-machines-linux"
		documentationCenter=""
		authors="vlivech"
		manager="timlt"
		editor=""
		tags="azure-resource-manager"
/>

<tags
		ms.service="virtual-machines-linux"
		ms.workload="infrastructure-services"
		ms.tgt_pltfrm="vm-linux"
		ms.devlang="na"
		ms.topic="article"
		ms.date="03/04/2016"
		ms.author="v-livech"
/>

# Azure VM へのユーザーの追加

新しい Linux VM を起動するときに必ず最初に行うタスクの 1 つは、新しいユーザーの作成です。この記事では、sudo ユーザー アカウントを作成し、パスワードを設定して、SSH 公開キーを追加した後、最後に `visudo` を使用してパスワードなしで sudo を許可するまでの手順を説明します。

前提条件としては、[Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)、[SSH の公開キーと秘密キー](virtual-machines-linux-mac-create-ssh-keys.md)、Azure リソース グループがあり、Azure CLI がインストールされていて、`azure config mode arm` を使用して Azure リソース マネージャー モードに切り替えてあることです。

## クイック コマンド

```bash
# Add a new user on RedHat family distros
bill@slackware$ sudo useradd -G wheel exampleUser

# Add a new user on Debian family distros
bill@slackware$ sudo useradd -G sudo exampleUser

# Set a password
bill@slackware$ sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully

# Copy the SSH Public Key to the new user
bill@slackware$ ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver

# Change sudoers to allow no password
# Execute visudo as root to edit the /etc/sudoers file
bill@slackware$ visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL

# Verify everything
# Verify the SSH keys & User account
bill@slackware$ ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
exampleuser@exampleserver$ sudo top
```

## 詳細なチュートリアル

### はじめに

新しいサーバーで最初に行う最も一般的なタスクの 1 つは、ユーザー アカウントの追加です。ルート ログインは常に無効にする必要があり、ルート アカウント自体も Linux サーバーでは二度と使わないようにし、sudo のみを使用する必要があります。新しいユーザーを作成した後、sudo を使用してその新しいユーザーにルート昇格特権を与えるのが、Linux を管理および使用する適切な方法です。

ここで使用するコマンドは `useradd` です。このコマンドは、`/etc/passwd`、`/etc/shadow`、`/etc/group`、`/etc/gshadow` を変更して、新しい Linux ユーザーを作成します。また、コマンド ライン フラグを `useradd` コマンドに追加して、新しいユーザーを Linux の適切な sudo グループに追加します。`useradd` は `/etc/passwd` にエントリを作成しますが、新しいユーザー アカウントへのパスワードの設定は行いません。とても簡単な `passwd` コマンドを使用して、新しいユーザーの初期パスワードを作成します。最後に、sudo 規則を変更して、ユーザーがすべてのコマンドに対してパスワードを入力しなくても sudo 特権でコマンドを実行できるようにします。ユーザーは公開キーと秘密キーのペアを使用してログインしているので、ユーザー アカウントは悪意のあるユーザーに対して安全であり、パスワードなしでの sudo アクセスが許可されるものと想定します。

### Azure VM への 1 人の sudo ユーザーの追加

SSH キーを使用して Azure VM にログインします。SSH 公開キー アクセスをセットアップしていない場合は、最初に「[Using Public Key Authentication with Azure (Azure での公開キー認証の使用)](http://link.to/article)」を参照してください。

`useradd` コマンドは次の処理を行います。

- 新しいユーザー アカウントを作成します
- 同じ名前で新しいユーザー グループを作成します
- 空白のエントリを `/etc/passwd` に追加します
- 空白のエントリを `/etc/gpasswd` に追加します

`-G` コマンド ライン フラグは、適切な Linux グループに新しいユーザー アカウントを追加して、新しいユーザー アカウントにルート昇格特権を与えます。

#### ユーザーを追加する

```bash
# On RedHat family distros
bill@slackware$ sudo useradd -G wheel exampleUser

# On Debian family distros
bill@slackware$ sudo useradd -G sudo exampleUser
```

#### パスワードを設定する

`useradd` コマンドは、新しいユーザーを作成し、`/etc/passwd` と `/etc/gpasswd` の両方にエントリを追加しますが、実際にはパスワードを設定しません。パスワードの設定は、`passwd` コマンドを使用して行います。

```bash
bill@slackware$ sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```

サーバーでの sudo 特権を持つユーザーが作成されました。

### 新しいユーザー アカウントに SSH 公開キーを追加する

コンピューターから、`ssh-copy-id` コマンドを使用して、先に設定した新しいパスワードを指定します。

```bash
bill@slackware$ ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver
```

### visudo を使用してパスワードなしでの sudo の使用を許可する

`visudo` を使用して `/etc/sudoers` ファイルを編集し、この非常に重要なファイルの不正変更に対する保護階層をいくつか追加します。`visudo` を実行すると、`/etc/sudoers` ファイルはロックされて、アクティブな編集中は他の誰もそのファイルを変更できなくなります。保存または終了時にも、`visudo` によって `/etc/sudoers` ファイルの誤りがチェックされます。これにより、破損した sudoers ファイルをシステムに放置できなくなります。

既に sudo アクセス用の適切な既定グループにユーザーがいます。次に、このグループがパスワードなしで sudo を使用できるようにします。

```bash
# Execute visudo as root to edit the /etc/sudoers file
bill@slackware$ visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL
```

### ユーザー、SSH キー、sudo を検証する

```bash
# Verify the SSH keys & User account
bill@slackware$ ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
exampleuser@exampleserver$ sudo top
```

<!---HONumber=AcomDC_0330_2016-->