---
title: Azure での自動スケールの使用
description: Azure でリソースの Web アプリ、クラウド サービス、仮想マシン、仮想マシン スケール セットをスケールする方法について説明します。
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: e0c9770e2065002a4e2acc1198ed096dc588f8e5
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342217"
---
# <a name="get-started-with-autoscale-in-azure"></a>Azure での自動スケールの使用
この記事では、Microsoft Azure Portal でリソースの自動スケール設定をセットアップする方法について説明します。

Azure Monitor の自動スケーリングは、[Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/)、[Cloud Services](https://azure.microsoft.com/services/cloud-services/)、[App Service - Web Apps](https://azure.microsoft.com/services/app-service/web/)、および [API Management サービス](../../api-management/api-management-key-concepts.md)にのみ適用されます。

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>サブスクリプションの自動スケール設定の表示

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u7ts]

Azure Monitor で自動スケールを適用できるすべてのリソースを確認できます。 詳しい手順については、次の手順を参照してください。

1. [Azure portal][1] を開きます。
1. 左側のウィンドウにある Azure Monitor アイコンをクリックします。
  ![Azure Monitor][2] を開きます。
1. **自動スケール** をクリックすると、現在の自動スケールの状態と共に、自動スケールが適用できるすべてのリソースが表示されます。
  ![Azure Monitor][3] での自動スケールの表示

上部のフィルター ウィンドウを使用すると、特定のリソース グループ、特定のリソースの種類、または特定のリソースに絞り込んでリソースを選択できます。

各リソースについて、現在のインスタンス数と自動スケールの状態を確認できます。 自動スケールの状態は次のいずれかになります。

- **未構成** : このリソースの自動スケール設定はまだ有効になっていません。
- **有効** : このリソースの自動スケール設定は有効になっています。
- **Disabled** : このリソースの自動スケール設定は無効になっています。

## <a name="create-your-first-autoscale-setting"></a>最初の自動スケール設定を作成する

簡単な手順に従って、最初の自動スケール設定を作成します。

1. Azure Monitor で **[自動スケール]** ブレードを開き、スケールするリソースを選択します (以下の手順では、Web アプリに関連付けられている App Service プランを使用します。 [初めての ASP.NET Web アプリを Azure に 5 分で作成][4]できます)。
1. 現在のインスタンス数は 1 です。 **[Enable autoscale]\(自動スケールを有効にする\)** をクリックします。
  ![新しい Web アプリのスケール設定][5]
1. スケール設定の名前を指定し、 **[ルールの追加]** をクリックします。 右側にコンテキスト ペインとして表示されるスケール ルールのオプションに確認してください。 既定では、リソースの CPU 使用率が 70% を超えた場合にインスタンス数を 1 つずつ増やしてスケールするようにオプションが設定されています。 このオプションを既定値のままにし、 **[追加]** をクリックします。
  ![Web アプリのスケール設定の作成][6]
1. これで最初のスケール ルールが作成されました。 UX では、ベスト プラクティスとして、ルール内に少なくとも 1 つのスケールを含めることが推奨されています。 そのためには次を行います。

    a. **[ルールの追加]** をクリックします。

    b. **[演算子]** に **[より小さい]** を設定します。

    c. **[しきい値]** を **20** に設定します。

    d. **[操作]** の **[Decrease count by]\(カウント数減少幅\)** を設定します。

   これで、CPU 使用率に基づいてスケールアウト/スケールインするスケール設定が作成されます。
   ![CPU に基づくスケール][8]
1. **[保存]** をクリックします。

お疲れさまでした。 これで、CPU 使用率に基づいて Web アプリを自動スケールする最初のスケール設定が作成されました。

> [!NOTE]
> 仮想マシン スケール セットまたはクラウド サービス ロールを使用する場合も同じ手順が適用されます。

## <a name="other-considerations"></a>その他の考慮事項
### <a name="scale-based-on-a-schedule"></a>スケジュールに基づくスケール変更
CPU 使用率に基づいてスケールするだけでなく、特定の曜日に異なる方法でスケールを設定することもできます。

