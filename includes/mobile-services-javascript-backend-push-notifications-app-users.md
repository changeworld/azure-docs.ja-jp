1.  Azure 管理ポータルにログオンし、[**Mobile Services**] をクリックして、目的のモバイル サービスをクリックします。

    ![][0]

2.  [**プッシュ**] タブをクリックし、[**権限**] に対して [**認証されたユーザーのみ**] を選択し、[**スクリプトの編集**] をクリックします。

    ![][1]

    これにより、プッシュ通知登録のコールバック関数をカスタマイズできます。Git を使用してソース コードを編集する場合は、`.\service\extensions\push.js` で、この同じ登録関数が見つかります。

3.  既存の **register** 関数を次のコードに置き換えます。

        exports.register = function (registration, registrationContext, done) {   
            // Get the ID of the logged-in user.
            var userId = registrationContext.user.userId;    

            // Perform a check here for any disallowed tags.
            if (!validateTags(registration))
            {
                // Return a service error when the client tries 
                // to set a user ID tag, which is not allowed.      
                done("You cannot supply a tag that is a user ID");      
            }
            else{
                // Add a new tag that is the user ID.
                registration.tags.push(userId);

                // Complete the callback as normal.
                done();
            }
        };

        function validateTags(registration){
            for(var i = 0; i < registration.tags.length; i++) { 
                console.log(registration.tags[i]);           
                if (registration.tags[i]
                .search(/facebook:|twitter:|google:|microsoft:/i) !== -1){
                    return false;
                }
                return true;
            }
        }

    これによって、ログインしているユーザーの ID であるタグが登録に追加されます。指定されたタグは、ユーザーが別のユーザーの ID を登録しないように検証されます。通知がこのユーザーに送信されると、その通知はこのデバイスとそのユーザーによって登録された他のデバイスで受信されます。

4.  戻る矢印をクリックし、[**データ**] タブをクリックし、[**TodoItem**] をクリックし、[**スクリプト**] をクリックし、[**挿入**] を選択します。

  [0]: ./media/mobile-services-javascript-backend-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-javascript-backend-push-notifications-app-users/mobile-services-push-registration-endpoint.png
