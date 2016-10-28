<properties
	pageTitle="Azure Notification Hubs - よく寄せられる質問 (FAQ)"
	description="Notification Hubs におけるソリューションの設計と実装についての FAQ"
	services="notification-hubs"
	documentationCenter="mobile"
	authors="wesmc7777"
	manager="erikre"
    keywords="プッシュ通知、プッシュ通知、iOS のプッシュ通知、android のプッシュ通知、ios のプッシュ、android のプッシュ"
	editor="" />

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="06/29/2016"
	ms.author="wesmc" />

#Azure Notification Hubs のプッシュ通知 - よく寄せられる質問 (FAQ)

##全般
###1\.Notification Hubs の価格モデルを教えてください。
Notification Hubs には、レベルが 3 つあります。

* **Free** - サブスクリプションあたり、1 か月間に最大 100 万プッシュが可能です。
* **Basic** - サブスクリプションあたりの 1 か月間のプッシュ数の基準値は 1,000 万です。クォータは、オプションで増やすことができます。
* **Standard** - サブスクリプションあたりの 1 か月間のプッシュ数の基準値は 1,000 万です。オプションでクォータを増やすことができ、充実した製品利用統計情報も利用できます。

最新の情報については、「[Notification Hubs の価格]」ページを参照してください。価格はサブスクリプション レベルで規定されており、Azure サブスクリプションに作成した名前空間または通知ハブの数に関係なく、プッシュ通知の開始数に基づいています。

**Free** レベルは、開発目的のために提供されており、SLA の保証はありません。このレベルは、Azure Notification Hubs のプッシュ通知機能の可能性について知りたいユーザーにとってはよい開始点ですが、中規模から大規模のアプリケーションには最適な選択肢ではない場合があります。

**Basic** と **Standard** レベルは本番環境用に提供されています。次の主要機能は *Standard レベル*のみで有効になります。

- *充実した製品利用統計情報* - Notification Hubs には、オフラインで表示および分析するために、製品利用統計情報やプッシュ通知の登録情報をエクスポートする機能が多数あります。
- *マルチ テナント機能* - Notification Hubs を使用して、マルチテナントをサポートするモバイル アプリを作成する場合に適しています。これにより、アプリの通知ハブの名前空間レベルでプッシュ通知サービス (PNS) の資格情報を設定して、この共通の名前空間でテナントに個別のハブを提供し、分離ができます。これにより、テナント間の重複を確実に避けるために各テナント用に分離した通知ハブからプッシュ通知を送受信する SAS キーを保持しつつ、簡単にメンテナンスをすることができます。
- *スケジュールされたプッシュ* - プッシュ通知をスケジュールできます。スケジュール後、キューに入れられ、送信されます。
- *一括インポート* - 登録を一括インポートできます。

###2\.Notification Hubs の SLA について教えてください。
**Basic** と **Standard** レベルの Notification Hubs では、アプリケーションが正しく構成されている場合、サポートされているレベルにデプロイされている通知ハブから、少なくとも 99.9% はプッシュ通知を送信したり、登録管理操作が実行できることが保証されています。SLA の詳細については、「[Notification Hubs の SLA]」ページを参照してください。

> [AZURE.NOTE] Notification Hubs は外部のプラットフォーム プロバイダーに依存してデバイスにプッシュ通知を送信するため、プラットフォーム通知サービスとデバイス間の区間における SLA の保証はありません。

###3\.Notification Hubs を使用している顧客を教えてください。
大勢のお客様が Notification Hubs をご使用になっています。次にいくつかご紹介します。

* ソチ 2014 年冬季オリンピック - 数百におよぶ利益団体、300 万台以上のデバイス、2 週間でディスパッチした通知 150 万件以上[CaseStudy - ソチ]
* Skanska - [CaseStudy - Skanska]
* シアトル タイムズ - [CaseStudy - シアトル タイムズ]
* Mural.ly - [CaseStudy - Mural.ly]
* 7Digital - [CaseStudy - 7Digital]
* Bing アプリ - 数千万台のデバイス、1 日あたり 300 万件の通知を送信

###4\.Notification Hubs をアップグレードまたはダウングレー年、サービス層を変更する方法は?
[Azure クラシック ポータル]にアクセスし、[Service Bus] をクリックし、名前空間をクリックし、お使いの通知ハブをクリックします。[スケール] タブの下で、Notification Hubs サービス層を変更できます。

![](./media/notification-hubs-faq/notification-hubs-classic-portal-scale.png)

