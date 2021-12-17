---
title: 脅威インテリジェンス プラットフォームを Microsoft Sentinel に接続する | Microsoft Docs
description: 脅威インテリジェンス プラットフォーム (TIP) またはカスタム フィードを Microsoft Sentinel に接続して、脅威インジケーターを送信する方法について説明します。
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1490e7b1b0364918ec63bf39ad5e1c70120549ec
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523070"
---
# <a name="connect-your-threat-intelligence-platform-to-microsoft-sentinel"></a>脅威インテリジェンス プラットフォームを Microsoft Sentinel に接続する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

**関連項目**: [Microsoft Sentinel を STIX/TAXII 脅威インテリジェンス フィードに接続する](connect-threat-intelligence-taxii.md)

多くの組織では、脅威インテリジェンス プラットフォーム (TIP) ソリューションを使用して、さまざまなソースからの脅威インジケーター フィードを集約し、プラットフォーム内のデータをキュレーションしてから、ネットワーク デバイス、EDR/XDR ソリューション、Microsoft Sentinel のような SIEM などの、さまざまなセキュリティ ソリューションに適用する脅威インジケーターを選択します。 **脅威インテリジェンス プラットフォーム データ コネクタ** では、これらのソリューションを使用して、脅威インジケーターを Microsoft Sentinel にインポートできます。 

TIP データ コネクタは [Microsoft Graph Security tiIndicators API](/graph/api/resources/tiindicator) と連携してこれを実現するため、コネクタを使用して、API とやり取りする任意のカスタム脅威インテリジェンス プラットフォームから、Microsoft Sentinel (および Microsoft 365 Defender などの他の Microsoft セキュリティ ソリューション) にインジケーターを送信できます。

:::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-import-path.png" alt-text="脅威インテリジェンスのインポート パス":::