1. **[Add a scale condition]\(スケール条件の追加\)** をクリックします。
1. スケール モードとルールの設定は既定の条件と同じです。
1. スケジュール、 **[Repeat specific days]\(特定の曜日に繰り返す\)** を選択します。
1. 曜日、スケール条件を適用する開始時刻と終了時刻を選択します。

![スケジュールに基づくスケール条件][9]
### <a name="scale-differently-on-specific-dates"></a>特定の日に異なる方法でスケールする
CPU 使用率に基づいてだけでなく、特定の曜日に異なる方法でスケールを設定することもできます。

1. **[Add a scale condition]\(スケール条件の追加\)** をクリックします。
1. スケール モードとルールの設定は既定の条件と同じです。
1. スケジュールで、 **[Specify start/end dates]\(開始日/終了日を指定する\)** を選択します。
1. スケール条件を適用する開始時刻と終了時刻を選択します。

![日付に基づくスケール条件][10]

### <a name="view-the-scale-history-of-your-resource"></a>リソースのスケール履歴を表示する
リソースをスケールアップ/スケールダウンするたびに、アクティビティ ログにイベントが記録されます。 **[実行履歴]** タブに切り替えることで、過去 24 時間のリソースのスケール履歴を表示できます。

![実行履歴][11]

すべてのスケール履歴 (最大 90 日間) を表示する場合は、 **[Click here to see more details]\(詳細を表示するには、ここをクリックしてください\)** をクリックします。 リソースやカテゴリに自動スケールがあらかじめ選択されたアクティビティ ログが表示されます。

### <a name="view-the-scale-definition-of-your-resource"></a>リソースのスケール定義を表示する
自動スケールは、Azure Resource Manager のリソースです。 **[JSON]** タブに切り替えると、JSON でスケール定義を表示できます。

![スケール定義][12]

必要に応じて、JSON に直接変更を加えることができます。 これらの変更は保存後反映されます。

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>自動スケールを無効にしてインスタンスを手動でスケールする
現在のスケール設定を無効にし、リソースを手動でスケールすることが必要な場合があります。

上部にある **[自動スケールの無効化]** ボタンをクリックします。
![自動スケールの無効化][13]

> [!NOTE]
> このオプションでは、構成が無効にます。 ただし、自動スケールをもう一度有効にした後元に戻せます。

自動スケールを無効にすると、手動でスケールするインスタンス数を設定できるようになります。

![手動スケールの設定][14]

**[Enable autoscale]\(自動スケールを有効にする\)** をクリックし、 **[保存]** をクリックすることで、いつでも自動スケールに戻すことができます。

## <a name="route-traffic-to-healthy-instances-app-service"></a>正常なインスタンスにトラフィックをルーティングする (App Service)

複数のインスタンスにスケールアウトする場合、App Service を使用すれば、ご利用のインスタンスに対して正常性チェックを実行して、正常なインスタンスにのみトラフィックをルーティングすることができます。 これを行うには、ご利用の App Service へのポータルを開いて、 **[監視]** の下にある **[正常性チェック]** を選択します。 **[有効]** を選択し、`/health` や `/api/health` など、ご利用のアプリケーションの有効な URL パスを指定します。 **[保存]** をクリックします。

ARM テンプレートでこの機能を有効にするには、`Microsoft.Web/sites` リソースの `healthcheckpath` プロパティをサイトの正常性チェック パス (`"/api/health/"` など) に設定します。 この機能を無効にするには、プロパティを空の文字列 (`""`) に戻します。

### <a name="health-check-path"></a>正常性チェック パス

パスからは、1 分以内に 200 から 299 まで (200 と 299 を含む) の状態コードでの応答がなければなりません。 1 分以内にパスからの応答がなかった場合、または範囲外のステータス コードが返された場合、インスタンスは "異常" と見なされます。 App Service は、正常性チェックのパス上の 302 件のリダイレクトには従いません。 正常性チェックは App Service の認証および承認機能と統合されています。システムは、これらのセキュリティ機能が有効になっている場合でも、エンドポイントに到達します。 独自の認証システムを使用する場合は、正常性チェック パスによって匿名アクセスが許可される必要があります。 サイトで HTTP **S** のみが有効になっている場合、正常性チェック要求が HTTP **S** 経由で送信されます。

