# Updated version of VFPCompression - Password Protection

来自：http://www.sweetpotatosoftware.com/SPSBlog/PermaLink,guid,6cfb3b79-5a25-42f6-8eba-00be6240f61c.aspx

翻译：xinjie

08/11/2007 - 更新文档和示例文件... 原来的文档和例子中存在一些错误。这个 FLL 并不需要修正。我编辑博客中的文档和例子是因为一些简单的错误。我再这里表示道歉。

### VFPCompression 库
一个来自 Universal Thread 的成员说，在这个库中增加密码保护功能将是有用的。所以，我增加了这个功能。注意，一些函数增加了一个可选的 cPassword 参数，下面的文档有显示。zlib 源代码也随之更新，所以在 zlib 中修复的在 vfpcompression.fll 中也做了更新。

在这个类库的下一个版本，我希望可以提供其他的压缩格式/压缩算法，并且可以生成自解压文件。如果你有任何关于这个类库的更号的改进提议或者是你发现的 bug ，请不要犹豫，立刻和我联系。

### VFP Compression 更新：

下载 [VFPCompression FLL  (大约 35 KB )](http://www.sweetpotatosoftware.com/files/vfpcompression.zip)

**_(译者注：此下载文件为原作者最终提供的版本)_**

**[最后一个版本的示例代码和使用文档](http://www.sweetpotatosoftware.com/blog/index.php/2008/12/01/vfpcompression-update-fix-and-zipfile-comment-support/)**

### VFP Compression 示例代码：
```foxpro
*!* Example 1
SET LIBRARY TO LOCFILE("vfpcompression.fll")
?ZipFileQuick("C:\MyFile.txt")
?ZipFileQuick("C:\MyFile.txt", "MyPassword")
SET LIBRARY TO

*!* Example 2
SET LIBRARY TO LOCFILE("vfpcompression.fll")
?ZipFolderQuick("C:\MyFolder")
?ZipFolderQuick("C:\MyFolder2", .T., "MyPassword")
?ZipFolderQuick("C:\MyFolder2", .F., "MyPassword")
SET LIBRARY TO

*!* Example 3
SET LIBRARY TO LOCFILE("vfpcompression.fll")
?UnzipQuick("C:\MyFile.zip", "C:\")
?UnzipQuick("C:\MyFolder.zip", "C:\", .T.)
?UnzipQuick("C:\MyFolder.zip", "C:\", .F., "MyPassword")
SET LIBRARY TO

*!* Example 4
SET LIBRARY TO LOCFILE("vfpcompression.fll")
?ZipOpen("MyZipFile.zip", "C:\", .F.)
?ZipFile("C:\SomeFile.txt", .F.)
?ZipFile("C:\AnotherFile.txt", .F., "MyPassword")
?ZipClose()
SET LIBRARY TO

*!* Example 5
SET LIBRARY TO LOCFILE("vfpcompression.fll")
?UnzipQuick("C:\MyZipFile.zip","C:\",.F.)
?UnzipQuick("C:\MyZipFile.zip","C:\",.F., "MyPassword")
SET LIBRARY TO

*!* Example 6
SET LIBRARY TO LOCFILE("vfpcompression.fll")
?ZipOpen("C:\MyZipFile.zip")
*!* ?ZipOpen("MyZipFile.zip", "C:\", .F.)
?ZipFolder("C:\MyFolder", .F.)
?ZipFolder("C:\AnotherFolder", .F., "MyPassword")
?ZipClose()
SET LIBRARY TO

*!* Example 7
SET LIBRARY TO LOCFILE("vfpcompression.fll")
CLEAR
lcOriginal = REPLICATE("Visual FoxPro Rocks!",100)
?"Original Length: " + TRANSFORM(LEN(lcOriginal))
?
lcCompressed = ZipString(lcOriginal)
?"Compressed: " + lcCompressed
?"Compressed Length: " + TRANSFORM(LEN(lcCompressed))
?
?"Length Savings: " + TRANSFORM(LEN(lcOriginal) - LEN(lcCompressed)) + " bytes"
?
lcUncompressed = UnzipString(lcCompressed)
*!* ?"Uncompressed: " + lcUncompressed
?"Uncompressed Length: " + TRANSFORM(LEN(lcUncompressed))
?"Equals Original: " + IIF(lcUncompressed == lcOriginal, "YES", "NO")
IF !(lcUncompressed == lcOriginal)
EXIT
ENDIF
SET LIBRARY TO

*!* Example 8
SET LIBRARY TO LOCFILE("vfpcompression.fll")
?ZipOpen("MyZip.zip", "C:\", .F.)
?ZipFile("C:\MyFile1.txt", .F., "MyPassword1") && passwords for files can be different
?ZipFile("C:\MyFile2.txt", .F., "MyPassword2") && passwords for files can be different
?ZipFile("C:\MyFile3.txt", .F.) && And some files can not be given a password
*!* could zip other files and folders here before close
?ZipClose()
SET LIBRARY TO
```

### VFP Compression 文档：

#### 函数 ZipString()

声明：ZipString(cString[, nLevel])

参数：

cString - 你想要压缩的字符串

nLevel - 使用1-9中哪种压缩级别 (1 是最快的压缩速度，9 是最大程度的压缩)。这个参数的默认值是 6 。

返回值：

字符型 - 传入字符串的压缩版本。

注释：

这个函数对于C/S程序得到字符串特别有用（例如备注字段、字符型字段以及XML），它可以在网络传输过程中对这些数据进行压缩，然后在另一端解压缩（使用函数 UnzipString）。

#### 函数 ZipFileQuick()

##### 声明： ZipFileQuick(cFileName, cPassword)

##### 参数：

cFileName - 你想要压缩的带完整路径的文件名。

cPassword - 你所想使用的用于保护压缩文件的密码。

##### 返回值：

逻辑型 - 如果压缩成功返回 .T. ，压缩失败返回 .F. 。

##### 注释：

所创建的压缩文件，具有和传入的文件同样的名字，其扩展名为“.zip”。

#### 函数 ZipFolderQuick()

声明： ZipFileQuick(cFolderName[, lIgnorePaths[, cPassword)

参数：

cFolderName - 你希望压缩的带完整路径的文件夹名。

cPassword - 你想要保护压缩文件的密码。

lIgnorePath - 如果你想在压缩文件中忽略文件的相对路径，可以传递 .T. 到这个参数。默认值是 .F. ，意味着保留相对路径。

返回值：

逻辑型 - 如果压缩成功返回 .T. ，否则返回 .F. 。

注释：

所创建的压缩文件具有和参数 cFolderName 同样的名字，扩展名为“.zip”。

函数 ZipOpen()

声明： ZipOpen(cZipFileName[,cFolderName[,lAppend]])

参数：

cZipFileName - 你想创建的压缩文件名，或者是是带完整路径的压缩文件名。

cFolderName - 存储压缩文件的完整路径文件夹名。

lAppend - 如果压缩文件已经存在，你可以使用 .T. 来在其中追加文件。默认值是 .F. 。

返回值：

逻辑型 - 如果执行成功返回 .T. ，否则返回 .F. 。

注释：

ZipOpen() 需要与 ZipClose() 函数协同使用。通常情况下，使用 ZipOpen() 来创建/打开压缩文件，使用 ZipFile()、ZipFileRelative()和ZipFolder() 函数来压缩文件 和/或 文件夹，然后使用 ZipClose() 函数关闭压缩文件。

函数 ZipClose()

声明：ZipClose()

参数： 无

返回值：

逻辑型- 执行成功返回 .T. ，否则返回 .F. 。

注释：

在使用 ZipOpen() 后必须调用ZipClose() 函数。通常情况下，使用 ZipOpen() 来创建/打开压缩文件，使用 ZipFile()、ZipFileRelative()和ZipFolder() 函数来压缩文件 和/或 文件夹，然后使用 ZipClose() 函数关闭压缩文件。

函数 ZipFile()

声明： ZipFile(cFileName[,lIgnorePath[,cPassword]])

参数： 

cFileName - 你希望要压缩的文件名或带完整路径的文件名。

lIgnorePath - 如果你希望在已经被创建的压缩文件中不保留文件的相对路径，就使用 .T. 。参数的默认值为 .F. ，这意味着保留文件的相对路径。

cPassword - 你想用于保护压缩文件的密码。

返回值：

逻辑型 - 执行成功返回 .T. ，否则返回 .F. 。

注释：

ZipFile() 函数用于ZipOpen() 函数和 ZipClose() 函数之间。通常情况下，使用 ZipOpen() 来创建/打开压缩文件，使用 ZipFile()、ZipFileRelative()和ZipFolder() 函数来压缩文件 和/或 文件夹，然后使用 ZipClose() 函数关闭压缩文件。

参数 cPassword 通常是针对整个压缩文件的，然而这并不是必须的。不同的密码可以针对压缩文件中不同的文件，你甚至可以选择用密码保护压缩文件中指定的文件。

函数 ZipFileRelative()

声明： ZipFileRelative(cFileName[,cRelativePath[, cPassword]])

参数：

cFileName - 你想压缩的文件名或带完整路径的文件名。

cRelativePath - 你想在压缩文件中保存该文件的相对路径。这允许你在压缩文件中设置一个结构（相对路径），它可以是不同于你要压缩的文件的真实的相对路径。

cPassword - 你想用于保护压缩文件的密码。

返回值：

逻辑型 - 执行成功返回 .T. ，否则返回 .F. 。

注释： 

ZipFileRelative() 函数用于ZipOpen() 函数和 ZipClose() 函数之间。通常情况下，使用 ZipOpen() 来创建/打开压缩文件，使用 ZipFile()、ZipFileRelative()和ZipFolder() 函数来压缩文件 和/或 文件夹，然后使用 ZipClose() 函数关闭压缩文件。

参数 cPassword 通常是针对整个压缩文件的，然而这并不是必须的。不同的密码可以针对压缩文件中不同的文件，你甚至可以选择用密码保护压缩文件中指定的文件。

函数 ZipFolder()

声明： ZipFolder(cFolderName[,lIgnorePaths[, cPassword])

参数： 

cFolderName - 你想要压缩的文件夹的完整路径。

lIgnorePaths - 如果你想在压缩文件中忽略文件夹的相对路径，可以传递参数 .T. 。默认值是 .F. ，意味着将保留相对路径。

cPassword - 你想用于保护压缩文件的密码。

返回值：

逻辑型 - 执行成功返回 .T. ，否则返回 .F. 。

注释：

ZipFolder() 函数用于ZipOpen() 函数和 ZipClose() 函数之间。通常情况下，使用 ZipOpen() 来创建/打开压缩文件，使用 ZipFile()、ZipFileRelative()和ZipFolder() 函数来压缩文件 和/或 文件夹，然后使用 ZipClose() 函数关闭压缩文件。

函数 UnzipString()

声明： UnzipString(cString)

参数：

cString - 你想解压的被压缩的字符串。

返回值：

字符型 - 解压后的字符串。

注释： 

这个函数只能解压由 ZipString() 函数或 zlib 中 compress() 函数、compress2() 函数压缩的字符串。

函数 UnzipQuick()

声明： UnzipQuick(cZipFileName, cOutputFolderName[, lIgnorePaths[, cPassword]])

参数：

cZipFileName - 你想解压的带完整路径的文件名。

cOutputFolderName - 你想将解压后的文件放置在哪个完整路径下。

cPassword - 用于解压文件的密码。

lIgnorePaths - 如果你想忽略压缩文件中的相对路径，传递参数 .T. 。默认值是 .F. ，这意味着解压后保留文件的相对路径。

返回值： 

逻辑型 - 执行成功返回 .T. ，否则返回 .F. 。

注释：

压缩文件中的路径及文件会被解压到参数 cZipFileName 参数指定的文件夹下。

函数 UnzipOpen()

声明： UnzipOpen(cZipFileName)

参数：

cZipFileName - 你想解压的文件名或带完整路径的文件名。

返回值：

逻辑型 - 执行成功返回 .T. ，否则返回 .F. 。

注释：

UnzipOpen() 函数与 UnZipClose() 函数配合使用。通常是这样来调用这一系列函数：使用 UnzipOpen() 打开压缩文件，使用 Unzip()、UnzipTo()、UnzipByIndex()、UnzipFile() 函数来解压文件 和/或 文件夹，然后使用 UnzipClose() 函数关闭压缩文件。

函数 UnzipClose()

声明：UnzipClose()

参数： None

返回值：

逻辑型 - 执行成功返回 .T. ，否则返回 .F. 。

注释： 

UnzipClose() 函数必须在 UnzipOpen() 函数后调用。通常是这样来调用这一系列函数：使用 UnzipOpen() 打开压缩文件，使用 Unzip()、UnzipTo()、UnzipByIndex()、UnzipFile() 函数来解压文件 和/或 文件夹，然后使用 UnzipClose() 函数关闭压缩文件。

函数 Unzip()

声明：Unzip(lIgnorePaths[, cPassword])

参数：

lIgnorePaths - 如果你想忽略压缩文件中的相对路径，就传递参数 .T. 。默认值是 .F. ，意味着保留相对路径。

cPassword - 用于解压缩文件的密码。

返回值：

逻辑型 - 执行成功返回 .T. ，否则返回 .F. 。

注释：

Unzip() 函数用于 UnzipOpen() 函数和 UnzipClose() 之间来解压整个压缩文件。其中的文件和文件夹将被解压到压缩文件所在的文件夹中（如果不使用前面的 UnzipSetFolder()函数）。通常是这样来调用这一系列函数：使用 UnzipOpen() 打开压缩文件，使用 Unzip()、UnzipTo()、UnzipByIndex()、UnzipFile() 函数来解压文件 和/或 文件夹，然后使用 UnzipClose() 函数关闭压缩文件。

函数 UnzipTo()

声明： UnzipTo(cOutputFolderName[, lIgnorePaths[, cPassword]])

参数：

cOutputFolderName - 压缩文件要被释放到的文件夹名。

lIgnorePaths - 如果你想忽略压缩文件中的相对路径，就传递参数 .T. 。默认值是 .F. ，意味着保留相对路径。

cPassword - 用于解压文件的密码。

返回值：

逻辑型 - 执行成功返回 .T. ，否则返回 .F. 。

注释：

UnzipTo() 函数用于 UnzipOpen() 函数和 UnzipClose() 之间。这个函数与 UnzipSetFolder() 函数及 Unzip() 函数很相似。通常是这样来调用这一系列函数：使用 UnzipOpen() 打开压缩文件，使用 Unzip()、UnzipTo()、UnzipByIndex()、UnzipFile() 函数来解压文件 和/或 文件夹，然后使用 UnzipClose() 函数关闭压缩文件。

函数： UnzipFile()

声明：UnzipFile(cOutputFolderName[, lIgnorePaths[, cPassword]])

参数：

cOutputFolderName - 当前包含在压缩文件中的文件被解压后所存放的文件夹名。

lIgnorePaths - 如果你想忽略压缩文件中的相对路径，就传递参数 .T. 。默认值是 .F. ，意味着保留相对路径。

cPassword - 用于解压文件的密码。

返回值：

逻辑型 - 执行成功返回 .T. ，否则返回 .F. 。

注释：

UnzipFile() 函数用于 UnzipOpen() 函数和 UnzipClose() 之间，用来解压在压缩文件中的当前所选择的要解压的文件。UnzipFile() 函数需要与 UnzipGotoTopFile（）、UnzipGotoNextFile（）、UnzipGotoFileByName（）以及 UnzipGotoFileByIndex（）协同使用。你可以认为压缩文件中的内容是一个表中的记录。从这个角度出发，UnzipGotoTopFile（）、UnzipGotoNextFile（）、UnzipGotoFileByName（）和 UnzipGotoFileByIndex（）就像是移动记录指针，UnzipFile（）函数用于解压当前记录指针所指向的文件。

参数 cPassword 通常情况下对于压缩文件中的文件是一样的，然而它并不需要一样。谨记一点，在一个压缩文件中，可以对不同的文件指定密码，并且，当你需要解压这样的存档文件时，你需要随之更改参数 cPassword 的值。

函数 UnzipByIndex()

声明： UnzipByIndex(nIndex[, cOutputFolderName[,lIgnorePaths, cPassword]]])

参数：

nIndex - 被解压的文件的索引号。

cOutputFolderName - 被解压出的文件所存放的文件夹名。

lIgnorePaths - 如果你想忽略压缩文件中的相对路径，就传递参数 .T. 。默认值是 .F. ，意味着保留相对路径。

cPassword - 用于解压文件的密码。

返回值：

逻辑型 - 执行成功返回 .T. ，否则返回 .F. 。

注释： 

UnzipByIndex() 函数用于 UnzipOpen() 函数和 UnzipClose() 函数之间，通过压缩文件中的位置（索引）来解压文件。UnzipByIndex() 需要与函数 UnzipFileCount（）函数协同使用。你可以认为压缩文件中的内容是一个表中的记录。从这个角度来讲，你可以认为使用  UnzipFileCount（）函数获得的是表中的记录数，并且，UnzipByIndex（）可以使用记录号来解压一个具体的文件。

参数 cPassword 通常情况下对于压缩文件中的文件是一样的，然而它并不需要一样。谨记一点，在一个压缩文件中，可以对不同的文件指定密码，并且，当你需要解压这样的存档文件时，你需要随之更改参数 cPassword 的值。

函数 UnzipFileCount()

声明：UnzipFileCount()

参数： 无

返回值：

逻辑型 - 执行成功返回 .T. ，否则返回 .F. 。

注释：

UnzipFileCount() 函数用于 UnzipOpen() 函数和 UnzipClose() 函数之间，用以找到包含在压缩文件中的文件数目。它事实上没有解压任何文件。UnzipFileCount() 函数与UnzipByIndex（）函数协同使用。你可以认为压缩文件中的内容是一个表中的记录。从这个角度来讲，你可以认为使用 UnzipFileCount（）函数获得的是表中的记录数，并且，UnzipByIndex（）可以使用记录号来解压一个具体的文件。

函数 UnzipSetFolder()

声明： UnzipSetFolder(cOutputFolderName)

参数： 

cOutputFolderName - 压缩文件解压后存放的文件夹名。

返回值： 

逻辑型 - 执行成功返回 .T. ，否则返回 .F. 。

注释：

UnzipSetFolder() 函数用于UnzipOpen() 函数和 UnzipClose() 函数之间，用于设置存放解压出的内容的文件夹。它事实上并不解压任何东西。通常是这样来调用这一系列函数：使用 UnzipSetFolder() 设置输出文件夹，使用 Unzip()、UnzipTo()、UnzipByIndex()、UnzipFile() 函数来解压文件 和/或 文件夹，然后使用 UnzipClose() 函数关闭压缩文件。

函数 UnzipGotoTopFile()

声明： UnzipGotoTopFile([cExtension])

参数： 

cExtension - 文件扩展名，用以过滤相同的文件类型。对所有其他类型文件不予理睬，并且仅仅在压缩文件中选择被指定的文件类型中的第一个文件。

返回值：

逻辑型 - 执行成功返回 .T. ，否则返回 .F. 。

注释：

UnzipGotoTopFile() 函数用于 UnzipOpen() 函数和 UnzipClose() 函数之间，用于在当前打开的压缩文件中选择一个特定的文件。UnzipGotoTopFile() 需要与 UnzipFile（）协同使用以便从压缩文件中解压一个特定的文件。利用可选参数，你可以选择特定文件类型中的第一个文件。你可以认为压缩文件中的内容是一个表中的记录。从这个角度来讲，你可以认为使用 UnzipGotoTopFile（）、UnzipGotoNextFile()、UnzipGotoFileByName（）和 UnzipGotoFileByIndex（）函数是用来移动记录指针的。并且 UnzipFile（）函数用于解压当前记录指针所指向的文件。

函数 UnzipGotoNextFile()

声明： UnzipGotoNextFile([cExtension])

参数：

cExtension - 文件扩展名，用以过滤相同的文件类型。对所有其他类型文件不予理睬，并且仅仅在压缩文件中选择被指定的文件类型中的下一个文件。

返回值：

逻辑型 - 执行成功返回 .T. ，否则返回 .F. 。

注释：

UnzipGotoNextFile() 函数用于 UnzipOpen() 函数和 UnzipClose() 函数之间，用于在当前打开的压缩文件中选择一个特殊的文件。 UnzipGotoNextFile() 需要与 UnzipFile（）函数协同工作，从压缩文件中解压一个指定的文件。通过使用可选参数你可以选择特定文件类型的下一个文件。你可以认为压缩文件中的内容是一个表中的记录。从这个角度来讲，你可以认为使用 UnzipGotoTopFile（）、UnzipGotoNextFile()、UnzipGotoFileByName（）和 UnzipGotoFileByIndex（）函数是用来移动记录指针的。并且 UnzipFile（）函数用于解压当前记录指针所指向的文件。

函数 UnzipGotoFileByName()

声明： UnzipGotoFileByName(cFileName[, lIgnoreFilePath])

参数：

cFileName - 你想要选择的压缩文件中的文件名。你可以指定一个相对路径来加快搜索。

lIgnoreFilePath - 如果你仅仅想找到特定的文件，你可以传递参数 .T. ，并且会忽略文件的相对路径。当前压缩文件中与参数 cFileName 匹配的第一个文件会被选择。

返回值： 

逻辑型 - 执行成功返回 .T. ，否则返回 .F. 。

注释：

UnzipGotoFileByName() 函数用于 UnzipOpen() 函数和 UnzipClose() 函数之间，用于在当前打开的压缩文件中通过文件名 和/或 相对路径来选择一个特定的文件。UnzipGotoFileByName() 与 UnzipFile（）协同工作可以从打开的压缩文件中，解压一个特定的文件。你可以认为压缩文件中的内容是一个表中的记录。从这个角度来讲，你可以认为使用 UnzipGotoTopFile（）、UnzipGotoNextFile()、UnzipGotoFileByName（）和 UnzipGotoFileByIndex（）函数是用来移动记录指针的。并且 UnzipFile（）函数用于解压当前记录指针所指向的文件。

函数 UnzipGotoFileByIndex()

声明： UnzipGotoFileByIndex([nIndex])

参数：

nIndex - 要选择的文件的索引号。

返回值：

逻辑型 - 执行成功返回 .T. ，否则返回 .F. 。

Remarks:

UnzipGotoFileByIndex() 函数用于 UnzipOpen() 函数和 UnzipClose() 函数之间，用于在当前打开的压缩文件中通过存在在压缩文件中的位置来选择一个特定的文件。UnzipGotoFileByIndex() 函数与 UnzipFile（）函数协同工作用于解压当前压缩文件中的特定文件。你可以认为压缩文件中的内容是一个表中的记录。从这个角度来讲，你可以认为使用 UnzipGotoTopFile（）、UnzipGotoNextFile（）、UnzipGotoFileByName（）和 UnzipGotoFileByIndex（）函数是用来移动记录指针。UnzipFile() 函数用于当前记录指针所指向的文件。

函数 UnzipAFileInfoByIndex()

声明： UnzipAFileInfoByIndex(cArrayName, nIndex)

参数： 

cArrayName - 要创建的包含文件信息的VFP数组名。

nIndex - 你想返回的相关文件信息的索引号。

返回值： 

逻辑型 - 执行成功返回 .T. ，否则返回 .F. 。

所创建的数组有13行（元素）。每一行都包含文件不同方面的信息。下表描述了每一行所包含的内容和数据类型：

行	数组内容	数据类型 
1	File Name （文件名）	Character
2	Comment （注释）	Character
3	Version （版本号）	Numeric
4	Version Needed （版本号）	Numeric
5	Flags （标志）	Numeric
6	Compression Method （压缩方法）	Numeric
7	DOS Date （日期）	Datetime
8	CRC （循环冗余校验）	Numeric
9	Compressed Size （压缩后的文件大小）	Numeric
10	Uncompressed Size （原始的文件大小）	Numeric
11	Internal Attribute （内在属性）	Numeric
12	External Attribute （外部属性）	Numeric
13	Folder （文件夹）	Logical
注释： 

所创建的数组的名字由参数 cArrayName 决定。如果同名数组存在则释放数组并重新创建。UnzipAFileInfoByIndex() 函数用于 UnzipOpen() 函数和 UnzipClose() 之间，用来显示一个特定文件的信息。该文件可以通过存储在当前压缩文件的位置找到。你可以认为压缩文件中的内容是一个表中的记录。从这个角度来讲，你可以使用 UnzipAFileInfoByIndex（）函数来查找文件，并且找到压缩文件中某个记录的信息。

函数 UnzipAFileInfo()

声明： UnzipAFileInfo(cArrayName)

参数：

cArrayName - 要创建的包含文件信息的VFP数组名。

返回值： 

逻辑型 - 执行成功返回 .T. ，否则返回 .F. 。

所创建的数组有13行（元素）。每一行都包含文件不同方面的信息。下表描述了每一行所包含的内容和数据类型：

行	数组内容 	数据类型 
1	File Name  （文件名）	Character
2	Comment （注释）	Character
3	Version （版本号）	Numeric
4	Version Needed （版本号）	Numeric
5	Flags （标志）	Numeric
6	Compression Method （压缩方法）	Numeric
7	DOS Date （日期）	Datetime
8	CRC （循环冗余校验）	Numeric
9	Compressed Size （压缩后的文件大小）	Numeric
10	Uncompressed Size （原始的文件大小）	Numeric
11	Internal Attribute （内在属性）	Numeric
12	External Attribute （外部属性）	Numeric
13	Folder （文件夹）	Logical
Remarks:

所创建的数组的名字由参数 cArrayName 决定。如果同名数组存在则释放数组并重新创建。UnzipAFileInfo() 函数用于 UnzipOpen() 函数和 UnzipClose() 之间，用来显示一个特定文件的信息。使 UnzipAFileInfo（）函数与 UnzipGotoTopFile（）、UnzipGotoNextFile（）、UnzipGotoFileByName（）和 UnzipGotoFileByIndex（）协同工作可以返回存储在当前打开的压缩文件中的特定文件信息。 你可以认为压缩文件中的内容是一个表中的记录。从这个角度来讲，你可以使用  UnzipAFileInfo（）函数函数来查找文件，并且找到压缩文件中某个记录的信息。 
