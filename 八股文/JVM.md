## 参数

### 1. 堆内存

> Java 虚拟机所管理的内存中最大的一块，Java 堆是所有线程共享的一块内存区域，在虚拟机启动时创建。**此内存区域的唯一目的就是存放对象实例，几乎所有的对象实例以及数组都在这里分配内存**

#### 2.1. 显式指定堆内存`-Xms`和`-Xmx`

```shell
-Xms<heap size>[unit]
-Xmx<heap size>[unit]
```

- **heap size** 表示要初始化内存的具体大小。
- **unit** 表示要初始化内存的单位。单位为***“ g”*** (GB) 、***“ m”\***（MB）、***“ k”\***（KB）。

如果我们要为 JVM 分配最小 2 GB 和最大 5 GB 的堆内存大小，我们的参数应该这样来写：

```bash
-Xms2G -Xmx5G
```

#### 2.2. 显式新生代内存

1. **`-XX:NewSize`** & **`-XX:MaxNewSize`**

   ```shell
   -XX:NewSize=<young size>[unit]
   -XX:MaxNewSize=<young size>[unit]
   ```

   ps: 如果我们要为 新生代分配 最小 256m 的内存，最大 1024m 的内存我们的参数应该这样来写：

   ```shell
   -XX:NewSize=256m
   -XX:MaxNewSize=1024m
   ```

2. **`-Xmn<young size>[unit]`**

   如果我们要为 新生代分配 256m 的内存（NewSize 与 MaxNewSize 设为一致），我们的参数应该这样来写：

   ```shell
   -Xmn256m
   ```

#### 2.3. 显式指定永久代/元空间的大小

```shell
-XX:MetaspaceSize=N #设置 Metaspace 的初始（和最小大小）
-XX:MaxMetaspaceSize=N #设置 Metaspace 的最大大小，如果不指定大小的话，随着更多类的创建，虚拟机会耗尽所有可用的系统内存。
```

