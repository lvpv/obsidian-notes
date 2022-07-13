# 第一部分：自定义持久层框架
## 1.1 分析JDBC操作问题
```java
public class JdbcTest {  
    public static void main(String[] args) {  
        Connection connection = null;  
        PreparedStatement preparedStatement = null;  
        ResultSet resultSet = null;  
        try {  
            // 加载数据库驱动  
            Class.forName("com.mysql.cj.jdbc.Driver");  
            // 通过驱动管理类获取数据库链接  
            connection = DriverManager  
                    .getConnection(  
                            "jdbc:mysql://localhost:3306/mybatis-study?characterEncoding=utf-8",  
                            "root", "root");  
            // 定义sql语句，?表示占位符  
            String sql = "select * from `user` where username = ?";  
            // 获取预处理对象  
            preparedStatement = connection.prepareStatement(sql);  
            // 设置参数，第⼀个参数为sql语句中参数的序号(从1开始)，第⼆个参数为设置的参数值  
            preparedStatement.setString(1, "tom");  
            // 向数据库发出sql执⾏查询，查询出结果集  
            resultSet = preparedStatement.executeQuery();  
            // 遍历查询结果集  
            while (resultSet.next()) {  
                int id = resultSet.getInt("id");  
                String username = resultSet.getString("username");  
                System.out.println("id = " + id);  
                System.out.println("username = " + username);  
            }  
        } catch (ClassNotFoundException | SQLException e) {  
            throw new RuntimeException(e);  
        } finally {  
            // 释放资源  
            if (resultSet != null) {  
                try {  
                    resultSet.close();  
                } catch (SQLException e) {  
                    throw new RuntimeException(e);  
                }  
            }  
            if (preparedStatement != null) {  
                try {  
                    preparedStatement.close();  
                } catch (SQLException e) {  
                    throw new RuntimeException(e);  
                }  
            }  
            if (connection != null) {  
                try {  
                    connection.close();  
                } catch (SQLException e) {  
                    throw new RuntimeException(e);  
                }  
            }  
        }  
    }  
}
```
### JDBC问题总结

原始jdbc开发存在的问题如下： 
	1、 数据库连接创建、释放频繁造成系统资源浪费，从⽽影响系统性能。 
	2、 Sql语句在代码中硬编码，造成代码不易维护，实际应⽤中sql变化的可能较⼤，sql变动需要改变 java代码。 
	3、 使⽤preparedStatement向占有位符号传参数存在硬编码，因为sql语句的where条件不⼀定，可能 多也可能少，修改sql还要修改代码，系统不易维护。 
	4、 对结果集解析存在硬编码(查询列名)，sql变化导致解析代码变化，系统不易维护，如果能将数据 库 记录封装成pojo对象解析⽐较⽅便