---
title: "Azure Active Directory における名前付きの場所 | Microsoft Docs"
description: "名前付きの場所とその構成方法について説明します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 126646d7460831f0235221595b8a93c88be6146d
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2017
---
# <a name="named-locations-in-azure-active-directory"></a>Azure Active Directory における名前付きの場所

名前付きの場所を使うと、組織内の信頼できる IP アドレス範囲にラベルを付けることができます。 Azure Active Directory は次の場合に名前付きの場所を使います。

- 報告される偽陽性の数を減らすための[リスク イベント](active-directory-reporting-risk-events.md)の検出。  

- [場所ベースの条件付きアクセス](active-directory-conditional-access-azure-portal.md#locations)。


この記事では、環境内で名前付きの場所を構成する方法について説明します。


## <a name="entry-points"></a>エントリ ポイント

名前付きの場所の構成ページにアクセスするには、Azure Active Directory ページの **[セキュリティ]** セクションで以下をクリックします。

![エントリ ポイント](./media/active-directory-named-locations/34.png)

- **条件付きアクセス:**

    - **[管理]** セクションで、**[名前付きの場所]** をクリックします。
    
        ![[名前付きの場所] コマンド](./media/active-directory-named-locations/06.png)

- **危険なサインイン:**

    - 上部のツール バーで、**[既知の IP アドレス範囲の追加]** をクリックします。

       ![[名前付きの場所] コマンド](./media/active-directory-named-locations/35.png)



## <a name="configuration-example"></a>構成の例

**名前付きの場所は、次のように構成します。**

1. [Azure ポータル](https://portal.azure.com)にグローバル管理者としてサインインします。

2. 左側のウィンドウで、**[Azure Active Directory]** をクリックします。

    ![左側のウィンドウの [Azure Active Directory] リンク](./media/active-directory-named-locations/01.png)

3. **[Azure Active Directory]** ページの **[セキュリティ]** セクションで、**[条件付きアクセス]** をクリックします。

    ![[条件付きアクセス] コマンド](./media/active-directory-named-locations/05.png)


4. **[条件付きアクセス]** ページの **[管理]** セクションで、**[名前付きの場所]** をクリックします。

    ![[名前付きの場所] コマンド](./media/active-directory-named-locations/06.png)


5. **[名前付きの場所]** ページで、**[新しい場所]** をクリックします。

    ![[新しい場所] コマンド](./media/active-directory-named-locations/07.png)


6. **[新規]** ページで、次の操作を行います。

    ![[新規] ブレード](./media/active-directory-named-locations/56.png)

    a. **[名前]** ボックスに、名前付きの場所の名前を入力します。

    b. **[IP 範囲]** ボックスに、IP 範囲を入力します。 IP 範囲は *Classless Inter-Domain Routing* (CIDR) 形式である必要があります。  

    c. **Create** をクリックしてください。



## <a name="what-you-should-know"></a>知っておくべきこと

**一括更新**: 一括更新で名前付きの場所を作成または更新する場合は、IP 範囲を含む CSV ファイルをアップロードまたはダウンロードできます。 アップロードすると、ファイル内の IP 範囲が一覧に追加されます。一覧の内容は上書きされません。

![[アップロード] リンクと [ダウンロード] リンク](./media/active-directory-named-locations/09.png)


**制限事項**: 最大 60 の名前付き場所を定義でき、それぞれに 1 つの IP 範囲を割り当てることができます。 構成する名前付き場所が 1 つだけの場合、そこには最大 500 の IP 範囲を定義できます。


## <a name="next-steps"></a>次のステップ

関連情報:

- **リスク イベント**については、「[Azure Active Directory リスク イベント](active-directory-reporting-risk-events.md)」をご覧ください。

- **条件付きアクセス**については、「[Azure Active Directory の条件付きアクセス](active-directory-conditional-access-azure-portal.md)」をご覧ください。

- **リスクの高いサインイン レポート**については、「[Azure Active Directory ポータルのリスクの高いサインイン レポート](active-directory-reporting-security-risky-sign-ins.md)」をご覧ください。  
