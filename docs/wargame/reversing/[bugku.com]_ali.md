## Timer(阿里CTF)

## 0x1 题目

> flag格式 flag{} 

## 0x2 解题步骤

参考链接：https://blog.csdn.net/xiangshangbashaonian/article/details/80301054

### 0x1 jeb分析文件

>  apk安装之后出现一个读秒的，20万秒，大于3600，所以大于一个小时，所以太慢了，不等了。 

jeb反编译，MainActivity代码 

```java
package net.bluelotus.tomorrow.easyandroid;
 
import android.os.Bundle;
import android.os.Handler;
import android.support.v7.app.AppCompatActivity;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
 
public class MainActivity extends AppCompatActivity {
    int beg;
    int k;
    int now;
    long t;
 
    static {
        System.loadLibrary("lhm");
    }
 
    public MainActivity() {
        super();
        this.beg = (((int)(System.currentTimeMillis() / 1000))) + 200000;
        this.k = 0;
        this.t = 0;
    }
 
    public static boolean is2(int n) {
        boolean v1 = true;
        if(n > 3) {
            if(n % 2 != 0 && n % 3 != 0) {
                int v0 = 5;
                while(true) {
                    if(v0 * v0 <= n) {
                        if(n % v0 != 0 && n % (v0 + 2) != 0) {
                            v0 += 6;
                            continue;
                        }
 
                        return false;
                    }
                    else {
                        return v1;
                    }
                }
 
                return false;
            }
 
            v1 = false;
        }
        else if(n <= 1) {
            v1 = false;
        }
 
        return v1;
    }
 
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        this.setContentView(2130968600);
        View v2 = this.findViewById(2131492944);
        View v3 = this.findViewById(2131492945);
        Handler v0 = new Handler();
        v0.postDelayed(new Runnable() {
            public void run() {
                MainActivity.this.t = System.currentTimeMillis();
                MainActivity.this.now = ((int)(MainActivity.this.t / 1000));
                MainActivity.this.t = 1500 - MainActivity.this.t % 1000;
                this.val$tv2.setText("AliCTF");
                if(MainActivity.this.beg - MainActivity.this.now <= 0) {
                    this.val$tv1.setText("The flag is:");
                    this.val$tv2.setText("alictf{" + MainActivity.this.stringFromJNI2(MainActivity.this
                            .k) + "}");
                }
 
                if(MainActivity.is2(MainActivity.this.beg - MainActivity.this.now)) {
                    MainActivity.this.k += 100;
                }
                else {
                    --MainActivity.this.k;
                }
 
                this.val$tv1.setText("Time Remaining(s):" + (MainActivity.this.beg - MainActivity.this
                        .now));
                this.val$handler.postDelayed(((Runnable)this), MainActivity.this.t);
            }
        }, 0);
    }
 
    public boolean onCreateOptionsMenu(Menu menu) {
        this.getMenuInflater().inflate(2131558400, menu);
        return 1;
    }
 
    public boolean onOptionsItemSelected(MenuItem item) {
        boolean v1 = item.getItemId() == 2131492959 ? true : super.onOptionsItemSelected(item);
        return v1;
    }
 
    public native String stringFromJNI2(int arg1) {
    }
}
```

- 关键点

  ```java
  if(MainActivity.this.beg - MainActivity.this.now <= 0) {
                      this.val$tv1.setText("The flag is:");
                      this.val$tv2.setText("alictf{" + MainActivity.this.stringFromJNI2(MainActivity.this
                              .k) + "}");
                  }
  ```

- 找到 beg 和 now 的点： 

  ```java
  public MainActivity() {
          super();
          this.beg = (((int)(System.currentTimeMillis() / 1000))) + 200000;//打开activity时候的时间（除以1000，所以是秒为单位）再加上200000，即200000+当前时间对应的秒
          this.k = 0;
          this.t = 0;
      }
  
  ```

  以及`MainActivity.this.now = ((int)(MainActivity.this.t / 1000));`即判断的时候的秒  那么 beg - now 就是 200000 + 开始的时间 - 判断的时间，也就是 200000 - 已经过去的时间，所以就是如果过去了 200000 秒，就可以出现 flag 了。  呈现 flag 是通过 native 层的`this.val$tv2.setText("alictf{" + MainActivity.this.stringFromJNI2(MainActivity.this .k) + "}");`也就是 stringFromJNI2 来呈现的，我们不必逆向 so，这个参数是一个 k，如果真的去等 200000 秒（虽然不可能），那么这里的 k 应该是多少呢？ 

