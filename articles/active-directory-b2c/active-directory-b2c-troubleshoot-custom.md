---
title: "Azure Active Directory B2C: カスタム ポリシーのトラブルシューティング | Microsoft Docs"
description: "Azure Active Directory B2C の問題のトラブルシューティング方法に関するトピック"
services: active-directory-b2c
documentationcenter: 
author: saeeda
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: saeeda
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 2fa67038f2a214c1569fc65fd9f1beba394cb790
ms.contentlocale: ja-jp
ms.lasthandoff: 04/27/2017

---

# <a name="azure-active-directory-b2c-collecting-logs"></a>Azure Active Directory B2C: ログの収集

この記事では、カスタム ポリシーの問題を診断できるよう、Azure AD B2C からログを収集する手順を説明します。

## <a name="use-application-insights"></a>Application Insights を使用する

Azure AD B2C では、Application Insights にデータを送信するための機能がサポートされています。  Application Insights は、例外を診断したり、アプリケーションのパフォーマンスの問題を視覚化したりするための手段を提供します。

### <a name="setup-application-insights"></a>Application Insights を設定する

1. [Azure ポータル](https://portal.azure.com)にアクセスします。 (Azure AD B2C テナントではなく) Azure サブスクリプションがあるテナントにいることを確認します。
1. 左側のナビゲーション メニューで、**[+ New]**(+ 新規) をクリックします。
1. **Application Insights** を探して選択し、**[Create]**(作成) をクリックします。
1. フォームに入力して **[Create]**(作成) をクリックします。 **[Application Type]**(アプリケーションの種類) は **[General]**(一般) を選択します。
1. リソースが作成されたら、Application Insights リソースを開きます。
1. 左側のメニューから **[Properties]**(プロパティ) を探してクリックします。
1. **インストルメンテーション キー**をコピーし、次のセクションのために保存します。

### <a name="set-up-the-custom-policy"></a>カスタム ポリシーを設定する

1. RP ファイル (例: SignUpOrSignin.xml) を開きます。
1. 次の属性を `<TrustFrameworkPolicy>` 要素に追加します:

  ```XML
  UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  ```

1. まだ存在しない場合、子ノード `<UserJourneyBehaviors>` を `<RelyingParty>` ノードに追加します。
2. 次のノードを `<UserJourneyBehaviors>` 要素の子として追加します。 `{Your Application Insights Key}` の部分は必ず、前のセクションで取得した**インストルメンテーション キー**に置き換えてください。

  ```XML
  <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
  ```

  * `DeveloperMode="true"` は、開発に適するが高ボリューム時は制約を受ける処理パイプラインを通じてテレメトリを迅速化するよう、Application Insights に指示します。
  * `ClientEnabled="true"` は、ページ ビューとクライアント側エラーを追跡するためのクライアント側スクリプトを Application Insights に送信します (不要)。
  * `ServerEnabled="true"` は、既存の UserJourneyRecorder JSON をカスタム イベントとして Application Insights に送信します。
  最終的な XML は次のようになります:

  ```XML
  <TrustFrameworkPolicy
    ...
    TenantId="fabrikamb2c.onmicrosoft.com"
    PolicyId="SignUpOrSignInWithAAD"
    UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  >
    ...
    <RelyingParty>
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
  </TrustFrameworkPolicy>
  ```

3. ポリシーをアップロードします。

### <a name="see-the-logs"></a>ログを確認する

>[!NOTE]
> Application Insights で新しいログが確認できるようになるまで、少し時間がかかります (5 分以内)。

1. [Azure Portal](https://portal.azure.com) で作成した Application Insights リソースを開きます。
1. **[Overview]**(概要) メニューで、**[Analytics]**(分析) をクリックします。
1. Application Insights で新しいタブを開きます。
1. 次に示すのは、ログを確認するために使用できるクエリの一覧です

| クエリ | 説明 |
|---------------------|--------------------|
traces | Azure AD B2C によって生成されたすべてのログを確認します |
traces \| where timestamp > ago(1d) | Azure AD B2C によって生成された直近 1 日分のすべてのログを確認します

分析ツールの詳細については、[こちら](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)を参照してください。





