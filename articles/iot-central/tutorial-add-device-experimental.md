---
title: Azure IoT Central アプリケーションに実デバイスを追加する | Microsoft Docs
description: オペレーターとして、Azure IoT Central アプリケーションに実デバイスを追加します。
author: sandeeppujar
ms.author: sandeepu
ms.date: 02/01/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 5933f74dcedb579023d187061229cdd53bce6414
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55819435"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>チュートリアル:Azure IoT Central アプリケーションに実デバイスを追加する

このチュートリアルでは、実デバイスを Microsoft Azure IoT Central アプリケーションに追加して構成する方法について説明します。

このチュートリアルは次の 2 つの部分で構成されています。

1. まず、オペレーター向けに、Azure IoT Central アプリケーションで実デバイスを追加して構成する方法について説明します。 この部分の最後では、2 つ目の部分で使用する接続文字列を取得します。
2. 次に、デバイス開発者向けに、実デバイスのコードについて説明します。 最初の部分で取得した接続文字列をサンプル コードに追加します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 新しい実デバイスの追加
> * 実デバイスの構成
> * アプリケーションからの実デバイスの接続文字列の取得
> * クライアント コードをアプリケーションにマッピングする方法について
> * 実デバイスのクライアント コードの構成

## <a name="prerequisites"></a>前提条件

始める前に、作成者が少なくとも Azure IoT Central アプリケーションを作成する最初の作成者向けチュートリアルを完了する必要があります。

* [新しいデバイスの種類を定義する](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) (必須)
* [デバイスのルールとアクションを構成する](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) (オプション)
* [オペレーターのビューをカスタマイズする](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) (オプション)

## <a name="add-a-real-device"></a>実デバイスの追加

実デバイスをアプリケーションに追加するには、「[新しいデバイスの種類を定義する](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)」チュートリアルで作成した **Connected Air Conditioner** デバイス テンプレートを使用します。

1. オペレーターが新しいデバイスを追加するには、左側のナビゲーション メニューで **[Device Explorer]** を選択します。

   ![Connected Air Conditioner が表示されている Device Explorer のページ](media/tutorial-add-device-experimental/explorer.png)

   **Device Explorer** には、**Connected Air Conditioner** デバイス テンプレートと、作成者がデバイス テンプレートを作成したときに自動的に作成されたシミュレートされたデバイスが表示されます。

2. コネクテッド空調機の実デバイスの接続を開始するには、**[新規]**、**[Real]\(実デバイス\)** を選択します。

   ![コネクテッド空調機の新しい実デバイスの追加の開始](media/tutorial-add-device-experimental/newreal.png)

3. デバイス ID (**小文字**) を入力するか、推奨デバイス ID を使います。新しいデバイスの名前を入力して、**[作成]** を選択することもできます。  

   ![デバイス名の変更](media/tutorial-add-device-experimental/rename.png)

## <a name="configure-a-real-device"></a>実デバイスの構成

実デバイスは、**Connected Air Conditioner** デバイス テンプレートから作成されます。 **[設定]** を使用して、デバイスを構成し、デバイスに関する情報を記録するプロパティ値を設定できます。

1. **[設定]** ページで、**[Set Temperature]** 設定の状態が**更新なし**になっていることに注目してください。 実デバイスがアプリケーションに接続し、設定に従って動作していることを確認するまで、この状態のままになります。 

    ![同期状態が表示されている [設定]](media/tutorial-add-device-experimental/settingssyncing.png)

2. 新しい実デバイス (コネクテッド空調機) の **[プロパティ]** ページには、そのデバイスの点検の場所と前回の点検日のプロパティがあります。どちらのプロパティも編集することができます。 シリアル番号フィールドとファームウェア バージョン フィールドは、デバイスがアプリケーションに接続されるまで空になります。 これらの値は読み取り専用です。デバイスから送信されるものであり、編集することはできません。

    ![実デバイスのプロパティ](media/tutorial-add-device-experimental/setproperties1.png)

3. 実デバイスの **[Measurements]\(測定\)**、**[ルール]**、**[ダッシュボード]** の各ページを確認できます。

## <a name="generate-connection-string-for-real-device-from-application"></a>アプリケーションからの実デバイスの接続文字列の生成

デバイス開発者は、実デバイスの "*接続文字列*" をデバイス上で実行されるコードに埋め込む必要があります。 接続文字列により、デバイスが Azure IoT Central アプリケーションに安全に接続できます。 接続文字列は、Node.js で記述されたクライアント コードを次の手順で準備する際に生成します。 Node.js アプリケーションは、コネクテッド空調機の実デバイスを表します。 

## <a name="prepare-the-client-code"></a>クライアント コードの準備

