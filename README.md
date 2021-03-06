## 复用的种类和目的：

复用的目的是为了增加可靠性，降低风险和成本。

软件复用有几个等级：

**源代码复用**，直接copy其他项目的源代码，然后再根据当前项目需求进行修改

缺点：导致很多版本，维护成本高。

 

**模块复用**，将可复用的部分封装成一个模块，模块有自己的维护周期，和项目独立。

优点：和项目解耦合，方便维护和复用

缺点：需要暴露接口，并设计起来难度大

 

其他复用有组件复用、服务复用，等级越高越抽象

 

## 模块开发的步骤：

模块化开发的步骤：

- 先确定依赖的模块
- **设计好api（对外暴露的接口），要求api要望名知意**
- 写测试用例/ 开发
- 写使用文档、并配有例子
- 打包发布

注意点：

- 一定要与具体项目解耦，因为模块是各个项目通用的，不能依赖于具体项目
- **api一旦设计好，就不要变化，确保升级时候可以向下兼容**

## 模块化开发的方法：

先确定模块化的方式，目前前端的模块方法有：amd、cmd、commonjs、umd、es6模块化

这些方式基本都基于npm或者bower。我们选择基于npm的方案（commonjs）。

 

### 1.创建一个npm项目

```powershell
mkdir 项目名称
cd ./项目名称
npm init
```

一步一步将命令行完成，这样就能生成一个package.json的文件

### 2.安装依赖，将需要的依赖的依次安装。

```powershell
npm install babel --save
```

> `--save-dev` 是你开发时候依赖的东西，`--save` 是你发布之后还依赖的东西
>
> 因为我们开发的模块，是需要被其他模块安装的。使用`--save`安装第三方模块的时候，安装我们模块时候会自动将第三方模块也安装了。

### 3.开发组件

**必须先设计api**。然后可以先开发，再写测试用例；也可以先写测试用例，再开发。开发实现之后，将开发的api暴露出来，我们可以将需要暴露的对象放到一个指定的js文件中，在package.json中，有main这个配置项，这个配置项设置为这个js文件。

如果项目需要编译，需要先编译，并将main配置到编译出这个js文件上面。

### 4.将资源提交到git上面

应该部署在企业内部的npm仓库中，但是因为目前没有，所有我们先部署在git上面。部署到git时候，需要在git上面打上标签。

### 5.安装使用

在使用方项目中执行

```powershell
npm install git的路径
```

### 6.版本维护

使用NPM下载和发布代码时都会接触到版本号。NPM使用语义版本号来管理代码，这里简单介绍一下。

语义版本号分为X.Y.Z三位，分别代表主版本号、次版本号和补丁版本号。当代码变更时，版本号按以下原则更新。

- 如果只是修复bug，需要更新Z位。
- 如果是新增了功能，但是向下兼容，需要更新Y位。
- 如果有大变动，向下不兼容，需要更新X位。

版本号有了这个保证后，在申明第三方包依赖时，除了可依赖于一个固定版本号外，还可依赖于某个范围的版本号。例如"argv": "0.0.x"表示依赖于0.0.x系列的最新版argv。

然后执行

```
npm version patch
```



### 7.使用方版本升级

当我们 的模块升了一个版本后，使用方可以上级，可以不升级。如果使用方向升级版本，需要执行：

```
npm upgrade 模块名
```

这样就升级了我们的版本。



## 联调

### 运行在node环境的npm包联调

当我们的项目存在bug需要修改的时候，往往需要在执行一遍开发模块补丁、模块升级、发布模块、升级模块这个过程，这样非常麻烦。

我们可以执行`npm link`来简化联调过程。

我们将我们模块的源代码目录中，执行

```
npm link
```

这样这个模块就部署到全局

在使用方项目中，执行

```
npm link 模块名
```

这样使用方模块就不在node_modules里面寻找，而是直接指向我们模块的源代码

调试好，重新发布模块，然后执行

```
npm unlink 模块名
npm isntall 模块名
```

### 运行在webpack环境的npm包联调

如果是由webpack打包，可以用更加简单联调。在webpack的配置文件里面，可以使用`resolve.alias`配置，让webpack引用包的时候使用`resolve.alias`后配置的路径。

```
resolve: {
    alias: {
        'my-module': path.join(__dirname, 'my-module的路径'),
    },
    ...
}
```
这样webpack会忽略掉node_modules的同名包，而去使用配置路径下的npm包

### 注意事项
无论使用npm link还是webpack的alias，都会产生一个问题 ———— 调试的npm包在使用require的时候，都会在他所在目录下寻找。例如我们开发的项目A，依赖我们联调的项目B，两个项目都依赖项目C。如果是在非联调状态下，项目A和项目B可以同时依赖同一个项目C(node6以上)；但是在联调状态下，项目A、B都会依赖他们自己项目下安装的项目C。也就是说项目C在内存中，会执行两次，如果项目C存在A和B共享的变量时候，就会出现错误。

## 测试用例

推荐使用 mocha和chai，对于样式的测试，对于带dom的测试，可以采用jsdom。做样式相关测试可以用backstopjs

 

 

 

 
