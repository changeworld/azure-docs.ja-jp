---
title: 統合アカウントと Enterprise Integration Pack の概要 | Microsoft Docs
description: 統合アカウント、Enterprise Integration Pack と Logic Apps についての詳細情報
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe

---
# 統合アカウントの概要
## 統合アカウントとは
統合アカウントは Azure アカウントです。これにより、エンタープライズ統合アプリでスキーマ、マップ、証明書、パートナーおよび契約を含むアーティファクトを管理することができます。作成する統合アプリには、スキーマ、マップまたは証明書などにアクセスするために統合アカウントを使用する必要があります。

## 統合アカウントの作成
1. **[参照]** を選択します。![](./media/app-service-logic-enterprise-integration-accounts/account-1.png)
2. フィルター検索ボックスに「**integration**」と入力し、結果リストから **[Integration Accounts (統合アカウント)]**を選択します。![](./media/app-service-logic-enterprise-integration-accounts/account-2.png)
3. ページ上部のメニューから *[追加]* を選択します。![](./media/app-service-logic-enterprise-integration-accounts/account-3.png)
4. **名前**を入力 し、使用する**サブスクリプション**を選択します。新しい**リソース グループ**を作成するか、または既存のリソース グループを選択します。統合アカウントがホストされる**場所**を選択し、 **価格レベル**を選択して、 **[作成]** ボタンを選択します。
   
   この時点で、統合アカウントは選択した場所にプロビジョニングされます。これは、1 分以内に完了する必要があります。![](./media/app-service-logic-enterprise-integration-accounts/account-4.png)
5. ページを更新します。新しい統合アカウントがリストされていることを確認します。お疲れさまでした。![](./media/app-service-logic-enterprise-integration-accounts/account-5.png)

## ロジック アプリに統合アカウントをリンクする方法
ロジック アプリでマップ、スキーマ、契約、および統合アカウントにある他のアーティファクトにアクセスするには、最初にロジック アプリに統合アカウントをリンクする必要があります。

### ロジック アプリに統合アカウントをリンクする手順を次に示します。
#### 前提条件
* 統合アカウント
* ロジック アプリ

> [!NOTE]
> 開始する前に、統合アカウントおよびロジック アプリが**同じ Azure の場所**にあることを確認してください。
> 
> 

1. ロジック アプリのメニューから **[設定]** リンクを選択します。![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-1.png)
2. [設定] ブレードから **[Integration Account (統合アカウント)]** 項目を選択します。![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-2.png)
3. **[Select an Integration account (統合アカウントを選択する)]** ドロップダウン リスト ボックスからロジック アプリにリンクする統合アカウントを選択します。![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-3.png)
4. 作業内容を保存します。![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-4.png)
5. 統合アカウントがロジック アプリにリンクされていることと、統合アカウント内のすべてのアーティファクトがロジック アプリに対して使用できるようになったことを示す通知が表示されます。![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-5.png)

ロジック アプリに統合アカウントがリンクされると、ロジック アプリに移動し、XML の検証、フラット ファイルのエンコード/デコードや変換などの B2B コネクタを使用して、B2B 機能を備えたアプリを作成することができます。

## 統合アカウントの削除方法
1. **[参照]** を選択します。![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. フィルター検索ボックスに「**integration**」と入力し、結果リストから **[Integration Accounts (統合アカウント)]**を選択します。![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. 削除する**統合アカウント**を選択します。![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4. メニューに配置されている**[削除]** リンクを選択します。![](./media/app-service-logic-enterprise-integration-accounts/delete.png)
5. 選択内容を確認します。

## 統合アカウントの移動方法
統合アカウントは、新しいサブスクリプションと新しいリソース グループに簡単に移動できます。統合アカウントを移動する必要がある場合は、次の手順に従います。

> [!IMPORTANT]
> 統合アカウントを移動すると、新しいリソース ID を使用するために、すべてのスクリプトを更新する必要があります。
> 
> 

1. **[参照]** を選択します。![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. フィルター検索ボックスに「**integration**」と入力し、結果リストから **[Integration Accounts (統合アカウント)]**を選択します。![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. 削除する**統合アカウント**を選択します。![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4. メニューに配置されている**[移動]** リンクを選択します。![](./media/app-service-logic-enterprise-integration-accounts/move.png)
5. 選択内容を確認します。

## 次のステップ
* [Enterprise Integration Pack についての詳細情報](app-service-logic-enterprise-integration-overview.md "Enterprise Integration Pack についての詳細情報")
* [契約についての詳細情報](app-service-logic-enterprise-integration-agreements.md "Enterprise Integration の契約についての詳細情報")

<!---HONumber=AcomDC_0803_2016-->