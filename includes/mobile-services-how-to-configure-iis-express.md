
1. モバイル サービスが現在 IIS Express で実行されている場合は、そのモバイル サービスを停止してください。IIS Express トレイ アイコンを右クリックし、モバイル サービスに対応する **[stop]** をクリックします。

    ![](./media/mobile-services-how-to-configure-iis-express/iis-express-tray-stop-site.png)


2. コマンド プロンプト ウィンドウで **ipconfig** コマンドを実行し、ワークステーションに対応する有効なローカル IP アドレスを参照します。

    ![](./media/mobile-services-how-to-configure-iis-express/ipconfig.png)


3. Visual Studio で、IIS Express に対応する applicationhost.config ファイルを開きます。このファイルは、ユーザー プロファイル ディレクトリの次のサブディレクトリにあります。

        C:\Users\<your profile name>\Documents\IISExpress\config\applicationhost.config

4. サービスに対するリモート接続要求を許可するように、IIS Express を構成します。この作業を実行するには、applicationhost.config ファイルでモバイル サービスに対応する site 要素を見つけ、上でメモした IP アドレスを使用して、ポートに対応する新しい  `binding` 要素を追加します。次に、applicationhost.config ファイルを保存します。 

    更新後の site 要素は次のようになります:

        <site name="todolist_Service(1)" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="C:\Archive\GetStartedDataWP8\C#\todolist_Service" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:58203:localhost" />
                <binding protocol="http" bindingInformation="*:58203:192.168.137.72" />
            </bindings>
        </site>

5. [Windows ファイアウォール] コンソールを開き、このポートへの接続を許可する新しいポート規則を作成します。Windows ファイアウォールで新しいポート規則を作成する方法の詳細については、「[ローカル コンピューターにポート規則を作成するには]」を参照してください。

    >[AZURE.NOTE] テスト コンピューターがドメインに参加している場合は、ファイアウォールの例外がドメイン ポリシーによって制御されている可能性があります。この場合は、コンピューター上のポートに対して例外を適用するために、ドメイン管理者に問い合わせる必要があります。

    ここまでで、モバイル サービスをホストする IIS Express と組み合わせてテストを実行するための構成が完了しました。 

    >[AZURE.NOTE] ローカルでサービスのテストが完了した後、作成した Windows ファイアウォールの規則を削除する必要があります。 


<!-- URLs. -->
[ローカル コンピューターにポート規則を作成するには]:  http://go.microsoft.com/fwlink/?LinkId=392240

<!--HONumber=42-->
