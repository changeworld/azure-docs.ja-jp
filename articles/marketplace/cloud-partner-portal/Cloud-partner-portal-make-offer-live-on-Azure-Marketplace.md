---
title: "Azure Marketplace でプランを公開する方法  | Microsoft Docs"
description: "マーケティング コンテンツと VM イメージが Azure Certified として認定されるための品質要件を満たしていることを確認し、Web サイトでプランを公開するまでにいくつかの段階があります。"
services: marketplace-publishing
documentationcenter: 
author: rupeshazure
manager: hamidm
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/29/2017
ms.author: rupesk@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 233d62b176303ca384aae9453c2e125c0c70fad2
ms.openlocfilehash: 4acd94e4b83164d52d2ce8db414bb99de109aca1
ms.lasthandoff: 04/12/2017


---


# <a name="make-your-virtual-machine-offer-live-on-azure-marketplace"></a>Azure Marketplace で仮想マシン プランを公開する

プランの詳細をすべて設定したら、プランを発行し、Azure Marketplace で公開できます。  マーケティング コンテンツと VM イメージが Azure Certified として認定されるための品質要件を満たしていることを確認し、Web サイトでプランを公開するまでにいくつかの段階があります。


![プラン公開シーケンス 0](./media/cloud-partner-portal-offer-go-live-azure-marketplace/makeanofferlive.png)


各ステップで何が行われるのか、また、プランの処理を進めるために、このプロセスのどの時点で行動する必要があるのかについて理解を深めるために、このプロセスを詳しく見ていきましょう。 


## <a name="publishing-process"></a>発行プロセス

発行プロセスを開始するには、[エディター] タブの [発行] をクリックします。 

![プラン公開シーケンス 1](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publish.png)


[状態] タブに、発行ステップと、プランがプロセスのどの段階にあるかが表示されます。 


![プラン公開シーケンス 2](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status.png)

発行プロセスの任意の時点でログインし、[すべてのプラン] タブをクリックして、プランの最新の状態を確認することもできます。 プランの状態を直接クリックすると、発行プロセスにおけるプランの現在の段階について詳細を確認できます。

![プラン公開シーケンス 3](./media/cloud-partner-portal-offer-go-live-azure-marketplace/alloffersstatus.png)

発行の各ステップを見ていきましょう。各ステップで何が行われるのかを説明し、各ステップの推定所要時間を示します。 

**前提条件の検証 (1 日以内)**

[発行] をクリックすると、プランの必須フィールドがすべて設定されていることを確認するために自動チェックが実行されます。 いずれかのフィールドが設定されていない場合、そのフィールドの横に警告が表示されます。フィールドを正しく設定し、もう一度 [発行] をクリックする必要があります。 


この手順を正常に完了すると、電子メール アドレスの入力を求めるポップアップが表示されます。 これは、発行プロセスの残りの部分について、発行の状態通知の送信先となる電子メール アドレスです。 電子メール アドレスを送信したら、このステップがが完了します。

![プラン公開シーケンス 4](./media/cloud-partner-portal-offer-go-live-azure-marketplace/publishyouroffer.png)

**認定 (5 日以内)**

このステップでは、VM イメージが Azure Certified の要件を満たしていることを確認するためにいくつかのテストが実行されます。 認定要件を確実に満たすために必要となるすべてのガイダンスについては、[こちら](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)をご覧ください。

このステップは数日かかることがあるため、クラウド パートナー ポータルからログアウトしてかまいません。 パートナーが対処する必要のあるエラーがある場合は、電子メール通知が送信されます。 何も問題がなければ、プロセスは自動的にプロビジョニング ステップに進みます。 

**プロビジョニング (1 日以内)**

この段階で、世界中のすべての Azure データ センターにイメージがレプリケートされます。レプリケーションが完了するまで最大 1 日かかることがあります。

**パッケージ化とリード ジェネレーションの登録 (1 時間以内)**

この段階で、技術コンテンツとマーケティング コンテンツを組み合わせて、Web サイトの製品ページが構成されます。 

また、リード ジェネレーション機能を構成済みの場合は、CRM にテスト リードを送信して CRM 統合が機能しているかどうかが検証されます。 このステップが完了すると、CRM または Azure テーブルに仮のデータが設定されたレコードが表示されます。 リード ジェネレーションに関するすべてのドキュメントがここに配置されます。

**プレビューで利用可能なプラン**

プレビューでプランにアクセスするために必要なステップが正常に完了したことを示す通知電子メールが送信されます。 このステップでは、プランをプレビューし、すべてが想定どおりに表示され、ステージング環境に VM が適切にデプロイされていることを確認する必要があります。 

**この確認は、ホワイトリストに登録されているサブスクリプションでのみ行うことができます。***

**発行元のサインオフ**

プレビューですべてが正しく表示され、適切に機能していることを確認したら、いつでもプランを公開できます。 プランを公開するには、[状態] タブの [起動] をクリックします。これにより、プランを運用環境に移行し、Web サイトで公開するための手順が開始されます。 通常は、[起動] をクリックしてから、プランが Web サイトで公開されるまで数時間かかります。 Web サイトでプランが正式に公開されると、電子メール通知が送信されます。

![プラン公開シーケンス 5](./media/cloud-partner-portal-offer-go-live-azure-marketplace/golive.png)

**公開**    

Azure Marketplace と Azure Portal でプランが公開されたので、顧客は仮想マシンを表示し、Azure サブスクリプションにデプロイできます。
どの時点でも、[すべてのプラン] タブをクリックすると、右側の列にすべてのプランの状態が表示されます。 状態をクリックすると、プランの発行フローの状態の詳細を確認できます。

## <a name="error-handling"></a>エラー処理

発行プロセス中にエラーが発生する場合があります。 エラーが発生すると、エラーが発生したことを通知し、今後の手順を説明する通知電子メールが送信されます。 また、[状態] タブをクリックすることで、このプロセス中にいつでもエラーを確認できます。 解決する必要がある問題を概説するエラー メッセージと共に、プロセスのどの時点でエラーが発生したのかが示されます。 

![プラン公開シーケンス 6](./media/cloud-partner-portal-offer-go-live-azure-marketplace/errormessage.png)


発行プロセスでエラーが発生した場合、エラーを修正し、[発行] をクリックしてプロセスを再開する必要があります。 エラーの修正後に再発行するときは、発行ステップの最初 (前提条件の検証) から始める必要があります。

エラーの解決に関する問題がある場合は、サポート リクエストを開いて、サポート エンジニアの支援を受けてください。


![プラン公開シーケンス 7](./media/cloud-partner-portal-offer-go-live-azure-marketplace/getsupport.png)



## <a name="canceling-the-publishing-request"></a>発行要求の取り消し

発行プロセスを開始した後に、要求を取り消すことが必要な場合があります。 発行要求は、"発行元のサインオフ" ステップに到達するまで取り消すことはできません。 発行要求を取り消すには、[Cancel Publish (発行の取り消し)] をクリックします。 発行の状態がステップ 1 にリセットされます。再度発行するには、[発行] をクリックし、その状態での手順に従う必要があります。

![プラン公開シーケンス 8](./media/cloud-partner-portal-offer-go-live-azure-marketplace/status5.png)














