---
title: "Spring MVC model"
date: "2018-06-21"
description: "This blog will introduce the basic idea for MVC and how to use Spring to implement it."
categories:
    - "Java"
    - "Cloud computing"
    - "Spring"
    - "MVC"
---

# Spring boot Framework

Spring boot framework is a strong framework helps you build a MVC model web service quickly. So in the first place, we need to understand what is MVC model

## Model-View-Controller

1. Model

Model represent knowledge. It represents how basic items interact with each other.

2. Views

A view is a (visual) representation of its model. A view is attached to its model (or model part) and gets the data necessary for the presentation from the model by asking questions. It may also update the model by sending appropriate messages. 

3. Controller

A controller is the link between a user and the system. It provides the user with input by arranging for relevant views to present themselves in appropriate places on the screen. 

So, in my understanding, **Model** is the basic object and how they interact with each other. **Views** is how to represent model. **Controller** is a way for user to control the model, to ask model to show what it wants.

## Spring boot basic knowledge

What is Spring boot? It is the easist way to create a stand-alone, production-grade Spring based Application. It has lots of componments that you could use then just with some tags. It is embed Tomcat, Jetty or Undertow directly and you do not need to worry about common DataBase opertion. You could use some interfaces to help you do these stuff.

It contains Spring MVC, Spring Data, Hibernate and Tomcat.

### Model

It is a Class with typical annotation. Using these annotation, Spring will know how to handle these class element and how to save them in the MySQL. 

The easist way is to create a **Entity** model. `@Data` is a **Project Lombok** annotation to autogenerate getters, setters, constructors, toString, hash, equals, and other things. It cuts down on the boilerplate.

`@Id` and `@GeneratedValue` are `JPA` annotations to note the primary key and that is generated automatically when needed.

```java
@Data
@Entity
public class Employee {

	private @Id @GeneratedValue Long id;
	private String firstName;
	private String lastName;
	private String description;

	private Employee() {}

	public Employee(String firstName, String lastName, String description) {
		this.firstName = firstName;
		this.lastName = lastName;
		this.description = description;
	}
}
```

It is easy to interact with MySQL, in the beginning, we need to set up some properties for [Application](https://spring.io/guides/gs/accessing-data-mysql/).

Then, we could create an interface. CRUD means create, read, update and delete. Then you could use it in the controller. 

```java
public interface TodoItemRepository extends CrudRepository<TodoItem, Long> {

}
```

### Controlller

The controller will do some operations on model. **RequestMapping** will do staff for web mapping.

```java
@Controller
public class HomeController {
@RequestMapping(value = "/")
	public String index() {
		return "index";
	}
}
```

### Viewer

In Spring, we do not need a specific viewer class. Most of work is done in html and js. 


# Conclusion

What I found in learning Spring-boot is it has so many components to learn and I need to understand the relationship between them.

