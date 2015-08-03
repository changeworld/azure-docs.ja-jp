
次の手順とスクリーンショットは、Windows ストア クライアントのテストに適用されるものですが、Azure Mobile Services でサポートされている他のすべてのプラットフォームでこれをテストすることができます。

1. Visual Studio で、クライアント アプリケーションを実行し、作成した Dave という名前のアカウントで認証を試みます。 

    ![](./media/mobile-services-aad-rbac-test-app/dave-login.png)

2. Dave には Sales グループのメンバーシップがあります。そのため、ロール ベースのアクセス確認によりテーブル操作へのアクセスは拒否されます。クライアント アプリケーションを閉じます。

    ![](./media/mobile-services-aad-rbac-test-app/unauthorized.png)

3. Visual Studio で、クライアント アプリケーションを再度実行します。今度は、作成した Bob という名前のユーザー アカウントで認証を試みます。

    ![](./media/mobile-services-aad-rbac-test-app/bob-login.png)

4. Bob には Sales グループのメンバーシップがあります。そのため、ロール ベースのアクセス確認によりテーブル操作へのアクセスが許可されます。

    ![](./media/mobile-services-aad-rbac-test-app/success.png)

<!---HONumber=July15_HO4-->