正常性チェック パスによって、ご利用のアプリケーションの重要なコンポーネントが確認されます。 たとえば、ご利用のアプリケーションがデータベースとメッセージング システムに依存している場合、正常性チェックのエンドポイントはそれらのコンポーネントに接続する必要があります。 アプリケーションが重要なコンポーネントに接続できない場合は、アプリケーションが異常であることを示す 500 レベルの応答コードがパスから返されます。

#### <a name="security"></a>セキュリティ 

大企業の開発チームは、多くの場合、その公開されている API のセキュリティ要件に従う必要があります。 正常性チェック エンドポイントをセキュリティで保護するには、まず [IP 制限](../../app-service/app-service-ip-restrictions.md#adding-ip-address-rules)、[クライアント証明書](../../app-service/app-service-ip-restrictions.md#adding-ip-address-rules)、Virtual Network などの機能を使用して、アプリケーションへのアクセスを制限する必要があります。 着信要求の `User-Agent` が `ReadyForRequest/1.0` と一致するように要求することで、正常性チェック エンドポイント自体をセキュリティで保護できます。 以前のセキュリティ機能によって要求が既にセキュリティで保護されているため、User-Agent になりすますことはできません。

### <a name="behavior"></a>動作

正常性チェック パスを指定すると、App Service によってすべてのインスタンス上のそのパスに対して ping が実行されます。 ping を 5 回実行しても成功の応答コードが返されない場合、そのインスタンスは "異常" と見なされます。 異常なインスタンスは、ロード バランサーのローテーションから除外されます。 `WEBSITE_HEALTHCHECK_MAXPINGFAILURES` アプリ設定を使用すると、必要な ping の失敗回数を構成できます。 このアプリ設定は、2 から 10 の任意の整数に設定できます。 たとえば、これを `2` に設定した場合、ping が 2 回失敗すると、インスタンスがロード バランサーから削除されます。 さらに、スケール アップまたはスケール アウトする場合は、新しいインスタンスが要求に対して準備ができていることを保証するために、ロード バランサーに追加される前に App Service によって正常性チェック パスに対して ping が実行されます。

残りの正常なインスタンスでは、負荷が増加する可能性があります。 残りのインスタンスが過負荷にならないように、ご利用のインスタンスの半分以下が除外されます。 たとえば、App Service プランが 4 つのインスタンスにスケール アウトされ、そのうちの 3 つが異常である場合、最大 2 つがロードバランサーのローテーションから除外されます。 他の 2 つのインスタンス (1 つは正常、1 つは異常) は、引き続き要求を受信することになります。 すべてのインスタンスが異常であるという最悪のシナリオでは、何も除外されません。この動作をオーバーライドする場合は、`WEBSITE_HEALTHCHECK_MAXUNHEALTYWORKERPERCENT` アプリ設定の値を `0` と `100` の間に設定できます。 これを大きな値に設定すると、異常なインスタンスがさらに多く削除されます (既定値は 50)。

インスタンスが 1 時間、異常のままである場合、それは新しいインスタンスに置き換えられます。 App Service プランによれば、1 時間あたり最大で 1 つのインスタンス、1 日あたり最大で 3 つのインスタンスが置き換えられます。

### <a name="monitoring"></a>監視

アプリケーションの正常性チェック パスを指定したら、Azure Monitor を使用してご利用のサイトの正常性を監視できます。 ポータルの **[正常性チェック]** ブレードで、上部のツールバーにある **[メトリック]** をクリックします。 これにより新しいブレードが開き、サイトの過去の正常性状態を確認したり、新しいアラート ルールを作成したりできるようになります。 サイトの監視方法の詳細については、[Azure Monitor に関するガイドを参照してください](../../app-service/web-sites-monitor.md)。

## <a name="next-steps"></a>次のステップ
- [アクティビティ ログ アラートを作成して、サブスクリプションで自動スケールのエンジン操作をすべて監視する](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [アクティビティ ログ アラートを作成して、サブスクリプションで失敗した自動スケールのスケールイン/スケールアウト操作をすべて監視する](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: ../../app-service/quickstart-dotnetcore.md
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png
