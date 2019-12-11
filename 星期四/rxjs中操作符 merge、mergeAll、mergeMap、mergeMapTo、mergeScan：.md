# rxjs中操作符 merge、mergeAll、mergeMap、mergeMapTo、mergeScan：

### 1.merge：创建一个输出 Observable ，它可以同时发出每个给定的输入 Observable 中值。



```javascript
//合并了各个流
const sourceOne$ = of(1, 2, 3);
   const sourceTwo$ = of(5, 6);
   const sourceThree$ = of(7, 8);
   sourceOne$
       .pipe(
         merge(sourceTwo$, sourceThree$)
       )
       .subscribe(val => {
         console.log(val);
       });
//输出
1
2
3
5
6
7
8
 
 
const button = document.querySelector('button');
    const click$ = fromEvent(button, 'click');
    const timer$ = interval(1000);
    click$.pipe(
      merge(timer$)
    ).subscribe(x => console.log(x));
0
1
2
//点击按钮
MouseEvent {isTrusted: true, screenX: 41, screenY: 254, clientX: 41, clientY: 143, …}
3
4
5
//点击按钮
MouseEvent {isTrusted: true, screenX: 41, screenY: 254, clientX: 41, clientY: 143, …}
6
7
```

![merge.png](https://upload-images.jianshu.io/upload_images/12442612-becd8a09ac5f5be6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- merge 订阅每个给定的输入 Observable (作为参数)，将所有输入 Observables 的所有值发送(不进行任何转换)到输出 Observable 。所有的输入 Observable 都完成了，输出 Observable 才 能完成。任何由输入 Observable 发出的错误都会立即在输出 Observalbe 上发出。



### 2. mergeAll：将高阶 Observable 转换成一阶 Observable ，一阶 Observable 会同时发出在内部 Observables 上发出的所有值。



```javascript
 const button = document.querySelector('button');
    const click$ = fromEvent(button, 'click');
    const interval$ = interval(1000);
    click$.pipe(
      map(() => interval$),
      mergeAll()
      ).subscribe(num => console.log(num));
//点击button
0
1
//点击button
0
2
1
3
2
4
//点击button
0
3
5
```

![mergeall.png](https://upload-images.jianshu.io/upload_images/12442612-e14d71034b694045.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

还不是很理解...

### 3.mergeMap：将每个源值投射成 Observable ，该 Observable 会合并到输出 Observable 中。

```javascript

const letters$ = of('a', 'b', 'c');
    letters$.pipe(
      mergeMap(x =>
        interval(1000).pipe(
          map(i => x + i)
        )
      )
    ).subscribe(x => console.log(x));
//输出
a0
b0
c0
a1
b1
c1
...
```

![mergemap.png](https://upload-images.jianshu.io/upload_images/12442612-9ec1b7088735d335.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 将每个值映射成 Observable ，然后使用 [mergeAll](https://cn.rx.js.org/class/es6/Observable.js~Observable.html#instance-method-mergeAll) 打平所有的内部 Observables 。
- 可以看作是map与mergeAll的结合。



### 4.mergeMapTo:将每个源值投射成同一个 Observable ，该 Observable 会多次合并到输出 Observable 中。

```javascript
const button = document.querySelector('button');
    const click$ = fromEvent(button, 'click');
    click$.pipe(
      mergeMapTo(interval(1000))
    ).subscribe(x => console.log(x));
 
 
//点击button
0
 1
//点击button
 0
2
1
3
2
4
//点击button
0
3
5
 
 const sourceOne$ = of(1, 2, 3);
    const sourceTwo$ = of(5, 6);
    sourceOne$
        .pipe(
          mergeMapTo(sourceTwo$)
        )
        .subscribe(val => {
          console.log(val);
        });
//输出
5
6
5
6
5
6

```

![mergemapto.png](https://upload-images.jianshu.io/upload_images/12442612-955e8052d3802ac2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将每个源值映射成给定的 Observable ：`innerObservable` ，而无论源值是什么，然后 将这些结果 Observables 合并到单个的 Observable ，也就是输出 Observable 。



### 5.mergeScan:在源 Observable 上应用 accumulator 函数，其中 accumulator 函数本身返回 Observable ，然后每个返回的中间 Observable 会被合并到输出 Observable 中。

```javascript
const button = document.querySelector('button');
    const click$ = fromEvent(button, 'click');
    const seed = 1; // 初始的累加值。
    const fn = (acc, one) => of(acc * one); // 在每个源值上调用的累加器函数。
    click$
    .pipe(
      mapTo(2),
      mergeScan(fn, seed)
    ).subscribe(x => console.log(x));
//点击button
2
//点击button
4
//点击button
8
//点击button
16
```

