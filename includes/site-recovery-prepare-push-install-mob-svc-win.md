### <a name="prepare-for-push-install-on-windows-machines"></a>Windows マシンへのプッシュ インストールの準備

1. Windows マシンとプロセス サーバー間にネットワーク接続が存在することを確認します。
2. プロセス サーバーから使用できる、マシンにアクセスするためのアカウントを作成します。 このアカウントには管理者特権 (ローカルまたはドメイン) が必要です。このアカウントはプッシュ インストールとエージェントの更新にのみ使用されます。

   > [!NOTE]
   > ドメイン アカウントを使用していない場合、ローカル マシンで "リモート ユーザー アクセス” コントロールを無効にする必要があります。 コンピューター上のリモート ユーザー アクセス制御を無効にするには、HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System レジストリ キーに新しい DWORD **LocalAccountTokenFilterPolicy** を追加してその値を 1 に設定します。 コマンド プロンプトから **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`** コマンドを実行すると、これと同じ操作を実行できます。
   >
   >
2. 保護対象のマシンの Windows ファイアウォールで、 **[ファイアウォールを介したアプリまたは機能を許可する]**を選択します。 **[ファイルとプリンターの共有]** と **[Windows Management Instrumentation]** を有効にします。 マシンがドメインに属している場合は、GPO を使用してファイアウォール設定を構成できます。

   ![ファイアウォールの設定](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

3. CSPSConfigtool で作成したアカウントを追加します。
    - 構成サーバーにログインします。
    - **cspsconfigtool.exe** を開きます。 (これはデスクトップにショートカットがあり、%ProgramData%\home\svsystems\bin フォルダーに保存されています)。
    - **[アカウントの管理]** タブの **[アカウントの追加]** をクリックします。
    - 作成したアカウントを追加します。 アカウントの追加後、マシンのレプリケーションを有効にするときに資格情報を入力する必要があります。


<!--HONumber=Jan17_HO3-->


