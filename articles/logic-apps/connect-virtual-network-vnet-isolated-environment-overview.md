---
title: 統合サービス環境 (ISE) により Azure Logic Apps から Azure Virtual Network にアクセスする
description: この概要では、統合サービス環境 (ISE) によって、Logic Apps から Azure Virtual Network (VNET) にアクセスする方法を説明します
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 12/03/2018
ms.openlocfilehash: 5046b1012e0074e9548cad050c16eef25c00cee0
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52845197"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>統合サービス環境 (ISE) を使用して、Azure Logic Apps から Azure Virtual Network リソースにアクセスする

> [!NOTE]
> この機能は*プライベート プレビュー*段階です。 アクセスを要求するには、[参加要求をここで作成](https://aka.ms/iseprivatepreview)します。

場合によって、ご利用のロジック アプリと統合アカウントで、仮想マシン (VM) や [Azure 仮想ネットワーク](../virtual-network/virtual-networks-overview.md)内の他のシステムまたはサービスのようなセキュリティで保護されたリソースにアクセスする必要があります。 このアクセスを設定するため、自分のロジック アプリと統合アカウントを実行するための ["*統合サービス環境*" (ISE) を作成する](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)ことができます。 

![統合サービス環境を選択する](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

ISE を作成すると、プライベートの分離された Logic Apps のインスタンスがご利用の Azure Virtual Network にデプロイされます。 このプライベート インスタンスは、ストレージなどの専用のリソースを使用し、パブリックで "グローバル" な Logic Apps サービスとは別に実行されます。 この分離は、他の Azure のテナントがご利用のアプリのパフォーマンスに与える、["うるさい隣人 (noisy neighbors)" による影響](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors)を軽減するのに役立ちます。 

この概要では、ISE によってロジック アプリと統合アカウントに Azure 仮想ネットワークへの直接アクセスが提供される方法を説明し、ISE とグローバルな Logic Apps サービスとの違いを比較します。

<a name="difference"></a>

## <a name="isolated-versus-global"></a>分離とグローバル

Azure で統合サービス環境 (ISE) を作成するときは、"*挿入*" 環境である Azure 仮想ネットワークを選択します。 Azure では、Logic Apps サービスのプライベート インスタンスが仮想ネットワークにデプロイされます。 このアクションにより、専用リソースでロジック アプリを作成して実行できる分離環境が作成されます。 ロジック アプリを作成するときは、アプリの場所としてこの環境を選択し、ロジック アプリで仮想ネットワークのリソースに直接アクセスできるようになります。 

ISE のロジック アプリはグローバルな Logic Apps サービスとユーザー操作が同じで、同様の機能を提供しています。 グローバルな Logic Apps サービスで同じ組み込みアクションとコネクタを使用できるだけでなく、ISE 固有のコネクタも使用できます。 たとえば、次のような標準コネクタは ISE で実行されるバージョンを提供しています。
 
* Azure Blob Storage、Azure File Storage、Azure Table Storage
* Azure キュー、Azure Service Bus、Azure Event Hubs、IBM MQ
* FTP、SFTP-SSH
* SQL Server、SQL Data Warehouse、Azure Cosmos DB
* AS2、X12、EDIFACT

ISE と ISE 以外のコネクタには、トリガーとアクションが実行される場所に違いがあります。

* ISE では、組み込みトリガーと HTTP などのアクションは常に、ロジック アプリと同じ ISE 内で動作します。 

* 2 つのバージョンを提供するコネクタの場合、1 つのバージョンは ISE で実行され、もう一方のバージョンはグローバルな Logic Apps サービスで実行されます。  

  **ISE** ラベルがあるコネクタは、常にご利用のロジック アプリと同じ ISE で実行されます。 **ISE** ラベルがないコネクタはグローバルな Logic Apps サービスで実行されます。 

  ![ISE のコネクタを選択する](./media/connect-virtual-network-vnet-isolated-environment-overview/select-ise-connectors.png)

* ISE 内で実行されるコネクタは、もグローバルな Logic Apps サービスでも使用できます。 

> [!IMPORTANT]
> ご利用の ISE で実行されるロジック アプリ、組み込みアクション、コネクタは、使用量ベースの料金プランではなく、異なる料金プランを使用します。 詳細については、「[Logic Apps の価格](../logic-apps/logic-apps-pricing.md)」をご覧ください。

<a name="vnet-access"></a>

## <a name="permissions-for-virtual-network-access"></a>仮想ネットワーク アクセスのアクセス許可

統合サービス環境 (ISE) を作成する場合、ご利用の環境を "*挿入する*" 場所として Azure 仮想ネットワークを選択することができます。 挿入では、Logic Apps サービスのプライベート インスタンスが仮想ネットワークにデプロイされます。 このアクションにより、専用リソースでロジック アプリを作成して実行できる分離環境が作成されます。 ロジック アプリを作成するときは、アプリの場所としてお使いの ISE を選択します。 その後、これらのロジック アプリでは、お使いの仮想ネットワークに直接アクセスして、そのネットワーク内のリソースに接続できます。 

仮想ネットワークに接続されているシステムの場合は、その仮想ネットワークに ISE を挿入できるので、次のいずれかを使用することで、ロジック アプリからそれらのシステムに直接アクセスできます。 

* そのシステムの ISE コネクタ (SQL Server など)

* HTTP アクション 

* カスタム コネクタ

仮想ネットワークに接続されていない、または ISE コネクタがないオンプレミス システムの場合は、[オンプレミスのデータ ゲートウェイをセットアップして使用する](../logic-apps/logic-apps-gateway-install.md)ことにより、これらのシステムに接続できます。

環境に挿入する Azure 仮想ネットワークを選択するには、Azure Logic Apps サービスに対するロールベースのアクセス制御 (RBAC) のアクセス許可を、仮想ネットワークでセットアップする必要があります。 このタスクでは、**[ネットワークの共同作成者]** と **[Classic Contributor]\(従来の共同作成者\)** のロールを Azure Logic Apps サービスに割り当てる必要があります。
これらのアクセス許可を設定するには、[ロジック アプリから Azure 仮想ネットワークに接続する方法](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#vnet-access)に関する記事をご覧ください

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>ISE での統合アカウント

統合サービス環境 (ISE) の内部のロジック アプリで統合アカウントを使用できます。 ただし、これらの統合アカウントでは、リンクされているロジック アプリと "*同じ ISE*" を使用する必要があります。 ISE のロジック アプリは、同じ ISE にある統合アカウントのみを参照できます。 統合アカウントを作成すると、ご利用の統合アカウントの場所として、自分の ISE を選択できます。

## <a name="get-support"></a>サポートを受ける

* 質問がある場合は、<a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">Azure Logic Apps フォーラム</a>にアクセスしてください。
* 機能のアイデアについて投稿や投票を行うには、<a href="https://aka.ms/logicapps-wish" target="_blank">Logic Apps のユーザー フィードバック サイト</a>にアクセスしてください。

## <a name="next-steps"></a>次の手順

* [分離されたロジック アプリから Azure Virtual Network に接続する](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)方法を理解する
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) の詳細を理解する
* [Azure サービスの仮想ネットワーク統合](../virtual-network/virtual-network-for-azure-services.md)について理解する
