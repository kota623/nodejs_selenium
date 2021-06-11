## e-typing from selenium

nodejs selenium の操作チュートリアル

```bash
インストール
npm install
実行コマンド
npm run test ./index.js
```

```json
./package.json
  "scripts": {
    "test": "mocha --timeout 10000" //タイムアウトを変更できる
  },

```

```js
//init
const { Browser, By, Key, until, WebDriver } = require('selenium-webdriver');
const { ignore, suite } = require('selenium-webdriver/testing');
```

使用する selenium のライブラリを変数に格納

```js
suite(function (env) {
  describe('Google Search', function () {
    let driver;
    before(async function () {
      //env.builder() は、事前設定された Builder インスタンスを返します
      //環境のターゲット ブラウザ (ブラウザ固有のブラウザを定義することもできます)
      //必要に応じてオプション (つまり、firefox.Options または chrome.Options))。
      driver = await env.builder().build();
    });

    ignore(env.browsers(Browser.CHROME)).it('demo', async function () {
      await driver.get('https://www.e-typing.ne.jp/roma/check/'); //ページを開く
      await driver.findElement(By.id('level_check_btn')).click(); //ボタンをクリック

      await driver.wait(
        until.elementLocated(function () {
          //until.elementLocated でページが開くのを待機して functionをtimeoutまで試行する。
          iframe = driver.findElement(By.id('typing_content')); //iframeにフォーカスを変えるために変数へ格納
          driver.switchTo().frame(iframe); //iframeにフォーカスを移動
          driver.findElement(By.id('start_btn')).click(); //iframe内のスタートボタンをクリック
          driver.switchTo().frame(driver); //フォーカスをiframeの外に変更

          var keyboard = driver.findElement(By.id('full_keyboard')).findElement(By.className('active')); //activeなキーを取得
          keyStr = keyboard.getText(); //取得したキーボードの文字列を格納

          //あとは文字列として入れるだけな気がする
          keyboard.sendKeys(Key.SPACE); //文字列ではないものを先に押しておく
          keyboard.sendKeys(keyStr); //取得した文字をキータイプする

          keyStr.then((keyboard) => {
            //デバッグ用プロミスで文字列をキャッチ
            console.log(keyboard);
          });
        }),
        300000,
      );

      after(() => driver && driver.quit());
    });
  });
});
```

> Selenium Document
>
> > https://www.selenium.dev/documentation/ja/selenium_installation/
