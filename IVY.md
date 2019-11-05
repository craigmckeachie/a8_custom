# IVY

Ivy is the code name for Angular's [next-generation compilation and rendering pipeline](https://blog.angular.io/a-plan-for-version-8-0-and-ivy-b3318dfc19f7). Starting with Angular version 8, you can choose to opt in to start using a preview version of Ivy and help in its continuing development and tuning.

> To preview Ivy, use `@angular/core@next` version of Angular (8.1.x), rather than `@angular/core@latest` (8.0.x), as it contains all the latest bug fixes and improvements. To do this in an Angular CLI project use the following commands:

```shell
npm uninstall @angular/cli -g 
npm install @angular/cli@next -g 
```

## Is Ivy Ready?

[Click here to see the status of Ivy](https://is-angular-ivy-ready.firebaseapp.com/#/status)

## Using Ivy in a new project

To start a new project with Ivy enabled, use the `--enable-ivy` flag with the [`ng new`](cli/new) command:

```sh
ng new shiny-ivy-app --enable-ivy
```

The new project is automatically configured for Ivy. Specifically, the enableIvy option is set to `true` in the project's `tsconfig.app.json` file.

## Using Ivy in an existing project

To update an existing project to use Ivy, set the `enableIvy` option in the `angularCompilerOptions` in your project's `tsconfig.app.json`.

```json
{
  "compilerOptions": { ... },
  "angularCompilerOptions": {
    "enableIvy": true
  }
}
```

AOT compilation with Ivy is faster and should be used by default. In the `angular.json` workspace configuration file, set the default build options for your project to always use AOT compilation.

```json
{
  "projects": {
    "my-existing-project": {
      "architect": {
        "build": {
          "options": {
            ...
            "aot": true,
          }
        }
      }
    }
  }
}


To stop using the Ivy compiler, set `enableIvy` to `false` in `tsconfig.app.json`, or remove it completely. Also remove `"aot": true` from your default build options if you didn't have it there before.
```

## Resources

- [A Plan for Version 8 & Ivy](https://blog.angular.io/a-plan-for-version-8-0-and-ivy-b3318dfc19f7)
- [Ivy Official Documentation](https://angular.io/guide/ivy)

- [Ivy Architecture](https://github.com/angular/angular/blob/master/packages/compiler/design/architecture.md)

- [The New Ivy Compiler Finally Works on Windows](https://levelup.gitconnected.com/the-new-angular-ivy-compiler-finally-works-on-windows-9042378cede0)

- [What is Angular Ivy](https://blog.ninja-squad.com/2019/05/07/what-is-angular-ivy/)

- [Exploring the new Ivy Compiler (slightly outdated)](https://blog.angularindepth.com/inside-ivy-exploring-the-new-angular-compiler-ebf85141cee1)
