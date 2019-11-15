---
title: FarmBeats リファレンス
description: ''
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 8e8e4524034f0a296045691309b065f8547bdaa0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797236"
---
# <a name="references"></a>参照

次に示すのは、Azure FarmBeats API の概要を示す一連の記述と手順です。

## <a name="rest-api"></a>REST API

Azure FarmBeats API は、農企業に JSON ベースの応答によって標準化された RESTful インターフェイスを提供します。これは、Azure FarmBeats の機能を活用するのに役立ちます。

- センサー、カメラ、ドローン、天気、衛星、キュレートされた地上データを取得する API
- 共通データ プロバイダー全体のデータの正規化/コンテキスト化
- すべての取り込まれたデータに対するスキーマ化されたアクセスとクエリ機能
- 作物の特徴に基づいてクエリを実行できるメタデータの自動生成  
- 高速なモデル構築のために自動生成される時系列集計
- カスタムデータ処理パイプラインを簡単に作成できる統合 Azure Data Factory (ADF) エンジン

## <a name="application-development"></a>アプリケーション開発

API には、Swagger の技術ドキュメントが含まれています。 すべての API とそれに対応する要求/応答の詳細については、[Swagger](https://aka.ms/FarmBeatsDatahubSwagger) を参照してください。

FarmBeats データ ハブ内のすべてのオブジェクト/リソースの概要を次に示します。

Farm | ファームは、FarmBeats システム内の物理的な場所に対応します。 各ファームには、ファーム名と一意のファーム ID があります。
--- | ---|
Device  | デバイスは、ファーム内に存在する物理デバイスに対応するものです。 各デバイスには一意のデバイス ID があります。 通常、デバイスはファーム ID を持つファームにプロビジョニングされます。
DeviceModel  | DeviceModel は、製造元、デバイスの種類 (ゲートウェイまたはノード) など、デバイスのメタデータに対応します。
センサー  | センサーは、値を記録する物理的なセンサーに対応するものです。 センサーは通常、デバイス ID を持つデバイスに接続されます。
SensorModel  | SensorModel はセンサーのメタデータに対応するものです。たとえば、製造元、センサーの種類 (アナログまたはデジタル)、センサーのメジャー (周辺温度や圧力) などです。
テレメトリ  | テレメトリでは、特定のセンサーと時間範囲のテレメトリ メッセージを読み取ることができます。
ジョブ  | ジョブはアクティビティのワークフローに対応します。これは FarmBeats システムで実行され、目的の出力を取得します。 各ジョブは、ジョブ ID とジョブの種類に関連付けられています。
JobType  | JobType は、システムでサポートされているさまざまなジョブの種類に対応します。 これには、システムおよびユーザーによって定義されるジョブの種類が含まれます。
ExtendedType  | ExtendedType は、システム内でシステムおよびユーザーによって定義される型の一覧に対応しています。 これは、FarmBeats システムで新しいセンサー、シーン、または Scenefile の種類をセットアップする際に役立ちます。
パートナー  | パートナーは、FarmBeats のセンサー/画像統合パートナーに対応します。
Scene  | シーンは、ファームのコンテキストで生成されるすべての出力に対応します。 各シーンには、シーン ID、シーン ソース、シーンの種類、およびファーム ID が関連付けられています。 各シーン ID には、複数のシーン ファイルを関連付けることができます。
SceneFile |SceneFile は、1 つのシーンに対して生成されるすべてのファイルに対応します。 1 つのシーン ID に複数の SceneFile ID を関連付けることができます。
ルール  |ルールは、アラートをトリガーするファーム関連のデータの条件に対応します。 各ルールは、ファームのデータのコンテキストに含まれます。
アラート:  | アラートは、ルール条件が満たされたときに生成される通知に対応します。 各アラートは、ルールのコンテキストに含まれます。
RoleDefinition  | RoleDefinition は、ロールに対して許可されるアクションと許可されないアクションを定義します。
RoleAssignment  |RoleAssignment は、ユーザーまたはサービス プリンシパルへのロールの割り当てに対応します。

**データ形式**

JSON (JavaScript Object Notation) は、任意のデータ構造をテキストによって簡単に表現できるようにする、言語に依存しない一般的なデータ形式です。 詳細については、json.org を参照してください。

## <a name="authentication-and-authorization"></a>認証と権限承認

REST API に対する HTTP 要求は Azure Active Directory (Azure AD) によって保護されます。
REST API に対して認証された要求を行うには、クライアント コードで有効な資格情報を使用して認証を行ってから API を呼び出す必要があります。 Azure AD はさまざまなアクターを連携させて認証を行い、認証の証拠としてクライアントにアクセス トークンを提供します。 その後、REST API 要求の HTTP Authorization ヘッダーでトークンが送信されます。 Azure AD 認証に関する詳細については、[Azure Active Directory](https://portal.azure.com) に関する開発者向けページを参照してください。

アクセス トークンは、次のようにして後続の API 要求のヘッダー セクションに含めて送信する必要があります。

```
headers = {"Authorization": "Bearer " + **access_token**}
```

**HTTP 要求ヘッダー**

Azure FarmBeats データ ハブへの API 呼び出しを行うときに指定する必要がある、最も一般的な要求ヘッダーを次に示します。


**ヘッダー** | **説明と例**
--- | ---
Content-Type  | 要求形式 (Content-type: application/<format>)。Azure FarmBeats データ ハブの API の形式は json です。 Content-Type, application/json
Authorization  | API 呼び出しを行うために必要なアクセス トークンを指定します。 **Authorization:Bearer <Access-Token>**
Accept | 応答形式。 Azure FarmBeats データ ハブ API シリーズの場合、形式は json です。**Accept: application/json**

**API 要求**

REST API 要求を行うには、HTTP (GET、POST、PUT、または DELETE) メソッド、API サービスの URL、(クエリ、データの送信、更新、または削除対象の) リソースの URI、および 1 つ以上の HTTP 要求ヘッダーを結合します。

API サービスの URL は、データハブの URL である https://\<yourdatahub-website-name>.azurewebsites.net です。また、必要に応じて GET 呼び出しにクエリ パラメーターを追加し、応答データのフィルター処理、サイズ制限、および並べ替えを行うことができます。

次の要求例では、デバイスの一覧を取得します。

```
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

大部分の GET、POST、PUT 呼び出しには、JSON 要求本文が必要です。

次の要求例ではデバイスを作成します (これには、入力 json と要求本文が含まれています)。

```json
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

**クエリ パラメーター**

REST **GET** 呼び出しでは、要求 URI に 1 つ以上のクエリ パラメーターを含めることによって、API 応答データのフィルター処理、サイズ制限、および並べ替えを行うことができます。 クエリ パラメーターについては、API ドキュメントおよび個々の GET 呼び出しを参照してください。
たとえば、クエリを実行してデバイスの一覧を取得する場合 (/Device に対して GET 呼び出しを行う場合)、次のクエリ パラメーターを指定できます。  

![プロジェクト (FarmBeats)](./media/for-references/query-parameters-device-1.png)

**エラー処理**

Azure FarmBeats データ ハブ API は、標準の HTTP エラーを返します。 最も一般的なエラー コードは次のとおりです。


 |エラー コード             | 説明 |
 |---                    | --- |
 |200                    | Success |
 |201                    | 作成 (Post) 成功 |
 |400                    | 正しくない要求。 要求にエラーがあります |
 |401                    | 権限がありません。 API の呼び出し元は、リソースにアクセスする特権がありません |
 |404                    | リソースが見つかりません |
 |5XX                    | 内部サーバー エラー。 5XX で始まるエラーコードは、サーバーで何らかのエラーが発生していることを意味します。 詳細については、サーバー ログと次のセクションを参照してください。 |


Azure FarmBeats データ ハブ API では、標準の HTTP エラーに加えて、次の形式の内部エラーも返されます。

```
{
  "message": "<More information on the error>",
  "status": "<error code>”,
  "code": "<InternalErrorCode>",
  "moreInfo": "<Details of the error>"
}
```

例:ファームを作成するときに、入力ペイロードで必須フィールド "Name" が指定されませんでした。 結果のエラーメッセージは次のようになります。

```
{
  "message": "Model validation failed",
  "status": 400,
  "code": "ModelValidationFailed",
  "moreInfo": "[\"The Name field is required.\"]"
}
```

## <a name="adding-users-or-app-registrations-to-azure-active-directory"></a>Azure Active Directory にユーザーまたはアプリ登録を追加する

 Azure FarmBeats API は、Azure Active Directory のユーザーまたはアプリ登録からアクセスできます。 Azure Active Directory でアプリ登録を作成するには、次の手順を実行します。  

1. [Azure portal](https://portal.azure.com) から、 **[Azure Active Directory] > [アプリの登録]** > **[新規登録]** に移動します。 または、既存のアカウントを使用することができます。
2. 新しいアカウントの場合は、次の手順を実行します。

    - 名前を入力
    - **この組織ディレクトリのみに含まれるアカウント (シングル テナント)** を選択します。
    - その他のフィールドは、既定値のままにします。
    - **[登録]** を選択します

3. 新規または既存のアプリの登録から、 **[概要]** で次の手順を実行します。

    - **[クライアント ID]** と **[テナント ID]** を取得します。
    - **[証明書とシークレット]** にアクセスして新しいクライアント シークレットを生成し、 **[Client-Secret] (クライアントシークレット)** を取得します。
    - [概要] に戻り、 **[Manage Application in local directory] (ローカルディレクトリ内のアプリケーションの管理)** の横にあるリンクをクリックします。
    - **[プロパティ]** にアクセスし、 **[オブジェクト ID]** を取得します。

4. [データ ハブ Swagger](https://<yourdatahub>.azurewebsites.net/swagger/index.html) にアクセスし、次の手順を実行します。
    - **RoleAssignment API** に移動します。
    - **POST** を実行し、先ほど作成した **[オブジェクト ID]** の RoleAssignment を作成します。 – 入力する json は次のとおりです。

  > [!NOTE]
  > ユーザーと AD 登録の追加の詳細については、[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) に関する記事を参照してください。

上記の手順を完了すると、アプリの登録 (クライアント) は、ベアラー認証とアクセス トークンを使用して Azure FarmBeats API を呼び出すことができます。  

アクセス トークンは、次のようにして後続の API 要求のヘッダー セクションに含めて送信します。

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
