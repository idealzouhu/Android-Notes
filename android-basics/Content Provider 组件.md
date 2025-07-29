### 什么是 ContentProvider

出于安全考虑，使用 Android 数据持久化技术（包括文件存储、SharedPreferences 存储以及数据库存储）持久化技术所保存的数据只能在当前应用程序中访问。但是，我们在有些场景下么要将我们程序中的数据共享给其他程序。例如系统的通讯录程序，它的数据库中保存了很多联 系人信息，如果这些数据都不允许第三方程序进行访问的话，恐怕很多应用的功能就要大打折 扣了。

Android 官方推荐使用更加安全可靠的 ContentProvider 来实现**跨程序数据共享**的功能。ContentProvider主要用于在不同的应用程序之间实现数据共享的功能，它提供了一套完整的机制，允许一个程序访问另一个程序中的数据，同时还能保证被访问数据的安全性。不同于文件存储和 SharedPreferences 存储中的两种全局可读写操作模式，ContentProvider **可以选择只对哪一部分数据进行共享**，从而保证我们程序中的隐私数据不会有泄漏的风险。



### ContentProvider 的使用方法

ContentProvider的用法一般有两种：

- 使用现有的 ContentProvider 读取和操作相应程序中的数据；
- 创建自己的 ContentProvider 给程序的数据提供外部访问接口。

如果一个应用程序通过ContentProvider对其数据提供了外部访问接口，那么任何其他的应用 程序都可以对这部分数据进行访问。Android系统中自带的通讯录、短信、媒体库等程序都提供 了类似的访问接口，这就使得第三方应用程序可以充分地利用这部分数据实现更好的功能。



### ContentResolver 的基本用法

对于每一个应用程序来说，**如果想要访问 ContentProvider 中共享的数据，就一定要借助 ContentResolver类**，可以通过Context中的getContentResolver()方法获取该类的实 例。

ContentResolver中提供了一系列的方法用于对数据进行增删改查操作，其中insert() 方法用于添加数据，update()方法用于更新数据，delete()方法用于删除数据，query()方 法用于查询数据。

不同于SQLiteDatabase，ContentResolver中的增删改查方法都是不接收表名参数的，而是 使用一个Uri参数代替，这个参数被称为内容URI。**内容URI给ContentProvider中的数据建立了唯一标识符**，它主要由两部分组成：authority 和 path。

- authority 是用于对不同的应用程序做区分的，一般为了避免冲突，会采用应用包名的方式进行命名。比如某个应用的包名是 com.example.app，那么该应用对应的authority就可以命名为 com.example.app.provider。

- path则是用于对同一应用程序中不同的表做区分的，通常会添 】加到authority的后面。比如某个应用的数据库里存在两张表table1和table2，这时就可以将 path分别命名为/table1和/table2，

把authority和path进行组合，内容URI就变成了 com.example.app.provider/table1和com.example.app.provider/table2。

