# javascript-practice
前端编程题精选

# 1. 逆波兰表示法（Reverse Polish notation，RPN）

## 难易
  简单
## 题目描述

>逆波兰表示法是一种是由波兰数学家扬·武卡谢维奇1920年引入的数学表达式方式，在逆波兰记法中，所有操作符置于操作数的后面，因此也被称为后缀表示法。逆波兰记法不需要括号来标识操作符的优先级。逆波兰结构在1960年代早期被提议用于表达式求值，以利用堆栈结构减少计算机内存访问。在1960和1970年代，逆波兰记法广泛地被用于台式计算器，因此也在普通公众（工程、商业和金融领域）中使用。

逆波兰记法中，操作符置于操作数的后面。例如表达“三加四”时，写作“3 4 +”，而不是“3 + 4”。如果有多个操作符，操作符置于第二个操作数的后面，所以常规中缀记法的“3 - 4 + 5”在逆波兰记法中写作“3 4 - 5 +”：先3减去4，再加上5。使用逆波兰记法的一个好处是不需要使用括号。

例如 `5 1 2 + 4 * + 3 -` 表示 `5 + ((1 + 2) * 4) - 3` 最终结果为 14

请构造一个计算器，计算使用逆波兰表示法的数学算式.

注意事项：
- 1. 不用考虑除数为0等异常情况
- 2. 每一个操作数和运算符用空格隔开

## 代码模板
```JavaScript
function calc(expr) {
  // TODO: Your code here
  return 0;
}
```
## 答案

```JavaScript
function calc(expr) {
    if(!expr) return 0;
    var stack=[];
    expr.split(" ").forEach(function (item ) {
        if(Number(item)){
            stack.push(Number(item));
        }else{
                var r=stack.pop();
                var l=stack.pop();
                stack.push(eval(l+item+r));
        }
    });
    return stack.pop();
}
```

# 2. 编写一个简易的SQL引擎
## 难易
困难
## 问题描述：

> 用JavaScript 编写一个能够处理简单的Structured Query Language(SQL)的引擎，需要能够支持的查询语句有：
- SELECT - 从数据库表中获取数据
- FROM - 要查询的表名
- WHERE - 该子句用于规定选择的标准。
- JION（加分项） - 用于根据两个或多个表中的列之间的关系，从这些表中查询数据

>使用方法示例：
1. SELECT 列名称 FROM 表名称 WHERE 列 运算符 值
2. SELECT 列名称 FROM 表1名称 JION 表2名称 ON 表1名称.列名称 = 表2名称.列名称

