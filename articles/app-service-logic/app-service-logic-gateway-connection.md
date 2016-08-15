<properties
   pageTitle="Logic Apps のオンプレミス データ ゲートウェイ接続 | Microsoft Azure"
   description="ロジック アプリからオンプレミス データ ゲートウェイへの接続の作成に関する情報。"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# Logic Apps 用のオンプレミス データ ゲートウェイへの接続

サポートされているロジック アプリのコネクタを使用すると、オンプレミス データ ゲートウェイ経由でオンプレミスのデータにアクセスする接続を構成できます。次の手順では、オンプレミス データ ゲートウェイをインストールし、ロジック アプリで使用できるように構成する方法を説明します。

## 前提条件

* オンプレミス データ ゲートウェイをアカウント (Azure Active Directory ベースのアカウント) に関連付けるために、職場または学校の電子メール アドレスを使用している必要があります。
    * Microsoft アカウント (@outlook.com、@live.com など) を使用している場合は、[こちらの手順に従って](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal)、Azure アカウントを使用して職場または学校の電子メール アドレスを作成できます。

> [AZURE.WARNING] Power BI に登録済みのアカウントを使用したときにしかオンプレミス ゲートウェイのインストールが完了しないという制限が現時点では存在します。当面、インストールを正常完了するために、任意のアカウントを "Power BI Free" に登録してください。

* オンプレミス データ ゲートウェイが[ローカル コンピューターにインストールされている](app-service-logic-gateway-install.md)必要があります。
* ゲートウェイが別の Azure オンプレミス データ ゲートウェイによって要求されていないことが必要です ([要求は、以下の手順 2. での作成時に行われます](#2-create-an-azure-on-premises-data-gateway-resource))。1 つのインストールは 1 つのゲートウェイ リソースのみに関連付けることができます。

## 接続のインストールと構成

### 1\.オンプレミス データ ゲートウェイのインストール

オンプレミス データ ゲートウェイのインストールについては、[この記事](app-service-logic-gateway-install.md)を参照してください。残りの手順を続行する前に、オンプレミス コンピューターにゲートウェイをインストールする必要があります。

### 2\.Azure オンプレミス データ ゲートウェイ リソースの作成

インストール後、オンプレミス データ ゲートウェイに Azure サブスクリプションを関連付ける必要があります。

1. ゲートウェイのインストールで使用したのと同じ職場または学校の電子メール アドレスを使用して、Azure にログインします。
1. **[新規]** リソースのボタンをクリックします。
1. **オンプレミス データ ゲートウェイ**を検索し、選択します。
1. ゲートウェイをアカウントに関連付けるための情報を入力します (適切な**インストール名**を選択するなど)。

    ![オンプレミス データ ゲートウェイ接続][1]
1. **[作成]** ボタンをクリックしてリソースを作成します。

### 3\.デザイナーでのロジック アプリ接続の作成

Azure サブスクリプションをオンプレミス データ ゲートウェイのインスタンスと関連付けたので、次はロジック アプリ内からゲートウェイへの接続を作成できます。

1. ロジック アプリを開き、オンプレミス接続をサポートするコネクタを選択します (この記事の執筆時点では SQL Server)。
1. **[Connect via on-premises data gateway (オンプレミス データ ゲートウェイ経由で接続する)]** チェック ボックスをオンにします。

    ![ロジック アプリ デザイナー ゲートウェイの作成][2]
1. 接続する **[ゲートウェイ]** を選択し、必要なその他の接続情報を指定します。
1. **[作成]** をクリックして接続を作成します。

以上で、ロジック アプリで使用するための接続が正しく構成されました。

## 次のステップ
- [ロジック アプリの接続の例とシナリオ](app-service-logic-examples-and-scenarios.md)
- [エンタープライズ統合機能](app-service-logic-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/app-service-logic-gateway-connection/createblade.PNG
[2]: ./media/app-service-logic-gateway-connection/blankconnection.PNG
[3]: ./media/app-service-logic-gateway-connection/checkbox.PNG

<!---HONumber=AcomDC_0803_2016-->