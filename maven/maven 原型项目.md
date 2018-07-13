## maven 原型项目

> 不想每次都从新搭建项目，可以用maven来做个模板项目

1. 找一个已经搭建好的maven项目，在这给项目下执行

   ```sh
   mvn archetype:create-from-project 
   ```
2. 在target目录下执行

```sh
mvn install
```
   执行完之后你就可以使用你的模板了
   
3. 构建你的项目
执行下面的语句时一定要是在空目录下执行
```sh
mvn archetype:generate -DarchetypeCatalog=local
```
执行成功你的项目出现了。