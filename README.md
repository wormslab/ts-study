# Decorator

```json
{
  "compilerOptions": {
    "module": "commonjs",
    "target": "es3",
    "sourceMap": true,
    "experimentalDecorators": true
  },
  "exclude": ["node_modules"]
}
```

"experimentalDecorator": true 로 설정해야 사용할 수 있다.



### Decorator syntax

```ts
function simpleDecorator(constructor : Function) {}

@simpleDecorator 
class ClassWithSimpleDecorator {}
```

다수의 instance 를 만들더라도 decorator 는 딱 한번만 실행된다.

즉 class 디자인을 좀더 풍부하게 도와줄 수 있는 역할을 한다.

cconstructor 를 인자로 받기 때문에 원하는 내용을 주입할 수 있다.



### Multiple decorators

```ts
function simpleDecorator(constructor : Function) {}
function secondDecorator(constructor : Function) {}

@simpleDecorator
@secondDecorator
class ClassWithSimpleDecorator {} 
```

secondDecorator -> simpleDecorator 순서로 호출이 된다

class 확장시에 최하위 class 부터 부모 class 순서대호 호출되는 것과 비슷하다



### Decorator factories

```ts
 function decoratorFactory(name: string) { 
   return function (constructor : Function ) {}
 }
```

express 에서 익히 보이던 패턴, 사용자로부터 옵션을 입력받아 사용하고자 하는 인터페이스에 맞도록 함수를 반환한다

> ```ts
> function closure (options) {
>   return (req, res, next) {
>       // ...use options
>   }
> }
> app.use(connector({ foo: 'bar' }))
> ```



### decorators

###### class decorator

###### property decorators

###### statis property decorators

###### method decorators

###### parameter decorators

```ts
@classConstructorDec
class ClassWithConstructor {}

function propertyDec(target: any, propertyKey : string) {}
class ClassWithPropertyDec { 
  @propertyDec name: string; 
}
class StaticClassWithPropertyDec { 
  @propertyDec static name: string; 
}

function methodDec (target: any, methodName: string, descriptor?: PropertyDescriptor) {}
class ClassWithMethodDec { 
  @methodDec 
  print(output: string) {}
}
```



### Using decorator metadata

tsconf.json 에 다음과 가능 flag 를 사용하면 메터데이터를 사용할 수 있다

```json
{ 
  "compilerOptions": {  
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true 
  }
} 
```

컴파일시에 <kbd>design:type</kbd>  <kbd>design:paramtypes</kbd>  <kbd>design:returntype</kbd>  필드를 추가적으로 생성한다

<kbd>reflect-metadata</kbd>  package 를 사용해서 메타데이터를 사용할 수 있다.

이는 C#, JAVA 에서 Reflection 이라고 불리는 기능과 유사하다

```ts
function metadataParameterDec(target: any, methodName : string,              
  parameterIndex: number) { 
    let designType = Reflect.getMetadata("design:type", target, methodName)
    console.log(designType)
}
```

📝 designType: function Function() { [native code] } 



### vue-property-decorator

```ts
// 라이브러리중 일부
export function Prop(options: PropOptions | Constructor[] | Constructor = {}) {
  return (target: Vue, key: string) => {
    applyMetadata(options, target, key)
    createDecorator((componentOptions, k) => {
      // ...    
    })(target, key) 
  }
}
```

```ts
@Component({  components: { VDivider },  filters: { comma }})
export default class ReservationCancelInfo extends Vue {
  @Prop({ type: String, required: true }) reservationNo!: string    
  @Watch('userQuery')
  handleChangeUserQuery (value) {
      // do somthing...
  }
}
```



# Generic

제네릭은 모든 타입의 객체를 다루면서도 객체 타입 무결성을 유지하는 코드를 작성하는 방법이다.



### Generic syntax

```ts
class ConcatenatorNumber<number> { 
  concatenateArray(inputArray: Array<number>): string {
}
class ConcatenatorString<string> {
 concatenateArray(inputArray: Array<string>): string {
}
// ... other type classes
```

위와같은 코드가 있을때 필요한 타입마다 비슷하게 생긴 class 를 정의하는것은 옳지 않아 보인다

<kbd>모든 타입의 객체를 다루면서도</kbd> 는 하나의 Concatenator class 를  이용하여 모든타입이 클래스를 사용할 수 있도록 한다는 의미이다.

```ts
class Concatenator< T > {
 concatenateArray(inputArray: Array< T >): string {}
}
```

위와같은 구문을 사용하여 generic class 를 구현할 수 있다

