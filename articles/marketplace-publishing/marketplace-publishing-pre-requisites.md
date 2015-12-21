<properties
   pageTitle="Azure Marketplace 向けプランを作成するための技術以外の前提条件 | Microsoft Azure"
   description="他のユーザーが購入できるプランを作成して、Azure Marketplace にデプロイするための要件を理解します。"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="12/06/2015"
  ms.author="hascipio; v-divte"/>

# Azure Marketplace 向けプラン作成の一般的な前提条件
プランの作成プロセスに必要な、ビジネス処理中心の一般的な前提条件を理解します。

## Microsoft 販売者として登録されていることを確認します。
Microsoft で販売者アカウントを登録する方法の詳細な手順については、「[アカウントの作成および登録](marketplace-publishing-accounts-creation-registration.md)」を参照してください。

- **既に登録されている**場合は、アカウントの所有者、または登録に使用された資格情報を確認します。
- ご自身が**発行アカウントの所有者ではない**場合は、アカウント所有者に、自分の Microsoft アカウントを共同管理者として[発行ポータル](https://publish.windowsazure.com)に追加してもらいます。**[発行元]** タブで、**[管理者]** リンクを使用します。
- Azure 発行プロセスの関係者が、このアドレスへのリンクを含む電子メールを受信するようにします。電子メールは、発行プロセスを完了するために、監視および応答する必要があります。
- 1 人のユーザーのみに関連付けられたアカウントを使用するのは避けてください。そのユーザーが会社を退職した場合、SKU に関する情報へのアクセスや発行機能に影響することがあります。

> [AZURE.IMPORTANT]無料プランのみを発行する予定 (またはライセンス持ち込み) であれば、会社の税金や銀行情報を入力する必要はありません。

> プラン作成を開始するには、会社の登録を完了する必要があります。ただし、会社が Microsoftデベロッパー センターで税金と銀行情報を入力している途中でも、開発者は[発行ポータル](https://publish.windowsazure.com)で仮想マシン イメージの作成を開始し、それらのイメージの認定取得、および Azure ステージング環境でのテストを実行できます。販売者アカウントの承認が必要になるのは、プランを Azure Marketplace に発行する最後の手順のみです。

> 販売者の登録作業で問題が発生した場合は、次の手順でサポート チケットをログしてください。1.[サポート](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&supportregion=ja-JP&pesid=15635&ccsid=635847950577064286)に問い合わせる。2.**[デベロッパー センター]** を選択する。3.**[Publisher Profile]** を選択します。4.連絡方法を選択する。


## Azure の「従量課金制」サブスクリプションを取得する
これは、VM イメージを作成して、それらのイメージを [Azure Marketplace](http://azure.microsoft.com/marketplace) に渡すために使用するサブスクリプションです。既存のサブスクリプションがない場合は、ここでサインアップしてください。https://account.windowsazure.com/signup?offer=ms-azr-0003p

## 「販売元」の国
> [AZURE.WARNING]Azure Marketplace でサービスを販売するには、登録済みエンティティが、承認されたいずれかの「販売元」の国からのものである必要があります。この制限は、支払いおよび課税上の理由から生じます。「販売元」の国の範囲は今後拡大される予定です。完全な一覧については、[Azure Marketplace 参加ポリシー](http://go.microsoft.com/fwlink/?LinkID=526833)のセクション 1b を参照してください。

## 次のステップ
次は、プランの種類ごとの技術的な前提条件を紹介します。Azure Marketplace 向けに作成するプランの種類に合わせて、記事へのリンクをクリックしてください。

| 仮想マシン イメージ | 開発者サービス | データ サービス | ソリューション テンプレート |
|-----|-----|-----|-----|
| [VM の技術的な前提条件](marketplace-publishing-vm-image-creation-prerequisites.md) | 開発者サービスの技術的な前提条件 | [データ サービスの技術的な前提条件](marketplace-publishing-data-service-creation-prerequisites.md) | [ソリューション テンプレートの技術的な前提条件](marketplace-publishing-solution-template-creation-prerequisites.md) |

## 関連項目
- [概要: Azure Marketplace へのプランの発行方法](marketplace-publishing-getting-started.md)

<!---HONumber=AcomDC_1210_2015-->