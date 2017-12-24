# clean-code-javascript

このリポジトリは個人資料の為、原文が必要な方は下記のリンクを参考にしてください。

clean code JavaScript(本家)  
https://github.com/ryanmcdermott/clean-code-javascript  

clean code JavaScript(日本語訳)  
https://mitsuruog.github.io/clean-code-javascript/  
https://github.com/mitsuruog/clean-code-javascript  


## 目次
  1. [はじめに](#introduction)
  2. [Variables](#variables)
  3. [Functions](#functions)
  4. [Concurrency](#Concurrency)
  5. [Error Handling](#error-handling)
  6. [Formatting](#formatting)
  7. [Comments](#comments)
  <!-- 8. [Translation](#translation) -->
  <!-- 4. [Objects and Data Structures](#objects-and-data-structures) -->
  <!-- 4. [Classes](#classes) -->
  <!-- 6. [SOLID](#solid) -->
  <!-- 7. [Testing](#testing) -->
## はじめに

<div>
<img style="" src="https://images-na.ssl-images-amazon.com/images/I/51bPR2V9fBL._SX404_BO1,204,203,200_.jpg" width="150px" />
</div>  

<div>
このJavaScriptコーディング・ガイドのリポジトリは
Clean Codeにヒントを得て、有志によってつくられた
JavaScriptに対するコーディング・ガイドになります。  
<a href="https://www.amazon.co.jp/dp/B001GSTOAM/ref=dp-kindle-redirect?_encoding=UTF8&amp;btkr=1" taget="_blank">Clean Code</a>は著者のRobert C. Martinらが長年の経験をもとにまとめたものです。
こちらの本は言語は限定されていません。  
  
キレイなコードとは3R(Readable、Reusable、Refactorable)になっているものです。
- Readable　読みやすい
- Reusable 再利用可能
- Refactorable 理解や修正がしやすいよう

全てのコードは、粘土が徐々に形作られていくように、曖昧な形から始まるものです。
その後でコードレビューを行い、不要なコードを削除して作りあげていくものだと書かれています。

<img src="https://github.com/misak1/clean-code-javascript/blob/master/point-50.png?raw=true" width="20"/> JavaScriptは良くも悪くも、非常に自由に書ける言語です。  
その為、個人の技量に応じてコードが複雑になったりしやすいので、  
改めてみてみると面白い発見がありましたので個人の見解も含めて紹介したいと思います。  

### 変更内容
・サンプルコードについて一部わかりにくいものについては変更しました。  
・業務で使うのがES6ということで、ES5関連の過去の記述方法については省略しました。  
・ES8や策定中の機能については省略しました。  
・デザインパターンやアルゴリズムについては省略しました。
</div>




## **Variables**
> 利用範囲の広い変数には意味のある名前を利用しましょう  
> 例えばconst（定数）として使う場合にこれが該当します。

**Bad:**
```javascript
const yyyymmdstr = getDate().format('YYYY/MM/DD');
```

**Good:**
```javascript
const currentDate = getDate().format('YYYY/MM/DD');
```


### 同じ処理をするものには同じ名前を利用すること

**Bad:**
```javascript
class User(){
  getUserInfo();
}
class Client(){
  getClientData();
}
class Customer(){
  getCustomerRecord();
}
```

**Good:**
```javascript
class User(){
  getUser();
}
class Client(){
  getUser();
}
class Customer(){
  getUser();
}
```



### 検索しやすい名前をつける
> マジックナンバーは他の人でも理解出来るように一度変数に代入しましょう。

**Bad:**
```javascript
setTimeout(blastOff, 86400000);
```

**Good:**
```javascript
const MILLISECONDS_IN_A_DAY = 86400000;
setTimeout(blastOff, MILLISECONDS_IN_A_DAY);
```


### コード説明できるように変数に代入する
> 説明的な変数を利用しましょう

**Bad:**
```javascript
let revision = '20171218164030';
let f = revision.match(/^(\d{4})(\d{2})(\d{2})(\d{2})(\d{2})(\d{2})/);
console.info(new Date(f[1], f[2], f[3], f[4], f[5], f[6])); // Thu Jan 18 2018 16:40:30 GMT+0900 (JST)
```

**Good:**
```javascript
let revision = '20171218164030';
let [, year, month, day, hour, minute, second] = revision.match(/^(\d{4})(\d{2})(\d{2})(\d{2})(\d{2})(\d{2})/);
console.info(new Date(year, month, day, hour, minute, second)); // Thu Jan 18 2018 16:40:30 GMT+0900 (JST)
```
<img src="https://github.com/misak1/clean-code-javascript/blob/master/point-50.png?raw=true" width="20"/> match戻り値[0]を切り捨てる為に0番目を宣言しないことで捨てています。(後述の分割代入構文が使われています)


<!-- ### メンタルマップを避ける
> 明らかなことは暗黙的なことよりも優れています。
> 訳注：メンタルマップとは、認知心理学において記憶の中に構成される「あるべき姿」のイメージをさす言葉です。

**Bad:**
```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((l) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // Wait, what is `l` for again?
  // ちょっと待って、もう一度`l`ってなんだっけ？
  dispatch(l);
});
```

**Good:**
```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((location) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});
```

<img src="https://github.com/misak1/clean-code-javascript/blob/master/point-50.png?raw=true" width="20"/> 要するに他人が見た時に不安を煽るだけのコメントは消しましょう。  
forEachでループするものがlocationとしておけば迷わないよ!ということ -->

### 不必要なコンテキストを加えない
> もしクラスやオブジェクト名が何かを伝えているのであれば、変数名でそのことを繰り返してはいけません。

**Bad:**
```javascript
const Car = {
  carMake: 'Honda',
  carModel: 'Accord',
  carColor: 'Blue'
};

function paintCar(car) {
  car.carColor = 'Red';
}
```

**Good:**
```javascript
const Car = {
  make: 'Honda',
  model: 'Accord',
  color: 'Blue'
};

function paintCar(car) {
  car.color = 'Red';
}
```


### 短絡評価や条件の代わりにデフォルト引数を利用すること

> デフォルト引数は多くの場合、短絡評価よりも明確です。
> ご存知の通り、これらを使った場合、関数は`undefined`の引数のみにデフォルト値を提供します。
> 他の`''`、`""`、`false`、`null`、`0`や`NaN`のような"falsy"値は、デフォルト値で置き換わることはありません。

**Bad:**
```javascript
function createMicrobrewery(name) {
  const breweryName = name || 'Hipster Brew Co.';
  // ...
}

```

**Good:**
```javascript
function createMicrobrewery(breweryName = 'Hipster Brew Co.') {
  // ...
}

```

<img src="https://github.com/misak1/clean-code-javascript/blob/master/point-50.png?raw=true" width="20"/> ES6になってからデフォルト引数が使えるようになっているので使っていきましょう。  
ただ、IEとかモバイルサファリで使えない場合があるので、生のjsを扱う場合は注意です。

## **Functions**
### 関数の引数(2つ以下が理想的)

関数の引数の数を制限することは、テストを簡単に行えるという点において非常に重要なことです。  
3つ以上あるということは、それぞれ別の引数を持ったケースのテストしなければならないという問題につながります。（テストケースの増大化）  
  
JavaScriptは、多くのクラスの雛形がなくとも素早くオブジェクトを作成することができるため、  
もし多くの引数を必要としているとわかった場合は、オブジェクトを使うことで解決できます。  
  
これを解決するのがES2015/ES6のdestructuring assignment(分割代入構文)です。  

**Bad:**
```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}
```

**Good:**
```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
});
```

### 関数は1つのことを行うこと

これはとても重要なルールです。  
関数をただ1つのことをやるように分離できた場合、それらを簡単にリファクタリングしたり、コードをかなりしっかりと読むことができます。  

**Bad:**
```javascript
function emailClients(clients) {
  clients.forEach((client) => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Good:**
```javascript
function emailActiveClients(clients) {
  clients
    .filter(isActiveClient)
    .forEach(email);
}

function isActiveClient(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```


### 関数名は何をするかを表すこと

**Bad:**
```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// It's hard to tell from the function name what is added
// 関数名からは何が追加されたのかがわかりにくい
addToDate(date, 1);
```

**Good:**
```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```

### 重複したコードを削除すること

> 重複したコードを避けるために絶対にベストを尽くしてください。  
> 重複したコードは、もし何かのロジックを変更しようとした場合、何か変更する場所が1つ以上あるという意味で悪です。

**Bad:**
```javascript
function showDeveloperList(developers) {
  developers.forEach((developer) => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach((manager) => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

**Good:**
```javascript
function showEmployeeList(employees) {
  employees.forEach((employee) => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    let portfolio;
    switch (employee.type) {
      case 'manager':
        portfolio = employee.getMBAProjects();
        break;
      case 'developer':
        portfolio = employee.getGithubLink();
        break;
    }

    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

<img src="https://github.com/misak1/clean-code-javascript/blob/master/point-50.png?raw=true" width="20"/> サンプルはリファクタリングとしてはいい例なのですが少々わかりにくいですね。  
最悪なのはコピペされたコードが複数箇所に点在していること。

### Object.assignでデフォルトオブジェクトを設定すること

**Bad:**
```javascript
const menuConfig = {
  title: null,
  body: 'Bar',
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || 'Foo';
  config.body = config.body || 'Bar';
  config.buttonText = config.buttonText || 'Baz';
  config.cancellable = config.cancellable !== undefined ? config.cancellable : true;
}

createMenu(menuConfig);
```

**Good:**
```javascript
const menuConfig = {
  title: 'Order',
  // User did not include 'body' key
  // ユーザーは `body` キーを含めなくていい
  buttonText: 'Send',
  cancellable: true
};

function createMenu(config) {
  config = Object.assign({
    title: 'Foo',
    body: 'Bar',
    buttonText: 'Baz',
    cancellable: true
  }, config);

  // config now equals: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // configはこれと同じになります
  // ...
}

createMenu(menuConfig);
```

<img src="https://github.com/misak1/clean-code-javascript/blob/master/point-50.png?raw=true" width="20"/> 似ているObject.assignと分割代入構文  
Object.assignはオブジェクトのテンプレートを定義することに対し  
分割代入構文はその場でオブジェクトを作ること  

### フラグを関数の引数のように利用しない

> フラグは、この関数が複数のことを行うことを利用者に伝えます。  
> 関数は1つのことを行うべきです。関数が真偽値によって異なるコードの経路を経由する場合、その関数を分割してください。  

**Bad:**
```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Good:**
```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```

<img src="https://github.com/misak1/clean-code-javascript/blob/master/point-50.png?raw=true" width="20"/> これはケースバイケースかと思います。


### グローバル関数に書き込まない

> グローバルを汚染することはJavaScriptにおけるバッドプラクティスです。
> なぜなら、他のライブラリをクラッシュさせるかもしれないし、あなたのAPIを使っているユーザーは、プロダクション環境で例外を受け取るまで、そのことについて何もわからないからです。

**Bad:**
```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};
```

**Good:**
```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
```

<img src="https://github.com/misak1/clean-code-javascript/blob/master/point-50.png?raw=true" width="20"/> どうしてもグローバルスコープを使う場合は、ビルトイン関数を上書きすることは避けるのと、また他と被らない名前で展開しましょう。（後述のビルトイン関数の上書きを使った利用例）

### Avoid negative conditionals
### 否定的な条件を避ける

**Bad:**
```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // なんらかの処理
}
```

**Good:**
```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // 何もしない
}else{
  // なんらかの処理
}
```

<img src="https://github.com/misak1/clean-code-javascript/blob/master/point-50.png?raw=true" width="20"/> 裏の裏は表みたいなややこしいことはしないで、基本を肯定で作りましょう  
何もしないブロックがあってもいいと思う（後述のコメント削除）


### 行き過ぎた最適化をしない

> モダンブラウザは、ランタイムの中で多くの最適化を行います。
> 何度も最適化を行なっているのであれば、それは時間の無駄です。[ここにどこで最適化が不足するかをみるための良い資料](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)があります。
> 可能であれば、それらが修正されるまでは、それらだけを最適化の対象としてください。

**Bad:**
```javascript
// 古いブラウザにおいては、キャッシュされていない`list.length`はコストが掛かる
// なぜなら、`list.length`が再計算されるから。しかし、モダンプラウザでは最適化されている
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Good:**
```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```
<img src="https://github.com/misak1/clean-code-javascript/blob/master/point-50.png?raw=true" width="20"/> c++は前置インクリメント(++i)にしたほうが良いとのこともありますがそちらも不要です。

### for文の利用について

基本構文
```javascript
for(初期化; ループの継続条件; カウンタ変数の更新){
    処理内容
}
```

<img src="https://github.com/misak1/clean-code-javascript/blob/master/point-50.png?raw=true" width="20"/> そもそもこのfor文を使う必要があるのか検討しましょう。
反復処理ならビルトイン関数で賄えるはずです。（array.map、array.filter、array.forEach）またはfor-in、for-of文。  
   
ケース１、continue、breakが必要な場合、  
ケース２、カウンタ変数を細かく制御したい場合  

```javascript
let t1 = [];
for(let i = 0; i < 20; i += 2){
  t1.push(i);
}
console.log(t1.join(',')); // 0,2,4,6,8,10,12,14,16,18

let t2 = [];
for(let i = 1, x = 2, v = 21; x < v; i++, x = i * 3 - 1){
    t2.push(x);
}
console.log(t2.join(',')); // 2,5,8,11,14,17,20
```

### 使っていないコードを削除する
> 使っていないコードは重複したコードと同じくらい悪いだけのものです。
> コードがバージョン管理されていれば、復元するのも簡単です。

**Bad:**
```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');

```

**Good:**
```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');
```


### オブジェクトはプライベートなメンバーを持つようにする
This can be accomplished through closures (for ES5 and below).

これはクロージャによって達成することができます。（ES5以前の場合）

**Bad:**
```javascript

const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**Good:**
```javascript
function makeEmployee(name) {
  return {
    getName() {
      return name;
    },
  };
}

const employee = makeEmployee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```


### メソッドチェーンを利用すること

これは、JavaScriptの中で非常に有用なテクニックで、jQueryやLodashのような多くのライブラリの中でみることができます。
クラスの関数の中の全ての関数の終わりで、単純に`this`を返すことで、クラスの中にあるメソッドをチェーンすることができます。  

**Bad:**
```javascript
class Car {
  constructor() {
    this.make = 'Honda';
    this.model = 'Accord';
    this.color = 'white';
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car();
car.setColor('pink');
car.setMake('Ford');
car.setModel('F-150');
car.save();
```

**Good:**
```javascript
class Car {
  constructor() {
    this.make = 'Honda';
    this.model = 'Accord';
    this.color = 'white';
  }

  setMake(make) {
    this.make = make;
    return this;
  }

  setModel(model) {
    this.model = model;
    return this;
  }

  setColor(color) {
    this.color = color;
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    return this;
  }
}

const car = new Car()
  .setColor('pink')
  .setMake('Ford')
  .setModel('F-150')
  .save();
```

## **Concurrency**
## **同期処理**
### コールバックではなく、Promiseを使う

コールバックは簡潔ではありません、そしてそれらは過剰な量のネストを引き起こします。  
ES2015/ES6ではPromiseがグローバルに組み込まれています。それらを使いましょう！  

**Bad:**
```javascript
import { get } from 'request';
import { writeFile } from 'fs';

get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin', (requestErr, response) => {
  if (requestErr) {
    console.error(requestErr);
  } else {
    writeFile('article.html', response.body, (writeErr) => {
      if (writeErr) {
        console.error(writeErr);
      } else {
        console.log('File written');
      }
    });
  }
});

```

**Good:**
```javascript
import { get } from 'request';
import { writeFile } from 'fs';

get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
  .then((response) => {
    return writeFile('article.html', response);
  })
  .then(() => {
    console.log('File written');
  })
  .catch((err) => {
    console.error(err);
  });

```


## **Error Handling**

例外が発生することは良いことです！この意味は、ランタイムがあなたのプログラムが何かおかしいことを正常に突き止めたということです。  
それは、関数の実行を直近のスタックで停止し、そのプロセスを停止し(ノード中)、コンソールのスタックトレースを通じてあなたに知らせてくれます。  

### 例外が捕らえられたことを無視しない

例外を捕捉して何もしないということは、あなたがそのエラーを修正したり、エラーが言ったことに対応したりすることができません。  
コンソール(`console.log`)にエラーを出力することは、頻繁にコンソール出力された海に埋もれてしまうため、それほど良いことではありません。  
コードの一部を `try/catch` で囲うということは、そこでエラー発生するかもしれないということを意味します。  
したがって、エラーが発生した時のために、なにかの対策をしたり、コードの行き先を作らなければなりません。  

**Bad:**
```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Good:**
```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
}
```

### 失敗したpromiseを無視しない

同じ理由により、`try/catch`にて発生した例外を無視するべきではありません。

**Bad:**
```javascript
getdata()
  .then((data) => {
    functionThatMightThrow(data);
  })
  .catch((error) => {
    console.log(error);
  });
```

**Good:**
```javascript
getdata()
  .then((data) => {
    functionThatMightThrow(data);
  })
  .catch((error) => {
    // One option (more noisy than console.log):
    console.error(error);
    // Another option:
    notifyUserOfError(error);
    // Another option:
    reportErrorToService(error);
    // OR do all three!
  });
```


## **Formatting**

組織単位でルールを作り、自動化ツールを１つ導入しましょう。  
フォーマットについて議論することは、時間の無駄です。  
理由は後述するugalify,minify化を参照  

### 一貫性を持った大文字を利用すること

JavaScriptには型がありません。そのため大文字は変数や関数などについて多くのことを教えてくれます。
ここでのポイントは、組織あるいはあなたが決めたルールを守ることです。

**Bad:**
```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const Artists = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Good:**
```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const artists = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```

この例のルール
定数はUPPERCASE + SNAKECASE
変数はCAMELCASE
songs、artistsがSMALLCASE？にする


### Function callers and callees should be close
### 関数の呼び出し元と呼び出し先は近くにあること

関数は他を呼び出す場合、それらをソースコードのなかの垂直方向で近くにおくようにしてください。
理想的には、呼び出し元を呼び出し先の上においてください。私たちは新聞のように、コードを上から下に読む傾向があります。
このため、あなたのコードをこのように読ませるようにしてください。

**Bad:**
```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, 'peers');
  }

  lookupManager() {
    return db.lookup(this.employee, 'manager');
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**Good:**
```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, 'peers');
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, 'manager');
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```



## **Comments**

### ビジネスロジックが複雑なものにのみコメントすること
Comments are an apology, not a requirement. Good code *mostly* documents itself.

コメントは弁明です、必須ではありません。良いコードは *ほとんどが* ドキュメントそのものです。

**Bad:**
```javascript
function hashIt(data) {
  // The hash
  let hash = 0;

  // Length of string
  const length = data.length;

  // Loop through every character in data
  for (let i = 0; i < length; i++) {
    // Get character code.
    const char = data.charCodeAt(i);
    // Make the hash
    hash = ((hash << 5) - hash) + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**Good:**
```javascript

function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = ((hash << 5) - hash) + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}

```


### コメントアウトしたコードをコードベースに残さない

バージョン管理があることがその理由です。古いコードは履歴に残しましょう。

**Bad:**
```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Good:**
```javascript
doStuff();
```


### 日記のようなコメントは持たない
特に日記の様なコメント。
履歴を取得するためには `git log` を使ってください！

**Bad:**
```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**Good:**
```javascript
function combine(a, b) {
  return a + b;
}
```


### JavaScriptで書いたコードを公開する際に気をつけること

JavaScriptはブラウザでそのまま実行されるので、書いたコードは丸見えになっています。  
その為、公開前にはminify(圧縮)ツールやuglify(難読化)ツールを利用しましょう。  
この際に、ホワイトスペースやコメントも一緒に削除されるので。  
ソースコードのコメントを必死になって整理しなくても良くなるというメリットもあります。  

<img src="https://github.com/misak1/clean-code-javascript/blob/master/uglify-min.png?raw=true">

### 後述のビルトイン関数の上書きを使った利用例

公開時にconsole.logが残っていると良くないことが多いですね。  
console.logからコードの処理が追われ、悪用されたり、
ログに書き出しているとよろしくないコードが出ていることもあります。 

ビルトイン関数を上書きすることでconsole.logを止める方法  
応急処置として使われる方法

console.logの無効化
```javascript
console.log = function(){};
```

無効化されたconsole.logの復活
```javascript
var f = document.body.appendChild(document.createElement('iframe'));
f.style.display = 'none';
window.console = f.contentWindow.console;
```




