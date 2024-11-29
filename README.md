src/
  └── com.jobportal/
      ├── dao/         // DAO classes
      ├── model/       // POJO (Plain Old Java Object) classes
      ├── util/        // Utility classes like DB connection
      └── Main.java    // Entry point for the application

CREATE DATABASE JobPortal;

USE JobPortal;

CREATE TABLE Users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL
);

CREATE TABLE Jobs (
    job_id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(100) NOT NULL,
    description TEXT,
    company_name VARCHAR(100),
    location VARCHAR(50),
    salary DECIMAL(10, 2)
);

package com.jobportal.util;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class DBConnection {
    private static final String URL = "jdbc:mysql://localhost:3306/JobPortal";
    private static final String USER = "root"; // Replace with your MySQL username
    private static final String PASSWORD = ""; // Replace with your MySQL password

    public static Connection getConnection() {
        try {
            return DriverManager.getConnection(URL, USER, PASSWORD);
        } catch (SQLException e) {
            System.err.println("Error connecting to the database: " + e.getMessage());
            throw new RuntimeException(e);
        }
    }
}


package com.jobportal.dao;

import com.jobportal.util.DBConnection;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class UserDAO {
    public boolean addUser(String name, String email, String password) {
        String sql = "INSERT INTO Users (name, email, password) VALUES (?, ?, ?)";
        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setString(1, name);
            stmt.setString(2, email);
            stmt.setString(3, password);

            return stmt.executeUpdate() > 0;
        } catch (SQLException e) {
            System.err.println("Error adding user: " + e.getMessage());
            return false;
        }
    }
}

package com.jobportal.dao;

import com.jobportal.util.DBConnection;
import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class JobDAO {
    public boolean addJob(String title, String description, String companyName, String location, double salary) {
        String sql = "INSERT INTO Jobs (title, description, company_name, location, salary) VALUES (?, ?, ?, ?, ?)";
        try (Connection conn = DBConnection.getConnection();
             PreparedStatement stmt = conn.prepareStatement(sql)) {

            stmt.setString(1, title);
            stmt.setString(2, description);
            stmt.setString(3, companyName);
            stmt.setString(4, location);
            stmt.setDouble(5, salary);

            return stmt.executeUpdate() > 0;
        } catch (SQLException e) {
            System.err.println("Error adding job: " + e.getMessage());
            return false;
        }
    }
}


package com.jobportal;

import com.jobportal.dao.UserDAO;
import com.jobportal.dao.JobDAO;

public class Main {
    public static void main(String[] args) {
        UserDAO userDAO = new UserDAO();
        JobDAO jobDAO = new JobDAO();

        // Add a user
        boolean userAdded = userDAO.addUser("John Doe", "john@example.com", "password123");
        System.out.println("User added: " + userAdded);

        // Add a job
        boolean jobAdded = jobDAO.addJob("Software Developer", "Develop software solutions.", "TechCorp", "New York", 80000.00);
        System.out.println("Job added: " + jobAdded);
    }
}
