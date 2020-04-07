---
title: Marketo でリード管理を構成する | Azure Marketplace
description: Azure Marketplace のお客様向けに Marketo でリード管理を構成します。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 9fa05eae2d297cbd6ae7243d191cae5a7a3f990e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288531"
---
# <a name="configure-lead-management-in-marketo"></a>Marketo でリード管理を構成する

この記事では、Microsoft の販売リードを処理するように、Marketo を設定する方法について説明します。

1. Marketo にサインインします。
2. **[Design Studio]** を選択します。
    ![Marketo の Design Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  **[New Form]\(新しいフォーム\)** を選択します。
    ![Marketo の [New Form]\(新しいフォーム\)](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  [New Form]\(新しいフォーム\) の必須フィールドを入力して、 **[Create]\(作成\)** を選択します。
    ![Marketo の新しいフォームの作成](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  [Field Details]\(フィールドの詳細\) で、 **[Finish]\(完了\)** を選択します。
    ![Marketo の完了フォーム](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  承認して閉じます。

6.  [MarketplaceLeadBacked] タブで、 **[Embed Code]\(埋め込みコード\)** を選択します。
    ![Marketo の [Embed Code]\(埋め込みコード\) オプション](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Marketo の [Embed Code]\(埋め込みコード\) では、次の例のようなコードが表示されます。

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. [Embed Code]\(埋め込みコード\) に示された値をコピーすると、Cloud パートナー ポータルの Marketo フィールドで **Server Id**、**Munchkin Id**、および **Form Id** を構成できます。

Marketo の [Embed Code]\(埋め込みコード\) の例から必要な ID を取得するには、次の例をガイドとして使用します。

- Server Id = **ys12**
- Munchkin Id = **123-PQR-789**
- Form Id = **1179**
