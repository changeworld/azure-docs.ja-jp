### <a name="prepare-for-a-push-installation-on-a-linux-server"></a>Linux サーバーへのプッシュ インストールの準備

1. Linux コンピューターとプロセス サーバーの間にネットワーク接続が存在することを確認します。
1. プロセス サーバーがコンピューターへのアクセスに使用できるアカウントを作成します。 アカウントは、ソース Linux サーバーの **root** ユーザーである必要があります  このアカウントは、プッシュ インストールと更新にのみ使用します。
1. ソース Linux サーバーの /etc/hosts ファイルに、ローカル ホスト名を、すべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが存在することを確認します。
1. レプリケート対象のコンピューターに最新の openssh、openssh-server、openssl の各パッケージをインストールします。
1. SSH (Secure Shell) が有効になっており、ポート 22 で実行中であることを確認します。
1. sshd_config ファイルで SFTP サブシステムとパスワード認証を有効にします。 次の手順に従います。

    a. **root** としてサインインします。

    b. **/etc/ssh/sshd_config** ファイルで、**PasswordAuthentication** で始まる行を見つけます。

    c. この行のコメントを解除し、値を **yes** に変更します。

    d. **Subsystem** で始まる行を見つけ、その行のコメントを解除します。

      ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

    e. **sshd** サービスを再起動します。

1. CSPSConfigtool で作成したアカウントを追加します。 次の手順に従います。

    a. 構成サーバーにサインインします。

    b. **cspsconfigtool.exe** を開きます。 これはデスクトップにショートカットがあり、%ProgramData%\home\svsystems\bin フォルダーに保存されています。

    c. **[アカウントの管理]** タブの **[アカウントの追加]** を選択します。

    d. 作成したアカウントを追加します。

    d. コンピューターのレプリケーションを有効にするときに使用する資格情報を入力します。