### 

### Using the type T

```js
const stringConcat = new Concatenator<string>(); 
const numberConcat = new Concatenator<number>(); 
```

T 타입으로 선언했기 때문에 사용자가 원하는 타입을 직접 선언하여 사용할 수 있다.

<kbd>concatenateArray</kbd> 메서드또한 T 타입으로 선언 했기 때문에 stringConcat 의 concatenateArray 의 메서드에서는 string 타입만 사용이 가능하고 numberConcat 의 concatenateArray 메서드에서는 number 타입만이 사용가능하다. 이것이 <kbd>객체 타입 무결성 유지</kbd> 를 의미한다



```ts
class MyClass { 
  private _name: string; 
  constructor(arg1: number) { 
    this._name = arg1 + "_MyClass"; 
  } 
}
const myArray: MyClass[] = [ new MyClass(1), new MyClass(2), new MyClass(3)];
const myArrayConcatentator = new Concatenator<MyClass>()
const myArrayResult = myArrayConcatentator.concatenateArray(myArray)
console.log(myArrayResult)
```

📝[object Object],[object Object],[object Object] 

JAVA 와 비슷하게 class 자체를 string 으로 출력하면 위와같은 결과를 만난다

```ts
class MyClass { 
  private _name: string; 
  constructor(arg1: number) { 
    this._name = arg1 + "_MyClass"; 
  } 
  toString(): string { 
    return this._name; 
  } 
} 
```

클래스를 원하는 형태로 출력시키기 위해서는 toString 메서드의 오버라이드가 필요하다.



### Constraining the type of T

```ts
enum ClubHomeCountry {
  England,
  Germany
}

interface IFootballClub {
  getName(): string
  getHomeCountry(): ClubHomeCountry
}

abstract class FootballClub implements IFootballClub {
  protected _name: string
  protected _homeCountry: ClubHomeCountry
  getName () {
    return this._name
  }

  getHomeCountry () {
    return this._homeCountry
  }
}

class Liverpool extends FootballClub {
  constructor () {
    super()
    this._name = 'Liverpool F.C.'
    this._homeCountry = ClubHomeCountry.England
  }
}

class BorussiaDortmund extends FootballClub {
  constructor () {
    super()
    this._name = 'Borussia Dortmund'
    this._homeCountry = ClubHomeCountry.Germany
  }
}

interface IFootballClubPrinter<T extends IFootballClub> {
  print(arg: T)
  IsEnglishTeam(arg: T)
}

class FootballClubPrinter<T extends IFootballClub> implements IFootballClubPrinter<T> {
  print (arg: T) {
    console.log(` ${arg.getName()} is ` + `${this.IsEnglishTeam(arg)}` + ' an English football team.')
  }

  IsEnglishTeam (arg: T): string {
    if (arg.getHomeCountry() === ClubHomeCountry.England) {
      return ''
    } else {
      return 'NOT'
    }
  }
}
```

코드가 너무 길도 장황하여 안좋은 예 같지만 결국 이야기 하고자 하는것은 T 타입으로 받은 인자가 특정한 인터페이스를 구현한것을 보장받고 싶을때 사용한다

> T 는 IFootballClub 을 구현하였으므로 print 메서드의 arg 는 getName() 과 getHomeCountry() 메서드를 사용할 수 있다.



### Generic interfaces

```ts
interface IFootballClubPrinter < T extends IFootballClub > { 
  print(arg : T); 
  IsEnglishTeam(arg : T); 
} 
```

위의 예제에서같이 인터페이스도  generic 을 이용하여 타입을 정의할 수 있다

```ts
class FootballClubPrinter< T >
 implements IFootballClubPrinter< T > {
} 
```

> error TS2344: Type 'T' does not satisfy the constraint 'IFootballClub'.

위에서 IFootballClubPrinter 가 T extends IFootballClub 으로 타입제한을 걸었는데 이를 지키지 않았다. 평소에 이런 generic class 구현을 자주한다면 헷갈리지 않을 수 있겠지만 이쯤되면 클래스 디자인 주화입마에 빠져버릴 가능성이 크다.



### Creating new objects within generics

```ts
class FirstClass { 
  id: number; 
} 

class SecondClass { 
  name: string; 
} 

class GenericCreator< T > {
  create (): T {
    return new T()
  }
}
```

generic 프로그래밍을 하다보면 클래스 생성도 T 를 이용하여 런타임에 생성가능하지 않을까 하는 생각에 위와같은 실수를 만나기 쉽다. T는 단지 타입일 뿐 구현체가 될 수 없기 때문에 별도의 문법을 제공한다

