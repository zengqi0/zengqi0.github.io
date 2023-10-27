---
layout: DVWA之SQL Injection Snort规则提取
title: 
date: 2023-08-01 09:43:02
tags:
---





# low

## 利用

![](DVWA之SQL Injection Snort规则提取\1.png)

判断是否存在注入，注入是字符型还是数字型

输入1，查询成功

输入1’and ‘1’ =’2，查询失败，返回结果为空：

输入1’or ‘1234 ’=’1234，查询成功：

返回了多个结果，说明存在字符型注入

2.猜解SQL查询语句中的字段数

输入1′ or 1=1 order by 1 #，查询成功：

## 源码

```php
<?php

if( isset( $_REQUEST[ 'Submit' ] ) ) {
    // Get input
    $id = $_REQUEST[ 'id' ];

    switch ($_DVWA['SQLI_DB']) {
        case MYSQL:
            // Check database
            $query  = "SELECT first_name, last_name FROM users WHERE user_id = '$id';";
            $result = mysqli_query($GLOBALS["___mysqli_ston"],  $query ) or die( '<pre>' . ((is_object($GLOBALS["___mysqli_ston"])) ? mysqli_error($GLOBALS["___mysqli_ston"]) : (($___mysqli_res = mysqli_connect_error()) ? $___mysqli_res : false)) . '</pre>' );

            // Get results
            while( $row = mysqli_fetch_assoc( $result ) ) {
                // Get values
                $first = $row["first_name"];
                $last  = $row["last_name"];

                // Feedback for end user
                echo "<pre>ID: {$id}<br />First name: {$first}<br />Surname: {$last}</pre>";
            }

            mysqli_close($GLOBALS["___mysqli_ston"]);
            break;
        case SQLITE:
            global $sqlite_db_connection;

            #$sqlite_db_connection = new SQLite3($_DVWA['SQLITE_DB']);
            #$sqlite_db_connection->enableExceptions(true);

            $query  = "SELECT first_name, last_name FROM users WHERE user_id = '$id';";
            #print $query;
            try {
                $results = $sqlite_db_connection->query($query);
            } catch (Exception $e) {
                echo 'Caught exception: ' . $e->getMessage();
                exit();
            }

            if ($results) {
                while ($row = $results->fetchArray()) {
                    // Get values
                    $first = $row["first_name"];
                    $last  = $row["last_name"];

                    // Feedback for end user
                    echo "<pre>ID: {$id}<br />First name: {$first}<br />Surname: {$last}</pre>";
                }
            } else {
                echo "Error in fetch ".$sqlite_db->lastErrorMsg();
            }
            break;
    } 
}

?>
```

不用担心，让我为您解释一下 "admin' or '1'='1" 这个字符串的含义。

这个字符串是一个典型的 SQL 注入攻击的示例。在 SQL 注入攻击中，攻击者试图通过在应用程序的输入字段中注入恶意的 SQL 代码来操纵应用程序的数据库查询。目标是绕过身份验证机制、提取敏感数据、修改数据库记录或执行未经授权的操作。

让我们逐步解析这个 SQL 注入攻击的字符串 "admin' or '1'='1"：

1. `admin'`: 攻击者输入的用户名或值为 "admin'"，在这里添加了一个单引号 `'`。单引号是用来终止 SQL 查询中的值，并创建一个不完整的 SQL 语句。
2. `or`: 这是 SQL 的逻辑运算符 "OR"。攻击者使用它来向 SQL 查询添加额外的条件。
3. `'1'='1'`: 这是攻击者添加的条件，目的是使整个 SQL 语句评估为 "true"（真）。在 SQL 中，将一个值与它自己进行比较将始终返回真。所以，`'1'='1'` 将会是真。

通过将这个字符串注入到一个易受攻击的 web 应用程序的输入字段中，攻击者可以试图操纵应用程序的 SQL 查询，返回比预期更多的数据，绕过登录检查，或获得未经授权的访问权限。

SQL 注入攻击是严重的安全风险，如果成功执行，可能会造成严重的后果。对于 web 开发人员来说，实施正确的输入验证并使用参数化查询（或预处理语句）来防止应用程序中的 SQL 注入漏洞非常重要。此外，进行 web 应用程序安全测试和定期进行安全审计也是必要的，以发现并修复潜在的漏洞。



# middle

