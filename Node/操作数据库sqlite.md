# NodeJS中使用SQLite3

express[操作所有数据库](https://www.expressjs.com.cn/guide/database-integration.html)

### 1、SQLite简介

sqlite是一款轻量级的数据库，sqlite的第一个版本是2000年就发布了的，经过十多年的历练，显然sqlite目前已经相当成熟。sqlite最大的特点就是没有任何数据库服务器，无论是C、java、node.js，只需要相应的驱动，就可以直接对数据库进行读写，速度是相当的快。相比之下，其他的sql数据库必须启动一个服务，而且还要安装、配置，sqlite简洁的令人感动。

由于没有了服务器，sqlite的sql执行速度相当快。sqlite的主要应用是嵌入式领域、移动互联网、火狐浏览器等，由于对并发连接支持非常不好，因此在信息管理系统中用的不多。但本人由于各种原因在做一个管理系统的项目中使用了sqlite，跑在大约1000用户的环境下，数据量达到了5000万的级别，居然没有出现任何问题。看来sql不可貌相，蚂蚁有时候也能干大象干的事。

### 2、Node.js安装sqlite3模块

和其他语言一样，node.js仍然只需要一个module就可以对sqlite进行操作了。

- npm find sqlite

发现sqlite的模块居然有几十个，足见node.js目前的火爆程度，小小的sqlite就有这么多可用模块。

其中一个就叫做sqlite3，解释如下：

- sqlite3  Asynchronous, non-blocking SQLite3 bindings 异步，非阻塞sqlite3绑定。

node.js最大的特点就是异步，这款sqlite的驱动太符合node的核心思想了，经过与其他模块的比较，最终还是选择了这款。

- npm install sqlite3

就将sqlite的驱动安装进来了。 

### 3、调用SQLite3的接口

```javascript
//file:test.js

var sqlite3 = require('sqlite3');
var db = new sqlite3.Database('/tmp/1.db',function() {
  db.run("create table test(name varchar(15))",function(){
    db.run("insert into test values('hello,world')",function(){
      db.all("select * from test",function(err,res){
        if(!err){            
          console.log(JSON.stringify(res));
        }
        else{
          console.log(err);
        }
      });
    })
  });
});
```

执行：

- node test.js
- [{"name":"hello,world"}]

怎么样，还是很简单的吧，插入的时候使用db.run()，查询的时候使用db.all()

由于是异步，必须在执行sql后注册回调函数，这样如果连续执行几十条sql，那代码就跟楼梯差不多了。但这就是node的特点，所以还是要慢慢适应。 

### 4、引入SQLite3模块

```javascript
var fs = require('fs');
var file = 'test.db';//这里写的就是数据库文件的路径
var exists = fs.existsSync(file);
var sqlite3 = require('sqlite3').verbose();
var db = new sqlite3.Database(file);
```

 

### 5、增删改查操作

```javascript
//增
var sql1 = db.prepare("insert into 表名 values (内容，跟mysql一样)");
sql1.run();
//console.log(sql1);//可以这样检查

//删
var sql2 = db.prepare("delete from 表名 where id = 1");
//console.log(sql2);
sql2.run();

//改
var sql3 = db.prepare("update 表名 set name = winston where id = 1");
sql3.run();

//查
//查一个表的所有数据
db.all("select * from 表名",function(err,row){
    console.log(JSON.stringify(row));
})

//查一条数据
db().each("select * from 表名",function(err,row){
     console.log(row);
})
```

###  

### 6、SQLite3 API介绍

在nodejs的模块安装模块下，进入sqlite3/lib目录下，打开sqlite3.js文件查看，操作数据库主要是用Database，Database相关的函数有：run、prepare、each、get、all、exec、map和close。

**Database**

- 用法：new sqlite3.Database(filename,[mode],[callback])。
- 功能：返回数据库对象并且自动打开和连接数据库，它没有独立打开数据库的方法。

**close**

- 用法：close([callback])。
- 功能：关闭和释放数据库对象。

**run**

- 用法：run(sql,param,...],[callback])。
- 功能：运行指定参数的SQL语句，完成之后调用回调函数，它不返回任何数据，在回调函数里面有一个参数，**SQL语句执行成功，则参数的值为null**,反之为一个错误的对象，它返回的是数据库的操作对象。在这个回调函数里面当中的this,里面包含有lastId(插入的ID)和change(操作影响的行数,如果执行SQL语句失败，则change的值永远为0)。

**get**

- 用法：get(sql,[param,...],[callback])。
- 功能：运行指定参数的SQL语句，完成过后调用回调函数。**如果执行成功，则回调函数中的第一个参数为null**,第二个参数为结果集中的第一行数据，反之则回调函数中只有一个参数，只参数为一个错误的对象。

**all**

- 用法：all(sql,[param,...],[callback])。
- 功能：运行指定参数的SQL语句，完成过后调用回调函数。**如果执行成功，则回调函数中的第一个参数为null**,第二个参数为查询的结果集，反之，则只有一个参数，且参数的值为一个错误的对象。

**prepare**

- 用法：prepare(sql,[param,...],[callback])。
- 功能：预执行绑定指定参数的SQL语句，返回一个Statement对象，如果执行成功，则回调函数的第一个参数为null,反之为一个错误的对象。
   

### 7、具体事例

由于sqlite3 API具体使用过程中重复代码量较多，所以进行了简单封装，在使用过程中如果第一次创建数据库和表，紧接着插入数据的话，可能导致表还未创建完成就查询出现错误（Nodejs是基于异步的且顺序不可控），所以在表创建和插入数据时使用同步方式操作来保证表已经存在。封装的代码如下：