## 代码模板
```JavaScript
function SQLEngine(database){

  this.execute = function(query){

  }

}
```
## 测试方法
```JavaScript
var movieDatabase = {
  movie: [
    { id: 1, name: 'Avatar', directorID: 1 },
    { id: 2, name: 'Titanic', directorID: 1 },
    { id: 3, name: 'Infamous', directorID: 2 },
    { id: 4, name: 'Skyfall', directorID: 3 },
    { id: 5, name: 'Aliens', directorID: 1 }
  ],
  actor: [
    { id: 1, name: 'Leonardo DiCaprio' },
    { id: 2, name: 'Sigourney Weaver' },
    { id: 3, name: 'Daniel Craig' },
  ],
  director: [
    { id: 1, name: 'James Cameron' },
    { id: 2, name: 'Douglas McGrath' },
    { id: 3, name: 'Sam Mendes' }
  ],
  actor_to_movie: [
    { movieID: 1, actorID: 2 },
    { movieID: 2, actorID: 1 },
    { movieID: 3, actorID: 2 },
    { movieID: 3, actorID: 3 },
    { movieID: 4, actorID: 3 },
    { movieID: 5, actorID: 2 },
  ]
};

describe('execution',function(){
  var engine = new SQLEngine(movieDatabase);

  it('should SELECT columns', function(){
    var actual = engine.execute('SELECT movie.name FROM movie');
    assertSimilarRows(actual, [{'movie.name':'Avatar'},
                                {'movie.name':'Titanic'},
                                {'movie.name':'Infamous'},
                                {'movie.name':'Skyfall'},
                                {'movie.name':'Aliens'}]);
  });

  it('should apply WHERE', function(){
    var actual = engine.execute('SELECT movie.name FROM movie WHERE movie.directorID = 1');
    assertSimilarRows(actual, [{'movie.name':'Avatar'},
                                {'movie.name':'Titanic'},
                                {'movie.name':'Aliens'}]);
  });

  it('should perform parent->child JOIN', function(){
    var actual = engine.execute('SELECT movie.name, director.name '
                               +'FROM movie '
                               +'JOIN director ON director.id = movie.directorID');
    assertSimilarRows(actual, [{'movie.name':'Avatar','director.name':'James Cameron'},
                                {'movie.name':'Titanic','director.name':'James Cameron'},
                                {'movie.name':'Aliens','director.name':'James Cameron'},
                                {'movie.name':'Infamous','director.name':'Douglas McGrath'},
                                {'movie.name':'Skyfall','director.name':'Sam Mendes'}]);
  });  

  it('should perform child->parent JOIN ', function(){
    var actual = engine.execute('SELECT movie.name, director.name '
                               +'FROM director '
                               +'JOIN movie ON director.id = movie.directorID');
    assertSimilarRows(actual, [{'movie.name':'Avatar','director.name':'James Cameron'},
                                {'movie.name':'Titanic','director.name':'James Cameron'},
                                {'movie.name':'Infamous','director.name':'Douglas McGrath'},
                                {'movie.name':'Skyfall','director.name':'Sam Mendes'},
                                {'movie.name':'Aliens','director.name':'James Cameron'}]);
  });

  it('should perform many-to-many JOIN and apply WHERE', function(){
    var actual = engine.execute('SELECT movie.name, actor.name '
                               +'FROM movie '
                               +'JOIN actor_to_movie ON actor_to_movie.movieID = movie.id '
                               +'JOIN actor ON actor_to_movie.actorID = actor.id '
                               +'WHERE actor.name <> \'Daniel Craig\'');
    assertSimilarRows(actual, [{'movie.name':'Aliens','actor.name':'Sigourney Weaver'},
                                {'movie.name':'Avatar','actor.name':'Sigourney Weaver'},
                                {'movie.name':'Infamous','actor.name':'Sigourney Weaver'},
                                {'movie.name':'Titanic','actor.name':'Leonardo DiCaprio'}]);
  });

});

function assertSimilarRows(actual, expected, message){
  function logFailed(m, rows){
    Test.expect(false, m +'<pre>' + rows.map(JSON.stringify).join(',\n') + '</pre>');
  }
  if(!actual || actual.length == 0 || !expected || expected.length == 0){
    return Test.assertSimilar(actual, expected, message);
  }  
  function allPropertiesInLeftInRight(a,b){
    return Object.keys(a).every(function(ak){ return a[ak] == b[ak]; })
  }
  function similarObjects(a,b){
    return allPropertiesInLeftInRight(a,b) && allPropertiesInLeftInRight(b,a);                     
  }
  function getRowsInLeftWhichAreNotInRight(left, right){
    return left.filter(function(r){
                  return !right.some(function(a){ return similarObjects(a,r); });
                });
  }
  var missingRowsInActual = getRowsInLeftWhichAreNotInRight(expected, actual),
      extraRowsInActual = getRowsInLeftWhichAreNotInRight(actual, expected);
  if(missingRowsInActual.length > 0){
    logFailed('Failure: expected result to include the following rows, but they were missing: ', missingRowsInActual);
    return;
  }
  if(extraRowsInActual.length > 0){
    logFailed('Failure: result contained the following rows which were not expected: ', extraRowsInActual);
    return;
  }

  Test.expect(true, message);
}
```

# 3.解析化学分子式中各个原子的个数
## 难易
中等
## 题目描述
> 完成函数parseMolecule()，解析任意形式的化学分子表达式，返回结果为一个对象

