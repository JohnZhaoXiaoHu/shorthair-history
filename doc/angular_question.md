# 常见问题
> 记录整个项目大部分的问题，便于回溯

## Template parse errors:Can't bind to 'ngModel' since it isn't a known property of 'input'
> Although ngModel is a valid Angular directive, it isn't available by default.
It belongs to the optional FormsModule and you must opt-in to using it.

缺少模块声明，并引入

````js
import { FormsModule } from '@angular/forms';

@NgModule({
  imports: [
    BrowserModule, FormsModule
  ]
})
````

## Template parse errors:'router-outlet' is not a known element:
导入RouterOutlet即可解决，但最好导出RouterModule解决，因为可能还会缺失其他指令
````
import { RouterModule, Routes } from '@angular/router';
@NgModule({
  exports: [
    RouterModule
  ]
})
````

## angular.json 怎么配置
[https://github.com/angular/angular-cli/wiki/angular-workspace](https://github.com/angular/angular-cli/wiki/angular-workspace)

如：css更改scss解析
````json
"schematics": {
    "@schematics/angular:component": {
      "styleext": "scss"
    }
  }
````

## [不要在constructor调用某些function做初始化](https://angular.io/tutorial/toh-pt4#call-it-in-ngoninit)
构造函数简单用于属性赋值，不应该做方法的调用。甚至异步http等请求。
最佳实践放在 **lifecycle hook** 处理。

## 如何引入UI库（ng-zorro）
执行如下命令即可，建议在新建项目时执行，因为会对原有项目部分文件自动做修改
````
ng add ng-zorro-antd
````
当然也可以通过 npm install xx来执行，需要自行配置，[点击查看官网即可](https://ng.ant.design/docs/getting-started/zh)

## Property 'xxx' does not exist on type 'XXComponent'
注意ts中constructor和属性的用法。
````js
// 添加private public 等修饰符 ...
constructor(private route: ActivatedRoute) { }
foo(){
  this.route //is ok
}
````

## [如何监听Router的变化](https://angular.io/api/router/Router#properties)
Router.events

## More than one component matched on this element.
ng-zorro的tr和checkbox不可写在一起

## If ngModel is used within a form tag, either the name attribute must be set or the form control must be defined as 'standalone' in ngModelOptions
form 标签中需要如果有ngModel，则必须再添加name属性

## router定义无错，但访问xx链接无法更新xxComponent
考虑是否路由中有类似 **:id**的配置规则，会被优先匹配

## 怎么做proxy
angular.json
定义proxy文件，proxy.conf.json中和webpack proxy类似配置即可
````js
"serve": {
  "builder": "@angular-devkit/build-angular:dev-server",
  "options": {
    "browserTarget": "app-client:build",
    "proxyConfig": "src/proxy.conf.json"
  }
}
````

## Error: Cannot match any routes. URL Segment: 'page/detail'
区分restful参数和query参数在路由配置上的区别
````
[routerLink]="['/page/detail']" [queryParams]="{id:id}" //not i need
[routerLink]="['/page/detail/'+item.id]"
````

## Invalid configuration of route 'page': redirectTo and children cannot be used together
在父路由中重定向到其子路由？
````
{
  path: 'page',
  data: {
    breadcrumb: '页面管理'
  },
  children: [{
    path: '',
    redirectTo: 'pages',
    pathMatch: 'full'
  }, {
    path: 'pages',
    component: PageListComponent,
    data: {
      breadcrumb: '页面列表'
    }
  },
}
````

## [改变ngModule策略](https://angular.cn/api/core/ChangeDetectionStrategy)
[https://alligator.io/angular/change-detection-strategy/](https://alligator.io/angular/change-detection-strategy/)
重写对象，改变引用
````
Object.assign({}, this.board);
````

## 子组件如何调用父组件方法
parent compontent html
````html
<nz-col nzSpan="12">
  <app-draw-board (testChildEmit)="testParent($event)"></app-draw-board>
</nz-col>
````

parent compontent js
````js
testParent ($event) {
  console.log($event);
}
````

child compontent html
````html
<button (click)="testChildHandle()">child compontent click</button>
````

child compontent js
````js
testChildHandle () {
  console.log('child click');
  this.testChildEmit.emit('child say hello');
}
````

## 如何定义Dynamic Component
[使用@ViewChild装饰器](https://angular.io/api/core/ViewChild)

## Error: ExpressionChangedAfterItHasBeenCheckedError: Expression has changed after it was checked. Previous value: 'ngIf: undefined'. Current value: 'ngIf: true'. It seems like the view has been created after its parent and its children have been dirty checked. Has it been created in a change detection hook ?
[https://github.com/angular/angular/issues/14748#issuecomment-312224737](https://github.com/angular/angular/issues/14748#issuecomment-312224737)
[https://github.com/angular/angular/issues/17572](https://github.com/angular/angular/issues/17572)

angular有自己一套渲染数据的方式，按照life cycle，从父组件到子组件传递。
这个错，大致由于父子组件在初始的时候已经加载好数据，但由于业务需要在错误的生命周期中又再次赋值，导致数据渲染不符合angular的逻辑（比如 **ngAfterViewInit** ）；
解决方法社区已给出，大致在何时的life cycle做处理，或者把业务逻辑改成异步，跳过angular的检查机制。

## 动态组件如何做父子通讯
使用Observable观察者模式，并且定义在Service确保监听的唯一
动态组件有ComponentRef，可以使用熟悉instance调用到动态子组件的数据

