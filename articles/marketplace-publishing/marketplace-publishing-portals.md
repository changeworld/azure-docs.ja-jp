<properties
   pageTitle="Marketplace のプランを作成するために必要なさまざまなポータルの概要 | Microsoft Azure"
   description="Marketplace のプランを作成するために必要なさまざまなポータルの概要"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2015"
   ms.author="hascipio" />


# 必要なポータル
プロセスを開始する前に、プランを発行するのに必要な各種ポータルについて説明しましょう。以下に、**販売者ダッシュボード、発行ポータル**、および **Azure プレビュー ポータル**の各ポータルの概要を、相互作用する順番で示します。
## 販売者ダッシュボード
[https://sellerdashboard.microsoft.com](https://sellerdashboard.microsoft.com)
### 説明
販売者ダッシュボードのアカウントは一度作成するだけで済みます。作成する前に、パートナーは販売者ダッシュボードのアカウントを会社がまだ所有していないことを確認してください。このプロセスでは、銀行口座の情報、税情報、および会社の住所情報が収集されます。

> [AZURE.NOTE]無料のプラン (またはライセンス持ち込み) のみを発行する場合、税情報と銀行情報は必要ありません。

### 使用する ID とアカウント
理想的には、配布リストまたはセキュリティ グループです (azurepublishing@partnercompany.com) など)。この配布リストまたはセキュリティ グループは、Microsoft アカウントとして登録される**必要があります**。

> [AZURE.TIP]個人に対する依存関係が削除されるため、配布リストまたはセキュリティ グループを使用することをお勧めします。ただし、個人アカウントも使用されます。

## 発行ポータル
[https://publish.windowsazure.com](https://publish.windowsazure.com)

### 説明
プランを処理し、それを公開するパートナー用のポータルです (該当する場合、マーケティング、価格設定、発行、認定など)。

### 使用する ID とアカウント
上記の**配布リスト**または**セキュリティ グループ**は、発行ポータルに初めてログインするときに使用する必要があります。その後、他のユーザーを共同管理者として追加できます。このようにして、販売者ダッシュボードの登録データにマップされます。

## Azure プレビュー ポータル
[https://ms.portal.azure.com](https://ms.portal.azure.com)
### 説明
パートナーはこのポータルに Marketplace でステージングされたプランと発行されたプランを表示できます (VM、ソリューション テンプレート、および ARM ベースの開発者サービスに適用)
### 使用する ID とアカウント
発行ポータルからプランをステージングしている間、サブスクリプション ID をホワイト リストに登録する必要があります。このポータルにログインして、ステージングされたプランをテストする間、同じサブスクリプション (関連付けられたユーザー名とパスワードがあります) を使用する必要があります。

## 関連項目
- [Getting Started: How to publish an offer for the Azure Marketplace (概要: Azure Marketplace のプランの発行方法)](marketplace-publishing-getting-started.md)

<!---HONumber=Oct15_HO3-->