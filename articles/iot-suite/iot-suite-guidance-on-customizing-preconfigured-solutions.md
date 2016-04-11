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

構成済みソリューションのソース コードは、Azure IoT Suite を使用して IoT ソリューションのエンド ツー エンド機能を実装する際に使用されるパターンとプラクティスを示すために提供されています。ソリューションをビルドしてデプロイする方法の詳細については、GitHub リポジトリを参照してください。

## 構成済みルールの変更

リモート監視ソリューションには、ソリューションに表示されるデバイス情報、テレメトリ、およびルールのロジックを実装するための 3 つの [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) ジョブが含まれています。

この 3 つの Stream Analytics ジョブとその構文については、「[リモート監視の事前構成済みソリューションのチュートリアル](iot-suite-remote-monitoring-sample-walkthrough.md)」で詳しく説明しています。

これらのジョブを直接編集し、ロジックを変更したり、シナリオに固有のロジックを追加したりすることができます。Stream Analytics ジョブを見つけるには、次の手順を実行します。
 
1. [Azure ポータル](https://portal.azure.com)に移動します。
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

また、Azure IoT には、リモート監視の構成済みソリューションと連動するように設計されている [C SDK サンプル](https://github.com/Azure/azure-iot-sdks/tree/master/c/serializer/samples/remote_monitoring)があります。

### 独自の (物理) デバイスの構築と使用

[Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) では、IoT ソリューションにさまざまな種類のデバイス (言語およびオペレーティング システム) を接続するためのライブラリが提供されます。

## アプリケーション ロールの手動設定

以下の手順では、**Admin** および **ReadOnly** アプリケーション ロールを構成済みソリューションに追加する方法を説明しています。azureiotsuite.com サイトからプロビジョニングされた構成済みのソリューションには既に **Admin** ロールと **ReadOnly** ロールが含まれています。

**ReadOnly** ロールのメンバーはダッシュボードとデバイス一覧を表示できますが、デバイスの追加、デバイス属性の変更、またはコマンドの送信は実行できません。**Admin** ロールのメンバーは、ソリューション内のすべての機能に完全にアクセスできます。

1. [Azure クラシック ポータル][lnk-classic-portal]に移動します。

2. **[Active Directory]** を選択します。

3. ソリューションをプロビジョニングするときに使用した AAD テナントの名前をクリックします。

4. **[アプリケーション]** をクリックします。

5. 構成済みソリューション名と一致するアプリケーションの名前をクリックします。一覧にアプリケーションが表示されない場合は、**[表示]** ドロップダウンの **[Applications my company owns (自分の会社が所有するアプリケーション)]** を選択して、チェック マークをクリックします。

6.  ページ下部の **[マニフェストの管理]**、**[マニフェストのダウンロード]** を順にクリックします。

7. これにより、.json ファイルがローカル コンピューターにダウンロードされます。このファイルを好みのテキスト エディターで開いて編集します。

8. .json ファイルの 3 行目は以下のようになります。

  ```
  "appRoles" : [],
  ```
  これを次のコードに置き換えます。

  ```
  "appRoles": [
  {
  "allowedMemberTypes": [
  "User"
  ],
  "description": "Administrator access to the application",
  "displayName": "Admin",
  "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "isEnabled": true,
  "value": "Admin"
  },
  {
  "allowedMemberTypes": [
  "User"
  ],
  "description": "Read only access to device information",
  "displayName": "Read Only",
  "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
  "isEnabled": true,
  "value": "ReadOnly"
  } ],
  ```

9. 更新された .json ファイルを保存します (既存のファイルを上書きできます)。

10.  Microsoft Azure 管理ポータルのページの下部で **[Manage Manifest (マニフェストの管理)]**、**[Upload Manifest (マニフェストのアップロード)]** の順に選択し、前の手順で保存した.json ファイルをアップロードします。

11. これで、**Admin** ロールと **ReadOnly** ロールがアプリケーションに追加されました。

12. これらのロールのいずれかをディレクトリ内のユーザーに割り当てるには、「[azureiotsuite.com サイトでのアクセス許可][lnk-permissions]」を参照してください。

## フィードバック

このドキュメントでの説明をご希望のカスタマイズがある場合は、 「[ユーザーの声](https://feedback.azure.com/forums/321918-azure-iot)」に機能の提案を投稿するか、この記事の下部でコメントしてください。

## 次のステップ

IoT デバイスの詳細については、[Azure IoT 開発者向けサイト](https://azure.microsoft.com/develop/iot/)でリンクとドキュメントを検索してください。

[IoT デバイス SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-permissions.md
[lnk-classic-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0330_2016------>