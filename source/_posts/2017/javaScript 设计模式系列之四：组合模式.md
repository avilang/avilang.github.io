---
title: javaScript 设计模式系列之四：组合模式
date: 2017-10-19
---

### 介绍

组合模式(Composite Pattern)：组合多个对象形成树形结构以表示具有“整体—部分”关系的层次结构。组合模式对单个对象（即叶子对象）和组合对象（即容器对象）的使用具有一致性，组合模式又可以称为“整体—部分”(Part-Whole)模式，它是一种对象结构型模式。

![](/myimages/2017-10-19_01.jpg "组合模式结构图")

在组合模式结构图中包含如下几个角色：
- Component（抽象构件）：它可以是接口或抽象类，为叶子构件和容器构件对象声明接口，在该角色中可以包含所有子类共有行为的声明和实现。在抽象构件中定义了访问及管理它的子构件的方法，如增加子构件、删除子构件、获取子构件等。
- Leaf（叶子构件）：它在组合结构中表示叶子节点对象，叶子节点没有子节点，它实现了在抽象构件中定义的行为。对于那些访问及管理子构件的方法，可以通过异常等方式进行处理。
- Composite（容器构件）：它在组合结构中表示容器节点对象，容器节点包含子节点，其子节点可以是叶子节点，也可以是容器节点，它提供一个集合用于存储子节点，实现了在抽象构件中定义的行为，包括那些访问及管理子构件的方法，在其业务方法中可以递归调用其子节点的业务方法。

### 例子：餐厅菜单的应用

```js
// "抽象构件"
var MenuComponent = function(){
};
MenuComponent.prototype.getName = function(){
  throw new Error("This method must be overwritten!");
};
MenuComponent.prototype.getDescription = function(){
  throw new Error("This method must be overwritten!");
};
MenuComponent.prototype.getPrice = function(){
  throw new Error("This method must be overwritten!");
};
MenuComponent.prototype.isVegetarian = function(){
  throw new Error("This method must be overwritten!");
};
MenuComponent.prototype.print = function(){
  throw new Error("This method must be overwritten!");
};
MenuComponent.prototype.add = function(){
  throw new Error("This method must be overwritten!");
};
MenuComponent.prototype.remove = function(){
  throw new Error("This method must be overwritten!");
};
MenuComponent.prototype.getChild = function(){
  throw new Error("This method must be overwritten!");
};
```

```js
// "叶子构件"
var MenuItem = function(sName, sDescription, bVegetarian, nPrice){
  MenuComponent.apply(this);		
  this.sName = sName;
  this.sDescription = sDescription;
  this.bVegetarian = bVegetarian;
  this.nPrice = nPrice;
};
MenuItem.prototype = new MenuComponent();
MenuItem.prototype.getName = function(){
  return this.sName;
};
MenuItem.prototype.getDescription = function(){
  return this.sDescription;
};
MenuItem.prototype.getPrice = function(){
  return this.nPrice;
};
MenuItem.prototype.isVegetarian = function(){
  return this.bVegetarian;
};
MenuItem.prototype.print = function(){
  console.log(this.getName() + ": " + this.getDescription() + ", " + this.getPrice() + "euros");
};
```

```js
// "容器构件"
var Menu = function(sName, sDescription){
  MenuComponent.apply(this);
  this.aMenuComponents = [];
  this.sName = sName;
  this.sDescription = sDescription;
  this.createIterator = function(){
    throw new Error("This method must be overwritten!");
  };
};
Menu.prototype = new MenuComponent();
Menu.prototype.add = function(oMenuComponent){
  this.aMenuComponents.push(oMenuComponent);
};
Menu.prototype.remove = function(oMenuComponent){
  var aMenuItems = [];
  var nMenuItem = 0;
  var nLenMenuItems = this.aMenuComponents.length;
  var oItem = null;

  for(; nMenuItem < nLenMenuItems;){
    oItem = this.aMenuComponents[nMenuItem];
    if(oItem !== oMenuComponent){
      aMenuItems.push(oItem);
    }
    nMenuItem = nMenuItem + 1;
  }
  this.aMenuComponents = aMenuItems;
};
Menu.prototype.getChild = function(nIndex){
  return this.aMenuComponents[nIndex];
};
Menu.prototype.getName = function(){
  return this.sName;
};
Menu.prototype.getDescription = function(){
  return this.sDescription;
};
Menu.prototype.print = function(){
  console.log(this.getName() + ": " + this.getDescription());
  console.log("--------------------------------------------");

  var nMenuComponent = 0;
  var nLenMenuComponents = this.aMenuComponents.length;
  var oMenuComponent = null;

  for(; nMenuComponent < nLenMenuComponents;){
    oMenuComponent = this.aMenuComponents[nMenuComponent];
    oMenuComponent.print();
    nMenuComponent = nMenuComponent + 1;
  }
};
```

```js
// "指定具体容器"
var DinnerMenu = function(){
  Menu.apply(this);
};
DinnerMenu.prototype = new Menu();

var CafeMenu = function(){
  Menu.apply(this);
};
CafeMenu.prototype = new Menu();

var PancakeHouseMenu = function(){
  Menu.apply(this);
};
PancakeHouseMenu.prototype = new Menu();
```

```js
// "顶级容器"
var Mattress = function(aMenus){
  this.aMenus = aMenus;
};
Mattress.prototype.printMenu = function(){
  this.aMenus.print();
};
```

```js
// 调用
var oPanCakeHouseMenu = new Menu("Pancake House Menu", "Breakfast");
var oDinnerMenu = new Menu("Dinner Menu", "Lunch");
var oCoffeeMenu = new Menu("Cafe Menu", "Dinner");
var oAllMenus = new Menu("ALL MENUS", "All menus combined");
oAllMenus.add(oPanCakeHouseMenu);
oAllMenus.add(oDinnerMenu);
oDinnerMenu.add(new MenuItem("Pasta", "Spaghetti with Marinara Sauce, and a slice of sourdough bread", true, 3.89));
oDinnerMenu.add(oCoffeeMenu);
oCoffeeMenu.add(new MenuItem("Express", "Coffee from machine", false, 0.99));
var oMattress = new Mattress(oAllMenus);
console.log("---------------------------------------------");
oMattress.printMenu();
console.log("---------------------------------------------");
```

### 相关阅读
[Design-Patterns-in-Javascript-Composite](https://github.com/tcorral/Design-Patterns-in-Javascript/tree/master/Composite)
[树形结构的处理——组合模式](http://wiki.jikexueyuan.com/project/design-pattern-structurized/composite-one.html)