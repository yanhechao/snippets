## maven 使用模板项目

> 不想每次都从新搭建项目，可以用maven来做个模板项目

1. 找一个已经搭建好的maven项目，在这给项目下执行

```sh
D:\project\springmvc\springmvc-helloworld
mvn archetype:create-from-project 
```
2. 在`target\generated-sources\archetype`目录下执行

```sh
mvn install
```
   执行完之后你就可以使用你的模板了

3. 构建你的项目
  ​
```sh
mvn archetype:generate -DarchetypeCatalog=local
```
> **备注:**执行下面的语句时一定要是在空目录下执行

```sh
Choose archetype:
1: local -> SpringTest:SpringTest-archetype (SpringTest-archetype)
Choose a number or apply filter (format: [groupId:]artifactId, case sensitive contains): : 1
```
选择1



```sh
Define value for property 'groupId': com.k
Define value for property 'artifactId': lll
Define value for property 'version' 1.0-SNAPSHOT: :
Define value for property 'package' com.k: : i
```
输入groupId,artifactId,version,package

执行成功你的项目出现了。