# 常见问题
## Template parse errors:Can't bind to 'ngModel' since it isn't a known property of 'input'
Although ngModel is a valid Angular directive, it isn't available by default.
It belongs to the optional FormsModule and you must opt-in to using it.

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

## (不要在constructor调用某些function做初始化)[https://angular.io/tutorial/toh-pt4#call-it-in-ngoninit]
构造函数简单用于属性赋值，不应该做方法的调用。甚至异步http等请求。
最佳实践放在lifecycle hook处理。

## 如何引入UI库（ng-zorro）
执行如下命令即可，建议在新建项目时执行，因为会对原有项目部分文件自动做修改
````
ng add ng-zorro-antd
````
当然也可以通过 npm install xx来执行，需要自行配置，[点击查看官网即可](https://ng.ant.design/docs/getting-started/zh)