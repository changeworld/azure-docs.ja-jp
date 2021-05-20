---
author: baanders
description: Azure Digital Twins 設定でのアクセス許可の手順のためのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: e49abce74f5f4ab3a624def8ec1eb1d674711462
ms.sourcegitcommit: dd425ae91675b7db264288f899cff6add31e9f69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/01/2021
ms.locfileid: "108759967"
---
Azure Digital Twins は、ロールベースのアクセス制御 (RBAC) に [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) を使用します。 つまり、ユーザーが Azure Digital Twins インスタンスへのデータ プレーン呼び出しを行うには、そのための適切なアクセス許可を持つロールをそのユーザーに割り当てておく必要があります。

Azure Digital Twins の場合、このロールは **"Azure Digital Twins Data Owner" (Azure Digital Twins データ所有者)** です。 ロールとセキュリティの詳細については、[概念: Azure Digital Twins ソリューションのセキュリティ](../articles/digital-twins/concepts-security.md)に関するページを参照してください。

> [!NOTE]
> このロールは、Azure Digital Twins インスタンスのスコープでも割り当てることができる Azure AD **[所有者]** ロールとは異なります。 これらは 2 つの個別の管理ロールであり、**Azure Digital Twins データ所有者** で許可されるデータ プレーンへのアクセス機能は所有者に付与されません。

このセクションでは、Azure サブスクリプションの Azure AD テナント内のユーザーの電子メールを使用して、Azure Digital Twins インスタンスでのそのユーザーのロールの割り当てを作成する方法について説明します。 組織内のロールによっては、このアクセス許可を自分で設定するか、または Azure Digital Twins インスタンスを管理するだれか他のユーザーの代わりに設定する可能性があります。