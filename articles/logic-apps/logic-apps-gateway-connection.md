---
title: "オンプレミスのデータへのアクセス - Azure Logic Apps | Microsoft Docs"
description: "オンプレミス データ ゲートウェイに接続し、ロジック アプリでオンプレミスのデータにアクセスする方法。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: 6cb4449d-e6b8-4c35-9862-15110ae73e6a
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/05/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: ce0be184fe11a7e5873639d79961e98de730ec86
ms.lasthandoff: 03/10/2017


---
# <a name="connect-to-on-premises-data-from-logic-apps"></a>ロジック アプリからオンプレミスのデータに接続する

オンプレミスのデータにアクセスするには、サポートされている Azure Logic Apps コネクタ向けにオンプレミス データ ゲートウェイへの接続を設定します。 オンプレミス データ ゲートウェイをインストールし、ロジック アプリで使用できるように設定する方法について、以下の手順で説明します。
オンプレミス データ ゲートウェイでは、以下のデータ ソース接続がサポートされています。

*   BizTalk Server
*    DB2  
*   ファイル システム
*   Informix
*   MQ
*    Oracle Database 
*   SAP アプリケーション サーバー 
*   SAP メッセージ サーバー
*    SQL Server

これらの接続の詳細については、[Azure Logic Apps のコネクタ](https://docs.microsoft.com/azure/connectors/apis-list)に関するページを参照してください。

## <a name="requirements"></a>必要条件

* オンプレミス データ ゲートウェイをアカウント (Azure Active Directory ベースのアカウント) に関連付けるために、Azure で職場または学校の電子メール アドレスを用意する必要があります。

* @outlook.com などの Microsoft アカウントを使用している場合は、Azure アカウントを使用して[職場または学校の電子メール アドレスを作成](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal)できます。

* [ローカル コンピューターへのオンプレミス データ ゲートウェイのインストール](logic-apps-gateway-install.md)を済ませている必要があります。

* インストールは&1; つのゲートウェイ リソースにのみ関連付けることができます。 インストールしたゲートウェイを別の Azure オンプレミス データ ゲートウェイから要求することはできません。 この要求はリソースの作成時 ([このトピックの手順 2](#2-create-an-azure-on-premises-data-gateway-resource)) に行います。

## <a name="install-and-configure-the-connection"></a>インストールと接続の構成

### <a name="1-install-the-on-premises-data-gateway"></a>1.オンプレミス データ ゲートウェイのインストール

インストールが済んでいない場合は、[オンプレミス データ ゲートウェイのインストール](logic-apps-gateway-install.md)手順に従います。 オンプレミスのコンピューターにデータ ゲートウェイがインストールされたことを確認してから、他の手順に進んでください。

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2.Azure オンプレミス データ ゲートウェイ リソースの作成

ゲートウェイをインストールしたら、そのゲートウェイに Azure サブスクリプションを関連付ける必要があります。

1. ゲートウェイのインストールで使用したのと同じ職場または学校の電子メール アドレスを使用して、Azure にサインインします。
2. **[新規]** を選択します。
3. **オンプレミス データ ゲートウェイ**を見つけて選択します。
4. ゲートウェイをアカウントに関連付けるために、適切な **[インストール名]** を選択するなどして、すべての情報を入力します。
   
    ![オンプレミス データ ゲートウェイ接続][1]

5. **[作成]** を選択してリソースを作成します。

### <a name="3-create-a-logic-app-connection-in-logic-app-designer"></a>3.ロジック アプリ デザイナーでのロジック アプリ接続の作成

Azure サブスクリプションをオンプレミス データ ゲートウェイのインスタンスに関連付けたので、次はロジック アプリからゲートウェイへの接続を作成します。

1. ロジック アプリを開き、SQL Server など、オンプレミス接続をサポートするコネクタを選択します。
2. **[Connect via on-premises data gateway (オンプレミス データ ゲートウェイ経由で接続する)]** を選択します。
   
    ![ロジック アプリ デザイナー ゲートウェイの作成][2]

3. 接続する **[ゲートウェイ]** を選択し、その他の必要な接続情報を入力します。
4. **[作成]** を選択して接続を作成します。

これで、ロジック アプリで使用する接続が構成できました。

## <a name="next-steps"></a>次のステップ

* [ロジック アプリの接続の例とシナリオ](../logic-apps/logic-apps-examples-and-scenarios.md)
* [エンタープライズ統合機能](../logic-apps/logic-apps-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/logic-apps-gateway-connection/createblade.png
[2]: ./media/logic-apps-gateway-connection/blankconnection.png
[3]: ./media/logic-apps-logic-gateway-connection/checkbox.png

