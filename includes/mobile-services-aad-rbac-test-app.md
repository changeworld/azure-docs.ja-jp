1.  Visual Studio で、クライアント アプリケーションを実行し、作成した Dave という名前のアカウントで認証を試みます。

    ![][0]

2.  Dave には Sales グループのメンバーシップがあります。そのため、ロール ベースのアクセス確認によりテーブル操作へのアクセスは拒否されます。クライアント アプリケーションを閉じます。

    ![][1]

3.  Visual Studio で、クライアント アプリケーションを再度実行します。今度は、作成した Bob という名前のユーザー アカウントで認証を試みます。

    ![][2]

4.  Bob には Sales グループのメンバーシップがあります。そのため、ロール ベースのアクセス確認によりテーブル操作へのアクセスが許可されます。

    ![][3]

  [0]: ./media/mobile-services-aad-rbac-test-app/dave-login.png
  [1]: ./media/mobile-services-aad-rbac-test-app/unauthorized.png
  [2]: ./media/mobile-services-aad-rbac-test-app/bob-login.png
  [3]: ./media/mobile-services-aad-rbac-test-app/success.png
