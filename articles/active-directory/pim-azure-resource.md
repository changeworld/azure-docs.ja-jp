---
title: "Privileged Identity Management (PIM) で Azure リソースへのアクセスを管理する"
description: "PIM のロール ベース アクセス管理を使用して、Azure リソースにアクセスする方法について説明します。"
services: active-directory
documentationcenter: 
author: skwan
manager: mtillman
editor: bryanla
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/22/2017
ms.author: skwan
ms.openlocfilehash: 1f31d8b76351ac8871f8a5b03d513f7b6704c709
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="manage-access-to-azure-resources-with-privileged-identity-management-preview"></a>Privileged Identity Management で Azure リソースへのアクセスを管理する (プレビュー)

悪意のあるサイバー攻撃から特権アカウントを保護するために、Azure Active Directory Privileged Identity Management (PIM) を使用して特権の露出時間を短縮し、レポートとアラートによって特権使用の可視性を向上できます。 PIM は、ユーザーが "Just-In-Time" (JIT) 特権のみを持つように制限するか、権限が自動的に失効する短期間の特権を割り当てることで、これを実現します。 

Azure ロール ベースのアクセス制御 (RBAC) で PIM を使用して、Azure のリソースへのアクセスを管理、制御、監視できるようになりました。 PIM では組み込みのロールとカスタム ロールのメンバーシップを管理できるため、以下の点で役立ちます。 

- Azure リソースへのオンデマンドの "Just-In-Time" アクセスを可能にする
- 割り当てられたユーザーとグループのリソースへのアクセス期限を自動的に終了させる
- 急ぎのタスクまたは緊急対応が必要なスケジュールのために Azure リソースに一時的なアクセス権を割り当てる
- 新しいユーザーまたはグループにリソースへのアクセスを割り当てたときや、それらのユーザーやグループがふさわしい割り当てをアクティブにするときにアラートを受け取る

詳細については、[Azure PIM におけるロールベースのアクセス制御の概要](privileged-identity-management/azure-pim-resource-rbac.md)に関する記事をご覧ください。