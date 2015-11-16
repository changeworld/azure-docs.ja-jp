<properties
	pageTitle="Azure ポータルを使用してメディア処理の規模を設定する方法"
	description="アカウントにプロビジョニングする [オンデマンド ストリーミング予約ユニット] および [エンコード予約ユニット] の数を指定することで Media Services の規模を設定する方法について説明します。"
	services="media-services"
	documentationCenter=""
	authors="juliako,milangada"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="juliako"/>


# Azure ポータルを使用してメディア処理の規模を設定する方法

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encoding-units.md)
- [Portal](media-services-portal-encoding-units.md)
- [REST](https://msdn.microsoft.com/library/azure/dn859236.aspx)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)

## 概要

Media Services アカウントは、メディア処理タスクを処理する速度を決定する予約ユニットの種類に関連付けられます。予約ユニットの種類には、**Basic**、**Standard**、**Premium** があります。たとえば、同じエンコード ジョブを実行した場合、**Standard** 予約ユニットの方が **Basic** 予約ユニットに比べ高速です。詳細については、「[Encoding Reserved Unit Types (エンコード予約ユニットの種類)](http://azure.microsoft.com/blog/author/milanga)」をご覧ください。

予約ユニットの種類を指定するだけでなく、エンコード予約ユニットを使用して、アカウントをプロビジョニングすることも指定できます。用意したエンコード予約ユニットの数によって、所定のアカウントで並列処理できるメディア タスクの数が決まります。たとえば、アカウントの予約ユニットの数が 5 である場合、処理するタスクがある限り、5 個のメディア タスクが並列実行されます。残りのタスクはキューで待機して、実行中のタスクが完了すると直ちにキューから取り出されて順番に処理されます。アカウントに予約ユニットが用意されていない場合、タスクは逐次処理されます。この場合、あるタスクが終了した後、次のタスクが開始するまでの待機時間は、システムのリソースが利用できるかどうかに左右されます。

>[AZURE.IMPORTANT]予約ユニットは、Azure Media Indexer を使用するインデックス作成ジョブを含む、すべてのメディア処理を並列化するために動作します。ただし、エンコードとは異なり、インデックス作成ジョブでは高速予約ユニットで高速処理は行いません。

予約ユニットの種類とエンコード予約ユニットの数を変更するには、以下の手順に従います。

1. [管理ポータル](https://manage.windowsazure.com/)で、**[Media Services]** をクリックします。次に、メディア サービスの名前をクリックします。

2. **[ENCODING]** ページを選択します。

	**[予約ユニットの種類]** を変更するには、[BASIC]、[STANDARD]、[PREMIUM] のいずれかを押します。

	選択した予約ユニットの種類の予約ユニット数を変更するには、**[ENCODING]** スライダーを使用します。


	![[プロセッサ] ページ](./media/media-services-portal-encoding-units/media-services-encoding-scale.png)


	>[AZURE.NOTE]シンガポール、香港、大阪、北京、上海のデータ センターでは、Premium 予約ユニットの種類を提供していません。

3. [保存] ボタンを押して、変更を保存します。

	新しいエンコード予約ユニットは、[保存] をクリックするとすぐに割り当てられます。

	>[AZURE.NOTE]コストの計算時には、24 時間の期間内に指定されたユニットの最大数が使用されます。

##クォータと制限

クォータと制限の詳細、サポート チケットを開く方法については、「[Quotas and limitations (クォータと制限)](media-services-quotas-and-limitations.md)」をご覧ください



##Media Services のラーニング パス

AMS のラーニング パスについては、以下を参照してください。

- [AMS のライブ ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS のオンデマンド ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

<!---HONumber=Nov15_HO2-->