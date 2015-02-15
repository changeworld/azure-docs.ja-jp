
1. [Azure の管理ポータル]にログオンし、**[Mobile Services]** をクリックし、モバイル サービスをクリックして、**[ダッシュボード]** タブをクリックします。

	![Select your mobile service](./media/mobile-services-enable-source-control/mobile-services-selection.png)

2. (オプション) Azure サブスクリプションの Mobile Services または Websites のソース管理の資格情報が設定済みの場合は、以下をスキップして手順 4 に進むことができます。それ以外の場合は、**[概要]** の下の **[ソース管理の設定]** をクリックして、**[はい]** をクリックして確定します。

	![Set up source control](./media/mobile-services-enable-source-control/mobile-setup-source-control.png)


3. **[ユーザー名]** と **[新しいパスワード]** を指定し、パスワードを確認して、チェック ボタンをクリックします。 

	![Set source control credentials](./media/mobile-services-enable-source-control/mobile-source-control-credentials.png)

	Git リポジトリがモバイル サービスに作成されます。指定した資格情報をメモしておきます。このリポジトリおよびサブスクリプション内のその他の Mobile Services リポジトリにアクセスするときに、必要になります。

4. **[構成]** タブをクリックし、**[ソース管理]** フィールドがあることを確認します。

	![Configure source control](./media/mobile-services-enable-source-control/mobile-source-control-configure.png)

	Git リポジトリの URL が表示されます。ローカル コンピューターにリポジトリを複製するために、この URL を使用します。

モバイル サービスのソース管理が有効な場合は、Git を使用して、リポジトリをローカル コンピューターに複製できます。
 
<!--HONumber=42-->
