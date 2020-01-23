---
title: Android デバイスの共有デバイス モード | Azure
description: 現場の最前線で働く従業員が Android デバイスを共有できるようにする共有デバイス モードについて説明します。
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: twhitney
ms.reviwer: hahamil
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70f629af35978771d48d0a29f9ec625628c3b630
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2020
ms.locfileid: "76291262"
---
# <a name="shared-device-mode-for-android-devices"></a>Android デバイスの共有デバイス モード

> [!NOTE]
> この機能はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

現場の最前線で働く従業員 (小売店員、航空機乗組員、フィールド サービス ワーカーなど) は多くの場合、共有モバイル デバイスを使用して作業を行います。 これは、これらの従業員が共有デバイス上の顧客およびビジネス データにアクセスするためにパスワードまたは PIN 番号の共有を開始するときに問題になります。

共有デバイス モードを使用すると、Android デバイスを複数の従業員で容易に共有できるように構成できます。 従業員はサインインし、顧客情報にすばやくアクセスできます。 自分のシフトまたはタスクが完了したら、デバイスからサインアウトでき、そのデバイスは直ちに次の従業員が使用できるよう準備が整います。

共有デバイス モードではまた、Microsoft ID によってバックアップされたデバイスの管理も提供されます。

共有デバイス モード アプリを作成するには、開発者とクラウド デバイス管理者が協力して作業します。

