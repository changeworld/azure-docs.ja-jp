<properties 
	pageTitle="プッシュ通知内での絵文字の使用" 
	description="プッシュ通知内で絵文字を使用する方法"					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="piyushjo" />

#プッシュ通知内での絵文字の使用

プッシュ通知に絵文字を含めることができます。現在、Azure Mobile Engagement では、アプリ内外のテキスト通知に設定されている 3 バイトの絵文字のみがサポートされています。次の手順に従ってください。

1.  最初に 3 バイトの絵文字のライブラリを検索する必要があります。次の[リンク](http://stackoverflow.com/questions/10153529/emoji-on-mysql-and-php-why-some-symbol-yes-other-not)で、使用できるすべての絵文字を参照できます。

	![][1]

2. Azure Mobile Engagement ポータルの [リーチ] タブに移動します。

3. プッシュ通知の種類 (アナウンス、ポーリングなど) を選択します。この例では、アナウンスのプッシュを選択します。

4. その通知のテキストに到達するまで、通知のさまざまなフィールドを指定していきます。ここで絵文字を追加します。タイトルまたはメッセージ、あるいはその両方に配置するかを選択できます。

	![][2]

5. 前述のリンクから使用したい絵文字を切り取ります。タイトルまたはメッセージ、あるいはその両方で、選択した場所に絵文字を直接貼り付けます。

6. 通知の他のフィールドに入力し、保存します。

7. アナウンスをテストまたはアクティブ化すると、指定どおりの絵文字を含む通知が表示されます。

	![][3] 
	![][4]

<!-- Images. -->
[1]: ./media/mobile-engagement-use-emoji-with-push/emoji.png
[2]: ./media/mobile-engagement-use-emoji-with-push/notification_input.png
[3]: ./media/mobile-engagement-use-emoji-with-push/notification_android.png
[4]: ./media/mobile-engagement-use-emoji-with-push/notification_ios.png
 

<!----HONumber=July15_HO2-->