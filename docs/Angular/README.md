## 搭建项目

### 本地环境
```shell
nodejs 10.9 + 
npm 
```

### ng 命令
```shell
// 全局安装 angular-cli
npm install -g @angular/cli

// 创建项目
ng new my-app

// 运行项目
ng serve

// 构建项目(预编译)
ng build --prod --aot

// 创建 component
ng generate component XXX
ng g component XXX

// 创建 service
ng g service XXX

// 创建 module + routing
ng g module XXX --routing

// 创建 interceptor 请求拦截器
ng g interceptor XXX

// 创建管道 pipe
ng g pipe XXX

// 创建指令 directive
ng g directive XXX
```

### angular项目目录结构
参考  
- [如何更好地组织Angular项目](https://zhuanlan.zhihu.com/p/63515048)  
- [ng-alain](https://github.com/ng-alain/ng-alain)  
```
├── node_modules
├── e2e
├── src
│   ├── app
│   │   ├── core                                # 核心模块
│   │   │   ├── i18n                            # 国际化
│   │   │   ├── net
│   │   │   │   └── default.interceptor.ts      # 默认HTTP拦截器
│   │   │   ├── services
│   │   │   │   └── startup.service.ts          # 初始化项目配置
│   │   │   └── core.module.ts                  # 核心模块文件
│   │   ├── layout                              # 通用布局
│   │   │   ├── header                          # 头部
│   │   │   ├── footer                          # 底部
│   │   │   └── shared.module.ts                # 通用布局文件
│   │   ├── routes
│   │   │   ├── **                              # 业务目录
│   │   │   ├── routes.module.ts                # 业务路由模块
│   │   │   └── routes-routing.module.ts        # 业务路由注册口
│   │   ├── shared                              # 共享模块
│   │   │   ├── pipe                            # 管道
│   │   │   ├── utils                           # 通用小组件
│   │   │   └── shared.module.ts                # 共享模块文件
│   │   ├── app.component.ts                    # 根组件
│   │   └── app.module.ts                       # 根模块
│   │   └── delon.module.ts                     # @delon模块导入
│   ├── assets                                  # 本地静态资源
│   ├── environments                            # 环境变量配置
│   ├── styles                                  # 样式目录
│   ├── index.html
│   ├── main.ts
│   └── style.scee
├── angular.json                                # angular 配置文件
├── proxy.config.json                           # 代理配置
├── tsconfig.json                               # ts 配置文件
├── tsconfig.app.jaon
└── README.md
```

### nginx代理部署

使用 try_files 指向 index.html，详细描述见[Web 应用的前端控制器模式](https://www.nginx.com/resources/wiki/start/topics/tutorials/config_pitfalls/#front-controller-pattern-web-apps)。

```nginx
try_files $uri $uri/ /index.html;
```

参考资料：
- [手把手搭建nginx服务器，部署前端代码](https://segmentfault.com/a/1190000017940311)
- [Nginx + Node + Vue 部署初试](https://juejin.im/post/5c57c8be6fb9a049e55411e7)

完整代码 
```nginx
worker_processes  1;

events {
    worker_connections  1024;
}

http {

    # 开启gzip压缩
    gzip  on;
    # 启用gzip压缩的最小文件，小于设置值的文件将不会压缩
    gzip_min_length 1k;
    # gzip 压缩级别，1-9，数字越大压缩的越好，也越占用CPU时间
    gzip_comp_level 9;
    # 进行压缩的文件类型。javascript有多种形式。其中的值可以在 mime.types 文件中找到。
    gzip_types text/plain application/json application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png application/vnd.ms-fontobject font/ttf font/opentype font/x-woff image/svg+xml;
    # 是否在http header中添加Vary: Accept-Encoding，建议开启
    gzip_vary on;

    server {
        listen          4201;
        server_name     localhost;
        root            /project_path;
        index           index.html;
        location / {
            try_files $uri $uri/ /index.html;
            index   index.html;
        }

        location /dapi/ {
            proxy_pass http://172.0.x.x:80;
        }
    }
}
```
