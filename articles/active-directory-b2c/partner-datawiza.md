---
title: Azure Active Directory B2C と Datawiza を構成するためのチュートリアル
titleSuffix: Azure AD B2C
description: セキュリティ保護されたハイブリッド アクセスのために Azure AD B2C 認証を Datawiza と統合する方法について説明します
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 7/07/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c33694c624deafa8cdc7ef37ac1dc39d98b5a4f9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044743"
---
# <a name="tutorial-configure-azure-ad-b2c-with-datawiza-to-provide-secure-hybrid-access"></a>チュートリアル: セキュリティで保護されたハイブリッド アクセスを提供するために、Azure AD B2C と Datawiza を構成する

このサンプル チュートリアルでは、Azure Active Directory (AD) B2C 認証を [Datawiza](https://www.datawiza.com/) と統合する方法について説明します。
Datawiza の [Datawiza Access Broker (DAB)](https://www.datawiza.com/access-broker) により、オンプレミスのレガシ アプリケーションを保護するために、Azure AD B2C を拡張する、シングル サインオン (SSO) ときめ細かいアクセス制御が可能になります。 このソリューションを使用すると、企業はアプリケーションを書き直すことなく、レガシから Azure AD B2C に迅速に移行できます。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](./tutorial-create-tenant.md)。

- DAB を実行するには、[Docker](https://docs.docker.com/get-docker/) が必要です。 アプリケーションは、仮想マシンやベアメタルなど、任意のプラットフォームで実行できます。

- レガシ ID システムから Azure AD B2C に移行するオンプレミスのアプリケーション。 このサンプルでは、DAB はアプリケーションがある同じサーバーにデプロイされます。 アプリケーションは localhost: 3001 で実行され、DAB では localhost: 9772 を介してアプリケーションへのトラフィックがプロキシされます。 アプリケーションへのトラフィックは最初に DAB に到達し、次にアプリケーションにプロキシされます。

## <a name="scenario-description"></a>シナリオの説明

Datawiza 統合には、次のコンポーネントが含まれています。

- **Azure AD B2C**:ユーザーの資格情報の検証を担当する承認サーバー。 認証済みユーザーは、Azure AD B2C ディレクトリに格納されているローカル アカウントを使用して、オンプレミスのアプリケーションにアクセスできます。

- **Datawiza Access Broker (DAB)** : サービス ユーザーがサインオンし、HTTP ヘッダーを介してアプリケーションに透過的に ID を渡します。

- **Datawiza Cloud Management Console (DCMC)** - DAB を管理する集中管理コンソール。 DCMC には、管理者が DAB の構成とそのアクセス制御ポリシーを管理するための UI および RESTful API が用意されています。

次のアーキテクチャの図に、この実装を示します。

![イメージは、ハイブリッド アプリケーションへのセキュリティ保護されたアクセスのための Azure AD B2C と Datawiza の統合のアーキテクチャを示しています](./media/partner-datawiza/datawiza-architecture-diagram.png)

| 手順 | 説明 |
|:-------|:---------------|
| 1. | ユーザーが、オンプレミスでホストされているアプリケーションへのアクセスを要求します。 DAB では、ユーザーによってアプリケーションに対して行われた要求がプロキシされます。|
| 2. | DAB によって、ユーザーの認証状態が確認されます。 セッション トークンを受け取っていない場合、または渡されたセッション トークンが無効な場合は、認証のためにユーザーが Azure AD B2C に送信されます。|
| 3. | Azure AD B2C によって、Azure AD B2C テナントでの DAB アプリケーションの登録時に指定されたエンドポイントにユーザー要求が送信されます。|
| 4. | DAB によって、アクセス ポリシーが評価され、アプリケーションに転送される HTTP ヘッダーに含まれる属性値が計算されます。 この手順では、DAB によって、IdP が呼び出され、ヘッダー値を正しく設定するために必要な情報が取得されることがあります。 DAB によって、ヘッダー値が設定され、要求がアプリケーションに送信されます。 |
|5.  | これで、ユーザーが認証され、アプリケーションにアクセスできるようになりました。|

## <a name="onboard-with-datawiza"></a>Datawiza を使用してオンボードする

従来のオンプレミスのアプリを Azure AD B2C と統合するには、[Datawiza](https://login.datawiza.com/df3f213b-68db-4966-bee4-c826eea4a310/b2c_1a_linkage/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile&client_id=4f011d0f-44d4-4c42-ad4c-88c7bbcd1ac8&redirect_uri=https%3A%2F%2Fconsole.datawiza.com%2Fhome&state=eyJpZCI6Ijk3ZjI5Y2VhLWQ3YzUtNGM5YS1hOWU2LTg1MDNjMmUzYWVlZCIsInRzIjoxNjIxMjg5ODc4LCJtZXRob2QiOiJyZWRpcmVjdEludGVyYWN0aW9uIn0%3D&nonce=08e1b701-6e42-427b-894b-c5d655a9a6b0&client_info=1&x-client-SKU=MSAL.JS&x-client-Ver=1.3.3&client-request-id=3ac285ba-2d4d-4ae5-8dc2-9295ff6047c6&response_mode=fragment) にお問い合わせください。

## <a name="configure-your-azure-ad-b2c-tenant"></a>Azure AD B2C テナントを構成する

1. Web アプリケーションを Azure AD B2C テナントに[登録](https://docs.datawiza.com/idp/azureb2c.html#microsoft-azure-ad-b2c-configuration)します。

2. Azure 管理ポータルで[サインアップとサインインのユーザー フローを構成](https://docs.datawiza.com/idp/azureb2c.html#configure-a-user-flow)します。

  >[!NOTE]
  >後で DCMC で DAB を設定するときに、テナント名、ユーザー フロー名、クライアント ID、クライアント シークレットが必要になります。

## <a name="create-an-application-on-dcmc"></a>DCMC でアプリケーションを作成する

1. [アプリケーションを作成し](https://docs.datawiza.com/step-by-step/step2.html)、DCMC でこのアプリケーションの `PROVISIONING_KEY` と `PROVISIONING_SECRET` のキー ペアを生成します。

2. ID (IdP) プロバイダーとして [Azure AD B2C を構成する](https://docs.datawiza.com/tutorial/web-app-azure-b2c.html#part-i-azure-ad-b2c-configuration)

![イメージに Idp を構成するための値が示されています](./media/partner-datawiza/configure-idp.png)

## <a name="run-dab-with-a-header-based-application"></a>ヘッダーベースのアプリケーションで DAB を実行する

1. Docker または Kubernetes のいずれかを使用して DAB を実行できます。 ユーザーがサンプル ヘッダーベースのアプリケーションを作成するには、Docker イメージが必要です。 詳細については [DAB と SSO の統合を構成する](https://docs.datawiza.com/step-by-step/step3.html)方法、および Kubernetes 固有の手順については、[Kubernetes を使用して DAB をデプロイする](https://docs.datawiza.com/tutorial/web-app-AKS.html)方法の手順を参照してください。 ダウンロードして使用するために、サンプル Docker イメージ `docker-compose.yml file` が用意されています。 コンテナー レジストリにログインして、DAB とヘッダーベースのアプリケーションのイメージをダウンロードします。 [こちらの手順](https://docs.datawiza.com/step-by-step/step3.html#important-step)に従ってください。
 
   ```yaml
   version: '3'

    services:
    datawiza-access-broker:
    image: registry.gitlab.com/datawiza/access-broker
    container_name: datawiza-access-broker
    restart: always
    ports:
      - "9772:9772"
    environment:
      PROVISIONING_KEY: #############################
      PROVISIONING_SECRET: #############################

    header-based-app:
    image: registry.gitlab.com/datawiza/header-based-app
    container_name: ab-demo-header-app
    restart: always
    environment:
      CONNECTOR: B2C
    ports:
      - "3001:3001"
    ```

 2. `docker-compose -f docker-compose.yml up` を実行した後に、ヘッダーベースのアプリケーションで Azure AD B2C を使用して SSO が有効にされている必要があります。 ブラウザーを開いて､「`http://localhost:9772/`」と入力します。

3. Azure AD B2C ログイン ページが表示されます。

## <a name="pass-user-attributes-to-the-header-based-application"></a>ヘッダーベースのアプリケーションにユーザー属性を渡す

1. DAB では IdP からユーザー属性を取得し、ヘッダーまたは Cookie を介してユーザー属性をアプリケーションに渡すことができます。 ヘッダーベースのアプリケーションにメール アドレス、名、姓などの[ユーザー属性を渡す](https://docs.datawiza.com/step-by-step/step4.html)方法の手順を参照してください。 

2. ユーザー属性が正常に構成されると、各ユーザー属性に緑色のチェック記号が表示されるはずです。

 ![イメージに、渡されたユーザー属性が示されています](./media/partner-datawiza/pass-user-attributes.png)

## <a name="test-the-flow"></a>フローをテストする

1. オンプレミスアプリケーションの URL に移動します。

2. DAB によって、ユーザー フローで構成したページにリダイレクトされます。

3. ページの一覧から IdP を選択します。

4. IdP にリダイレクトされたら、要求に従って資格情報を入力します。その IdP による必要に応じて、多要素認証 (MFA) トークンが含まれます。

5. 正常に認証されると、Azure AD B2C にリダイレクトされ、それによってアプリケーションの要求が DAB のリダイレクト URI に転送されます。

6. DAB によって、ポリシーが評価され、ヘッダーが計算されて、上流のアプリケーションにユーザーが送信されます。  

7. 要求されたアプリケーションが表示されます。

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)
