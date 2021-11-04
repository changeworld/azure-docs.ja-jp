---
title: Datawiza を使用した安全なハイブリッド アクセス
titleSuffix: Azure AD
description: このチュートリアルでは、Datawiza と Azure AD を統合して安全なハイブリッド アクセスを実現する方法について説明します
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 8/27/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: d527db391185c899bed3137c3783aa81f8a00c63
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067031"
---
# <a name="tutorial-configure-datawiza-with-azure-active-directory-for-secure-hybrid-access"></a>チュートリアル: Datawiza と Azure Active Directory の統合を構成して安全なハイブリッド アクセスを実現する

このサンプル チュートリアルでは、Azure Active Directory (Azure AD) と [Datawiza](https://www.datawiza.com/) を統合して安全なハイブリッド アクセスを実現する方法について説明します。

Datawiza の [Datawiza Access Broker (DAB)](https://www.datawiza.com/access-broker) は、Azure AD を拡張してシングル サインオン (SSO) と粒度の細かいアクセスの制御を可能にし、オンプレミスとクラウドでホストされるアプリケーション (Oracle E-Business Suite、Microsoft IIS、SAP など) の保護を実現します。

このソリューションを使用することで企業は、Symantec SiteMinder、NetIQ、Oracle、IBM といった従来の Web アクセス マネージャー (WAM) から Azure AD へと迅速に移行でき、アプリケーションを書き換える必要がありません。 また、ノーコードまたはローコードのソリューションとして Datawiza を使用し、新しいアプリケーションを Azure AD に統合することもできます。 エンジニアリングにかかる時間が短縮されて大幅なコスト削減が可能となり、安全な方法でプロジェクトを遂行することができます。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure サブスクリプション。 サブスクリプションがない場合は、[試用アカウント](https://azure.microsoft.com/free/)を取得できます。

- お使いの Azure サブスクリプションにリンクされている [Azure AD テナント](../fundamentals/active-directory-access-create-new-tenant.md)。

- DAB を実行するには、[Docker](https://docs.docker.com/get-docker/) と [docker-compose](https://docs.docker.com/compose/install/) が必要です。 アプリケーションは、仮想マシンやベアメタルなど、任意のプラットフォームで実行できます。

- レガシ ID システムから Azure AD に移行するアプリケーション。 この例では、DAB はアプリケーションがある同じサーバーにデプロイされます。 アプリケーションは localhost: 3001 で実行され、DAB では localhost: 9772 を介してアプリケーションへのトラフィックがプロキシされます。 アプリケーションへのトラフィックは最初に DAB に到達し、次にアプリケーションにプロキシされます。

## <a name="scenario-description"></a>シナリオの説明

Datawiza 統合には、次のコンポーネントが含まれています。

- [Azure AD](../fundamentals/active-directory-whatis.md) - ID とアクセス管理のための Microsoft のクラウドベースのサービスです。ユーザーはこのサービスを利用して外部リソースおよび内部リソースにサインインしてアクセスすることができます。

- Datawiza Access Broker (DAB) - サービス ユーザーがサインオンし、HTTP ヘッダーを介してアプリケーションに透過的に ID を渡します。

- Datawiza Cloud Management Console (DCMC) - DAB を管理する集中管理コンソール。 DCMC には、管理者が DAB の構成とそのアクセス制御ポリシーを管理するための UI および RESTful API が用意されています。

次のアーキテクチャの図に、この実装を示します。

![アーキテクチャ図を示す画像](./media/datawiza-with-azure-active-directory/datawiza-architecture-diagram.png)

|手順| 説明|
|:----------|:-----------|
|  1. | ユーザーが、オンプレミスまたはクラウドでホストされているアプリケーションへのアクセスを要求します。 DAB では、ユーザーによってアプリケーションに対して行われた要求がプロキシされます。|
| 2. |DAB によって、ユーザーの認証状態が確認されます。 セッション トークンを受け取っていない場合、または渡されたセッション トークンが無効な場合は、認証のためにユーザーが Azure AD に送信されます。|
| 3. | Azure AD によって、Azure AD テナントでの DAB アプリケーションの登録時に指定されたエンドポイントにユーザー要求が送信されます。|
| 4. | DAB によって、アクセス ポリシーが評価され、アプリケーションに転送される HTTP ヘッダーに含まれる属性値が計算されます。 この手順では、DAB によって、ID プロバイダーが呼び出され、ヘッダー値を正しく設定するために必要な情報が取得されることがあります。 DAB によって、ヘッダー値が設定され、要求がアプリケーションに送信されます。 |
| 5. |  これで、ユーザーが認証され、アプリケーションにアクセスできるようになりました。|

## <a name="onboard-with-datawiza"></a>Datawiza を使用してオンボードする

オンプレミスまたはクラウドでホストされるアプリケーションを Azure AD と統合するには、[Datawiza Cloud Management Console](https://console.datawiza.com/) (DCMC) にログインします。

## <a name="create-an-application-on-dcmc"></a>DCMC でアプリケーションを作成する

[アプリケーションを作成し](https://docs.datawiza.com/step-by-step/step2.html)、DCMC でこのアプリケーションの `PROVISIONING_KEY` と `PROVISIONING_SECRET` のキー ペアを生成します。

Datawiza では、Azure AD 向けに、便利な[ワンクリック統合](https://docs.datawiza.com/tutorial/web-app-azure-one-click.html)が提供されています。 この方法で Azure AD と DCMC を統合すると、Azure AD テナントにアプリケーションの登録が自動的に作成されます。

![idp の構成を示す画像](./media/datawiza-with-azure-active-directory/configure-idp.png)

Azure AD テナントで既存の Web アプリケーションを使用したい場合は、このオプションを無効にし、フォームのフィールドの内容を自分で入力してもかまいません。 テナント ID、クライアント ID、クライアントシークレットが必要となります。 [Web アプリケーションを作成して、これらの値をテナントで取得](https://docs.datawiza.com/idp/azure.html)します。

![フォームを使用した IdP の構成を示す画像](./media/datawiza-with-azure-active-directory/use-form.png)

## <a name="run-dab-with-a-header-based-application"></a>ヘッダーベースのアプリケーションで DAB を実行する

1. Docker または Kubernetes のいずれかを使用して DAB を実行できます。 ユーザーがサンプル ヘッダーベースのアプリケーションを作成するには、Docker イメージが必要です。 [DAB と SSO の統合を構成](https://docs.datawiza.com/step-by-step/step3.html)します。 [Kubernetes を使用して DAB をデプロイ](https://docs.datawiza.com/tutorial/web-app-AKS.html)します。 ダウンロードして使用するために、サンプル Docker イメージ `docker-compose.yml` ファイルが用意されています。 [コンテナー レジストリにログイン](https://docs.datawiza.com/step-by-step/step3.html#important-step)して、DAB とヘッダーベースのアプリケーションのイメージをダウンロードします。

    ```yaml
    services:
      datawiza-access-broker:
      image: registry.gitlab.com/datawiza/access-broker
      container_name: datawiza-access-broker
      restart: always
      ports:
      - "9772:9772"
      environment:
      PROVISIONING_KEY: #############################################
      PROVISIONING_SECRET: ##############################################
      
      header-based-app:
      image: registry.gitlab.com/datawiza/header-based-app
      restart: always
     ports:
     - "3001:3001"
   ```

2. `docker-compose -f docker-compose.yml up` を実行した後に、ヘッダーベースのアプリケーションで Azure AD を使用して SSO が有効にされている必要があります。 ブラウザーを開いて､「`http://localhost:9772/`」と入力します。

3. Azure AD ログイン ページが表示されます。

## <a name="pass-user-attributes-to-the-header-based-application"></a>ヘッダーベースのアプリケーションにユーザー属性を渡す

1. DAB では IdP からユーザー属性を取得し、ヘッダーまたは Cookie を介してユーザー属性をアプリケーションに渡すことができます。 ヘッダーベースのアプリケーションにメール アドレス、名、姓などの[ユーザー属性を渡す](https://docs.datawiza.com/step-by-step/step4.html)方法の手順を参照してください。

2. ユーザー属性が正常に構成されると、各ユーザー属性に緑色のチェック記号が表示されるはずです。

   ![Datawiza アプリケーションのホーム ページを示す画像](./media/datawiza-with-azure-active-directory/datawiza-application-home-page.png)

## <a name="test-the-flow"></a>フローをテストする

1. アプリケーションの URL に移動します。

2. DAB から Azure AD ログインページにリダイレクトされます。

3. 認証が正常に完了すると、DAB にリダイレクトされます。

4. DAB によって、ポリシーが評価され、ヘッダーが計算されて、上流のアプリケーションにユーザーが送信されます。

5. 要求されたアプリケーションが表示されます。

## <a name="next-steps"></a>次のステップ

- [Datawiza と Azure AD B2C の統合を構成する](../../active-directory-b2c/partner-datawiza.md)

- [Datawiza のドキュメント](https://docs.datawiza.com)