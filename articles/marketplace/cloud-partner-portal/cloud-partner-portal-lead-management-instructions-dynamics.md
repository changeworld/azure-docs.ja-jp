---
title: "Azure Marketplace の Dynamics CRM Online での潜在顧客管理の手順 | Microsoft Docs"
description: "この記事では、パブリッシャーが Dynamics CRM Online による潜在顧客管理をセットアップする方法について説明します。"
services: cloud-partner-portal
documentationcenter: 
author: Bigbrd
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: brdi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 05b076bf173d0fbff4baaf44c57ce215dbf23979
ms.lasthandoff: 04/19/2017


---

# <a name="lead-management-instructions-for-dynamics-crm-online"></a>Dynamics CRM Online での潜在顧客管理の手順

本書では、Dynamics CRM Online システムをセットアップして Microsoft がセールス リードを提供できるようにする方法の手順を説明します。

> [!NOTE]
> **以下の手順を実行するために必要なユーザーのアクセス許可:** ソリューションをインストールするために Dynamics CRM Online インスタンスの管理者である必要があり、潜在顧客サービスの新しいサービス アカウントを作成するためにテナント管理者である必要があります。

**ソリューションのインストール:** <br/>
1. [Microsoft Marketplace Lead Writer ソリューション](https://testdriveaccount.blob.core.windows.net/testdrivecon/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)をダウンロードし、ローカルに保存します。

2. Dynamics CRM Online の設定に移動します。  <br/>
(下のイメージのようにオプションが表示されない場合は、必要なアクセス許可を持っていません) <br/>
![Dynamics でのセットアップのイメージ](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline1.png)

3.    [インポート] をクリックし、手順 1. でダウンロードしたソリューションを選択します。  <br/>
![Dynamics でのインポートのイメージ](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline2.png)

4.    ソリューションのインストールを終えます。

**ユーザーのアクセス許可:** <br/>
Dynamics CRM インスタンスに潜在顧客を書き込むことを承認できるように、Microsoft とサービス アカウント情報を共有する必要があります。 以下の手順では、サービス アカウントと、割り当てるアクセス許可を作成する方法について説明します。 <br/>
1.    [Microsoft Office 365 管理ポータル](https://go.microsoft.com/fwlink/?LinkId=225975)に移動します。

2.    [管理] タイルをクリックします。 <br/>
![Dynamics の管理セットアップのイメージ](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline3.png)

3.    [ユーザーの追加] をクリックします。<br/>
![Dynamics でのユーザーの追加のイメージ](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline4.png)

4.    潜在顧客ライター サービス用の新しいユーザーを作成します。 以下のことを確実に行ってください。 
*  パスワードを指定して、[Make this user change their password when they first sign in](このユーザーが最初にサインインしたときに自分のパスワードを変更するようにする) チェック ボックスをオフにします。
*     “ユーザー (管理者のアクセスなし)” になるロールを選択します。
*     次に示すように製品ライセンスを選択します。 (選択したライセンスに対して課金されます。ソリューションは Dynamics CRM Online Basic ライセンスでも機能します) <br/>
![Dynamics でのユーザーの追加のイメージ](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline5.png)

5.  Dynamics CRM Online に移動し、[設定]->[セキュリティ] と移動します。<br/>
![Dynamics でのセキュリティ設定のイメージ](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline6.png)

6.  手順 4. で作成したユーザーを選択し、上部のリボンにある [ロールの管理] をクリックして、下に示すようにロールを Microsoft Marketplace Lead Writer として割り当てます。 <br/>
![Dynamics での役割の管理のイメージ](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline7.png)<br/>

このロールは、インポートしたソリューションによって作成され、潜在顧客を書き込み、互換性確保のためにソリューションのバージョンを確認するためだけのアクセス許可を持っています。

7.  Dynamics 365 8.2 の場合は、インポートしたソリューションによって作成される Microsoft Marketplace Lead Writer ロールのために、追加のアクセス許可が必要です。 [セキュリティ] では、[セキュリティ ロール] を選択して Microsoft Marketplace Lead Writer のロールを見つけ、そのロールを選択して、次に示す[コア レコード] タブで、[ユーザー エンティティの UI 設定] の作成/読み取り/書き込みの設定を変更します。 <br/>
![Dynamics でのセキュリティ ロールのイメージ](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline10.jpg)<br/>
![Dynamics でのセキュリティ ロールの管理のイメージ](./media/cloud-partner-portal-lead-management-instructions-dynamics/crmonline11.jpg)<br/>


最後に、クラウド パートナー ポータルで、**[Url]**、**[ユーザー名]**、**[パスワード]** の各フィールドにアクセス情報を入力します。
