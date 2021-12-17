---
title: Azure AD Connect 同期 V2 エンドポイント | Microsoft Docs
description: このドキュメントでは、Azure AD Connect 同期 V2 エンドポイント API の更新について説明します。
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8560ea04e57553f556122cfa1986cabb03442a4e
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399031"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api"></a>Azure AD Connect 同期 V2 エンドポイント API 
Microsoft がデプロイした Azure AD Connect 用の新しいエンドポイント (API) では、Azure Active Directory に対する同期サービス操作のパフォーマンスが向上しています。 新しい V2 エンドポイントを使用すると、Azure AD に対するエクスポートとインポートのパフォーマンスが明らかに向上していることがわかります。 この新しいエンドポイントでは、以下をサポートします。
    
 - メンバー数が最大 250,000 までのグループの同期。
 - Azure AD に対するエクスポートとインポートのパフォーマンスの向上。
 
> [!NOTE]
> 現在、新しいエンドポイントでは、書き戻される Microsoft 365 グループに対するグループ サイズの制限は構成されていません。 これにより、Active Directory と同期サイクルの待機時間に影響がある可能性があります。 グループ サイズは段階的に増やすことをお勧めします。  

>[!NOTE]
> Azure AD Connect 同期 V2 エンドポイント API は一般公開されていますが、現在、これらの Azure 環境でのみ使用できます。
> - Azure Commercial
> - Azure China Cloud
> - Azure US Government Cloud。これは、Azure German Cloud では利用できません

## <a name="prerequisites"></a>前提条件  
新しい V2 エンドポイントを使用するには、Azure AD Connect v2.0 を使用する必要があります。 AADConnect V2.0 をデプロイすると、V2 エンドポイントが自動的に有効になります。
最新の 1.6 ビルドにアップグレードすると、グループ メンバーシップの上限が 50k にリセットされるという既知の問題があります。 サーバーを AADConnect 1.6 ビルドにアップグレードすると、サーバーの同期を有効にする前にグループ メンバーシップの上限を 250,000 に増やしたときに適用したルールの変更を、お客様が再適用する必要があります。 

## <a name="frequently-asked-questions"></a>よく寄せられる質問  
 
**新しいエンドポイントがアップグレードと新規インストールの既定値になるのはいつですか?**   V2 エンドポイントは、AADConnect V2.0 では既定の設定です。このエンドポイントの利点が活用されるよう、お客様には、AADConnect V2.0 へのアップグレードをお勧めします。
古いバージョンと共に V2 エンドポイントを実行している場合、より新しい V1.6 リリースにアップグレードしようとすると、グループ メンバーシップの 50,000 件制限が復活するという問題があります。 サーバーを AADConnect 1.6 ビルドにアップグレードすると、サーバーの同期を有効にする前にグループ メンバーシップの上限を 250,000 に増やしたときに適用したルールの変更を、お客様が再適用する必要があります。 

## <a name="next-steps"></a>次のステップ

* [Azure AD Connect 同期:同期を理解してカスタマイズする](how-to-connect-sync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)