## 示例
注：该示例中未给出含有大括号{}的分子式，编写算法时应考虑到
```JavaScript
var water = 'H2O';
parseMolecule(water); // return {H: 2, O: 1}

var magnesiumHydroxide = 'Mg(OH)2';
parseMolecule(magnesiumHydroxide); // return {Mg: 1, O: 2, H: 2}

var fremySalt = 'K4[ON(SO3)2]2';
parseMolecule(fremySalt); // return {K: 4, O: 14, N: 2, S: 4}
```
## 考点
正则, 规约


## 答案
较好的解法
```JavaScript
function parseMolecule(formula) {
    var found, tmp,
        regexp = /(\(([^)}\]]+)\)|\[([^)}\]]+)\]|\{([^)}\]]+)\}|([A-Z][a-z]?))(\d+)/g;

    while (found = regexp.exec(formula)) {
        found   = found.filter(Boolean);
        tmp     = Array(+found[3] + 1).join(found[2]);
        formula = formula.replace(found[0], tmp);
        regexp.lastIndex = 0;   
    }

   return formula.match(/[A-Z][a-z]?/g).reduce(function(obj, val) {
       val in obj ? obj[val]++ : (obj[val] = 1);
       return obj;
   }, {});
}
```
较出色的解法
```JavaScript
function parseMolecule(formula) {
    var r1=/\((\w+)\)(\d{0,})/g;  //匹配小括号及后面可能出现的任意位数的数字
    var r2=/\[(\w+)\](\d{0,})/g;  //匹配中括号及后面可能出现的任意位数的数字
    var r3=/\{(\w+)\}(\d{0,})/g;  //匹配大括号及后面可能出现的任意位数的数字

    var formula = formula.replace(r1,repeat).replace(r2,repeat).replace(r3,repeat);//括号去掉完毕
    console.log(formula);
    var c_num=formula.match(/[A-Z][a-z]?\d{0,}/g);//输出类似[ 'K4', 'O', 'N', 'S', 'O3'， 'S']形式数组
    var res={}; //定义一个用来存储结果的对象
    c_num.forEach(function(c){
        var _c=c.match(/[A-Z][a-z]/)?c.split("")[0]+c.split("")[1]:c.split("")[0];//判断元素是否存在小写字母
        var num=c.match(/[0-9]$/)?parseInt(/\d+/.exec(c)[0]):1;//如果元素符号后存在数字则存入相应数字，否则存入1
        res[_c]=(res[_c]||0)+num;
    });
    return(res);
}
function repeat(m,p1,p2) {   //定义一个去掉括号的函数，在replace方法中回掉
    p2=parseInt(p2)-1;
    for(var temp=p1;p2--;temp+=p1);
    return temp;
}
```
# 4.判断给定的点是否在多边形内部
## 难易
中等
## 考察点
侧重算法

## 题目描述

完成函数`pointInPoly（ploy,point）`，如果该point在图形ploy内部，则返回true，否则返回false

用平面直角坐标系中的点[X,Y]表示ploy,point，即ploy为一个二维数组，point为一个长度为2的一维数组，不用考虑异常输入

示例： pointInPoly（[-5, -5], [5, -5], [0, 5]], [0,0]）返回true

## 解题思路

被测试的点做一条直线，将该直线与多边形的边的交点中，以被测点为界分开，若两侧的点均为奇数，则该点一定在图形内，否则在图外


## 代码示例

```JavaScript
//Return true if point is inside poly, and false if it is not
function pointInPoly(poly,point) {
    console.log(poly+point);
    var px=point[0],py=point[1];
    var x=poly.map((item)=>item[0]),
        y=poly.map((item)=>item[1]),
        xMax=x.reduce((x,y)=>x>y?x:y),
        xMin=x.reduce((x,y)=>x<y?x:y),
        yMax=y.reduce((x,y)=>x>y?x:y),
        yMin=y.reduce((x,y)=>x<y?x:y);
    if(px<xMin||px>xMax||py<yMin||py>yMax){
        return false;
    }
    x.push(x[0]);
    y.push(y[0]);
    var inside=false;
    for(i=0;i<x.length;i++){
        let j=i+1;
        if((px<x[i])!=(px<x[j])&&(py-y[i])<(px-x[i])*(y[j]-y[i])/(x[j]-x[i]))
        //判断该点是否在两边横坐标之间&&判断该点是否在交点上分（交点为过被测点做垂线，该垂线与边的交点）
        {
            inside=!inside;
        }
    }
    return inside;
}
```

