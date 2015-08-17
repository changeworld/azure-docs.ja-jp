

プッシュ通知は通常、互換性のあるライブラリを使用して Mobile Services などのバックエンド サービスや ASP.NET に送信されます。ライブラリがバックエンドで利用できない場合、REST API を直接使用して通知メッセージを送信できます。

通知の送信方法を確認できるチュートリアルの一覧を次に示します。

- Azure Mobile Services: Notification Hubs に統合されている Azure Mobile Services バックエンドから通知を送信する方法の例については、[Mobile Services でのプッシュ通知の使用]に関するページを参照してください。  
- ASP.NET: [Notification Hubs を使用したユーザーへのプッシュ通知]
- Azure Notification Hub Java SDK: Java からの通知を送信するには「[Java から Notification Hubs を使用する方法](../articles/notification-hubs/notification-hubs-java-backend-how-to.md)」を参照してください。これは Android の開発用に Eclipse でテストされています。
- PHP: [PHP から Notification Hubs を使用する方法](../articles/notification-hubs/notification-hubs-php-backend-how-to.md)


チュートリアルの次のセクションでは、[Notification Hub REST インターフェイス](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)を使用してアプリで直接通知メッセージを送信する方法について説明します。すべての登録デバイスは、任意のデバイスから送信された通知を受信します。

<!---HONumber=August15_HO6-->