- 跟 k 相关的两个关键点如下： 

  ```java
  public static boolean is2(int n) {
          boolean v1 = true;
          if(n > 3) {
              if(n % 2 != 0 && n % 3 != 0) {
                  int v0 = 5;
                  while(true) {
                      if(v0 * v0 <= n) {
                          if(n % v0 != 0 && n % (v0 + 2) != 0) {
                              v0 += 6;
                              continue;
                          }
   
                          return false;
                      }
                      else {
                          return v1;
                      }
                  }
   
                  return false;
              }
   
              v1 = false;
          }
          else if(n <= 1) {
              v1 = false;
          }
   
          return v1;
      }
   
   
   
   if(MainActivity.is2(MainActivity.this.beg - MainActivity.this.now)) {
                      MainActivity.this.k += 100;
                  }
                  else {
                      --MainActivity.this.k;
                  }
  
  ```

- 总结思路：通过 beg - now 代入 is2 函数对 k 进行操作，200000，需要一秒一秒的操作，而我们，直接通过写代码模拟出 200000 的结果，找到 k，然后改动 k 值，直接调用，就可以得到 flag 了。

### 0x2 计算k值

is2 是用 java 写的，懒得看逻辑，直接复制到 java 里边： 

```java
package test;
 
public class Main {
 
    public static boolean is2(int n) {
        boolean v1 = true;
        if(n > 3) {
            if(n % 2 != 0 && n % 3 != 0) {
                int v0 = 5;
                while(true) {
                    if(v0 * v0 <= n) {
                        if(n % v0 != 0 && n % (v0 + 2) != 0) {
                            v0 += 6;
                            continue;
                        }
 
                        return false;
                    }
                    else {
                        return v1;
                    }
                }
            }
 
            v1 = false;
        }
        else if(n <= 1) {
            v1 = false;
        }
 
        return v1;
    }
 
    public static void main(String args[]) {
        int time = 200000;
        int k = 0;
        while (time > 0) {
            if (is2(time)) {
                k += 100;
            }
            else {
                k--;
            }
            time--;
        }
        System.out.println(k);
    }
 
}

```
输出：运行得到 k 值:1616384 

### 0x3 修改并打包运行原apk

1. 将输出 flag 的条件反过来，即 MainActivity$1.smali 中的`if-gtz v0, :cond_0`这句话（后面是输出 The Flag Is 那里的跳转）改为`if-ltz v0, :cond_0`
2. 将 k 值改为常量，即上文提到的 smali 文件中的`iget v3, v3 ....(省略);->k:I`之后**另起一行**添加`const v3, 1616384`
3. 重新打包，签名，安装，得到 flag 为 alictf{Y0vAr3TimerMa3te7} 

注意：

1. android killer1.3版本中的apktools版本编译会发生错误，下载最新的apktool之后，在配置中添加并应用，成功

2. [手动运行apktools实现反编译，修改，打包，运行的过程](https://blog.csdn.net/dreamer2020/article/details/52761606)

3. [java命令行使用](https://blog.csdn.net/wgw335363240/article/details/6332238)，要注意test包名需要手动创建，运行指令为：`java test.Main`

4. vs code下运行java单文件，需要在同级目录下创建`.classpath`文件

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <classpath>
       <classpathentry kind="src" path="src"/>
       <classpathentry kind="con" path="org.eclipse.jdt.launching.JRE_CONTAINER/org.eclipse.jdt.internal.debug.ui.launcher.StandardVMType/JavaSE-1.8/"/>
       <classpathentry kind="con" path="org.eclipse.buildship.core.gradleclasspathcontainer"/>
       <classpathentry kind="output" path="bin"/>
   </classpath>
   ```

   



