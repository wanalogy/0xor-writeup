

# web2

## 题目

> http://ctf.bugku.com/challenges#web2
>
> 听说聪明的人都能找到答案<http://120.24.86.145:8002/web2/> 


## 解答思路
- 查看页面源代码，获取隐藏信息

  

## 流程图
```mermaid
graph TD
A[收集信息:查看页面源代码] --> B{是否收集到?}
B --> |是| D[页面展示数据]
D --> E{是否符合flag格式? }
E --> |否|H[暂存入数据库]
E --> |是| F[由用户修改或者直接提交flag]
F --> G{flag是否正确}
G --> |否| H
G--> |是| I[存入数据库]
```




## 解题步骤

1. 查看源代码 ，查看隐藏信息

   ```javascript
   <!flag KEY{Web-2-bugKssNNikls9100}>
   ```

   


## 漏洞挖掘思路

- 无

## 自动化解析

- TODO

- 判断题目特征：`web` `php` `md5` 
- 设定题目难度等级`level`为simple
- 提交相应符合要求的字符串