```javascript
/**
 * File: sqlite.js.
 * Author: W A P.
 * Email: 610585613@qq.com.
 * Datetime: 2018/07/24.
 */
var fs = require('fs');
var sqlite3 = require('sqlite3').verbose();
var DB = DB || {};
DB.SqliteDB = function(file){
    DB.db = new sqlite3.Database(file);
    DB.exist = fs.existsSync(file);
    if(!DB.exist){
        console.log("Creating db file!");
        fs.openSync(file, 'w');
    };
};

DB.printErrorInfo = function(err){
    console.log("Error Message:" + err.message + " ErrorNumber:" + errno);
};

DB.SqliteDB.prototype.createTable = function(sql){
    DB.db.serialize(function(){
        DB.db.run(sql, function(err){
            if(null != err){
                DB.printErrorInfo(err);
                return;
            }
        });
    });
};

/// tilesData format; [[level, column, row, content], [level, column, row, content]]
DB.SqliteDB.prototype.insertData = function(sql, objects){
    DB.db.serialize(function(){
        var stmt = DB.db.prepare(sql);
        for(var i = 0; i < objects.length; ++i){
            stmt.run(objects[i]);
        }
        stmt.finalize();
    });
};

DB.SqliteDB.prototype.queryData = function(sql, callback){
    DB.db.all(sql, function(err, rows){
        if(null != err){
            DB.printErrorInfo(err);
            return;
        }

        /// deal query data.
        if(callback){
            callback(rows);
        }
    });
};


DB.SqliteDB.prototype.executeSql = function(sql){
    DB.db.run(sql, function(err){
        if(null != err){
            DB.printErrorInfo(err);
        }
    });
};

DB.SqliteDB.prototype.close = function(){
    DB.db.close();
};

/// export SqliteDB.
exports.SqliteDB = DB.SqliteDB;
```

针对以上封装接口的调用代码如下：

```javascript
/**
 * File: callSqlite.js.
 * Author: W A P.
 * Email: 610585613@qq.com.
 * Datetime: 2018/07/24.
 */


/// Import SqliteDB.
var SqliteDB = require('./sqlite.js').SqliteDB;
var file = "Gis1.db";
var sqliteDB = new SqliteDB(file);

/// create table.
var createTileTableSql = "create table if not exists tiles(level INTEGER, column INTEGER, row INTEGER, content BLOB);";

var createLabelTableSql = "create table if not exists labels(level INTEGER, longitude REAL, latitude REAL, content BLOB);";

sqliteDB.createTable(createTileTableSql);

sqliteDB.createTable(createLabelTableSql);

/// insert data.
var tileData = [[1, 10, 10], [1, 11, 11], [1, 10, 9], [1, 11, 9]];
var insertTileSql = "insert into tiles(level, column, row) values(?, ?, ?)";
sqliteDB.insertData(insertTileSql, tileData);


/// query data.
var querySql = 'select * from tiles where level = 1 and column >= 10 and column <= 11 and row >= 10 and row <=11';

sqliteDB.queryData(querySql, dataDeal);


/// update data.
var updateSql = 'update tiles set level = 2 where level = 1 and column = 10 and row = 10';
sqliteDB.executeSql(updateSql);


/// query data after update.
querySql = "select * from tiles where level = 2";
sqliteDB.queryData(querySql, dataDeal);
sqliteDB.close();
function dataDeal(objects){
    for(var i = 0; i < objects.length; ++i){
        console.log(objects[i]);
    }
}
```

 

------

 

## NodeJS 使用 better-sqlite3 操作sqlite 数据库

###  

### Better-sqlite3简介

Node.js中SQLite3最快最简单的库。

- 完整的事务支持
- 高性能，高效率和安全性
- 易于使用的同步API （比异步API更快......是的，你读得正确）
- 支持用户定义的函数，聚合和扩展
- 64位整数（在您需要它们之前不可见）

性能上 better-sqlite3 优于 sqlite3 。

 

### 安装

```bash
npm install --save better-sqlite3
```

### 使用

```javascript
const db = require('better-sqlite3')('foobar.db', options);



const row = db.prepare('SELECT * FROM users WHERE id=?').get(userId);



console.log(row.firstName, row.lastName, row.email);
```

### **代码示例**

```javascript
const TAG = '## TokenDao: ';

class TokenDao {
    constructor(db) {
        this.db = db;
        this.saveUserToken = this.saveUserToken.bind(this);
    }
    getUserToken(username,appType){
        var stmt = this.db.prepare('select userID,username,tokenString from UserToken where username=? and appType=?');
        var row = stmt.get(username,appType);
        console.log('getUserToken: '+JSON.stringify(row));
        return row;
    }
    saveUserToken(username,appType,tokenString ) {
        var stmt = this.db.prepare('select userID,username,tokenString from UserToken where username=? and appType=?');
        var row = stmt.get(username,appType);
        if(row){
            console.log('存在，则更新');
            
            //存在，则更新
            var stmt = this.db.prepare("update UserToken set tokenString = ? where username=? and appType=?");
            stmt.run(tokenString,username, appType);
        }else{
            console.log('不存在，则插入');
            //不存在，则插入
            var stmt = this.db.prepare("INSERT INTO UserToken (userName,appType,tokenString) VALUES (?,?,?)");
            stmt.run(username, appType,tokenString);
        }
    }
} 

module.exports = TokenDao;
```

 