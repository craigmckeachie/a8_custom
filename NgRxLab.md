# NgRx Lab

### Setup

1. **Copy** the folder `\code\labs\lab29\complete\project-manage` into your `working` directory.
2. In the `working\project-manage` directory run the command `npm install`.

### Installation

1. **Install** `@ngrx/schematics` from npm:

   ```
   ng add @ngrx/schematics
   ```

1. This will ask you if you want to make the `@ngrx/schematics` the **default** **collection** in your Angular CLI project. **Choose** `n` for **no** in this lab.

1) After installing @ngrx/schematics, **install** the NgRx **following** **library** dependencies.

   ```
   ng add @ngrx/store
   ng add @ngrx/store-devtools
   ng add @ngrx/effects
   ```

2. **Open** `src\app\app.module.ts` and notice that the `StoreModule` was automatically imported by the `ng add` commands and the Redux Dev Tools are configured.

### Actions

1. **Create** the `src\app\projects\shared` and `src\app\projects\shared\state` **directories**.
1. **Create** the **file** `src\app\projects\shared\state\project.actions.ts`.
1. **Add** the following **actions** using the `createAction` helper function.

   #### `src\app\projects\shared\state\project.actions.ts`

   ```ts
   import { createAction, props } from '@ngrx/store';
   import { Project } from '../project.model';

   export const load = createAction('[Project] Load');
   export const loadSuccess = createAction(
     '[Project] Load Success',
     props<{ projects: Project[] }>()
   );
   export const loadFail = createAction(
     '[Project] Load Fail',
     props<{ error: any }>()
   );

   export const save = createAction(
     '[Project] Save',
     props<{ project: Project }>()
   );
   export const saveSuccess = createAction(
     '[Project] Save Success',
     props<{ project: Project }>()
   );
   export const saveFail = createAction(
     '[Project] Save Fail',
     props<{ error: any }>()
   );
   ```

### State

1. **Create** the **file** `src\app\projects\shared\state\project.reducer.ts`.
2. Define the state for the slice of the state related to projects including:

   1. an interface
   2. initial or default values
   3. selectors to select different parts of state

   #### `src\app\projects\shared\state\project.reducer.ts`

   ```ts
   import { Project } from '../project.model';
   import { State } from 'src/app/reducers';

   export interface ProjectState {
     loading: boolean;
     saving: boolean;
     error: string;
     projects: Project[];
   }

   export const initialState = {
     loading: false,
     saving: false,
     error: '',
     projects: []
   };
   ```

### Reducer

1. Create the `projectReducer` using the `createReducer` helper function.

   #### `src\app\projects\shared\state\project.reducer.ts`

   ```ts
   ...
   import { createReducer, on } from '@ngrx/store';
   import {
   load,
   loadSuccess,
   loadFail,
   save,
   saveSuccess,
   saveFail
   } from './project.actions';

   const _projectReducer = createReducer(
   initialState,
   on(load, state => ({ ...state, loading: true })),
   on(loadSuccess, (state, { projects }) => ({
       ...state,
       projects,
       loading: false,
       saving: false
   })),
   on(loadFail, (state, { error }) => ({ ...state, error, loading: false })),
   on(save, state => ({ ...state, saving: true })),
   on(saveSuccess, (state, { project }) => {
       const updatedProjects = state.projects.map(item =>
       project.id === item.id ? project : item
       );
       return {
       ...state,
       projects: updatedProjects,
       saving: false
       };
   }),
   on(saveFail, (state, { error }) => ({ ...state, error, saving: false }))
   );

   export function projectReducer(state, action) {
   return _projectReducer(state, action);
   }

   ```

### Effects

1. Create the `ProjectEffects` class and the `load$` and `save$` effects using the `createEffect` helper function.

   #### `src\app\projects\shared\state\project.effects.ts`.

   ```ts
   import { Injectable } from '@angular/core';
   import { of } from 'rxjs';
   import { Actions, ofType, createEffect } from '@ngrx/effects';

   import { switchMap, catchError, map, mergeMap } from 'rxjs/operators';
   import { ProjectService } from '../project.service';
   import {
     load,
     loadSuccess,
     loadFail,
     save,
     saveSuccess,
     saveFail
   } from './project.actions';

   @Injectable()
   export class ProjectEffects {
     load$ = createEffect(() => {
       return this.actions$.pipe(
         ofType(load),
         switchMap(() => {
           return this.projectService.list().pipe(
             map(data => loadSuccess({ projects: data })),
             catchError(error => of(loadFail({ error: error })))
           );
         })
       );
     });

     save$ = createEffect(() => {
       return this.actions$.pipe(
         ofType(save),
         mergeMap(({ project }) => {
           return this.projectService.put(project).pipe(
             map(() => saveSuccess({ project })),
             catchError(error => of(saveFail({ error: error })))
           );
         })
       );
     });

     constructor(
       private actions$: Actions,
       private projectService: ProjectService
     ) {}
   }
   ```

