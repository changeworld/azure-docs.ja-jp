---
title: ワークロード ID 用の Azure Active Directory の条件付きアクセス (プレビュー)
description: 条件付きアクセス ポリシーを使用したワークロード ID の保護
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57af80a8baea02532d8711a57963668bf63ece81
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725473"
---
# <a name="conditional-access-for-workload-identities-preview"></a>ワークロード ID 用の条件付きアクセス (プレビュー)

以前は、条件付きアクセス ポリシーは、ユーザーが SharePoint Online や Azure portal などのアプリやサービスにアクセスするときにのみ、適用されていました。 このプレビューでは、組織が所有するサービス プリンシパルに適用される条件付きアクセス ポリシーのサポートが追加されます。 この機能を、ワークロード ID 用の条件付きアクセスと呼びます。 

ワークロード ID とは、アプリケーションまたはサービス プリンシパルが (場合によってはユーザーのコンテキストにある) リソースにアクセスすることを可能にする ID です。 これらのワークロード ID は、従来のユーザー アカウントとは次のように異なります。

- 通常、正式なライフサイクル プロセスがありません。
- 資格情報またはシークレットをどこかに保存する必要があります。
- アプリケーションで複数の ID を使用することができます。 
 
これらの相違点により、ワークロード ID の管理が困難になり、リークのリスクが高まり、アクセスをセキュリティで保護できる可能性が低くなります。

> [!NOTE]
> ポリシーは、お使いのテナントに登録されているシングル テナント サービス プリンシパルに適用できます。 サード パーティの SaaS およびマルチテナント アプリは、対象外です。 マネージド ID は、ポリシーの対象にはなりません。 

このプレビューでは、企業ネットワークのパブリック IP 範囲など、信頼された IP 範囲外からのサービス プリンシパルをブロックできます。 

## <a name="implementation"></a>実装

### <a name="step-1-set-up-a-sample-application"></a>手順 1: サンプル アプリケーションを設定する

サービス プリンシパルを利用するテスト アプリケーションが既にある場合は、この手順を省略できます。

サンプル アプリケーションを設定します。これにより、ユーザーの ID ではなく、アプリケーション ID を使用してジョブまたは Windows サービスを実行する方法を示します。 このアプリケーションを作成するには、記事「[クイックスタート: コンソール アプリの ID を使用してトークンを取得し、Microsoft Graph API を呼び出す](../develop/quickstart-v2-netcore-daemon.md)」に記載されている手順に従ってください。

### <a name="step-2-create-a-conditional-access-policy"></a>手順 2: 条件付きアクセス ポリシーを作成する

サービス プリンシパルに適用される、場所ベースの条件付きアクセス ポリシーを作成します。

1. **Azure portal** にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** の順に移動します。
1. **[新しいポリシー]** を選択します。
1. ポリシーに名前を付けます。 ポリシーの名前に対する意味のある標準を組織で作成することをお勧めします。
1. **[割り当て]** で、 **[ユーザーまたはワークロード ID]** を選択します。
   1. **[このポリシーは何に適用されますか?]** で、 **[ワークロード ID (プレビュー)]** を選択します。
   1. **[含める]** で、 **[サービス プリンシパルの選択]** を選択し、一覧から適切なサービス プリンシパルを選択します。
1. **[クラウド アプリまたはアクション]** で、 **[すべてのクラウド アプリ]** を選択します。 ポリシーは、サービス プリンシパルがトークンを要求した場合にのみ適用されます。
1. **[条件]**  >  **[場所]** で、 **[すべての場所]** を含め、アクセスを許可する **[選択された場所]** を除外します。
1. **[許可]** で使用可能な唯一のオプションは **[アクセスをブロックする]** です。 トークン要求が許容範囲外から行われた場合、アクセスはブロックされます。
1. ポリシーを **レポート専用** モードで保存し、管理者が影響を見積もることができるようにするか、ポリシーを **有効** にしてポリシーを適用することができます。
1. **[作成]** を選択してポリシーを完成させます。

## <a name="roll-back"></a>ロールバック

この機能をロールバックする場合は、作成したポリシーを削除または無効にすることができます。

## <a name="sign-in-logs"></a>サインイン ログ

サインイン ログを使用して、サービス プリンシパルに対してポリシーがどのように適用されているか、またはレポート専用モードを使用している場合には予想されるポリシーの影響を確認できます。

1. **Azure Active Directory** >  **[サインイン ログ]**  >  **[サービス プリンシパル サインイン]** に移動します。
1. ログ エントリを選択し、 **[条件付きアクセス]** タブを選択して評価情報を表示します。

サービス プリンシパルが条件付きアクセスによってブロックされた場合のエラーの理由: "条件付きアクセス ポリシーのため、アクセスがブロックされました。" 

## <a name="reference"></a>リファレンス

### <a name="finding-the-objectid"></a>ObjectID の検索

サービス プリンシパルの objectID は Azure AD エンタープライズ アプリケーションから取得できます。 Azure AD アプリの登録のオブジェクト ID は使用できません。 この識別子は、サービス プリンシパルではなく、アプリの登録のオブジェクト ID です。

1. **Azure portal** >  **[Azure Active Directory]**  >  **[エンタープライズ アプリケーション]** に移動し、登録したアプリケーションを検索します。
1. **[概要]** タブから、アプリケーションの **[オブジェクト ID]** をコピーします。 この識別子はサービス プリンシパルに固有のもので、呼び出し元のアプリを検索するために条件付きアクセス ポリシーによって使用されます。

### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph ベータ版 エンドポイントを使用した構成用のサンプル JSON。

```json
{
  "displayName": "Name",
  "state": "enabled OR disabled OR enabledForReportingButNotEnforced",
  "conditions": {
    "applications": {
      "includeApplications": [
        "All"
      ]
    },
    "clientApplications": {
      "includeServicePrincipals": [
        "[Service principal Object ID] OR ServicePrincipalsInMyTenant"
      ],
      "excludeServicePrincipals": [
        "[Service principal Object ID]"
      ],
    },
    "locations": {
      "includeLocations": [
        "All"
      ],
      "excludeLocations": [
        "[Named location ID] OR AllTrusted"
      ]
    }
  },
  "grantControls": {
    "operator": "and",
    "builtInControls": [
      "block"
    ]
  }
}
```

## <a name="next-steps"></a>次のステップ

- [条件付きアクセスポリシーでの場所の条件の使用](location-condition.md)
- [条件付きアクセス:プログラムによるアクセス](howto-conditional-access-apis.md)
- [条件付きアクセスのレポート専用モードとは](concept-conditional-access-report-only.md)
 