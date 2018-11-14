---
title: Azure Digital Twins のセキュリティのベスト プラクティスについて | Microsoft Docs
description: Azure Digital Twins のセキュリティのベストプラクティス
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: adgera
ms.openlocfilehash: 6ca01523744dbce15f8fdb3bbe2d5a9b44510f3f
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959479"
---
# <a name="security-best-practices"></a>セキュリティのベスト プラクティス

Azure Digital Twins のセキュリティにより、IoT グラフ内の特定のリソースとアクションへのアクセスを正確に制御できます。 これは、ロールベースのアクセス制御と呼ばれるきめ細かいロールとアクセス許可の管理によって行われます。

Azure Digital Twins では、Azure Active Directory (Azure AD) など、Azure IoT に存在する他のセキュリティ機能も使用されます。 そのため、Azure Digital Twins アプリの構成には、現在推奨されているものと同じ多くの [Azure IoT のセキュリティ プラクティス](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/)を使用することが含まれます。

この記事では、従うべき主要なベスト プラクティスの概要を示します。

> [!IMPORTANT]
> IoT スペースの最大限のセキュリティを確保するには、追加のセキュリティ リソースを確認してください。 デバイス ベンダーを必ず含めます。

## <a name="iot-security-best-practices"></a>IoT セキュリティのベスト プラクティス

IoT デバイスを安全にセキュリティで保護するためのいくつかの重要なプラクティスは、次のとおりです。

> [!div class="checklist"]
> * IoT スペースに接続されている各デバイスを、不正操作防止の方法で保護します。
> * IoT スペース内の各デバイス、センサー、人の役割を制限します。 侵害された場合、影響は最小限に抑えられます。
> * デバイスの IP アドレスのフィルター処理の潜在的な使用とポートの制約を考慮します。
> * パフォーマンスを向上させるために、I/O とデバイスの帯域幅を制限します。 レート制限により、サービス拒否攻撃を防ぐことでセキュリティを向上させることができます。
> * デバイスのファームウェアを最新に保ちます。

IoT スペースを安全にセキュリティで保護するためのいくつかの重要なプラクティスは、次のとおりです。

> [!div class="checklist"]
> * 保存済み、格納済み、または永続的なデータを暗号化します。
> * パスワードまたはキーを定期的に変更または更新する必要があります。
> * ロールによって、アクセスとアクセス許可を慎重に制限します。 この記事の「ロールベースのアクセス制御のベスト プラクティス」セクションをご覧ください。
> * 強力な暗号化を使用します。 長いパスワードを要求し、セキュリティで保護されたプロトコルと 2 要素認証を使用します。

IoT リソースを監視して、通常の操作の範囲外である外れ値、脅威、またはリソース パラメーターを見張ります。 監視の管理には Azure Analytics を使用します。

> [!NOTE]
> イベントの処理と監視について詳しくは、[Azure Digital Twins でのイベントとメッセージのルーティング](./concepts-events-routing.md)に関する記事をご覧ください。

## <a name="azure-active-directory-best-practices"></a>Azure Active Directory のベスト プラクティス

Azure Digital Twins は、Azure AD を使用してユーザーを認証し、アプリケーションを保護します。 Azure AD では、さまざまな最新アーキテクチャ用の認証がサポートされます。 すべては、OAuth 2.0 や OpenID Connect などの業界標準のプロトコルに基づいています。 Azure AD 用の IoT スペースをセキュリティで保護するためのいくつかの重要なプラクティスは、次のとおりです。

> [!div class="checklist"]
> * Azure AD アプリのシークレットとキーは、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) などのセキュリティで保護された場所に保存します。
> * 認証するアプリ シークレットではなく、信頼する[証明機関](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started)によって発行された証明書を使用します。
> * トークンに対するアクセスの OAuth 2.0 スコープを制限します。
> * トークンが有効である期間と、トークンが有効のままになっているかどうかを確認します。
> * トークンが有効である期間を適切な期間に設定します。
> * 期限切れのトークンを更新します。

## <a name="role-based-access-control-best-practices"></a>ロールベースのアクセス制御のベスト プラクティス

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>次の手順

Azure IoT のベスト プラクティスの詳細については、「[IoT セキュリティのベスト プラクティス](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/)」をご覧ください。

ロールベースのアクセス制御の詳細については、[ロールベースのアクセス制御](./security-role-based-access-control.md)に関するページをご覧ください。

認証については、[API を使用した認証](./security-authenticating-apis.md)に関するページをご覧ください。
