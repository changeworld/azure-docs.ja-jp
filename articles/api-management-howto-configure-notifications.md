<properties pageTitle="How to configure notifications and email templates in Azure API Management" metaKeywords="" description="Learn how to configure notifications and email templates in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How to configure notifications and email templates in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Azure API Management で通知と電子メール テンプレートを構成する方法

API Management (プレビュー) には、特定のイベントの通知を構成する機能と、API Management インスタンスの管理者および開発者との通信に使用される電子メール テンプレートを構成する機能があります。このトピックでは、使用可能なイベントの通知を構成する方法について説明し、これらのイベントに使用できる電子メール テンプレートを構成する方法の概要を示します。

## このトピックの内容

-   [発行者通知の構成][発行者通知の構成]
-   [電子メール テンプレートの構成][電子メール テンプレートの構成]

## <a name="publisher-notifications"> </a>発行者通知の構成

通知を構成するには、ご利用の API Management サービスの Azure ポータルで **[管理コンソール]** をクリックします。API Management の管理ポータルが表示されます。

> まだ API Management サービス インスタンスを作成していない場合は、「[Azure API Management の使用][Azure API Management の使用]」チュートリアルの「[API Management インスタンスの作成][API Management インスタンスの作成]」を参照してください。

![API Management console][API Management console]

左側の **[API Management]** メニューの **[通知]** をクリックして、使用可能な通知を表示します。

![Publisher notifications][Publisher notifications]

通知に対して、次のイベントを構成できます。

-   **[サブスクリプション要求 (承認が必要)]** - 指定された電子メール受信者およびユーザーは、承認が必要な API 成果物のサブスクリプション要求に関する電子メール通知を受け取ります。
-   **[新しいサブスクリプション]** - 指定された電子メール受信者およびユーザーは、新しい API 成果物のサブスクリプションに関する電子メール通知を受け取ります。
-   **[アプリケーション ギャラリー要求]** - 指定された電子メール受信者およびユーザーは、新しいアプリケーションがアプリケーション ギャラリーに送信されたときに電子メール通知を受け取ります。
-   **[BCC]** - 指定された電子メール受信者およびユーザーは、開発者宛てに送信されたすべての電子メールの電子メール ブラインド カーボン コピーを受け取ります。
-   **[新しい問題またはコメント]** - 指定された電子メール受信者およびユーザーは、新しい問題やコメントが開発者ポータルに送信されたときに電子メール通知を受け取ります。
-   **[アカウントの使用停止メッセージ]** - 指定された電子メール受信者およびユーザーは、アカウントの使用が停止されたときに電子メール通知を受け取ります。
-   **[サブスクリプション クォータ制限間近]** - 指定された電子メール受信者およびユーザーは、サブスクリプションの使用量が使用量クォータに近づいたときに電子メール通知を受け取ります。

それぞれのイベントに対し、電子メール アドレス テキスト ボックスを使用して電子メール受信者を指定するか、または一覧からユーザーを選択できます。

通知先の電子メール アドレスを指定するには、電子メール アドレス テキスト ボックスに電子メール アドレスを入力します。複数の電子メール アドレスを指定するには、電子メール アドレスをコンマで区切ります。

![Notification recipients][Notification recipients]

通知を送信するユーザーを指定するには、**[受信者の追加]** をクリックします。目的のユーザーの横のチェック ボックスをオンにし、**[OK]** をクリックします。

> 一覧には管理者のみが表示される点に注意してください。

通知の受信者を構成したら、**[保存]** をクリックして、更新された通知の受信者を適用します。

> 変更を保存しないで **[発行者通知]** タブから移動しようとすると、その旨を示すメッセージが表示されます。

## <a name="email-templates"> </a>電子メール テンプレートの構成

API Management には、サービスの管理および使用に関連して送信する電子メール メッセージ向けの電子メール テンプレートが用意されています。次の電子メール テンプレートが用意されています。

-   承認されたアプリケーション ギャラリーへの送信
-   開発者向けのお別れのあいさつ
-   開発者向けのクォータ制限間近通知
-   ユーザーの招待
-   問題に追加された新しいコメント
-   新しい問題の受信
-   アクティブ化された新しいサブスクリプション
-   サブスクリプションの更新の確認
-   サブスクリプション要求の拒否
-   サブスクリプション要求の受信

これらのテンプレートは、必要に応じて変更できます。

API Management インスタンスの電子メール テンプレートを表示および構成するには、左側の **[API Management]** メニューの **[通知]** をクリックし、**[電子メール テンプレート]** タブを選択します。

![Email templates][Email templates]

特定のテンプレートを表示および構成するには、**[テンプレート]** ボックスの一覧から目的のテンプレートを選択します。

![Email templates list][Email templates list]

それぞれの電子メール テンプレートは、件名がプレーンテキスト形式で記述され、本文定義が HTML 形式で記述されています。必要に応じてそれぞれの項目をカスタマイズできます。

![Email template editor][Email template editor]

**[パラメーター]** には、パラメーターの一覧が表示されます。件名または本文にパラメーターを挿入しておくと、電子メールの送信時にパラメーターが指定された値と置き換えられます。パラメーターを挿入するには、パラメーターを挿入する場所にカーソルを置いた状態で、パラメーター名の左側の矢印をクリックします。

**[プレビュー]** または **[テスト送信]** をクリックすると、電子メールのプレビューを表示したり、テスト用の電子メールを送信したりできます。

> プレビュー時またはテスト用の電子メールの送信時は、パラメーターは実際の値で置き換えられません。
>
> 電子メール テンプレートに加えた変更を保存するには、**[保存]** をクリックします。変更を取り消すには、**[キャンセル]** をクリックします。

  [発行者通知の構成]: #publisher-notifications
  [電子メール テンプレートの構成]: #email-templates
  [Azure API Management の使用]: ../api-management-get-started
  [API Management インスタンスの作成]: ../api-management-get-started/#create-service-instance
  [API Management console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
  [Publisher notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
  [Notification recipients]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png
  [Email templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
  [Email templates list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
  [Email template editor]: ./media/api-management-howto-configure-notifications/api-management-email-template.png
