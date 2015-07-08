# Android与iOS开发对比系列】之 数据存储

![image](./img/avi_3.png)


##写在前面的话

 相比Android和iOS,我觉得Android的数据存储更开放一些。Android天生就可以使用多Java I/O；并且天生开放的特性，开发者可以直接在SD卡中读写文件，自由度比较高。缺点吗，也是因为太开放，所以Android的相册和文件夹都惨不忍睹。
 
 Android本身自带Java的反射和注解，很早就有了ORM数据库。这里解释一下，ORM就是 Object Relation Mapping, 对象关系映射。 通过建立对象来生成数据库字段，大大简化了代码。
 
 Android的ORM我用过 [GreenDao](https://github.com/greenrobot/greenDAO) 和  [ActiveAndroid](https://github.com/pardom/ActiveAndroid) , GreenDao需要写Java项目来生成，而且lib包也比较多。后来我用ActiveAndroid，类似于Ruby的ORM存储方式，用起来比较顺手。
 
 IOS有Core Data, 但是用起来比较复杂。我一开始是直接使用 [FMDB](https://github.com/ccgus/fmdb) ,因为是SQLite的封装，还需要写SQLite代码，写起来还是比较麻烦。 当然，现在有了 [MagicalRecord](https://github.com/magicalpanda/MagicalRecord) ，类似ORM数据库的Core Data封装, 能简化不少代码。
 
 
 

## Android的4种数据存储方式

### 1 使用SharedPreferences存储数据；  

SharedPreferences是用来存储一些Key/Value类似的成对的基本数据类型。

它只能存储基本数据类型，也即int, long, boolean, String, float。

IOS相对的就是`NSUserDefaults`;

下面是示例代码：

```
void WriteSharedPreferences(String strName,String strPassword){
	SharedPreferences user = getSharedPreferences(“user_info”,Context.MODE_PRIVATE);
	user.edit();
	user.putString(“NAME”, strName);
	user.commit();
}

void ReadSharedPreferences(){
	String strName,strPassword;
	SharedPreferences   user = getSharedPreferences(“user_info”,Context.MODE_PRIVATE);
	strName = user.getString(“NAME”,””);
}

```

SharedPreferences是采用了XML格式将数据存储到设备中，在DDMS中的File Explorer中的/data/data/<package name>/shares_prefs下.

SharedPreferences同样是沙盒机制：只能在同一个包内使用，不能在不同的包之间使用。

```
<?xml version=”1.0″ encoding=”UTF-8″?>
<map>
<string name=”NAME”>XXXX</string>
</map>
```

-----------

### 2 文件存储数据；

#### Internal Storage内部存储空间

这里是指手机内置的存储空间，称为内部存储。使用内部存储主要有二个方式，一个是文件操作，一个是文件夹操作.

Context提供了两个方法来打开数据文件里的文件IO流 

``` 
FileInputStream openFileInput(String name);  
FileOutputStream(String name , int mode).
```
Context还提供了如下几个重要的方法：

```  
getDir(String name , int mode); //在应用程序的数据文件夹下获取或者创建name对应的子目录  
File getFilesDir();  //获取该应用程序的数据文件夹得绝对路径  
String[] fileList(); //返回该应用数据文件夹的全部文件   
``` 
  
Context.openFileOutput(String fileName, int mode)生成的文件自动存储在`/data/data/Package Name/files`目录下，其全路径是`/data/data/Package Name/files/fileName` 。  
注意下，这里的参数fileName不可以包含路径分割符（如"/"）.

内部存储空间应该用来保存比较重要的数据，apk被卸载时，apk在内部存储空间的文件数据将被删除。

示例代码：

```
FileOutputStream  output = Context.openOutputFile(filename， Context.MODE_PRIVATE);  
output.write(data）；// use output to write whatever you like
output.close();
```

```
FileInputStream input = Context.openInputFile(filename);
input.read();
input.close();
```

```
public String read() {
        try {
            FileInputStream inStream = this.openFileInput("message.txt");
            byte[] buffer = new byte[1024];
            int hasRead = 0;
            StringBuilder sb = new StringBuilder();
            while ((hasRead = inStream.read(buffer)) != -1) {
                sb.append(new String(buffer, 0, hasRead));
            }

            inStream.close();
            return sb.toString();
        } catch (Exception e) {
            e.printStackTrace();
        } 
        return null;
    }
    
    public void write(String msg){
        // 步骤1：获取输入值
        if(msg == null) return;
        try {
            // 步骤2:创建一个FileOutputStream对象,MODE_APPEND追加模式
            FileOutputStream fos = openFileOutput("message.txt",
                    MODE_APPEND);
            // 步骤3：将获取过来的值放入文件
            fos.write(msg.getBytes());
            // 步骤4：关闭数据流
            fos.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
```

-------------------


#### External Storage外部存储空间

就是读写sdcard上的文件

其中读写步骤按如下进行:

1 调用Environment的getExternalStorageState()方法判断手机上是否插了sd卡,且应用程序具有读写SD卡的权限，如下代码将返回true

Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)

2 调用Environment.getExternalStorageDirectory()方法来获取外部存储器，也就是SD卡的目录,或者使用"/mnt/sdcard/"目录

3 使用IO流操作SD卡上的文件 

需要权限：

```
<uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"/>
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```

示例代码：

```
// 文件写操作函数
    private void write(String content) {
        if (Environment.getExternalStorageState().equals(
                Environment.MEDIA_MOUNTED)) { // 如果sdcard存在
            File file = new File(Environment.getExternalStorageDirectory()
                    .toString()
                    + File.separator
                    + DIR
                    + File.separator
                    + FILENAME); // 定义File类对象
            if (!file.getParentFile().exists()) { // 父文件夹不存在
                file.getParentFile().mkdirs(); // 创建文件夹
            }
            PrintStream out = null; // 打印流对象用于输出
            try {
                out = new PrintStream(new FileOutputStream(file, true)); // 追加文件
                out.println(content);
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                if (out != null) {
                    out.close(); // 关闭打印流
                }
            }
        } else { // SDCard不存在，使用Toast提示用户
            Toast.makeText(this, "保存失败，SD卡不存在！", Toast.LENGTH_LONG).show();
        }
    }

    // 文件读操作函数
    private String read() {

        if (Environment.getExternalStorageState().equals(
                Environment.MEDIA_MOUNTED)) { // 如果sdcard存在
            File file = new File(Environment.getExternalStorageDirectory()
                    .toString()
                    + File.separator
                    + DIR
                    + File.separator
                    + FILENAME); // 定义File类对象
            if (!file.getParentFile().exists()) { // 父文件夹不存在
                file.getParentFile().mkdirs(); // 创建文件夹
            }
            Scanner scan = null; // 扫描输入
            StringBuilder sb = new StringBuilder();
            try {
                scan = new Scanner(new FileInputStream(file)); // 实例化Scanner
                while (scan.hasNext()) { // 循环读取
                    sb.append(scan.next() + "\n"); // 设置文本
                }
                return sb.toString();
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                if (scan != null) {
                    scan.close(); // 关闭打印流
                }
            }
        } else { // SDCard不存在，使用Toast提示用户
            Toast.makeText(this, "读取失败，SD卡不存在！", Toast.LENGTH_LONG).show();
        }
        return null;
    }
```

-----------

### 3 SQLite数据库存储数据；

这里不详细说了，SQLite3都是通用的。可以参考学习：[w3school](http://www.runoob.com/sqlite/sqlite-tutorial.html), 我iOS写SQLite关系表把这个又学了一遍。

简单代码如下：

```
 db.executeSQL(String sql);  
 db.executeSQL(String sql, Object[] bindArgs);//sql语句中使用占位符，然后第二个参数是实际的参数集 
 db.insert(String table, String nullColumnHack, ContentValues values);  
 db.update(String table, Contentvalues values, String whereClause, String whereArgs);  
 db.delete(String table, String whereClause, String whereArgs);
```

-----------

### 4 使用ContentProvider存储数据；

Android内置的许多数据都是使用ContentProvider形式，供开发者调用的(如视频，音频，图片，通讯录等)，可以向其他应用共享其数据。

我以前使用过ContentProvider和SQLiteDatabase CursorLoader相结合的方式，[github地址](https://github.com/TindleWei/ManYouJia/blob/master/manyou/src/com/manyou/wei/dao/DataProvider.java)。

这里代码就不详细介绍了。


-----------

### 补充

Preference，File， DataBase这三种方式分别对应的目录是   
`/data/data/Package Name/Shared_Pref`,  
`/data/data/Package Name/files`,   
`/data/data/Package Name/database`


## iOS的4种数据存储方式

iOS都是沙盒存储，数据都在app的目录下。

### 1 NSUserDefaults

与Android的SharedPreferences原理相同。

NSUserDefaults可以存储的数据类型包括：NSData、NSString、NSNumber、NSDate、NSArray、NSDictionary

保存数据：

```
NSUserDefaults *defaults =[NSUserDefaults standardUserDefaults];

NSString *name =@”default string“;
[defaults setObject:firstName forKey:@"name"];
 
UIImage *image=[[UIImage alloc]initWithContentsOfFile:@"photo.jpg"];
NSData *imageData = UIImageJPEGRepresentation(image, 100);//UIImage对象转换成NSData
[defaults setObject:imageData forKey:@"image"];

[defaults synchronize];//用synchronize方法把数据持久化到standardUserDefaults数据库
```

读取数据：

```
NSUserDefaults *defaults =[NSUserDefaults standardUserDefaults];

NSString *name = [defaults objectForKey:@"name"];//根据键值取出name

NSData *imageData = [defaults dataForKey:@"image"];
UIImage *Image = [UIImage imageWithData:imageData];//NSData转换为UIImage
```

---------------


### 2 NSKeyedArchiver归档

采用归档的形式来保存数据，该数据对象需要遵守NSCoding协议。
  
对象对应的类必须提供encodeWithCoder:和initWithCoder:方法。对对象进行编码和解码。

下面的例子是从这篇博客中看到的：[http://blog.csdn.net/dqjyong/article/details/7669252](http://blog.csdn.net/dqjyong/article/details/7669252)

例如对Possession对象归档保存。

```
定义Possession：
@interface Possession：NSObject<NSCoding>{//遵守NSCoding协议
       NSString *name;//待归档类型
}
@implementation Possession
-(void)encodeWithCoder:(NSCoder *)aCoder{
            [aCoder encodeObject:name forKey:@"name"];
}
-(void)initWithCoder:(NSCoder *)aDecoder{
            name=[[aDeCoder decodeObjectforKey:@"name"] retain];
}
```

归档操作：

```
如果对Possession对象allPossession归档保存，只需要NSCoder子类NSKeyedArchiver的方法archiveRootObject:toFile: 即可。

NSString *path = [self possessionArchivePath];
[NSKeyedArchiver archiveRootObject:allPossessions toFile: path ];
```

解压操作：

```
同样调用NSCoder子类NSKeyedArchiver的方法unarchiveRootObject:toFile: 即可  
allPossessions = [[NSKeyedUnarchiver unarchiveObjectWithFile:path] retain];
```

---------------


### 3 SQLite

这个不多说了，我具体就是直接看`FMDB`的文档。

---------------

### 4 CoreData

简述一下 Core Data数据持久化是对SQLite的一个升级，它是ios集成的.

我们在CoreData中使用的几个类。  
1 NSManagedObjectModel(被管理的对象模型)  
  相当于实体，不过它包含 了实体间的关系
           
2 NSManagedObjectContext(被管理的对象上下文)  
  操作实际内容  
  作用：插入数据  查询  更新  删除  
  
3 NSPersistentStoreCoordinator(持久化存储助理)  
  相当于数据库的连接器
4 NSFetchRequest(获取数据的请求)  
   相当于查询语句
   
5 NSPredicate(相当于查询条件)  
6 NSEntityDescription(实体结构)  
7 后缀名为.xcdatamodel的包  


示例代码,封装好的CoreData管理类CoreDataManager.h:

```
#import <foundation foundation.h="">
#import "News.h"
#define TableName @"News"
 
@interface CoreDateManager : NSObject
 
@property (readonly, strong, nonatomic) NSManagedObjectContext *managedObjectContext;
@property (readonly, strong, nonatomic) NSManagedObjectModel *managedObjectModel;
@property (readonly, strong, nonatomic) NSPersistentStoreCoordinator *persistentStoreCoordinator;
 
- (void)saveContext;
- (NSURL *)applicationDocumentsDirectory;
 
//插入数据
- (void)insertCoreData:(NSMutableArray*)dataArray;
//查询
- (NSMutableArray*)selectData:(int)pageSize andOffset:(int)currentPage;
//删除
- (void)deleteData;
//更新
- (void)updateData:(NSString*)newsId withIsLook:(NSString*)islook;
 
@end</foundation>
```

```
#import "CoreDateManager.h"
 
@implementation CoreDateManager
 
@synthesize managedObjectContext = _managedObjectContext;
@synthesize managedObjectModel = _managedObjectModel;
@synthesize persistentStoreCoordinator = _persistentStoreCoordinator;
 
- (void)saveContext
{
    NSError *error = nil;
    NSManagedObjectContext *managedObjectContext = self.managedObjectContext;
    if (managedObjectContext != nil) {
        if ([managedObjectContext hasChanges] && ![managedObjectContext save:&error]) {
            // Replace this implementation with code to handle the error appropriately.
            // abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
            NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
            abort();
        }
    }
}
 
#pragma mark - Core Data stack
 
// Returns the managed object context for the application.
// If the context doesn't already exist, it is created and bound to the persistent store coordinator for the application.
- (NSManagedObjectContext *)managedObjectContext
{
    if (_managedObjectContext != nil) {
        return _managedObjectContext;
    }
     
    NSPersistentStoreCoordinator *coordinator = [self persistentStoreCoordinator];
    if (coordinator != nil) {
        _managedObjectContext = [[NSManagedObjectContext alloc] init];
        [_managedObjectContext setPersistentStoreCoordinator:coordinator];
    }
    return _managedObjectContext;
}
 
// Returns the managed object model for the application.
// If the model doesn't already exist, it is created from the application's model.
- (NSManagedObjectModel *)managedObjectModel
{
    if (_managedObjectModel != nil) {
        return _managedObjectModel;
    }
    NSURL *modelURL = [[NSBundle mainBundle] URLForResource:@"NewsModel" withExtension:@"momd"];
    _managedObjectModel = [[NSManagedObjectModel alloc] initWithContentsOfURL:modelURL];
    return _managedObjectModel;
}
 
// Returns the persistent store coordinator for the application.
// If the coordinator doesn't already exist, it is created and the application's store added to it.
- (NSPersistentStoreCoordinator *)persistentStoreCoordinator
{
    if (_persistentStoreCoordinator != nil) {
        return _persistentStoreCoordinator;
    }
     
    NSURL *storeURL = [[self applicationDocumentsDirectory] URLByAppendingPathComponent:@"NewsModel.sqlite"];
     
    NSError *error = nil;
    _persistentStoreCoordinator = [[NSPersistentStoreCoordinator alloc] initWithManagedObjectModel:[self managedObjectModel]];
    if (![_persistentStoreCoordinator addPersistentStoreWithType:NSSQLiteStoreType configuration:nil URL:storeURL options:nil error:&error]) {
        NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
        abort();
    }
     
    return _persistentStoreCoordinator;
}
 
#pragma mark - Application's Documents directory
 
// Returns the URL to the application's Documents directory.获取Documents路径
- (NSURL *)applicationDocumentsDirectory
{
    return [[[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask] lastObject];
}
 
//插入数据
- (void)insertCoreData:(NSMutableArray*)dataArray
{
    NSManagedObjectContext *context = [self managedObjectContext];
    for (News *info in dataArray) {
        News *newsInfo = [NSEntityDescription insertNewObjectForEntityForName:TableName inManagedObjectContext:context];
        newsInfo.newsid = info.newsid;
        newsInfo.title = info.title;
        newsInfo.imgurl = info.imgurl;
        newsInfo.descr = info.descr;
        newsInfo.islook = info.islook;
         
        NSError *error;
        if(![context save:&error])
        {
            NSLog(@"不能保存：%@",[error localizedDescription]);
        }
    }
}
 
//查询
- (NSMutableArray*)selectData:(int)pageSize andOffset:(int)currentPage
{
    NSManagedObjectContext *context = [self managedObjectContext];
     
    // 限定查询结果的数量
    //setFetchLimit
    // 查询的偏移量
    //setFetchOffset
     
    NSFetchRequest *fetchRequest = [[NSFetchRequest alloc] init];
 
    [fetchRequest setFetchLimit:pageSize];
    [fetchRequest setFetchOffset:currentPage];
     
    NSEntityDescription *entity = [NSEntityDescription entityForName:TableName inManagedObjectContext:context];
    [fetchRequest setEntity:entity];
    NSError *error;
    NSArray *fetchedObjects = [context executeFetchRequest:fetchRequest error:&error];
    NSMutableArray *resultArray = [NSMutableArray array];
     
    for (News *info in fetchedObjects) {
        NSLog(@"id:%@", info.newsid);
        NSLog(@"title:%@", info.title);
        [resultArray addObject:info];
    }
    return resultArray;
}
 
//删除
-(void)deleteData
{
    NSManagedObjectContext *context = [self managedObjectContext];
    NSEntityDescription *entity = [NSEntityDescription entityForName:TableName inManagedObjectContext:context];
 
    NSFetchRequest *request = [[NSFetchRequest alloc] init];
    [request setIncludesPropertyValues:NO];
    [request setEntity:entity];
    NSError *error = nil;
    NSArray *datas = [context executeFetchRequest:request error:&error];
    if (!error && datas && [datas count])
    {
        for (NSManagedObject *obj in datas)
        {
            [context deleteObject:obj];
        }
        if (![context save:&error])
        {
            NSLog(@"error:%@",error);  
        }  
    }
}
//更新
- (void)updateData:(NSString*)newsId  withIsLook:(NSString*)islook
{
    NSManagedObjectContext *context = [self managedObjectContext];
 
    NSPredicate *predicate = [NSPredicate
                              predicateWithFormat:@"newsid like[cd] %@",newsId];
     
    //首先你需要建立一个request
    NSFetchRequest * request = [[NSFetchRequest alloc] init];
    [request setEntity:[NSEntityDescription entityForName:TableName inManagedObjectContext:context]];
    [request setPredicate:predicate];//这里相当于sqlite中的查询条件，具体格式参考苹果文档
     
    //https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/Predicates/Articles/pCreating.html
    NSError *error = nil;
    NSArray *result = [context executeFetchRequest:request error:&error];//这里获取到的是一个数组，你需要取出你要更新的那个obj
    for (News *info in result) {
        info.islook = islook;
    }
     
    //保存
    if ([context save:&error]) {
        //更新成功
        NSLog(@"更新成功");
    }
}
@end
```


---------------

### 5 Plist

Plist (NSArray\NSDictionary） 也可以来存储数据。

全名 Property List，属性列表文件，它是一种用来存储串行化后的对象的文件。属性列表文件的扩展名为.plist ，因此通常被称为 plist文件。文件是xml格式的。

详细可参考[http://blog.csdn.net/mad1989/article/details/8560796](http://blog.csdn.net/mad1989/article/details/8560796)



