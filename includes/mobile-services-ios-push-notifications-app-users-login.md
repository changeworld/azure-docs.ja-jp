
次に、登録が試行される前にユーザーが認証されるように、プッシュ通知が登録される方法を変更する必要があります。

1. **QSAppDelegate.m** で、**didFinishLaunchingWithOptions** の実装をまとめて削除します。
2. **QSTodoListViewController.m** を開き、**viewDidLoad** メソッドの末尾に次のコードを追加します。

```
// Register for remote notifications
[[UIApplication sharedApplication] registerForRemoteNotificationTypes:
UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
```

<!---HONumber=Oct15_HO3-->