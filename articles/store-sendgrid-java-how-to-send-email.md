<properties linkid="dev-java-how-to-access-control" urlDisplayName="SendGrid 電子メール サービス" pageTitle="SendGrid 電子メール サービスの使用方法 (Java) - Azure" metaKeywords="Azure SendGrid, Azure 電子メール サービス, Azure SendGrid Java, Azure 電子メール Java" description="Azure で SendGrid 電子メール サービスを使用して電子メールを送信する方法について説明します。コード サンプルは Java で記述されています。" metaCanonical="" services="" documentationCenter="Java" title="Java から SendGrid を使用して電子メールを送信する方法" authors="waltpo" solutions="" manager="" editor="mollybos" />





# Java から SendGrid を使用して電子メールを送信する方法

このガイドでは、Azure で SendGrid 電子メール サービスを使用して
一般的なプログラム タスクを実行する方法を紹介します。コード サンプルは Java で記述されて
います。紹介するシナリオは、**電子メールの作成**、**電子メールの送信**、
**添付ファイルの追加**、**フィルターの使用**、および**プロパティの
更新**です。SendGrid と電子メールの送信の詳細については、
「[次のステップ][]」のセクションを参照してください。

## 目次

-   [SendGrid 電子メール サービスとは][]
-   [SendGrid アカウントを作成する][]
-   [方法: javax.mail ライブラリを使用する][]
-   [方法: 電子メールを作成する][]
-   [方法: 電子メールを送信する][]
-   [方法: 添付ファイルを追加する][]
-   [方法: フィルターを使用してフッター、追跡、および分析を有効にする][]
-   [方法: 電子メールのプロパティを更新する][]
-   [方法: その他の SendGrid サービスを使用する][]
-   [次のステップ][]

## <a name="bkmk_WhatIsSendGrid"> </a>SendGrid 電子メール サービスとは

SendGrid は、信頼性の高い[トランザクション電子メール配信]、
拡張性、およびリアルタイム分析の機能を備えた[クラウドベース電子メール サービス]であり、
柔軟な API を備えているためカスタム統合も容易です。SendGrid の一般的な使用シナリオを
次に示します。

-   顧客に受信通知を自動送信する
-   顧客に広告メールを月 1 回送信するための配布リストを
    管理する
-   ブロックされた電子メールや顧客の応答性などを表す測定値を
    リアルタイムで収集する
-   傾向を認識するために役立つレポートを生成する
-   顧客の問い合わせを転送する
- アプリケーションからの通知を電子メールで送信する

詳細については、<http://sendgrid.com> を参照してください。

## <a name="bkmk_CreateSendGridAcct"> </a>SendGrid アカウントを作成する

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_HowToUseJavax"> </a>方法: javax.mail ライブラリを使用する

javax.mail ライブラリを 
<http://www.oracle.com/technetwork/java/javamail> などから取得し、
コードにインポートします。大まかにまとめると、javax.mail ライブラリを使用して SMTP 経由で
電子メールを送信するプロセスは、次の処理を実行することです。