##設計と開発
###1\.サポートするサーバー側のプラットフォームはどれですか。
SDK と、.NET、Java、PHP、Python、Node.js の[完全なサンプル]を提供しています。アプリのバックエンドを設定して、こうしたプラットフォームを使い Notification Hubs に通信できるようにします。Notification Hubs の API は REST のインターフェイスに基づいているため、依存関係を増やしたい場合は、代わりにそれらと直接対話するように設定することもできます。詳細については、「[NH - REST API]」ページを参照してください。

###2\.どのクライアント プラットフォームをサポートしていますか。
[Apple iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md)、[Android](notification-hubs-android-push-notification-google-gcm-get-started.md)、[Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)、[Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md)、[Kindle](notification-hubs-kindle-amazon-adm-push-notification.md)、[Android China (Baidu 経由)](notification-hubs-baidu-china-android-notifications-get-started.md)、Xamarin (iOS (xamarin-notification-hubs-ios-push notification-apns-get-started.md) および [Android](xamarin-notification-hubs-push-notifications-android-gcm.md))、[Chrome Apps](notification-hubs-chrome-push-notifications-get-started.md) および [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari) プラットフォームへのプッシュ通知の送信をサポートしています。これらのプラットフォームでプッシュ通知を送信する方法が説明されている概要チュートリアルの全一覧については、「[NH - 概要チュートリアル]」ページを参照してください。

###3\.SMS/メール/Web 通知はサポートしていますか。
Notification Hubs は、主に、上記のプラットフォームを使用してモバイル アプリに通知を送信することを目的としています。メールや SMS アラートの送信機能は提供していませんが、こうした機能を提供するサード パーティ製のプラットフォームを Notification Hubs に統合させて、[Azure Mobile Apps] を使い、ネイティブのプッシュ通知を送信できます。

また、Notification Hubs には、すぐに使えるブラウザー内プッシュ通知の送信サービスもありません。これは、サポートされているサーバー側プラットフォームに SignalR を使用して実装すれば使用できます。Chrome サンドボックスのブラウザー アプリに通知を送信する方法については、「[Chrome アプリのチュートリアル]」を参照してください。

###4\.Azure Mobile Apps と Azure Notification Hubs との関係について教えてください。いつ、どれを使えばいいのですか。
モバイル アプリのバックエンドが既にあり、プッシュ通知の送信機能だけを追加する場合は、Azure Notification Hubs を使う必要があります。モバイル アプリのバックエンドを最初から設定する場合は、Azure Mobile Apps の使用を検討する必要があります。Azure Mobile Apps では通知ハブを自動的にプロビジョニングするため、モバイル アプリのバックエンドから簡単にプッシュ通知を送信できます。Azure Mobile Apps の料金には、通知ハブの基本料金が含まれており、それに含まれるプッシュ数を超過する場合のみ料金を支払います。価格の詳細については、「[App Service の価格]」ページを参照してください。

###5\.Notification Hubs を使用してプッシュ通知を送信する場合、サポートされるデバイス数は何台ですか。
サポートされているデバイス数の詳細については、「[Notification Hubs の価格]」を参照してください。

