---
title: Azure AD の条件付きアクセス ギャップ アナライザー ブック | Microsoft Docs
description: 条件付きアクセス ギャップ アナライザー ブックを使用する方法について説明します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenho
editor: ''
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/05/2021
ms.author: markvi
ms.reviewer: sarbar
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd62dbd1fc279e2ed26472b1c0a0464b56be92c0
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990531"
---
# <a name="conditional-access-gap-analyzer-workbook"></a>条件付きアクセス ギャップ アナライザー ブック

Azure AD では、条件付きアクセス ポリシーを構成することでリソースへのアクセスを保護できます。
IT 管理者は、リソースが適切に保護されているようにするため、条件付きアクセス ポリシーが予期したとおりに機能することを確認する必要があります。 条件付きアクセス ギャップ アナライザー ブックを使用すると、条件付きアクセスの実装におけるギャップを検出できます。  

この記事では、このブックの概要を紹介します。


## <a name="description"></a>説明

![ブックのカテゴリ](./media/workbook-conditional-access-gap-analyzer/workbook-category.png)

IT 管理者は、適切なユーザーだけがリソースにアクセスできるようにする必要があります。 Azure AD の条件付きアクセスは、この目標を達成するのに役立ちます。  

条件付きアクセス ギャップ アナライザー ブックは、条件付きアクセス ポリシーが予期したとおりに機能することを検証する助けとなります。

**このブックでは:**

- 条件付きアクセス ポリシーが適用されていないユーザー サインインが強調表示されます。 
- 条件付きアクセス ポリシーから意図せず除外されたユーザー、アプリケーション、または場所が存在しないことを確認できます。  

 

## <a name="sections"></a>セクション


ブックには 4 つのセクションがあります。  

- レガシ認証を使用してサインインしているユーザー 

- 条件付きアクセス ポリシーの影響を受けていないアプリケーションごとのサインインの回数 

- 条件付きアクセス ポリシーをバイパスしている高リスクのサインイン イベント 

- 条件付きアクセス ポリシーの影響を受けなかった場所ごとのサインインの回数 


![場所ごとの条件付きアクセスの適用範囲](./media/workbook-conditional-access-gap-analyzer/conditianal-access-by-location.png)

これらの各傾向により、サインインの内訳がユーザー レベルまで明らかになるため、シナリオごとに、どのユーザーが条件付きアクセスをバイパスしているかを確認できます。 

## <a name="filters"></a>フィルタ

このブックでは、時間範囲フィルターの設定がサポートされています。

![時間範囲フィルター](./media/workbook-conditional-access-gap-analyzer/time-range.png)



## <a name="best-practices"></a>ベスト プラクティス

このブックを使用して、テナントが以下の条件付きアクセスのベスト プラクティスのために構成されていることを確認します。  

- レガシ認証のサインインをすべてブロックする 

- すべてのアプリケーションに、少なくとも 1 つの条件付きアクセス ポリシーを適用する 

- 高リスクのサインインをすべてブロックする  

- 信頼されていない場所からのサインインをブロックする  

 





## <a name="next-steps"></a>次の手順

- [Azure AD ブックの使用方法](howto-use-azure-monitor-workbooks.md)
