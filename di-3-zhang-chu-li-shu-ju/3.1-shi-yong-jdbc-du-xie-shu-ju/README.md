# 3.1 使用 JDBC 读写数据

几十年来，关系数据库和 SQL 一直是数据持久化的首选。尽管近年来出现了许多替代数据库类型，但关系数据库仍然是通用数据存储的首选，而且不太可能很快被取代。

在处理关系数据时，Java 开发人员有多个选择。两个最常见的选择是 JDBC 和 JPA。Spring 通过抽象支持这两种方式，这使得使用 JDBC 或 JPA 比不使用 Spring 更容易。在本节中，我们将重点讨论 Spring 是如何支持 JDBC 的，然后在第 3.2 节中讨论 Spring 对 JPA 的支持。

Spring JDBC 支持起源于 JdbcTemplate 类。JdbcTemplate 提供了一种方法，通过这种方法，开发人员可以对关系数据库执行 SQL 操作，与通常使用 JDBC 不同的是，这里不需要满足所有的条件和样板代码。

为了更好地理解 JdbcTemplate 的作用，我们首先来看一个示例，看看如何在没有 JdbcTemplate 的情况下用 Java 执行一个简单的查询。

{% code title="程序清单 3.1 不使用 JdbcTemplate 查询数据库" %}
```java
@Override
public Ingredient findOne(String id) {
    Connection connection = null;
    PreparedStatement statement = null;
    ResultSet resultSet = null;
    try {
        connection = dataSource.getConnection();
        statement = connection.prepareStatement(
            "select id, name, type from Ingredient");
        statement.setString(1, id);
        resultSet = statement.executeQuery();
        Ingredient ingredient = null;
        if(resultSet.next()) {
            ingredient = new Ingredient(
                resultSet.getString("id"),
                resultSet.getString("name"),
                Ingredient.Type.valueOf(resultSet.getString("type")));
        }
        return ingredient;
    } catch (SQLException e) {
        // ??? What should be done here ???
    } finally {
        if (resultSet != null) {
            try {
                resultSet.close();
            } catch (SQLException e) {
            }
        }
        if (statement != null) {
            try {
                statement.close();
            } catch (SQLException e) {
            }
        }
        if (connection != null) {
            try {
                connection.close();
            } catch (SQLException e) {
            }
        }
    }
    return null;
}
```
{% endcode %}

在程序清单 3.1 的某个地方，有几行代码用于查询数据库中的 ingredients。但是很难在 JDBC 的混乱代码中找到查询指针。它被创建连接、创建语句和通过关闭连接、语句和结果集来清理的代码所包围。

更糟糕的是，在创建连接或语句或执行查询时，可能会出现许多问题。这要求捕获一个 SQLException，这可能有助于（也可能无助于）找出问题出在哪里或如何解决问题。

SQLException 是一个被检查的异常，它需要在 catch 块中进行处理。但是最常见的问题，如未能创建到数据库的连接或输入错误的查询，不可能在 catch 块中得到解决，可能会重新向上抛出以求处理。相反，要是考虑使用 JdbcTemplate 的方法。

{% code title="程序清单 3.2 使用 JdbcTemplate 查询数据库" %}
```java
private JdbcTemplate jdbc;
​
@Override
public Ingredient findOne(String id) {
    return jdbc.queryForObject(
        "select id, name, type from Ingredient where id=?",
        this::mapRowToIngredient, id);
}
​
private Ingredient mapRowToIngredient(ResultSet rs, int rowNum)
    throws SQLException {
    return new Ingredient(
        rs.getString("id"),
        rs.getString("name"),
        Ingredient.Type.valueOf(rs.getString("type")));
}
```
{% endcode %}

程序清单 3.2 中的代码显然比程序清单 3.1 中的原始 JDBC 示例简单得多；没有创建任何语句或连接。而且，在方法完成之后，不会对那些对象进行任何清理。最后，这样做不会存在任何在 catch 块中不能处理的异常。剩下的代码只专注于执行查询（调用 JdbcTemplate 的 queryForObject\(\) 方法）并将结果映射到 Ingredient 对象（在 mapRowToIngredient\(\) 方法中）。

程序清单 3.2 中的代码是使用 JdbcTemplate 在 Taco Cloud 应用程序中持久化和读取数据所需要做的工作的一个片段。让我们采取下一步必要的步骤来为应用程序配备 JDBC 持久化。我们将首先对域对象进行一些调整。

