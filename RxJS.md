# RxJS Demos

## RxJS

### Observables

**Observable**: represents the idea of an invokable collection of future values or events.

```diff
import { Component, OnInit } from '@angular/core';
+ import { of } from 'rxjs';

@Component({
  selector: 'app-root',
  template: ``,
  styles: []
})
export class AppComponent implements OnInit {
+  ngOnInit(): void {
+    const observable$ = of(1, 2, 3);
+    observable$.subscribe(x => console.log(x));
+  }
}
```

OR

```diff
export class AppComponent implements OnInit {
+  ngOnInit(): void {
+    of(1, 2, 3).subscribe(x => console.log(x));
+  }
}
```

Result

- Open Chrome DevTools
- Switch to the Console tab
- Refresh the browser

```
1
2
3
```

<div style="page-break-after: always;"></div>

#### Creating a Stream of DOM Events

```diff
import { Component, OnInit, ViewChild } from '@angular/core';
import { of,
+ fromEvent } from 'rxjs';

@Component({
  selector: 'app-root',
  template: `
+    <button #myButton>Click Me</button>
  `,
  styles: []
})
export class AppComponent implements OnInit {
  @ViewChild('myButton', { static: true }) button;
  ngOnInit(): void {
+    console.log(this.button);
+    const clicks$ = fromEvent(this.button.nativeElement, 'click');
+    clicks$.subscribe(event => console.log(event));
  }
}

```

Result

- Open Chrome DevTools
- Switch to the Console tab
- Refresh the browser
- Click the button on the page

```
MouseEvent {isTrusted: true, screenX: 30, screenY: 101, clientX: 30, clientY: 22, …}
MouseEvent {isTrusted: true, screenX: 30, screenY: 101, clientX: 30, clientY: 22, …}
...
```

<div style="page-break-after: always;"></div>

#### Listening for keyup events

```ts
import { Component, OnInit, ViewChild } from '@angular/core';
import { fromEvent } from 'rxjs';

@Component({
  selector: 'app-root',
  template: `
    <input #myInput />
  `,
  styles: []
})
export class AppComponent implements OnInit {
  @ViewChild('myInput', { static: true }) input;
  ngOnInit(): void {
    console.log(this.input);
    const keyupEvents$ = fromEvent(this.input.nativeElement, 'keyup');
    keyupEvents$.subscribe((event: Event) =>
      console.log((event.target as HTMLInputElement).value)
    );
  }
}
```

Result

- Open Chrome DevTools
- Switch to the Console tab
- Refresh the browser
- Enter the letters `abcdef` into the input

```
a
ab
abc
abcd
...
```

<div style="page-break-after: always;"></div>

### Observers

**Observer**: is a collection of callbacks that knows how to listen to values delivered by the Observable.

```ts
import { Component, OnInit } from '@angular/core';
import { of, Observer } from 'rxjs';

@Component({
  selector: 'app-root',
  template: ``,
  styles: []
})
export class AppComponent implements OnInit {
  ngOnInit(): void {
    const observable$ = of(1, 2, 3);
    const observer: Observer<any> = {
      next: x => console.log(x),
      complete: () => console.log('completed'),
      error: x => console.log(x)
    };
    observable$.subscribe(observer);
  }
}
```

Result

- Open Chrome DevTools
- Switch to the Console tab
- Refresh the browser

```
1
2
3
completed
...
```

<div style="page-break-after: always;"></div>

### Subcriptions

**Subscription**: represents the execution of an Observable, is primarily useful for cancelling the execution.

```diff
import { Component, OnInit } from '@angular/core';
import { of, Observer,
+ interval } from 'rxjs';

@Component({
  selector: 'app-root',
  template: ``,
  styles: []
})
export class AppComponent implements OnInit {
  ngOnInit(): void {
-    const observable$ = of(1, 2, 3);
+    // Emits ascending numbers, one every second (1000ms)
+   const observable$ = interval(1000);
    const observer: Observer<any> = {
      next: x => console.log(x),
      complete: () => console.log('completed'),
      error: x => console.log(x)
    };
    const subscription = observable$.subscribe(observer);
+   setTimeout(() => subscription.unsubscribe(), 5000);
  }
}

```

Result

- Open Chrome DevTools
- Switch to the Console tab
- Refresh the browser

```
0
1
2
3
4
...
```

<div style="page-break-after: always;"></div>

### Operators

Continue from prior demo or...

```shell
git checkout rxjs-subscriptions
```

#### map