# 5 .用函数式编程思维实现加法，乘法和指数乘法三个函数
## 难易
困难

## 题目描述

> 背景介绍见https://www.codewars.com/kata/church-numbers-add-multiply-exponents/train/javascript

给定的条件：

- 函数churchify：
使用方式：churchify(n:Number)(f:function)(x:number)，返回结果为`f performed on x n times ` ，即嵌套执行n次f(x)。该函数的实现方式很多，题目直接给出了一种实现：

 ```javascript
var churchify = (n) => (f) => (x) => {
      for(var i = 0; i < n; i++) x = f(x);
      return x;
    };
 ```
- 函数numerify:
 接收一个函数作为参数，这个函数是一个辅助函数，定义是这样的：

 ```javascript
 var numerify = (c) => c((i) => i + 1)(0);
 ```
- 函数tesChurch:
 这个函数很重要：

 ```javascript
 var testChurch = (fn, x, y) => numerify( fn( churchify(x) )( churchify(y) ) )
 ```

要求设计三个函数Add,Mul,Pow。使得：

- testChurch(Add,x,y)返回的结果为 x+y的结果
- testChurch(Mul,x,y)返回的结果为 x*y的结果
- testChurch(Pow,x,y)返回的结果为 x^y(x的y次方)的结果

另外题目还要求要考虑算法的复杂度，否则会超时，不要直接调用Math.pow方法。

## 参考答案

```javascript
var Add = (c1) => (c2) => (f) => (x) => c1(f)(c2(f)(x));
var Mul = (c1) => (c2) => (f) => (x) => c1((i)=>i+c2(f)(x))(x);
var Pow = (c1) => (c2) => (f) => (x) => c2((i)=>i*c1(f)(x))(1);
```
或
```JavaScript
var churchAdd = (c1) => (c2) => (f) => (x) => c1(f)(c2(f)(x));
var churchMul = (c1) => (c2) => (f) => (x) => c1(c2(f))(x);
var churchPow = (cb) => (ce) => (f) => (x) => ce(cb)(f)(x);
```
# 6.其他算法类编程题

推荐’卡特兰数’问题的变种 博客地址：http://blog.csdn.net/u010582082/article/details/69569237

该问题的同构问题有相当多，全是一个解法思路：

- n对括号 有多少种组合
- 矩阵链乘，依据乘法结合律，不改变其顺序，只用括号表示成对的乘积，有几种括号化的方案
- n个元素入栈 有多少种出栈顺序
- 凸多边形通过互不相交的对角线划分，求划分方案数
- 在圆上选择2n个点，将这些点连接起来，使得所得到的n条线段不相交的方法数
- 2n边的凸多边形，连接对角线 可以分出三角形的个数
- n × n格点中不越过对角线的单调路径的个数(上班路线选择问题)
- 给定n个节点组成二叉搜索树个数（或组成的二叉树形态数）
- 2n个高矮不同的人 站成两排 保证后排对应的人比前排高 每排从左到 右越来越高 有多少种排列方式
- 《编程之美》4.3中的买票找零问题：2n个人排队买票，其中n个人持50元，n个人持100元。每张票50元，且一人只买一张票。初始时售票处没有零钱找零。请问这2n个人一共有多少种排队顺序，不至于使售票处找不开钱
- （腾讯笔试）在图书馆一共6个人在排队，3个还《面试宝典》一书，3个在借《面试宝典》一书，图书馆此时没有了面试宝典了，求他们排队的总数？
- （阿里笔试）说16个人按顺序去买烧饼，其中8个人每人身上只有一张5块钱，另外8个人每人身上只有一张10块钱。 烧饼5块一个，开始时烧饼店老板身上没有钱。 16个顾客互相不通气，每人只买一个。 问这16个人共有多少种排列方法能避免找不开钱的情况出现。
