---
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 8/12/2020
ms.author: sngun
ms.openlocfilehash: 42d1e0f056457ba54e0102a4d23f42a81ebea08d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "90068739"
---
**SDK の提供終了はどのような方法で通知されますか?**

サポートされる SDK に速やかに移行できるように、提供終了となる SDK のサポート終了は 12 か月前に通知されます。 さらに、Azure portal、Azure の更新情報、担当サービス管理者への直接連絡など、さまざまな連絡手段で通知します。

**その 12 か月間、提供終了予定の Azure Cosmos DB SDK でアプリケーションを作成できますか?** 

はい。12 か月の通知期間中も、提供終了予定の Azure Cosmos DB SDK でアプリケーションを作成、デプロイ、変更することができます。 この 12 か月の通知期間中、適宜、新しくサポートされるバージョンの Azure Cosmos DB SDK に移行することが推奨されます。 

**提供終了日以降、サポートされていない Azure Cosmos DB SDK を使用するアプリケーションはどうなりますか?** 

提供終了日以降、Azure Cosmos DB により、提供終了になった SDK のバージョンに対してバグ修正、新機能の追加、サポートの提供は行われません。 アップグレードしない場合でも、提供終了になった SDK のバージョンから送信される要求は、引き続き Azure Cosmos DB サービスによって処理されます。 

**最新の機能と更新プログラムがあるのは、どの SDK バージョンですか?**

新機能と更新プログラムは、サポートされている最新の SDK のメジャー バージョンの最新のマイナー バージョンにのみ追加されます。 新機能、パフォーマンスの向上、バグ修正を利用するには、常に最新バージョンを使用することをお勧めします。 提供終了していない古いバージョンの SDK を利用している場合、Azure Cosmos DB への要求は引き続き機能しますが、新しい機能にはアクセスできません。  

**期限前にアプリケーションを更新できない場合、どうすればよいですか?**

可能な限り早く最新の SDK にアップグレードすることが推奨されます。 SDK が提供終了予定になると、アプリケーションを更新するために 12 か月が与えられます。 提供終了日までに更新できない場合、提供終了になったバージョンの SDK から送信された要求は引き続き Azure Cosmos DB によって処理されるため、実行中のアプリケーションは引き続き機能します。 ただし、Azure Cosmos DB では、提供終了になった SDK のバージョンに対してバグ修正、新機能の追加、サポートの提供は行われません。 

サポート プランをお持ちでテクニカル サポートが必要な場合は、サポート チケットを提出して、[お問い合わせください](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。
    


