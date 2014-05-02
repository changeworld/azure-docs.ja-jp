<properties linkid="manage-linux-common-tasks-user-root-privileges" urlDisplayName="root 権限の使用" pageTitle=" Azure 上の Linux 仮想マシンでの root 権限の使用" metaKeywords="" description="Azure 上の Linux 仮想マシンで root 権限を使用する方法について説明します。" metaCanonical="" services="virtual-machines" documentationCenter="" title="Azure 上の Linux 仮想マシンでの root 権限の使用" authors="" solutions="" manager="" editor="" />




#Azure 上の Linux 仮想マシンでの root 権限の使用

`sudo` コマンドを使用すると、Linux 仮想マシンの昇格した特権でコマンドを実行できます。ただし、システムがプロビジョニングされている方法によって操作が異なります。

1. **SSH キーとパスワード、またはパスワードのみ** - 仮想マシンが証明書 (`.CER` ファイル) とパスワード、またはユーザー名とパスワードだけでプロビジョニングされている場合。この場合は、コマンドを実行する前に `sudo` からユーザーのパスワードの入力が求められます。

2. **SSH のキーのみ** - 仮想マシンが証明書 (`.cer` ファイルまたは `.pem` ファイル) を使用してプロビジョニングされ、パスワードがない場合。この場合は、コマンドを実行する前に `sudo` からユーザーのパスワードの入力が求められることはありません。


##SSH キーとパスワード、またはパスワードのみ

SSH キーまたはパスワード認証を使用して Linux 仮想マシンにログオンし、`sudo` を使用してコマンドを実行します。たとえば、次のようになります。

	# sudo <コマンド>
	[sudo] azureuser のパスワード

この場合はパスワードの入力が求められます。パスワードを入力した後 `sudo` によってコマンドが `root` 権限で実行されます。


##SSH キーのみ

SSH キー認証を使用して Linux 仮想マシンにログオンし、`sudo` を使用してコマンドを実行します。たとえば、次のようになります。

	# sudo <コマンド>

この場合はパスワードの入力が求められません。`<Enter>` キーを押した後 `sudo` によってコマンドが `root` 権限で実行されます。


