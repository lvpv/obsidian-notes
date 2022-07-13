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
            Class.forName("com.mysql.jdbc.Driver");  
            // 通过驱动管理类获取数据库链接  
            connection = DriverManager  
                    .getConnection(  
                            "jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf-8",  
                            "root","root");  
            // 定义sql语句，?表示占位符  
            String sql = "select * from `user` where username = ?";  
            // 获取预处理对象  
            preparedStatement = connection.prepareStatement(sql);  
            // 设置参数，第⼀个参数为sql语句中参数的序号(从1开始)，第⼆个参数为设置的参数值  
            preparedStatement.setString(1,"tom");  
            // 向数据库发出sql执⾏查询，查询出结果集  
            resultSet = preparedStatement.executeQuery();  
            // 遍历查询结果集  
            while (resultSet.next()){  
                int id = resultSet.getInt("id");  
                String username = resultSet.getString("username");  
                System.out.println("id = " + id);  
                System.out.println("username = " + username);  
            }  
        } catch (ClassNotFoundException | SQLException e) {  
            throw new RuntimeException(e);  
        }finally {  
            // 释放资源  
            if (resultSet != null){  
                try {  
                    resultSet.close();  
                } catch (SQLException e) {  
                    throw new RuntimeException(e);  
                }  
            }  
            if (preparedStatement != null){  
                try {  
                    preparedStatement.close();  
                } catch (SQLException e) {  
                    throw new RuntimeException(e);  
                }  
            }  
            if (connection != null){  
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
