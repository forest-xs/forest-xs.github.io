# MySQL 函数大全（基于 MySQL 8.0+）  
*适用于开发查阅 · 建议收藏 · 持续更新建议参考官方文档*

> **⚠️ 重要提示**  
> - 本文档基于 **MySQL 8.0+**（窗口函数需 8.0+，JSON 函数需 5.7+）  
> - 函数数量庞大，此处精选**高频实用函数**，完整列表请查阅 [MySQL 官方文档](https://dev.mysql.com/doc/refman/8.0/en/functions.html)  
> - 实际使用前请结合业务验证，注意版本兼容性与性能影响  

---

## 📌 目录
1. [字符串函数](#字符串函数)  
2. [数值函数](#数值函数)  
3. [日期与时间函数](#日期与时间函数)  
4. [聚合函数（含窗口用法）](#聚合函数)  
5. [窗口函数专项](#窗口函数专项)  
6. [控制流函数](#控制流函数)  
7. [JSON 函数](#json-函数)  
8. [加密与哈希函数](#加密与哈希函数)  
9. [类型转换与系统函数](#类型转换与系统函数)  
10. [实用技巧与注意事项](#实用技巧与注意事项)  

---

## 字符串函数
| 函数                                         | 说明                     | 示例                                          |
| -------------------------------------------- | ------------------------ | --------------------------------------------- |
| `CONCAT(s1,s2,...)`                          | 拼接字符串               | `CONCAT('Hello',' ','World') → 'Hello World'` |
| `CONCAT_WS(sep,s1,s2)`                       | 用分隔符拼接             | `CONCAT_WS(',','A','B') → 'A,B'`              |
| `SUBSTRING(str,pos,len)`                     | 截取子串                 | `SUBSTRING('MySQL',2,3) → 'ySQ'`              |
| `LEFT(str,len) / RIGHT(str,len)`             | 左/右截取                | `LEFT('abc',2) → 'ab'`                        |
| `LENGTH(str)`                                | 字节长度                 | `LENGTH('你好') → 6` (UTF8)                   |
| `CHAR_LENGTH(str)`                           | 字符数                   | `CHAR_LENGTH('你好') → 2`                     |
| `UPPER(str) / LOWER(str)`                    | 大小写转换               | `UPPER('abc') → 'ABC'`                        |
| `TRIM([BOTH/LEADING/TRAILING] rem FROM str)` | 去除空格/指定字符        | `TRIM('  abc  ') → 'abc'`                     |
| `REPLACE(str,old,new)`                       | 替换子串                 | `REPLACE('a*b','*','-') → 'a-b'`              |
| `INSTR(str,substr)`                          | 返回子串位置（从1开始）  | `INSTR('MySQL','SQL') → 3`                    |
| `LOCATE(substr,str[,pos])`                   | 同 INSTR，可指定起始位置 | `LOCATE('o','Hello',3) → 5`                   |
| `LPAD(str,len,pad) / RPAD`                   | 左/右填充                | `LPAD('5',3,'0') → '005'`                     |
| `REGEXP_REPLACE(str,pat,rep)`                | 正则替换（8.0+）         | `REGEXP_REPLACE('a1b2','[0-9]','*') → 'a*b*'` |

---

## 数值函数
| 函数                      | 说明                  | 示例                              |
| ------------------------- | --------------------- | --------------------------------- |
| `ABS(x)`                  | 绝对值                | `ABS(-5) → 5`                     |
| `CEIL(x) / FLOOR(x)`      | 向上/下取整           | `CEIL(3.2) → 4`, `FLOOR(3.8) → 3` |
| `ROUND(x,d)`              | 四舍五入（d为小数位） | `ROUND(3.1415,2) → 3.14`          |
| `TRUNCATE(x,d)`           | 截断（不四舍五入）    | `TRUNCATE(3.1415,2) → 3.14`       |
| `MOD(x,y)`                | 取余                  | `MOD(10,3) → 1`                   |
| `POWER(x,y)`              | 幂运算                | `POWER(2,3) → 8`                  |
| `SQRT(x)`                 | 平方根                | `SQRT(16) → 4`                    |
| `RAND([seed])`            | 随机数 [0,1)          | `RAND() → 0.73...`                |
| `SIGN(x)`                 | 符号（-1/0/1）        | `SIGN(-5) → -1`                   |
| `GREATEST(val1,val2,...)` | 返回最大值            | `GREATEST(1,5,3) → 5`             |
| `LEAST(val1,val2,...)`    | 返回最小值            | `LEAST(1,5,3) → 1`                |

---

## 日期与时间函数
| 函数                                                | 说明                            | 示例                                           |
| --------------------------------------------------- | ------------------------------- | ---------------------------------------------- |
| `NOW() / SYSDATE()`                                 | 当前日期时间（SYSDATE为执行时） | `2024-06-15 14:30:00`                          |
| `CURDATE() / CURTIME()`                             | 当前日期 / 时间                 | `CURDATE() → '2024-06-15'`                     |
| `DATE_ADD(date, INTERVAL expr unit)`                | 日期加法                        | `DATE_ADD(NOW(), INTERVAL 1 DAY)`              |
| `DATE_SUB(date, INTERVAL expr unit)`                | 日期减法                        | `DATE_SUB(NOW(), INTERVAL 2 HOUR)`             |
| `DATEDIFF(date1,date2)`                             | 日期差（天）                    | `DATEDIFF('2024-06-20','2024-06-15') → 5`      |
| `TIMESTAMPDIFF(unit,dt1,dt2)`                       | 指定单位差值                    | `TIMESTAMPDIFF(HOUR, dt1, dt2)`                |
| `DATE_FORMAT(date,format)`                          | 格式化输出                      | `DATE_FORMAT(NOW(),'%Y-%m-%d') → '2024-06-15'` |
| `STR_TO_DATE(str,format)`                           | 字符串转日期                    | `STR_TO_DATE('20240615','%Y%m%d')`             |
| `YEAR(date) / MONTH / DAY / HOUR / MINUTE / SECOND` | 提取部件                        | `MONTH(NOW()) → 6`                             |
| `UNIX_TIMESTAMP([date])`                            | 转Unix时间戳                    | `UNIX_TIMESTAMP() → 1718430600`                |
| `FROM_UNIXTIME(unix_ts[,format])`                   | 时间戳转日期                    | `FROM_UNIXTIME(1718430600)`                    |

---

## 聚合函数
> *既可用于 GROUP BY 聚合，也可作为窗口函数（见下节）*
| 函数                                             | 说明                | 示例                             |
| ------------------------------------------------ | ------------------- | -------------------------------- |
| `COUNT(*) / COUNT(col)`                          | 行数统计（*含NULL） | `COUNT(*)`                       |
| `SUM(col)`                                       | 求和                | `SUM(salary)`                    |
| `AVG(col)`                                       | 平均值              | `AVG(score)`                     |
| `MAX(col) / MIN(col)`                            | 最大/最小值         | `MAX(price)`                     |
| `GROUP_CONCAT(col [ORDER BY ... SEPARATOR ','])` | 分组拼接字符串      | `GROUP_CONCAT(name ORDER BY id)` |
| `BIT_OR / BIT_AND / BIT_XOR`                     | 位运算聚合          | `BIT_OR(flags)`                  |

---

## 窗口函数专项（MySQL 8.0+ 核心特性）
### 🌟 基础语法
```sql
function_name(expression) OVER (
  [PARTITION BY partition_expr, ...]
  [ORDER BY sort_expr [ASC|DESC], ...]
  [frame_clause]
)
```
- **PARTITION BY**：分组（类似 GROUP BY，但保留明细行）  
- **ORDER BY**：窗口内排序（对排名/偏移函数至关重要）  
- **frame_clause**：窗口范围（ROWS / RANGE）  
  - `ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`（默认累加）  
  - `ROWS BETWEEN 1 PRECEDING AND 1 FOLLOWING`（滑动窗口）  

### 🔑 常用窗口函数分类
#### 1️⃣ 排名函数
| 函数           | 说明                   | 示例场景                 |
| -------------- | ---------------------- | ------------------------ |
| `ROW_NUMBER()` | 连续唯一排名           | 分页、去重               |
| `RANK()`       | 跳跃排名（并列占位）   | 成绩排名（1,2,2,4）      |
| `DENSE_RANK()` | 密集排名（并列不占位） | 成绩排名（1,2,2,3）      |
| `NTILE(n)`     | 将分区分为n组          | 百分位分组（NTILE(100)） |

#### 2️⃣ 偏移分析函数
| 函数                         | 说明                    | 示例                                                         |
| ---------------------------- | ----------------------- | ------------------------------------------------------------ |
| `LEAD(col, offset, default)` | 向后取第offset行        | `LEAD(salary,1,0) OVER (ORDER BY hire_date)`                 |
| `LAG(col, offset, default)`  | 向前取第offset行        | `LAG(price) OVER (PARTITION BY product_id ORDER BY date)`    |
| `FIRST_VALUE(col)`           | 窗口首行值              | `FIRST_VALUE(sales) OVER (ORDER BY month)`                   |
| `LAST_VALUE(col)`            | 窗口末行值（注意frame） | `LAST_VALUE(amount) OVER (ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING)` |
| `NTH_VALUE(col, n)`          | 窗口第n行值             | `NTH_VALUE(score, 2) OVER (ORDER BY id)`                     |

#### 3️⃣ 聚合类窗口函数
```sql
SUM(sales) OVER (PARTITION BY dept ORDER BY month 
                 ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total
AVG(score) OVER (ORDER BY date ROWS BETWEEN 2 PRECEDING AND CURRENT ROW) AS moving_avg
COUNT(*) OVER (PARTITION BY category) AS category_total
```

#### 💡 窗口函数实战示例
```sql
-- 每部门内按薪资排名 + 累计薪资
SELECT 
  name, dept, salary,
  ROW_NUMBER() OVER (PARTITION BY dept ORDER BY salary DESC) AS dept_rank,
  SUM(salary) OVER (PARTITION BY dept ORDER BY hire_date) AS dept_running_sum,
  LAG(salary) OVER (PARTITION BY dept ORDER BY hire_date) AS prev_salary
FROM employees;

-- 计算同比（与上月销售额对比）
SELECT 
  month, sales,
  LAG(sales) OVER (ORDER BY month) AS last_month_sales,
  ROUND((sales - LAG(sales) OVER (ORDER BY month)) / LAG(sales) OVER (ORDER BY month) * 100, 2) AS growth_pct
FROM sales_data;
```

---

## 控制流函数
| 函数                                  | 说明             | 示例                                             |
| ------------------------------------- | ---------------- | ------------------------------------------------ |
| `IF(expr,true_val,false_val)`         | 简单条件判断     | `IF(score>=60,'Pass','Fail')`                    |
| `IFNULL(expr1,expr2)`                 | NULL替换         | `IFNULL(phone,'N/A')`                            |
| `NULLIF(expr1,expr2)`                 | 相等返回NULL     | `NULLIF(a,b)`（a=b时返回NULL）                   |
| `CASE WHEN ... THEN ... ELSE ... END` | 多条件分支       | `CASE WHEN age<18 THEN 'Minor' ELSE 'Adult' END` |
| `COALESCE(val1,val2,...)`             | 返回首个非NULL值 | `COALESCE(email1,email2,'default@test.com')`     |

---

## JSON 函数（MySQL 5.7+）
| 函数                             | 说明                   | 示例                                        |
| -------------------------------- | ---------------------- | ------------------------------------------- |
| `JSON_EXTRACT(json,path)` / `->` | 提取JSON值             | `info->'$.name'`                            |
| `JSON_UNQUOTE(json_val)` / `->>` | 提取并去引号           | `info->>'$.city'`                           |
| `JSON_SET(json,path,val,...)`    | 设置/添加值            | `JSON_SET(info,'$.age',30)`                 |
| `JSON_INSERT`                    | 仅插入（存在不覆盖）   | `JSON_INSERT(info,'$.tag','new')`           |
| `JSON_REPLACE`                   | 仅替换（不存在不添加） | `JSON_REPLACE(info,'$.name','Tom')`         |
| `JSON_REMOVE(json,path,...)`     | 删除节点               | `JSON_REMOVE(info,'$.temp')`                |
| `JSON_CONTAINS(json,val[,path])` | 判断是否包含           | `JSON_CONTAINS(info, '"admin"', '$.roles')` |
| `JSON_KEYS(json[,path])`         | 返回键名数组           | `JSON_KEYS(info)`                           |

---

## 加密与哈希函数
| 函数                              | 说明                           | 示例                                  |
| --------------------------------- | ------------------------------ | ------------------------------------- |
| `MD5(str)`                        | MD5哈希（128位）               | `MD5('pwd')`                          |
| `SHA1(str) / SHA2(str, hash_len)` | SHA1 / SHA2（224,256,384,512） | `SHA2('data',256)`                    |
| `PASSWORD(str)`                   | **已弃用**（仅用于旧版认证）   | ❌ 避免使用                            |
| `AES_ENCRYPT(str,key)`            | AES加密                        | `HEX(AES_ENCRYPT('secret','key'))`    |
| `AES_DECRYPT(crypt_str,key)`      | AES解密                        | `AES_DECRYPT(UNHEX(crypt_hex),'key')` |
| `RANDOM_BYTES(len)`               | 生成随机字节（8.0+）           | `RANDOM_BYTES(16)`                    |

---

## 类型转换与系统函数
| 类别         | 函数                             | 说明                                           |
| ------------ | -------------------------------- | ---------------------------------------------- |
| **类型转换** | `CAST(expr AS type)`             | `CAST('123' AS SIGNED)`, `CAST(NOW() AS DATE)` |
|              | `CONVERT(expr, type)`            | 同 CAST，支持字符集转换                        |
| **系统信息** | `DATABASE()`                     | 当前数据库名                                   |
|              | `USER() / CURRENT_USER()`        | 连接用户 / 认证用户                            |
|              | `VERSION()`                      | MySQL版本                                      |
|              | `LAST_INSERT_ID()`               | 最后插入的自增ID                               |
|              | `ROW_COUNT()`                    | 上一条DML影响行数                              |
| **条件判断** | `ISNULL(expr)`                   | 是否为NULL                                     |
|              | `ISNULL(expr)`                   | 是否为NULL                                     |
|              | `INET_ATON(ip) / INET_NTOA(num)` | IP与整数互转                                   |

---

## 实用技巧与注意事项 ✅
1. **窗口函数性能**  
   - 避免在大数据量无索引列上使用 `ORDER BY`  
   - 合理使用 `PARTITION BY` 减小窗口范围  
   - `LAST_VALUE` 默认窗口为 `RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`，需显式指定完整窗口获取真正“最后值”

2. **NULL 处理**  
   - 聚合函数（SUM/AVG等）自动忽略 NULL  
   - 字符串拼接中 `CONCAT('a', NULL) → NULL`，建议用 `CONCAT_WS` 或 `IFNULL`

3. **时区注意**  
   - `NOW()` 返回会话时区时间，`UTC_TIMESTAMP()` 返回 UTC  
   - 用 `CONVERT_TZ(dt, from_tz, to_tz)` 转换时区

4. **JSON 性能**  
   - 频繁查询的JSON字段建议生成虚拟列+索引  
   - 避免在WHERE中直接使用 `JSON_EXTRACT`（无法走索引）

5. **安全提醒**  
   - 避免在应用层拼接SQL（防注入）  
   - 密码存储用 `SHA2` + 盐值，**切勿明文或仅用MD5**  
   - 敏感数据加密建议应用层处理（如AES）

6. **文档维护建议**  
   - 保存时标注MySQL版本（如：本文档基于 8.0.36）  
   - 添加个人常用示例片段（如：部门累计销售额模板）  
   - 定期同步官方文档更新（关注 [MySQL Release Notes](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/)）

---

📥 **保存建议**  
- 保存为 `MySQL_Functions_CheatSheet.md`  
- 打印核心章节（窗口函数/日期函数）贴于工位  
- 结合 [MySQL 8.0 Function Reference](https://dev.mysql.com/doc/refman/8.0/en/functions.html) 在线查阅  

✨ *善用函数提升SQL效率，但复杂逻辑建议下沉至应用层。保持代码可读性与可维护性！*