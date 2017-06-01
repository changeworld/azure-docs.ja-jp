---
title: "Azure Active Directory における名前付きの場所 | Microsoft Docs"
description: "名前付きの場所を構成することによって、組織が所有している IP アドレスが、特殊な場所へのあり得ない移動というリスク イベントの種類について誤検出を行うことを回避できます。"
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
ms.date: 05/10/2017
ms.author: markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 9637c56cf88ccf4c54fda789d4e7e3ca3e0a4fed
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="named-locations-in-azure-active-directory"></a>Azure Active Directory における名前付きの場所

名前付きの場所は Azure Active Directory の機能で、組織内の信頼された IP アドレス範囲にラベル付けできます。 お使いの環境で、[リスク イベント](active-directory-reporting-risk-events.md)の検知において名前付きの場所を使用して、*特殊な場所へのあり得ない移動*というリスク イベントの種類について報告される誤検出の数を減らすことができます。 




## <a name="configuration"></a>構成

**名前付きの場所は、次のように構成します。**

1. [Azure Portal](https://portal.azure.com) にグローバル管理者としてサインインします。

2. 左側のウィンドウで、**[Azure Active Directory]** をクリックします。

    ![名前付きの場所](./media/active-directory-named-locations/01.png)

3. **[Azure Active Directory]** ブレードの **[セキュリティ]** セクションで、**[条件付きアクセス]** をクリックします。

    ![名前付きの場所](./media/active-directory-named-locations/05.png)


4. **[条件付きアクセス]** ブレードの **[管理]** セクションで、**[Named locations] (名前付きの場所)** をクリックします。

    ![名前付きの場所](./media/active-directory-named-locations/06.png)


5. **[名前付きの場所]** ブレードの上部のメニューで、**[新しい場所]** をクリックします。

    ![名前付きの場所](./media/active-directory-named-locations/07.png)


6. **[新規]** ブレードで、次の手順を実行します。

    ![名前付きの場所](./media/active-directory-named-locations/08.png)

    a. **[名前]** テキスト ボックスに、名前付きの場所の名前を入力します。

    b. **[IP 範囲]** ボックスに、IP 範囲を入力します。 IP 範囲は *Classless Inter-Domain Routing* (CIDR) 形式である必要があります。  

    c. **[作成]**をクリックします。



## <a name="what-you-should-know"></a>知っておくべきこと

**一括更新** - 一括更新で名前付きの場所を作成または更新する場合は、IP 範囲を含む CSV ファイルをアップロードまたはダウンロードします。 アップロードすると、ファイル内の IP 範囲が一覧に追加されます。一覧の内容は上書きされません。

![名前付きの場所](./media/active-directory-named-locations/09.png)


**制限事項** - 名前付き場所を定義する場合、1 つの IP 範囲は最大 60 の名前付き場所に割り当てることができます。 構成する名前付き場所が 1 つだけの場合、そこには最大 500 の IP 範囲を定義できます。


## <a name="next-steps"></a>次のステップ

関連情報:

- リスク イベントの詳細については、「[Azure Active Directory リスク イベント](active-directory-reporting-risk-events.md)」をご覧ください。


