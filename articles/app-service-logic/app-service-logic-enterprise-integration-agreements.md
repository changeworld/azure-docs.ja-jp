<properties 
	pageTitle="パートナーと Enterprise Integration Pack の概要 |Microsoft Azure App Service" 
	description="Enterprise Integration Pack と Logic Apps を使用してパートナーを使用する方法について説明します。" 
	services="logic-apps" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2016" 
	ms.author="deonhe"/>


# 契約と Enterprise Integration Pack についての詳細情報

## Overview
契約は企業間 (B2B) 通信に必要不可欠であり、これによりビジネス エンティティは業界標準のプロトコルを使用してシームレスに通信することができます。

## 契約とは

契約は、Enterprise Integration Pack に関する限り、B2B 取引先間の通信配列です。契約は、パートナーが達成したいと考えている通信に基づいており、プロトコルまたはトランスポートに固有のものです。

エンタープライズ統合には、次の 3 つのプロトコル/トランスポート標準がサポートされています。

- [AS2](./app-service-logic-enterprise-integration-as2.md)
- [X12](./app-service-logic-enterprise-integration-x12.md)
- [EDIFACT](./app-service-logic-enterprise-integration-edifact.md)

## 契約を使用する理由
契約を使用する一般的な利点をいくつか次に示します。
- さまざまな組織や事業がよく知られている形式で情報交換できることが有効です。
- B2B トランザクションを実行する際に、効率性を改善します。
- エンタープライズ統合アプリを作成する際に、作成、管理、および使用が簡単です。

## 契約の作成方法
- [AS2 契約の作成](./app-service-logic-enterprise-integration-as2.md)
- [X12 契約の作成](./app-service-logic-enterprise-integration-x12.md)

## 契約の使用方法
契約を作成すると、Azure ポータルからこれを使用して、B2B 機能を持つ [Logic Apps](./app-service-logic-what-are-logic-apps.md "Logic Apps についての詳細情報") を作成できます。

## 契約の編集方法
契約を編集するには、次の手順に従います。
1. 変更する契約を含む統合アカウントを選択します。
2. **[Agreements (契約)]** タイルを選択します。
3. **[Agreements (契約)]** ブレードで変更する契約を選択します。
4. 上部のメニューで、**[編集]** を選択します。
5. [編集] メニューが開いたら、変更を行います。次に **[OK]** ボタンを選択し、変更を保存します。

## 契約の削除方法
契約を削除するには、削除する契約を含む統合アカウント内から次の手順に従います。
1. **[Agreements (契約)]** タイルを選択します。
2. **[Agreements (契約)]** ブレードで削除する契約を選択します。
3. 上部のメニューで、**[削除]** を選択します。
4. 契約を削除することを確認します。
5. 契約が [Agreements (契約)] ブレードにリストされなくなったことに注意してください。
 

## 次のステップ
- [AS2 契約の作成](./app-service-logic-enterprise-integration-as2.md)

<!---HONumber=AcomDC_0810_2016-->