```diff
import { Component, OnInit } from '@angular/core';
import { of, Observer, interval } from 'rxjs';
+ import { map } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  template: ``,
  styles: []
})
export class AppComponent implements OnInit {
  ngOnInit(): void {
    // Emits ascending numbers, one every second (1000ms)
    const observable$ = interval(1000);
    const observer: Observer<any> = {
      next: x => console.log(x),
      complete: () => console.log('completed'),
      error: x => console.log(x)
    };

+    const observableCommingOutOfThePipe$ = observable$.pipe(
+      map(x => x * 10)
+    );
+    const subscription = observableCommingOutOfThePipe$.subscribe(observer);

-   const subscription = observable$.subscribe(observer);
-   setTimeout(() => subscription.unsubscribe(), 5000);
  }
}

```

<div style="page-break-after: always;"></div>

Result

- Open Chrome DevTools
- Switch to the Console tab
- Refresh the browser

```
0
10
20
30
...
```

<div style="page-break-after: always;"></div>

#### tap

```diff
import { map,
+ tap } from 'rxjs/operators';
...

    const observableCommingOutOfThePipe$ = observable$.pipe(
+     tap(x => console.log(x)),
      map(x => x * 10)
    );
```

Result

- Open Chrome DevTools
- Switch to the Console tab
- Refresh the browser

```
0
0
1
10
2
20
3
30
...
```

<div style="page-break-after: always;"></div>

#### filter

```ts
import { filter } from 'rxjs/operators';
...

const observableCommingOutOfThePipe$ = observable$.pipe(
  filter(x => x % 2 === 0)
);

```

Result

- Open Chrome DevTools
- Switch to the Console tab
- Refresh the browser

```
0
0
1
10
2
20
3
30
...
```

<div style="page-break-after: always;"></div>

RxJS code commonly uses the fluent syntax and chains functions.

```ts
ngOnInit(): void {
    // Emits ascending numbers, one every second (1000ms)
    // const observable$ = interval(1000);
    // const observer: Observer<any> = {
    //   next: x => console.log(x),
    //   complete: () => console.log('completed'),
    //   error: x => console.log(x)
    // };

    // const observableCommingOutOfThePipe$ = observable$.pipe(
    //   filter(x => x % 2 === 0)
    // );

    // observableCommingOutOfThePipe$.subscribe(observer);

    interval(1000)
      .pipe(filter(x => x % 2 === 0))
      .subscribe(x => console.log(x));
  }
```

<div style="page-break-after: always;"></div>

### Subjects

#### Read & Write

A Subject is like an Observable. It can be subscribed to, just like you normally would with Observables. It also has methods like next(), error() and complete() just like the observer you normally pass to your Observable creation function.

#### Multicast

The main reason to use Subjects is to multicast. An Observable by default is unicast. Unicasting means that each subscribed observer owns an independent execution of the Observable. To demonstrate this:

#### Unicast Observable Demo

```ts
import { Component, OnInit } from '@angular/core';
import { Observable } from 'rxjs';

@Component({
  selector: 'app-root',
  template: ``,
  styles: []
})
export class AppComponent implements OnInit {
  ngOnInit(): void {
    const observable = new Observable(subscriber => {
      subscriber.next(Math.random());
    });

    // subscription 1
    observable.subscribe(data => {
      console.log(data); // 0.24957144215097515 (random number)
    });

    // subscription 2
    observable.subscribe(data => {
      console.log(data); // 0.004617340049055896 (random number)
    });
  }
}
```

#### Multicast

Subjects can multicast. Multicasting basically means that one Observable execution is shared among multiple subscribers.

_Subjects are like EventEmitters, they maintain a registry of many listeners._ When calling subscribe on a Subject it does not invoke a new execution that delivers data. It simply registers the given Observer in a list of Observers.

#### Multicast Subject Example

```ts
import { Component, OnInit } from '@angular/core';
import { Subject } from 'rxjs';

@Component({
  selector: 'app-root',
  template: ``,
  styles: []
})
export class AppComponent implements OnInit {
  ngOnInit(): void {
    const subject = new Subject();

    // subscription 1
    subject.subscribe(data => {
      console.log(data); // 0.24957144215097515 (random number)
    });

    // subscription 2
    subject.subscribe(data => {
      console.log(data); // 0.004617340049055896 (random number)
    });

    subject.next(Math.random());
  }
}
```

<div style="page-break-after: always;"></div>

###

Observables = data producers
Subjects = data producer and a data consumer

By using Subjects as a data consumer you can use them to convert Observables from unicast to multicast.

Here’s a demonstration of that:

