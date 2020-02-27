# File類

Point：

1. createNewFile\(\),delete\(\),mkdir\(\),mkdirs\(\);
2. exists\(\),isFile\(\),isAbsolute\(\),isDirectory\(\),canRead\(\),canWrite\(\),isHidden\(\);
3. lastModified\(\),length\(\),list\(\),listFiles\(\),renameTo\(\)，getName\(\),getParent\(\),getPath\(\),getAbsolutePath\(\)，delete\(\);
4. 讀取；

## 創建

```java
//在工作空间目录下创建a.txt的文件
File f = new File("a.txt"); 
f.createNewFile(); 

//在G:\路径下创建一个a.txt的文件.如果已经有的话这不会重新创建
File f = new File("G:\\a.txt"); 
f.createNewFile();

//如果路径写成\\a.txt,会在盘符下创建新的文件
File f = new File("\\a.txt"); 
f.createNewFile();


//在工作空间目录下创建a.txt的文件夹
File f = new File("a"); 
f.mkdir(); 

//在G:\路径下创建一个a.txt的文件夹.如果已经有的话这不会重新创建
File f = new File("G:\\a"); 
f.mkdir();

//如果路径写成\\a.txt,会在盘符下创建新的文件夹
File f = new File("\\a"); 
f.mkdir();

//在g盘下创建文件夹a,a 下创建一个b文件夹
File f = new File("G:\\a\\b"); 
f.mkdirs();   //注意mkdirs(),创建多个文件夹
```

## new File

```java
File f = new File("a");//此时f是文件夹
File f = new File("parent","child"); //此时f是文件,parent文件夹下的文件

//注意:此时会在盘符根目录下创建文件夹 或文件 d
File f = new File("", "d");
f.createNewFile(); // f.mkdir()
```

## filter

\`\`\`java

String\[\] s = f1.list\(new FilenameFilter\(\) { /\*\*

* dir 需要被过滤的文件夹 name 需要别被过滤的文  件名 .此名是相对路径
* 如果返回true 则证明是符合条件的文件.会将改文件返回到数组中 \*/ @Override public boolean accept\(File dir, String name\) { File f = new File\(dir, name\); if \(f.isDirectory\(\)\) { return false; }

  if \(f.getName\(\).endsWith\("txt"\)\) { return true; }

  return false; } }\);

File\[\] fs = f1.listFiles\(new FileFilter\(\) { /\*\*

* pathname 表示要被过滤的文件,注意:不是文件名
* 返ture 证明是符合条件的文件 \*/ @Override public boolean accept\(File pathname\) {

  if \(pathname.length\(\) &gt; 1024  _1024_  20\) { return true; }

  return false; } }\);  
  \`\`\` Ref:[https://blog.csdn.net/fengltxx/article/details/51583977](https://blog.csdn.net/fengltxx/article/details/51583977)