特定のシナリオで、1,000 万を超える登録済みのデバイスをサポートするようにソリューションを拡張したい場合は、直接[お問い合わせ](https://azure.microsoft.com/overview/contact-us/)ください。

###6\.送信できるプッシュ通知の数を教えてください。
選択されているレベルに応じ、Azure は、システムで流動する通知の数を基に自動的に増やします。

>[AZURE.NOTE] 使用の全体コストは、送信するプッシュ通知数によって上がる場合があります。レベルによって制限があることを理解しておいてください。これは、「[Notification Hubs の価格]」ページで概説されています。

既存のお客様は Notification Hubs を使って毎日数百万のプッシュ通知を送信しています。Azure Notification Hubs を使用している限り、プッシュ通知のリーチを拡張するために、特別な処理は必要ありません。

###7\.プッシュ通知が自分のデバイスに到達するまでどのくらいの時間がかかりますか。
Azure Notification Hubs では、着信負荷が一定で急増することのない通常の使用シナリオにおいては、**1 分間に少なくとも 100 万件のプッシュ通知**を処理できます。この数はタグの数や着信するメッセージおよびその他の要因によって異なります。

サービスは、この推定配信時間中に、登録したタグやタグ式を基に、プラットフォームあたりのターゲットを計測し、各プッシュ通知配信サービスへメッセージを送信します。ここからデバイスに通知を送信するのはプッシュ通知サービス (PNS) の責任になります。

PNS では、通知の送信に対する SLA を保証しませんが、通常ほとんどのプッシュ通知はこちらのプラットフォームに送信された時から数分以内 (通常は 10 分の制限時間内) にデバイスに送信されます。中にはもう少し時間がかかるものもあります。

>[AZURE.NOTE] また、Azure Notification Hubs には 30 分以内に PNS に送信できないプッシュ通知を停止するポリシーがあります。こうした遅延にはさまざまな理由が考えられますが、最も一般的な理由は PNS がアプリケーションをスロットルするためです。

###8\.遅延に対する保証はありますか。
プッシュ通知は外部プラットフォーム専用のプッシュ通知サービスによって送信されるという性質であるため、遅延に対する保証はありません。通常、ほとんどのプッシュ通知は数分以内に送信されます。

###9\.名前空間と Notification Hubs を使用するソリューションを設計する際に考慮する必要がある点を教えてください。

####モバイル アプリ/環境

* 1 つの環境、1 つのモバイル アプリに対して通知ハブは 1 つにする必要があります。
* 複数テナントのシナリオでは、各テナントで個別のハブが必要になります。
* テスト環境と本番環境で同じ通知ハブを共有しないでください。通知の送信中に問題が発生する原因になる場合があります。たとえば、Apple では Sandbox と Production Push エンドポイントをそれぞれ個別の資格情報で提供しています。
* 既定では、Azure ポータルまたは Visual Studio の Azure が統合されているコンポーネントから、登録済みのデバイスにテスト通知を送信できます。しきい値は、登録プールからランダムに選択される 10 個のデバイスに設定されています。

>[AZURE.NOTE] ハブがもともと Apple Sandbox の証明書で構成されており、後から Apple の本番用の証明書を使用するように再構成される場合、古いデバイスのトークンは新しい証明書で無効になり、プッシュが失敗する原因になります。本番環境とテスト環境は別にし、個別の環境にはそれぞれのハブを使用することをお勧めします。

####PNS の資格情報

モバイル アプリがプラットフォームの開発者ポータル (Apple や Google など) で登録される場合、アプリの識別子とセキュリティ トークンを取得します。これはデバイスにプッシュ通知を送信できるようにするために、アプリのバックエンドがプラットフォームのプッシュ通知サービスに提供するものです。セキュリティ トークンは、Notification Hubs で構成する必要がある証明書 (Apple iOS、Windows Phone など) やセキュリティ キー (Google Android、Windows など) の形式にできます。これは通常、通知ハブ レベルで行われますが、マルチ テナントのシナリオでは名前空間レベルで行うこともできます。

####名前空間

名前空間はデプロイのグループ化に使用できます。また、マルチ テナントのシナリオで同じアプリのすべてのテナントにすべての通知ハブを表すことにも使用できます。

####地理的分散

プッシュ通知のシナリオでは、地理的分散は常に重要というわけではありません。最終的にプッシュ通知をデバイスに送信するさまざまなプッシュ通知サービス (APNS、GCM など) が均等に分散されていないことに注意してください。

アプリケーションが世界中で使われている場合は、別の名前空間で複数のハブを作成し、全世界のさまざまな Azure リージョンで Notification Hubs サービスの可用性を活用できます。

>[AZURE.NOTE] これは、特に登録に関する管理コストが増加するため、お勧めできません。本当に必要な場合のみ行ってください。

###10\.登録はアプリのバックエンドから行うべきですか。クライアント デバイスから直接行うべきですか。
アプリのバックエンドからの登録は、登録を作成する前にクライアント認証を実行する必要がある場合、アプリのロジックに基づいてアプリのバックエンドで作成または変更する必要のあるタグがある場合に役立ちます。詳細については、「[バックエンド登録のガイダンス]」ページと「[バックエンド登録のガイダンス - 2]」ページを参照してください。

###11\.プッシュ通知の配信のセキュリティ モデルは何ですか。
Azure Notification Hubs では [Shared Access Signature (SAS)](../storage/storage-dotnet-shared-access-signature-part-1.md) ベースのセキュリティ モデルを使用しています。SAS トークンはルートの名前空間レベルや詳細な通知ハブ レベルで使用できます。こうした SAS トークンは、送信メッセージの許可やリッスン通知の許可などさまざまな承認規則で設定できます。詳細については、「[NH セキュリティ モデル]」のドキュメントを参照してください。

###12\.プッシュ通知内の機密情報を含むペイロードはどのように扱われますか。
すべての通知は、プラットフォームのプッシュ通知サービス (PNS) によってターゲット デバイスに送信されます。送信者が Azure Notification Hubs に通知を送信すると、通知は処理されてそれぞれの PNS に渡されます。

送信者から Azure Notification Hubs に接続され、さらに HTTPS を使用する PNS へと繋がります。

>[AZURE.NOTE] Azure Notification Hubs でメッセージのペイロードが記録されることは決してありません。

ただし、機密情報を含むペイロードの送信には、セキュリティで保護されたプッシュのパターンをお勧めします。送信者は機密情報を含むペイロードなしで、メッセージの識別子が付いた ping 通知をデバイスに送信します。デバイスのアプリでこのペイロードを受信すると、セキュリティ保護された API が直接呼び出され、メッセージの詳細がフェッチされます。前述のパターンを実装する方法の説明については、「[NH - 安全なプッシュのチュートリアル]」ページを参照してください。

##操作
###1\.災害復旧 (DR) ストーリーとは何ですか。
Microsoft ではメタデータの災害復旧 (通知ハブの名前、接続文字列やその他の重要情報など) を提供しています。災害復旧シナリオが開始された場合、Notification Hubs インフラストラクチャで失われるのは、登録データ **セグメントのみ**です。復旧後、新しいハブにこのデータを再入力するソリューションを実装する必要があります。

- *ステップ 1* - 別のデーター センターにセカンダリ通知ハブを作成します。これは DR 発生時にすぐに作成できます。または最初から作成しておくこともできます。通知ハブは、数秒間で比較的高速にプロビジョニングできるため、どちらのオプションを選択しても大きな違いはありません。最初から作成しておくことによって、DR 発生時の管理機能への影響からも保護できるので、こちらのオプションを強くお勧めします。

- *ステップ 2* - プライマリの通知ハブからの登録をセカンダリの通知ハブに利用します。登録は入ってくるものであり、一般的に PNS 側により期限切れになる傾向により正しく作動しないので、両方のハブで登録を管理し、最新に保つことはお勧めしません。Notification Hubs では、期限が切れたり、無効な登録についての PNS のフィードバックを受信しながら、そうした登録をクリーンアップします。

次のいずれかのアプリのバックエンドを使用することをお勧めします。

- 一連の登録を管理し、DR 発生時にセカンダリの通知ハブに一括挿入できる

**OR**

- バックアップとしてプライマリのハブから登録のダンプを定期的に取得し、セカンダリ NH に一括挿入する

>[AZURE.NOTE] Standard レベルで利用できる登録のエクスポート/インポート機能については「[登録の一括エクスポートと変更]」を参照してください。

バックエンドがない場合、ターゲット デバイスのいずれかでアプリを起動すると、セカンダリ通知ハブで新しい登録が実行され、最終的に、セカンダリ通知ハブにすべてのアクティブなデバイスが登録されます。

短所は、アプリが起動していないとき通知を受信しない期間がデバイスにあるという点です。

###2\.監査ログ機能はありますか。
すべての Notification Hubs 管理操作は、[Azure クラシック ポータル]で公開されている操作ログに移動します。

##監視とトラブルシューティング
###1\.どのようなトラブルシューティングの機能がありますか。
Azure Notification Hubs には、特に通知がドロップされた場合の最も一般的なシナリオをトラブルシューティングする機能がいくつか用意されています。詳細については、ホワイト ペーパー「[NH - トラブルシューティング]」を参照してください。

###2\.どのようなテレメトリ機能がありますか。
Azure Notification Hubs では [Azure クラシック ポータル]でテレメトリ データの表示を有効にします。使用可能なメトリックの詳細については、「[NH - メトリック]」を参照してください。

>[AZURE.NOTE] 通知の成功は、プッシュ通知が外部のプッシュ通知サービス (例: Apple の APNS、Google の GCM など) に、配信されたことのみを意味します。ターゲット デバイスに通知が配信されるかどうかは PNS の責任です。通常、PNS は、サード パーティに配信メトリックを公開しません。

また、プログラムによってテレメトリ データをエクスポートする機能も提供しています (**Standard** レベル)。詳細については、「[NH - メトリックのサンプル]」を参照してください。

[Azure クラシック ポータル]: https://manage.windowsazure.com
[Notification Hubs の価格]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs の SLA]: http://azure.microsoft.com/support/legal/sla/
[CaseStudy - ソチ]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[CaseStudy - Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[CaseStudy - シアトル タイムズ]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[CaseStudy - Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[CaseStudy - 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[NH - REST API]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH - 概要チュートリアル]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Chrome アプリのチュートリアル]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[バックエンド登録のガイダンス]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[バックエンド登録のガイダンス - 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[NH セキュリティ モデル]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[NH - 安全なプッシュのチュートリアル]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[NH - トラブルシューティング]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[NH - メトリック]: https://msdn.microsoft.com/library/dn458822.aspx
[NH - メトリックのサンプル]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[登録の一括エクスポートと変更]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[完全なサンプル]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile Apps]: https://azure.microsoft.com/ja-JP/services/app-service/mobile/
[App Service の価格]: https://azure.microsoft.com/ja-JP/pricing/details/app-service/

<!---HONumber=AcomDC_0706_2016-->