
1. 管理ポータルで、**[データ]** タブをクリックし、**TodoItem** テーブルをクリックします。 
 
2. **[todoitem]** で、**[スクリプト]** タブをクリックし、**[挿入]** をクリックします。
   
   	**TodoItem** テーブルで挿入が発生したときに呼び出される関数が表示されます。

3. insert 関数を次のコードに置き換え、**[保存]** をクリックします。

		function insert(item, user, request) {
		// Define a simple payload for a GCM notification.
	    var payload = {
	        "data": {
	            "message": item.text
	        }
	    };		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		        push.gcm.send(null, payload, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse, payload);
		                request.respond();
		                },              
		            error: function (pushResponse) {
		                console.log("Error Sending push:", pushResponse);
		                request.respond(500, { error: pushResponse });
		                }
		            });
		        },
		    error: function(err) {
		        console.log("request.execute error", err)
		        request.respond();
		    }
		  });
		}

   	これで、新しい insert スクリプトが登録されます。このスクリプトは [gcm オブジェクト](http://go.microsoft.com/fwlink/p/?LinkId=282645)を使用して、挿入が成功した後に、登録されているすべてのデバイスにプッシュ通知を送信します。

<!---HONumber=July15_HO3-->