この記事のサンプル コードは [Node.js](https://nodejs.org/) で記述されており、次の処理を行うための十分なコードを示しています。

* Azure IoT Central アプリケーションにデバイスとして接続する。
* コネクテッド空調デバイスとして温度テレメトリを送信する。
* Azure IoT Central アプリケーションにデバイス プロパティを送信する。
* **Set Temperature** 設定を使用するオペレーターに応答する。
* Azure IoT Central アプリケーションからの Echo コマンドを処理する。

「[次の手順](#next-steps)」セクションに示されている "ハウツー" 記事では、より完全なサンプルを提供し、他のプログラミング言語の使用方法を説明しています。 Azure IoT Central へのデバイスの接続方法の詳細については、[デバイス接続](concepts-connectivity-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)に関する記事を参照してください。

以下の手順は、[Node.js](https://nodejs.org/) サンプルを準備する方法を示しています。

1. マシンに [Node.js](https://nodejs.org/) バージョン 4.0.x 以降をインストールします。 Node.js は、さまざまなオペレーティング システムで使用できます。

1. マシンに `connectedairconditioner` という名前のフォルダーを作成します。

1. コマンド ライン環境で、作成した `connectedairconditioner` フォルダーに移動します。

1. 次のコマンドを使用して DPS キー ジェネレーターをインストールします。

    ```cmd/sh
    npm i -g dps-keygen
    ```

   コマンド ライン ツールについて詳しくは、[こちら](https://www.npmjs.com/package/dps-keygen)をご覧ください。

1. アプリケーションにおけるデバイス インスタンスの接続文字列は、IoT Central によって提供されたデバイス情報から生成されます。

   IoT Central ポータルに戻ります。 コネクテッド空調機の実デバイスの [デバイス] 画面で、**[接続]** を選択します。

   ![接続情報の表示リンクが表示されている [デバイス] ページ](media/tutorial-add-device-experimental/connectionlink.png)

1. [デバイスの接続] ページで、スコープ ID、デバイス ID、プライマリ キーをテキスト エディターにコピーして貼り付け、保存します。 これらの値は、次の手順で使用します。

   ![接続の詳細](media/tutorial-add-device-experimental/device-connect.png)

1. コマンド ライン環境に戻り、次のコマンドを実行して接続文字列を生成します。

    ```cmd/sh
    dps_keygen <scope_id> <device_id> <Primary Key>
    ```

   出力結果をコピーし、新しいファイル (connection.txt など) に保存します。

1. Node.js プロジェクトを初期化するために、次のコマンドを実行し、既定値をすべてそのまま使用します。

    ```cmd/sh
    npm init
      ```

1. 必要なパッケージをインストールするために、次のコマンドを実行します。

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. テキスト エディターを使用して、`connectedairconditioner` フォルダーに **ConnectedAirConditioner.js** というファイルを作成します。

1. **ConnectedAirConditioner.js** ファイルの先頭に次の `require` ステートメントを追加します。

    ```javascript
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. このファイルに次の変数宣言を追加します。

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    > [!NOTE]
    > 後の手順で、プレースホルダー `{your device connection string}` を更新します。

1. これまでに行った変更を保存しますが、ファイルは開いたままにしておきます。

## <a name="understand-how-client-code-maps-to-the-application"></a>クライアント コードをアプリケーションにマッピングする方法について

前のセクションでは、Azure IoT Central アプリケーションに接続するアプリケーションのスケルトン Node.js プロジェクトを作成しました。 このセクションでは、次の処理を行うコードを追加します。

* Azure IoT Central アプリケーションに接続する。
* Azure IoT Central アプリケーションにテレメトリを送信する。
* Azure IoT Central アプリケーションにデバイス プロパティを送信する。
* Azure IoT Central アプリケーションから設定を受信する。
* Azure IoT Central アプリケーションからの Echo コマンドを処理する。

1. Azure IoT Central アプリケーションに温度テレメトリを送信するために、**ConnectedAirConditioner.js** ファイルに次のコードを追加します。

    ```javascript
    // Send device telemetry.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var data = JSON.stringify({ temperature: temperature });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    送信する JSON のフィールド名は、デバイス テンプレートの温度テレメトリに指定したフィールド名と一致する必要があります。 この例では、フィールド名は **temperature** です。

1. **firmwareVersion** や **serialNumber** などのデバイス プロパティを送信するには、次の定義を追加します。

    ```javascript
    // Send device properties
    function sendDeviceProperties(twin) {
      var properties = {
        firmwareVersion: "9.75",
        serialNumber: "10001"
      };
      twin.properties.reported.update(properties, (errorMessage) => 
      console.log(` * Sent device properties ` + (errorMessage ? `Error: ${errorMessage.toString()}` : `(success)`)));
    }
    ```

1. **setTemperature** など、デバイスがサポートする設定を定義するために、次の定義を追加します。

    ```javascript
    // Add any settings your device supports
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Azure IoT Central から送信された設定を処理するために、適切なデバイス コードを見つけて実行する次の関数を追加します。

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

    この関数では、次の処理を実行します。

    * Azure IoT Central が目的のプロパティを送信するのを監視する。
    * 適切な関数を見つけて、呼び出し、設定の変更を処理する。
    * Azure IoT Central アプリケーションに確認応答を返送する。

1. Azure IoT Central アプリケーションから **echo** などのコマンドに応答するには、次の定義を追加します。

    ```javascript
    // Respond to the echo command
    function onCommandEcho(request, response) {
      // Display console info
      console.log(' * Echo command received');
      // Respond
      response.send(10, 'Success', function (errorMessage) {});
    }
    ```

1. Azure IoT Central への接続を完了し、クライアント コードの関数をフックする次のコードを追加します。

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Setup device command callbacks
        client.onDeviceMethod('echo', onCommandEcho);
        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up
            sendDeviceProperties(twin);
            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    client.open(connectCallback);
    ```

1. これまでに行った変更を保存しますが、ファイルは開いたままにしておきます。

## <a name="configure-client-code-for-the-real-device"></a>実デバイスのクライアント コードの構成

<!-- Add the connection string to the sample code, build, and run --> Azure IoT Central アプリケーションに接続するようにクライアント コードを構成するには、このチュートリアルの前の方でメモした実デバイスの接続文字列を追加する必要があります。

1. **ConnectedAirConditioner.js** ファイルで、次のコード行を見つけます。

    ```javascript
    var connectionString = '{your device connection string}';
    ```

1. `{your device connection string}` を実デバイスの接続文字列に置き換えます。 接続文字列は、先ほどテキスト エディターで保存しました。

1. **ConnectedAirConditioner.js** ファイルの変更を保存します。

1. サンプルを実行するために、コマンド ライン環境で次のコマンドを入力します。

    ```cmd/sh
    node ConnectedAirConditioner.js
    ```

    > [!NOTE]
    > このコマンドを実行するときは、`connectedairconditioner` フォルダーにいることを確認してください。

1. アプリケーションからコンソールに次の出力が生成されます。

   ![クライアント アプリケーションの出力](media/tutorial-add-device-experimental/output.png)

1. 約 30 秒後に、デバイスの **[Measurements]\(測定\)** ページにテレメトリが表示されます。

   ![実際のテレメトリ](media/tutorial-add-device-experimental/realtelemetry.png)

1. **[設定]** ページでは、設定が同期されていることを確認できます。 デバイスは、初めて接続したときに、設定値を受け取り、変更を確認しました。

   ![設定の同期](media/tutorial-add-device-experimental/settingsynced.png)

1. **[設定]** ページで、デバイスの温度を **95** に設定し、**[Update device]\(デバイスの更新\)** を選択します。 サンプル アプリケーションがこの変更を受け取り、処理します。

   ![設定の受信と処理](media/tutorial-add-device-experimental/receivesetting.png)

   > [!NOTE]
   > "setting update" メッセージが 2 つあります。 1 つは `pending` 状態が送信されたとき、もう 1 つは `completed` 状態が送信されたときです。

1. **[Measurements]\(測定\)** ページでは、デバイスがより高い温度値を送信していることを確認できます。

    ![高くなっている温度テレメトリ](media/tutorial-add-device-experimental/highertemperature.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="nextstepaction"]
> * 新しい実デバイスの追加
> * 新しいデバイスの構成
> * アプリケーションからの実デバイスの接続文字列の取得
> * クライアント コードをアプリケーションにマッピングする方法について
> * 実デバイスのクライアント コードの構成

ここでは、実デバイスを Azure IoT Central アプリケーションに接続しました。推奨される次の手順は以下のとおりです。

オペレーターは、以下の方法を学ぶことができます。

* [デバイスの管理](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [デバイス セットを使用する](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [カスタム分析を作成する](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)

デバイス開発者は、以下の方法を学ぶことができます。

* [DevKit を準備して接続する](howto-connect-devkit-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Raspberry Pi を準備して接続する](howto-connect-raspberry-pi-python.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [汎用の Node.js クライアント を Azure IoT Central アプリケーションに接続する](howto-connect-nodejs-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [コードをカスタマイズする][lnk-nodejs-device-ref]


[lnk-nodejs-device-ref]: /javascript/api/azure-iot-device/?view=azure-iot-typescript-latest