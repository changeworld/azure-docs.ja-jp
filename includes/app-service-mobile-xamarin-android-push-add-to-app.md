
4. `ToDoBroadcastReceiver` という名前のプロジェクトに新しいクラスを作成します。

5. 次の using ステートメントを **ToDoBroadcastReceiver** クラスに追加します。

		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;

6. **using** ステートメントと **namespace** 宣言の間に次のアクセス許可要求を追加します。

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7. 既存の **ToDoBroadcastReceiver** クラスの定義を次の内容に置き換えます。
 
	    [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
	    [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, 
	        Categories = new string[] { "@PACKAGE_NAME@" })]
	    [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, 
	        Categories = new string[] { "@PACKAGE_NAME@" })]
	    [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, 
        Categories = new string[] { "@PACKAGE_NAME@" })]
        public class ToDoBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
	        // Set the Google app ID.
	        public static string[] senderIDs = new string[] { "<PROJECT_NUMBER>" };
        }

	上記のコードの _`<PROJECT_NUMBER>`_ を、Google の開発者ポータルでアプリケーションをプロビジョニングしたときに Google によって割り当てられたプロジェクト番号に置き換える必要があります。

8. ToDoBroadcastReceiver.cs プロジェクト ファイルに、**PushHandlerService** クラスを定義する次のコードを追加します。
 
		// The ServiceAttribute must be applied to the class.
    	[Service] 
    	public class PushHandlerService : GcmServiceBase
    	{
        	public static string RegistrationID { get; private set; }
 
        	public PushHandlerService() : base(ToDoBroadcastReceiver.senderIDs) { }
    	}

	このクラスは **GcmServiceBase** から派生したものであり、このクラスに **Service** 属性を適用する必要があるということに注意してください。

	>[AZURE.NOTE]**GcmServiceBase** クラスでは、**OnRegistered()** メソッド、O**nUnRegistered()** メソッド、**OnMessage()** メソッド、および **OnError()** メソッドを実装しています。**PushHandlerService** クラスでは、これらのメソッドをオーバーライドする必要があります。

5. **OnRegistered** イベント ハンドラーをオーバーライドする次のコードを **ToDoBroadcastReceiver** クラスに追加します。

        protected override void OnRegistered(Context context, string registrationId)
        {
            System.Diagnostics.Debug.WriteLine("The device has been registered with GCM.", "Success!");
            
            // Get the MobileServiceClient from the current activity instance.
            MobileServiceClient client = ToDoActivity.CurrentActivity.CurrentClient;           
            var push = client.GetPush();

            List<string> tags = null;

            //// (Optional) Uncomment to add tags to the registration.
            //var tags = new List<string>() { "myTag" }; // create tags if you want

            try
            {
                // Make sure we run the registration on the same thread as the activity, 
                // to avoid threading errors.
                ToDoActivity.CurrentActivity.RunOnUiThread(
                    async () => await push.RegisterNativeAsync(registrationId, tags));
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(
                    string.Format("Error with Azure push registration: {0}", ex.Message));                
            }
        }

	このメソッドでは、返された GCM 登録 ID を使用して、プッシュ通知のために Azure に登録します。

10. **PushHandlerService** の **OnMessage** メソッドを次のコードでオーバーライドします。

        protected override void OnMessage(Context context, Intent intent)
        {          
            string message = string.Empty;

            // Extract the push notification message from the intent.
            if (intent.Extras.ContainsKey("message"))
            {
                message = intent.Extras.Get("message").ToString();
                var title = "New item added:";

                // Create a notification manager to send the notification.
                var notificationManager = 
                    GetSystemService(Context.NotificationService) as NotificationManager;

                // Create a new intent to show the notification in the UI. 
                PendingIntent contentIntent = 
					PendingIntent.GetActivity(context, 0, 
					new Intent(this, typeof(ToDoActivity)), 0);	          

                // Create the notification using the builder.
                var builder = new Notification.Builder(context);
                builder.SetAutoCancel(true);
                builder.SetContentTitle(title);
                builder.SetContentText(message);
                builder.SetSmallIcon(Resource.Drawable.ic_launcher);
                builder.SetContentIntent(contentIntent);
                var notification = builder.Build();

                // Display the notification in the Notifications Area.
                notificationManager.Notify(1, notification);

            }
        }

12. **OnUnRegistered()** および **OnError()** (これらはプロジェクトをコンパイルするために必要) 用の次のメソッド オーバーライド コードを追加します。

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            throw new NotImplementedException();
        }

        protected override void OnError(Context context, string errorId)
        {
            System.Diagnostics.Debug.WriteLine(
                string.Format("Error occurred in the notification: {0}.", errorId));
        }

<!---HONumber=August15_HO8-->