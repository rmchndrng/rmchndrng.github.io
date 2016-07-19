----------
* auto-gen TOC:
{:toc}

----------
##Introduction
I am trying to make use of the new Angular2 component router to implement a fairly complex application which strongly relies on it to maintain the states. 
The source code of the application can be found at https://github.com/rmchndrng/ddintong2router

###The Sample Application
The sample application's view is divided into 5 sections
1. Top
2. Bottom
3. Left
4. Right
5. Middle

Each section will have its own routing including child. Inorder to achieve this requirement, we make use of the Auxillary router-outlet directive.

The main app component template will contain the router-outlets

    app.component.html
    <div class="container-fluid">
        <div class="row">
            <div class="col-md-12">
                Top<router-outlet name="top"></router-outlet>
            </div>
        </div>
        <div class="row">
            <div class="col-md-4">Left<router-outlet name="left"></router-outlet></div>
            <div class="col-md-4">Middle<router-outlet></router-outlet></div>
            <div class="col-md-4">Right<router-outlet name="right"></router-outlet></div>
        </div>
        <div class="row">
            <div class="col-md-12">Bottom<router-outlet name="bottom"></router-outlet>
            </div>
        </div>
    </div>

The Middle section will hold the Primary router outlet and the rest each will contain the auxillary router outlets. 
The router outlet names are in sync with the section names mentioned above.

###Route configurations
The router configurations are as follows

    app.routes.ts
    import { provideRouter, RouterConfig } from '@angular/router';

    import {PrimaryRoutes} from './primary'
    import {LeftRoutes} from './left'
    import {RightRoutes} from './right'
    import {BottomRoutes} from './bottom'
    import {TopRoutes} from './top'

    export const routes: RouterConfig = [
    ...PrimaryRoutes,
    ...LeftRoutes,
    ...RightRoutes,
    ...BottomRoutes,
    ...TopRoutes  
    ];

    export const APP_ROUTER_PROVIDERS = [
    provideRouter(routes, {enableTracing: true})
    ];

    primary-routes.ts
    import { RouterConfig } from '@angular/router';
    import {P1Component} from './p1'
    import {P2Component} from './p2'
    import {P3Component} from './p3'

    export const PrimaryRoutes: RouterConfig = [
        { path: '', redirectTo: 'p1', pathMatch: 'full' },    
        { path: 'p1', component:P1Component },
        { path: 'p2', component:P2Component },
        { path: 'p3', component:P3Component },
        { path: '**', redirectTo: 'p1'},    
    ];

    bottom-routes.ts
    import { RouterConfig } from '@angular/router';
    import {B1Component} from './b1'
    import {B2Component} from './b2'
    import {B3Component} from './b3'

    export const BottomRoutes: RouterConfig = [
        { path: '', redirectTo: 'b1',  outlet: 'bottom', pathMatch:'full' },           
        { path: 'b1', component: B1Component, outlet: 'bottom' },
        { path: 'b2', component: B2Component , outlet: 'bottom'},
        { path: 'b3', component: B3Component , outlet: 'bottom'},
        { path: '**', redirectTo: 'b1', outlet:'bottom'},
    ];

    left-routes.ts
    import { RouterConfig } from '@angular/router';
    import {L1Component} from './l1'
    import {L2Component} from './l2'
    import {L3Component} from './l3'

    export const LeftRoutes: RouterConfig = [
        { path: '', redirectTo: 'l1',  outlet: 'left', pathMatch:'full' },
        { path: 'l1', component: L1Component, outlet: 'left' },
        { path: 'l2', component: L2Component , outlet: 'left'},
        { path: 'l3', component: L3Component , outlet: 'left'},
        { path: '**', redirectTo: 'l1', outlet: 'left'},
    ];

    right-routes.ts
    import { RouterConfig } from '@angular/router';
    import {R1Component} from './r1'
    import {R2Component} from './r2'
    import {R3Component} from './r3'

    export const RightRoutes: RouterConfig = [
        { path: '', redirectTo: 'r1',  outlet: 'right', pathMatch:'full' },            
        { path: 'r1', component: R1Component, outlet: 'right' },
        { path: 'r2', component: R2Component , outlet: 'right'},
        { path: 'r3', component: R3Component , outlet: 'right'},
        { path: '**', redirectTo: 'r1', outlet: 'right'},
    ];

    top-routes.ts
    import { RouterConfig } from '@angular/router';
    import {T1Component} from './t1'
    import {T2Component} from './t2'
    import {T3Component} from './t3'

    export const TopRoutes: RouterConfig = [
        { path: '', redirectTo: 't1',  outlet: 'top', pathMatch:'full' },            
        { path: 't1', component: T1Component, outlet: 'top' },
        { path: 't2', component: T2Component , outlet: 'top'},
        { path: 't3', component: T3Component , outlet: 'top'},
        { path: '**', redirectTo: 't1', outlet: 'top'},
    ];