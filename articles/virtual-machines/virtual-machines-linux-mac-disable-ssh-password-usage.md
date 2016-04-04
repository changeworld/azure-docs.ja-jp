<properties
	pageTitle="SSHD の構成により Linux VM で SSH パスワードを無効化する | Microsoft Azure"
	description="SSH のパスワード ログインを無効にすることにより、Azure の Linux VM を保護します。"
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
	ms.topic="article"
	ms.date="01/29/2016"
	ms.author="v-vlivech"/>

# SSHD の構成により Linux VM で SSH パスワードを無効化する

この記事では、Linux VM のログイン セキュリティをロックダウンする方法について説明します。SSH ポート 22 が開かれるやいなや、ボットはパスワードを推測してログインしようとします。この記事では、SSH 経由でのパスワード ログインを無効にします。パスワードを使用する機能を完全に削除することにより、この種のブルート フォース攻撃から Linux VM を保護します。これには、SSH の公開/秘密キーによるログインのみを許可するように Linux SSHD が構成されるメリットもあります。これは、Linux にログインする最も安全な方法です。その可能な組み合わせにより膨大な数の秘密キーを推測する必要があり、ボットによる SSH キーのブルート フォース攻撃を阻止できます。


## 目標

- 以下のことを許可しないように SSHD を構成します。
  - パスワード ログイン
  - ルート ユーザー ログイン
  - チャレンジ/レスポンス認証
- 以下のことを許可するように SSHD を構成します。
  - SSH キーのログインのみ
- ログインしたままでの SSHD の再起動
- 新しい SSHD 構成のテスト

## はじめに

[SSH の定義](https://en.wikipedia.org/wiki/Secure_Shell)

SSHD は Linux VM で実行されている SSH Server です。SSH は、MacBook または Linux コンピューター上のシェルから実行するクライアントです。SSH は、ワークステーションと Linux VM の間の通信をセキュリティ保護および暗号化するために使用されるプロトコルでもあります。

この記事では、期間全体を通して Linux VM に対して 1 つのログインを常に開いておくことが非常に重要です。このため、2 つの端末を開き、その両方から Linux VM への SSH を開きます。一方の端末を使用して SSHD 構成ファイルを変更し、SSHD サービスを再起動します。サービスが再開したら、他の端末で変更をテストします。SSH パスワードを無効にして SSH キーだけに依存するようにするので、SSH キーが正しくない場合、VM への接続を閉じると、VM は永続的にロックされて誰もログインできなくなり、削除して再作成することが必要になります。

## 前提条件

- [Azure の Linux VM と Mac for Linux VM に SSH キーを作成する](link)
- Azure アカウント
  - [無料試用版サインアップ](https://azure.microsoft.com/pricing/free-trial/)
  - [Azure ポータル](http://portal.azure.com)
- Azure で動作している Linux VM
- `~/.ssh/` の SSH 公開/秘密キー ペア
- Linux VM 上の `~/.ssh/authorized_keys` の SSH 公開キー
- VM での sudo 権限
- 開いているポート 22

## クイック コマンド

_簡単な説明だけが必要な経験豊富な Linux 管理者は、ここから開始します。詳細な説明と手順が必要な場合は、このセクションをスキップします。_

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes

# Change PermitRootLogin to this:
PermitRootLogin no

# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no

# On the Debian Family
username@macbook$ sudo service ssh restart

# On the RedHat Family
username@macbook$ sudo service sshd restart
```

## 詳しいチュートリアル

端末 1 (T1) で Linux VM にログインします。端末 2 (T2) で Linux VM にログインします。

T2 で SSHD 構成ファイルを編集します。

```
username@macbook$ sudo vim /etc/ssh/sshd_config
```

ここでは、パスワードを無効にして SSH キーのログインを有効にする設定のみを編集します。このファイルには多くの設定があり、必要に応じて Linux と SSH をセキュリティ保護できます。調べてください。

#### パスワード ログインを無効にする

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no
```

#### 公開キー認証を有効にする

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes
```

#### ルート ログインを無効にする

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PermitRootLogin to this:
PermitRootLogin no
```

#### チャレンジ/レスポンス認証を無効にする

```
# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no
```

### SSHD を再起動する

T1 シェルから、まだログインしたままであることを確認します。これは、パスワードを無効にしたので、SSH キーが正しくない場合にロックアウトされないようにするために重要なことです。Linux VM で何らかの設定が正しくない場合でも、ユーザーはまだログインしていて、SSHD サービスを再起動する間接続は維持されるので、T1 を使用して sshd\_config を修正できます。

T2 から以下を実行します。

##### Debian ファミリで

```
username@macbook$ sudo service ssh restart
```

##### RedHat ファミリで

```
username@macbook$ sudo service sshd restart
```

VM でパスワードが無効になり、VM はブルート フォース パスワード ログイン試行から保護されます。SSH キーのみを許可すると、ログインが速く、はるかに安全になります。

<!---HONumber=AcomDC_0323_2016-->