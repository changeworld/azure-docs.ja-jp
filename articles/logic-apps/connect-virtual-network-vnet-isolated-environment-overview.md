---
title: Azure Logic Apps から Azure Virtual Network (VNET) にアクセスする
description: この概要では、分離されたロジック アプリが、プライベートおよび専用のリソースを使用する統合サービス環境 (ISE) から Azure Virtual Network (VNET) にどのように接続するのかを示します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: b012f1ac9c5f08a7e74871ca215299904f6b1deb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958647"
---
# <a name="access-to-azure-virtual-network-vnet-resources-from-isolated-azure-logic-apps"></a>分離された Azure Logic Apps から Azure Virtual Network (VNET) のリソースにアクセスする

> [!NOTE]
> この機能は*プライベート プレビュー*段階です。 アクセスを要求するには、[参加要求をここで作成](https://aka.ms/iseprivatepreview)します。

場合によって、ご利用のロジック アプリと統合アカウントで、仮想マシン (VM) や [Azure Virtual Network (VNET)](../virtual-network/virtual-networks-overview.md) 内の他のシステムまたはサービスのようなセキュリティ保護されたリソースにアクセスしなければならないことがあります。 このアクセスを提供するため、自分のロジック アプリと統合アカウントを作成するための場所として、[*統合サービス環境* (ISE) を作成する](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)ことができます。 

![統合サービス環境を選択する](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

ISE を作成すると、プライベートの分離された Logic Apps のインスタンスがご利用の VNET にデプロイされます。 プライベート インスタンスは、ストレージなどの専用のリソースを使用し、パブリックで "グローバル" な Logic Apps サービスとは別に実行されます。 この分離は、他の Azure のテナントがご利用のアプリのパフォーマンスに与える、["うるさい隣人 (noisy neighbors)" による影響](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)を軽減するのに役立ちます。 

この概要では、ISE の作成がご利用の Azure VNET 内のリソースに直接アクセスするロジック アプリと統合アカウントにどのように役立つかを説明し、ISE とグローバルな Logic Apps サービスとの違いを比較します。

<a name="difference"></a>

## <a name="isolated-versus-global"></a>分離とグローバル

Azure で統合サービス環境 (ISE) を作成すると、Azure VNET をご利用の環境の*ピア*として選択することができます。 Azure は、Logic Apps サービスのプライベート インスタンスをご利用の VNET にデプロイするため、その結果として、専用のリソースでロジック アプリを作成して実行できる、分離された環境が生じます。 ロジック アプリを作成すると、ご利用のアプリの場所としてこの環境を選択でき、ロジック アプリが VNET のリソースに直接アクセスできるようにもなります。  

ISE のロジック アプリはグローバルな Logic Apps サービスとユーザー操作が同じで、同様の機能を提供しています。 グローバルな Logic Apps サービスから提供される同じビルトインとコネクタを使用できるだけでなく、ISE のバージョンを提供するコネクタからも選択できます。 たとえば、次のような標準コネクタは ISE で実行されるバージョンを提供しています。
 
* Azure Blob Storage、Azure File Storage、Azure Table Storage
* Azure キュー
* Azure Service Bus
* FTP
* SSH FTP (SFTP)
* SQL Server
* AS2、X12、EDIFACT

ISE と ISE 以外のコネクタには、トリガーとアクションが実行される場所に違いがあります。

* ご利用の ISE で組み込みのトリガーと、HTTP のようなアクションを使用する場合、そのようなトリガーとアクションは常にご利用のロジック アプリと同じ ISE で実行されます。 

* 2 つのバージョンを提供するコネクタの場合、1 つのバージョンは ISE で実行され、もう一方のバージョンはグローバルな Logic Apps サービスで実行されます。  

  **ISE** ラベルがあるコネクタは、常にご利用のロジック アプリと同じ ISE で実行されます。 **ISE** ラベルがないコネクタはグローバルな Logic Apps サービスで実行されます。 

  ![ISE のコネクタを選択する](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* ご利用の ISE で構成したコネクタもグローバルな Logic Apps サービスで使用できます。 

> [!IMPORTANT]
> ご利用の ISE で実行されるロジック アプリ、組み込みアクション、コネクタは、使用量ベースの料金プランではなく、異なる料金プランを使用します。 詳細については、「[Logic Apps の価格](../logic-apps/logic-apps-pricing.md)」をご覧ください。

<a name="vnet-access"></a>

## <a name="permissions-for-vnet-access"></a>VNET へのアクセス許可

統合サービス環境 (ISE) を作成すると、Azure Virtual Network (VNET) をご利用の環境の*ピア*として選択することができます。 ただし、この関係、つまり*ピアリング*は、ISE を作成するときに*しか*作成できません。 この関係により、ご利用の ISE から VNET のリソースにアクセスできるようになり、その ISE のロジック アプリが VNET のリソースに直接接続できるようになります。 ISE にリンクしている VNET のオンプレミス システムの場合、次のいずれかの項目を使用することで、ロジック アプリが直接それらのシステムにアクセスできるようになります。 

* そのシステムの ISE コネクタ (SQL Server など)

* HTTP アクション 

* カスタム コネクタ

VNET にない、または ISE コネクタを持たないオンプレミス システムには、[オンプレミスのデータ ゲートウェイをセットアップして使用した](../logic-apps/logic-apps-gateway-install.md)後も引き続き接続できます。

ご利用の環境で Azure VNET をピアとして選択するには、Azure Logic Apps サービスに対するロール ベースのアクセス制御 (RBAC) のアクセス許可をご利用の Azure VNET でセットアップする必要があります。 このタスクでは、**[ネットワークの共同作成者]** と **[Classic Contributor]\(従来の共同作成者\)** のロールを Azure Logic Apps サービスに割り当てる必要があります。 ピアリングに必要なロールのアクセス許可の詳細については、[「仮想ネットワーク ピアリングの作成、変更、削除」の「アクセス許可」のセクション](../virtual-network/virtual-network-manage-peering.md#permissions)を参照してください。

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>ISE での統合アカウント

統合アカウントは統合サービス環境 (ISE) で実行されるロジック アプリで使用できますが、その統合アカウントはリンクされているロジック アプリと*同じ ISE* を使用する必要もあります。 ISE のロジック アプリは、同じ ISE にある統合アカウントのみを参照できます。 統合アカウントを作成すると、ご利用の統合アカウントの場所として、自分の ISE を選択できます。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、<a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure Logic Apps フォーラム</a>にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、<a href="http://aka.ms/logicapps-wish" target="_blank">Logic Apps のユーザー フィードバック サイト</a>にアクセスしてください。

## <a name="next-steps"></a>次の手順

* [分離されたロジック アプリから仮想ネットワーク (VNET) に接続する](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)方法を理解する
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) の詳細を理解する
* [Azure サービスの仮想ネットワーク統合](../virtual-network/virtual-network-for-azure-services.md)について理解する
