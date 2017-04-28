---
title: "Azure Marketplace の Marketo での潜在顧客管理の手順 | Microsoft Docs"
description: "この記事では、パブリッシャーが Marketo による潜在顧客管理をセットアップする方法について説明します。"
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
ms.openlocfilehash: 77c9bdf6b2634e222592ac0818f608ee3cb3fe09
ms.lasthandoff: 04/19/2017


---

# <a name="lead-management-instructions-for-marketo"></a>Marketo での潜在顧客管理の手順

本書では、Marketo システムをセットアップして Microsoft がセールス リードを提供できるようにする方法の手順を説明します。 

1. Marketo の「Design Studio」へ進みます。 <br/>
![Marketo セットアップ イメージ](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

2. [New Form](新しいフォーム) をクリックします <br/>
![Marketo の新しいフォームのイメージ](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

3. 新しいフォーム ポップアップのフィールドに入力します <br/>
![Marketo フォーム作成イメージ](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4. 次のフォームで [完了] をクリックします。 書式設定やフィールドについて心配しないでください。 <br/>
![Marketo フォーム完了イメージ](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5. 承認して閉じます。

6. 次のページでは、埋め込みコード セクションに進みます。<br/>
![Marketo 埋め込みコード イメージ](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7. 下のサンプルのような埋め込みコードが表示されます。 

   ` <script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    `<form id="mktoForm_1179"></form> `

    `<script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>`

8. 埋め込みコードで使用できる値を使用して、クラウド パートナー ポータルの Marketo フィールドで<b>サーバー ID、Munchkin ID、およびフォーム ID</b> に入力します。 <br\> <br/>

  以下の例は、上記のサンプル データから ID を求める際に役立ちます。 <br\>

  serverId = **ys12** <br\>

 munchkinId = **123-PQR-789** <br\>

 formId = **1179** <br\> <br/>