- 開発者は、単一アカウント アプリ (共有デバイス モードでは複数アカウント アプリはサポートされていません) を記述し、アプリの構成に `"shared_device_mode_supported": true` を追加して、共有デバイスのサインアウトなどを処理するためのコードを記述します。
- デバイス管理者は、認証アプリをインストールし、その認証アプリを使用してデバイスを共有モードに設定することによって、共有されるデバイスを準備します。 [認証アプリ](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview)を使用してデバイスを共有モードにすることができるのは、[クラウド デバイス管理者](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#cloud-device-administrator)ロールのユーザーだけです。 組織ロールのメンバーシップは、Azure portal で次のコマンドを使用して構成できます。 **[Azure Active Directory]**  >  **[ロールと管理者]**  >  **[クラウド デバイス管理者]** 。

 この記事では主に、開発者が考慮すべき事項に重点を置いています。

## <a name="single-vs-multiple-account-applications"></a>単一アカウント アプリケーションと複数アカウント アプリケーションの選択

Microsoft Authentication Library SDK (MSAL) を使用して記述されたアプリケーションは、単一のアカウントまたは複数のアカウントを管理できます。 詳細については、[単一アカウント モードまたは複数アカウント モード](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account)に関するページを参照してください。 アプリで使用できる Microsoft ID プラットフォーム機能は、アプリケーションが単一アカウント モードまたは複数アカウント モードのどちらで実行されているかによって異なります。

**共有デバイス モード アプリは、単一アカウント モードでのみ動作します**。

> [!IMPORTANT]
> 複数アカウント モードのみをサポートするアプリケーションは、共有デバイス上で実行できません。 従業員が、単一アカウント モードをサポートしていないアプリを読み込んだ場合、そのアプリは共有デバイス上で実行されません。
>
> MSAL SDK がリリースされる前に記述されたアプリは複数アカウント モードで実行されるため、共有モード デバイス上で実行するには、単一アカウント モードをサポートするように更新しておく必要があります。

**単一アカウントと複数アカウントの両方のサポート**

アプリは、個人デバイスと共有デバイスの両方での実行をサポートするように構築できます。 現在、アプリが複数のアカウントをサポートしており、共有デバイス モードをサポートするようにしたい場合は、単一アカウント モードのサポートを追加してください。

アプリで実行されているデバイスの種類に応じて、アプリが動作を変更するようにもできます。 いつ単一アカウント モードで実行するかを決定するには、`ISingleAccountPublicClientApplication.isSharedDevice()` を使用します。

アプリケーションが実行しているデバイスの種類を表す 2 つの異なるインターフェイスが存在します。 MSAL のアプリケーション ファクトリにアプリケーション インスタンスを要求すると、適切なアプリケーション オブジェクトが自動的に提供されます。

次のオブジェクト モデルは、受信する可能性のあるオブジェクトの種類と、それが共有デバイスのコンテキストで何を示すかを示しています。

![パブリック クライアント アプリケーションの継承モデル](media/v2-shared-device-mode/ipublic-client-app-inheritance.png)

`PublicClientApplication` オブジェクトを取得したら、種類のチェックを行い、適切なインターフェイスにキャストする必要があります。 次のコードは、複数アカウント モードまたは単一アカウント モードをチェックし、アプリケーション オブジェクトを適切にキャストします。

```java
private IPublicClientApplication mApplication;

        // Running in personal-device mode?
        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        // Running in shared-device mode?
        } else if (mApplication instanceOf ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

アプリが共有デバイスまたは個人デバイスのどちらで実行されているかに応じて、次の違いが適用されます。

|  | 共有モード デバイス  | 個人デバイス |
|---------|---------|---------|
| **Accounts**     | 単一のアカウント | 複数のアカウント |
| **サインイン** | グローバル | グローバル |
| **サインアウト** | グローバル | 各アプリケーションは、サインアウトがアプリに対してローカルか、またはアプリケーションのファミリに対するものかを制御できます。 |
| **サポートされているアカウントの種類** | 職場アカウントのみ | 個人アカウントと職場アカウントをサポート  |

## <a name="why-you-may-want-to-only-support-single-account-mode"></a>単一アカウント モードのみをサポートする理由

共有デバイスを使用している現場の最前線で働く従業員にのみ使用されるアプリを記述している場合は、単一アカウント モードのみをサポートするようにアプリケーションを記述することをお勧めします。 これには、医療記録アプリ、請求書アプリ、大部分の基幹業務アプリなどの、タスクに重点を置いたほとんどのアプリケーションが含まれます。 単一アカウント モードのみをサポートすると、複数アカウント アプリに含まれる追加機能を実装する必要がないため、開発が簡素化されます。

## <a name="what-happens-when-the-device-mode-changes"></a>デバイス モードが変更されたときの動作

アプリケーションが複数アカウント モードで実行されているときに、管理者がデバイスを共有デバイス モードにすると、そのデバイス上のすべてのアカウントがアプリケーションから消去され、アプリケーションは単一アカウント モードに移行します。

## <a name="shared-device-sign-out-and-the-overall-app-lifecycle"></a>共有デバイスのサインアウトとアプリのライフサイクル全体

ユーザーがサインアウトしたら、そのユーザーのプライバシーとデータを保護するためのアクションを実行する必要があります。 たとえば、医療記録アプリを構築している場合は、ユーザーがサインアウトしたら、以前に表示された患者記録が消去されていることを確認する必要があります。 アプリケーションはこの処理を行う準備をし、フォアグラウンドに入るたびにチェックする必要があります。

アプリが、共有モードのデバイス上で実行されているアプリで MSAL を使用してユーザーをサインアウトすると、サインイン アカウントとキャッシュされているトークンがアプリとデバイスの両方から削除されます。

次の図は、アプリのライフサイクル全体と、アプリの実行中に発生する可能性のある共通イベントを示しています。 この図には、アクティビティの開始時刻から、アカウントのサインインとサインアウト、およびアクティビティの一時停止、再開、停止などのイベントがどのように整合しているかまで含まれています。

![共有デバイス アプリのライフサイクル](media/v2-shared-device-mode/lifecycle.png)

## <a name="next-steps"></a>次のステップ

現場の最前線で働く従業員用のアプリを共有モードの Android デバイスで実行する方法を示す [Android アプリケーションでの共有デバイス モードの使用](tutorial-v2-shared-device-mode.md)に関するチュートリアルを試してみてください。
