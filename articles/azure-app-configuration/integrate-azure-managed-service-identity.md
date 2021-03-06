---
title: Azure マネージド ID と統合するためのチュートリアル | Microsoft Docs
description: このチュートリアルでは、Azure マネージド ID を利用し、Azure App Configuration に対して認証を行ってアクセスする方法について説明します
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 874522b6b4ca3739e0736d4f70f76bb82cad25f9
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957353"
---
# <a name="tutorial-integrate-with-azure-managed-identities"></a>チュートリアル:Azure マネージド ID との統合

Azure Active Directory [マネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) は、クラウド アプリケーションのシークレット管理を単純化するのに役立ちます。 マネージド ID では、自分のコードを設定して、それが実行される Azure コンピューティング サービス用に作成されたサービス プリンシパルを、Azure Key Vault またはローカル接続文字列に格納された別の資格情報の代わりに使用できます。 Azure App Configuration とその .NET Core、.NET、Java Spring のクライアント ライブラリには、MSI サポートが組み込まれています。 これの使用は必須ではありませんが、MSI によって、シークレットが含まれるアクセス トークンが不要になります。 コードがアプリ構成ストアにアクセスするために必要なのは、そのサービス エンドポイントだけです。また、シークレットの流出を心配することなく、その URL をコードに直接埋め込むことができます。

このチュートリアルでは、MSI を活用して App Configuration にアクセスする方法について説明します。 これは、クイック スタートで紹介されている Web アプリに基づいています。 先に進む前に、[App Configuration を使用した ASP.NET Core アプリの作成](./quickstart-aspnet-core-app.md)を完了しておいてください。

このチュートリアルの手順は、任意のコード エディターを使用して完了できます。 ただし、推奨のエディターは [Visual Studio Code](https://code.visualstudio.com/) です (Windows、macOS、および Linux プラットフォームで使用できます)。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * App Configuration へのマネージド ID アクセスを許可する
> * App Configuration に接続する際にマネージド ID を使用するようにアプリを構成する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* [.NET コア SDK](https://www.microsoft.com/net/download/windows)
* [構成済みの Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="add-a-managed-identity"></a>マネージド ID の追加

ポータルでマネージド ID を設定するには、最初に通常の方法でアプリケーションを作成した後、機能を有効にします。

1. 通常どおりに [Azure portal](https://aka.ms/azconfig/portal) でアプリを作成します。 ポータルでアプリに移動します。

2. 左側のナビゲーションで **[設定]** グループまで下へスクロールして、**[ID]** を選択します。

3. **[システム割り当て済み]** タブ内で、**[状態]** を **[オン]** に切り替えて、**[保存]** をクリックします。

    ![App Service でのマネージド ID の設定](./media/set-managed-identity-app-service.png)

## <a name="grant-access-to-app-configuration"></a>App Configuration へのアクセスの許可

1. [Azure portal](https://aka.ms/azconfig/portal) で、**[すべてのリソース]** をクリックし、クイック スタートで自分が作成したアプリ構成ストアをクリックします。

2. **[アクセス制御 (IAM)]** を選択します。

3. **[アクセスの確認]** タブ内で、**[ロールの割り当てを追加する]** カード UI の **[追加]** をクリックします。

4. **[ロール]** を *[共同作成者]* に、**[アクセスの割り当て先]** を *[App Service]* (*[システム割り当てマネージド ID]* の下) に設定します。

5. **[サブスクリプション]** を自分の Azure サブスクリプションに設定し、自分のアプリ用の Azure Service リソースを選択します。

6. **[Save]** をクリックします。

    ![マネージド ID の追加](./media/add-managed-identity.png)

## <a name="use-a-managed-identity"></a>マネージド ID の使用

1. *appsettings.json* を開いて以下を追加し、*<service_endpoint>* を (角かっこも含めて) アプリ構成ストアへの URL に置換します。

    ```json
    "AppConfig": {
        "Endpoint": "<service_endpoint>"
    }
    ```

2. *Program.cs* を開き、`config.AddAzureAppConfiguration()` メソッドを置換して `CreateWebHostBuilder` メソッドを更新します。

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(o => o.ConnectWithManagedIdentity(settings["AppConfig:Endpoint"]));
            })
            .UseStartup<Startup>();
    ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-from-local-git"></a>ローカル Git からのデプロイ

Kudu ビルド サーバーを使用したアプリへのローカル Git のデプロイを有効にする最も簡単な方法は、Cloud Shell を使用することです。

### <a name="configure-a-deployment-user"></a>デプロイ ユーザーを構成する

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Kudu を使用するローカル Git を有効にする

Kudu ビルド サーバーを使用したアプリへのローカル Git のデプロイを有効にするには、Cloud Shell で [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) を実行します。

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Git 対応アプリを作成するには、Cloud Shell で `--deployment-local-git` パラメーターを指定して [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) を実行します。

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

`az webapp create` コマンドの出力は次のようになります。

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>プロジェクトをデプロイする

"_ローカル ターミナル ウィンドウ_" で、ローカル Git リポジトリに Azure リモートを追加します。 _\<url>_ を、「[アプリの Git を有効にする](#enable-local-git-with-kudu)」で取得した Git リモートの URL で置き換えます。

```bash
git remote add azure <url>
```

アプリをデプロイするために、次のコマンドで Azure リモートにプッシュします。 パスワードの入力を求められたら、Azure Portal へのログインに使用するパスワードではなく、「[デプロイ ユーザーの構成](#configure-a-deployment-user)」で作成したパスワードを入力するようにしてください。

```bash
git push azure master
```

出力には、MSBuild (ASP.NET 向け)、`npm install` (Node.js 向け)、`pip install` (Python 向け) など、ランタイム固有のオートメーションが表示される場合があります。

### <a name="browse-to-the-azure-web-app"></a>Azure Web アプリの参照

ブラウザーを使用して自分の Web アプリに移動し、コンテンツがデプロイされていることを確認します。

```bash
http://<app_name>.azurewebsites.net
```

![App Service で実行されるアプリ](../app-service/media/app-service-web-tutorial-dotnetcore-sqldb/azure-app-in-browser.png)

## <a name="use-managed-identity-in-other-languages"></a>他の言語におけるマネージド ID の使用

.NET Framework 用および Java Spring 用の App Configuration プロバイダーにも、マネージド ID に対する組み込みサポートがあります。 これらの場合、プロバイダーを構成するときに、完全な接続文字列ではなく、アプリ構成ストアの URL エンドポイントを使用するだけで済みます。 たとえば、クイック スタートで作成される .NET Framework コンソール アプリの場合、*App.config* ファイルで次の設定を指定します。

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" endpoint="${Endpoint}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="Endpoint" value ="Set via an environment variable - for example, dev, test, staging, or production endpoint." />
    </appSettings>
    ```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure マネージド サービス ID を追加して、App Configuration へのアクセスを効率化し、アプリの資格情報管理を改善しました。 App Configuration の使用の詳細については、Azure CLI のサンプルに進んでください。

> [!div class="nextstepaction"]
> [CLI のサンプル](./cli-samples.md)