```ts
class GenericCreator< T > { 
  create(arg1: { new(): T }) : T { 
    return new arg1(); 
  } 
} 
```

위와같이 사용한다면 클래스 생성도 generic 을 적용할 수 있다

```ts
const creator1 = new GenericCreator<FirstClass>()
const firstClass = creator1.create(FirstClass)

const creator2 = new GenericCreator<SecondClass>()
const secondClass = creator2.create(SecondClass)
```

> firstClass 에서는 id property 만 접근이 가능하고 secondClass 에서는 name property 만 접근이 가능하다
> 
> 도대체가 이 코드가 왜 필요한지 모르겠다. new FirstClass() 한줄 과 무엇이 다른가...
> 
> decorator, reflection 조합이 훨씬 더 효율적으로 보인다

# 

# Asynchronous

### Promise

콜백지옥을 해결해줄 여러가지의 라이브러리들을 제치고 표준으로 채택된 비동기 프로세스 기술이다

```js
fs.readdir(source, function (err, files) {
  if (err) {
    console.log('Error finding files: ' + err)
  } else {
    files.forEach(function (filename, fileIndex) {
      console.log(filename)
      gm(source + filename).size(function (err, values) {
        if (err) {
          console.log('Error identifying file size: ' + err)
        } else {
          console.log(filename + ' : ' + values)
          aspect = (values.width / values.height)
          widths.forEach(function (width, widthIndex) {
            height = Math.round(width / aspect)
            console.log('resizing ' + filename + 'to ' + height + 'x' + height)
            this.resize(width, height).write(dest + 'w' + width + '_' + filename, function(err) {
              if (err) console.log('Error writing file: ' + err)
            })
          }.bind(this))
        }
      })
    })
  }
})
```



### Promise syntax

```ts
function fnDelayedPromise (resolve: () => void, reject: () => void) {
  function afterTimeout () {
    resolve()
  }

  setTimeout(afterTimeout, 2000)
}

function delayedResponsePromise (): Promise<void> {
  return new Promise<void>(fnDelayedPromise)
}
```

resolve, reject 2개의 함수를 인자로 받아 Promise 객체를 생성할 수 있다



### Using promises

```ts
function fnDelayedPromise (resolve: () => void, reject: () => void) {
  function afterTimeout () {
    resolve()
  }

  setTimeout(afterTimeout, 2000)
}

function errorPromise (): Promise<void> {
  return new Promise<void>((resolve: () => void, reject: () => void) => {
    reject()
  })
}
```

위와같이 정의된 Promise 가 있다면

```ts
function callDelayedPromise() { 
  console.log(`calling delayedPromise`); 
  delayedPromise().then( 
    () => { console.log(`delayedPromise.then()`) } 
  ); 
} 

callDelayedPromise(); 
```

resolved promise 는 then 구문을 이용하고..

```ts
function callErrorPromise() { 
  console.log(`calling errorPromise`); 
  errorPromise().then( 
    () => { console.log(`no error.`) } 
  ).catch( 
    () => { console.log(`an error occurred`)} 
  ); 
} 

callErrorPromise(); 
```

rejected promise 는 catch 구문을 이용한다

> 솔직히 예제와 설명이 너무 구림..



### Callback vs promise syntax

```ts
function standardCallback () {
  function afterCallbackSuccess () {
    // execute this code
  }
  function afterCallbackError () {
    // execute on error
  }
  // invoke async function
  invokeAsync(afterCallbackSuccess, afterCallbackError)
}

function usingPromises () {
  // invoke async function
  delayedPromise().then(
    () => {
      // execute on success
    }
  ).catch(
    () => {
      // execute on error
    }
  )
}
```

이렇게 단적인 면만 놓고 보면 어떤게 더 좋아 보이는지 말하기 어려워 보인다.

무엇인 <kbd>fluent syntax</kbd>  라는것인지..

### Returning values from promises

```ts
function callPromiseWithParam() { 
  console.log(`calling delayedPromiseWithParam`); 
  delayedPromiseWithParam().then( (message: string) => { 
    console.log(`Promise.then() returned ${message} `); 
  } ); 
} 

callPromiseWithParam(); 
```

📝calling delayedPromiseWithParam
📝Promise.then() returned resolved_within_promise

### Async and await