Microsoft Sentinel の[脅威インテリジェンス](understand-threat-intelligence.md)について、特に、Microsoft Sentinel と統合できる[脅威インテリジェンス プラットフォーム製品](threat-intelligence-integration.md#integrated-threat-intelligence-platform-products)について詳細をご確認ください。

## <a name="prerequisites"></a>前提条件  

- Microsoft Graph Security tiIndicators API との直接統合を使用する TIP 製品またはカスタム アプリケーションにアクセス許可を付与するための、**グローバル管理者** または **セキュリティ管理者** の Azure AD ロール。

- 脅威インジケーターを格納する Microsoft Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

## <a name="instructions"></a>手順

統合された TIP またはカスタム脅威インテリジェンス ソリューションから Microsoft Sentinel に脅威インジケーターをインポートするには、次の手順に従います。
1.  Azure Active Directory からアプリケーション ID とクライアント シークレットを取得する
2.  この情報を TIP ソリューションまたはカスタム アプリケーションに入力する
3.  Microsoft Sentinel で脅威インテリジェンス プラットフォーム データ コネクタを有効にする

### <a name="sign-up-for-an-application-id-and-client-secret-from-your-azure-active-directory"></a>Azure Active Directory からアプリケーション ID とクライアント シークレットにサインアップする

TIP を使用しているか、カスタム ソリューションを使用しているかにかかわらず、tiIndicators API では、フィードを接続して脅威インジケーターを送信できるようにするための基本的な情報が必要になります。 次の 3 つの情報が必要になります。

- アプリケーション (クライアント) ID
- ディレクトリ (テナント) ID
- クライアント シークレット

この情報は、次の 3 つの手順を含む **アプリの登録** と呼ばれるプロセスを通じて Azure Active Directory から取得できます。

- Azure Active Directory にアプリを登録する
- アプリから Microsoft Graph tiIndicators API に接続して脅威インジケーターを送信するために必要な、アクセス許可を指定する
- これらのアクセス許可をこのアプリケーションに付与するために、組織から同意を得ます。

#### <a name="register-an-application-with-azure-active-directory"></a>Azure Active Directory にアプリケーションを登録します

1. Azure portal から、 **[Azure Active Directory]** サービスに移動します。
1. メニューから **[アプリの登録]** を選択し、 **[新しい登録]** を選択します。
1. アプリケーションの登録を表す名前を選択し、 **[シングル テナント]** のラジオ ボタンを選択して、 **[登録]** を選択します。 

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-register-application.png" alt-text="アプリケーションを登録する":::

1. 表示された画面で、 **[アプリケーション (クライアント) ID]** と **[ディレクトリ (テナント) ID]** の値をコピーします。 これらは、Microsoft Sentinel に脅威インジケーターを送信する TIP またはカスタム ソリューションを構成するために後で必要になる情報の最初の 2 つです。 3 番目の **クライアント シークレット** が後に来ます。

#### <a name="specify-the-permissions-required-by-the-application"></a>アプリケーションに必要なアクセス許可を指定する

1. **Azure Active Directory** サービスのメイン ページに戻ります。

1. メニューから **[アプリの登録]** を選択し、新しく登録したアプリを選択します。

1. メニューで **[API Permissions]\(API の許可\)** を選択し、 **[Add a permission]\(許可の追加\)** をクリックします。

1. **[API を選択します]** ページで、 **[Microsoft Graph]** を選択し、Microsoft Graph のアクセス許可の一覧から選択します。

1. [アプリケーションに必要なアクセス許可の種類] のプロンプトが表示されたら、 **[アプリケーションのアクセス許可]** を選択します。 これは、アプリ ID とアプリ シークレット (API キー) での認証を行うアプリケーションで使用されるアクセス許可の種類です。

1. **[ThreatIndicators.ReadWrite.OwnedBy]** を選択し、 **[アクセス許可の追加]** を選択して、このアクセス許可をアプリのアクセス許可の一覧に追加します。

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-api-permissions-1.png" alt-text="アクセス許可を指定する":::

#### <a name="get-consent-from-your-organization-to-grant-these-permissions"></a>これらのアクセス許可を付与するために組織から同意を得る

1. Azure Active Directory のグローバル管理者は、同意を得るために、アプリの **[API のアクセス許可]** ページで **[<テナント名> に管理者の同意を与えます]** ボタンを選択する必要があります。 お使いのアカウントでグローバル管理者の役割をお持ちでない場合、このボタンは使用できず、この手順を実行するには、組織の全体管理者に依頼する必要があります。 

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-api-permissions-2.png" alt-text="同意する":::

1. アプリに同意が与えられると、 **[状態]** に緑色のチェックマークが表示されます。

アプリが登録され、アクセス許可が付与されたので、リストの最後のもの (アプリのクライアント シークレット) を取得できます。

1. **Azure Active Directory** サービスのメイン ページに戻ります。

1. メニューから **[アプリの登録]** を選択し、新しく登録したアプリを選択します。

1. メニューで **[Certificates & secrets]\(証明書とシークレット\)** を選択し **[New client secret]\(新しいクライアント シークレット\)** をクリックして、アプリのシークレット (API キー) を取得します。

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-client-secret.png" alt-text="クライアント シークレットの取得":::

1. **[Add]\(追加\)** をクリックし、**クライアント シークレットをコピー** します。

    > [!IMPORTANT]
    > **クライアント シークレット** は、ページから離れる前にコピーする必要があります。 このページから離れると、このシークレットを再度取得することはできません。 TIP またはカスタム ソリューションを構成するときに、この値が必要になります。

### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>この情報を TIP ソリューションまたはカスタム アプリケーションに入力する

以上で、Microsoft Sentinel に脅威インジケーターを送信するための TIP またはカスタム ソリューションの構成に必要な 3 つの情報がすべて揃いました。

- アプリケーション (クライアント) ID
- ディレクトリ (テナント) ID
- クライアント シークレット

1. 必要に応じて、統合された TIP またはカスタム ソリューションの構成に、これらの値を入力します。

1. ターゲット製品として、**Microsoft Sentinel** を指定します。

1. アクションとして、**アラート** を指定します。

この構成が完了すると、TIP またはカスタム ソリューションから、Microsoft Sentinel を対象とした **Microsoft Graph tiIndicators API** を介して脅威インジケーターが送信されます。

### <a name="enable-the-threat-intelligence-platforms-data-connector-in-microsoft-sentinel"></a>Microsoft Sentinel で脅威インテリジェンス プラットフォーム データ コネクタを有効にする

統合プロセスの最後の手順は、Microsoft Sentinel で **脅威インテリジェンス プラットフォーム データ コネクタ** を有効にすることです。 コネクタを有効にすると、TIP またはカスタム ソリューションから送信された脅威インジケーターを Microsoft Sentinel で受信できます。 これらのインジケーターは、組織のすべての Microsoft Sentinel ワークスペースで使用できます。 各ワークスペースについて、次の手順に従って脅威インテリジェンス プラットフォーム データ コネクタを有効にします。

1. Azure portal から、 **[Microsoft Sentinel]** サービスに移動します。

1. TIP またはカスタム ソリューションから送信された脅威インジケーターのインポート先となる **ワークスペース** を選択します。

1. メニューで **[Data connectors]\(データ コネクタ\)** 、 **[Threat Intelligence Platforms]\(脅威インテリジェンス プラットフォーム\)** 、 **[Open connector page]\(コネクタのページを開く\)** を順にクリックします。

1. 既にアプリの登録を終え、脅威インジケーターを送信するよう TIP またはカスタム ソリューションを設定したので、あとは **[Connect]\(接続\)** を押すだけです。

数分以内に、脅威インジケーターが Microsoft Sentinel ワークスペースに送られるようになります。 新しいインジケーターは、Microsoft Sentinel ナビゲーション メニューからアクセスできる **[脅威インテリジェンス]** ブレードで確認できます。

## <a name="next-steps"></a>次の手順

このドキュメントでは、Microsoft Sentinel に脅威インテリジェンス プラットフォームを接続する方法を説明しました。 Microsoft Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Microsoft Sentinel を使用した脅威の検出](./detect-threats-built-in.md)の概要。
