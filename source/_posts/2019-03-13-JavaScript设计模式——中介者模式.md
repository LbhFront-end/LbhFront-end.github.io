---
title: JavaScript设计模式——中介者模式
date: 2019-03-13 11:30:00
tags: JavaScript设计模式
categories: 
	- JavaScript设计模式




---

学习曾探的 《JavaScript设计模式与开发实践》并做记录。

[书籍的购买链接](http://www.ituring.com.cn/book/1632/)

设计模式的定义是：在面向对象软件设计过程中针对特定问题的简洁而优雅的解决方案。

通俗一点，设计模式是在某种场合下对某个问题的一种解决方案。是给面向对象软件开发中的一些好的设计取个名字。

# JavaScript设计模式——中介者模式

中介者模式的作用就是接触对象与对象之间的紧耦合关系。增加一个中介者对象后，所有的相关对象都通过中介者对象来通信，而不是互相引用，所以当一个对象发生变化时，只需要通知中介者对象就可以了。中介者使各对象之间耦合松散，而且可以独立地改变它们之间的交互。中介者模式使得网状的多对多关系变成了简单的一对多的关系。

## 现实中的中介者

**1.机场指挥塔**

中介者也被称为调停者，我们想象一下机场的指挥塔，如果没有指挥塔的存在，每一架飞机要和方圆 100 公里内的所有飞机通信，才能确定航线以及飞行状况，后果是不可想象的。现实中的情况是，每架飞机都只需要和指挥塔通信。指挥塔作为调停者，知道每一架飞机的飞行状况，所以它可以安排所有飞机的起降时间，及时做出航线调整。

**2.博彩公司**

打麻将的人经常遇到这样的问题，打了几局之后开始计算钱，A 自摸了两把，B 杠了三次，C 点炮一次给 D，谁应该给谁多少钱已经很难计算清楚，而这还是在只有 4 个人参与的情况下。
在世界杯期间购买足球彩票，如果没有博彩公司作为中介，上千万的人一起计算赔率和输赢绝对是不可能实现的事情。有了博彩公司作为中介，每个人只需和博彩公司发生关联，博彩公司会根据所有人的投注情况计算好赔率，彩民们赢了钱就从博彩公司拿，输了钱就交给博彩公司。

## 例子——泡泡堂游戏

先定义一个玩家构造函数，它有三个简单的原型方法：Play.prototype.win、Play.prototype.lose 以及表示玩家死亡的 Play.prototype.die

因为玩家的数量是2，所有当其中一个玩家死亡的时候游戏便结束，同时通知它的对手胜利。

```javascript
function Player(name){
    this.name = name;
    this.enemy = null; 
}
Player.prototype.win = function(){
    console.log(this.name + ' won');
}
Player.prototype.lose = function(){
    console.log(this.name + ' lost');
}
Player.prototype.die = function(){
    this.lose();
    this.enemy.win();
}
// 创建两个玩家对象：
const player1 = new Player('皮蛋');
const player2 = new Player('小乖');
// 给玩家互相设置敌人：
player1.enemy = player2;
player2.enemy = player1;
// 当玩家 player1 被泡泡炸死的时候，只需要调用这一句代码便完成了一局游戏：
player1.die();
```

### 为游戏增加队伍

因为玩家数量变多，用下面的方式来设置队友和敌人无疑很低效

```javascript
player1.partners= [player1,player2,player3,player4]; 
player1.enemies = [player5,player6,player7,player8]; 
Player5.partners= [player5,player6,player7,player8]; 
Player5.enemies = [player1,player2,player3,player4]; 
```

所以我们定义一个数组 players 来保存所有的玩家，在创建玩家之后，循环 players 来给每个玩家设置队友和敌人：

```javascript
const players = [];
// 改写构造函数，使得每个玩家都增加一些属性，分别是队友列表，敌人列表，玩家当前状态，角色名字已经玩家所在队伍的颜色
function Player(name,teamColor){
    this.partners = [];
    this.enemies = [];
    this.state = 'live';
    this.name = name;
    this.teamColor = teamColor;
}
Player.prototype.win = function(){
    console.log('winner: '+this.name);
}
Player.prototype.lose = function(){
    console.log('loser: '+this.name);
}
Player.prototype.die = function(){
    let all_dead = true;
    this.state = 'dead';
    for(let i=0,partner;partner = this.partners[i++];){ // 遍历队友列表
        if(partner.state !== 'dead'){ // 如果还有一个队友没有死亡，则游戏还没有失败
            all_dead = false;
            break;
        }
    }
    if(all_dead === true){ // 如果队友全部死亡
        this.lose();
        for(let i=0,partner;partner = this.partners[i++];){ // 通知所有队友玩家游戏失败
            partner.lose();
        }
        for(let i=0,enemy;enemy = this.enemies[i++];){ // 通知所有敌人游戏胜利
            enemy.win();
        }				
    }
}

// 定一个工厂来创建玩家：
const playerFactory = function(name,teamColor){
    const newPlayer = new Player(name,teamColor); // 创建新玩家
    for(let i=0,player;player = players[i++];){ // 通知所有玩家，有新角色加入
        if(player.teamColor === newPlayer.teamColor){ // 如果是同一队的玩家
            player.partners.push(newPlayer); // 互相添加到队友列表
            newPlayer.partners.push(player)
        }else{
            player.enemies.push(newPlayer);  // 互相添加到敌人列表
            newPlayer.enemies.push(player);
        }
    }
    players.push(newPlayer);
    return newPlayer;
}

const player1 = playerFactory( '皮蛋', 'red' ), 
player2 = playerFactory( '小乖', 'red' ), 
player3 = playerFactory( '宝宝', 'red' ), 
player4 = playerFactory( '小强', 'red' ), 

player5 = playerFactory( '黑妞', 'blue' ),
player6 = playerFactory( '葱头', 'blue' ),
player7 = playerFactory( '胖墩', 'blue' ), 
player8 = playerFactory( '海盗', 'blue' );


player1.die();
player2.die();
player3.die();
player4.die();
// loser: 小强
// loser: 皮蛋
// loser: 小乖
// loser: 宝宝
// winner: 黑妞
// winner: 葱头
// winner: 胖墩
// winner: 海盗
```

### 玩家增多带来的困扰

现在我们已经可以随意地为游戏增加玩家或者队伍，但问题是，每个玩家和其他玩家都是紧紧耦合在一起的。在此段代码中，每个玩家对象都有两个属性，this.partners 和 this.enemies，用来保存其他玩家对象的引用。当每个对象的状态发生改变，比如角色移动、吃到道具或者死亡时，都必须要显式地遍历通知其他对象。

在这个例子中只创建了 8 个玩家，或许还没有对你产生足够多的困扰，而如果在一个大型网络游戏中，画面里有成百上千个玩家，几十支队伍在互相厮杀。如果有一个玩家掉线，必须从所有其他玩家的队友列表和敌人列表中都移除这个玩家。游戏也许还有解除队伍和添加到别的队伍的功能，红色玩家可以突然变成蓝色玩家，这就不再仅仅是循环能够解决的问题了。面对这样的需求，我们上面的代码可以迅速进入投降模式

### 用中介者模式改造

首先依然是定义 Player 构造函数和 player 对象的原型方法噶，在 player 对象的这些原型方法中，不再负责具体的执行逻辑，而是把操作转交给中介者对象，我们把中介者对象命名为 playerDirector:

```javascript
function Player(name,teamColor){
    this.name = name;
    this.teamColor = teamColor;
    this.state = 'live';
}
Player.prototype.win = function(){
    console.log('winner: '+this.name);
}
Player.prototype.lose = function(){
    console.log('loser: '+this.name);
}		
Player.prototype.die = function(){
    this.state = 'dead';
    playerDirector.reciveMessage('playerDead',this); // 给中介者发送消息，玩家死亡
}
Player.prototype.remove = function(){
    playerDirector.reciveMessage('removePlayer',this); // 给中介者发送消息，移除一个玩家
}
Player.prototype.changeTeam = function(color){
    playerDirector.reciveMessage('changeTeam',this,color); // 给中介者发送消息，玩家换队
}

const playerFactory = function(name,teamColor){
    const newPlayer = new Player(name,teamColor);
    playerDirector.reciveMessage('addPlayer',newPlayer); // 给中介者发送消息，新增玩家
    return newPlayer;
}
```

最后，需要实现这个中介者 PlayerDirector 对象，一般有两种方式。

- 利用发布-订阅模式。将 playerDirector 实现为订阅者，各 player 作为发布者，一旦 player 的状态发生改变，便推送信息给 playerDirector ，playerDirector 处理消息后将反馈发送给其他 player
- 在 playerDirector 中开发一些接受消息的接口，各 player 可以直接调用该接口来给 playerDirector 发送消息，player 只需传递一个参数给  playerDirector ，这个参数的目的是使得 playerDirector 可以识别发送者。同样，playerDirector 接受消息之后会将处理结果反馈给其他 player。

这两种方式的实现本质上没有什么区别。在这里使用第二种方式，playerDirector 开发一个对外暴露的接口 recievMessage,负责接收 player 对象发送的消息，而 player 对象发送消息的时候，总是把自身的 this 作为参数发送给 playerDirector ，以便其识别消息来自于哪个玩家的对象，代码如下：

```javascript
const playerDirector = (function(){
    const players = {}, // 保存所有玩家
    operations = {}; // 中介者可以执行的操作

    operations.addPlayer = function(player){ // 增加玩家
        const teamColor = player.teamColor;
        players[teamColor] = players[teamColor] || []; // 如果该颜色的玩家还没有成立队伍，则新成立一个队伍
        players[teamColor].push(player);
    };

    operations.removePlayer = function(player){ // 移除玩家
        const teamColor = player.teamColor,
        teamPlayers = players[teamColor] || [];
        for(let i=teamPlayers.length - 1;i>=0;i--){
            if(teamPlayers[i] === player){
                teamPlayers.splice(i,1);
            }
        }				
    };
    operations.changeTeam = function(player,newTeamCOlor){ // 玩家换队
        operations.removePlayer(player);
        player.teamColor = newTeamCOlor;
        operations.addPlayer(player);
    };
    operations.playerDead = function(player){ // 玩家死亡
        const teamColor = player.teamColor,
        teamPlayers = players[teamColor];
        let all_dead = true;
        for(let i=0,player;player = teamPlayers[i++];){
            if(player.state !== 'dead'){
                all_dead = false;
                break;
            }
        }
        if(all_dead){
            for(let i=0,player;player = teamPlayers[i++];){
                player.lose();
            }
            for(let color in players){
                if(color !== teamColor){
                    const teamPlayers = players[color]; //其他队伍的玩家
                    for(let i=0,player;player = teamPlayers[i++];){
                        player.win();
                    }
                }
            }
        }
    };
    const reciveMessage = function(){
        const message = Array.prototype.shift.call(arguments);
        operations[message].apply(this,arguments);
    };
    return {
        reciveMessage
    }
})();

// 测试
const player1 = playerFactory( '皮蛋', 'red' ), 
player2 = playerFactory( '小乖', 'red' ), 
player3 = playerFactory( '宝宝', 'red' ), 
player4 = playerFactory( '小强', 'red' ), 

player5 = playerFactory( '黑妞', 'blue' ),
player6 = playerFactory( '葱头', 'blue' ),
player7 = playerFactory( '胖墩', 'blue' ), 
player8 = playerFactory( '海盗', 'blue' );

player1.changeTeam( 'blue' ); 
// 换队

player1.die();
player2.die();
player3.die();
player4.die();	
```



## 例子——购买商品

假设我们正在编写一个手机购买的页面，在购买流程中，可以选择手机的颜色以及输入购买数量，同时页面中有两个展示区域，分别向用户展示刚刚选择好的颜色和数量。还有一个按钮动态显示下一步的操作，我们需要查询该颜色手机对应的库存，如果库存数量少于这次的购买数量，按钮将被禁用并且显示库存不足，反之按钮可以点击并且显示放入购物车。

这个需求是非常容易实现的，假设我们已经从后台获取了所有颜色手机的库存量：

```javascript
var goods = { // 手机库存
    'red':3,
    'blue':6
}
```

页面布局需要：

- 下拉选择框 colorSelect 
- 文本输入框 numberInput 
- 展示颜色信息 colorInfo 
- 展示购买数量信息 numberInfo 
- 决定下一步操作的按钮 nextBtn 

### 编写代码

HTML代码：

```html
选择颜色：<select id="colorSelect">
    <option value="">请选择</option>
    <option value="red">红色</option>
    <option value="blue">蓝色</option>
</select>
输入购买数量：<input type="text" id="numberInput">
您选择了颜色：<div id="colorInfo"></div>
您输入了数量：<div id="numberInfo"></div>
<button id="nextBtn" disabled="true">请选择手机颜色和购买数量</button>
```

接下来监听 colorSelect 的 onchange 事件函数和 numberInput 的 oninput 事件函数，然后在这两个事件中做出相应处理。

```javascript
const colorSelect = document.getElementById('colorSelect'),
numberInput = document.getElementById('numberInput'),
colorInfo = document.getElementById('colorInfo'),
numberInfo = document.getElementById('numberInfo'),
nextBtn = document.getElementById('nextBtn');

const goods ={
    'red':3,
    'blue':6
}
colorSelect.onchange = function(){
    let color = this.value,
    number = numberInput.value,
    stock = goods[color];
    console.log(color);
    colorInfo.innerHTML = color;

    if(!color){
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请选择手机颜色';
        return;
    }
    if(((number - 0)|0) !== number - 0){ //用户输入的购买数量是为正整数
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请输入正确的购买数量';
        return;
    }
    if(number > stock){
        nextBtn.disabled = true;
        nextBtn.innerHTML = '库存不足';
        return;
    }
    nextBtn.disabled = false;
    nextBtn.innerHTML = '放入购物车';
}

numberInput.oninput = function(){
    let color = colorSelect.value,
    number = this.value,
    stock = goods[color];
    numberInfo.innerHTML = number;
    if(!color){
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请选择手机颜色';
        return;
    }
    if(((number - 0)|0) !== number - 0){ //用户输入的购买数量是为正整数
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请输入正确的购买数量';
        return;
    }
    if(number > stock){
        nextBtn.disabled = true;
        nextBtn.innerHTML = '库存不足';
        return;
    }
    nextBtn.disabled = false;
    nextBtn.innerHTML = '放入购物车';
}
```

### 可能遇到的困难

虽然目前顺利完成了代码编写，但随之而来的需求改变有可能给我们带来麻烦。假设现在要求去掉 colorInfo 和 numberInfo 这两个展示区域，我们就要分别改动 colorSelect.onchange 和numberInput.onput 里面的代码，因为在先前的代码中，这些对象确实是耦合在一起的。目前我们面临的对象还不算太多，当这个页面里的节点激增到 10 个或者 15 个时，它们之间的联系可能变得更加错综复杂，任何一次改动都将变得很棘手。为了证实这一点，我们假设页面中将新增另外一个下拉选择框，代表选择手机内存。现在我们需要计算颜色、内存和购买数量，来判断 nextBtn 是显示库存不足还是放入购物车。

首先我们要增加两个 HTML 节点：

```html
选择颜色：<select id="colorSelect">
    <option value="">请选择</option>
    <option value="red">红色</option>
    <option value="blue">蓝色</option>
</select>
选择内存: <select id="memorySelect"> 
    <option value="">请选择</option> 
    <option value="32G">32G</option> 
    <option value="16G">16G</option> 
</select> 	
输入购买数量：<input type="text" id="numberInput"><br/> 
您选择了颜色：<div id="colorInfo"></div><br/> 
您选择了内存: <div id="memoryInfo"></div><br/> 
您输入了数量：<div id="numberInfo"></div><br/> 
<button id="nextBtn" disabled="true">请选择手机颜色和购买数量</button>
```

接着增加 memorySelect 的onchange 事件函数，改变之前的 colorSelect 和 numberInput事件

```javascript
const colorSelect = document.getElementById('colorSelect'),
numberInput = document.getElementById('numberInput'),
colorInfo = document.getElementById('colorInfo'),
numberInfo = document.getElementById('numberInfo'),
nextBtn = document.getElementById('nextBtn'),
memorySelect = document.getElementById( 'memorySelect' ), 
memoryInfo = document.getElementById( 'memoryInfo' );

const goods ={
    "red|32G": 3, // 红色 32G，库存数量为 3 
    "red|16G": 0, 
    "blue|32G": 1, 
    "blue|16G": 6 
}
colorSelect.onchange = function(){
    let color = this.value,
    memory = memorySelect.value, 
    number = numberInput.value,
    stock = goods[color+'|'+memory];
    colorInfo.innerHTML = color;

    if(!color){
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请选择手机颜色';
        return;
    }
    if(!memory){
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请选择内存大小';
        return;
    }
    if(((number - 0)|0) !== number - 0){ //用户输入的购买数量是为正整数
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请输入正确的购买数量';
        return;
    }
    if(number > stock){
        nextBtn.disabled = true;
        nextBtn.innerHTML = '库存不足';
        return;
    }
    nextBtn.disabled = false;
    nextBtn.innerHTML = '放入购物车';
}

memorySelect.onchange = function(){
    let color = colorSelect.value,
    memory = this.value, 
    number = numberInput.value,
    stock = goods[color+'|'+memory];
    memoryInfo.innerHTML = memory;
    if(!color){
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请选择手机颜色';
        return;
    }
    if(!memory){
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请选择内存大小';
        return;
    }			
    if(((number - 0)|0) !== number - 0){ //用户输入的购买数量是为正整数
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请输入正确的购买数量';
        return;
    }
    if(number > stock){
        nextBtn.disabled = true;
        nextBtn.innerHTML = '库存不足';
        return;
    }
    nextBtn.disabled = false;
    nextBtn.innerHTML = '放入购物车';
}		
numberInput.oninput = function(){
    let color = colorSelect.value,
    memory = memorySelect.value, 
    number = this.value,
    stock = goods[color+'|'+memory];
    numberInfo.innerHTML = number;

    if(!color){
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请选择手机颜色';
        return;
    }
    if(!memory){
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请选择内存大小';
        return;
    }
    if(((number - 0)|0) !== number - 0){ //用户输入的购买数量是为正整数
        nextBtn.disabled = true;
        nextBtn.innerHTML = '请输入正确的购买数量';
        return;
    }
    if(number > stock){
        nextBtn.disabled = true;
        nextBtn.innerHTML = '库存不足';
        return;
    }
    nextBtn.disabled = false;
    nextBtn.innerHTML = '放入购物车';
}
```

我们仅仅是增加一个内存的选择条件，就要改变如此多的代码，这是因为在目前的实现中，每个节点对象都是耦合在一起的，改变或者增加任何一个节点对象，都要通知到与其相关的对象。

### 引入中介者

现在我们来引入中介者对象，所有的节点对象只跟中介者通信。当下拉选择框 colorSelect、memorySelect 和文本输入框 numberInput 发生了事件行为时，它们仅仅通知中介者它们被改了，同时把自身当作参数传入中介者，以便中介者辨别是谁发生了改变。剩下的所有事情都交给中介者对象来完成，这样一来，无论是修改还是新增节点，都只需要改动中介者对象里的代码。

```javascript
const goods ={
    "red|32G": 3, // 红色 32G，库存数量为 3 
    "red|16G": 0, 
    "blue|32G": 1, 
    "blue|16G": 6 
}		

const mediator = (function(){
    const colorSelect = document.getElementById('colorSelect'),
    numberInput = document.getElementById('numberInput'),
    colorInfo = document.getElementById('colorInfo'),
    numberInfo = document.getElementById('numberInfo'),
    nextBtn = document.getElementById('nextBtn'),
    memorySelect = document.getElementById( 'memorySelect' ), 
    memoryInfo = document.getElementById( 'memoryInfo' );
    return{
        changed:function(obj){
            let color = colorSelect.value,
            memory = memorySelect.value,
            number = numberInput.value,
            stock = goods[color+'|'+memory];
            if(obj === colorSelect){ // 判断是哪个选择框或者是输入框对应赋值
                colorInfo.innerHTML = color;
            }else if(obj === memorySelect){
                memoryInfo.innerHTML = memory;
            }else if(obj === numberInput){
                numberInfo.innerHTML = number;
            }

            if(!color){
                nextBtn.disabled = true;
                nextBtn.innerHTML = '请选择手机颜色';
                return;
            }
            if(!memory){
                nextBtn.disabled = true;
                nextBtn.innerHTML = '请选择内存大小';
                return;
            }
            if(((number - 0)|0) !== number - 0){ //用户输入的购买数量是为正整数
                nextBtn.disabled = true;
                nextBtn.innerHTML = '请输入正确的购买数量';
                return;
            }
            if(number > stock){
                nextBtn.disabled = true;
                nextBtn.innerHTML = '库存不足';
                return;
            }
            nextBtn.disabled = false;
            nextBtn.innerHTML = '放入购物车';					
        }
    }
})();

// 事件函数
colorSelect.onchange = function(){
    mediator.changed(this);
}
memorySelect.onchange = function(){
    mediator.changed(this);
}
numberInput.onchange = function(){
    mediator.changed(this);
}
```

可以想象，某天我们又要新增一些跟需求相关的节点，比如 CPU 型号，那我们只需要稍稍改动 mediator 对象即可：

```javascript
var goods = { // 手机库存
 "red|32G|800": 3, // 颜色 red，内存 32G，cpu800，对应库存数量为 3 
 "red|16G|801": 0, 
 "blue|32G|800": 1, 
 "blue|16G|801": 6 
}; 
var mediator = (function(){ 
 // 略
 var cpuSelect = document.getElementById( 'cpuSelect' ); 
     return { 
         change: function(obj){ 
             // 略
             var cpu = cpuSelect.value, 
             stock = goods[ color + '|' + memory + '|' + cpu ]; 
             if ( obj === cpuSelect ){ 
             cpuInfo.innerHTML = cpu; 
         } 
     // 略
     } 
 } 
})(); 
```



## 小结

中介者模式是迎合迪米特法则的一种实现。也叫做最少知识原则，是指一个对象应该尽可能少地了解另外的对象。如果对象之间耦合性太高，一个对象发生改变之后，难免会影响其他的对象。而在中介者模式中，对象之间几乎不知道彼此的存在，它们只能通过中介者对象来互相影响对方。

因此，中介者模式使各个对象之间得以解耦，以中介者和对象之间的一对多关系取代了对象之间网状的多对多的关系。各个对象只需要关注功能的说笑呢，对象之间的交互关系交给了中介者对象来维护。

不过，中介者模式也存在一些缺点。其中，最大的缺点就是系统中会新增一个中介者对象，因此对象之间交互的复杂性，转移成了中介者对象的复杂性，使得中介者对象经常是巨大的。中介者对象自身往往是一个难以维护的对象。

我们都知道，毒贩子虽然使吸毒者和制毒者之间的耦合度降低，但毒贩子也要抽走一部分利润。同样，在程序中，中介者对象要占去一部分内存。而且毒贩本身还要防止被警察抓住，因为它了解整个犯罪链条中的所有关系，这表明中介者对象自身往往是一个难以维护的对象。

中介者模式可以非常方便地对模块或者对象进行解耦，但对象之间并非一定需要解耦。在实际项目中，模块或对象之间有些依赖关系是很正常的。关键在于如何取衡量对象之间的耦合程度。一般来说，如果对象之间 的复杂耦合确实导致调用和维护出现了困难，而且这些耦合度随着项目的变化呈现出来指数增长曲线，那我们就可以考虑用中介者模式来重构代码。