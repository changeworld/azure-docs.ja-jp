---
title: Privileged Identity Management (PIM) で Azure リソースへのアクセスを管理する
description: Privileged Identity Management (PIM) とロールベースのアクセス制御 (RBAC) を使用して Azure リソースへのアクセスを管理する方法について説明します。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 141cba29f5027ce092775d97c1abe9ecf11badf5
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436047"
---
# <a name="manage-access-to-azure-resources-with-privileged-identity-management"></a>Privileged Identity Management で Azure リソースへのアクセスを管理する

悪意のあるサイバー攻撃から特権アカウントを保護するために、Azure Active Directory Privileged Identity Management (PIM) を使用して特権の露出時間を短縮し、レポートとアラートによって特権使用の可視性を向上できます。 PIM は、ユーザーが "Just-In-Time" (JIT) 特権のみを持つように制限するか、権限が自動的に失効する短期間の特権を割り当てることで、これを実現します。 

Azure ロール ベースのアクセス制御 (RBAC) で PIM を使用して、Azure のリソースへのアクセスを管理、制御、監視できるようになりました。 PIM では組み込みのロールとカスタム ロールのメンバーシップを管理できるため、以下の点で役立ちます。 

- Azure リソースへのオンデマンドの "Just-In-Time" アクセスを可能にする
- 割り当てられたユーザーとグループのリソースへのアクセス期限を自動的に終了させる
- 急ぎのタスクまたは緊急対応が必要なスケジュールのために Azure リソースに一時的なアクセス権を割り当てる
- 新しいユーザーまたはグループがリソース アクセスの割り当てを受けたときや、そのようなユーザーまたはグループが資格のある割り当てをアクティブ化するときにアラートを取得する

詳細については、[Azure PIM におけるロールベースのアクセス制御の概要](../active-directory/privileged-identity-management/azure-pim-resource-rbac.md)に関する記事を参照してください。