# clean-code-javascript

このリポジトリは個人資料の為、原文が必要な方は下記のリンクを参考にしてください。

clean code JavaScript(本家)  
https://github.com/mitsuruog/clean-code-javascript 

clean code JavaScript(日本語訳)  
https://mitsuruog.github.io/clean-code-javascript/  
https://github.com/misak1/clean-code-javascript  

## 目次
  1. [はじめに](#introduction)
  2. [Variables](#variables)
  3. [Functions](#functions)
  4. [Concurrency](#concurrency)
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
<img style="float:right; margin:0 0 10px 10px" src="https://images-na.ssl-images-amazon.com/images/I/51bPR2V9fBL._SX404_BO1,204,203,200_.jpg" width="150px" >
<div>
このJavaScriptコーディング・ガイドのリポジトリは
Clean Codeにヒントを得て、有志によってつくられた
JavaScriptに対するコーディング・ガイドになります。  
<a href="https://www.amazon.co.jp/dp/B001GSTOAM/ref=dp-kindle-redirect?_encoding=UTF8&amp;btkr=1" taget="_blank">Clean Code</a>は著者のRobert C. Martinらが長年の経験をもとにまとめたものです。
こちらの本は言語は限定されていません。

キレイなコードは3R(Readable、Reusable、Refactorable)になっていることを指し
読みやすい、再利用可能、理解や修正がしやすいように整理されている
これらを目指すためのガイドになります。

全てのコードは、粘土が徐々に形作られていくように、曖昧な形から始まるものです。
その後でコードレビューを行い、不要なコードを削除して作りあげていくものです。
とも語られているそうです。

JavaScriptは良くも悪くも、非常に自由に書ける言語です。
その為、個人の技量に応じてコードが複雑になったりしやすいので、
改めてみてみると面白い発見がありましたので個人の見解も含めて紹介したいと思います。

### 変更内容
・サンプルコードについて一部わかりにくいものについては差し替えました。  
・業務で使うのがES6ということで、ES5関連の過去の記述方法については削除  
・ES8や策定中の機能については削除  
・デザインパターンについては話がややこしくなるので省略します。
</div>
</div>



## **Variables**
> 利用範囲の広い変数には意味のある名前を利用しましょう
例にconst（定数）として使う場合にこれが該当します。

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
match戻り値[0]を切り捨てる為に0番目を宣言しないことで捨てている(後述の分割代入構文)



### Avoid Mental Mapping
### メンタルマップを避ける
明らかなことは暗黙的なことよりも優れています。

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
要するに他人に見せてもしょうがないコメントは不安を煽るだけだから残すなということ!?
forEachでまわすものをlocationとしておけば問題ないのに...




### Don't add unneeded context
### 不必要なコンテキストを加えない

もしクラスやオブジェクト名が何かを伝えているのであれば、変数名でそのことを繰り返してはいけません。

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


### Use default arguments instead of short circuiting or conditionals
### 短絡評価や条件の代わりにデフォルト引数を利用すること

デフォルト引数は多くの場合、短絡評価よりも明確です。
ご存知の通り、これらを使った場合、関数は`undefined`の引数のみにデフォルト値を提供します。
他の`''`、`""`、`false`、`null`、`0`や`NaN`のような"falsy"値は、デフォルト値で置き換わることはありません。

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

ES6になってからデフォルト引数が使えるようになっているので、使いましょう。
IEとかモバイルサファリで使えない場合があるので、生のjsを扱う場合は注意です。



## **Functions**
### Function arguments (2 or fewer ideally)
### 関数の引数(2つ以下が理想的)

関数の引数の数を制限することは、テストを簡単に行えるという点において非常に重要なことです。
3つ以上あるということは、それぞれ別の引数を伴った数多くの異なるケースをテストしなければならないという問題につながります。

1つか2つの場合は理想的で、3つ以上になる場合は次の手法を検討してください。
ほとんどの場合、上位のオブジェクトを引数とすれば十分でしょう。

JavaScriptは、多くのクラスの雛形がなくとも素早くオブジェクトを作成することができるため、もし多くの引数を必要としているとわかった場合は、オブジェクトを使うことができます。

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



### Functions should do one thing
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


### Function names should say what they do
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


### Remove duplicate code
### 重複したコードを削除すること

重複したコードを避けるために絶対にベストを尽くしてください。
重複したコードは、もし何かのロジックを変更しようとした場合、何か変更する場所が1つ以上あるという意味で悪です。

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

サンプルはリファクタリングとしてはいい例なの、
最悪なのはコピペコードが複数箇所に点在していることです。



### Set default objects with Object.assign
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

似ているObject.assignと分割代入構文
Object.assignはオブジェクトのテンプレートを定義すること、
分割代入構文はその場でオブジェクトを作ること




### Don't use flags as function parameters
### フラグを関数の引数のように利用しない

フラグは、この関数が複数のことを行うことを利用者に伝えます。
関数は1つのことを行うべきです。関数が真偽値によって異なるコードの経路を経由する場合、その関数を分割してください。

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

ケースバイケースかと思います。



### Don't write to global functions
### グローバル関数に書き込まない

グローバルを汚染することはJavaScriptにおけるバッドプラクティスです。
なぜなら、他のライブラリをクラッシュさせるかもしれないし、あなたのAPIを使っているユーザーは、プロダクション環境で例外を受け取るまで、そのことについて何もわからないからです。

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

どうしてもグローバルスコープを使う場合は、ビルトイン関数を上書きすることは避けるのと、また他と被らない名前で展開しましょう。（後述のビルトイン関数の上書きを使った利用例）



<!-- ### Favor functional programming over imperative programming
### 手続き型プログラミングより関数型プログラミングを優先する

JavaScriptは、Haskellがやっているような関数型言語ではありませんが、部分的にその機能があります。
関数型言語は簡潔で用意にテストすることができます。あなたができる時は、このプログラミングスタイルを優先してください。

**Bad:**
```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Good:**
```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

const INITIAL_VALUE = 0;

const totalOutput = programmerOutput
  .map((programmer) => programmer.linesOfCode)
  .reduce((acc, linesOfCode) => acc + linesOfCode, INITIAL_VALUE);
```


### Encapsulate conditionals
### 条件をカプセル化する

**Bad:**
```javascript
if (fsm.state === 'fetching' && isEmpty(listNode)) {
  // ...
}
```

**Good:**
```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === 'fetching' && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```
 -->

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

裏の裏は表みたいなややこしいことはしないで、基本を肯定で作る
何もしないブロックがあってもいいと思う（後述のコメント削除）



<!-- ### Avoid conditionals
### 条件文を避ける

これは一見不可能なタスクのように見えます。
最初にこれを聞いて、ほとんどの人はこう言います。「`if`文なしで、何をするの？」
この答えは、多くの場合、同じタスクを実行するためにポリモーフィズム(多様性)を使ってできるよ。ということです。
2つめの質問は大抵これです。「うーん、いいと思うんだけど、なぜそれをやりたいんだろう。。。」
この答えは、私たちが先に学んだクリーンなコードコンセプト、「関数はただ1つのことを行うべき」です。
あなたのクラスや関数が`if`文を持っているとき、この関数は1つ以上のことを行なっていることを示唆しています。
たった1つのことをやるということを覚えておいてください。

**Bad:**
```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case '777':
        return this.getMaxAltitude() - this.getPassengerCount();
      case 'Air Force One':
        return this.getMaxAltitude();
      case 'Cessna':
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**Good:**
```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```
 -->
<!-- 
### Avoid type-checking (part 1)
### 型チェックを避ける (part 1)


JavaScriptには型がありません、このことは、関数がどんな型の引数でも受け取ることができることを意味します。
ときにはこの自由に夢中になって、関数の中でタイプチェックをするような誘惑に駆られるようになります。
これをやらなければならないときに、これを避ける方法はたくさんあります。まず、最初に考えるべきことは、一貫性のあるAPIです。

**Bad:**
```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location('texas'));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location('texas'));
  }
}
```

**Good:**
```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location('texas'));
}
```


### Avoid type-checking (part 2)
### 型チェックを避ける (part 2)

もし、あなたが文字列、数値、配列のような基本的なプリミティブな値を扱っていて、ポリモーフィズムを使えない、しかしまだタイプチェックが必要だと感じている場合。
TypeScriptの利用を検討してみてください。
これは標準のJavaScript構文の上に静的な型を提供するので、通常のJavaScriptに変わる優れた代替品です。
通常のJavaScript手作業のタイプチェックの問題は、偽物の型安全を得るために、あまりにも多くの余計な構文が必要なことです。これらは失った可読性を補うようなものではありません。
JavaScriptをクリーンに保ち、良いテストを書き。良いコードレビューを行なってください。
それ以外の場合は、それらの全てをTypeScriptで行います。(私が言ったように、それは素晴らしい代替え品です！)

**Bad:**
```javascript
function combine(val1, val2) {
  if (typeof val1 === 'number' && typeof val2 === 'number' ||
      typeof val1 === 'string' && typeof val2 === 'string') {
    return val1 + val2;
  }

  throw new Error('Must be of type String or Number');
}
```

**Good:**
```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```
 -->

### Don't over-optimize
### 行き過ぎた最適化をしない

モダンブラウザは、ランタイムの中で多くの最適化を行います。
何度も最適化を行なっているのであれば、それは時間の無駄です。[ここにどこで最適化が不足するかをみるための良い資料](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)があります。
可能であれば、それらが修正されるまでは、それらだけを最適化の対象としてください。

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
c++は前置インクリメント(++i)にしたほうが良いとのこともありますがそちらも不要です。

### for文について

基本構文
```javascript
for(初期化; ループの継続条件; カウンタ変数の更新){
    処理内容
}
```

そもそもこのfor文を使う必要があるのか検討しましょう。
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





### Remove dead code
### 使っていないコードを削除する
使っていないコードは重複したコードと同じくらい悪いだけのものです。
コードがバージョン管理されていれば、復元するのも簡単です。

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

<!-- 
## **Objects and Data Structures**
## **オブジェクとデータ構造**

### Use getters and setters
### gettersとsettersを使うこと


gettersとsettersを使ってオブジェクト上のデータにアクセスする方が、単純にオブジェクトのプロパティをみるよりも良くなります。
「なぜ？」と尋ねるかもしれない。これはあまりまとまっていないものですが、その理由のリストです。

* もし、オブジェクトのプロパティを取得する以上のことをやろうとした場合、コードベースの全てのアクセッサを検索して変更して回る必要がない。
* 単純に`set`を行う時に、バリデーションを追加することができる。
* 内部をカプセル化する。
* 取得や設定の時に、簡単にロギングやエラーハンドリングを追加できる。
* オブジェクトのプロパティを遅延評価することができる、これをその値をサーバーから取得すると言おう。

**Bad:**
```javascript
function makeBankAccount() {
  // ...

  return {
    balance: 0,
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;
```

**Good:**
```javascript
function makeBankAccount() {
  // this one is private
  // これはprivate
  let balance = 0;

  // a "getter", made public via the returned object below
  // getter、以下でオブジェクトを返すことでpublicにします
  function getBalance() {
    return balance;
  }

  // a "setter", made public via the returned object below
  // setter、パラメータオブジェクト受け取ることでpublicにします(なんか原文が違う。。。)
  function setBalance(amount) {
    // ... validate before updating the balance
    // ... balanceを更新するまえにバリデーションを行う
    balance = amount;
  }

  return {
    // ...
    getBalance,
    setBalance,
  };
}

const account = makeBankAccount();
account.setBalance(100);
```
 -->


### Make objects have private members
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


<!-- 
## **Classes**
### Prefer ES2015/ES6 classes over ES5 plain functions
### ES5の関数よりもES2015/ES6のクラスの方を好むこと

古典的なES5クラスでは、可読性の良いクラス継承、構築、メソッド定義を行うことが難しいです。
もし、継承が必要な場合（そう認識していないかもしれない）、ES2015/ES6クラスを優先してください。
しかしならがら、巨大で複雑なオブジェクトが必要だとわかるまでは、クラスよりも小さな関数を優先してください。

**Bad:**
```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error('Instantiate Animal with `new`');
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error('Instantiate Mammal with `new`');
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error('Instantiate Human with `new`');
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**Good:**
```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() { /* ... */ }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() { /* ... */ }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() { /* ... */ }
}
```
 -->


### Use method chaining
### メソッドチェーンを利用すること

これは、JavaScriptの中で非常に有用なパターンで、jQueryやLodashのような多くのライブラリの中でみることができます。
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


<!-- ### Prefer composition over inheritance
### 継承よりコンポジション（組み合わせ）を好む

有名なGang of Four(四人組)による[*デザインパターン*](https://en.wikipedia.org/wiki/Design_Patterns)のように、
可能な場所では継承よりもコンポジションを優先するべきです。
継承を利用する良い理由もコンポジションを利用する良い理由もたくさんあります。
この章の要点は、もしあなたが本能的に継承を使うように心が動くのであれば、コンポジションがその問題をよりよくモデル化できるかどうか考えてみてください。
いくつかの場合、それができます。

You might be wondering then, "when should I use inheritance?" It
depends on your problem at hand, but this is a decent list of when inheritance
makes more sense than composition:

1. Your inheritance represents an "is-a" relationship and not a "has-a"
relationship (Human->Animal vs. User->UserDetails).
2. You can reuse code from the base classes (Humans can move like all animals).
3. You want to make global changes to derived classes by changing a base class.
(Change the caloric expenditure of all animals when they move).

あなたは、「いつ継承を使うべきか？」について疑問に思うかもしれません。
それは、手元にある問題に依存します。しかし、これらは継承がコンポジションよりも意味をなす場合のまともなリストです。

1. 継承が「had-a(訳注：「A had a B」で「AはBを含んでいる」の意味)」ではなくて「is-a(訳注：「A is a B」で「AはBである」の意味)」を表している場合(Human->Animal(人間は動物である) と User->UserDetails(人は属性情報を含んでいる))
2. 基底クラスからコードを再利用できる(人は動物のように動くことができる)
3. 基底クラスを変更することで、派生クラスを全体的に変更したい(全ての動物の移動中の消費カロリーを変更する)

**Bad:**
```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee
// よくない。なぜなら、従業員(Employee)は税情報を持っている。しかし、従業員税情報(EmployeeTaxData)は従業員ではない
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Good:**
```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}
```
 -->

<!-- ## **SOLID**
### Single Responsibility Principle (SRP)
### 単一責任の原則(SRP)

クリーンコードに記載されているように、「クラスが変更される理由は、1つ以上あってはならない」。
多くの機能を詰め込んだ超満員のクラスは、フライトで1つのスーツケースしか持てない場合などは魅力的に見えます。
これに関する問題は、そのクラスが概念的に一貫性が乏しく、さまざなな理由により変更ことが多くあるからです。
クラスを変更することに掛かる時間が最小限にすることが重要です。
それが重要なのは、あまりにも多くの機能が1つのクラスにあって、その中のひとまとまりを変更する場合、コードベースの中の他の依存しているモジュールに対してどのように影響を与えるか理解することが難しいことになるからです。

**Bad:**
```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Good:**
```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}


class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```
 -->

<!-- ### Open/Closed Principle (OCP)
### オープン・クローズドの原則(OCP)

Bertrand Meyer(バートランド・メイヤー)が記したように、「ソフトウエアの構成要素(クラス、モジュール、関数など)は、拡張に対してオープンで、変更に対してはクローズであるべき」
それはどう言う意味でしょうか？
この原則は基本的に、ユーザーに対して既存のコードを変更することなく、新しい機能を加えることができるようにするべきと記されている。

**Bad:**
```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'ajaxAdapter';
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'nodeAdapter';
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === 'ajaxAdapter') {
      return makeAjaxCall(url).then((response) => {
        // transform response and return
      });
    } else if (this.adapter.name === 'httpNodeAdapter') {
      return makeHttpCall(url).then((response) => {
        // transform response and return
      });
    }
  }
}