### Configure

1. Add the project related state and reducer to the root state and reducer.

   #### `src\app\reducers\index.ts`.

   ```diff
   import {
   ActionReducer,
   ActionReducerMap,
   createFeatureSelector,
   createSelector,
   MetaReducer
   } from '@ngrx/store';
   import { environment } from '../../environments/environment';
   + import {
   +   ProjectState,
   +   projectReducer
   + } from '../projects/shared/state/project.reducer';

   export interface State {
   +  projectState: Project+State;
   }

   export const reducers: ActionReducerMap<State> = {
   +  projectState: projectReducer
   };

   export const metaReducers: MetaReducer<State>[] = !environment.production
   ? []
   : [];
   ```

### Connect the Container & Store

1. Refactor the `ProjectsContainerComponent` to use the `Store` instead of the `ProjectService` to access data. More specifically:

   1. Change all class members to be Observables.
   2. Inject the `Store` instead of the `ProjectService` in the component's constructor.
   3. Dispatch the `load` action in `ngOnInit` instead of calling the service directly.
   4. Dispatch the `save` action in the `onSaveListItem` event handler method instead of calling the service directly.

   #### `src\app\projects\projects-container\projects-container.component.ts`

   ```diff
   import { Component, OnInit } from '@angular/core';
   import { Project } from '../shared/project.model';
   - import { ProjectService } from '../shared/project.service';
   + import { Observable } from 'rxjs';
   + import { Store, select } from '@ngrx/store';
   + import { State } from 'src/app/reducers';
   + import { load, save } from '../shared/state/project.actions';
   + import {
   +   getProjects,
   +   getError,
   +   getLoading,
   +   getSaving
   + } from '../shared/state/project.reducer';

   @Component({
   selector: 'app-projects-container',
   templateUrl: './projects-container.component.html',
   styleUrls: ['./projects-container.component.css']
   })
   export class ProjectsContainerComponent implements OnInit {
   -  projects: Project[];
   -  errorMessage: string;
   -  loading: boolean;
   +  projects$: Observable<Project[]>;
   +  errorMessage$: Observable<string>;
   +  loading$: Observable<boolean>;
   +  saving$: Observable<boolean>;

   -  constructor(private projectService: ProjectService) {}
   +  constructor(private store: Store<State>) {}

   ngOnInit() {
   -    this.loading = true;
   -    this.projectService.list().subscribe(
   -      data => {
   -        this.loading = false;
   -        this.projects = data;
   -      },
   -      error => {
   -        this.loading = false;
   -        this.errorMessage = error;
   -      }
   -    );
   +    this.projects$ = this.store.pipe(select(getProjects));
   +    this.errorMessage$ = this.store.pipe(select(getError));
   +    this.loading$ = this.store.pipe(select(getLoading));
   +    this.saving$ = this.store.pipe(select(getSaving));
   +    this.store.dispatch(load());
   }

   onSaveListItem(event: any) {
       const project: Project = event.item;
   -    this.projectService.put(project).subscribe(
   -      updatedProject => {
   -        const index = this.projects.findIndex(
   -          element => element.id === project.id
   -        );
   -        this.projects[index] = project;
   -      },
   -      error => (this.errorMessage = error)
   -    );
   -  }
   +   this.store.dispatch(save({ project }));

   }

   ```

1. Verify the application functionality works as it did previously including:
   1. Loading and saving data.
   2. Handles errors appropriately when the backend is down.
   3. Displays loading and saving messages.
1. Lastly, verify you can now time travel.
   1. Open Chrome DevTools (F12 or fn+F12 on Windows)
   1. Install the [Redux DevTools Extension](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=en) if you haven't already.
   1. Use the application and then travel back in time and replay your actions.
