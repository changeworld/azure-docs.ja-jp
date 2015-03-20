<properties 
	pageTitle="Azure デプロイで Java から SendGrid を使用して電子メールを送信する方法" 
	description="" 
	services="" 
	documentationCenter="java" 
	authors="thinkingserious" 
	manager="sendgrid" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork"/>

# Azure デプロイで Java から SendGrid を使用して電子メールを送信する方法

次の例では、Azure でホストされる Web ページから SendGrid を使用して電子メールを送信する方法を示しています。次のスクリーンショットに示すように、作成されたアプリケーションは電子メールに関する値の入力をユーザーに求めます。

![Email form][emailform]

送信された電子メールは次のスクリーン ショットのようになります。

![Email message][emailsent]

このトピックでコードを使用するためには次の操作を行う必要があります。

1. javax.mail JAR を <http://www.oracle.com/technetwork/java/javamail/index.html> などから入手します。
2. JAR を Java のビルド パスに追加します。
3. この Java アプリケーションの作成に Eclipse を使用している場合は、Eclipse のデプロイ アセンブリ機能を使用して、アプリケーション デプロイ ファイル (WAR) に SendGrid ライブラリを含めることができます。この Java アプリケーションの作成に Eclipse を使用していない場合、ライブラリが Java アプリケーションと同じ Azure ロールにインクルードされており、アプリケーションのクラス パスに追加されていることを確認してください。


また、電子メールを送信するには、SendGrid のユーザー名とパスワードを取得している必要があります。SendGrid を使用した開始を参照してください。 [Java から SendGrid を使用して電子メールを送信する方法](../store-sendgrid-java-how-to-send-email)です。

