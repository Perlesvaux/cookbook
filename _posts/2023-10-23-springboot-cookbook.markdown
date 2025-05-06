---
layout: post
title:  "Springboot (Java)"
date:   2023-10-23 14:36:15 -0600
categories: java
tags: java maven springboot mvc 
---
* Table of Contents
{:toc}

## Install Java and Maven:

```bash
sudo apt install default-jdk    #Check version with: java -version
sudo apt install maven          #Check version with: mvn --version
```

## 1- TAKE CARE OF THE DATABASE FIRST!:

An empty database & an user with enough privileges (SELECT INSERT UPDATE DELETE) will be enough  

```bash
sudo apt install mysql-server   #Installing MySQL
sudo mysql_secure_installation  #Wizard to improve security.
systemctl status mysql          #If inactive: systemctl start mysql. Runs at localhost:3306
sudo mysql                      #Enters MySQL CLI. Check from which port it's running: SHOW VARIABLES LIKE 'port';
```

```bash
#same as:  CREATE SCHEMA schemaname; To view all databases: SHOW DATABASES;
CREATE DATABASE databasename;   
#to view all users: SELECT USER, HOST from mysql.user;
CREATE USER 'username'@'localhost' IDENTIFIED BY 'enterPasswordHere';      
#view all grants: SHOW GRANTS FOR 'username'@'localhost'; | note:  *.* == allDatabases.allTables
GRANT INSERT, SELECT, UPDATE, DELETE ON databasename.* to 'username'@'localhost';  
```

New user can now access MySQL like this:

```bash
mysql -u username -p   #A password-prompt will ensue. For now, 'databasename' should be empty. Check with: USE databasename; SHOW TABLES;
```
## 2 - Create a new maven project:

