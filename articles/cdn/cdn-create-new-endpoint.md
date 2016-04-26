<properties
	 pageTitle="Azure CDN を使用する方法"
	 description="このトピックでは、Azure の Content Delivery Network (CDN) を有効にする方法を説明します。このチュートリアルでは、新しい CDN プロファイルとエンドポイントの作成手順について説明します。"
	 services="cdn"
	 documentationCenter=""
	 authors="camsoper"
	 manager="erikre"
	 editor=""/>
<tags
	 ms.service="cdn"
	 ms.workload="media"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="get-started-article"
	 ms.date="04/15/2016" 
	 ms.author="casoper"/>

# Azure CDN を使用する方法  

[Azure ポータル](https://portal.azure.com)で、CDN を有効にすることができます。Web Apps、BLOB ストレージ、Cloud Services など多数の種類の統合された Azure 送信元がサポートされています。Azure Media Services ストリーミング エンドポイントに対して CDN を有効にすることもできます。配信元がこれらの Azure サービスのいずれでもない場合、または Azure 外の場所でホストされている場合は、カスタムの配信元を作成することができます。配信元の CDN エンドポイントを有効にすると、パブリックにアクセスできるオブジェクトはすべて CDN エッジ キャッシュの対象になります。

>[AZURE.NOTE] CDN の機能の概要と、機能の一覧については、「[CDN の概要](./cdn-overview.md)」を参照してください。

## 新しい CDN プロファイルを作成する

CDN プロファイルは、CDN エンドポイントのコレクションです。各プロファイルには、1 つ以上の CDN エンドポイントが含まれます。複数のプロファイルを使って、インターネット ドメイン、Web アプリケーション、またはその他の一部の基準別に CDN エンドポイントを整理する必要が生じる場合があります。

> [AZURE.NOTE] 既定では、1 つの Azure サブスクリプションは 4 つの CDN プロファイルに限定されます。各 CDN プロファイルは 10 個の CDN エンドポイントに制限されます。
>
> CDN の価格は、CDN プロファイル レベルで適用されます。Standard と Premium の CDN 機能を組み合わせて使用する場合は、複数の CDN プロファイルが必要になります。


**新しい CDN プロファイルを作成するには**

1. [Azure ポータル](https://portal.azure.com)の左上の **[新規]** をクリックします。**[新規]** ブレードで、**[メディア + CDN]**、**[CDN]** の順に選択します。

    新しい CDN プロファイル ブレードが表示されます。

    ![新しい CDN プロファイル][new-cdn-profile]

2. CDN プロファイルの名前を入力します。

3. **[価格レベル]** を選択するか、既定値を使用します。

4. **リソース グループ**を選択または作成します。リソース グループの詳細については、「[Azure リソース マネージャーの概要](resource-group-overview/#resource-groups)」を参照してください。

5. この CDN プロファイルの**サブスクリプション**を選択します。

6. **[場所]** を選択します。これは、CDN プロファイル情報が格納される Azure の場所です。CDN エンドポイントの場所には影響しません。ストレージ アカウントと同じ場所である必要はありません。

7. **[作成]** ボタンをクリックして、新しいプロファイルを作成します。

## 新しい CDN エンドポイントを作成する

**ストレージ アカウントに対する新しい CDN エンドポイントを作成するには**

1. [Azure ポータル](https://portal.azure.com)で、CDN プロファイルに移動します。これは、前の手順でダッシュボードにピン留めしている可能性があります。ピン留めしていない場合は、**[参照]**、**[CDN プロファイル]** の順にクリックし、エンドポイントの追加先のプロファイルをクリックします。

    CDN プロファイル ブレードが表示されます。

    ![CDN プロファイル][cdn-profile-settings]

2. **[エンドポイントの追加]** ボタンをクリックします。

    ![[エンドポイントの追加] ボタン][cdn-new-endpoint-button]

    **[エンドポイントの追加]** ブレードが表示されます。

    ![[エンドポイントの追加] ブレード][cdn-add-endpoint]

3. この CDN エンドポイントの**名前**を入力します。この名前は、ドメイン `<endpointname>.azureedge.net` でキャッシュされたリソースにアクセスする際に使用します。

4. **[配信元の種類]** ドロップダウンで、配信元の種類を選択します。

	![CDN 配信元の種類](./media/cdn-create-new-endpoint/cdn-origin-type.png)

5. **[配信元ホスト名]** ドロップダウンで、配信元のドメインを選択または入力します。ドロップダウンに、手順 4 で指定した種類の利用可能な配信元がすべて一覧表示されます。**[配信元の種類]** として *[カスタムの配信元]* を選択した場合は、カスタムの配信元のドメインを入力します。

6. **[元のパス]** ボックスに、キャッシュするリソースへのパスを入力するか、空のままにして、手順 5 で指定したドメインでのリソースをキャッシュするようにします。

7. **[配信元のホスト ヘッダー]** で、CDN の各要求で送信するホスト ヘッダーを入力するか、既定値をそのまま使用します。

8. **[プロトコル]** と **[配信元ポート]** に、配信元のリソースへのアクセスに使用するプロトコルとポートを指定します。クライアントは CDN 上のリソースにアクセスするときに、引き続きこれらの同じプロトコルとポートを使用します。少なくとも 1 つのプロトコル (HTTP または HTTPS) を選択する必要があります。
	
	> [AZURE.TIP] HTTPS を使用して CDN コンテンツにアクセスする場合、次の制約があります。
	> 
	> - CDN によって提供される SSL 証明書を使用する必要がある。サード パーティの証明書はサポートされません。
	> - CDN から提供されたドメイン (`<identifier>.azureedge.net`) を使用して、HTTPS コンテンツにアクセスする必要があります。現時点では CDN がカスタム証明書をサポートしていないために、HTTPS サポートではカスタム ドメイン名 (CNAME) を使用できません。

9. **[追加]** ボタンをクリックして、新しいエンドポイントを作成します。

10. エンドポイントが作成されると、プロファイルのエンドポイントの一覧に表示されます。一覧には、キャッシュされたコンテンツへのアクセスに使用する URL と元のドメインが表示されます。

    ![CDN エンドポイント][cdn-endpoint-success]

    > [AZURE.NOTE] エンドポイントはすぐには使用できません。登録が CDN ネットワークに反映されるまでに最大で 90 分かかる場合があります。CDN ドメイン名を直ちに使用しようとするユーザーは、CDN を経由してコンテンツを取得できるようになるまでは状態コード 404 を受け取る場合があります。

##関連項目
- [クエリ文字列による要求のキャッシュ動作の制御](cdn-query-string.md)
- [CDN コンテンツをカスタム ドメインにマッピングする方法](cdn-map-content-to-custom-domain.md)
- [Azure CDN エンドポイント上のアセットを事前に読み込む](cdn-preload-endpoint.md)
- [Azure CDN エンドポイントの消去](cdn-purge-endpoint.md)

[new-cdn-profile]: ./media/cdn-create-new-endpoint/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png

<!---HONumber=AcomDC_0420_2016-->