```ts
import { Component, OnInit } from '@angular/core';
import { Subject, Observable } from 'rxjs';

@Component({
  selector: 'app-root',
  template: ``,
  styles: []
})
export class AppComponent implements OnInit {
  ngOnInit(): void {
    const observable = new Observable(subscriber => {
      subscriber.next(Math.random());
    });

    const subject = new Subject();

    // subscriber 1
    subject.subscribe(data => {
      console.log(data);
    });

    // subscriber 2
    subject.subscribe(data => {
      console.log(data);
    });

    observable.subscribe(subject);
  }
}
```

### Practical Example

Search Box

```ts
import { Component, OnInit } from '@angular/core';
import { Subject } from 'rxjs';

@Component({
  selector: 'app-root',
  template: `
    <input
      type="text"
      #term
      (input)="search(term.value)"
      placeholder="search"
    />
    <br />
    <p *ngFor="let message of messages">{{ message }}</p>
  `,
  styles: []
})
export class AppComponent implements OnInit {
  messages: string[] = [];
  private searchTermStream$ = new Subject<string>();

  ngOnInit(): void {
    this.searchTermStream$.subscribe(term =>
      this.messages.push(`http call for: ${term}`)
    );
  }

  search(term: string) {
    this.searchTermStream$.next(term);
  }
}
```

<div style="page-break-after: always;"></div>

Result

- Type `angular` quickly in the searchbox
- Ouput is shown below the input on the page

```
http call for: an

http call for: ang

http call for: ang

http call for: angu

http call for: angular

http call for: angular

http call for: angular
...
```

<div style="page-break-after: always;"></div>

#### debounceTime

```diff
import { Component, OnInit } from '@angular/core';
import { Subject } from 'rxjs';
+ import { debounceTime } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  template: `
    <input
      type="text"
      #term
      (keyup)="search(term.value)"
      placeholder="search"
    />
    <br />
    <p *ngFor="let message of messages">{{ message }}</p>
  `,
  styles: []
})
export class AppComponent implements OnInit {
  messages: string[] = [];
  private searchTermStream$ = new Subject<string>();

  ngOnInit(): void {
    this.searchTermStream$
+     .pipe(debounceTime(300))
      .subscribe(term => this.messages.push(`http call for: ${term}`));
  }

  search(term: string) {
    this.searchTermStream$.next(term);
  }
}
```

Result

- Type `angular` quickly in the searchbox
- Ouput is shown below the input on the page

```
http call for: angular
```

<div style="page-break-after: always;"></div>

#### distinctUntilChanged

Try

- Type `angular` quickly in the searchbox
- Delete the last letter `r` then retype the `r`
- Ouput is shown below the input on the page

Result

```
http call for: angular

http call for: angula

http call for: angular
```

<div style="page-break-after: always;"></div>

```diff
import { Component, OnInit } from '@angular/core';
import { Subject } from 'rxjs';
import { debounceTime,
+ distinctUntilChanged } from 'rxjs/operators';

@Component({
  selector: 'app-root',
  template: `
    <input
      type="text"
      #term
      (keyup)="search(term.value)"
      placeholder="search"
    />
    <br />
    <p *ngFor="let message of messages">{{ message }}</p>
  `,
  styles: []
})
export class AppComponent implements OnInit {
  messages: string[] = [];
  private searchTermStream$ = new Subject<string>();

  ngOnInit(): void {
    this.searchTermStream$
      .pipe(
+        debounceTime(1000),
+       distinctUntilChanged()
      )
      .subscribe(term => this.messages.push(`http call for: ${term}`));
  }

  search(term: string) {
    this.searchTermStream$.next(term);
  }
}
```

<div style="page-break-after: always;"></div>

Try

- Type `angular` quickly in the searchbox
- Delete the last letter `r` then retype the `r`
- Ouput is shown below the input on the page

Result

```
http call for: angular
```

Notice that I increased the debounceTime so that it's easier to retype the letters you removed.

<div style="page-break-after: always;"></div>

#### switchMap

Cancels the orginal obervable and returns a new one

```diff
this.searchTermStream$
      .pipe(
        debounceTime(1000),
        distinctUntilChanged(),
+        switchMap((term: string) => {
+          return of(`new observable: ${term}`);
+        })
      )
      .subscribe(term => this.messages.push(` ${term}`));
```

<div style="page-break-after: always;"></div>

## Additional Reading

- [Understanding RxJS BehaviorSubject, ReplaySubject and AsyncSubject](https://medium.com/@luukgruijs/understanding-rxjs-behaviorsubject-replaysubject-and-asyncsubject-8cc061f1cfc0)
- [Persist Login Status with Behavior Subject](https://netbasal.com/angular-2-persist-your-login-status-with-behaviorsubject-45da9ec43243)