Use the [Springboot Initializr](https://start.spring.io/). Try [this one](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.7.14&packaging=jar&jvmVersion=11&groupId=school&artifactId=roster&name=roster&description=Demo%20project%20for%20Spring%20Boot&packageName=school.roster&dependencies=web,data-jpa,thymeleaf,mysql) out!    
(i.e. Language: Java|Project: Maven|Spring Boot: 2.7.13|Packaging: Jar|Java: 11)
Working with current setup: Java version: 11.0.19 | Apache Maven 3.6.3

Include these dependencies:

```bash
Spring Web        #Uses Apache Tomcat as the default embedded container.
MySQL Driver      #MySQL JDBC driver (Appears in 'pom.xml' as: 'mysql-connector-j').
Spring Data JPA   #Automates creation of tables and columns.
Thymeleaf         #Server-side Java template engine

```

Upon un-Zipping the file, a "pom.xml" will be found at the "root of the project".

## 3- Connecting the database to the backend:

From the "root of the project", locate this file:

```bash
src/main/resources/application.properties
```

Add these lines to that configuration file:

```bash
#Or: spring.datasource.url=jdbc:mysql://localhost:3306/databasename?useSSL=false&serverTimezone=UTC
spring.datasource.url=jdbc:mysql://localhost:3306/databasename  #replace 'databasename'! 
spring.datasource.username=userNameHere                         #replace 'userNameHere'!
spring.datasource.password=enterPasswordHere                    #replace with user's password!
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver    #Keep the same
spring.jpa.hibernate.ddl-auto=update                            #Keep the same
```


## 4- If everything is OK, it should compile...:


```bash
mvn clean package               #Or: mvn clean test | mvn clean install
java -jar target/PROJECT.jar    #Or, in cases where you have a 'main' method: java -cp target/PROJECT.jar com.project.Main
```

It should run on localhost:8080

now let's work on the REST-API itself. it's made up of a CONTROLLER that interacts with a database through a REPOSITORY of MODELs.
To put it simple:  "API's repository" = "database table"; "API model" = "database entry/row".
The end user accesses the back-end through the "API's controller", which is a series of mappings of: URL end-points + functions.
To code the following classes, use this directory:


```bash
cd src/main/java/PROJECT/
```


## 5- Model (i.e. Student.java):

This class is an abstraction of database entry.

```java
import javax.persistence.*;
//indicates this class is an entry in a database. A table modeled after this schema is created automatically if it doesn't yet exist.
@Entity
public class Student{

    //indicates this variable is the 'primary key' and uniquely identifies each entry
    @Id
    //indicates each primary key will be automatically generated. End-user only needs to enter the other columns (name and age).
    @GeneratedValue(strategy=GenerationType.IDENTITY)
    private long id;

    //indicates it's a field (column) in the database.
    @Column
    private String name;

    @Column
    private int age;

    //getters and setters!
    public long getId(){

        return this.id;
    }

    public String getName(){
        return this.name;
    }

    public int getAge(){
        return this.age;
    }

    public void setId(int id){
        this.id = id;
    }

    public void setName(String name){
        this.name = name;
    }

    public void setAge(int age){
        this.age = age;
    }
}
```


## 6- Repository (i.e. Classroom.java):

This class is an abstraction of a database table.

```java
import org.springframework.data.jpa.repository.JpaRepository;
//i.e. This is a repository of 'Student' objects. Each uniquely identified by a 'Long' variable (i.e. id)
public interface Classroom extends JpaRepository<Student, Long>{}
```

## 7- Controller (i.e. SchoolController.java):

This class is what the end-user interacts with.

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.beans.factory.annotation.Autowired;
import java.util.*;

@RestController
public class SchoolController{
    @Autowired
    private Classroom room;  //Repository injection

    @GetMapping(value="/")
    public String homeScreen(){
        return "Welcome!";
    }

    @GetMapping(value="/students")
    public List<Student> getStudents(){
        return room.findAll();
    }

    @PostMapping(value="/enroll")
    public String enroll(@RequestBody Student scholar){
        room.save(scholar);
        return "New student added...";
    }

    @DeleteMapping(value="/remove/{id}")
    public String removeStudent(@PathVariable long id){
        Student selected = room.findById(id).get();
        selected.delete(selected);
        return "Student deleted";
    }

    @PutMapping(value="/edit")
    public String editStudent(@PathVariable long id, @RequestBody Student scholar){
        Student selected = room.findById(id).get();
        selected.setName(scholar.getName());
        selected.setAge(scholar.getAge());
        room.save(selected);
        return "Student edited...";
    }

}

```


## 8- REST-Controller is now READY!:

Use Postman to send GET, POST, PUT, DELETE requests.

```bash
mvn clean package               #Compile and test!
java -jar target/PROJECT.jar    #or: java -cp target/PROJECT.jar com.company.project.Mainclass
```


If it's up and running, go to:  localhost:8080/

in postman, try:
```bash
POST     localhost:8080/enroll     Body>raw>JSON
```
```json
{
    "name": "John",
    "age": 38
}
```


## 9- Now let's create the MVC-Controller. (i.e. SchoolRenderer.java):

Thymeleaf is used by this class to render templates stored at 'src/main/resources/templates/' (i.e. index.html)

```java
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

@Controller
public class Renderer{

    @GetMapping("/")
    public String showIndexPage(){
        return "jsform";
    }
}

```

## 10- Now it's time to implement the front-end:
I'll leave the "(USER_INPUT)" retrieval up to you. What you need to know is the bread and butter; how to use the "fetch" API to send HTTP requests.
```javascript
//--GET--
fetch("/students")
.then(response => response.json())
.then(data => {//i.e. 'data' is a list of Student-Objects//})
.catch(error => {console.log("Error: ", error)});


//--POST--
fetch("/enroll",
{
    method:"POST",
    headers:{"Content-Type":"application/json"}
    body: JSON.stringify(USER_INPUT)
})
.catch(error => {console.log("Error: ", error)});


//--DELETE--
fetch(`/remove/${id}`,
{
    method:"DELETE",
})
.catch(error => {console.log("Error: ", error)});


//--PUT--
fetch(`/edit/${id}`,
{
    method: "PUT",
    headers: {"Content-Type":"application/json"},
    body: JSON.stringify(USER_INPUT)
})
.catch(error => {console.log("Error: ", error)});

```

Alternatively, you can use "async" and "await" for cleaner code:

```javascript
//--GET--
async function fetchItems() {
  try {
         const response = await fetch('/enrolled');
         const data = await response.json();
         //handle request
      } catch (error) {
     console.error('Error:', error);
      }
}


//--POST--
 async function addStudent() {
 try  {
     await fetch('/enroll', POSTmsg());
     //handle request
      } catch (error) {
     console.error('Error:', error);
      }
}

//--DELETE--
async function deleteStudent(each_id){
  try {
     await fetch(`/remove/${each_id}`, {method: 'DELETE'});
     //handle request
      } catch (error) {
     console.error('Error:', error);
      }
}


//--PUT--
async function updateStudent(each_id){
   try{
     await fetch(`/modify/${each_id}`, PUTmsg());
     //handle request
      } catch (error) {
     console.error('Error:', error);
      }
}

```
