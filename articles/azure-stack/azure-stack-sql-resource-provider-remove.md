---
title: Azure Stack 上の SQL リソース プロバイダーを削除する | Microsoft Docs
description: Azure Stack のデプロイから SQL リソース プロバイダーを削除する方法について説明します。
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 9f90201cad0f74923460c2f25eff4de98dc6690a
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35294782"
---
# <a name="removing-the-mysql-resource-provider"></a>MySQL リソース プロバイダーの削除  
SQL リソース プロバイダーを削除する前に、最初にすべての依存関係を削除する必要があります。

## <a name="remove-the-mysql-resource-provider"></a>MySQL リソースプロバイダーを削除する 

1. 既存の SQL リソース プロバイダーの依存関係が削除されていることを確認します。

  > [!NOTE]
  > 依存リソースがリソース プロバイダーを現在使用している場合でも、SQL リソース プロバイダーのアンインストールは続行されます。 
  
2. このバージョンの SQL リソースプロバイダー アダプターに対してダウンロードした元のデプロイ パッケージがあることを確認します。
3. 次のパラメーターを使用して、デプロイ スクリプトを再実行します。
    - -Uninstall パラメーターを使用する
    - 特権エンドポイントの IP アドレスまたは DNS 名。
    - 特権エンドポイントへのアクセスに必要な、クラウド管理者の資格情報。
    - Azure Stack サービス管理者アカウントの資格情報。 Azure Stack のデプロイに使用したのと同じ資格情報を使用します。

## <a name="next-steps"></a>次の手順
[App Services を PaaS として提供する](azure-stack-app-service-overview.md)
