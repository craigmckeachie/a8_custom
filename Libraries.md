# Libraries

1. Run the commands:

   ```
   ng new my-workspace --createApplication="false"
   cd my-workspace
   ng generate application my-first-app
   ng generate library my-lib
   ng build my-lib
   ```

1. Import `MyLibModule` in the app.

   #### `my-first-app\src\app\app.module.ts`

   ```diff
   import { BrowserModule } from '@angular/platform-browser';
   import { NgModule } from '@angular/core';

   import { AppComponent } from './app.component';
   + import { MyLibModule } from 'my-lib';

   @NgModule({
     declarations: [AppComponent],
     imports: [BrowserModule
   +  ,MyLibModule
     ],
     providers: [],
     bootstrap: [AppComponent]
   })
   export class AppModule {}
   ```

1. Delete the html in the AppComponent template and add the component from `my-lib`.

   #### `my-first-app\src\app\app.component.html`

   ```html
   <lib-my-lib></lib-my-lib>
   ```

1. Change the lib component.
1. Notice the app doesn't update.
1. Rebuild and the app updates.

   ```
   ng build my-lib
   ```

   > No need to stop and start `ng serve`

   OR

   ```
   ng build my-lib --watch
   ```

   > Library triggers rebuild automatically when changed.

## Issues

If you receive this error after running the command `ng build my-lib`:

```
BrowserslistError: Unknown version 67 of android
```

- Delete `node_modules` and `package-lock.json`
- Ensure these versions:
  ```json
    "devDependencies": {
    "@angular-devkit/build-angular": "^0.801.3",
    "@angular-devkit/build-ng-packagr": "^0.801.3",
    ...
  ```
- Run `npm install` again.
- [See this github issue for more information](https://github.com/ng-packagr/ng-packagr/issues/1411)

## Reference

- [File Structure: Library Project Files](https://angular.io/guide/file-structure#library-project-files)
- [Creating Libraries Official Documentation](https://angular.io/guide/creating-libraries)
- [Using npm Link](https://medium.com/dailyjs/how-to-use-npm-link-7375b6219557)
- [Creating Your Own Libraries with the Angular CLI](https://blog.angulartraining.com/create-your-own-libraries-with-angular-cli-7b434600bbb7)

```

```
