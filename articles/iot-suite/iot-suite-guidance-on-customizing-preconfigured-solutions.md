<properties
	pageTitle="構成済みソリューションのカスタマイズ | Microsoft Azure"
	description="Azure IoT Suite の構成済みソリューションのカスタマイズ方法に関するガイダンスを提供します。"
	services=""
    suite="iot-suite"
	documentationCenter=".net"
	authors="stevehob"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/02/2016"
     ms.author="stevehob"/>

# 構成済みソリューションのカスタマイズ

Azure IoT Suite で提供される構成済みソリューションを利用すれば、スイート内のサービスを連動させ、エンド ツー エンド ソリューションを提供できます。ここから出発し、いくつかのポイントで特定のシナリオのためにソリューションを拡張したり、カスタマイズしたりできます。次のセクションでは、これらの一般的なカスタマイズ ポイントの概要を示します。

## ソース コードの検索

構成済みソリューションのソース コードは、次のリポジトリの Github で入手できます。

- リモート監視: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
- 予測的なメンテナンス: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

構成済みソリューションのソース コードは、Azure IoT Suite を使用して IoT ソリューションのエンド ツー エンド機能を実装する際に使用されるパターンとプラクティスを示すために提供されています。コマンドラインからの構築とデプロイに関するガイダンスについては、各構成済みソリューションに関する GitHub の Wiki に記載されています。

- [リモート監視に関する Wiki](https://github.com/Azure/azure-iot-remote-monitoring/wiki)
- [予測的なメンテナンスに関する Wiki](https://github.com/Azure/azure-iot-predictive-maintenance/wiki)

## 構成済みソリューションのアクセス許可の管理
各構成済みソリューションのソリューション ポータルは、新しい Azure Active Directory アプリケーションとして作成されています。ソリューション ポータル (AAD アプリケーション) のアクセス許可は次の方法で管理できます。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)を開きます。
2. **[自分の会社が所有するアプリケーション]** を選択し、チェック マークをクリックして、AAD アプリケーションに移動します。
3. **[ユーザー]** に移動し、Azure Active Directory テナント内のメンバーにロールを割り当てます。 

既定では、アプリケーションは、**Administrator**、**Read Only**、**Implicit Read Only** のロールでプロビジョニングされています。**Implicit Read Only** は、Azure Active Directory テナントのメンバーで、ロールが割り当てられていないユーザーに付与されています。GitHub リポジトリをフォークし、ソリューションを再デプロイした後で、[RolePermissions.cs](https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs) に変更を加えることができます。

## 構成済みルールの変更

リモート監視ソリューションには、ソリューションに表示されるデバイス情報、テレメトリ、およびルールのロジックを実装するための 3 つの [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) ジョブが含まれています。

この 3 つの Stream Analytics ジョブとその構文については、「[リモート監視の事前構成済みソリューションのチュートリアル](iot-suite-remote-monitoring-sample-walkthrough.md)」で詳しく説明しています。

これらのジョブを直接編集し、ロジックを変更したり、シナリオに固有のロジックを追加したりすることができます。Stream Analytics ジョブを見つけるには、次の手順を実行します。
 
1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. IoT ソリューションと同じ名前のリソース グループに移動します。 
3. 変更を加える Azure Stream Analytics ジョブを選択します。 
4. コマンドのセットで **Stop** を選択してジョブを停止します。 
5. 入力、クエリ、および出力を編集します。

    変更を簡単に加えるには、**Rules** ジョブのクエリを、**">"** の代わりに **"<"** を使用するように変更します。ルールを編集してもソリューション ポータルには引き続き **">"** が表示されますが、基になるジョブを変更したため、動作は反転されます。

6. ジョブの開始

> [AZURE.NOTE] リモート監視ダッシュボードは特定のデータによって異なるため、ジョブを変更するとダッシュボードで障害が発生する可能性があります。

## 独自のルールの追加

構成済みの Azure Stream Analytics ジョブの変更だけでなく、Azure ポータルを使用して、新しいジョブを追加したり、新しいクエリを既存のジョブに追加したりできます。

## デバイスのカスタマイズ

最も一般的な拡張アクティビティの 1 つは、シナリオに固有のデバイスの操作です。デバイスを操作するためのいくつかの方法があります。これらの方法には、シナリオに合わせたシミュレーション対象デバイスの変更や、[IoT デバイス SDK][] を使用したソリューションへの物理デバイスの接続が含まれます。

デバイスをリモート監視の構成済みソリューションに追加する手順については、[IoT Suite とデバイスの接続](iot-suite-connecting-devices.md)に関するドキュメントを参照してください

### 独自のシミュレーション対象デバイスの作成

リモート監視ソリューション ソース コード (上記を参照) には .NET シミュレーターが含まれます。このシミュレーターはソリューションの一部としてプロビジョニングされるものであり、さまざまなメタデータやテレメトリを送信したり、さまざまなコマンドに応答したりするために変更できます。

リモート監視の構成済みソリューションの構成済みシミュレーターは、温度と湿度のテレメトリを生成する冷却デバイスです。GitHub リポジトリをフォークしたら、[Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) プロジェクトでシミュレーターに変更を加えることができます。

また、Azure IoT には、リモート監視の構成済みソリューションと連動するように設計されている [C SDK サンプル](https://github.com/Azure/azure-iot-sdks/c/serializer/samples/remote_monitoring)があります。

### 独自の (物理) デバイスの構築と使用

[Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) では、IoT ソリューションにさまざまな種類のデバイス (言語およびオペレーティング システム) を接続するためのライブラリが提供されます。

## 次のステップ

このドキュメントでの説明をご希望のカスタマイズがある場合は、 [User Voice](https://feedback.azure.com/forums/321918-azure-iot) に機能の提案を投稿するか、この記事の下部でコメントしてください。iotsolhelp@microsoft.com までメールをお送りいただくこともできます。

IoT デバイスの詳細については、[Azure IoT 開発者向けサイト](https://azure.microsoft.com/develop/iot/)でリンクとドキュメントを検索してください。

[IoT デバイス SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/

<!---HONumber=AcomDC_0309_2016-->