<properties 
	pageTitle="パートナーと Enterprise Integration Pack の詳細情報 | Microsoft Azure App Service | Microsoft Azure" 
	description="Enterprise Integration Pack と Logic Apps でパートナーを使用する方法について説明します。" 
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
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# パートナーと Enterprise Integration Pack についての詳細情報

## 概要
パートナーを作成する前に、相互にやり取りされるメッセージの識別と検証に役立つ情報を、取引を始める相手の組織と共有する必要があります。こうした話し合いが終わり、取引を始める準備が整ったら、統合アカウントで*パートナー*を作成できます。

## パートナーとは
パートナーとは、企業間 (B2B) のメッセージングとトランザクションに参加するエンティティです。

## パートナーの使用方法
パートナーは、契約の作成に使用されます。契約は、パートナー間で交換されるメッセージに関する詳細を定義するものです。

契約を作成する前に、少なくとも 2 つのパートナーを統合アカウントに追加しておく必要があります。契約の対象となるパートナーのいずれかは、自分の組織である必要があります。自分の組織を表すパートナーは、**ホスト パートナー**と呼ばれます。2 つ目のパートナーは、自分の組織とメッセージを交換する他の組織を表し、**ゲスト パートナー**と呼ばれます。ゲスト パートナーには、別の会社のほか、自身の組織内の部署も指定できます。

パートナーを追加した後、追加したパートナーを使用して契約を作成します。

受信と送信の設定は、ホスト パートナー側から見た方向になります。たとえば、契約の受信設定は、ホスト パートナーがゲスト パートナーからメッセージを受信する方法を決めるものです。同様に、契約の送信設定は、ホスト パートナーがゲスト パートナーにメッセージを送信する方法を示すものです。

## パートナーを作成する方法
Azure ポータルで次の手順を実行します。
1. **[参照]** を選択します。 ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)
2. フィルター検索ボックスに「**integration**」と入力し、結果リストから **[Integration Accounts (統合アカウント)]** を選択します。 ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)
3. パートナーの追加先となる**統合アカウント**を選択します。 ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)
4.  **[パートナー]** タイルを選択します。 ![](./media/app-service-logic-enterprise-integration-partners/partner-1.png)
5. 開いた [パートナー] ブレードの **[追加]** ボタンを選択します。 ![](./media/app-service-logic-enterprise-integration-partners/partner-2.png)
6. パートナーの**名前**を入力してから、**[Qualifier (修飾子)]** を選択し、最後に**値**を入力します。この値は、アプリに届くドキュメントを識別するために使用されます。 ![](./media/app-service-logic-enterprise-integration-partners/partner-3.png)
7. *ベル*通知アイコンを選択して、パートナー作成プロセスの進捗状況を確認します。 ![](./media/app-service-logic-enterprise-integration-partners/partner-4.png)
8. **[パートナー]** タイルを選択します。これにより、タイルが更新されます。新しいパートナーが正しく追加されたことが反映されており、パートナーの増加後の数が表示されます。 ![](./media/app-service-logic-enterprise-integration-partners/partner-5.png)
10. [パートナー] タイルを選択すると、[パートナー] ブレードにも、新しく追加されたパートナーが表示されます。 ![](./media/app-service-logic-enterprise-integration-partners/partner-6.png)

## パートナーを編集する方法

統合アカウントに既に存在するパートナーを編集するには、次の手順に従います。
1. **[パートナー]** タイルを選択します。
2. [パートナー] ブレードが開いたら、編集するパートナーを選択します。
3. **[Update Partner (パートナーの更新)]** タイルで、必要な変更を行います。
4. 変更に問題がない場合は、**[保存]** リンクを選択します。問題がある場合は、**[破棄]** リンクを選択して変更を破棄します。 ![](./media/app-service-logic-enterprise-integration-partners/edit-1.png)

## パートナーを削除する方法
1. **[パートナー]** タイルを選択します。
2. [パートナー] ブレードが開いたら、編集するパートナーを選択します。
3. **[削除]** リンクを選択します。 ![](./media/app-service-logic-enterprise-integration-partners/delete-1.png)

## 次のステップ
- [契約についての詳細情報](./app-service-logic-enterprise-integration-agreements.md "Enterprise Integration の契約についての詳細情報")

<!---HONumber=AcomDC_0803_2016-->