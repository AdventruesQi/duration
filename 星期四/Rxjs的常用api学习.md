# Rxjs中的基础api的学习：



这里主要介绍一些Rxjs常用api的用法，基础的概念还得之前师尊的react系列课程的文档：

观看方法：

```javascript
// 图谱
// ----- 代表一个Observable
// -----X 代表一个Observable有错误发生
// -----| 代表一个Observable结束
// (1234)| 代表一个同步Observable结束

// 特别提示：以下的操作符介绍均采用rxjs6的写法！！！
```



### map方法：

- 其实map操作符和js数组里的map差不多，都是传入一个callback，执行callback回传新值.

🧩具体代码如下：

```javascript
/**
 * 例如： interval(1000).pipe(map(x => x + 1));
 *       -----0-----1-----2-----3--...
 *              map(x => x + 1)
 *       -----1-----2-----3-----4--...
 */
interval(1000).pipe(
    map(x => x + 1)
).subscribe({
    next: (value) => { console.log('======map操作符: ', value); },
    error: (error) => { console.log('======map操作符---Error: ', error); },
    complete: () => { console.log('======map操作符: complete'); }
});
```



### mapTo方法：

- mapTo是把传进来的值改写成为一个固定值

🧩具体代码如下：

```javascript
/**
 * 例如： interval(1000).pipe(mapTo(2))
 *      -----0-----1-----2-----3--...
 *              mapTo(2)
 *      -----2-----2-----2-----2--...
 */
interval(1000).pipe(
    mapTo('mapTo')
).subscribe({
    next: (value) => { console.log('======mapTo操作符: ', value); },
    error: (error) => { console.log('======mapTo操作符---Error: ', error); },
    complete: () => { console.log('======mapTo操作符: complete'); }
});
```



### filter方法：

- 其实filter操作符和js数组里的filter也差不多，都是传入一个call back，执行callback，根据回传的boolean值过滤源数据，再回传新值。

🧩具体代码如下：

```javascript
/**
 * 例如：interval(1000).pipe(filter(x => x % 2 === 0));
 *      -----0-----1-----2-----3-----4--...
 *           filter(x => x % 2 === 0)
 *      -----0-----------2-----------4--...
 */
interval(1000).pipe(
    filter(x => x % 2 === 0)
).subscribe({
    next: (value) => { console.log('======filter操作符: ', value); },
    error: (error) => { console.log('======filter操作符---Error: ', error); },
    complete: () => { console.log('======filter操作符: complete'); }
});
```



完整的例子：

```javascript
import { Component, OnInit, OnDestroy } from '@angular/core';
import { Observable, of, interval, Subscription } from 'rxjs';
import { map, take, mapTo, filter } from 'rxjs/operators';

@Component({
    selector: 'app-rxjs-demo',
    template: `
        <h3>Rxjs Demo To Study! -- Operators操作符(map、mapTo、filter)</h3>
        <button (click)="originMapHandler()">origin map</button>
        <button class="mgLeft" (click)="mapHandler()">map</button>
        <button class="mgLeft" (click)="mapToHandler()">mapTo</button>
        <button class="mgLeft" (click)="filterHandler()">mapTo</button>
        <app-back></app-back>
    `,
    styles: [`
        .mgLeft {
            margin-left: 20px;
        }
    `]
})
export class RxjsDemoComponent implements OnInit, OnDestroy {
    originMapSubscription: Subscription;
    mapSubscription: Subscription;
    mapToSubscription: Subscription;
    filterSubscription: Subscription;

    constructor() { }

    ngOnInit(): void {
        // 图谱
        // ----- 代表一个Observable
        // -----X 代表一个Observable有错误发生
        // -----| 代表一个Observable结束
        // (1234)| 代表一个同步Observable结束
    }

    map(source, callback) {
        return Observable.create(observer => {
            return source.subscribe(
                (value) => {
                    try {
                        observer.next(callback(value));
                    } catch (e) {
                        observer.error(e);
                    }
                },
                (err) => { observer.error(err); },
                () => { observer.complete(); }
            );
        });
    }

    originMapHandler() {
        // 1. 传统写法创建Map操作符
        const people = of('Jerry', 'Anna');
        const helloPlople = this.map(people, item => item + ' Hello~');
        this.originMapSubscription = helloPlople.subscribe({
            next: (value) => { console.log('======传统写法创建Map操作符: ', value); },
            error: (error) => { console.log('======传统写法创建Map操作符---Error: ', error); },
            complete: () => { console.log('======传统写法创建Map操作符: complete'); }
        });
    }

    mapHandler() {
        /**
         * 例如： interval(1000).pipe(map(x => x + 1));
         *       -----0-----1-----2-----3--...
         *              map(x => x + 1)
         *       -----1-----2-----3-----4--...
         */
        this.mapSubscription = interval(1000).pipe(
            map(x => x + 1),
            take(4)
        ).subscribe({
            next: (value) => { console.log('======map操作符: ', value); },
            error: (error) => { console.log('======map操作符---Error: ', error); },
            complete: () => { console.log('======map操作符: complete'); }
        });
    }

    mapToHandler() {
        /**
         * 例如： interval(1000).pipe(mapTo(2))
         *      -----0-----1-----2-----3--...
         *              mapTo(2)
         *      -----2-----2-----2-----2--...
         */
        this.mapToSubscription = interval(1000).pipe(
            mapTo('mapTo'),
            take(3)
        ).subscribe({
            next: (value) => { console.log('======mapTo操作符: ', value); },
            error: (error) => { console.log('======mapTo操作符---Error: ', error); },
            complete: () => { console.log('======mapTo操作符: complete'); }
        });
    }

    filterHandler() {
        /**
         * 例如：interval(1000).pipe(filter(x => x % 2 === 0));
         *      -----0-----1-----2-----3-----4--...
         *           filter(x => x % 2 === 0)
         *      -----0-----------2-----------4--...
         */
        this.filterSubscription = interval(1000).pipe(
            filter(x => x % 2 === 0),
            take(5)
        ).subscribe({
            next: (value) => { console.log('======filter操作符: ', value); },
            error: (error) => { console.log('======filter操作符---Error: ', error); },
            complete: () => { console.log('======filter操作符: complete'); }
        });
    }

    ngOnDestroy() {
        if (this.originMapSubscription) {
            this.originMapSubscription.unsubscribe();
        }
        if (this.mapSubscription) {
            this.mapSubscription.unsubscribe();
        }
        if (this.mapToSubscription) {
            this.mapToSubscription.unsubscribe();
        }
        if (this.filterSubscription) {
            this.filterSubscription.unsubscribe();
        }
    }
}
```