```ts
function awaitDelayed (): Promise<void> {
  return new Promise<void>((resolve: () => void, reject: () => void) => {
    function afterWait () {
      console.log('calling resolve')
      resolve()
    }
    setTimeout(afterWait, 1000)
  })
}

async function callAwaitDelayed () {
  console.log('call awaitDelayed')
  await awaitDelayed()
  console.log('after awaitDelayed')
}

callAwaitDelayed()

```

await 를 사용해서 awaitDelayed 함수가 resolve 처리되기 까지 실행를 미룰수(deferred) 있다

### Await errors

```ts
function awaitError() : Promise<string> { 
  return new Promise<string> ( 
    ( resolve: (message: string) => void, 
      reject: (error: string) => void ) =>  
      { 
         function afterWait() { 
           console.log(`calling reject`); 
           reject("an error occurred"); 
         }     
         setTimeout(afterWait, 1000); 
      } 
  ); 
} 

async function callAwaitError () {
  console.log('call awaitError')
  try {
    await awaitError()
  } catch (error) {
    console.log(`error returned : ${error}`)
  }
  console.log('after awaitDelayed')
}

callAwaitError()
```

reject 함수를 호출하면 오류를 반환하므로 try/catch 를 이용하여 오류를 잡아낼 수 있다

>  책에는 Promise.catch 는 설명이 되어 있지만 await 는 try/catch 를 이용하여 설명했다.

### 

### Generator function

### coroutine

caller가 함수를 call하고, 함수가 caller에게 값을 return하면서 종료하는 것에 더해 return하는 대신 suspend(혹은 yield)하면 caller가 나중에 resume하여 중단된 지점부터 실행을 이어갈 수 있다.



### JS  generator

```js
function* generator(i) {
  yield i;
  yield i + 10;
}

const gen = generator(10);

setTimeout(() => {
  console.log(gen.next().value);
  // expected output: 10
}, 3000)

setTimeout(() => {
  console.log(gen.next().value);
  // expected output: 20
}, 5000)
```

javascript 에서 coroutine 의 구현체로 coroutine 을 가능하게 한다



### Npm - co library

```js
function co(gen) {
  var ctx = this;
  var args = slice.call(arguments, 1);

  // we wrap everything in a promise to avoid promise chaining,
  // which leads to memory leak errors.
  // see https://github.com/tj/co/issues/180
  return new Promise(function(resolve, reject) {
    if (typeof gen === 'function') gen = gen.apply(ctx, args);
    if (!gen || typeof gen.next !== 'function') return resolve(gen);

    onFulfilled();

    /**
     * @param {Mixed} res
     * @return {Promise}
     * @api private
     */

    function onFulfilled(res) {
      var ret;
      try {
        ret = gen.next(res);
      } catch (e) {
        return reject(e);
      }
      next(ret);
      return null;
    }

    /**
     * @param {Error} err
     * @return {Promise}
     * @api private
     */

    function onRejected(err) {
      var ret;
      try {
        ret = gen.throw(err);
      } catch (e) {
        return reject(e);
      }
      next(ret);
    }

    /**
     * Get the next value in the generator,
     * return a promise.
     *
     * @param {Object} ret
     * @return {Promise}
     * @api private
     */

    function next(ret) {
      if (ret.done) return resolve(ret.value);
      var value = toPromise.call(ctx, ret.value);
      if (value && isPromise(value)) return value.then(onFulfilled, onRejected);
      return onRejected(new TypeError('You may only yield a function, promise, generator, array, or object, '
        + 'but the following object was passed: "' + String(ret.value) + '"'));
    }
  });
}
```

github start 11K 짜리 비동기 코딩을 위한 js 라이브러리 구현의 일부 결국 마지막 next 함수가 핵심인데 지속적으로 ret generator 의  next 함수를 yield 가 호출될 때마다 응답해준다

```js
co(function *(){
  // yield any promise
  var result1 = yield Promise.resolve(true);
  var result2 = yield Promise.resolve(true);
}).catch(onerror);

co(function *(){
  // resolve multiple promises in parallel
  var a = Promise.resolve(1);
  var b = Promise.resolve(2);
  var c = Promise.resolve(3);
  var res = yield [a, b, c];
  console.log(res);
  // => [1, 2, 3]
}).catch(onerror);
```

실제 이 라이브러리를 사용했을때 코드

이 모습을 조금만 들여다 보면  generator function 의 * 기호가 async 키워드로 yield 키워드가 await 키워드로 바뀐것이 async / await 라고 볼 수 있다. TS 에서는 처음부터 async / await 를 사용했고 이것이 더 직관적으로 보여 JS 도 이것을 채택한것으로 보인다.



