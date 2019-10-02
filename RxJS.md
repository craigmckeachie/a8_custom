# RxJS Examples
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

<!-- ### Subjects -->

<div style="page-break-after: always;"></div>

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