function makeAjaxCall(url) {
  // request and return promise
}

function makeHttpCall(url) {
  // request and return promise
}
```

**Good:**
```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'ajaxAdapter';
  }

  request(url) {
    // request and return promise
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'nodeAdapter';
  }

  request(url) {
    // request and return promise
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    return this.adapter.request(url).then((response) => {
      // transform response and return
    });
  }
}
```
 -->

<!-- ### Liskov Substitution Principle (LSP)
### リスコフの置換原則 (LSP)

これは非常に単縦なコンセプトと呼ぶには恐れ多いです。
正式には、次のように定義されています。
「もしSがTの派生型の場合、T型のオブジェクトは、そのプログラムの特性(正確さ、作業能力、など)を損なうことなく、S型のオブジェクトで置き換えることができなければならない(いういなれば、S型のオブジェクトはT型のオブジェクトと置換することができる)」
これでもさらにやっかいな定義です。


これのもっとも良い説明は、もし親クラスと子クラスがある場合、親クラスと子クラスは異なる振る舞いを起こすことなく、交互に利用することができるということです。
まだ混乱しているかもしれないので、古典的な正方形と長方形の例を見てましょう。
数学的には、正方形は長方形ですが、もし継承による「is-a(AはBである)」の関係を利用している場合、容易に問題に陥ります。

**Bad:**
```javascript
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width;
    this.height = width;
  }

  setHeight(height) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach((rectangle) => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // BAD: Returns 25 for Square. Should be 20.　だめ！正方形に25が返ってしまう、20になるべきです。
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Good:**
```javascript
class Shape {
  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(length) {
    super();
    this.length = length;
  }

  getArea() {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach((shape) => {
    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```
 -->

<!-- ### Interface Segregation Principle (ISP)
### インターフェイス分離の原則 (ISP)

JavaScriptはインターフェイスを持っていないため、この原則は他のように厳密には適用されません。
しかしながら、これは重要かつ、JavaScriptの欠落した型システムにも関連しています。


ISPは「クライアントに、彼らが利用していないインターフェイスへの依存を強要してはならない」と記されています。
JavaScriptにおいては、ダックタイピングのため、インターフェイスは暗黙的な契約です。


このJavaScriptでこの原則を説明を見せるための良い例は、巨大な設定オブジェクトが必要なクラスです。
クライアントに巨大な数のオプション設定を要求しないことが効果的です。なぜなら、多くの場合すべての設定は必要ないからです。
それらを任意にすることが「肥ったインターフェイス」を持つことを防ぐために有効です。

**Bad:**
```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName('body'),
  animationModule() {} // Most of the time, we won't need to animate when traversing. ほとんどの場合、アニメーションを必要としていない
  // ...
});

```

**Good:**
```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.options = settings.options;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.setupOptions();
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName('body'),
  options: {
    animationModule() {}
  }
});
```
 -->

<!-- ### Dependency Inversion Principle (DIP)
### 依存性逆転の原則 (DIP)
This principle states two essential things:
1. High-level modules should not depend on low-level modules. Both should
depend on abstractions.
2. Abstractions should not depend upon details. Details should depend on
abstractions.

この原則では2つの重要なことを述べています。
1. 上位のモジュールは下位のモジュールに依存してはならない。それぞれは、抽象に依存するべき。
2. 抽象は実装に依存してはいけない、実装は抽象に依存するべき。


これははじめの内は理解することが難しですが、もしAngularJSを触ったことがある人であれば、依存性注入(DI)と言う形でその実装をみたことがあるでしょう。
それらは同一のコンセプトではありませんが、DIPは上位のモジュールが下位のモジュールの実装を知り、それらを設定することを保ちます（え？保つ？逆じゃないの？？）。
このことの巨大なメリットは、モジュール間の密結合を削減することです。
密結合することは、非常に悪い開発のパターンです。なぜなら、それはコードをリファクタしにくくするからです。


以前に述べたように、JavaScriptはインターフェイスを持たないため、その抽象とは暗黙の契約の上に依存しています。
すなわち、あるオブジェクトやクラスのメソッドやプロパティは、他のオブジェクトやクラスにさらけ出されています。
下の例の中の暗黙の契約とは、全ての `InventoryTracker` リクエストモジュールは、 `requestItems` メソッドを持っていることです。

**Bad:**
```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ['HTTP'];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // BAD: We have created a dependency on a specific request implementation.
    // We should just have requestItems depend on a request method: `request`
    // だめ！ある特別なリクエスト実装に依存したものを持っている
    // `request`のような、リクエストメソッドに依存したrequestItemsだけを持つべき
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(['apples', 'bananas']);
inventoryTracker.requestItems();
```

**Good:**
```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ['HTTP'];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ['WS'];
  }

  requestItem(item) {
    // ...
  }
}

// By constructing our dependencies externally and injecting them, we can easily
// substitute our request module for a fancy new one that uses WebSockets.
// 外部から依存関係を構築しそれを注入することで、リクエストモジュールを
// 新しいWebSocketを使ったイケているものに置き換えることができる。
const inventoryTracker = new InventoryTracker(['apples', 'bananas'], new InventoryRequesterV2());
inventoryTracker.requestItems();
```


## **Testing**
## **テスト**
Testing is more important than shipping. If you have no tests or an
inadequate amount, then every time you ship code you won't be sure that you
didn't break anything. Deciding on what constitutes an adequate amount is up
to your team, but having 100% coverage (all statements and branches) is how
you achieve very high confidence and developer peace of mind. This means that
in addition to having a great testing framework, you also need to use a
[good coverage tool](http://gotwarlost.github.io/istanbul/).

テストはリリースするよりも大事なことです。
もしテストがなかったり不十分だった場合、コードをリリースするたびに、何も壊れていないことを確かめることはできません。
何をもって十分な量であるかを決定することはチームに任されていますが、(すべての文や分岐に対して)100%のカバレッジを持つことは、非常に高い信頼性と開発者の安心を達成する方法です。
この意味することは、あなたは良いテストフレームワークを持つことに加えて、[良いカバレッジツール](http://gotwarlost.github.io/istanbul/)も使うことが必要だということです。

There's no excuse to not write tests. There's [plenty of good JS test frameworks](http://jstherightway.org/#testing-tools), so find one that your team prefers.
When you find one that works for your team, then aim to always write tests
for every new feature/module you introduce. If your preferred method is
Test Driven Development (TDD), that is great, but the main point is to just
make sure you are reaching your coverage goals before launching any feature,
or refactoring an existing one.

テストを書かない理由はありません。ここに[多くの優れたJSテストフレームワーク](http://jstherightway.org/#testing-tools)があるので、チームが好むものを一つ見つけてください。
チームに対して一つのフレームワークを見つけた場合は、導入する全ての機能・モジュールごとに、常にテストを書くことを目指します。
もし、あなたが気に入っている方法がテスト駆動開発(TDD)であれば、それは素晴らしいことです。
しかし重要なポイントは、なにかの機能をリリースする前にカバレッジのゴールに達成するか、すでに存在するものをリファクタリングすることを確実にすることだけです。

### Single concept per test
### テストごとに単純なコンセプトを持つこと

**Bad:**
```javascript
import assert from 'assert';

describe('MakeMomentJSGreatAgain', () => {
  it('handles date boundaries', () => {
    let date;

    date = new MakeMomentJSGreatAgain('1/1/2015');
    date.addDays(30);
    assert.equal('1/31/2015', date);

    date = new MakeMomentJSGreatAgain('2/1/2016');
    date.addDays(28);
    assert.equal('02/29/2016', date);

    date = new MakeMomentJSGreatAgain('2/1/2015');
    date.addDays(28);
    assert.equal('03/01/2015', date);
  });
});
```

**Good:**
```javascript
import assert from 'assert';

describe('MakeMomentJSGreatAgain', () => {
  it('handles 30-day months', () => {
    const date = new MakeMomentJSGreatAgain('1/1/2015');
    date.addDays(30);
    assert.equal('1/31/2015', date);
  });

  it('handles leap year', () => {
    const date = new MakeMomentJSGreatAgain('2/1/2016');
    date.addDays(28);
    assert.equal('02/29/2016', date);
  });

  it('handles non-leap year', () => {
    const date = new MakeMomentJSGreatAgain('2/1/2015');
    date.addDays(28);
    assert.equal('03/01/2015', date);
  });
});
```
 -->

## **Concurrency**
## **同期処理**
### Use Promises, not callbacks
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


<!-- ### Async/Await are even cleaner than Promises
### Async/AwaitはPromiseよりさらに簡潔です

Promiseはコールバックに対しては非常に簡潔ですが、S2017/ES8はより簡潔な解決案としてasyncとawaitを引き連れてきました。
あなたが必要なことは`async`キーワードを関数の先頭につけることです。そして`then`で関数を連結することなく、ロジックを命令的に書くことができます。
あなたが、今日のES2017/ES8機能の恩恵を受けたい場合は、これを使ってください。

**Bad:**
```javascript
import { get } from 'request-promise';
import { writeFile } from 'fs-promise';

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

**Good:**
```javascript
import { get } from 'request-promise';
import { writeFile } from 'fs-promise';

async function getCleanCodeArticle() {
  try {
    const response = await get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin');
    await writeFile('article.html', response);
    console.log('File written');
  } catch(err) {
    console.error(err);
  }
}
```
 -->


## **Error Handling**
## **エラーハンドリング**

例外が発生することは良いことです！この意味は、ランタイムがあなたのプログラムが何かおかしいことを正常に突き止めたということです。
それは、関数の実行を直近のスタックで停止し、そのプロセスを停止し(ノード中)、コンソールのスタックトレースを通じてあなたに知らせてくれます。

### Don't ignore caught errors
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

### Don't ignore rejected promises
### 失敗したpromiseを無視しない
For the same reason you shouldn't ignore caught errors
from `try/catch`.

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
## **コメント**

### Only comment things that have business logic complexity.
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


### Don't leave commented out code in your codebase
### コメントアウトしたコードをコードベースに残さない
Version control exists for a reason. Leave old code in your history.

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


### Don't have journal comments
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