情報は、知識をさらに、 [Hello World アプリケーションの作成 Azure の Eclipse で](http://msdn.microsoft.com/library/windowsazure/hh690944)、または、Eclipse を使用していない場合は、Azure での Java アプリケーションをホストするための他の手法を強くお勧めします。

## 電子メール送信用の Web フォームの作成

次のコードでは、電子メール送信用のユーザー データを取得する Web フォームの作成方法を示しています。このコンテンツでは、JSP ファイルに **emailform.jsp** という名前を付けています。

	<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
	    pageEncoding="ISO-8859-1" %>
	<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
	<html>
	<head>
	<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
	<title>Email form</title>
	</head>
	<body>
	 <p>Fill in all fields and click <b>Send this email</b>.</p>
	 <br/>
	  <form action="sendemail.jsp" method="post">
	   <table>
	     <tr>
	       <td>To:</td>
	       <td><input type="text" size=50 name="emailTo">
	       </td>
	     </tr>
	     <tr>
	       <td>From:</td>
	       <td><input type="text" size=50 name="emailFrom">
	       </td>
	     </tr>
	     <tr>
	       <td>Subject:</td>
	       <td><input type="text" size=100 name="emailSubject" value="My email subject">
	       </td>
	     </tr>
	     <tr>
	       <td>Text:</td>
	       <td><input type="text" size=400 name="emailText" value="Hello,<p>This is my message.</p>Thank you." />
	       </td>
	     </tr>
	     <tr>
	       <td>SendGrid user name:</td>
	       <td><input type="text" name="sendGridUser">
	       </td>
	     </tr>
	     <tr>
	       <td>SendGrid password:</td>
	       <td><input type="password" name="sendGridPassword">
	       </td>
	     </tr>
	     <tr>
	       <td colspan=2><input type="submit" value="Send this email">
	       </td>
	     </tr>
	   </table>
	 </form>
	 <br/>
	</body>
	</html>

## 電子メール送信用のコードの作成

次のコードは、emailform.jsp によって表示されるフォームへの入力が完了すると呼び出され、電子メール メッセージを作成して送信します。このコンテンツでは、JSP ファイルに **sendemail.jsp** という名前を付けています。

	<%@ page language="java" contentType="text/html; charset=ISO-8859-1"
	    pageEncoding="ISO-8859-1" import="javax.activation.*, javax.mail.*, javax.mail.internet.*, java.util.Date, java.util.Properties" %>
	<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
	<html>
	<head>
	<meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
	<title>Email processing happens here</title>
	</head>
	<body>
	    <b>This is my send mail page.</b><p/>
	 <%
	 
	 final String sendGridUser = request.getParameter("sendGridUser");
	 final String sendGridPassword = request.getParameter("sendGridPassword");
	 
	 class SMTPAuthenticator extends Authenticator
	 {
	   public PasswordAuthentication getPasswordAuthentication()
	   {
	        String username = sendGridUser;
	        String password = sendGridPassword;
	      
	        return new PasswordAuthentication(username, password);   
	   }
	 }
	 try
	 {
	     
	     // The SendGrid SMTP server.
	     String SMTP_HOST_NAME = "smtp.sendgrid.net";
	
	     Properties properties;
	    
	     properties = new Properties();
	     
	     // Specify SMTP values.
	     properties.put("mail.transport.protocol", "smtp");
	     properties.put("mail.smtp.host", SMTP_HOST_NAME);
	     properties.put("mail.smtp.port", 587);
	     properties.put("mail.smtp.auth", "true");
	     
	     // Display the email fields entered by the user. 
	     out.println("Value entered for email Subject: " + request.getParameter("emailSubject") + "<br/>");        
	     out.println("Value entered for email      To: " + request.getParameter("emailTo") + "<br/>");
	     out.println("Value entered for email    From: " + request.getParameter("emailFrom") + "<br/>");
	     out.println("Value entered for email    Text: " + "<br/>" + request.getParameter("emailText") + "<br/>");
	
	     // Create the authenticator object.
	     Authenticator authenticator = new SMTPAuthenticator();
	     
	     // Create the mail session object.
	     Session mailSession;
	     mailSession = Session.getDefaultInstance(properties, authenticator);
	     
	     // Display debug information to stdout, useful when using the
	     // compute emulator during development.
	     mailSession.setDebug(true);
	
	     // Create the message and message part objects.
	     MimeMessage message;
	     Multipart multipart;
	     MimeBodyPart messagePart; 
	     
	     message = new MimeMessage(mailSession);
	     
	     multipart = new MimeMultipart("alternative");
	     messagePart = new MimeBodyPart();
	     messagePart.setContent(request.getParameter("emailText"), "text/html");
	     multipart.addBodyPart(messagePart);            
	
	     // Specify the email To, From, Subject and Content. 
	     message.setFrom(new InternetAddress(request.getParameter("emailFrom")));
	     message.addRecipient(Message.RecipientType.TO, new InternetAddress(request.getParameter("emailTo")));
	     message.setSubject(request.getParameter("emailSubject")); 
	     message.setContent(multipart);
	     
	     // Uncomment the following if you want to add a footer.
	     // message.addHeader("X-SMTPAPI", "{\"filters\": {\"footer\": {\"settings\": {\"enable\":1,\"text/html\": \"<html>This is my <b>email footer</b>.</html>\"}}}}");
	
	     // Uncomment the following if you want to enable click tracking.
	     // message.addHeader("X-SMTPAPI", "{\"filters\": {\"clicktrack\": {\"settings\": {\"enable\":1}}}}");
	     
	     Transport transport;
	     transport = mailSession.getTransport();
	     // Connect the transport object.
	     transport.connect();
	     // Send the message.
	     transport.sendMessage(message,  message.getRecipients(Message.RecipientType.TO));
	     // Close the connection.
	     transport.close();
	 
	    out.println("<p>Email processing completed.</p>");
	     
	 }
	 catch (Exception e)
	 {
	     out.println("<p>Exception encountered: " + 
	                        e.getMessage()     +
	                        "</p>");   
	 }
	%>
	
	</body>
	</html>

電子メールを送信するだけでなく、emailform.jsp はユーザーに結果を返します。たとえば、次のスクリーンショットのようになります。

![Send mail result][emailresult]

## 次のステップ

アプリケーションをコンピューティング エミュレーターにデプロイし、ブラウザーで emailform.jsp を実行します。フォームに値を入力し、**[Send this email]** をクリックして、sendemail.jsp で結果を確認します。

Azure 上で Java から SendGrid を使用する方法を示すために、このコードが用意されています。運用環境で Azure に展開する前に、エラー処理やその他の機能をさらに追加することができます。次に例を示します。 

* Web フォームを使用する代わりに、Azure ストレージ BLOB または SQL データベースを使用して、電子メール アドレスと電子メール メッセージを保存するようにします。Java で Azure ストレージ blob の使用方法の詳細については、次を参照してください。 [Java から Blob ストレージ サービスを使用する方法](http://www.windowsazure.com/develop/java/how-to-guides/blob-storage/)です。Java で SQL データベースの使用方法の詳細については、次を参照してください。 [Java での SQL データベースを使用して](http://www.windowsazure.com/develop/java/how-to-guides/using-sql-azure-in-java/)です。
* 使用できます `RoleEnvironment.getConfigurationSettings`を web フォームを使用して、それらの値を取得するのではなく、デプロイの構成設定からの SendGrid のユーザー名とパスワードを取得します。については、 `RoleEnvironment`クラスを参照してください[JSP での Azure サービス ランタイム ライブラリを使用して](http://msdn.microsoft.com/library/windowsazure/hh690948)とでは、Azure サービス ランタイム パッケージのドキュメント<http://dl.windowsazure.com/javadoc>です。
* Java での SendGrid の使用の詳細については、「[Java から SendGrid を使用して電子メールを送信する方法](../store-sendgrid-java-how-to-send-email).」を参照してください。

[emailform]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailform.jpg
[emailsent]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaEmailSent.jpg
[emailresult]: ./media/store-sendgrid-java-how-to-send-email-example/SendGridJavaResult.jpg

<!--HONumber=47-->
