<properties
   pageTitle="Web UI 経由の ACS コンテナー管理"
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
   
Mesos はクラスター化されたワークロードをデプロイし、拡張縮小するための環境を提供し、基礎となるハードウェアを抽象化します。Mesos に加え、コンピューティング ワークロードのスケジュールと実行をフレームワークで管理します。さまざまな人気のワークロードのフレームワークを利用できますが、このドキュメントでは、Marathon でコンテナー デプロイを作成し、拡大縮小する方法について説明します。以下の例を進める前に、ACS で Mesos クラスターを構成し、そのクラスターにリモート接続する必要があります。これらの項目の詳細については、次の記事を参照してください。

- [Azure コンテナー サービス クラスターをデプロイする](./container-service-deployment.md) 
- [ACS クラスターに接続する](./container-service-connect.md)

## Mesos UI について知る

SSH トンネルを確立したら、http://localhost/Mesos に移動します。これで Mesos Web UI が読み込まれます。このページから、有効なエージェント、タスクの状態、リソースの可用性など、Mesos クラスターに関する情報を収集できます。

![Create deployment](media/ui1.png)

## Marathon UI について知る

Marathon UI を表示するには、http://localhost/Marathon に移動します。この画面から、ACS Mesos クラスターで新しいコンテナーやその他のアプリケーションを開始できます。実行中のコンテナーやアプリケーションに関する情報も表示されます。

![Create deployment](media/ui2.png)

## Docker 形式のコンテナーのデプロイ

Marathon を使用し、Mesos クラスターで新しいコンテナーを開始するには、`Create Application` ボタンをクリックします。新しいアプリケーションのフォームを使用し、アプリケーションまたはコンテナーのパラメーターを定義します。この例では、単純な Nginx コンテナーがデプロイされます。次の情報を入力します。完了したら、[作成] をクリックします。
 
フィールド | 値
----------------|-----------
ID | nginx
イメージ | nginx
ネットワーク | ブリッジ
コンテナー ポート | 80
ホスト ポート | 80
プロトコル | TCP

![Create deployment](media/ui3.png)

Marathon メイン ページに戻ると、コンテナーのデプロイ ステータスを確認できます。

![Create deployment](media/ui4.png)

Mesos アプリ (http://localhost/Mesos)) に戻すと、タスクが、この場合は Docker 形式のコンテナーが Mesos クラスターで実行されていることがわかります。タスクが実行されているクラスター ノードを確認することもできます。

![Create deployment](media/ui5.png)

## コンテナーの拡張

Marathon Web UI では、コンテナーのインスタンス数を増減することもできます。これを実行するには、Marathon ページに移動し、拡大縮小するコンテナーを選択し、`scale` ボタンをクリックします。[Scale Application] ウィンドウで、任意のコンテナー インスタンス数を入力し、`Scale Application` を選択します。

![Create deployment](media/ui6.png)

拡大縮小操作が完了すると、同じタスクの複数のエージェントが Mesos エージェントに分散されているのを確認できます。

![Create deployment](media/ui8.png)

<!---HONumber=AcomDC_0323_2016-->