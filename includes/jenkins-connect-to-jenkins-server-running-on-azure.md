1. ブラウザーで、Jenkins 仮想マシンに移動します。 Jenkins ダッシュボードはセキュリティ保護されていない HTTP 経由ではアクセスできないため、SSH を使用して仮想マシンにトンネリングする必要があることを示すメッセージが表示されます。

    ![Jenkins は、SSH を使用して Jenkins 仮想マシンに接続する方法を説明する情報を提供する。](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ssh-instructions.png)

1. SSH へのアクセスを使用して bash またはターミナル ウィンドウを開きます。

1. 次の `ssh` コマンドを入力しますが、プレースホルダーの **&lt;username>** と **&lt;domain>** を Jenkins 仮想マシンの作成時に指定した名前に置き換えます。

    ```bash
    ssh -L 127.0.0.1:8080:localhost:8080 <username>@<domain>.eastus.cloudapp.azure.com
    ```

1. `ssh` プロンプトに従って、Jenkins 仮想マシンにログインします。

1. 次のコマンドを入力して、Jenkins のロックを解除するための初期パスワードを取得します。

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

1. bash またはターミナル ウィンドウに表示されているパスワードをクリップボードにコピーします。

1. ブラウザーで `http://localhost:8080` にアクセスします。

1. 先ほど取得したパスワードを、**[Administrative password]\(管理パスワード\)** フィールドに貼り付け、**[Continue]\(続行\)** を選択します。

    ![Jenkins は、Jenkins 仮想マシンに初めて接続するときに、ロックを解除するために使用する必要がある初期パスワードを格納する。](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-unlock.png)

1. **[Install suggested plugins]\(推奨されるプラグインのインストール\)** を選択します。

    ![Jenkins では、推奨されるプラグインのコレクションを初回のエントリで簡単にインストールできる](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-customize.png)

1. **[Create First Admin User]\(最初の管理ユーザーの作成\)** ページで、Jenkins ダッシュボードの管理ユーザーとパスワードを作成し、**[Save and finish]\(保存して終了する\)** を選択します。

1. **[Jenkins is ready!]\(Jenkins の準備ができました\)**  ページで、**[Start using Jenkins]\(Jenkins の使用を開始する\)** を選択します。

    ![Jenkins がインストールされ、アクセス用に構成された後、それを使用してジョブのビルド と作成を開始できる。](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ready.png)