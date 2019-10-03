# Lifecycle Hooks

A component has a lifecycle managed by Angular.

Angular **creates** and renders components along with their children, checks when their data-bound *properties* **change**, and **destroys** them before removing them from the DOM.

Angular offers **lifecycle hooks** that provide visibility into these key life moments and the ability to act when they occur.



## Starting Point

Open `demos\start\` directory.

```
git checkout input-property -f
git clean -df
```

## Changes

#### `src\app\app.component.ts`

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <app-fruit-list [fruits]="data"></app-fruit-list>
    <button (click)="onClickChange()">Change List</button>
  `,
  styles: []
})
export class AppComponent {
  data: string[] = ['Apple', 'Orange', 'Plum'];
  onClickChange() {
    this.data = ['Banana', 'Kiwi', 'Grape'];
  }
}
```

#### `src\app\fruit-list\fruit-list.component.ts`

```ts
import {
  Component,
  OnInit,
  Input,
  OnChanges,
  SimpleChanges
} from '@angular/core';

@Component({
  selector: 'app-fruit-list',
  template: `
    <ul>
      <li *ngFor="let fruit of fruits">
        {{ fruit }}
      </li>
    </ul>
  `,
  styles: []
})
export class FruitListComponent implements OnInit, OnChanges {
  @Input()
  fruits: string[];
  constructor() {
    console.log('Constructor');
  }
  ngOnChanges(changes: SimpleChanges): void {
    console.log('OnChanges');
    console.log('Previous Values: ' + changes.fruits.previousValue);
    console.log('Current Values: ' + changes.fruits.currentValue);
  }

  ngOnInit() {
    console.log('OnInit');
  }
}
```

#### Results

Refresh Page:

```
Constructor
OnChanges
Previous Values: undefined
Current Values: ["Apple", "Orange", "Plum"]
OnInit
```

Click Change List:

```
OnChanges
Previous Values: ["Apple", "Orange", "Plum"]
Current Values: ["Banana", "Kiwi", "Grape"]
```

## Destroy

#### `src\app\app.component.ts`

```diff
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <app-fruit-list [fruits]="data"
+   *ngIf="showList"></app-fruit-list>
    <button (click)="onClickChange()">Change List</button>
+    <br />
+    <button (click)="onClickRemove()">Remove</button>
  `,
  styles: []
})
export class AppComponent {
  data: string[] = ['Apple', 'Orange', 'Plum'];
  showList = true;
  onClickChange() {
    this.data = ['Banana', 'Kiwi', 'Grape'];
  }
+  onClickRemove() {
+    this.showList = !this.showList;
+  }
}
```

#### `src\app\fruit-list\fruit-list.component.ts`

```diff
import {
  Component,
  OnInit,
  Input,
  OnChanges,
  SimpleChanges,
+  OnDestroy
} from '@angular/core';

@Component({
  selector: 'app-fruit-list',
  template: `
    <ul>
      <li *ngFor="let fruit of fruits">
        {{ fruit }}
      </li>
    </ul>
  `,
  styles: []
})
export class FruitListComponent implements OnInit, OnChanges,
+ OnDestroy {
  @Input()
  fruits: string[];
  constructor() {
    console.log('Constructor');
  }
  ngOnChanges(changes: SimpleChanges): void {
    console.log('OnChanges');
    console.log('Previous Values: ' + changes.fruits.previousValue);
    console.log('Current Values: ' + changes.fruits.currentValue);
  }
  ngOnInit() {
    console.log('OnInit');
  }
+  ngOnDestroy(): void {
+    console.log('Destroyed');
+  }
}
```

#### Results

Click Remove

```
Destroyed
```

## Final Code

#### `src\app\app.component.ts`

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
    <app-fruit-list [fruits]="data" *ngIf="showList"></app-fruit-list>
    <button (click)="onClickChange()">Change List</button>
    <br />
    <button (click)="onClickRemove()">Remove</button>
  `,
  styles: []
})
export class AppComponent {
  data: string[] = ['Apple', 'Orange', 'Plum'];
  showList = true;
  onClickChange() {
    this.data = ['Banana', 'Kiwi', 'Grape'];
  }
  onClickRemove() {
    this.showList = !this.showList;
  }
}
```

#### `src\app\fruit-list\fruit-list.component.ts`

```ts
import {
  Component,
  OnInit,
  Input,
  OnChanges,
  SimpleChanges,
  OnDestroy
} from '@angular/core';

@Component({
  selector: 'app-fruit-list',
  template: `
    <ul>
      <li *ngFor="let fruit of fruits">
        {{ fruit }}
      </li>
    </ul>
  `,
  styles: []
})
export class FruitListComponent implements OnInit, OnChanges, OnDestroy {
  @Input()
  fruits: string[];
  constructor() {
    console.log('Constructor');
  }
  ngOnChanges(changes: SimpleChanges): void {
    console.log('OnChanges');
    console.log('Previous Values: ' + changes.fruits.previousValue);
    console.log('Current Values: ' + changes.fruits.currentValue);
  }
  ngOnInit() {
    console.log('OnInit');
  }
  ngOnDestroy(): void {
    console.log('Destroyed');
  }
}
```


## Reference

- [Lifecycle Hooks Documentation](https://angular.io/guide/lifecycle-hooks)
