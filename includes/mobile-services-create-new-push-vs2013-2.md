1. **channels** テーブルの insert.js ファイルで、次のコード行を探し、コメント アウトするかファイルから削除して、変更内容を保存します。

		sendNotifications(item.channelUri);

		function sendNotifications(uri) {
		    console.log("Uri: ", uri);
		    push.wns.sendToastText01(uri, {
		        text1: "Sample toast from sample insert"
		    }, {
		        success: function (pushResponse) {
		            console.log("Sent push:", pushResponse);
		        }
		    });
		}
		
	insert.js ファイルに対する変更内容を保存すると、新しいバージョンのスクリプトがモバイル サービスにアップロードされます。

2. サーバー エクスプローラーで、TodoItem テーブルを展開し、insert.js ファイルを開きます。現在の insert 関数を次のコードに置き換えて、変更内容を保存します。

		function insert(item, user, request) {
			request.execute({
				success: function() {
					request.respond();
					sendNotifications();
				}
			});
		
			function sendNotifications() {
				var channelsTable = tables.getTable('channels');
				channelsTable.read({
					success: function(devices) {
						devices.forEach(function(device) {
							push.wns.sendToastText04(device.channelUri, {
								text1: item.text
							}, {
								success: function(pushResponse) {
									console.log("Sent push:", pushResponse);
								}
							});
						});
					}
				});
			}
		}
		
	次に、新しい TodoItem を挿入するときに、プッシュ通知をすべての登録デバイスに送信します。

<!---HONumber=July15_HO4-->