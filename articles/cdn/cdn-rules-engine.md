<properties
	pageTitle="CDN - 規則エンジンを使用した既定の HTTP 動作のオーバーライド"
	description="規則エンジンでは、HTTP 要求の処理方法 (特定種類のコンテンツの配信のブロックなど) のカスタマイズのほか、キャッシュ ポリシーの定義、HTTP ヘッダーの変更などを行えます。"
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/25/2016" 
	ms.author="casoper"/>

# 規則エンジンを使用して既定の HTTP 動作をオーバーライドする

## 概要

規則エンジンでは、特定の種類のコンテンツの配信のブロック、キャッシュ ポリシーの定義、HTTP ヘッダーの変更など、HTTP 要求の処理方法をカスタマイズできます。このチュートリアルでは、CDN 資産のキャッシュ動作を変更するルールの作成について説明します。

> [AZURE.NOTE] 規則エンジンは、Premium CDN 層の機能です。Standard CDN と Premium CDN の機能の比較については、「[Azure CDN の概要](cdn-overview.md)」を参照してください。

## チュートリアル

1. [CDN プロファイル] ブレードで、**[管理]** をクリックします。

	![[CDN プロファイル] ブレードの [管理] ボタン](./media/cdn-rules-engine/cdn-rules-manage-btn.png)

	CDN 管理ポータルが開きます。

2. **[HTTP ラージ]** タブ、**[規則エンジン]** の順にクリックします。

	新しい規則のオプションが表示されます。

	![CDN の新しい規則オプション](./media/cdn-rules-engine/cdn-new-rule.png)

3. **[名前 / 説明]** テキストボックスに名前を入力します。

4. 規則を適用する要求の種類を指定します。既定では、一致条件 **[常に]** が選択されています。このチュートリアルでは **[常に]** を使用するため、選択されたままにしておきます。

	![CDN の一致条件](./media/cdn-rules-engine/cdn-request-type.png)

	>[AZURE.TIP] ドロップダウンには多くの種類の一致条件が用意されています。一致条件の左側の青い情報アイコンをクリックすると、現在選択している状態の詳細が表示されます。
	>
	>一致条件の詳しい完全な一覧については、「[CDN ルール エンジンの一致条件と機能詳細](cdn-rules-engine-details.md#match-conditions)」を参照してください。

5.  **[機能]** の横にある **[+]** をクリックして、新しい機能を追加します。左側のドロップダウンで、**[内部の最長期間を強制する]** を選択します。表示されるテキスト ボックスに、「**300**」と入力します。他の既定値はそのまま使用します。

	![CDN の機能](./media/cdn-rules-engine/cdn-new-feature.png)

	>[AZURE.NOTE] 一致条件と同様に、新しい機能の左側にある青い情報アイコンをクリックすると、その機能に関する詳細が表示されます。**[内部の最長期間を強制する]** では、**Cache-Control** および **Expires** ヘッダーをオーバーライドして、CDN エッジ ノードが配信元からの資産を更新するタイミングを制御します。今回の 300 秒のサンプルでは、その配信元から資産を更新する前に、CDN エッジ ノードは 5 分間、資産をキャッシュします。
	>
	>機能の詳しい完全な一覧については、「[CDN ルール エンジンの一致条件と機能詳細](cdn-rules-engine-details.md#features)」を参照してください。

6.  **[追加]** をクリックして、新しい規則を保存します。次に、新しい規則は承認を待機します。承認されると、ステータスが**保留中の XML** から**アクティブな XML** に変わります。

## 考慮事項

- 複数の規則が表示される順序は、これらの規則の処理方法に影響します。前の規則で指定したアクションは、後続の規則でオーバーライドされます。

## 関連項目
* [ルール エンジンの一致条件と機能詳細](cdn-rules-engine-details.md)
* [Azure CDN の概要](cdn-overview.md)
* [Microsoft Azure CDN でのリアルタイム統計情報](cdn-real-time-stats.md)
* [詳細な HTTP レポート](cdn-advanced-http-reports.md)
* [Edge のパフォーマンスの分析](cdn-edge-performance.md)

<!---HONumber=AcomDC_0302_2016-->