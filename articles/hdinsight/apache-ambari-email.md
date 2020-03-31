---
title: チュートリアル:Azure HDInsight で Apache Ambari のメール通知を構成する
description: この記事では、Apache Ambari で SendGrid を使用してメール通知を行う方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 03/10/2020
ms.openlocfilehash: 21376eb40fb40abe67f7e03d15aabd7d89ea62f8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "80081901"
---
# <a name="tutorial-configure-apache-ambari-email-notifications-in-azure-hdinsight"></a>チュートリアル:Azure HDInsight で Apache Ambari のメール通知を構成する

このチュートリアルでは、SendGrid を使用して Apache Ambari のメール通知を構成します。 [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) には使いやすい Web UI と REST API が用意されているため、HDInsight クラスターを簡単に管理および監視できます。 Ambari は HDInsight クラスターに含まれ、クラスターの監視と構成の変更を行うために使用します。 [SendGrid](https://sendgrid.com/solutions/) は、信頼性の高いトランザクション メール配信、拡張性、およびリアルタイム分析の機能を備えたクラウドベースの無料の電子メール サービスであり、柔軟な API を備えているためカスタム統合も容易です。 Azure ユーザーは 1 か月あたり 25,000 通の電子メールを無料で利用できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Sendgrid のユーザー名を取得する
> * Apache Ambari のメール通知を構成する

## <a name="prerequisites"></a>前提条件

* SendGrid 電子メール アカウント。 手順については、「[SendGrid を使用した Azure での電子メールの送信方法](https://docs.microsoft.com/azure/sendgrid-dotnet-how-to-send-email)」を参照してください。

* HDInsight クラスター。 [Azure portal を使用した Apache Hadoop クラスターの作成](./hdinsight-hadoop-create-linux-clusters-portal.md)に関するページを参照してください。

## <a name="obtain-sendgrid-username"></a>SendGrid のユーザー名を取得する

1. [Azure portal](https://portal.azure.com) から自分の SendGrid リソースに移動します。

1. [概要] ページから **[管理]** を選択して、ご利用のアカウントの SendGrid Web ページに移動します。

    ![Azure portal の SendGrid の概要](./media/apache-ambari-email/azure-portal-sendgrid-manage.png)

1. 左側のメニューからご利用のアカウント名に移動し、 **[アカウントの詳細]** を選択します。

    ![SendGrid ダッシュボードのナビゲーション](./media/apache-ambari-email/sendgrid-dashboard-navigation.png)

1. **[アカウントの詳細]** ページで **[ユーザー名]** を記録します。

    ![SendGrid のアカウントの詳細](./media/apache-ambari-email/sendgrid-account-details.png)

## <a name="configure-ambari-e-mail-notification"></a>Ambari のメール通知を構成する

1. Web ブラウザーから、`https://CLUSTERNAME.azurehdinsight.net/#/main/alerts` に移動します。ここで、`CLUSTERNAME` はクラスターの名前です。

1. **[Actions]\(操作\)** ボックスの一覧から **[Manage Notifications]\(通知の管理\)** を選択します。

1. **[Manage Alert Notifications]\(アラート通知の管理\)** ウィンドウから **+** アイコンを選択します。

    ![Ambari のアラート通知の作成](./media/apache-ambari-email/azure-portal-create-notification.png)

1. **[Create Alert Notification]\(アラート通知の作成\)** ダイアログから次の情報を入力します。

    |プロパティ |説明 |
    |---|---|
    |名前|通知の名前を指定します。|
    |グループ|必要に応じて構成します。|
    |重大度|必要に応じて構成します。|
    |説明|省略可能。|
    |Method|**[EMAIL]\(メール\)** のままにします。|
    |Email To (メールの送信先)|通知の宛先となるメールを指定します。複数ある場合はコンマで区切って指定してください。|
    |SMTP Server (SMTP サーバー)|`smtp.sendgrid.net`|
    |SMTP Port (SMTP ポート)|25 (暗号化なし接続) または 587 (TLS 接続)。|
    |Email From (メールの送信元)|メール アドレスを入力します。 このアドレスは本物でなくてもかまいません。|
    |Use authentication (認証の使用)|このチェック ボックスをオンにします。|
    |ユーザー名|SendGrid のユーザー名を入力します。|
    |Password|Azure で SendGrid リソースを作成するときに使用したパスワードを入力します。|
    |Password Confirmation (パスワードの確認入力)|パスワードを再入力します。|
    |Start TLS (TLS の開始)|このチェック ボックスをオンにします。|

    ![Ambari のアラート通知の作成](./media/apache-ambari-email/ambari-create-alert-notification.png)

    **[保存]** を選択します。 **[Manage Alert Notifications]\(アラート通知の管理\)** ウィンドウに戻ります。

1. **[Manage Alert Notifications]\(アラート通知の管理\)** ウィンドウの **[Close]\(閉じる\)** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、SendGrid を使用して Apache Ambari のメール通知を構成する方法について説明しました。 Apache Ambari の詳細については、次の各ページを参照してください。

* [Apache Ambari Web UI を使用した HDInsight クラスターの管理](./hdinsight-hadoop-manage-ambari.md)

* [アラート通知の作成](https://docs.cloudera.com/HDPDocuments/Ambari-latest/managing-and-monitoring-ambari/content/amb_create_an_alert_notification.html)
