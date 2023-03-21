# BigDecimal

用于浮点数的计算，不会造成精度丢失。

**浮点数之间的等值判断，基本数据类型不能用 == 来比较，包装数据类型不能用equals来判断**

<mark>错误情况</mark>：

```java
float a = 1.0F-0.9F;
float b = 0.9F-0.8F;

if (a==b) {
    //判断结果为false
}

Float x = Float.valueOf(a);
Float y = Float.valueOf(b);
if(x.equals(y)) {
    //判断结果为false
}
```

<mark>正确情况：</mark>

```java
(1)指定一个误差范围，两个浮点数的差值在此范围内，则认为是相等的
float a = 1.0F-0.9F;
float b = 0.9F-0.8F;
float diff = 1e-6F;

if(Math.abs(a-b) < diff) {
    System.out.println("true");
}


(2) 使用BigDecimal来定义值，再进行浮点数的运算操作。
BigDecimal a = new BigDecimal("1.0");
BigDecimal b = new BigDecimal("0.9");
BigDecimal c = new BigDecimal("0.8");

BigDecimal x = a.subtract(b);
BigDecimal y = b.subtract(c);

if(x.compareTo(y) == 0) {
    System.out.println("true");
}
```

使用`compareTo`进行等值比较，`equals()`方法会比较值和精度（1.0==1.00 retuan false），`compareTo()`会忽略精度。

## 常见方法

#### 创建

为防止精度丢失，使用`BigDecimal(String val)`构造方法或者`BigDecimal.valueOf(double val)`静态方法创建对象。

#### 加减乘除

`add`方法用于两个`BigDecimal`对象相加，`subtract`方法用于两个BigDecimal对象相减，`multiply`将两个对象相乘，`divide`方法将两个对象相除

```java
BigDecimal a = new BigDecimal("1.0");
BigDecimal b = new BigDecimal("0.9");
System.out.println(a.add(b));// 1.9System.out.println(a.subtract(b));// 0.1
System.out.println(a.multiply(b));// 0.90
System.out.println(a.divide(b));// 无法除尽，抛出 ArithmeticException 异常
System.out.println(a.divide(b, 2, RoundingMode.HALF_UP));// 1.11
```

这里需要注意的是，在我们使用 `divide` 方法的时候尽量使用 3 个参数版本，并且`RoundingMode` 不要选择 `UNNECESSARY`，否则很可能会遇到 `ArithmeticException`（无法除尽出现无限循环小数的时候），其中 `scale` 表示要保留几位小数，`roundingMode` 代表保留规则。

```java
public BigDecimal divide(BigDecimal divisor, int scale, RoundingMode roundingMode) {
    return divide(divisor, scale, roundingMode.oldMode);
}
```

保留规则：

```java
public enum RoundingMode {
   // 2.5 -> 3 , 1.6 -> 2
   // -1.6 -> -2 ,-2.5 -> -3
             UP(BigDecimal.ROUND_UP),
   // 2.5 -> 2 , 1.6 -> 1
   // -1.6 -> -1 , -2.5 -> -2
             DOWN(BigDecimal.ROUND_DOWN),
             // 2.5 -> 3 , 1.6 -> 2
   // -1.6 -> -1 , -2.5 -> -2
             CEILING(BigDecimal.ROUND_CEILING),
             // 2.5 -> 2 , 1.6 -> 1
   // -1.6 -> -2 , -2.5 -> -3
             FLOOR(BigDecimal.ROUND_FLOOR),
       // 2.5 -> 3 , 1.6 -> 2
   // -1.6 -> -2 , -2.5 -> -3
             HALF_UP(BigDecimal.ROUND_HALF_UP),
   //......
}
```
