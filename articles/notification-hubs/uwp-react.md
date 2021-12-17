---
title: Android と iOS のアプリケーションに Azure Notification Hubs 通知を送信する
description: Azure Notification Hubs のクロスプラットフォーム機能について説明します。
author: sethmanheim
ms.author: sethm
ms.service: notification-hubs
ms.topic: conceptual
ms.date: 06/14/2021
ms.custom: template-concept
ms.openlocfilehash: 229de776e7e2dace0253d7cc4495663969cb3e62
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285242"
---
# <a name="send-notifications-to-android-and-ios-applications"></a>Android と iOS のアプリケーションに通知を送信する

この記事では、複数のプラットフォームでの Azure Notification Hubs の機能を示すために構築された Azure Notification Hubs のサンプル アプリケーションの概要について説明します。 このアプリケーションで使用する土地調査シナリオでは、デスクトップ アプリケーションの **Contoso Land Survey** によって、Android と iOS の両方の Contoso アプリケーションで受信できる通知が送信されます。

[GitHub から完全なサンプル](https://github.com/Azure/azure-notificationhubs-samples/tree/main/NotificationHubSample)をダウンロードすることができます。

## <a name="prerequisites"></a>前提条件

サンプルをビルドするには、次の前提条件を満たしておく必要があります。

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。
- Microsoft Visual Studio 2019 以降。 このサンプルでは、[Visual Studio 2019](https://www.visualstudio.com/products) を使用します。
- 次のワークロードがインストールされている Visual Studio 2019:
  - .NET 5.0 SDK
  - ASP.NET および Web の開発
  - Azure の開発
  - Node.js 開発
  - [UWP アプリ開発ツール](/windows/uwp/get-started/get-set-up)
- Android のプッシュ通知を有効にするための Firebase アカウント。
- iOS のプッシュ通知を有効にするための Apple Developer アカウント。
- Azure でホストされている SQL Server データベース インスタンス。
- Azure Notification Hubs の名前空間とハブ。

## <a name="sample-architecture"></a>サンプル アーキテクチャ

このソリューションは、次のコンポーネントで構成されています。

- Azure Notification Hubs のインスタンス: [Azure portal](https://portal.azure.com) で構成されている ANH の名前空間とハブ。
- SQL Server データベース: [Azure portal](https://portal.azure.com) で構成されている SQL Server データベースのインスタンス。
- ASP.NET アプリ バックエンド: .NET 5.0 上に構築された Web API バックエンド。これは、通知ハブに接続し、Azure App Service としてホストされます。
- Windows UWP アプリケーション: React Native を使用して構築された UWP アプリケーション。さまざまなユーザーや調査グループにニュースや調査の情報をディスパッチする "マネージャー" アプリケーションとして機能します。 また、このアプリケーションは、新しいユーザーを作成したり、ユーザーが割り当てられているグループを編集したりするのにも役立ちます。
- Android と iOS のクライアント アプリ: React Native を使用して構築された "Land Survey" アプリケーション。 これらのアプリにより、UWP マネージャー アプリケーションによってディスパッチされた情報がユーザーに表示されます。

## <a name="sample-folder-structure"></a>サンプルのフォルダー構造

GitHub のサンプル アプリケーションには、次のフォルダーが含まれています。

- **NotificationHub.Sample.API**: バックエンドとして機能する Visual Studio 2019 の ASP.NET Web API ソリューション。
- **app**: 管理者ログインを使用して通知をディスパッチし、調査ユーザー ログインで通知を受信できるようにする、クロスプラットフォームの React Native アプリケーション。
- **azure-template**: Azure サブスクリプションでこのデプロイを構成するために必要なすべてのリソースのデプロイに使用できる Azure Resource Manager テンプレート (`parameters.json` と `template.json`)。 Resource Manager テンプレートのデプロイの詳細については、[Azure portal を使用した ARM テンプレートの作成とデプロイ](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)に関する記事を参照してください。

## <a name="sample-overview"></a>サンプルの概要

次のセクションでは、このサンプルを構成するコンポーネントの概要について説明します。

### <a name="controllers"></a>Controllers

#### <a name="authentication"></a>認証

AuthenticateController.cs 内の次のメソッドは、ログインしたユーザーを認証するために使用されます。

```cs
[HttpPost]
[Route("login")]
public async Task<IActionResult> Login([FromBody] LoginModel model)
{
   var user = await userManager.FindByNameAsync(model.Username);
    if (user != null && await userManager.CheckPasswordAsync(user, model.Password))
    {
        var userRoles = await userManager.GetRolesAsync(user);

        var authClaims = new List<Claim>
        {
            new Claim(ClaimTypes.Name, user.UserName),
            new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString()),
        };

        foreach (var userRole in userRoles)
        {
            authClaims.Add(new Claim(ClaimTypes.Role, userRole));
        }

        var authSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_configuration["JWT:Secret"]));

        var token = new JwtSecurityToken(
            issuer: _configuration["JWT:ValidIssuer"],
            audience: _configuration["JWT:ValidAudience"],
            expires: DateTime.Now.AddHours(3),
            claims: authClaims,
            signingCredentials: new SigningCredentials(authSigningKey, SecurityAlgorithms.HmacSha256)
            );

        UserDetails userDetails = new UserDetails();
        userDetails.FirstName = model.Username;
        userDetails.LastName = model.Username;
        userDetails.UserName = model.Username;

        return Ok(new
        {
            token = new JwtSecurityTokenHandler().WriteToken(token),
            expiration = token.ValidTo,
            username = model.Username,
            email = user.Email,
            role = userRoles != null ? userRoles[0] : "Site-Manager",
            user = userDetails
        });
    }
    return Unauthorized();
}

[HttpPost]
[Route("register")]
public async Task<IActionResult> Register([FromBody] RegisterModel model)
{
    var userExists = await userManager.FindByNameAsync(model.Username);
    if (userExists != null)
        return StatusCode(StatusCodes.Status500InternalServerError, new Response { Status = "Error", Message = "User already exists!" });

    ApplicationUser user = new ApplicationUser()
    {
        Email = model.Email,
        SecurityStamp = Guid.NewGuid().ToString(),
        UserName = model.Username
    };
    var result = await userManager.CreateAsync(user, model.Password);
    if (!result.Succeeded)
        return StatusCode(StatusCodes.Status500InternalServerError, new Response { Status = "Error", Message = "User creation failed! Please check user details and try again." });

    if (!await roleManager.RoleExistsAsync(UserRoles.SiteManager))
        await roleManager.CreateAsync(new IdentityRole(UserRoles.SiteManager));

    if (await roleManager.RoleExistsAsync(UserRoles.SiteManager))
    {
        await userManager.AddToRoleAsync(user, UserRoles.SiteManager);
    }

    return Ok(new Response { Status = "Success", Message = "User created successfully!" });
}
```

### <a name="dashboard"></a>ダッシュボード

DashboardController.cs 内のダッシュボード コントローラーからは、すべての通知情報が返されます。

```cs
public class DashboardController : ControllerBase
{
    private readonly ApplicationDbContext _db;
    private readonly INotificationService _notificationService;

    public DashboardController(ApplicationDbContext dbContext, INotificationService notificationService)
    {
        _db = dbContext;
        _notificationService = notificationService;
    }

    [HttpGet("insights")]
    public async Task<IActionResult> GetDashboardInsight(string duration)
    {
        DashboardInsight dashboardInsight = new DashboardInsight();

        dashboardInsight.DeviceTrends = await _notificationService.GetAllRegistrationInfoAsync();

        var notificationMessages = _db.NotificationMessages.ToList();

        switch (duration)
        {
            case "Daily":
                {
                    dashboardInsight.NotificationTrends = _db.NotificationMessages
                                                            .GroupBy(m => m.SentTime.Date)
                                                            .Select(m => new NotificationTrend()
                                                            {
                                                                Timestamp = m.Key.ToShortDateString(),
                                                                NotificationsSent = m.Count()
                                                            }).ToList();
                }
                break;
            case "Weekly":
                {
                    dashboardInsight.NotificationTrends = notificationMessages
                                                            .GroupBy(m => WeekNumber(m.SentTime.Date))
                                                            .Select(m => new NotificationTrend()
                                                            {
                                                                Timestamp = FirstDateOfWeekISO8601(DateTime.Now.Year, m.Key).ToShortDateString(),
                                                                NotificationsSent = m.Count()
                                                            }).ToList();
                }
                break;
            case "Monthly":
                {
                    dashboardInsight.NotificationTrends = _db.NotificationMessages
                                                            .GroupBy(m => m.SentTime.Date.Month)
                                                            .Select(m => new NotificationTrend()
                                                            {
                                                                Timestamp = m.Key + "-" + DateTime.Now.Year,
                                                                NotificationsSent = m.Count()
                                                            }).ToList();
                }
                break;
            default:
                break;
        }

        dashboardInsight.TotalGroups = _db.SurveyGroups.Count();
        dashboardInsight.TotalUsers = _db.Users.Count();
        dashboardInsight.TotalNotificationsSent = _db.NotificationMessages.Count();

        return Ok(dashboardInsight);
    }
```

### <a name="front-end"></a>フロントエンド

このサンプルでは、バックエンド API を呼び出すために、実際の API 呼び出しを行う notification.service.js サービスを作成します。 このコードは app\data\services\notification.service.js にあります。

```nodejs
export const sendNotificationAPI = async (userInfo) => {
  let url = `${api}notification/send`;
  let authHeader = await getAuthHeaders();
  return await post(url, userInfo, { ...authHeader });
};

export const getNotificationsAPI = async () => {
  let url = `${api}notification/get`;
  let authHeader = await getAuthHeaders();
  return await get(url, { ...authHeader });
};
```

### <a name="manager-application"></a>マネージャー アプリケーション

React Native を使用して構築されたこのサンプルの UWP アプリケーションは、さまざまなユーザーや調査グループにニュースや調査の情報をディスパッチする "マネージャー" アプリケーションとして機能します。 また、このアプリケーションは、新しいユーザーを作成したり、ユーザーが割り当てられているグループを編集したりするのにも役立ちます。

マネージャー ログインの場合は、次のエンドポイントと POST 本文を使用して、任意のユーザー資格情報を生成します。 任意の HTTP REST クライアントを使用できます。

#### <a name="endpoint"></a>エンドポイント

```http
POST {{endpoint}}api/authenticate/register-admin
```

#### <a name="body"></a>本文

```http
{
  "username": "<USER_NAME>",
  "email": "<EMAIL>",
  "password": "<PASSWORD>"
}
```

登録が完了すると、同じ資格情報で UWP アプリケーションにサインインできるようになります。

### <a name="notification-controller"></a>通知コントローラー

NotificationController.cs の次のコードにより、通知の取得と送信が行われます。

```cs
[Produces("application/json")]
[Consumes("application/json")]
[HttpPost("send")]
public async Task<ActionResult> SendNotification([FromBody] NotificationMessage notificationMessage)
{
    try
    {
        List<string> tags = new List<string>();

        // attach survey group and user information with notificationMessage
        notificationMessage.SurveyGroupTags.ForEach(surveyGroupId =>
        {
            var group = _db.SurveyGroups.Where(g => g.Id == surveyGroupId).FirstOrDefault();
            if (group != null)
            {
                notificationMessage.SurveyGroups.Add(group);
                tags.Add($"group:{group.GroupName.Replace(' ', '-')}");
            }
        });

        notificationMessage.UserTags.ForEach(userId =>
        {
            var user = _db.Users.Where(u => u.Id == userId).FirstOrDefault();
            if (user != null)
            {
                notificationMessage.Users.Add(user);
                tags.Add($"username:{user.UserName}");
            }
        });
        _db.NotificationMessages.Add(notificationMessage);

        // send template notification
        var notification = new Dictionary<string, string>();
        notification.Add("title", notificationMessage.NotificationTitle);
        notification.Add("message", notificationMessage.NotificationDescription);

        var res = await _notificationService.RequestNotificationAsync(notificationMessage, tags, HttpContext.RequestAborted);

        await _db.SaveChangesAsync();
        return Ok(notificationMessage);
    }
    catch (Exception ex)
    {
        return BadRequest(ex.Message);
    }
}

[Produces("application/json")]
[HttpGet("get")]
public async Task<ActionResult> Get()
{
    try
    {
        var surveyGroups = _db.NotificationMessages.Include(message => message.SurveyGroups).Include(message => message.Users).ToList();
        return Ok(surveyGroups);
    }
    catch (Exception ex)
    {
        return BadRequest();
    }
}
```

### <a name="notification-service"></a>通知サービス

**NotificationHub.Sample.API/NotificationHub.Sample.API/Services/Notifications/INotificationService.cs** にある通知サービスには、インストールを作成および削除するためのメソッドが含まれます。 登録されているすべてのユーザーに通知を送信したり、すべての登録情報を取得したりするためのメソッドもあります。

```cs
public interface INotificationService
{
   Task<bool> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation, CancellationToken cancellationToken);
   Task<bool> DeleteInstallationByIdAsync(string installationId, CancellationToken cancellationToken);
   Task<bool> RequestNotificationAsync(NotificationMessage notificationMessage, IList<string> tags, CancellationToken cancellationToken);
   Task<List<DeviceTrend>> GetAllRegistrationInfoAsync();
}
```

## <a name="deploy-the-solution"></a>ソリューションのデプロイ方法

サンプルを実行するには、以下の前提条件が満たされている必要があります。

- Azure サブスクリプション。 Azure のサブスクリプションがない場合は、[Azure の無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)します。
- Android のプッシュ通知をセットアップするための Firebase アカウント。
- iOS のプッシュ通知をセットアップするための Apple Developer アカウント。
- Visual Studio 2019 がインストールされている Windows コンピューターと、ASP.NET Core および UWP アプリケーションの開発サポート。
- Android アプリケーションを実行する場合は、開発用コンピューター上の Android SDK と Android Studio。
- iOS アプリケーションを実行する場合は、XCode と最新の iOS SDK が含まれる Mac OSX マシン。

### <a name="deploy-resources"></a>リソースのデプロイ

- **[Azure Notification Hubs インスタンス](#create-resource-notification-hub):** Azure で構成された通知ハブ。
- **[SQL Server データベース インスタンス](#create-resource-sql-database):** Azure でホストされている SQL Server データベース。
- **ASP.NET アプリバックエンド:** .NET 5.0 で構築された Web API バックエンド。Azure App Service としてホストされている Azure Notification Hubs と接続します。 詳細については、「[クイックスタート: ASP.NET Web アプリをデプロイする](../app-service/quickstart-dotnetcore.md?pivots=development-environment-vs&tabs=net50)」を参照してください。

すべてのリソースを手動でデプロイしたくない場合は、GitHub リポジトリに用意されている Azure Resource Manager の template.json ファイルを使用して、すべての必要なインスタンスを 1 ステップでデプロイできます。 テンプレートファイルは、/azure-template のリポジトリで入手できます。 Resource Manager テンプレートの使用方法の詳細については、「[チュートリアル:Azure クイックスタート テンプレートを使用する](../azure-resource-manager/templates/template-tutorial-quickstart-template.md?tabs=azure-powershell)」を参照してください。

### <a name="set-up-notifications-for-android-and-ios"></a>Android と iOS に対する通知を設定する

- **Firebase:** Android で通知を受信するには、Firebase サービスを構成し、Azure Notification Hubs インスタンスにそれを接続します。 詳細については、[Google Firebase の設定の構成](configure-google-firebase-cloud-messaging.md)に関する記事を参照してください。
- **Apple Push Notification Service (APNS):** iOS で通知を受信するには、Apple Developer アカウントを使用して APNS サービスを構成し、Azure Notification Hubs インスタンスにそれを接続します。 詳細については、[Apple Push Notification Service の設定の構成](configure-apple-push-notification-service.md)に関する記事を参照してください。

## <a name="build-the-solution"></a>ソリューションをビルドする

サンプルをビルドするには、次の手順のようにします。

### <a name="create-resource-sql-database"></a>リソースを作成する: SQL データベース

Azure portal で [SQL Server データベース インスタンスを作成](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)します。 次に例を示します。

:::image type="content" source="media/uwp-react/resources-sql.png" alt-text="SQL インスタンス リソース":::

:::image type="content" source="media/uwp-react/template-screenshot.png" alt-text="デプロイ テンプレート":::

### <a name="create-resource-notification-hub"></a>リソースを作成する: 通知ハブ

次のようにして、Azure portal で通知ハブを作成します。

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-backend"></a>バックエンドを構成する

アプリ バックエンドを構成するには、 **/NotificationHub.Sample.API/appsettings.json** ファイルを見つけて、SQL Server の接続文字列を構成します。

```json
"ConnectionStrings": {
    "SQLServerConnectionString": "Server=tcp:<SERVER_NAME>,1433;Initial Catalog=<DB_NAME>;Persist Security Info=False;User ID=<DB_USER_NAME>;Password=<PASSWORD>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"
  },
```

`<SERVER_NAME>` は SQL Server の名前に、`<DB_NAME>` はデプロイしたデータベースの URL に、`<DB_USER_NAME>` は構成されているユーザー名に、`<PASSWORD>` は構成されているパスワードに、それぞれ置き換えます。

API ソリューションは、ローカル環境または任意の IIS サーバーで実行することも、Azure Web App Service としてデプロイすることもできます。 API の URL はすぐに使用できるようにしておきます。

### <a name="build-the-notificationhubsampleapi-application"></a>NotificationHub.Sample.API アプリケーションをビルドする

1. Visual Studio で、/NotificationHubSample/NotificationHub.Sample.API/NotificationHub.Sample.API.sln ソリューションを読み込みます。
2. **[ビルド]** メニューの **[ソリューションのビルド]** を選択します。
3. ソリューションを Azure に発行します。ソリューション エクスプローラーで、**NotificationHub.Sample.API** プロジェクトを右クリックし、 **[発行]** を選択します。
4. **[発行]** ダイアログで、 **[Azure]** (最初のオプション) を選択し、 **[次へ]** を選択します。
5. **[Azure App Service (Windows)]** を選択して、 **[次へ]** を選択します。
6. 次のダイアログにリダイレクトされ、そこで適切なサブスクリプション名と AppService インスタンスを選択できます。 バックエンドが Azure にデプロイされ、新しい URL にリダイレクトされます。

   :::image type="content" source="media/uwp-react/publish.png" alt-text="Publish project":::

7. バックエンドが発行された後、生成された URL を、 **/app** フォルダーにある **config.js** に追加します。 必ず URL の後に `/api/` を追加してください。

## <a name="run-react-native-frontend-application-for-windows"></a>Windows 用の React Native フロントエンド アプリケーションを実行する

このアプリケーションでは、モバイル アプリケーション (Android または iOS) と UWP マネージャー アプリケーションの両方が同時に実行されている必要があります。 これらの両方を実行するには、次の手順のようにします。

好みのターミナルまたはシェル ウィンドウで **app** フォルダーを開きます。 次に、以下を実行します。

### <a name="windows"></a>Windows

1. `npm install` を実行して、すべてのパッケージ依存関係をインストールします。
2. `npm run start` を実行して、1 つのコンソール ウィンドウで Metro サーバーを起動します。
3. 別のターミナル ウィンドウを開き、`npx react-native run-windows` を実行して UWP アプリケーションを実行します。
4. デプロイまたはビルドが失敗する場合は、トラブルシューティング ガイドを参照してください。

### <a name="android"></a>Android

1. React-Native プロジェクトで Firebase を構成して、通知機能を使用できるようにします。
2. Firebase プロジェクトを正常に構成したら、Firebase ポータルから **google-services.json** ファイルをダウンロードします。
3. **./app/android/app/google-services.json** をこの新しいファイルに置き換えます。 アプリケーション パッケージ名を、Firebase で構成されているものと一致するようにします。 パッケージ名は、**AndroidManifest.xml** ファイルで構成されています。
4. React Native アプリケーションで通知を構成する方法の詳細については、[React Native にプッシュ通知を送信するチュートリアル](/azure/developer/mobile-apps/notification-hubs-backend-service-react-native)に関する記事を参照してください。
5. 通知を構成した後、1 つのコンソール ウィンドウで `npm run start` を実行して Metro サーバーを起動します。 Windows アプリケーションが既に実行されている場合は、このステップをスキップできます。
6. 新しいコンソール ウィンドウで、`npx react-native run-android` を実行して Android アプリケーションを実行します。

### <a name="ios"></a>iOS

1. React-Native プロジェクトで APNS を構成して、通知機能を使用できるようにします。
2. iOS では、App Store または TestFlight を通して署名およびインストールされたアプリケーションによってのみ、通知を受信できます。 Apple Developer アカウントでアプリケーションを作成する必要があります。 Apple Developer アカウントで構成されているアプリケーションのバンドル識別子を、アプリケーションの **Info.plist** ファイルと **Entitlements.plist** ファイルで構成する必要があります。
3. React Native アプリケーションで通知を構成する方法の詳細については、[React Native にプッシュ通知を送信するチュートリアル](/azure/developer/mobile-apps/notification-hubs-backend-service-react-native)に関する記事を参照してください。
4. 通知を構成した後、1 つのコンソール ウィンドウで `npm run start` を実行して Metro サーバーを起動します。 Windows アプリケーションが既に実行されている場合は、このステップをスキップできます。
5. 新しいコンソール ウィンドウで、`npx react-native run-ios` を実行して iOS アプリケーションを実行します。 前述のように、ローカル環境にデプロイした iOS では、通知は機能しません。 iOS シミュレーターでは通知を受信できません。

## <a name="troubleshooting"></a>トラブルシューティング

Windows アプリケーション用に React Native を実行すると、次のエラーが発生する場合があります。

`error MSB4057: The target "Deploy" does not exist in the project`

これは、Windows 用の React Native での既知の問題です。 これを修正するには、**app/windows** フォルダーの **app.sln** を開いた後、Visual Studio で **CheckboxWindows** プロジェクトと **ReactNativeAsyncStorage** プロジェクトの **.csproj** ファイルをアンロードします。 その後、**app.csproj** ファイルで `...</Project>` の直前に次の行を追加してから、プロジェクトを再度読み込みます。

```xml
<Target Name="Deploy"/>
```

## <a name="next-steps"></a>次のステップ

- [チュートリアル: Azure Notification Hubs を使用して React Native アプリにプッシュ通知を送信する](/azure/developer/mobile-apps/notification-hubs-backend-service-react-native)
- [チュートリアル: Firebase SDK を使用して Android デバイスにプッシュ通知を送信する](android-sdk.md)
- [Azure Notification Hubs](notification-hubs-push-notification-overview.md)
- [Windows 用 React Native](https://microsoft.github.io/react-native-windows/)