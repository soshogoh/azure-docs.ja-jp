---
title: コンテナーの構成
titlesuffix: Face - Azure Cognitive Services
description: コンテナーの構成設定。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: diberry
ms.openlocfilehash: 6a4d20073275e3d858cecb73c2e95c97ea53a647
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311972"
---
# <a name="configure-face-docker-containers"></a>Face Docker コンテナーの構成

**Face** コンテナーのランタイム環境は、`docker run` コマンドの引数を使用して構成されます。 このコンテナーには、いくつかの必須の設定と省略可能な設定があります。 いくつかのコマンドの[例](#example-docker-run-commands)をご覧ください。 このコンテナーに固有の設定は、課金設定です。 

コンテナーの設定は[階層](#hierarchical-settings)になっていて、[環境変数](#environment-variable-settings)または Docker の[コマンドライン引数](#command-line-argument-settings)を使用して設定できます。

## <a name="configuration-settings"></a>構成設定

[!INCLUDE [Container shared configuration settings table](../../../includes/cognitive-services-containers-configuration-shared-settings-table.md)]

> [!IMPORTANT]
> [`ApiKey`](#apikey-configuration-setting)、[`Billing`](#billing-configuration-setting)、[`Eula`](#eula-setting) の各設定は一緒に使用されるため、それらの 3 つすべてに有効な値を指定する必要があります。そうしないと、お客様のコンテナーは起動しません。 これらの構成設定を使用してコンテナーをインスタンス化する方法の詳細については、「[課金](face-how-to-install-containers.md#billing)」を参照してください。

## <a name="apikey-configuration-setting"></a>ApiKey 構成設定

`ApiKey` 設定では、コンテナーの課金情報を追跡するために使用される Azure リソース キーを指定します。 ApiKey の値を指定する必要があります。また、その値は、[`Billing`](#billing-configuration-setting) 構成設定に指定された _Face_ リソースの有効なキーであることが必要です。

この設定は次の場所で確認できます。

* Azure portal:**Face の** [リソース管理] の **[キー]** の下

## <a name="applicationinsights-setting"></a>ApplicationInsights 設定

[!INCLUDE [Container shared configuration ApplicationInsights settings](../../../includes/cognitive-services-containers-configuration-shared-settings-application-insights.md)]

## <a name="billing-configuration-setting"></a>Billing 構成設定

`Billing` 設定では、コンテナーの課金情報を測定するために使用される Azure の _Face_ リソースのエンドポイント URI を指定します。 この構成設定の値を指定する必要があり、値は Azure の _Face_ リソースの有効なエンドポイント URI である必要があります。 コンテナーから、約 10 ～ 15 分ごとに使用状況が報告されます。

この設定は次の場所で確認できます。

* Azure portal:**Face の**概要、ラベル `Endpoint`

|必須| Name | データ型 | 説明 |
|--|------|-----------|-------------|
|はい| `Billing` | String | 課金エンドポイント URI<br><br>例:<br>`Billing=https://westcentralus.api.cognitive.microsoft.com/face/v1.0` |

## <a name="eula-setting"></a>Eula 設定

[!INCLUDE [Container shared configuration eula settings](../../../includes/cognitive-services-containers-configuration-shared-settings-eula.md)]

## <a name="fluentd-settings"></a>Fluentd の設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-fluentd.md)]

## <a name="http-proxy-credentials-settings"></a>Http プロキシ資格情報設定

[!INCLUDE [Container shared configuration fluentd settings](../../../includes/cognitive-services-containers-configuration-shared-settings-http-proxy.md)]

## <a name="logging-settings"></a>Logging の設定
 
[!INCLUDE [Container shared configuration logging settings](../../../includes/cognitive-services-containers-configuration-shared-settings-logging.md)]

## <a name="mount-settings"></a>マウントの設定

コンテナーとの間でデータを読み書きするには、バインド マウントを使用します。 入力マウントまたは出力マウントは、[docker run](https://docs.docker.com/engine/reference/commandline/run/) コマンドで `--mount` オプションを指定することによって指定できます。

Face コンテナーでは、トレーニングやサービスのデータを格納するために入力マウントまたは出力マウントが使用されることはありません。 

ホストのマウント場所の厳密な構文は、ホスト オペレーティング システムによって異なります。 また、Docker サービス アカウントによって使用されるアクセス許可とホストのマウント場所のアクセス許可とが競合するために、[ホスト コンピューター](face-how-to-install-containers.md#the-host-computer)のマウント場所にアクセスできないこともあります。 

|省略可能| Name | データ型 | 説明 |
|-------|------|-----------|-------------|
|禁止| `Input` | String | Face コンテナーでは、これは使用されません。|
|省略可能| `Output` | String | 出力マウントのターゲット。 既定値は `/output` です。 これはログの保存先です。 これには、コンテナーのログが含まれます。 <br><br>例:<br>`--mount type=bind,src=c:\output,target=/output`|

## <a name="hierarchical-settings"></a>階層的な設定

[!INCLUDE [Container shared configuration hierarchical settings](../../../includes/cognitive-services-containers-configuration-shared-hierarchical-settings.md)]

## <a name="example-docker-run-commands"></a>docker run コマンドの例 

以下の例では、`docker run` コマンドの記述方法と使用方法を示す構成設定が使用されています。  コンテナーは一度実行すると、お客様が[停止](face-how-to-install-containers.md#stop-the-container)するまで動作し続けます。

* **行連結文字**: 以降のセクションの Docker コマンドには、行連結文字としてバック スラッシュ (`\`) が使用されています。 お客様のホスト オペレーティング システムの要件に応じて、置換または削除してください。 
* **引数の順序**: Docker コンテナーについて高度な知識がある場合を除き、引数の順序は変更しないでください。

{_<引数名>_} はお客様独自の値に置き換えてください。

| プレースホルダー | 値 | 形式または例 |
|-------------|-------|---|
|{BILLING_KEY} | Face リソースのエンドポイント キー。 |xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx|
|{BILLING_ENDPOINT_URI} | リージョンを含む課金エンドポイントの値。|`https://westcentralus.api.cognitive.microsoft.com/face/v1.0`|

> [!IMPORTANT]
> コンテナーを実行するには、`Eula`、`Billing`、`ApiKey` の各オプションを指定する必要があります。そうしないと、コンテナーが起動しません。  詳細については、「[課金](face-how-to-install-containers.md#billing)」を参照してください。
> ApiKey の値は、[Azure Face Resource keys]\(Azure Face リソース キー\) ページからの**キー**です。 

## <a name="face-container-docker-examples"></a>Face コンテナーの Docker の例

次の Docker の例は、Face コンテナーに関するものです。 

### <a name="basic-example"></a>基本的な例 

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
  containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY} 
  ```

### <a name="logging-example-with-command-line-arguments"></a>コマンドライン引数を使用したログの例

  ```
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} ApiKey={BILLING_KEY} \
  Logging:Console:LogLevel=Information
  ```

### <a name="logging-example-with-environment-variable"></a>環境変数を使用したログの例

  ```
  SET Logging:Console:LogLevel=Information
  docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 containerpreview.azurecr.io/microsoft/cognitive-services-face \
  Eula=accept \
  Billing={BILLING_ENDPOINT_URI} \
  ApiKey={BILLING_KEY}
  ```

## <a name="next-steps"></a>次の手順

* [コンテナーのインストール方法と実行方法](face-how-to-install-containers.md)を確認する。