1.  SMTP に関する値を指定します。たとえば、SMTP サーバーは、
    SendGrid では smtp.sendgrid.net になります。

        public class MyEmailer {
	       private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
	       private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";
        
		   public static void main(String[] args) throws Exception{
         	  new MyEmailer().SendMail();
           }
        
		   public void SendMail() throws Exception
           {
              Properties properties = new Properties();
           	  properties.put("mail.transport.protocol", "smtp");
           	  properties.put("mail.smtp.host", SMTP_HOST_NAME);
           	  properties.put("mail.smtp.port", 587);
           	  properties.put("mail.smtp.auth", "true");
           	  // â€¦

2.  <span class="auto-style1">javax.mail.Authenticator</span>
    クラスを継承し、
    <span class="auto-style1">getPasswordAuthentication</span> メソッドの実装で
    SendGrid のユーザー名とパスワードを返します。  

        private class SMTPAuthenticator extends javax.mail.Authenticator {
        public PasswordAuthentication getPasswordAuthentication() {
           String username = SMTP_AUTH_USER;
           String password = SMTP_AUTH_PWD;
           return new PasswordAuthentication(username, password);
        }

3.  <span class="auto-style1">javax.mail.Session</span> オブジェクトを使用して、
    認証された電子メール セッションを作成します。

        Authenticator auth = new SMTPAuthenticator();
        Session mailSession = Session.getDefaultInstance(properties, auth);

4.  メッセージを作成し、**To**、**From**、**Subject**、およびコンテンツの
    値を設定します。このコードは「[方法: 電子メールを作成する](#bkmk_HowToCreateEmail)」に示しています。
5.  <span class="auto-style1">javax.mail.Transport</span> オブジェクトを
    使用してメッセージを送信します。このコードは
    「[方法: 電子メールを送信する][How to: Send an Email]」に示しています。
    セクションを参照してください。

## <a name="bkmk_HowToCreateEmail"> </a>方法: 電子メールを作成する

次のコードは電子メールに関する値を指定する方法を示しています。

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
		"<p>Hello,</p>
		<p>Your Contoso order has <b>shipped</b>.</p>
		<p>Thank you,<br>John</br></p>",
		"text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

## <a name="bkmk_HowToSendEmail"> </a>方法: 電子メールを送信する

次のコードは電子メールを送信する方法を示しています。

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getRecipients(Message.RecipientType.TO));
    // Close the connection.
    transport.close();

## <a name="bkmk_HowToAddAttachment"> </a>方法: 添付ファイルを追加する

次のコードは添付ファイルを追加する方法を示しています。

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\\"; 
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="bkmk_HowToUseFilters"> </a>方法: フィルターを使用してフッター、追跡、および分析を有効にする

SendGrid では、"フィルター" を使用することで追加の電子メール機能を
利用できます**。その設定を電子メール メッセージに追加することで、
クリック追跡、Google 分析、サブスクリプション追跡などの
独自の機能を有効にすることができます。すべてのフィルターの一覧については、
[フィルター設定に関するページ][]を参照してください。

-   次のコードでは、フッター フィルターを挿入して、送信される電子メールの
    下部に HTML テキストが追加されるようにしています。

        message.addHeader("X-SMTPAPI", 
			"{\"filters\": 
			{\"footer\": 
			{\"settings\": 
        	{\"enable\":1,\"text/html\": 
			\"<html><b>Thank you</b> for your business.</html>\"}}}}");

-   別のフィルターの例として、クリック追跡があります。たとえば、
    電子メールのテキストに次のようなハイパーリンクが含まれており、そのクリック率を
    追跡する必要があるとします。

        messagePart.setContent(
			"Hello,
			<p>This is the body of the message. Visit 
			<a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
			Thank you.", 
        	"text/html");

-   クリック追跡を有効にするには、次のコードを使用します。

        message.addHeader("X-SMTPAPI", 
			"{\"filters\": 
			{\"clicktrack\": 
			{\"settings\": 
        	{\"enable\":1}}}}");

## <a name="bkmk_HowToUpdateEmail"> </a>方法: 電子メールのプロパティを更新する

一部の電子メールのプロパティは、**set*Property*** を使用して上書きしたり、
**add*Property*** を使用して追加したりすることができます。

たとえば、**ReplyTo** アドレスを指定するには、次のコードを使用します。

    InternetAddress addresses[] = 
		{ new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };
    
	message.setReplyTo(addresses);

**Cc** 受信者を追加するには、次のコードを使用します。

    message.addRecipient(Message.RecipientType.CC, new 
    InternetAddress("john@contoso.com"));

## <a name="bkmk_HowToUseAdditionalSvcs"> </a>方法: その他の SendGrid サービスを使用する

SendGrid の Web ベース API を使用して、Azure アプリケーションから
その他の SendGrid 機能を利用することができます。詳細については、
[SendGrid API に関するドキュメント][]を参照してください。

## <a name="bkmk_NextSteps"> </a>次のステップ

これで、SendGrid 電子メール サービスの基本を学習できました。
さらに詳細な情報が必要な場合は、次のリンク先を参照してください。

* Azure の展開での SendGrid の使用方法を示すサンプル: [Azure デプロイで Java から SendGrid を使用して電子メールを送信する方法](/ja-jp/develop/java/how-to-guides/sendgrid-sample/)
* Java からの SendGrid の使用に関する情報: <http://sendgrid.com/docs/Code_Examples/java.html>
* SendGrid API に関するドキュメント: <http://sendgrid.com/docs/API_Reference/index.html>
* Azure ユーザー向けの SendGrid 特別プラン: <http://sendgrid.com/azure.html>

  [次のステップ]: #bkmk_NextSteps
  [SendGrid 電子メール サービスとは]: #bkmk_WhatIsSendGrid
  [SendGrid アカウントの作成]: #bkmk_CreateSendGridAcct
  [方法: javax.mail ライブラリを使用する]: #bkmk_HowToUseJavax
  [方法: 電子メールを作成する]: #bkmk_HowToCreateEmail
  [方法: 電子メールを送信する]: #bkmk_HowToSendEmail
  [方法: 添付ファイルを追加する]: #bkmk_HowToAddAttachment
  [方法: フィルターを使用してフッター、追跡、および分析を有効にする]: #bkmk_HowToUseFilters
  [方法: 電子メールのプロパティを更新する]: #bkmk_HowToUpdateEmail
  [方法: その他の SendGrid サービスを使用する]: #bkmk_HowToUseAdditionalSvcs
  [http://sendgrid.com]: http://sendgrid.com
  [http://sendgrid.com/pricing.html]: http://sendgrid.com/pricing.html
  [http://www.sendgrid.com/azure.html]: http://www.sendgrid.com/azure.html
  [http://sendgrid.com/features]: http://sendgrid.com/features
  [http://www.oracle.com/technetwork/java/javamail]: http://www.oracle.com/technetwork/java/javamail/index.html
  [フィルター設定に関するページ]: http://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
  [SendGrid API に関するドキュメント]: http://sendgrid.com/docs/API_Reference/index.html
  [http://sendgrid.com/azure.html]: http://sendgrid.com/azure.html
  [cloud-based email service (クラウド ベースの電子メール サービス)]: http://sendgrid.com/solutions
  [transactional email delivery (トランザクション電子メール配信)]: http://sendgrid.com/transactional-email

