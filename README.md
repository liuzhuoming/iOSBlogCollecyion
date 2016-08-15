# FMDB

标签（空格分隔）： iOS FMDB 数据库

---
## FMDB的构成
* **FMDatabase** – 表示一个单独的SQLite数据库。 用来执行SQLite的命令。
* **FMResultSet** – 表示FMDatabase执行查询后结果集合
* **FMDatabaseQueue** – 如果你想在多线程中执行多个查询或更新，你应该使用该类。这是线程安全的

## 数据库的创建


`#define TESTDB @"TESTDB.sqlite"`


```
NSArray *paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
NSString *documentDirectory = [paths objectAtIndex:0];
NSString *dbPath = [documentDirectory stringByAppendingPathComponent:TESTDB];
NSLog(@"%@",dbPath);
FMDatabase *db = [FMDatabase databaseWithPath:dbPath] ;
_db = db;
if (![db open]) {
NSLog(@"Could not open db.");
return ;
}
```
## 操作
### 创建表
```
[db executeUpdate:@"CREATE TABLE PersonList (Name text, Age integer, Sex integer, Phone text, Address text)"];
```

### 增删改
除了查询以外 全部用 **executeUpdate** 而且value必须是对象类型

```
- (IBAction)addDB:(id)sender {
if ([_db open]) {
[_db executeUpdate:@"INSERT INTO PersonList (Name, Age, Sex, Phone, Address) VALUES (?,?,?,?,?)",
@"alex",@20, @0,@"091234567", @"Taiwan, R.O.C"];
[_db close];
}

}
- (IBAction)subDB:(id)sender {



}
- (IBAction)changeDB:(id)sender {
if ([_db open]) {
[_db executeUpdate:@"UPDATE PersonList SET Age = ? WHERE Name = ?",[NSNumber numberWithInt:30],@"alex"];
[_db close];
}

}
- (void)dealloc
{
[_db close];
}
```
### 查

> FMResultSet  提供了很多方法来获得所需的格式的值：
intForColumn:
longForColumn:
longLongIntForColumn:
boolForColumn:
doubleForColumn:
stringForColumn:
dataForColumn:
dataNoCopyForColumn:
UTF8StringForColumnIndex:
objectForColumn:

```
- (IBAction)searchDB:(id)sender {
if ([_db open]) {
// 1.执行查询语句
FMResultSet *resultSet = [self.db executeQuery:@"SELECT * FROM PersonList"];
// 2.遍历结果
while ([resultSet next]) {
NSString *name = [resultSet stringForColumn:@"Name"];
int age = [resultSet intForColumn:@"Age"];
NSLog(@" %@ %d", name, age);
}
[_db close];
}
}
```


## 多线程

```
NSArray *paths = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES);
NSString *documentDirectory = [paths objectAtIndex:0];
NSString *dbPath = [documentDirectory stringByAppendingPathComponent:TESTDB];
FMDatabaseQueue *queue = [FMDatabaseQueue databaseQueueWithPath:dbPath];
[queue inDatabase:^(FMDatabase *db) {

}];
```


