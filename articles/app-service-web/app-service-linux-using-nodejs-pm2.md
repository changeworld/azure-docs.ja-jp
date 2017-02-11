---
title: "Web Apps on Linux での Node.js 向け PM2 構成の使用 | Microsoft Docs"
description: "Web Apps on Linux での Node.js 向け PM2 構成の使用"
keywords: "Azure App Service, Web アプリ, Node.js, PM2, Linux, OSS"
services: app-service
documentationcenter: 
author: naziml
manager: wpickett
editor: 
ms.assetid: fb420f32-6d74-49c7-992f-0ed5616e66e7
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: naziml
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a46f52198e956406c2384c45ed4323aff27259a0


---
# <a name="use-pm2-configuration-for-nodejs-in-web-apps-on-linux"></a>Web Apps on Linux での Node.js 向け PM2 構成の使用
Web Apps on Linux 向けにアプリケーション スタックを Node.js に設定した場合、Node.js スタートアップ ファイルを設定するためのオプションが次の図のように表示されます。

![Node.js スタートアップ ファイルの設定][1]

これを使用して、次のタスクのいずれかを実行できます。

* Node.js アプリのスタートアップ スクリプトの指定 (例: /bin/server.js)。
* Node.js アプリに使用する PM2 構成ファイルの指定 (例: /foo/process.json)。
  
  > [!NOTE]
  > 特定のファイルが変更されたときに Node.js プロセスを自動的に再起動させるには、PM2 構成を使用します。 そうしないと、変更の通知を受信したときもアプリケーションが再起動しません (アプリケーション コードが変更されたときなど)。
  > 
  > 

Node.js の[プロセス ファイルのドキュメント](http://pm2.keymetrics.io/docs/usage/application-declaration/)ですべてのオプションを確認できますが、process.json ファイルとして使用できるもののサンプルを次に示します。

        {
          "name"        : "worker",
          "script"      : "/bin/server.js",
          "instances"   : 1,
          "merge_logs"  : true,
          "log_date_format" : "YYYY-MM-DD HH:mm Z",
          "watch": ["/bin/server.js", "foo.txt"],
          "watch_options": {
            "followSymlinks": true,
            "usePolling"   : true,
            "interval"    : 5
          }
        }

この構成で重要な点は次のとおりです。

* "script" プロパティでは、アプリケーションの開始スクリプトを指定します。
* "instances" プロパティでは、起動するノード プロセスのインスタンス数を指定します。 複数のコアを持つ大規模な VM でアプリケーションを実行している場合は、ここでより大きな値を設定し、リソースを最大化することをお勧めします。
* "watch" 配列では、変更時にノード プロセスを再起動させるすべてのファイルを指定します。
* "watch_options" には、現時点では "usePolling" を true として指定する必要があります。これはアプリケーションのコンテンツのマウント方法が原因です。

## <a name="next-steps"></a>次のステップ
* [App Service on Linux とは](app-service-linux-intro.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-nodejs-pm2/nodejs-startup-file.png



<!--HONumber=Nov16_HO3-->


