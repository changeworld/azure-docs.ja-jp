
1. [Azure 管理ポータル]にログオンし、**[Mobile Services]** をクリックし、モバイル サービスをクリックして、**[ダッシュボード]** タブをクリックします。

	![モバイル サービスを選択する](./media/mobile-services-enable-source-control/mobile-services-selection.png)

2. (省略可能) Azure サブスクリプションの Mobile Services または Websites のソース管理の資格情報が設定済みの場合は、以下をスキップして手順 4 に進むことができます。それ以外の場合は、**[概要]** の下の **[ソース管理の設定]** をクリックして、**[はい]** をクリックして確定します。

	![ソース管理を設定する](./media/mobile-services-enable-source-control/mobile-setup-source-control.png)


3. **[ユーザー名]** と **[新しいパスワード]** を指定し、パスワードを確認して、チェック ボタンをクリックします。

	![ソース管理の資格情報を設定する](./media/mobile-services-enable-source-control/mobile-source-control-credentials.png)

	Git リポジトリがモバイル サービスに作成されます。指定した資格情報をメモしておきます。このリポジトリとサブスクリプション内のその他の Mobile Services リポジトリにアクセスするときに、必要になります。

4. **[構成]** タブをクリックし、**[ソース管理]** フィールドがあることを確認します。

	![ソース管理を構成する](./media/mobile-services-enable-source-control/mobile-source-control-configure.png)

	Git リポジトリの URL が表示されます。ローカル コンピューターにリポジトリを複製するために、この URL を使用します。

Mobile Services のソース管理が有効な場合は、Git を使用して、リポジトリをローカル コンピューターに複製できます。
 
<!--HONumber=54-->