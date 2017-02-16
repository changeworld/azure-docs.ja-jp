### <a name="prepare-for-push-install-on-linux-servers"></a>Linux サーバーへのプッシュ インストールの準備

1. Linux マシンとプロセス サーバー間にネットワーク接続が存在することを確認します。
2. プロセス サーバーから使用できる、マシンにアクセスするためのアカウントを作成します。 このアカウントは、ソース Linux サーバーの **root** ユーザーである必要があります (このアカウントは、プッシュ インストール/更新にのみ使用されます)。
3. ソース Linux サーバーの `/etc/hosts` ファイルに、ローカル ホスト名を、すべてのネットワーク アダプターに関連付けられた IP アドレスにマップするエントリが含まれることを確認します。
4. レプリケートするマシンに最新の openssh、openssh-server、openssl の各パッケージをインストールします。
5. SSH がポート 22 で有効であり実行中であることを確認します。
6. 以下の手順で、sshd_config ファイルで SFTP サブシステムとパスワード認証を有効にします。
  - **root** としてログインします。
  - /etc/ssh/sshd_config ファイルで、**PasswordAuthentication** で始まる行を見つけます。
  - この行のコメントを解除し、値 **no** を **yes** に変更します。
   6.4 **Subsystem** で始まる行を見つけ、その行のコメントを解除します。

     ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

7. CSPSConfigtool で作成したアカウントを追加します。

    - 構成サーバーにログインします。
    - **cspsconfigtool.exe** を開きます。 (これはデスクトップにショートカットがあり、%ProgramData%\home\svsystems\bin フォルダーに保存されています)。
    - **[アカウントの管理]** タブの **[アカウントの追加]** をクリックします。
    - 作成したアカウントを追加します。 アカウントの追加後、マシンのレプリケーションを有効にするときに資格情報を入力する必要があります。


<!--HONumber=Jan17_HO3-->


