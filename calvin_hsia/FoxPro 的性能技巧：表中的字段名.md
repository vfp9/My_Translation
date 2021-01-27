# Foxpro Performance tip: field name lookup for tables
作者：Calvin_Hsia

时间：2004年12月14日，星期二，2:33 PM

翻译：xinjie
 
    当FoxPro打开或使用表或游标时，我们是知道在表中使用的字段名。 

    例如，下面的语句创建了一个具有三个字段的表。 
```foxpro
CREATE TABLE foo (lastname c(10), firstname c(10),address c(20))
```
 

    当遇到这样一行代码时： 
```foxpro
            x = lastname + firstname
```
    Fox 需要知道“lastname”是当前打开表的字段名还是一个变量。

 

    所有在 FoxPro 中使用的名字，实际都存储在“name table”中，使用一个专门的整数索引来描述所这些名字，它被称为 NTls，也就是 Name Table 的索引。 

 

    名字可以是一个被使用的变量名、一个属性/方法名，或者是一个本地的属性/方法名，像 ActiveProject 。命令和函数的名字并不放在 Name Table 中。

 

    但使用一个表时（打开或创建），VFP 为针对每个字段创建一个数组结构。结构内容就是表示字段名的 NTl 。VFP 在 Name Table 中搜索字段名，并返回一个索引。如果没有找到，那么就在其中追加记录。

 

    在过去（FoxPro 7.0 以前）当打开一个表时，数组的大小就被分配好了(曾经使用过的最大的 Name Table 索引）。所有的数组元素都被设置为 0，然后遍历所有字段设置n个数组元素为n+1个（这些字段并不包含在已有的n个字段中）。然后，如果 “lastname”、“fristname”和“address”的NTls 是 200、210和220，那么第200个元素会被设置为1，第210个元素会被设置为2，第220个元素会被设置为3。为了提升性能，数组会调整到所发现的最大 NTl 。

 

    这样的方式工作的很好，并且在查找一个字段名时非常快：如果你想知道 NTI 200 是否在表中，你只需要检查第200个元素是否是非零值，它可以指出包含在表中的字段的位置。

 

    如果在 name table 中有 3000 个名字，那么要查询的数组就有 3000 个元素，所有的元素都是零，除了 # 字段（FCOUNT()）元素。(all of which were zero except for the # of fields (FCOUNT()) elements. )

 

    在后来的VFP版本中，增加了新的特性，默认 name table 的尺寸随着属性 # 和基类的变化而变化。

 

    如果你打开一打（十二个）表，每个表或许都超过十二个字段，利用复杂的代码运算（它们将添加到 name table 中），然后打开其他的表，name table 的增长意味着查询数组要花很长的时间。

 

    为了获得最好的性能，就要尽量少的访问磁盘。磁盘访问的速度远远低于内存访问，所以使用内存是很聪明的。

 

    在VFP7（2001年六月）中我改变了查询数组的算法，利用稀疏的查找数组方法不会消耗所有的内存。这是典型的平衡内存空间和执行时间的方法。

 

    不用创建用于查询的数组，包含字段名的数组是为了线性查找给出的 NTI 。使用一个表来搜索大量的字段会明显变慢，但是这样可以避免使用一个巨大的数组，并使宝贵的内存可用于其他的任务。

 

    通过下面的代码，可以看到是使用线性来搜索字段。

    这里创建了一个含有255个字段的表，并且在循环中对变量“x”赋值。当表被关闭或者字段很少时，循环非常快。当字段很多时，访问所有字段需要的时间比较长。判断一个 NTI 是否是一个字段意味着在当前工作区中遍历整个字段列表。

 

    如果 NTI 不是一个字段 (像下面的“y”)，就要线性搜索到列表末尾来判断它是否是一个字段，所以性能就会变差。一个简单的提高速度的方式（并且可以提高代码的可读性、降低维护难度并且加快速度）是对变量使用前缀“m.”，它用来说明变量名“不是”一个字段名，而是一个变量，即使是当一个具有很多字段的表在打开状态时。

 

    重申：如果你的VFP代码使用这样的语法来表示变量“不是”字段引用，即使当前工作区中存在打开的表，前面所说的前缀“m.”也可以提供更好的性能。VFP“知道”它不是一个字段名。

 

    一个 2G 的处理器执行255个整型变量与一个数组结构元素的比较会很快，但是一个很小的代码书写方式的改变可以消除循环时，我们为什么不用呢？

 

 

*试验具有2到255个字段的表

#define NFLDS 255 &&创建一个具有255个字段的表

 

DIMENSION aflds[NFLDS,5]

FOR i = 1 TO NFLDS

      aflds[i,1]="fld"+PADL(i,3,"0")

      aflds[i,2]="i"

      aflds[i,3]=4

      aflds[i,4]=0

ENDFOR

 

CREATE CURSOR test FROM ARRAY aflds

INSERT INTO test (fld001) VALUES (0)

 

use   && 注释掉这一行来实验当表打开和关闭后的速度

y=1

nsec=SECONDS()

FOR i = 1 to 1000000

      x=y

*     x=m.y

*     x=fld255

*     x=test.fld255

ENDFOR

?SECONDS()-nsec

RETURN

 

 

最初的BUG报告 (只有在VFP6中才可以重现)

 

*Steps to Repro:

CLEAR all

CLEAR

memst=VAL(SYS(1016))

?SYS(1011),SYS(1016)

FOR i = 1 to 1000

     

      mstr='e'+TRANSFORM(INT(SECONDS()*1000))

      CREATE CURSOR foo ((mstr) c(10))

      ?SYS(1011),SYS(1016), VAL(SYS(1016)) - memst

      use in foo

ENDFOR

RETURN

 

结果： 

第1列的数值是一个常数，但是第2列中句柄占用的内存持续增加。

 

预期结果：

并不会使用这么多的内存。
