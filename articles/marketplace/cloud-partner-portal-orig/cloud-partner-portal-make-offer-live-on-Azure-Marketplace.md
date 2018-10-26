---
title: Azure Marketplace で仮想マシン プランを公開する
description: Azure Marketplace で仮想マシン プランを公開する
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ff3ca665382efc9133a4c4ce3f3fde25c03c5e52
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2018
ms.locfileid: "48808560"
---
<a name="make-your-virtual-machine-offer-live-on-azure-marketplace"></a>Azure Marketplace で仮想マシン プランを公開する
=========================================================

オファーの詳細をすべて設定したら、オファーを発行し、Azure Marketplace で公開できます。 オファーを公開するにはいくつかの段階があります。 マーケティング コンテンツと VM イメージが、Azure Certified として認定されるための品質要件を満たしていることを確認します。

![プラン公開シーケンス 0](./media/cloud-partner-portal-offer-go-live-azure-marketplace/makeanofferlive.png)

このプロセスをさらに詳しく見ていき、各ステップで何が起こなわれているのかを理解しましょう。 このプロセスでは、オファーの進行の継続を確認する必要があります。

<a name="publishing-process"></a>発行プロセス
------------------

[エディター] タブの [発行] をクリックして、発行プロセスを開始します。

![オファー公開シーケンス 1 - 発行](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publish.png)

[状態] タブに、発行ステップと、プランがプロセスのどの段階にあるかが表示されます。

![オファー公開シーケンス 2 - 状態](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status.png)

発行プロセスの任意の時点でサインインし、[すべてのオファー] タブをクリックして、オファーの最新の状態を確認することもできます。 プランの状態を直接クリックすると、発行プロセスにおけるプランの現在の段階について詳細を確認できます。

![オファー公開シーケンス 3 - すべてのオファーの状態](./media/cloud-partner-portal-offer-go-live-azure-marketplace/alloffersstatus.png)

発行の各ステップを見ていきましょう。各ステップで何が行われるのかを説明し、各ステップの推定所要時間を示します。

**前提条件の検証 (1 日以内)**

[発行] をクリックすると、オファーの必須フィールドがすべて確実に入力されるようにするために、自動チェックが実行されます。 いずれかのフィールドが設定されていない場合、そのフィールドの横に警告が表示されます。フィールドを正しく設定し、もう一度 [発行] をクリックする必要があります。

この手順を正常に完了すると、電子メール アドレスの入力を求めるポップアップが表示されます。 これは、発行プロセスの残りの部分について、発行の状態通知の送信先となる電子メール アドレスです。 電子メール アドレスを送信したら、このステップがが完了します。

![オファー公開シーケンス 4 - オファーの発行](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publishyouroffer.png)

**認定 (\<5 日以内)**

このステップでは、VM イメージが Azure Certified の要件を満たしていることを確認するためにいくつかのテストが実行されます。 認定要件を確実に満たすために必要となるすべてのガイダンスについては、[こちら](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)をご覧ください。

このステップは数日かかることがあるため、Cloud パートナー ポータルからサインアウトしてかまいません。 パートナーが対処する必要のあるエラーがある場合は、電子メール通知が送信されます。 何も問題がなければ、プロセスは自動的にプロビジョニング ステップに進みます。

**プロビジョニング (\<1 日以内)**

この段階で、世界中のすべての Azure データ センターにイメージがレプリケートされます。レプリケーションが完了するまで最大 1 日かかることがあります。

**パッケージ化とリード ジェネレーションの登録 (\<1 時間以内)**

この段階で、技術コンテンツとマーケティング コンテンツを組み合わせて、Web サイトの製品ページが構成されます。

また、リード ジェネレーション機能を構成済みの場合は、CRM にテスト リードを送信して CRM 統合が機能しているかどうかが検証されます。 このステップが完了すると、CRM または Azure テーブルに仮のデータが設定されたレコードが表示されます。 リード ジェネレーションに関するすべてのドキュメントが[ここ](./cloud-partner-portal-get-customer-leads.md)に配置されます。

**プレビューで利用可能なプラン**

プレビューでプランにアクセスするために必要なステップが正常に完了したことを示す通知電子メールが送信されます。 このステップでは、プランをプレビューし、すべてが想定どおりに表示され、ステージング環境に VM が適切にデプロイされていることを確認する必要があります。

**この確認は、ホワイトリストに登録されているサブスクリプションでのみ行うことができます。**\*

**発行元のサインオフ**

プレビューですべてが正しく表示され、適切に機能していることを確認したら、いつでもプランを公開できます。 プランを公開するには、[状態] タブの [起動] をクリックします。これにより、プランを運用環境に移行し、Web サイトで公開するための手順が開始されます。 通常は、[起動] をクリックしてから、プランが Web サイトで公開されるまで数時間かかります。 Web サイトでプランが正式に公開されると、電子メール通知が送信されます。

![オファー公開シーケンス 5 - 公開](./media/cloud-partner-portal-offer-go-live-azure-marketplace/golive.png)

**公開**

Azure Marketplace と Azure Portal でオファーが公開されたので、顧客は仮想マシンを表示し、Azure サブスクリプションにデプロイできます。 どの時点でも、[すべてのプラン] タブをクリックすると、右側の列にすべてのプランの状態が表示されます。 状態をクリックすると、プランの発行フローの状態の詳細を確認できます。

<a name="error-handling"></a>エラー処理
--------------

発行プロセス中にエラーが発生する場合があります。 エラーが発生すると、エラーが発生したことを通知し、今後の手順を説明する通知電子メールが送信されます。 また、[状態] タブをクリックすることで、このプロセス中にいつでもエラーを確認できます。解決する必要がある問題を概説するエラー メッセージと共に、プロセスのどの時点でエラーが発生したのかが示されます。

![オファー公開シーケンス 6 - エラー メッセージ](./media/cloud-partner-portal-offer-go-live-azure-marketplace/errormessage.png)

発行プロセスでエラーが発生した場合、エラーを修正し、[発行] をクリックしてプロセスを再開する必要があります。 エラーの修正後に再発行するときは、発行ステップの最初 (前提条件の検証) から始める必要があります。

エラーの解決に関する問題がある場合は、サポート リクエストを開いて、サポート エンジニアの支援を受けてください。

![オファー公開シーケンス 7 - サポートを受ける](./media/cloud-partner-portal-offer-go-live-azure-marketplace/getsupport.png)

<a name="canceling-the-publishing-request"></a>発行要求の取り消し
--------------------------------

発行プロセスを開始した後に、要求を取り消すことが必要な場合があります。 発行要求は、発行元のサインオフ ステップに到達するまで取り消すことはできません。 取り消すには、[発行の取り消し] をクリックします。 発行の状態がステップ 1 にリセットされます。再度発行するには、[発行] をクリックし、その状態での手順に従う必要があります。

![オファー公開シーケンス 8 - 状態](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status5.png)

