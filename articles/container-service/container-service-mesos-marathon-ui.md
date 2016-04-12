<properties
   pageTitle="Web UI を使用した Azure コンテナー サービスのコンテナー管理| Microsoft Azure"
   description="Marathon Web UI を使用して Azure コンテナー サービスのクラスター サービスにコンテナーをデプロイします。"
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker、コンテナー、マイクロ サービス、Mesos、Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="nepeters"/>

# Web UI 経由のコンテナー管理

Mesos はクラスター化されたワークロードをデプロイし、スケールするための環境を提供すると共に、基礎となるハードウェアを抽象化します。Mesos に加え、コンピューティング ワークロードのスケジュールと実行を管理するフレームワークもあります。

一般的な各種ワークロードに対応したフレームワークがあるものの、このドキュメントでは、Marathon を使ってコンテナー デプロイを作成し、スケールする方法について説明します。これらの例を見ていく前に、Azure コンテナー サービスで構成された Mesos クラスターが必要です。また、このクラスターへのリモート接続も必要です。これらの項目の詳細については、次の記事を参照してください。

- [Azure コンテナー サービス クラスターのデプロイ](./container-service-deployment.md)
- [Azure コンテナー サービス クラスターに接続する](./container-service-connect.md)

## Mesos UI について知る

Secure Shell (SSH) トンネルを確立したら、http://localhost/Mesos に移動します。これで Mesos Web UI が読み込まれます。このページから、有効なエージェント、タスクの状態、リソースの可用性など、Mesos クラスターに関する情報を収集できます。

![Create deployment UI 1](media/ui1.png)

## Marathon UI について知る

Marathon UI を表示するには、http://localhost/Marathon に移動します。この画面から、Azure コンテナー サービス Mesos クラスターで新しいコンテナーやその他のアプリケーションを開始できます。コンテナーとアプリケーションの実行に関する情報も確認できます。

![Create deployment UI 2](media/ui2.png)

## Docker 形式のコンテナーのデプロイ

Marathon を使用して Mesos クラスターで新しいコンテナーを開始するには、**[Create Application (アプリケーションの作成)]** ボタンをクリックします。**[New Application (新しいアプリケーション)]** フォームを使用して、アプリケーションまたはコンテナーのパラメーターを定義します。この例では、単純な Nginx コンテナーをデプロイします。次の情報を入力します。入力し終えたら **[Create (作成)]** をクリックします。

フィールド | 値
----------------|-----------
ID | nginx
イメージ | nginx
ネットワーク | ブリッジ
コンテナー ポート | 80
ホスト ポート | 80
プロトコル | TCP

![Create deployment UI 3](media/ui3.png)

Marathon メイン ページに戻ると、コンテナーのデプロイの状態を確認できます。

![Create deployment UI 4](media/ui4.png)

Mesos アプリ (http://localhost/Mesos) に戻ると、タスク (この場合は Docker 形式のコンテナー) が Mesos クラスターで実行されていることがわかります。タスクが実行されているクラスター ノードを確認することもできます。

![Create deployment UI 5](media/ui5.png)

## コンテナーのスケール

Marathon Web UI では、コンテナーのインスタンス数をスケールすることもできます。これを実行するには、Marathon ページに移動し、スケールするコンテナーを選択して、**[Scale (スケール)]** ボタンをクリックします。**[Scale Application (アプリケーションのスケール)]** ダイアログ ボックスで、任意のコンテナー インスタンス数を入力し、**[Scale Application (アプリケーションのスケール)]** を選択します。

![Create deployment UI 6](media/ui6.png)

スケール操作が完了したら、同じタスクの複数のインスタンスが Mesos エージェントに分散されているのを確認できます。

![Create deployment UI 7](media/ui8.png)

<!---HONumber=AcomDC_0406_2016-->