---
title: Azure での Datadog の前提条件 - Azure パートナー ソリューション
description: この記事では、Azure 環境を構成して Datadog のインスタンスを作成する方法について説明します。
ms.service: partner-services
ms.topic: conceptual
ms.date: 05/28/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 637d3cfb3baf8a53cb62943419077fad0f70d33d
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110657091"
---
# <a name="configure-environment-before-datadog-deployment"></a>Datadog を展開する前に環境を構成する

この記事では、Datadog の最初のインスタンスを展開する前に環境を設定する方法について説明します。 これらの条件は、クイックスタートを完了するための前提条件です。

## <a name="access-control"></a>アクセス制御

Azure Datadog 統合を設定するには、Azure サブスクリプションに対する **所有者** アクセス権が必要です。 設定を始める前に、[適切なアクセス権があることを確認してください](../../role-based-access-control/check-access.md)。

## <a name="add-enterprise-application"></a>エンタープライズ アプリケーションの追加
 
Datadog リソース内で Security Assertion Markup Language (SAML) シングル サインオン (SSO) 機能を使用するには、エンタープライズ アプリケーションを設定する必要があります。 エンタープライズ アプリケーションを追加するには、グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、サービス プリンシパル所有者のいずれかのロールが必要です。

エンタープライズ アプリケーションを設定するには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)に移動します。 **[Azure Active Directory]** を選択します。
1. 左側のウィンドウで、**[エンタープライズ アプリケーション]** を選択します。
1. **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** で、「*Datadog*」を検索します。 検索結果を選択し、 **[追加]** を選択します。

   :::image type="content" source="media/prerequisites/datadog-azure-ad-app-gallery.png" alt-text="AAD エンタープライズ ギャラリー内の Datadog アプリケーション。" border="true":::

1. アプリが作成されたら、サイド パネルからプロパティに移動します。 **[ユーザーの割り当てが必要ですか?]** を **[いいえ]** に設定し、 **[保存]** を選択します。

   :::image type="content" source="media/prerequisites/user-assignment-required.png" alt-text="Datadog アプリケーションのプロパティを設定する" border="true":::

1. サイド パネルから **[シングル サインオン]** に移動します。 **[SAML]** を選択します。

   :::image type="content" source="media/prerequisites/saml-sso.png" alt-text="SAML 認証。" border="true":::

1. **シングル サインオン設定を保存** するかどうかを確認するメッセージが表示されたら、 **[はい]** を選択します。

   :::image type="content" source="media/prerequisites/save-sso.png" alt-text="Datadog アプリのシングル サインオンを保存する" border="true":::

1. これで、シングル サインオンの設定が完了しました。

## <a name="next-steps"></a>次のステップ

Datadog のインスタンスを作成するには、「[クイックスタート: Datadog の使用を開始する](create.md)」を参照してください。
