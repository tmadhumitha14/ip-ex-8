# ip-ex-8

import java.io.*;
import javax.servlet.*;
import javax.servlet.http.*;
import java.sql.*;

public class OrderServlet extends HttpServlet {
    public void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String url = "jdbc:mysql://localhost:3306/your_database"; // Update with your database URL
        String user = "your_username"; // Update with your username
        String password = "your_password"; // Update with your password
        
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            Connection con = DriverManager.getConnection(url, user, password);
            Statement stmt = con.createStatement();
            
            // Query to get total number of orders
            ResultSet rs1 = stmt.executeQuery("SELECT COUNT(*) AS totalOrders FROM orders");
            rs1.next();
            int totalOrders = rs1.getInt("totalOrders");
            
            // Query to get total revenue
            ResultSet rs2 = stmt.executeQuery("SELECT SUM(order_amount) AS totalRevenue FROM orders");
            rs2.next();
            double totalRevenue = rs2.getDouble("totalRevenue");
            
            // Calculate average order value
            double averageOrderValue = totalRevenue / totalOrders;
            
            // Set attributes to be displayed in JSP
            request.setAttribute("totalOrders", totalOrders);
            request.setAttribute("totalRevenue", totalRevenue);
            request.setAttribute("averageOrderValue", averageOrderValue);
            
            // Forward the request to index.jsp
            RequestDispatcher rd = request.getRequestDispatcher("index.jsp");
            rd.forward(request, response);
            
            con.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns="http://xmlns.jcp.org/xml/ns/javaee"
    xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
    id="WebApp_ID" version="4.0">
    <display-name>OrderApp</display-name>
    <servlet>
        <servlet-name>OrderServlet</servlet-name>
        <servlet-class>OrderServlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>OrderServlet</servlet-name>
        <url-pattern>/order</url-pattern>
    </servlet-mapping>
</web-app>
<!DOCTYPE html>
<html>
<head>
    <title>Order Information</title>
</head>
<body>
    <h1>Order Information</h1>
    <p>Total Number of Orders: ${totalOrders}</p>
    <p>Total Revenue: ${totalRevenue}</p>
    <p>Average Order Value: ${averageOrderValue}</p>
</body>
</html>


// program 1

import java.sql.*;

public class OrderDAO {
    private Connection connection;

    public OrderDAO() throws SQLException {
        String url = "jdbc:mysql://localhost:3306/your_database_name"; // Update with your database URL
        String user = "your_username"; // Update with your username
        String password = "your_password"; // Update with your password
        connection = DriverManager.getConnection(url, user, password);
    }

    public int getTotalOrders() throws SQLException {
        Statement statement = connection.createStatement();
        ResultSet resultSet = statement.executeQuery("SELECT COUNT(*) AS totalOrders FROM orders");
        resultSet.next();
        return resultSet.getInt("totalOrders");
    }

    public double getTotalRevenue() throws SQLException {
        Statement statement = connection.createStatement();
        ResultSet resultSet = statement.executeQuery("SELECT SUM(quantity * unit_price) AS totalRevenue FROM orders");
        resultSet.next();
        return resultSet.getDouble("totalRevenue");
    }

    public void close() throws SQLException {
        if (connection != null) {
            connection.close();
        }
    }
}


CREATE DATABASE IF NOT EXISTS your_database_name;

USE your_database_name;

CREATE TABLE IF NOT EXISTS orders (
    order_id INT PRIMARY KEY AUTO_INCREMENT,
    customer_name VARCHAR(255),
    product_name VARCHAR(255),
    quantity INT,
    unit_price DOUBLE
);
