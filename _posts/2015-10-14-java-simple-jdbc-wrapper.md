---
layout: post
title:  "简单的 JDBC 封装"
subtitle: "保持心态"
tags: [Java, JDBC, 封装]
comments: true
---
``` java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class DBUtils {

    private DBUtils() {}

    private static final String CLASS_NAME = "com.mysql.jdbc.Driver";

    private static final String URL = "jdbc:mysql://localhost:3306/xxx";
    private static final String USER = "root";
    private static final String PASSWORD = "123456";

    public static Connection getConn() {
        Connection conn = null;
        try {
            Class.forName(CLASS_NAME);
            conn = DriverManager.getConnection(URL, USER, PASSWORD);
        } catch (ClassNotFoundException | SQLException e) {
            e.printStackTrace();
        }
        return conn;
    }

    public static Statement getStmt(Connection conn) {
        Statement stmt = null;
        try {
            stmt = conn.createStatement();
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return stmt;
    }

    public static ResultSet executeQuery(Statement stmt, String sql) {
        ResultSet rs = null;
        try {
            rs = stmt.executeQuery(sql);
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return rs;
    }

    public static void close(AutoCloseable obj) {
        if (obj != null) {
            try {
                obj.close();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```
