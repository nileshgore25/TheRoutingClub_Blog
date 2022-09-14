---
title: "Developing Applications Using Cisco Platforms and APIs"
date: 2022-06-16
draft: false
tags: [DEVCOR, DevNet, Cisco]
# series: [MPLS VPN]
# categories: [technical]
toc: true
sidebar: false
singleColumn : true
# description: abc
# introDescription: efg
# abstract: xyz
summary: "This Post is to document the learnings from - Developing Applications Using Cisco Platforms and APIs"
# AuthorName: "Nilesh Gore"
---

This Post is to document the learnings from - Developing Applications Using Cisco Platforms and APIs

# 1 Designing for Maintainability

##  1.1 Introduction

The maintainability of software is often thought about as a scale that tells you how difficult an application or a piece of code is to understand, maintain, repair, and enhance. With long-running applications, the cost of maintenance can often amount to half or more of the project-related expenses, which is why it is important to make software as maintainable as you can in both design and implementation.

##  1.2 Functional and Non-Functional Requirements

When designing software, it is often very hard to evaluate how the application should be built and which design pattern should be used. While the developer's experience and good practices come in handy, it is often better to start objectively, with collecting requirements—functional and non-functional.

Functional requirements specify what the software in question should do. They describe the functionalities of a system, such as a business process, data manipulation, user interaction, media processing, calculation, or any other action that the system can perform. Functional requirements can be measured in "yes" or "no" terms and usually include the words "can" and "shall."

For example, some functional requirements for a document editing web application would be:

*	User can access documents through a web GUI.
*	User can create, edit, and delete documents.
*	User can save documents to a local drive.
*	User can restore documents to a previous version.
*	Administrator can create and delete users.
*	Documents can be sent via GUI to other users.

On the other hand, non-functional requirements tell you how a system should perform those actions. You can also think of them as the quality attributes. Non-functional requirements often use the words "must" and "should" and are commonly measured over an interval or a range.

A few examples of non-functional requirements are:

*	The system must support at least 10,000 concurrent users.
*	The system should be available at least 99 percent of the time.
*	Web GUI should take less than two seconds to load.
*	Users are required to register before they can use the system.
*	Passwords must not show anywhere in the logs or the GUI.
*	The GUI system can be translated into all other languages.

Avoid using nonspecific words when writing non-functional requirements. Instead of saying "Request must be fast," say "Request must be completed under 0.3 seconds." Instead of "GUI must be user-friendly," say something like "GUI must respond in under 0.5 seconds and all main functionalities must be accessible from the home screen."

So what comes first, the functional or the non-functional requirements?

![](/images/devcor/devcor_1_1_1.JPG)


Whether to prioritize your functional requirement or your non-functional requirement is a question like the chicken or the egg problem.

One would argue that businesses are driven by the amount of money they earn, which can be directly correlated to their success in their business area. In this case, business requirements (that fall under functional requirements) are the clear winner and most projects start with functional requirements. But what about business requirements that verge on the edge of non-functional requirements (for example, scalability and security for a cloud service provider)?

As with most things, it is important to evaluate requirements to see which ones should be prioritized and which ones impact your application the most. Product owners may focus purely on functional requirements, and it may be up to the development team to take ownership of non-functional requirements.

To consider the impact a requirement has on application quality, assess the requirement by identifying the requirement and creating user stories (for example, users need to have good experience while browsing), then determining the measurable criteria (for example, round-trip time should be under 500 ms), and finally, identifying the impact (for example, users will not visit your site if it is too slow).

### Functional Requirements

Functional requirements are designed to be read by a general, not necessarily technical, audience. Therefore, they are often derived from user stories. User stories are short descriptions of functionalities, as seen from the end-user perspective. They focus more on how the user interacts with the system and what they expect the goal of the interaction to be, rather than what the system does, but since the software is often very domain-specific, they help you to understand the system better as a developer.

Another concept used in the formulation of functional requirements is the use case. In contrast to user stories, use cases focus less on the user's interaction and more on the cause and effect of actions. If a user story stated, "A user can save documents by clicking Save," the related use case would state "When the Save button is pressed, the current document is saved to the server's NoSQL database and to the client's local cache."

Functional requirements should:

*	Be very concise and to the point. Avoid vagueness.
*	Be testable.
*	Clearly define who can perform certain actions or access data.
*	Fully cover every scenario, including what the system should not do.
*	Include complete information about information going in/out of the system.
*	Contain only a single functionality.

When gathering requirements, consider the business, administrative, user and system requirements.

Business requirements are high-level requirements from a business perspective, such as the users being able to log in with credentials from an existing application.

Administrative requirements take care of routine actions, such as logging every change made to a document.

User requirements contain the desired outcome of certain actions, like the creation of a new document.

System requirements describe software and hardware specifications, like a specific error code being returned when an unauthorized user tries to access a document that is not theirs.

### Non Functional Requirements

Non-functional requirements specify quality attributes or technical requirements of the system.

They are more related to the system's architecture than its functionalities and are often constraints on the technical properties of a system.

Non-functional requirements are often very technical and less thought about than the functional requirements. While functional requirements are commonly defined with the help of the project's stakeholders and are more straightforward, non-functional requirements are more implicit and sometimes just assumed. An example of an assumed, but not commonly listed non-functional requirement, is a short loading time of a web page (for example, less than two seconds). Another example of an assumed non-functional requirement is a sudden change of underlying technology, due to lower licensing costs. If modularity is not included as a non-functional requirement from the beginning, this change can cause big delays.

For those reasons, comprising a list of non-functional requirements more often than not falls on you and your team as software designers or architects. In contrast to functional requirements, which are often isolated features that can be worked on by a single developer, non-functional requirements can span the entire application or system and must be considered by all the developers working on the project.

Not all non-functional requirements can be implemented and tested at the same time. For example, while a requirement for portability can be considered at the beginning of the implementation, full performance efficiency tests cannot be satisfied until the code is running in an actual production environment. Not all non-functional requirements should be taken into consideration when developing, since it is easy to go overboard. For example, a simple web page probably does not need to have 99.999 percent availability and use enterprise-grade security.

Since many different non-functional requirements exist, the choice of on which non-functional requirements to put the most emphasis on can have major impact on further development. Too many non-functional requirements can make application development an incredibly long and complicated process, and too few non-functional requirements can create an application that does what it is supposed to do, but is very slow, insecure, and costly to maintain.

## 1.3 Non Functional Requirements and Application Quality


Many different non-functional requirements exist and the choice of which ones to focus on can have a major impact on further development. Too many non-functional requirements can make application development an incredibly long and complicated process, and too few non-functional requirements can create an application that does what it is supposed to do, but is very slow, insecure, and costly to maintain.

Non-functional requirements specify software product qualities or technical requirements of the system. There are many non-functional requirements and the list of them is growing year by year, since software development is a quickly evolving field. The following are some of the most used non-functional requirements:

* Availability (degree to which the system is operational)
* Backup (ability to create a copy of the data so that the system can be later restored from it)
* Compliance (conformity to certain certifications and specifications, such as the ISO standard)
* Data integrity (assurance of accuracy of the data)
* Disaster recovery (ability to recover from natural and human-made disasters)
* Extensibility (potential for future growth such as adding new features)
* Functional stability (the system always does what it is supposed to)
* Maintainability (degree to which the system can be maintained easily)
* Observability (how well internal state of the system can be understood)
* Open Source (permission to use the source code)
* Performance efficiency (how well the system performs under a stated amount of users or resources)
* Portability (ability to port the system to a different hardware or software environment)
* Reliability (ability of the system to function for a stated amount of time)
* Resilience (degree to which the system continues to provide service despite faults and challenges)
* Responsiveness (ability to complete given tasks in given time)
* Scalability (ability to increase the amount of work done by the system by adding resources)
* Security (degree of resilience against potential harm caused by others)
* Testability (degree to which a specific software component can be tested in a given context)
* Transparency (operating in a way that is easy to see to others)
* Usability (the ease to learn and use a system by others)

As a developer, it is crucial to be aware of the big picture of what the application does, who will be using it, and what its lifecycle will look like.

Will the number of users stay the same or will it grow? Is the application supposed to handle sudden traffic spikes? What will the response time be for users around the world? Will new users be able to use the application without any help?

The answers to these questions have a noticeable impact on the application quality, and a larger impact if not all assumptions and expectations of the customer have been incorporated into the design.

A well-defined and implemented set of nonfunctional requirements has some great benefits:

* **Increased code quality:** By adhering to good coding practices and using standardized development practices as a nonfunctional requirement, the code becomes easier to understand and maintain.

* **Better speed and performance:** Performance and speed can be one of the most important requirements in a system (for example, high-throughput or stock-trading systems). By including them as a nonfunctional requirement, you ensure suitable operability even under higher loads.

* **Fewer bugs and errors:** A focus on nonfunctional requirements such as functional stability, testability, and resilience can reduce the number of bugs and errors in an application.

* **Higher uptime:** By including nonfunctional requirements such as resilience, disaster recovery, and availability, you can achieve very high uptime for your application.

* **Improved security:** Security is taken for granted in some enterprise environments. Security as a nonfunctional requirement can add an additional layer of security.

* **Reduced maintenance costs:** Nonfunctional requirements, such as modularity and portability, can drastically lower the long-term costs for maintaining applications.

* **Better user experience:** Very important benefit for GUI-based systems, since user experience often makes or breaks the application. Usability and responsiveness are often expected from the user, so an additional focus on them as nonfunctional requirements ensures happier customers.

On the other hand, ignoring nonfunctional requirements, deprioritizing them until the end of development, or just plain skipping them, can cause a major headache for someone down the road, either for the product owner, who will have to dedicate extra resources to fix these problems, or for the development team, who will spend months refactoring the code in order to comply with all requirements.

Situations like this can cause a large amount of technical debt. This is a concept in software development that reflects cutting corners, taking shortcuts, improper design, or strict time constraints in the development phase. It can be caused by an insufficient or ever-changing scope of requirements and lack of code modularity in combination with changes in the environment, pressure to focus on business requirements, and many other factors. In general, technical debt is commonly caused by poor or a lack of nonfunctional requirements. As with regular financial debt, technical debt can acquire interest—meaning if you pile more debt on top of the existing debt, it is only going to get worse.

Technical debt takes time, people, and resources to fix. It is not uncommon to spend more time refactoring a piece of code than it would take to properly implement it in the first place, because there may now be additional dependencies.

Some examples of technical debt include:

* **Lack of testing:** Code tests are often skipped, since they are usually not a part of the requirements that the customer has. This causes a higher number of bugs that get past manual testing and are discovered later, when an application is already operational.

* **Weak security:** High security is often a big constraint on the system—for example, using proxies, access lists, and encryption. Security often is less strict during the development phase for easier and quicker development and is stepped up as an application is shipped to production. However, the security is not always increased because of time and resource constraints, which causes technical debt.

* **Improper environment:** Environments can be set up with a set of assumptions in mind (for example, architecture type, high availability). If these assumptions are not included in the nonfunctional requirements, different people can assume different things. This way you can end up with a scenario where backups are being saved on production servers and losing the production servers also causes the loss of backups.

* **Lack of modularity:** A very common consequence of an absent nonfunctional requirement is a lack of modularity. No code modularity complicates making any changes to the underlying hardware and software, such as changing the load balancer vendor or using a different software library for HTTP requests, for example.

In the long term, it is common to see that the nonfunctional properties of the system become the main reason an application succeeds or fails. Therefore, it is often said that nonfunctional requirements correlate strongly with the application quality. The notion is not new in software development; however, we witness again and again how a potentially very useful and promising application dies off very quickly even though the application does what it is supposed to, either because it was not scalable, it lagged visibly when used, or it gets hacked within the first month of release.

Since nonfunctional requirements often do not present any immediate added value to the product, their importance can be demonstrated to nontechnical personnel by aligning it with business goals (for example, expected business growth, compliance with certain regulations to enter new markets, and so on) or by presenting them with what-if scenarios (for example, what if user data gets breached due to low security, what if our developers resign and leave undocumented code).

Lack of nonfunctional requirements can make the application nonfunctional.

## 1.4 Maintainability Through Design

If there is one thing you can be sure of when designing your application, it is that you will have to change something at some point. While most changes can be done with enough time on your hands, this will often not be the case. By designing your application to be maintainable, you can save a lot of time and trouble in the future.

In the recent times, software projects have grown considerably in both size and complexity. Project lifecycle costs often dedicate more than 75 percent of resources to maintaining and modifying existing projects to fix errors or add new functionalities to meet the customers’ needs. Even if the requirements were gathered and followed to the letter, it is not uncommon to miss some use cases and to require additional functionalities. Change is inevitable and the need to maintain and improve the software at a fast and cheap rate is desirable.

Maintenance costs include but are not limited to:

* **Fixing bugs and errors:** Bugs and errors reduce the effectiveness of an application. They are usually fixed by priority. The most severe and most frequently occurring bugs are fixed first. While most bugs should be caught by various testing methods, some bugs only surface after some time or in a specific environment. Maintainable code allows you to isolate and fix the error more quickly.

* **Adding new features:** Additional features or improvements to existing features are commonly requested by customers. If the application is designed well, adding new functionalities should be simple enough.

* **Refactoring code:** Refactoring improves the quality of an application. It is often performed after the initial deploy due to technical debt, created in the development phase.

* **Performance optimization:** Applications usually have room for performance improvement. Run-time analysis can highlight performance bottlenecks that can be optimized so that the application can perform better. These bottlenecks can be code-related (bad, inefficient code) or environment-related (slow network, storage).

* **Adapting code for environment:** Updates to underlying hardware and software are sometimes not backward-compatible and break a piece of your application. Testing the application with new environment components and then adapting the code for the application to function is a common maintenance task.

* **Preventing potential problems:** The problems that the application will encounter can sometimes be predicted (disks getting full, date-related problems like Y2K) and fixed beforehand.

Early planning is important for achieving maintainable software. To achieve maintainability of your application through design, it is a good idea to adhere to existing good practices and strategies while planning your application. Some common practices that will almost always make your application more maintainable are:

* **Modular design:** Modular design makes it easier to replace or reuse application components and to work on specific components independently.

* **Naming conventions:** Adhering to a naming convention throughout the project increases observability and standardization and makes it easier for additional or new developers to work on a project.

* **Software configuration management:** Tracking and controlling changes is vital to any software project, even more so if there are more people working on it. Configuration management takes care of code versioning, in addition to any hardware and software configurations.

* **Coding standards:** Using well defined coding standards makes development of new functionalities faster, since it takes away some of the decision making from the developers themselves. It also makes modifying existing code easier, since code from all developers has a similar look and feel due to standardization.

* **Common toolset:** By using a common development toolset (everything from Integrated Development Environment (IDE) and OS to environment and testing tools ...), communication between developers becomes easier and less toolset specific. This enables easier and standardized onboarding of new developers and new technologies.

* **Object-oriented design:** By using object-oriented programming and design, you reduce the complexity and tight coupling between components. Object-oriented design should also be modular and extensive by nature. Independent classes also make any subsequent changes easier to implement, since a change is required only in a smaller number of classes instead of throughout the application.

The list of industry's best practices changes from year to year and from technology to technology. As far as the maintainability goes, one of the best practices you should adhere to is the SOLID principle. It helps you design maintainable, extendable and object-oriented code.

### SOLID Principle

SOLID is an acronym for five software design principles:

1.  **Single responsibility principle:** One class should have only a single responsibility.

1.  **Open-closed principle:** Components (classes, methods, etc.) should be open for extension but closed for modification.

1.  **Liskov's substitution principle:** Derived types must be completely substitutable for their base types.

1.  **Interface segregation principle:** Clients should not be forced to depend upon the interfaces they do not use.

1.  **Dependency inversion principle:** Program to an interface, not to an implementation.


These five principles are a subset of many software design principles that apply to object-oriented design. They translate well to general software development and have become very popular when applied in combination with agile development. As you can see, SOLID is a mnemonic for the first letters of the principles.

When combined, the principles make it easy for developers to maintain and extend software. They also reduce the amount of potential technical debt and keep the code flexible, no matter how many functionalities are added on top of it.

The single responsibility principle states that a class should have only one reason to change, which implies that it has one and only one job.

If the class handles more than one responsibility, any potential changes affect others too. This is not ideal when handling small applications but can become a complete nightmare when you work with larger enterprise systems and have classes that are used by several unrelated subsystems. For example, a class that handles creation of weekly financial reports and contains some methods that are used for formatting data within the accounting and HR departments.

The principle should be applied not only on the class level, but also on the module, method, or microservice levels. A common beginner's mistake is a method that is too long and does multiple things, such as code that makes a GET request, parses the response, and saves something to a database, all in one function.

Following is an example of a class that has not one, but two responsibilities.

![](/images/devcor/devcor_1_4_1.JPG)

This class violates the single responsibility principle because the class **NetworkService** has two responsibilities.

First, it takes care of setting the class parameters, ***set_name*** and ***set_config***, in addition to deploying the service itself, ***deploy_service***. Any changes to the deployment process could cause changes to the NetworkService class, which are not needed.

To apply the single responsibility principle, you need to decouple responsibilities so each class only has a single responsibility.

![](/images/devcor/devcor_1_4_2.JPG)

For this example, the **NetworkService** class should only be responsible for setting data for the service itself.

An additional class **NetworkServiceDeployer** will be responsible for deploying the service.

In practical terms, this allows two developers to work on these classes. One can change how the service parameters are set, while the other one can change how the service is deployed, all without affecting each other's work.

The facade pattern is commonly used alongside the single responsibility principle. It hides the complexity of underlying code by providing context-specific interfaces.

![](/images/devcor/devcor_1_4_3.JPG)

The facade pattern does not violate the single responsibility principle, since the class still has only one responsibility; it only offers you an interface to the secondary one and does not contain the implementation of that functionality itself. In case of modifications to **NetworkServiceDeployer**, the **NetworkService** class would stay the same.

The open-closed principle states that components should be open for extension and closed for modification.

In practical terms, the open-closed principle means that additional features can be added without changing the underlying source code, while still using it. You should not have to modify any classes, interfaces, or other components to add a new functionality.

Examine the following example. NetworkServiceDeployer is used to deploy network services in different network segments, depending on the service type. Currently, the deployer supports deployment in ***user*** networks. What happens if you want to add support for ***mgmt*** networks? You would have to modify the existing code and add support for ***mgmt*** networks, which violates the open-closed principle.

![](/images/devcor/devcor_1_4_4.JPG)

To conform to the open-closed principle in this example, the code must be built in such a way that the **NetworkService** and **NetworkServiceDeployer** remain unchanged. Fortunately, Python and other object-oriented languages allow you to extend classes to add extra parameters or functionalities to class instances.

In this case, you add a ***segment*** parameter that converts the if clause from the previous example to an extended class parameter.

![](/images/devcor/devcor_1_4_5.JPG)

This code snippet now adheres to the open-closed principle and allows us to add any number of network service types by adding additional extended classes.

Note the **super()** call. This is a function call that has access to the original nonextended class, ***NetworkService***, its parameters, and methods. By using it, you can initialize the original class and then append extra parameters to each extended class.

Liskov's substitution principle requires subtypes to be substitutable for their supertypes. The aim is to ensure that a subtype can assume the place of its supertype without causing any errors or issues in the application.

In practical terms, a child class should never change the characteristics, such as arguments or return types, of its parent class.

Therefore, a subclass should require nothing more and promise nothing less.

The following example demonstrates how Liskov’s Substitution Principle can be violated. An extended class should not change the behavior as the **NetworkServiceUser** class does for the **get_config** method. It introduces another parameter (***network_id***) and prints out the config, instead of returning it.

![](/images/devcor/devcor_1_4_6.JPG)

The benefit of Liskov’s substitution principle becomes obvious when you have different extended classes and expect them to behave the same.

For example, if you had an array of different **NetworkService** classes and subclasses and you wanted to call **get_config** on every class (no matter the class subtype), this class could not be used instead of its superclass, since it would not return the config and would raise an error because of a missing parameter.

Another slightly less serious saying regarding Liskov’s substitution principle is: "If it looks like a duck and quacks like a duck but requires batteries, you probably have the wrong abstraction."

The interface segregation principle states that interfaces should not depend on interfaces they do not use. It steers you toward creating smaller and more flexible interfaces.

![](/images/devcor/devcor_1_4_7.JPG)

The example demonstrates how the violation of interface segregation principle makes the code less readable and harder to change and maintain. It also forces the developer to implement empty dummy methods that do nothing. Interfaces that contain many such methods are called fat interfaces, while leaner and more specific interfaces are called role interfaces. A symptom of many fat interfaces in an application is interface pollution—where many classes become incredibly long and contain many methods that they do not need. If interface segregation principle is not enforced, the interface pollution trickles down to subclasses of fat interfaces, further increasing the technical debt.

In most cases it is preferable to have a larger number of subclasses than a larger subclass for maintenance, especially if the former conforms to the single responsibility principle.

The dependency inversion principle focuses on avoiding tight coupling by separating low- and high-level classes. It encourages you to program to an interface and not to an implementation and states:

High-level modules should not depend on low-level modules. Both should depend on abstractions.

Abstractions should not depend on details. Details should depend on abstractions.

Dependency inversion implicitly enforces modularity, since it makes low-level modules easier to replace, as they are not tightly coupled into the higher-level classes.

The example shows a snippet of code, where the high-level class **NetworkService** is highly dependent on an implementation of a low-level class **Router_IOS** and its parameters. The low-level class is also dependent on the high-level one, since **NetworkService** directly sets the service parameter for **Router_IOS**.

![](/images/devcor/devcor_1_4_8.JPG)

Such code is impractical to maintain. Changing the high-level class requires changes in low-level classes and vice versa. If, for example, you wanted to add a Cisco IOSXR router and a virtual switch to the topology, you would have to add new methods to the high-level class and create new low-level classes for each equipment type.

So, how can you implement the last example in a way that it adheres to the dependency inversion principle?

You need to adapt the high-level class to use an abstraction of lower-level classes. In this case, an abstraction would be the process of adding a network service device to the network service itself and setting the service parameter on the device, where it does not matter if the underlying device is a router, a switch, an Internet of Things (IoT) sensor, or something completely different.

High-level classes should manage decisions and orchestrate lower-level classes, but not do any "work" themselves, whereas lower-level classes should contain little decision-making but implement the actual "work", such as application progamming interface (API) calls, database writes, view updates, data manipulations, and so on.

![](/images/devcor/devcor_1_4_9.JPG)

For the purpose of abstraction, a **NetworkServiceDevice** class is added. It does not do anything by itself and is extended by the actual devices in the **Router_IOS** class, which overrides the methods in **NetworkServiceDevice** class.

This way, the **NetworkService** class does not depend on the implementation of actual devices, but rather only on the abstraction of the network service device. Device implementations no longer rely on the **NetworkService** class to set the service parameter, but rather read it from the **NetworkServiceDevice** abstraction and set it themselves.

Note how low-level classes implement add_device and set_service methods differently but use the same abstraction in high-level class.

## 1.5 Maintainability Through Implementation

While design plays a big part in how maintainable your application is, the implementation itself can amplify or diminish any benefits to maintainability. Adhering to the industry's good practices makes implementation and later maintenance much smoother.

The implementation phase follows the requirements gathering and design phase and consists of the actual coding process.

During the implementation phase:

* Major decisions should have been made but can be revisited.
* Consistency and coordination within the team are important.
* Stick to common tools, languages, and techniques.
* Bad implementation decreases application quality.
* Be aware of the "Bus Factor."
* Good implementation reduces implementation and maintenance times and lowers the learning curve.

To understand how to build maintainable software during implementation, first it is important to understand the implementation phase. This is the phase where the actual application gets built. The focus shifts from software designers and architects to developers (who might be the same people). The developers begin developing code and tests, designers help with graphical materials, system administrators build the environment, and so on. While, depending on the development model (agile, waterfall, etc), most of the major decisions should have been made, it is not uncommon to see design modifications due to unforeseen consequences. Ideally, this should be kept to a minimum, since design changes during implementation can be hard to implement.

The implementation phase commonly includes many developers. If every developer uses their favorite tools, language, libraries, coding techniques, versioning or comment styles, the project soon becomes an unsightly mess that no one wants to touch after deployment. This increases the "bus factor" on a project. The bus factor is a measurement of how much a project will suffer if a project member suddenly becomes unavailable (that is, gets hit by a bus). This is a common problem in software development, since code can be encrypted, obfuscated, undocumented, never shared, or incomprehensible to others.

Using common implementation techniques allows many developers to work in sync on the same project, no matter the actual functionality, and lowers the learning curve when developers work on code they did not write themselves. Good implementation also reduces the number of errors and can lower the implementation and maintenance time considerably.

### Consistent Coding

Software implementation should aim for high consistency—a useful and practical metric. All the development team members can benefit from consistent code, both while developing and debugging the code. Consistent coding enables standardization and simplifies adding new features by allowing the existing code to be reused and resolves some decisions more efficiently by observing certain coding styles.

Consistent code is one of the best indicators of good coding:

* Allows you to make assumptions and predictions about its behavior.
* Helps you avoid surprises in implementation.
* Easier to automate, test, and refactor.
* Simplifies onboarding of new developers.

Consistent code does not necessarily mean that the code itself is good. Depending on what the application does, how long the lifecycle will be, and how frequently the code base will be refactored, consistent bad code can be better than inconsistent good code. This does not imply that you should follow the path of least resistance and be satisfied with producing bad code if it is consistent. It only means that consistency is a software quality that can be prioritized.

For example, take an e-commerce app, written in Node.js by developer Alice. The application works fine but needs a new module after 2 years of operation. Since Alice recently left the company, Bob takes over the development and maintenance. Alice used a certain software library for handling requests that Bob is unfamiliar with. Instead of using this library, he uses his favorite one for the new module, adding to the list of dependencies. Another 2 years pass, Bob gets a promotion, and now it is up to another new developer, Chris, to take over. Chris analyzes the feature and decides to use PHP to build the third module on top of the existing application, again adding to the dependencies and the complexity of the application. Even though a different library and PHP might have been a better choice for the individual features at some point, it makes sense to evaluate potential benefits against the loss of consistency within the application.

Common tools, used to achieve consistency in software projects are:

* Linters to analyze code and style guide.
* Adherence to a naming and coding convention.
* Shared libraries and development tools.
* A software configuration management system.

A linter is a tool that automatically analyzes source code and notifies you about errors (both stylistic and potentially functional), bugs, and suspicious uses of code.

Linters originated in a UNIX utility that used to flag suspicious and nonportable constructs in C source code, but they have evolved greatly since then. A linter is not a single program, but rather a tool concept. It comes in many forms: as an extension to IDEs, as a plug-in for other systems (continuous integration and continuous deployment [CI/CD] pipelines, compilers), as standalone applications, or as modules that you can include in your project.

While a linter is a tool, it does not dictate or decide how the code should be stylized. Coding style or programming style does that instead.

A linter analyzes lines and patterns in source code:

* Indentation and vertical alignment: Coding style dictates how lines will be indented and how multiline commands will be aligned.
* Tabs and whitespaces: The eternal question of tabs versus spaces is solved by using a preferred style. This helps with languages where indentation is important, like Python.
* Line length and wrapping: A linter can notify you if a line exceeds a certain length or if different (for example, Windows/UNIX) line endings are used, which can interfere with execution of code.
* String formats: Coding style sets a format for strings and their formatting (for example, single or double quotes, string concatenation).
* Naming and commenting conventions: A linter notifies you if case naming (such as CamelCase or ALL CAPS) or commenting conventions are breached.
* Language-specific programming concepts: Style guides enforce the style of programming constructs such as loops, classes, objects, exception handling, and other structures. It also checks if the function calls match the expected returns and if the correct types are expected.

Coding styles are language specific. While they may include similar style elements, the languages are different enough that the styles cannot be reused. Some style guides are vendor-specific (that is, Google Style Guides) intended to be applied in the ecosystem of that vendor for easier troubleshooting and development.

Some of the most popular linters are ESLint for JavaScript and PyLint for Python.

This is an example of linter output, using Visual Studio Code in combination with a Python linting extension. Some linter errors are fatal (red) and will cause the application to encounter an error, while others are warnings (yellow) and only notify you about a style error.

![](/images/devcor/devcor_1_5_1.JPG)

Each class, function, data structure, or other programming construct should have a unique purpose in your code. If you find yourself writing the same code over and over, there is probably a better solution to what you are doing.

The DRY (Don't Repeat Yourself) Principle states that every piece of knowledge must have a single, unambiguous, and authoritative representation within a system.

It aims to reduce code duplication and increase code reusability:

* Saves time and effort (less code overall, easier to grasp)
* Easier to maintain (each functionality is in only one place)
* Reduce the chance of bugs

Code reusability is not only relevant for a single project. During your career as a developer, you will notice that many of your tasks are repetitive and can be automated (for example, creating backups, running tests, setting up your environment). Consider if such tasks are worth automating, since a small-time investment up front can save you a lot of time in the long run.

### Keeping Track

A sizeable part of the code that keeps an application running is usually not written by the same people that develop it, since a big part of the code base is kept in prewritten software libraries, also called packages or modules. These libraries are collections of various classes, functions, and functionalities written in a specific language and offer a well-defined interface to interact with them. Libraries are not necessarily built by other people. Many bigger companies have in-house libraries for their business domain.

In most programming languages, you either include the libraries in the build process (static libraries) or load them at run time by importing them in your code (dynamic libraries).

Because the application depends on them to function, they are called dependencies.

![](/images/devcor/devcor_1_5_2.JPG)

Dependencies often rely on and include other dependencies, leading to a huge number of dependencies being included in a project, sometimes only for a simple functionality. Languages, such as JavaScript, are notorious for dependency chains and dependencies on specific versions of software libraries. Incompatible or conflicting dependencies are sometimes also known as dependency hell.

Therefore, a structured approach toward dependency management is very important for maintenance. Many languages feature specific tools for automatic dependency management, such as Gradle and Maven (primarily for Java), npm for JavaScript, and pip for Python. Dependency management is very language and environment specific and should be taken as such.

Even more important than keeping track of dependencies is keeping track of and controlling the application itself.

This process is called Software Configuration Management and includes:

* **Version control:** Keeping track of changes in your software with the help of versioning tools like Git. Versioning can take place at the code level (for example, commits) or at a higher level like feature and application versions.

* **Configuration control:** This process ensures that the changes to a system are done in a controlled and coordinated manner. This is commonly done with a control panel that can accept or deny changes that are about to happen.

* **Build management:** The process of building the application from the source code. It may also include testing, analyzing, and then deploying the software. Build management also includes the management of tools required for build process.

* **Environment management:** Setting up the environment and keeping it up to date. This includes, but is not limited to, updating the operating system and its dependencies, setting up the network, checking if the hardware or software licenses are still valid, deploying virtual machines (VMs), and more.

* **Bug tracking:** Tracking and tracing bugs and defects. This is often included in project management tools.

Tools like CFEngine, Puppet, Ansible, Jenkins, and others support the software configuration management process.

## 1.6 Modularity in Application Design

Every system is composed of many smaller building blocks. The easier it is to break a system down into smaller subsystems, the easier it is to maintain and to replace them. Modular application design adheres to the idea that complex problems are easier to solve if they are broken down into smaller pieces.

Modular application design is the process of designing software so that it is divided into separate, unique modules.

Since applications can contain thousands of lines of code, managing and maintaining applications can become quite difficult. If applications are broken down into self-contained modules, they become:

* **Easier to understand:** It is much easier to understand a smaller piece of code that does one thing, than a huge codebase whose functionalities are all over the place. Using modules reduces the overall complexity of an application.

* **Takes less effort to maintain:** Fixing, maintaining, and debugging code with limited scope is much more straightforward and faster than nonmodular code.

* **Application becomes more flexible and extendable:** Modular design encourages application flexibility, since functionalities can be replaced or extended individually.

* **Code reusability increases:** Modularity enforces a reusable design of modules. Individual functionalities can be reused in other parts of the application or on other projects.

![](/images/devcor/devcor_1_6_1.JPG)

Modular software design enforces the following software qualities:

* **Composability:** The idea of composability is to produce software from reusable modules. The modules should work in an environment different from the one in which they were developed. Composed software should also be a reusable module itself.

* **Decomposability:** Decomposability is a scale of how easy it is to break down a problem into subproblems, connected by simple interfaces. The communication between problems should be minimized so work on each problem can proceed independently.

* **Understandability:** This trait defines how easy it is to understand and learn to use a module.

* **Continuity:** Continuity states that the smaller the change in specification is, the fewer modules need to be changed. This goes hand in hand with the single responsibility principle. If each module does one thing, and does that one thing well, the number of changes for each change request is minimal.

* **Protection:** Modular protection is satisfied if faults that occur in a certain module stay confined to that module. In more practical terms, an error in Module A should not break Module B. This does not mean that an error cannot be raised in Module A and handled in Module B; for example, if an API call failed in Module A, but the failed API call is handled in Module B, this Module A error would be handled by Module B, since the call passes control from Module A to Module B.

While composability and decomposability may seem to be opposites at first, they are not, and they are not mutually exclusive. Composability takes a bottom-up approach to design and decomposability takes a top-down approach. Both are required in modular software design. Evaluating when and how to use these methods is important. Decomposability is preferred in designing modules that fulfill specific requirements, while composability is preferred in more general modules.

![](/images/devcor/devcor_1_6_2.JPG)

Modular applications ideally have high cohesion and low coupling. A high cohesion means that the elements of a specific module belong together by functionality, data types, and so on. Low coupling states that the modules are largely independent from one another. Interactions between modules should be kept to a minimum but should be plentiful inside a module.

In contrast, modules with low cohesion often make function calls to other modules, while seldom using the module's internal functions and classes.

The opposite of modular design is integrated design, where no distinct lines exist between application components.

So, what exactly is an application module?

A module is a broad term in software development. Depending on the programming language and project type, a module can represent many things. A module can be a single class in object-oriented programming, an assembly (in .NET languages), a package (in Java or Go), a single file, or any other type of a functional component that is considered as a distinct part of a whole.

In general, modules have a few distinct properties:

* **Each module has a distinct purpose:** Modules should focus on a small set of data functionalities (they should do one thing and do it well). This produces transparent, elegant, and high-quality code. Combining different modules should produce a new and distinguished functionality.

* **A module encapsulates data and functionality:** The complexity of the underlying implementation is hidden from its consumers so that the module can be fixed or modified at any time. Subsequent changes in module's lifecycle are made behind the scenes. A module only needs to define its capabilities and the interface through which they are accessible.

* **Modules are reusable:** Good code is very valuable. It is reusable across the code base and even across projects and technologies. Many good and general-purpose modules end up being used by thousands of developers all over the internet. Code reusability also reduces code duplication.

* **Modules can have dependencies:** Each module can have some dependencies on other modules. Module versioning schemes are used to specify dependencies for a particular module.

![](/images/devcor/devcor_1_6_3.JPG)

Each module is comprised of an interface and a body. The interface is the set of all the elements in a module available to all the users of a module. It is also called the module's exported resources. A module's body is where the functionalities of a module are realized. It is also called the implementation of the module. Modules should also be designed to change, so the use of software patterns that emphasize reusability, such as the single responsibility principle or the dependency inversion principle, is recommended for implementation.

In addition to considering the aforementioned software qualities, module designers also should adhere to certain rules:

1. **Direct Mapping Rule:** The structure that is used in implementation of a software system should remain compatible with the structure used for modeling the system. If a clear model of the system exists, direct mapping is needed to ensure that no data is lost in the implementation and that any potential changes are easier to implement, which benefits continuity.

1. **Few Interfaces Rule:** Every module should communicate with as few other modules as possible.

1. **Small Interfaces Rule:** When two modules communicate, they should exchange as little information as possible. This and the previous rule aim to reduce the amount of communication between modules, which reduces the overhead and complexity.

1. **Explicit Interfaces Rule:** When two objects are communicating, this must be obvious in their class definitions. The conversations need to be clearly visible. This rule benefits composability and decomposability, since if you need to decompose a module into many submodules or compose it from several other modules, clearly visible connections make it easier.

1. **Information Hiding Rule:** Each module must have a limited subset of properties and functionalities available to the public; the rest should be secret. This is commonly achieved in code by making some classes public and making others private. By reducing the number of public functionalities, you increase the amount of module that can change without affecting the clients. The bigger the public part, the greater that chance is.

### Modularity and Microservices

While modularity applies well at the code level, it is also desired at higher levels. As you know, modularity is an important quality of microservices and you can see how individual microservices behave like distributed modules in modular design.

![](/images/devcor/devcor_1_6_4.JPG)

For this example, take an e-commerce application, focused on selling sports equipment, developed with microservices architecture. It features several microservices: authentication, order processing, catalog service, reviews database and the UI. Each microservice is focused on a single functionality inside a business domain. This way, each microservice covers a bounded context inside the domain, where bounded context is a logical boundary between subdomains.

Compare how the properties of a module relate to these microservices:

* Each microservice has a distinct purpose. Whether that is listing available items or authenticating a user, each microservice features a unique and focused functionality.

* Each microservice encapsulates the underlying functionality. As with modules, microservices do not care how the functionalities of other microservices are implemented. They communicate with each other via API or triggered events. This way, it is easy to change the underlying hardware and software on which the microservice is based.

* Each microservice is reusable. All the microservices in the example are usable in a way in other applications. Authentication can be reused for other applications inside an enterprise, order processing can be reused for other e-commerce applications, and so on.

* Each microservice can depend on other microservices. While microservices are fully capable of functioning alone, you usually need several microservices depending on each other, and working in tandem, to achieve a meaningful business functionality.

## 1.7 Dependency Injection

Some common problems you will encounter during development are how to make your classes and objects independent from one another, even though they depend on each other, how to make your application support different configurations, and how to specify such configurations separately in an effective manner.

Imagine your home TV as an object in a software project. It has a clearly defined functionality and a unique purpose inside your home (for example, watching movies, news and more). Functionality aside, the TV cannot operate by itself. It depends on electricity to power it and it depends on a medium to stream the video and sound to the screen.

Therefore, we can state that electricity and an information stream are dependencies of a TV.

Now consider the options you have to connect these elements together. For electricity, you could include a battery inside the TV. This would not be very useful, as the battery would have to be replaced directly in the TV. You could solder the power cable directly to the electrical grid, but then you could only use the TV where it was soldered to the power source. Or you could include an interface with the TV that is able to be plugged in many different power sockets—a power cord.

The same thinking applies to the information stream. You can include the media on the TV itself, but then you are limited in what the TV can show you. Instead, the TV offers you an abstraction of a media stream, such as an HDMI input socket, so you can choose what media to stream yourself.

Ideally, the TV works when connected to any kind of suitable electric outlet and displays whatever you send it over a cable. It should not depend on any specific brand or vendor but on their abstractions instead.

This is how you should implement your software too. A dependency injection is a pattern in software development that involves decoupling the code so that it separates the object's behavior from its dependencies. Dependencies in software can be any objects or data that are required for another piece of software to function. One way of structuring the providing of dependencies is to embed the logic for instantiating or locating the dependency directly inside of the code that requires it. Another way is to let the client declare its dependencies and have an external piece of code provide or inject them—dependency injection.

![](/images/devcor/devcor_1_7_1.JPG)

The goal of dependency injection is to achieve Separation of Concerns. Separation of concerns is a principle that tries to separate an application into unique sections, so that each section manages a specific set of information (a concern). Separation of concerns is a baseline for some other software qualities, such as modularity.

A good example of separation of concerns is the most basic web page development stack—HTML, CSS, and Javascript. Here, each language manages its own domain, but still complement each other. HTML is used for defining web page content, CSS tells you how the content will be presented, and JS defines how the content behaves and interacts with the user.

First, it is important to clarify some basic terms.

Dependency injection defines four different roles within its domain:

1. **Service:** An object that can be used.

1. **Client:** An entity that uses said service.

1. **Interface:** Defines how the client may use the service.

1. **Injector:** Constructs the service and injects it into the client.

The client is considered the “dependent” class. It depends on the service class to provide an actual service. Note that when saying client, the client-server relationship is only valid in the domain of dependency injection. A dependency injection client can still be a server in some other context (for example, a HTTP Response server).

The client should have no concrete knowledge of the implementation of the dependencies. This way, if the implementation of the dependency changes, the client won't have to, since it only relies on the interface that the dependency offers.

The pattern has some strict rules that should be enforced during implementation:

* The client delegates the responsibility of injecting its dependencies (services) to the dependency injector.

* The client doesn't know how to create the service, it only knows the interface of the service. The service doesn't know that it is used by the client.

* The dependency injector knows how to create the client and the service. It also knows that the client depends on the service and knows how to inject the service into the client.

* The client and the service know nothing about the dependency injector.

It also differentiates between three distinct injection methods:

* Constructor injection (dependencies provided by a constructor)

* Setter injection (client exposes setter method that is used to inject dependency)

* Interface injection (dependency's interface provides an injector method)

Dependency injection adheres to and is a result of the single responsibility and dependency inversion principles found in SOLID.

It also follows the inversion of control principle. In traditional software development, your custom-written code has the control (for example, a "main" function inside a program), and makes function calls to reusable software libraries within the framework to take care of generic tasks. With inversion of control, the control flow is inverted. Here, it is the framework that calls your custom-written code to fill in the blanks and only passes the control to them for a short amount of time.

For a practical example, consider a simple application that waits for users to enter their info and then sends it to a server. Traditional control flow gives control to this program, which makes function calls to create an application, windows, and input fields, and then waits for the user to enter the information. The custom-written code is in control and only passes it to the framework or libraries when it needs to. Inversion of control, on the other hand, inverts this control flow and builds the application using a software framework, that can construct and control graphical elements such as windows and input fields. It only passes control to your code when the custom code needs to be called.

For another practical example, imagine you are building a small application in Python. While Python offers many libraries that support dependency injection (for example, injector, abstractmethod), the example uses plain Python for simplicity's sake.

Your task is to create and test a UserManager class that can read users from external user repositories (for example, a SQLite database).

For the first revision, the implementation consists of two tightly coupled classes.

![](/images/devcor/devcor_1_7_2.JPG)

**UserManager** class depends on the **SqlSvc** class. Therefore, **SqlSvc** class is a dependency of the **UserManager** class. They are tightly coupled together, since the **UserManager** class knows how the **SqlSvc** class is implemented because it must do a function call directly on the service class. **UserManager** also instantiates the **SqlSvc** class, which means that it controls the **SqlSvc** class.

This is not an example of good code. Think about the following questions:

* How can you change the underlying user repository to something else? (You would need to create additional methods for new repositories)

* How can you test UserManager class independently of SqlSvc class? (You cannot—Every test needs a sqlite database set up)

For the second revision, dependency injection via a constructor has been used.

![](/images/devcor/devcor_1_7_3.JPG)

Note the following changes:

The **SqlSvc** class has been abstracted as a **UserSvc** interface. It no longer contains the code that read the users from the database.

The **UserManager** class no longer relies on the actual implementation of the **SqlSvc**, but on the abstract interface **UserSvc**.

The Injector class has been added. The Injector knows how to create both the client and the server. After the service has been constructed and injected into the client, you could do a successful **users_manager.get_users()** call to get the users from the SQL repository.

If the need arises to read the users from a different user repository (for example, Microsoft Windows Active Directory), you would implement a new service (for example, ADSvc) and inject that service into the client.

If the previous example injected the dependency directly into the constructor, the following example uses a setter method.

The benefit of this method is that dependency can be changed during run time by calling the setter method again.

![](/images/devcor/devcor_1_7_4.JPG)


One of the biggest benefits of dependency injection is the ability to test the client independently of the service.

In the following example, two mock service classes are created. The first one allows you to test the UserManager class with 2 users, Alice and Bob, and the second one allows you to test UserManager with no users.

![](/images/devcor/devcor_1_7_5.JPG)

As with most software patterns, dependency injection is only one solution to a problem. Granted, it is a pattern that is seeing a rise in adoption, since modern architectures such as microservices prefer highly reusable and loosely coupled code, though this does not mean it should always be used.

Consider if you should even use dependencies. Some projects have a very limited scope, and the introduction of dependencies and their injection can unnecessarily bloat the code base. Using dependencies for the sake of dependency injection is counterproductive.

Similarly, if a class is completely encapsulated within another class and only used by the outer class, it seldom makes sense to include the service class as a dependency, but rather to instantiate it.

Following are some general pros and cons of using a dependency injection pattern:

| Pros                                                         | Cons                                                                           |
|--------------------------------------------------------------|--------------------------------------------------------------------------------|
| Provides looser coupling between components.                 | Code is more difficult to trace and navigate.                                  |
| Allows the client to be configurable.                        | Increases complexity.                                                          |
| Removes the requirement for the knowledge of implementation. | Code is tightly coupled to the implementation of dependency injection pattern. |
| Makes client code easier to test by mocking dependencies.    | Requires more knowledge and time to implement.                                 |
| Creates reusable code.                                       | -                                                                              |

# 2 Designing for Serviceability

## 2.1 Introduction

Software departments write computer software. However, when an application is deployed to production, DevOps or Operations departments commonly share or take over servicing, monitoring, and supporting it. For this reason, applications should be serviceable by any person with enough training and not just the application developers.

Application serviceability is a trait that tells you how easy an application is to install, configure, monitor, and make sure it provides the service it was intended to. As with maintainability, serviceability must be considered in the design phase, since later changes can be very costly and difficult to implement.

## 2.2 Observability in Application Design

Software applications are becoming plentiful and more complex. Applications are no longer built from one or two services and installed on a single machine, but exist as a distributed system in a cloud. This change is great for the application and the users, but complicates the application’s serviceability. Increased complexity is one of the reasons why observability is becoming very important in applications.

Observability is the property of a system that tells you how well internal states of a system can be inferred from knowledge of its external outputs. In more practical terms, observability tells you what is happening inside the observed system by observing the outside of the system, without having to add new functionalities to do that. It should also help you determine why the application broke, not just that it did.

When observing an application for its operational stats, three questions can be asked:

* **What is the application doing:** The application itself can usually tell you the most about what is going on, since it knows the operational context. The process where an application notifies the observer about events, actions, and errors that happened during run time is called logging. It must be designed to clearly convey relevant information to the observer.

* **What are the supporting systems doing:** Supporting systems, which include the hardware on which the application is running and the internal and external systems that enable the operation of the application, can tell you a lot about the application’s operation. An application can run exactly as designed, but if it does not have enough memory to process everything in time, or if database queries take too long, it can become unusable. Monitoring is a popular technique to observe the supporting systems for operational parameters.

* **What are the users doing:** Users can sometimes surprise developers the most with their innovative ways to use applications in ways they were not designed. Even though some applications do not have any active users and are automated processes, one can still observe if they are doing what they are supposed to. Documentation is a great and simple tool to steer users into the right direction.

When building an application, observability must be taken into consideration both at a high and a low level. High-level observability is often included as a nonfunctional requirement of the system and is a part of the application design. Low-level observability is included in the implementation phase of the development.

Consider the following when designing:

* Status of application components.
* Health checks on external systems.
* Operational status of services.
* User triggered actions.
* Hardware status and load.

Consider the following when implementing:

* Programming language-specific logging.
* Function results.
* Transaction and query statuses.
* API responses.
* Event and state logging.

While designing the application, keep in mind that it probably will work incorrectly at some point in its lifecycle. Not necessarily because of a software-related bug—some software components can become unresponsive, an application can be misused, external systems (load balancer, application programming interface [API], and so on) can go offline, underlying hardware can encounter malfunctions, and so on. Therefore, you want to observe as many high-level operational parameters as you can (and as make sense), to help you build a bigger picture about what is going on with your application.

The implementation of the application has opportunities to increase observability too, although it is much more context dependent. Function inputs and outputs can be logged, as can (and should) various API calls and responses. This also applies to database transactions and queries, stream processing, state changes, user triggered actions, and more.

Each programming language also has features that impact observability. High-level programming languages, like Java and Python, often include features or libraries that allow you to collect run-time metrics about the application, such as metrics from Java Virtual Machine, or that relay events, transactions, and messages, that occur in the system via logging.

### Logging

Logging is one of the most useful tools for increasing observability of an application and should always be used unless there are reasons not to log information, namely privacy concerns and security issues.

Broadly, a log is a file that records events that occurred while executing software.

While most programming languages support information output in the form of **print()** or **log()** functions, this is often not enough for anything beyond the most basic applications.

That is where logging frameworks come in. They standardize and simplify the logging process, log storage, and analysis. Some frameworks (like log4j) are widely used in the industry and well documented.

Logging frameworks are usually straightforward to integrate into your code, and typically consist of a logger class with some parameters that is fed log messages or can intercept and format messages that are sent to the console.

![](/images/devcor/devcor_2_1_1.JPG)

Logging is commonly done on several logging levels. The logging levels convey the nature of the log message and dictate what gets written into a log file. Higher log levels (up to ALL) also contain all the lower log level messages.

Although each logging framework can have its own logging levels, the following levels are considered common:

* **OFF:** No data is logged.
* **FATAL:** Something catastrophic has happened and the application probably stopped working after a fatal error. Sometimes also called critical.
* **ERROR:** Used for logging errors that do not necessarily break the execution.
* **WARN:** Indicates that there might be a problem in the application.
* **INFO:** Conveys normal application behavior, state changes, and so on.
* **DEBUG:** Granular information about the execution that gives additional insight into what is happening.
* **TRACE:** Extremely detailed information on the flow of the system.
* **ALL:** Log everything, including any custom logging.

Not all logs are the same. Look at these example logs for the same sequence of events.

![](/images/devcor/devcor_2_1_2.JPG)

When logging information, consider that the person looking at the logs might not be you and might not know what exactly the application does in full detail. This is why you must supply all the information that could be helpful with the debugging or analysis. There is little benefit in the DevOps knowing an API endpoint is failing if there are 20 different endpoints for three different purposes, and little information in an error saying a request is malformed if the request is not printed.

Logs are commonly divided by functionality—for example, a web application can have separate logs for API calls, networking, database queries, and the server processes.

Present the information in a consistent and well-formatted way by using time stamps, object serialization, logging levels, and so on.

### Monitoring

Today, a lot of applications no longer reside on a single machine that contains all of the necessary components, but span over multiple clouds, shared infrastructure, and geographical locations. Even though they are built with high availability in mind, problems will arise, and when they do, it is important to find them quickly. Application monitoring is used to narrow down, isolate, or even predict these problems. It is a broad term that covers monitoring everything from low-level hardware parameters to high-level service states.

Application monitoring covers observation of a broader area:

* Server parameters (CPU, memory, temperature)
* Storage (read/write operations per second)
* Database (query speed, index speed)
* Connectivity (bandwidth, number of requests)
* Services (load balancer nodes, APIs, containers)
* Application components (message queues, middleware)
* Application flow (waiting time, error rates)

Many of these metrics are related and good knowledge of correlations can help you overcome potential problems.

A good example, which logging alone could not solve, would be a web application that stores some data that has been running well for the past 3 years, but in the last month, users have started complaining about the slow response time while performing actions.

A brief analysis of monitored parameters reveals that these actions cause short spikes in CPU usage. This data can then be correlated to a slightly slower query execution speed. A look at the connectivity shows no anomalies, but increased storage I/O operations per second (IOPS) catch your attention. You check the memory metrics and see that the database has outgrown its original server's memory and started using virtual memory, causing delays when paging the data to disk.

As with logging, the industry came up with many monitoring frameworks or Application Performance Management, from simple ones, which monitor a single parameter, to complex ones, that offer observability across entire application and infrastructure environment.

![](/images/devcor/devcor_2_1_3.JPG)

An example of one such application is Cisco AppDynamics. Applications like this use a central controller and many dedicated agents to collect the data. Individual agents are installed on application servers (for example, a Node.js agent is installed on a web server and a Database agent is installed on a Structured Query Language (SQL) server) which then sends data to the controller for aggregation and analysis. Application performance management comes with a wide variety of premade monitoring agents. However, they also allow you to create custom monitoring scripts or insert specific code into your application, allowing you to monitor additional metrics.

### Documentation

The last tool that improves application observability is documentation. Since applications and their code can be quite complex, it helps to have a high-level and a low-level overview of what the application does.

For a high-level overview, documentation is used. There are many types—technical documentation, design documentation, requirements documentation—and each one is intended for a specific target audience and goes into a different depth of explanation about the subject.

For developers, lower-level documentation is also very important, since it documents what the application code does. While a regular user would be happy with an explanation like "This application saves pictures to the cloud," a developer would not be and would be left to figure out where the application sends the pictures, which technology it uses to do so, and other important information.

Low-level documentation is commonly implemented as code comments and variable and function naming conventions, and all programming languages support them.

![](/images/devcor/devcor_2_1_4.JPG)

While it is often said that good code is self-documenting, one should not use this as an excuse to not comment code. A comment can make the difference between short bug troubleshooting and spending five hours searching for obscure side effect that should be mentioned in function description.

Good comments provide an overview of the code, tell you where and why the code is used in an application, what its quirks, possible bugs, and features are, and provide references to other material if there are external libraries or bugs. They should also pay attention to context and avoid stating the obvious. For example, a FOR loop with a comment of "Loops over array" is much less informative than a comment of "Iterates through all users".

## 2.3 Scalability in Application Design

Applications long resided on big mainframe computers using a monolithic structure, which meant that every application was self-sufficient and ran separately from any other application. While this approach was efficient then, scaling such applications for more users or bigger loads was quite expensive. Therefore, the evolution of applications went in another direction and today is more focused on distributed and scalable infrastructure.

Scalability is a property of a system that describes the system’s ability to handle larger and larger amounts of work. For applications, this can mean increasing the amount of users, increasing the throughput of processed data, improving geographic availability, or enhancing functionalities without disrupting existing activities.

For a web store that is selling computer components, scaling it could mean many things:

* Increasing the number of concurrent users that can access the web portal.
* Adding more servers, based on geographical location, to reduce latency.
* Balancing the load toward the back-end servers.
* Caching static content and queries and using content delivery networks.
* Refactoring your application so it has looser coupling.

Scalability improves handling larger amounts of work in multiple dimensions:

* **Functional scalability:** Do more without disrupting existing services.
* **Geographical scalability:** Stay effective while expanding geographically.
* **Load scalability:** Increase load on individual components.
* **Administrative scalability:** Support more users or tenants.
* **Generation scalability:** Be able to adapt to newer components.
* **Heterogenous scalability:** Be able to adapt to different vendors.

When talking about the broader direction you scale in, three options are available:

* **Vertical scalability:** Scaling vertically (or scaling up/down), means adding resources to a single node. In an application, this means adding more memory or storage to the server and increasing the number of CPUs. Vertical scaling is usually very simple to implement, since it does not require changing the underlying functionality of an application and usually just involves adding and configuring additional hardware. This presents a bottleneck down the road, since servers do not support unlimited expansions of their hardware. The price also becomes an issue, since high-performance component prices usually do not scale linearly with their performance. You end up paying more for those components than you would otherwise pay for that amount of processing power or storage. Vertical scaling is also more prone to system failures, since it relies on a single system to work.

* **Horizontal scalability:** This is an approach to scalability where the resources, available to a single node stay the same and the number of nodes changes. This can mean adding another web server to handle requests, distributing a database across a cluster, distributing processing across multiple nodes, load balancing the traffic to each node is equally under load, and much more. Horizontal scaling (also called scale in/out) is not usually something that can be done easily, and should be considered in the design phase, since it changes the environment in which the application can run. If implemented correctly, horizontal scaling does not have any theoretical limitations, since the work is distributed equally across the nodes and any increase in workload just adds additional nodes, whose cost increases relatively linearly with their number. Scaling horizontally also makes the application more resilient to system failures, since a live node can process any work that needs to be done by a failed node.

* **Hybrid scalability:** These two approaches can also be combined. Hybrid scaling is often implemented as an autoscaling solution that uses predictive and reactive scaling to optimize performance. Predictive scaling analyzes patterns to figure out the predicted resource usage for a time period and accordingly scales the resources horizontally. Since these predictions will never be 100 percent correct, vertical scalability is used reactively, when the current resources are insufficient to handle the predicted workload.

Scaling is often only thought about for increasing the amount of resources, but also works great for downscaling. Downscaling is the process of gradually reducing the number of available resources for a system. In applications, this usually means reducing the number of servers, databases, or containers and the resources available to them.

If your application is under a heavy load for 2-3 hours during peak business hours, it makes sense to scale out your application environment for that time and downscale it during off-peak hours. This scaling can save a lot of money. It also makes the resources available to other applications for that time. This type of downscaling is easier to do with horizontally scaled applications, since it involves temporarily deploying more or fewer servers, whereas downscaling vertically scaled applications is not a quick process, as it is not practical to swap out hardware several times per week.

Many cloud service providers, like Amazon Web Service, offer elastic scaling for deployments. Elasticity takes care of the need to cope with different loads from increased or reduced demand. When the application traffic or load on a specific resource is higher than usual, the environment can adapt by rapidly adding more resources, adding more load balancing, deploying more microservice instances, or something similar. The same logic applies to lower than usual traffic. When the load is low, the elasticity takes care of downscaling to keep the costs of the environment lower.

Cloud elasticity is popular with services that frequently experience increased traffic, such as a web store during holiday sales.

While designing a scalable application, the application’s architecture plays a major role.

Applications historically evolved on monolithic infrastructure running on big mainframe computers. They used tight coupling between application components, which means that components were highly dependent on each other—they had to be aware of each other, of what they did, and how they did it. Changing a single component could break all the other components in the application. While this kind of application was simple to deploy, since you deployed the whole codebase to a single server, it was not practical to make changes to or to scale---especially horizontally, since application state caused data locality problems.

With the evolution of distributed environments, cloud computing, and the rise of APIs, developers began decoupling their applications more. The idea was to create loosely coupled applications, where each component would have a single purpose or responsibility. These components were meant to be less dependent on each other, scalable, allow for individual changes and failures, and could change their underlying technology without affecting the application. The components could also be tested independently, a big perk for test-driven development.

However, to make applications like that work, the information flow through the application has to be stateless. Statelessness is a property of a system that states that it is designed to operate without any knowledge of previous actions or events. The thought was that statelessness was perfect for distributed scalable infrastructure, since information passing between components did not have to be routed to the correct nodes, but could be processed by any node of the suitable application tier. Statefulness, on the other hand, remembers previous session data and client info and uses that data the next time a client makes a request.

The combination of stateless, scalable applications, and distributed and cloud computing created microservices.

Applications usually run on dedicated infrastructure but having a separate physical node for each service can be too expensive, unmaintainable, or just unnecessary. Therefore, it is important for you to know their advantages, disadvantages, and scaling implications.


![](/images/devcor/devcor_2_3_2.png)

![](/images/devcor/devcor_2_3_3.png)

![](/images/devcor/devcor_2_3_4.png)

There are many approaches to infrastructure sharing in distributed systems, and some vendors use innovative approaches to this problem. However, three distinct architecture styles addressing infrastructure sharing are commonly used:

Share nothing architecture: The most basic architecture. Each service operates within its own node and each node has autonomy over a subset of data. It only uses processing power, memory, services, and storage dedicated to this application. This architecture is considered resilient, since errors and increased load in one application do not affect other applications. It is also the simplest (but not the most optimal or cheapest) architecture to scale, since scaling share nothing architecture usually means scaling the nodes up/out. This architecture also supports nondisruptive upgrades, since single nodes are taken down rather than the a fully shared infrastructure.
Shared disk/database architecture: This architecture differs from the previous one by using shared disks or databases. While share nothing architecture gives sole access to distinct data, shared disk/database architecture allows access to all disks or databases that are connected to any node. Since services are usually unaware of each other, this architecture creates a need for a distributed locking system to prevent concurrent writes on any disk. These locking systems are complex mechanisms that synchronize write operations, perform load balancing, and improve availability in case of node failures. They can become choke points for all of the services in the cluster if not designed properly or if under too heavy a load. The complexity of scaling shared disk/database infrastructure is greater than in share nothing architecture but can be cheaper and reduce the amount of data to be stored due to sharing.

Shared everything architecture: In addition to sharing the storage, share everything architecture shares the memory, processing power, and other services. While share nothing architecture focuses on performance, share everything architecture focuses on maximizing resource utilization. Shared infrastructure is susceptible to individual node components (storage IOPS, available memory) bottlenecking and hampering the performance of all the applications using the infrastructure. The more shared that the application infrastructure is, the more it benefits from stateless data flow.

Since every application is different, there is no universal solution to application scaling. It requires good knowledge about an application and good application observability.

Scalability must be considered when designing an application, because little can be done to scale the application without changing the code and redesigning the application.

So, how does one scale an application in practice?

One of the first things that can be done is increasing the number of CPU cores and amount of memory available to an application or migrating the data to a faster storage. This has its limits, since stronger servers or virtual machines (VMs) get expensive quickly and there is a hard limit as to how much hardware you can install somewhere.

Databases can also be optimized without changing the underlying code. Indexing your database or using horizontal scaling techniques, such as sharding, can speed up your database greatly.

To really enable your application for scaling, a redesign may be needed. This can be a complex task, especially if the application is in use and must be refactored incrementally.

It is a good idea to split your application into components (a model-view-controller pattern is a classic example) and, if viable, use containers and microservices for the individual components, since they use fewer resources than full-blown servers. These components should be stateless, if possible. Some processing can also be offloaded to the client side (for example, picture resizing before submitting them), but developers must be careful not to expose any sensitive data.

Traffic management with load balancing can also be used to scale individual component types and more evenly distribute the load between them.

Content caching is also useful, since content delivery networks (CDN) can store some data closer to network edges and minimize the time it takes to load it. Static or frequent HTTP responses can also be cached, as can some database queries.

## 2.4 High Availability and Resiliency

One of the problems developers face when developing an application is dealing with unexpected failures. There is only so much you can do with exception handling in your code to make sure it is operational most of the time. However, it is impossible to predict everything that can go wrong, especially in distributed environments where multiple services and servers work together.

What happens when a VM freezes? What happens when a link to your database becomes unavailable? What happens when there is a power outage in one of the data centers hosting your application?

Unplanned random occurrences can and will occur at some point, so it is important for your application to be able to adapt to those events as fast as possible.

More and more applications have a global audience, are operationally critical for other services, or have some other requirement for them to be available around the clock. From social media to web hosting platforms, electronic banking applications, and digital electrical grid infrastructure, all services strive to be available and operational as much of the time as possible. There are two approaches (that can also be combined) to ensure uninterrupted operation—high availability and resiliency.

* Availability of the system is expressed as a percentage of uptime in a given amount of time.

* High Availability is a quality of a system that assures it can operate for a desired amount of time.

* Service Level Agreements (SLAs) with customers often specify availability percentages as high as 99.9% ("Three Nines") or even 99.999% ("Five Nines").

| Availability % | Monthly Downtime | Yearly Downtime | Full Availability for 10 Systems |
|----------------|------------------|-----------------|----------------------------------|
| 90.0 %         | 72 hours         | 36.5 days       | 35 %                             |
| 99.9 %         | 43.8 minutes     | 8.76 hours      | 99 %                             |
| 99.99 %        | 25.9 seconds     | 5.26 minutes    | 99.99 %                          |

Every system needs daily, monthly, or yearly maintenance for updates, improvements, and bug fixes. This is considered a scheduled maintenance window and is sometimes included in the SLA as an exception, while other times it counts against the total systems availability.

In the table, you can see the implications that availability percentages have on downtime and availability.

A yearly availability of 90 percent gives you three days per month of downtime, which means a bit over two hours daily for maintenance, upgrades, and troubleshooting. This is suitable for services that, for example, do not operate at night and are geographically localized in a certain part of the world. While 90 percent availability may seem high at first, it implies that for a distributed architecture containing 10 systems with 90 percent availability, all of them will be up at the same time only 35 percent, or around 123 days, of the year. If the architecture contained 100 systems, the full availability would be only 0.003 percent.

Therefore, a much higher availability is usually specified. A "Three nines" availability regime improves on that greatly, with a monthly downtime of less than an hour. Under this regime, one hundred systems would be available at the same time more than 90 percent of the time. This is considered a good business practice and is plenty for regular businesses, but in huge multinational companies, every minute counts. If a company makes $500M every year, each hour of downtime can cost $60,000 or more in lost revenue.

Therefore, a lot of businesses strive for the holy grail of availability—the "Five nines." 99.999 percent availability allows for a downtime of less than one second per day and around five minutes per year. Even in a distributed system with over 10,000 systems, they will all be available simultaneously more than 90 percent of the time and, combined with other techniques of ensuring high availability and resiliency, provide a near-continuous availability.

There are even stricter availability regimes, but they are mostly used in special cases like military engineering and space industry.

So, how do you achieve high availability and resiliency?

![](/images/devcor/devcor_2_4_1.png)

High availability is a product of a good system design. There are three principles that help achieve high availability:

* **Elimination of single points of failure:** Data flow through an application should not rely on a singular component to do the job, since this means an error in that component will cause a failure of the whole system. Instead, redundant components should be introduced into the application design to take over work when one or more components of the same type become unavailable. This creates a need for stateless data flow through the application, since it is simpler to operate with stateless data when using multiple components rather than to keep track of various sessions and states.

* **Detection and handling of failures:** Each component should be able to partially diagnose itself and other related components in order to detect if that component has failed. Detection of failures should only be visible in the inner workings of the application and to the maintainers of the application. An end user’s experience should be seamless. Component failures are commonly detected by sending test requests and waiting for a valid response or by listening for heartbeat signals, periodic signals that indicate a piece of hardware or software is operational.

* **Reliable crossover mechanism:** When a component failure is detected and redundant components are available, data flow should be directed to the redundant component. This is done with a crossover (also called a failover or switchover). The crossover mechanism should be sturdy and reliable, since it becomes a single point of failure if it is unable to switch data flow to a redundant component. As you can see in the figure, component A1 tried to pass some data to component B1, which is not operational. Component A1 then tried to pass the data to B2, which succeeded, since B2 is operational.

![](/images/devcor/devcor_2_4_2.png)

Resiliency is a different concept. While high availability seeks to detect and replace points of failure with redundant components, resiliency tries to recover from temporary failures through error handling and error correction in code. It is cheaper to implement than high availability, since most changes are made in the code and no new equipment needs to be purchased. Good software design combines both approaches.

Several methods exist for making your code and application more resilient:

* **Retry loops:** You can solve some problems by retrying the operation that encountered the error. Example cases include packet loss, long response time or other network problems, and temporary internal server or database errors. Sometimes, like with an overloaded infrastructure, retrying might make the problem worse by creating more load for the infrastructure. For that reason, retries are usually performed with longer and longer delays between them. Stateful data can cause multiple issues with retry loops if not handled correctly: actions can be duplicated or more errors can be triggered. For that reason, the actions should be idempotent, meaning they can be applied multiple times without changing the result beyond the initial application (that is, deleting the A resource will only delete the A resource, even if executed 10 times).

* **Fallback mechanisms:** Fallback allows your code to use a predefined or default value if the application fails to retrieve an actual value from another component. Fallback values can be predefined (for example, setting a user interface skin on a web page where the list of skins could not be loaded) or recently cached (for example, some HTTP content that should be dynamically generated but for some reason was not). Using fallback to compensate for failures is not possible in every case, but mostly in those where the choice of fallback value does not carry business or other implications (imagine password validation falling back to "True" or an ATM machine having a default payout of $1000).

* **Implementing timeouts:** Timeouts are a simple and widely used mechanism to avoid requests that wait for a response forever. A timeout is a time period given for a request to receive a response before it is considered to have failed. Duplicate actions can also be prevented with timeouts, especially in distributed environments. For example, if a VM provisioning process takes three minutes to complete, a retry loop should timeout if the time period is greater than five minutes, if no other checks are in place.

* **Circuit breaking:** This is a design pattern intended to isolate smaller failures and minimize their effect, preventing cascading errors, which are errors that trigger other errors until the application breaks. The idea is to wrap some functional code in a circuit breaker and then execute it, skip it, or throw an error, depending on the state of the component. It can prevent errors by skipping or waiting for processes, avoid failures due to components being offline by not communicating with them, and much more. For example, if your application relies on a web service to provide some data, but this service is detected to be offline, it makes sense to "break the circuit." Instead of creating many requests that are guaranteed to timeout, these requests should be failed immediately until the service is back online or a set amount of time has passed. This prevents unnecessary processing of requests you know are going to fail and the keeps the request queue from becoming overloaded.

### High-Availability Deployment

High-availability applications are commonly deployed in high-availability clusters.

![](/images/devcor/devcor_2_4_3.png)

High-availability clusters are groups of application components, servers, and containers that support specific applications and provide continuous uptime by harnessing the power of redundancy. They are used for load balancing, crossover, and even backup purposes.

The components of a high-availability cluster share persistent storage, where the application stores stateful data so it can be restored later if there are failures. A private network to monitor the heartbeat and status of components is also commonly used.

Clusters support both horizontal and vertical scaling. For the former, more redundant components are added and for the latter, the compute resources of the components are increased.

High-availability infrastructure can be deployed on-premises, in one or more public clouds, or as a combination of those two—a hybrid deployment.

| Deployment    | Pros                                                         | Cons                                              |
|---------------|--------------------------------------------------------------|---------------------------------------------------|
| On-premises   | - Highly customizable - Data stays in your hands             | - High up-front cost - Scaling requires more work |
| Public clouds | - Little maintenance required - Nearly unlimited scalability | - Limited functionality                           |
| Hybrid        | - Best of both worlds - Cloud bursting                       | - Complex to set up and maintain                    |

## 2.5 Latency and Rate Limiting

High latency, low bandwidth, and packet loss are three common reasons for application misbehavior and must be considered when designing an application. They are frequently the reason an application feels slow and unresponsive and can cause bad user experiences. While networking technology has advanced in recent years, reducing latency and increasing bandwidth, applications require faster and faster response times and increasing amounts of data to operate properly.

Latency is a term not only used in IT. It depends on the system being observed and on the nature of observation.

To understand how high latency and low throughput affect application performance, it is important to first understand the terms in networking and application design:

* **Latency:** The time interval needed for a packet to travel from the sender to the receiver.

* **Round-trip time (RTT):** The time that is needed to travel from sender to receiver and back.

* **Bandwidth:** The maximum rate at which the information that can be transferred across a given communication path.

* **Throughput:** The actual rate at which the information is being transferred.

![](/images/devcor/devcor_2_5_1.png)

Latency is always limited downwards by the medium it uses. For Ethernet cables, the speed was limited to typical speeds of 50% – 99% of the speed of light. Today, with the increasing usage of cheap fiber optic cables, latency is limited by the speed of light traveling through the cables.

The speed will never equal to the theoretical speed limit of the medium. There is always overhead for data encapsulation, processing, transferal, and parsing on both ends. Therefore, you should strive to reduce the amount of overhead your data needs to travel to a server or clients. The theoretical speed of light is about 3.3 microseconds per kilometer, while the actual speed at which the data is transferred via fiber optics is about 5 microseconds per kilometer. So, when using a direct fiber optics cable, one can expect the RTT between two points on the opposite sides of the globe to be about one fifth of a second, or 200 milliseconds, since latency normally uses milliseconds).

In reality, this number is much higher. Cables are not set up in straight lines and there are many switches and routers to hop through. There is also the "Last Mile" problem, which states that the last part of the connection to the end user is usually the bottleneck of the network, because of cheap hardware, ISP throttling, or nonoptimal setups. Therefore, actual numbers are two or three times higher than the theoretical ones.

Another common problem that causes application misbehavior is packet loss.

![](/images/devcor/devcor_2_5_2.png)

Packet loss can be caused in several ways:

* **Hardware faults:** Old or malfunctioning hardware and bad wiring can cause packet loss that is hard to pinpoint.

* **Network congestion:** When a network link is overloaded and receives more packets that it can handle, it drops packets to continue operating.

* **Device misconfiguration:** Network devices can be misconfigured and relay data to the wrong network or drop incorrect packets.

* **Wireless signal:** Wireless networks usually operate with some degree of packet loss due to uncontrollable or unpredictable circumstances such as interference from other networks or thick walls.

* **DDoS and other attacks:** Denial of service and similar attacks can cause packet loss because they cause enormous stress to the network devices.

* **Bugs in software:** Bugs in applications can also cause packet loss by ending sessions too early, using outdated libraries or network device firmware, and via other bugs.

Packet loss affects different applications differently. Apps that primarily use TCP protocol for transferring data experience packet loss as slower response times, since TCP will make sure that all the lost packages get re-sent.

UDP-based applications (like streaming services and IP telephony) experience packet loss as a lower quality of service (lower resolution, choppy voice, and so on).

High latency, low bandwidth, and packet loss all cause a similar issue—the network throughput drops and affects application performance.

Low throughput can cause multiple problems:

* **Slower loading of resources and web pages:** The lower the throughput, the slower the loading speed of any resource over a network. While larger files are expected to take some time to load, this issue becomes much more apparent with smaller files like web pages, where a combination of high latency and distributed resources, like advertisements, causes simple web pages to load noticeably slower.

* **Slower authentication and session negotiation:** Authentication protocols and session negotiation commonly use a multistep handshake. If packets are dropped in between, some steps must be retransmitted or the whole process must be redone.

* **Noticeable lag in video games and GUI applications:** Low bandwidth can cause visible delay when performing actions in multiplayer video games, where up-to-date information is crucial. Applications using a GUI also suffer from this problem (for example, mouse cursor lagging behind).

* **Service timeouts and interruptions:** Applications normally use timeouts for various actions. Low throughput can delay actions long enough that a timeout is reached. This can cause loss of state or data on client side.

* **Reduced video and audio quality:** Multimedia and streaming applications commonly work on the best-effort principle, which means they can operate with some packet losses. If too many packets are dropped before they reach the client, the quality of the media suffers.

* **Increased hardware load due to packet retransmission:** Every time a packet is retransmitted, it must be processed by the source system. If many of them are dropped in a short time, they can place a considerable load on the system.

* **Reduced efficiency of time-sensitive applications:** Some applications, like automated stock trading apps, rely on and make predictions based on real-time data. Unpunctual data can reduce profitability or even cause monetary losses for such applications.

* **A bad user experience:** Perhaps the most important problem of all—a bad user experience. Multiple surveys show that website performance issues are a deterrent for most of the users to visit that site again. Another indicator of a bad user experience is a high bounce rate—the rate at which the users "bounce" away from your website immediately after loading it and viewing only a single page.

High latency and low bandwidth can be somewhat alleviated by a proper design and implementation.

![](/images/devcor/devcor_2_5_3.png)

Caching is commonly done via Content Delivery Networks (CDNs). CDNs are geographically distributed servers that replicate data from the origin server to the edge servers. Client requests are then served by their closest server.

It is not necessary for all the data to be cached. Sometimes only static content is cached (media, HTML pages) while dynamic content is retrieved from the origin server. Other times, whole websites and applications are cached on CDNs, with dynamic content generated on the spot by those servers.

Caching content reduces latency caused by long transport routes and obstacles on the way.

Resource loading can be optimized in different ways:

* **Pagination:** A technique commonly used by APIs that splits the resource into more manageable chunks. For example, if an API request returns 12000 rows, it makes sense to split them into responses with 100 rows each.

* **Lazy loading:** Resources are loaded only when needed and not when their parent resource is initialized.

![](/images/devcor/devcor_2_5_4.png)

![](/images/devcor/devcor_2_5_5.png)

These two examples display how a resource can be loaded either at the point where it is needed with lazy loading (in the **doSomething** method) or at the initialization of the class with eager loading (in the **__init__** method).

The principle of lazy loading can be used often—from loading class dependencies to loading web page content—as it contributes to the efficiency of the application. It is ideal in cases where network content must be accessed and loading times must be kept to a minimum.

Parallel processing is a good improvement over sequential processing. It allows for processing of multiple tasks at once, reducing the processing latency. Tasks can be divided between:

* Processes (multiprocessing)
* Processor threads (multithreading)

![](/images/devcor/devcor_2_5_6.png)

![](/images/devcor/devcor_2_5_7.png)

While most systems benefit from parallel processing, it is especially beneficial to systems that process many small tasks. Application servers, even in distributed environments, sometimes must handle thousands (if not more) requests per hour. If those requests were processed one by one, as with traditional sequential processing, the delay would be considerable and noticeable to clients.

Events that require processing are commonly caught with event loops—pieces of software that wait for and dispatch events inside a program. These loops then use message queues to delegate the tasks to suitable processes or threads.

An overloaded system will work slowly or stop working altogether. Rate limiting is a tool to control the rate at which the data is passing through the system and prevent overloading individual components.

![](/images/devcor/devcor_2_5_8.png)

Applications can rate limit many things:

* **Number of requests:** Limiting the number of requests can mean limiting requests made against an API endpoint and returning HTTP code 429—Too Many Requests when the limit is exceeded, limiting requests against a resource (for example, limiting how many virtual servers provisioning requests can be made), and much more.

* **User actions:** Some user triggered actions can be sensitive, costly, or both and need to be limited. For example, users are usually limited in how many times they can try to enter their password when authenticating. Some actions are limited to prevent their abuse, especially for destructive actions like resource deletion and for actions with real-world consequences, like financial transactions and purchases.

* **Server traffic:** Traffic limiting is a popular technique to prevent denial of service attacks, where the total amount of traffic toward the servers is limited. More commonly, traffic is temporarily limited from the geographical area that the attack is coming from.

* **Concurrency:** The number of concurrent processes can be limited to prevent the processors from being overloaded and to reduce the wait time for tasks to be allocated and processed.

Exponential backoff is a technique used to determine the rate of certain actions. It multiplicatively decreases the rate until an acceptable rate is found or the action is completed. The reason for that is that some actions success is less likely the longer they are retried.

For example, your application is monitoring the status of an external service that goes offline. Instead of querying the service for heartbeat every second, you can use exponential backoff to limit the rate at which the service is probed:

* 1st try = 1 second + random millisecond interval
* 2nd try = 2 seconds + random millisecond interval
* 3rd try = 4 seconds + random millisecond interval
* 4th try = 8 seconds + random millisecond interval

A token bucket can also be used to limit the rate of actions. A token bucket is a software concept that uses the analogy of a self-replenishing bucket that is filled with tokens and delegates these tokens to individual tasks.

Buckets commonly use a set rate at which they fill up, and are therefore excellent at preventing unintended spikes from causing havoc in your systems.

When using rate limiting, it is a good idea to include an override option (for example, using a specific user, endpoint, server...), to prevent important traffic or requests from becoming locked out of the system.

## 2.6 Architectural Patterns

Software architecture is the fundamental structure of a software system. Each architecture is composed of multiple software elements or services and their interconnections. The architecture defines the fundamental structural choices about the system that helps you solve the problems at hand. This structure is, like real-world architecture, difficult and costly to change later, when anything has been built on those decisions. Multiple architectural patterns have emerged in the software world, commonly stemming from use cases or available infrastructure.

Software architecture carries decisions that have heavy implications on further decisions and the development of an application or a software system.

The architecture itself has a wide scope and must cover many aspects:

* **High-level abstraction of a software system:** Tells us which architecture pattern will be used, how the general flow of information through the application will look, and what its general use is.

* **Important components and their impact on a system:** Any components that are imperative for the systems operation must be considered in architecture. While applications can work without some components (for example, if a logging server goes offline or has its disks full, the application should still work), some are critical for its operation (for example, a database containing all the users that need to log in or a messaging queue that delegates tasks).

* **Components that are hard to change or replace:** Some components are extremely hard to change or replace once integrated into the system. Architecture should put a lot of thought into decisions that must be done right the first time.

* **Technical characteristics and properties of a system:** Different architectures and patterns have different technical qualities, like security, scalability, and others. Architecture should evaluate and be aware of the pros and cons that each decision brings.

* **Business risks that certain decisions bring:** Software architecture must adapt to business requirements and vice versa, to a lesser degree. Some of those requirements have certain implications and risks: for example, the inability to adapt to new vendors or a requirement for "five nines"—99,999 percent availability.

* **Relations and connections between components:** While components play a major role in an architecture, their interconnections also play a big part, especially in newer architectural patterns, which sometimes rely on the web to operate. Relations between components are important too (for example, loose coupling for improved scalability).

When talking about architecture, it is important to know that architecture is not the same as infrastructure. While infrastructure describes the set of components that make up a system, architecture describes the design of and interactions between those components. Applications are built on infrastructure that uses a specific architecture pattern.

Architecture is also not the same as design! While architecture tries to convert some software characteristics and requirements, like observability and scalability, into an actual solution, design focuses on low-level implementation such as classes, specific vendors and libraries, dependencies, design patterns, and other components.

Remember, as with design, there is usually more than one way to do things and alternate options should always at least be looked at.

The most basic, or earliest, architectural pattern is a monolith.

### Monolithic Architecture

![](/images/devcor/devcor_2_6_1.png)

Monolithic architecture is a single-tier architecture. This means that it consists of a single application layer that contains the user interface, business logic, and the data interface all in one. While the database itself may reside on another physical system, the logic to access and modify it is encapsulated in the data interface on the monolith.

This architecture pattern is considered self-contained and self-sufficient, meaning it contains all the components that are required to operate it and has no external dependencies. The underlying infrastructure was historically a mainframe computer—large and powerful computers with high reliability, security, hot-swapping features, and a great amount processing power. Today, a more powerful server (or more of them) can fulfill these roles. Monolithic applications are not limited to run on a single server and can run in a distributed environment as distributed monoliths, but this approach is considered the worst of both worlds, since you end up with tightly coupled complex distributed system, which goes against the core ideas of both patterns. For example, a distributed monolithic web store needs to keep track of which users belong to which instance and always point them there, since their session is only saved on a specific instance.

Up to a point, monolithic architecture is easy to scale. Architecture tends to be capped and vertical scalability stops there. Monolithic architecture does not rule out horizontal scalability by having three identical monolithic applications running behind a load balancer, if the design of the application is suitable. The problem is that individual components cannot be scaled, just the whole monolith.

Applications that use a monolithic architecture are commonly developed in a single programming stack and deployed as a single file or a directory, such as a NodeJS directory or a single Java .war file. This makes the initial development of an application quite simple, since all the development is done within a single environment using a common Integrated Development Environment (IDE). In comparison, distributed architecture can require a specific environment and IDE for each individual component. A single programming stack also means that you are stuck with it. Since the whole application is one single piece, you are limited to using components that support that stack. For example, choosing Java for your development means only using components that support running on Java Virtual Machine (JVM).

Note that for applications, the focus is on enterprise and business-enabling applications and not on simple, consumer-focused applications like a calculator or a media player.

The problems with monolithic applications become more apparent later in the application’s lifecycle. Deploying these applications, while simple, can become cumbersome. Every change requires a complete redeploy of the application, a tiresome process while developing and maintaining the application. A singular code base means that all the developers work on a single piece of code that can only be tested as a whole and not as individual components. Continuous deployment is difficult if the application is very large and slow. Change requests can be very complex due to the tight coupling of the components. This situation can lead to the application becoming a "big ball of mud"—something that nobody understands in its entirety and nobody wants to touch or maintain, because every change or a bug fix is prone to break something else.

Purely monolithic applications and architecture are becoming rare today since they do not support changes or scaling very well. Distributed architecture has also evolved greatly in the last several years. Monoliths are still used for various e-commerce applications, private data silos, and for simple applications that reside client-side, like Microsoft Word or Mozilla Firefox.

### Service-Oriented Architecture

![](/images/devcor/devcor_2_6_2.png)

Service-Oriented Architecture (SOA) is an architectural style where services are provided to other components of the application through a shared communication channel. Its evolution started over a decade ago, when enterprise applications became more interconnected and sharing parts of infrastructure and services became preferable. Today, a similar style, called microservices, is frequently chosen over SOA, due to a higher degree of independence of contained services.

SOA uses four different service types—business, enterprise, application, and infrastructure.

Although there are no industry standards for what a SOA service should look like, some general principles should be applied when designing them:

* **Standardized service contract:** Service should adhere to the service description, commonly a Web-Service Description Language (WSDL) document or XML schema.

* **Loose coupling and statelessness:** Like microservices, services should have minimal dependencies on each other and use stateless data.

* **Reusability:** Logic should be broken down such that it can be reused if needed.

* **Abstraction:** The internal processes of the services should be hidden from outside world. A service should act as a black box.

* **Autonomy:** Service should have the control over the functionality it encapsulates.

* **Discoverability:** Services include some metadata with which they can be found and interpreted.

Communication between services is done over an Enterprise Service Bus (ESB). An ESB is a centralized middleware tool that is used to connect services that often have incompatible interfaces and manage communication between services. Services that talk to ESB use canonical contracts to communicate with each other. Since each service in SOA uses different underlying systems, different protocols, and different technologies, the data model must be as simple as possible and be a superset of all the data the services need—a canonical data model. XML is commonly used for the representation of this model, since SOA often connects services that use XML-based Simple Object Access Protocol (SOAP) and WSDL for describing their services. ESB implementation is not standardized and can be done with a variety of messaging queues. Each ESB can commonly handle messages from many different transportation methods like HTTP, Business Process Execution Language (BPEL), Secure FTP, and others. While an ESB is a great and easily scalable tool that lowers the system’s complexity, it can cause a bottleneck in communication and become the single point of failure that brings down all communication.

Services can be deployed on one server or on several. They can also be deployed as a whole or service-by-service. It depends on how composite the application is and if there are any systemic changes to the system or the ESB. Generally, the deployment of SOA architecture is less flexible than the deployment of microservices.

Choosing SOA as your architecture is a good pick if you are building an enterprise application that must integrate many complex services and applications (with some even existing outside of your domain). Microservices should be chosen over SOA for simpler or smaller web applications that need on-demand scalability and flexibility and benefit from continuous deployment and integration.

### Microservices

![](/images/devcor/devcor_2_6_3.png)

Microservices, in contrast to monoliths, do not form a single functional tier, but rather two, three, or more tiers. These tiers form an application that is a set of loosely coupled (micro)services that constantly interact with each other.

Each microservice provides a functionality that is separate and independent from other services. The implementation of components inside a microservice is hidden to others, so the dependency between services is very minimal. To consume a microservice, only the input and output format are needed, and possibly the place of the microservice in the application chain.

These services can be developed, tested, and maintained very rapidly, since each one can be worked on independently. Certain microservices with a general use, such as a database reader or a message queue, can frequently be reused in other applications. This allows development teams to produce code that is useable by the whole business unit. The ownership of specific microservices becomes more clearly defined, with certain business units owning their own microservices, as opposed to the IT department owning the entire monolith. Continuous deployment, continuous delivery, and microservices go hand in hand, enabling the development team to rapidly push out new functionalities and the customer to provide relevant feedback sooner.

Microservices are smaller than regular services and have a limited scope. Because of their size, they are easier to understand and to work on, even later in their lifecycle. Their maintenance also does not require the knowledge of the whole system, but only of the specific domain the microservice deals with. Microservices can easily be scaled individually and on demand. Where a monolithic application might struggle to handle a daily traffic spike, a microservices-based application can scale out the components under the largest load and scale in when the spike has passed. Individual components can be refactored independently and have their underlying technology replaced easily. Fault isolation becomes easier since errors will mostly affect a single microservice.

All the benefits aside, microservice architecture greatly increases the complexity of an application. The infrastructure is now a distributed system, which has its own problems. Partial failure becomes relevant and must be handled, redundant components are recommended, end-to-end (E2E) tests become harder to do, and problems can occur in the layer connecting the microservices. The amount of hardware needed to set up a microservices environment also increases since each additional microservice adds its own overhead.

Microservices applications are a common sight today with the wide variety of cloud providers, evolution of containers and cloud bursting technology. Microservices commonly run inside small virtualized machines or containers (for example, Docker, Kubernetes, and Hyper-V) and use HTTP APIs to communicate.

Service-oriented architecture is very similar to microservices. Both architecture styles feature services as their main components, but they differ in their service characteristics. While microservices define two loosely defined types of services (functional, which support business operations, and infrastructure, which support infrastructure), SOA uses four different service types—business, enterprise, application and infrastructure services. This is not to say that the only difference between microservices and SOA is in the number of service types they define. In general, SOA services are less granular. With microservices, a service is a single-purpose unit that focuses on doing one thing, whereas with SOA, services are much bigger and can sometimes be represented by whole subsystems.

### From a Monolith to Microservices

Breaking down monoliths into microservices can be a complex task and should be done piece by piece.

For this example, imagine that you have a monolithic web-store application that sells sports equipment.

This is a diagram of the architecture of the application. It contains a user-facing website that customers can interact with and an SQL database containing items and orders. It also includes a shopping cart functionality and software capable of issuing orders and processing payments.

![](/images/devcor/devcor_2_6_4.png)

As the business that handles the web store expands, a need arises for another service to have access to the "Items" database. Since the application is tightly coupled, using the existing component to give access to another service is impossible. Having another identical database is viable, but not practical due to the increased cost and the need to set up the data access layer again. The best solution would be to decouple the “Items" component from the monolith.

![](/images/devcor/devcor_2_6_5.png)

The result is an independent "Items" service that can serve the information to other external services not related to this application. This is not yet a true microservice, since it uses a database that is shared with the monolith. An error within the database could take down the whole system.

The business started using an external online payment provider, so the in-house "Payment" component must be replaced with an external one, which is already designed to function as a microservice.

There is also talk of a possible expansion into the market of nutrition supplements and the business strategist wants to have a system in place to receive orders for those types of items.

![](/images/devcor/devcor_2_6_6.png)

Therefore, you continue decoupling components from the monolithic application. "Order" and "Payment" components are decoupled. An external service is used for payments and an "Order" microservice is created with its own database (NoSQL database, since data is semistructured and is bound to change and expand).

When decoupling the components, it is important to use data that is as stateless as possible. Some microservices, like the "Items" component in this example can be stateless, since they treat each request as a standalone unit, without remembering context or state, and return the queried list of items to different clients. Other microservices cannot be stateless. The "Payment" microservice must be stateful, since most of the payment systems use Online Transaction Processing (OLTP), which requires stateful communication with several steps.

Since only the "Cart" component and User Interface are tightly coupled now, it makes sense to create a "Cart" microservice too. The amount of API calls for this application has increased considerably, so it makes sense to use an API gateway for efficient and secure API request handling.

![](/images/devcor/devcor_2_6_7.png)

The final result is an application based on microservices. Each component can be developed and scaled out independently and used outside of this specific application. The application is more complex and the cost of maintenance is higher than before, but the application's architecture allows for cheap and simple changes to individual microservices in the future, where the faults of monolithic architecture become apparent.

### Event-Driven Architecture

Event-driven architecture focuses on creating, consuming, and reacting to events.

![](/images/devcor/devcor_2_6_8.png)

An event is defined as a significant state change (for example, an order in web-store is created, a session is established, a user changes their password...) and is created by an event creator. The event creator only needs to know that something has happened, not what to do with that information, since the event is then passed onward over an event channel that could be almost anything—a TCP/IP connection, API call, an input file, and so on.

The event is then propagated to one or more event consumers, who react according to their business logic. A single event can trigger multiple reactions from consumers or even a cascade of events.

Between the consumer and provider sits middleware that takes charge of the event distribution. While events can be propagated directly to services, they are usually not, since the number of events, and their corresponding processing on every node, would get out of hand quickly. Therefore, two different topologies are commonly used for this middleware:

* **Event Broker:** In this topology, events arrive at a broker or distributed brokers, are placed in a queue, and are dispatched to corresponding event processors.

* **Event Mediator:** Here, events require coordination to be processed (i.e. order validation, additional authentication...) by a central mediator component.

Due to the distributed nature of event processors and dedicated event channels, event-driven architecture is highly scalable. However, it is quite complex to implement and debug. Since events are asynchronous and processed remotely, you must address certain issues like the lack of processor availability or errors in the event broker/mediator. The event channel can cause problems too, as some messages can get lost or corrupted.

* **Message Queues**

Message queues are a useful tool for decoupling components.

![](/images/devcor/devcor_2_6_9.png)

Enterprise service buses, event brokers, and event mediators all use tools for asynchronous communication within the same system or a process.

In IT, the concept of a message queue is used for this purpose. It coordinates the exchanging of messages between different services without blocking the processing of information on the services themselves.

For example, imagine you have a web service that is used to book tickets for concerts. It consists of a user interface and a back-end system for reservation processing. The process of booking a ticket through the user interface is much less demanding on the server than the processing of the booking in the back-end. This situation means multiple requests can be created while the first one is still processing in the back-end. The web service is under a big load at certain times of the month when new tickets are added, but has little traffic otherwise. Here is how the communication would look with different architectures:

* If the web service used a monolithic structure and ran on a single server, an increased load on the user interface would cause the whole application to become unresponsive, due to certain components being overwhelmed. It does not matter which component, since the resources are shared.

* If the same application ran as two separate services that communicated over an API, the unresponsiveness could still happen if the back-end system failed to process the requests in time. The application would freeze and wait for the response from the back-end system while the requests from the user interface would keep arriving. Even worse, some requests could get lost in between.

* The best case would be to fully decouple those two components by placing a message queue service in between. It would wait for messages coming from the user interface and delegate them to the back-end system (or more of them if you want to scale) when the back-end system is free to process the next request. This way, all the requests get handled and the web service stays operational even during the traffic spikes.

The message within an message queue is forwarded to and processed by exactly one event processor. Which processor that is does not matter, since message queues work with stateless data.

Message queues can also feature persistent storage, so the messages will stay in the queue even if one of the services fails. In addition, message queue administrators can set security policies on messages, set a Time to Live (TTL), configure load balancing and other message distribution options, set delivery confirmation policies, resend policies, and more.

Another concept like message queues is the publish-subscribe pattern (pub-sub). Instead of sending the messages directly to the receiving services, pub-sub uses topics to broadcast messages. Topics are subjects under which messages are aggregated. Subscribers (message consumers) subscribe to any number of the topics a pub-sub provides. When a new message is pushed to the topic by a publisher (message creator), it is broadcast to all the subscribers of that topic.

## 2.7 Sequence Diagrams

Software developers must model complex interactions in their applications, full of dependencies, decisions, and conditions. These models then must be shared with and presented to other people. The models can be described on paper as a list of steps and conditions. However, this list can become nontransparent very quickly. Humans are great at perceiving visualized information. Therefore, a visual representation of interaction models is needed—sequence diagrams.

Sequence diagrams, sometimes called UML diagrams, are diagrams that depict interactions between different entities in a time-ordered fashion. They are used to model and visualize the flow of information through your system, allowing you to troubleshoot and document your logic. Sequence diagrams are used both in design and analysis and are one of the most popular techniques for dynamic modeling (creating models that change over time).

Common uses for sequence diagrams include:

* Usage scenarios (descriptions of potential ways that the system can be used)

* Logic of services (high-level logic of what the service does when invoked by various external actors)

* Logic of methods (particularly complex logic of some operations and functions)

Sequence diagrams are based on Unified Modeling Language (UML), a modeling language used in the field of software engineering to visualize the design of a system. In addition to sequence diagrams, UML supports a wide variety of diagrams and offers solutions for visualization of many software-related concepts—classes, components, objects, deployments, packages, profiles, activities, states, timing, use cases, and more. The sequence diagram is one of the most used visualization tools, along the class diagram.

![](/images/devcor/devcor_2_7_1.png)

This is a very basic example of a model that displays the interactions between a restaurant customer, waiter, and a cook.

What are sequence diagrams comprised from?

![](/images/devcor/devcor_2_7_2.png)

One of the main components of these diagrams are participants. Participants are the individual components that interact with others within the model. Participants use the notation of “ObjectName:className" as you can see in the example of "P1:Participant," where P1 is the instance object name and Participant is the class name. If the instance name of the class is irrelevant, it can be skipped and that participant is displayed simply as ":Participant."

Specialized roles of participants exist, such as actors. These actors are not actual performance artists, or even humans necessarily, but entities outside the scope of the system you are modeling that interact directly with it.

Commonly, the actor is the user or users of the application. However, an actor can also be, for example, a shop customer, bank account holder, external API service, and much more.

Other participant roles include boundary, control, and entity. These will be looked at later.

Each participant has its own lifeline. A lifeline indicates the participant's presence and activity over time. They can become activated, displaying that they are busy executing a task or waiting for a message. A narrow rectangle denotes an activated lifeline, as you can see in the example of an Actor's lifeline after sending a Request and before receiving a Response. When a participant is no longer busy, its lifeline becomes deactivated.

Additional information can be put into diagram notes.

Interactions between participants are conveyed by messages, depicted by arrows between participant's lifelines.

![](/images/devcor/devcor_2_7_3.png)

Each message arrow has its own meaning and use case:

* Synchronous message: A synchronous call to another participant. The sender first sends a message. After that, it is blocked from any other operations until it receives a response. For example, an application gives the user a prompt with a choice.

* Return message: A message corresponding to a previous synchronous message. Dashed lines generally represent reply messages.

* Asynchronous message: An asynchronous call to another participant. After the message is sent, the sender does not wait for a response, such as when an application sends a "Welcome" message.

* Lost message: A message where the sending event has a known sender, but the receiving event is outside of the scope of the system, like sending a push notification to a user.

* Found message: A message where the sending event is unknown or outside of the system, but the receiving event is known. For example, a user contacts support system via email.

* Create message: Create message is used to create a new lifeline instance.

* Delete message: Delete message is sent to terminate another lifeline.

* Self-Message: Also called a reflexive message, self-message is used to depict the participant interacting with itself.

You sometimes see differences in arrow drawings when using different UML diagram tools, but their meaning should always be the same.

Many times in an application flow, certain conditions must be met, or some actions have to be repeated.

Grouping operators are used to create combined fragments—logical groupings that contain conditional structure, represented by a rectangle in the sequence diagram. Each combined fragment is composed from one or more interactions and interaction operator.

The following is an example of the most commonly used combined fragment types.

![](/images/devcor/devcor_2_7_4.png)

![](/images/devcor/devcor_2_7_5.png)

The **"opt"** operator works as a simple IF statement.

The **"alt"** operator is used for IF...ELSE..THEN statements and can have as many conditions as are needed.

Loops are created with the **"loop"** operator. To break the loop prematurely, the **"break"** operator can be used. Break is also used to handle errors in the application flow.

Finally, the **"par"** operator depicts parallelism. All the fragments inside this combined fragment are executed in parallel.

A good practice is to use an Entity-Control-Boundary (ECB) pattern, which is a variation of the Model-View-Controller (MVC) pattern that also encapsulates some business logic.

![](/images/devcor/devcor_2_7_6.png)

An Entity is an object that represents some meaningful chunk of system data. It correlates to an MVC model, such as a Customer entity.

Control objects represent MVC controllers and manage the flow of interactions between boundaries and entities. They manage the processing and execution of the commands coming from a system boundary.

Boundaries represent MVC views and are responsible for interaction with external actors, such as a form on the user interface.

To increase the robustness of the system, the following constraints should be applied:

* Actor may only see and communicate with boundaries

* Boundaries may only communicate with actors and control objects only

* Controls can communicate with boundaries, entities, and other controls

* Entities can know about other entities and can communicate with controls.

Applications that use such designs have increased adherence to the separation of concerns principle, which improves scalability and serviceability of the application.

This will not be possible in some, or even most, of the cases, due to incompatible design, simplicity, multiple responsibilities, and so on. Basic participant notations are used there instead.

### Sequence Diagram Example

This simplified diagram displays the sequence of events for the validation of a PIN on an ATM machine.

Observe how different elements and message types are used for interactions between different participants and actors.

![](/images/devcor/devcor_2_7_7.png)

First a card is inserted into the card reader. If the sequence diagram was only about validating the card, not the PIN, the insertion of a card could be marked as a found event.

This insertion triggers the activation of the ATM lifeline, as the card reader sends the ATM the message that a card is inserted. ATM then requests the PIN from the card reader, which activates a nested timeline that waits for the PIN input from the actor. When the PIN has been input and forwarded to the ATM system, the nested timeline deactivates. The ATM sends the PIN onwards to the BankSystem, where the valid PIN resides, for comparison to the valid PIN.

The alternative operator then handles the response from the BankSystem. If the PIN is valid, the ATM sends an authorization message to the card reader, which then displays the transactions menu. If the PIN is incorrect, the CardReader asynchronously ejects the card.


# 3 Implementing ChatOps Application

## 3.1 Introduction

Developers have always been able to find new ways of using their existing applications and protocols. The same applies to messaging applications, and Cisco Webex Teams is one of them. It offers text, audio, and video communication between users. By opening their application programming interfaces (APIs) to the public, they enabled endless possibilities of chat integration.

When starting a project and using brand-new APIs, simply glancing at documentation, writing code, and hoping everything works is very time-consuming and, simply put, bad practice.

Before you start designing your application, you will have to read some documentation about Cisco Webex Teams Representation State Transfer (REST) API. You will also need to learn how to use Unified Modeling Language (UML) to create a sequence diagram. Sequence diagrams will help you achieve your goals and integrate ChatOps faster and with greater confidence and understanding.

## 3.2 Introducing ChatOps

Developers, operators, and systems and network administrators all use some form of digital communication on a daily basis to convey information between them.

Instant Messaging (IM) and Real-Time Chat applications are not some hot new technologies that have just been invented. The thing that is different about messaging applications now is how you use them.

An explosion of API availability throughout the industry allows countless options for integrating products and services into your existing infrastructure. From routers and switches to web applications, online search services, and storage providers—they are all available with their programmability exposed and well documented.

IM and real-time chat services available today allow programmatic access to their ecosystems and enable users to integrate those services into existing infrastructure through APIs. This opens endless possibilities for how a messaging application is used. A chat workspace quickly becomes a centralized hub for conversation, collaboration, and operations—ChatOps.

Integrating existing infrastructure APIs into chat workspaces transforms them into centralized hub for:

* Conversation
* Collaboration
* Operations

ChatOps is a way of interacting with infrastructure through conversation by using real-time chat and chatbots. Chatbots can be considered a part of the team and can use your tools and services to run automated tasks on your infrastructure.

A chatbot is just like a normal user that joins a group conversation and can access messages passed between other group members—real group members. A chatbot is always on and always participates in the conversations, but it is up to you to extend chatbot program logic.

There are many uses cases for chatbots, but a common use is to integrate them into an existing support channel. Many companies use chatbots as a first line of support to help their customers satisfy their needs without ever interacting with a real support agent.

Chatbots can learn, evolve, and expand their knowledge base by using Machine Learning (ML) algorithms and Artificial Intelligence (AI).

**Note:** The word "bot" is short for robot.

Chatbots respond to questions, but they can be programmed to do more complex things, like executing an existing automated workflow. They do this through conversation—they can chat with users about current operations in progress and their status.

![](/images/devcor/devcor_3_2_1.png)

Chatbot capabilities and limitations are defined by chatbot service users and chatbot service providers, which are not necessary messaging service providers themselves. There are third-party chatbot service providers that offer chatbot services for many messaging platforms. If your development, support, or sales presence spans over multiple platforms, third-party chatbot providers enable you to make a single API call and send that message to all their supported platforms.

Messaging platform providers that offer chatbot APIs can differentiate between real user and a chatbot, because a real user must register an account and enable chatbot functionality using their account credentials. After enabling a chatbot service, the messaging provider generates an API access token that is assigned to that service instance, along with permissions that govern what information is accessible to it.

Chatbot service providers can automatically supply chatbots with access to some of the following information:

* Member activity
* Channel information
* Chatbot information
* Metadata
* Statistics

![](/images/devcor/devcor_3_2_2.png)

## ChatOps Adoption

Some teams may not be using the same messaging platform as the others, or even not using any form of IM. Adopting ChatOps gives teams high level of workflow transparency. A chat is no longer just a conversation between members and teams, but a workspace where infrastructure is managed.

Here are the benefits of ChatOps adoption:

* Centralized infrastructure management
* Conversational approach to operations
* Tighter collaboration
* Transparent workflow

Even if different teams are using different messaging platforms, there are always chatbot frameworks that allow you connect teams and chatbots together and have one common place for collaborated operations.

Not every chatbot must be designed from scratch. There are chatbot frameworks for different programming languages available and there are online services that offer chatbot integration for many messaging platforms.

Observe the following benefits, frameworks, and chatbot APIs:

Benefits:

* Conversation
* Centralization
* Collaboration
* Transparency

Frameworks:

* Botkit
* Errbot
* Hubot
* Lita
* Wobot
* Robot

Chatbot API:

* Cisco Webex Teams
* Microsoft Teams
* Google Hangouts
* Slack
* Hipchat

## 3.3 ChatOps with Cisco Webex Teams

Chatbots can be used as notifiers, controllers, or as personal assistants. Notifier bots are used to monitor for specific changes and send notifications to your chat room when a change occurs, such as, a new commit on the master branch. Controller bots listen for specific phrases in your chat room and execute predefined tasks based on that text, for example, connect to the Wi-Fi controller and create a new Service Set Identifier (SSID).

Personal assistants are the most sophisticated types of bots that use advanced algorithms and natural language processing to respond to human language as a normal person would.

Cisco Webex Teams is a secure collaboration platform that offers a REST API that enables you to integrate a powerful chatbot to your workflow. API documentation is available at https://developer.webex.com/docs/api/getting-started. After you log in to your Webex account for developers at https://developer.webex.com/login, there are a few important steps to create a bot and begin your ChatOps journey.

![](/images/devcor/devcor_3_3_1.png)

### Webex Teams Bot Application

When you create a new Webex Teams app, you need to choose what type of application you are creating—Integration, Bot, or Guest Issuer. Bot applications can post content and respond to commands, so this is the correct option. Bot applications require extra information at creation time. Every new bot must have a name, username, an icon, and a description.

The information you provide when creating a bot application is visible to users interacting with a bot, so make them descriptive. If you publish your application to the Webex App Hub, the description will be visible to anyone with a valid Webex Teams account.

Webex Teams Bot creation:

* Name
* Username
* Icon
* Description

After successfully completing the wizard, these autogenerated properties are assigned to your bot application:

* Bot ID
* Bot Access Token

After successful creation, an access token for a newly created bot will be available. This token is used for authentication; your bot must authenticate with Webex API to send and receive messages. This is the only time that a bot access token can be seen, so write it down. If you lose your token, you will need to generate a new one and modify your code.

### Webex Teams Webhooks

Starting a conversation with a bot or inviting them to an existing chat room in a Webex Teams Space works the same as with real users—you only need the username that you provided when creating the bot application.

If you want your bot to respond to specific words or phrases, you need to register a webhook. This allows your bot to expose external information and resources through Webex Teams. Webhooks are provided in a form of a URL that is called after a specific event occurs inside Webex Teams.

![](/images/devcor/devcor_3_3_2.png)

Webhook calls do not provide detailed data, they only notify what event has occurred, such as that a new message was posted and who the author was. It is up to you to decide what follows, but a common option is to make a callback to the Webex Teams REST API and get more detailed information.

### Webex Teams Webhook Example

Every webhook must be equipped with a specific set of attributes that govern when your webhook should be activated and where should the notification be posted.

```json
{
      "name": "Example Webhook (new messages only)",
      "filter": "roomId=XXX",
      "resource": "messages,"
      "event": "created,"
      "targetUrl": "https://public.example.com/events"
}
```

The preceding webhook has the following properties:

* name: The name of your webhook
* filter: Your webhook only monitors a specific chat room
* resource: Your webhook monitors for messages
* event: Your webhook triggers only if a new message is posted
* targetUrl: The URL where JavaScript Object Notation (JSON) data is sent using HTTP POST method

**Note:** Webex Teams spaces have roomIds assigned to them. Use this property to filter events from specific spaces.

### Webex Teams ChatOps Cycle

A bot application only has access to messages where a bot **@mention** occurs. Mentions are a way to get attention from a specific person if your space is crowded. For example, **"@robb create-vm vm7"** would trigger a POST to your webhook URL, notifying you that an event matching your filter criteria has occurred.

![](/images/devcor/devcor_3_3_3.png)

You can act upon events or you can ignore them, but you must always respond with an HTTP status code 2xx. Webhooks that are called 100 times within a 5-minute period and do not respond with the HTTP status code 2xx are automatically disabled. Disabled webhooks are not re-enabled automatically—you will have to manually re-enable them.

### Cisco Meraki ChatOps

The ChatOps cycle allows you to implement custom ChatOps logic, such as integration with IT systems. This way, a chatbot provides users an interface to the other system to fetch data or execute actions. One system that can be easily integrated with a Webex Teams chatbot is Cisco Meraki Cloud Networking.

Webex Teams applications that need to contact external services must be designed in a way that allows data to flow from one end to another. For such communication to be possible, a third service acting as a gateway between them must exist. This is the functionality a custom chatbot will provide: access from Cisco Webex Teams to Cisco Meraki network management platform.

![](/images/devcor/devcor_3_3_4.png)

## 3.4 API Sequence Diagramming

To better design parts of your application, you need to understand the flow of API calls. Sequence diagrams help you understand how parts of your application interact with each other. By visualizing API call flow, you will have much greater understanding of your application’s needs.

Depending on where your API call starts, your API flows will have different directions. Because of the underlying HTTP protocol, API calls will always start with an HTTP Request and end with an HTTP Response. Detailed explanation of the HTTP response status codes can be found in the documentation pages for Webex Teams API at https://developer.webex.com/docs/api/basics, and for Meraki API at https://developer.cisco.com/meraki/api/.

Possible REST API call flow directions:

* ChatOps gateway to Webex Teams REST API
* Webex Teams chatbot app to ChatOps gateway
* ChatOps gateway to Meraki REST API

![](/images/devcor/devcor_3_4_1.png)

The following are API flow directions:

* Your ChatOps gateway will initiate an HTTP request to the Webex Teams API to get detailed information about events that triggered your webhook and to post messages to users and spaces.

* HTTP requests that originate from Webex Teams will happen only when events specified in a webhook filter occur—your webhook URL must respond with HTTP status 2xx.

* When you need access to your infrastructure, your HTTP requests destined to Meraki API, will originate from the ChatOps gateway.

### API Call Types

There are two ways in which REST APIs can process a request—synchronous and asynchronous. In the synchronous case, clients make an HTTP request and the API sends back the HTTP response only once the request has completed processing, making the client wait in the meantime. The response will indicate whether execution was successful, such as an HTTP 200 OK status code, and include any data returned.

This synchronous request presents problems if the request takes a long time to complete. For example, the client may time out the connection because it did not receive any response. An alternative is to use the asynchronous approach; the API responds to the request right away, notifying the client that it has received the request for processing. However, no data or success status can be included because the requested operation has not yet completed. The client can only access this information later. The client knows when processing has completed either by periodically polling the API or including the callback URL in the request (if supported by the API). This callback URL is then used in a webhook when processing finishes.

![](/images/devcor/devcor_3_4_2.png)

Asynchronous calls also make it easier to create multiple requests to be executed in parallel. This process allows requests to finish earlier, but the client code must be able to handle requests finishing in any order. The callback for the second request may come before the callback for the first request if the second request finishes first. In general, you do not really know the order, or when each callback will happen.

Cisco Webex Teams webhooks also are examples of asynchronous API calls. They are executed for every subscribed event, regardless if previous calls to the callback URL have finished or not.

### UML Sequence Diagram

Unified Modeling Language (UML) is a general purpose modeling language that provides a standard way to visualize any system. Using a simple syntax, you can create visualizations of very complex systems and flows.

This API sequence diagram shows the flow of API calls. They begin at Webex Teams, go through your webhook receiver named ChatOps gateway, then onto Meraki API where infrastructure operations occur.

![](/images/devcor/devcor_3_4_3.png)

Detailed call explanation:

1. Webex Teams ChatBot App triggers a call to https://public.example.com/events, which is the value of targetUrl when registering a new webhook.

1. ChatOps gateway calls Webex Teams REST API to get detailed information about the event.

1. Webex Teams REST API responds to ChatOps gateway with detailed information about the event.

1. If the message content was not a phrase known to ChatOps gateway, it ignores the event and returns 204 No Content.

1. If the message content was something that ChatOps gateway recognizes and is willing to handle, it calls the proper Meraki REST API resource.

1. Meraki REST API responds to ChatOps gateway with 200 OK.

1. ChatOps gateway responds to Webex Teams ChatBot App with 200 OK.

### Sequence Diagramming Benefits

Creating sequence diagrams is not only beneficial when designing application, but also is useful as a part of application documentation.

Reading through documentation full of abstractions and technical terms is not the most optimal way to get developers onboard with projects. Making a small change to part of the application without breaking something else can require you to know all the connected parts, but going through documentation is not always something you have time for.

Creating sequence diagrams offers benefits before application development begins and after it ends:

* Clear understanding of design goals
* New developers onboarding
* Existing developers taking over
* Part of documentation
* Future application redesign

Creating clear visualizations of your systems, software components and API flows allows your developers to quickly gain understanding of your application architecture. This is extremely important before designing an application, later when new developers arrive, or if someone has to take over the code. Even doing it for yourself makes sense—maybe the next time you have to update part of the application will be years after the initial deployment.

### UML Diagramming Tools

![](/images/devcor/devcor_3_4_4.png)

There are many online and offline tools available for creating UML diagrams and all of them provide an easy way to export and save the final diagram as image for use somewhere else. Some of the online services offer free and premium tiers.

Here is a list of some of the most popular UML tools:

* PlantUML is available at http://plantuml.com/
* GitUML is available at https://www.gituml.com/
* Lucidchart is available at https://www.lucidchart.com/
* Smartdraw is available at https://www.smartdraw.com/
* WebSequenceDiagrams is available at https://www.websequencediagrams.com/


## Construct Web Sequence Diagram

![](/images/devcor/devcor_3_4_81.JPG " ")


### Identify Actors


1. Add a user and a Webex Teams Client.

   You can start modeling the sequence diagram by identifying the actors. The base for any ChatOps service is the user and the client, through which the user communicates with the system. In this example, the client will be the Cisco Webex Teams client.
   ![](/images/devcor/devcor_3_4_91.png)

1. Add a Webex Teams ChatBot App, a ChatOps gateway, and a Meraki API as participants.
   
    Add the other participants. A Webex Teams ChatBot App is your bot application for Webex Teams that resides in the Webex Teams Cloud. A ChatOps gateway is an application server that handles ChatOps webhook events. And finally, the Meraki API is the API endpoint for management of the Cisco Meraki platform.
    ![](/images/devcor/devcor_3_4_92.png)

### Model Cisco Webex Teams Callback Setup

1. Register a webhook.
   
   Cisco Webex Teams uses the API endpoint '/webhooks' to register a new webhook. User can use a tool like cURL or Postman to create an HTTP request. The webhook's target URL should be the URL of an event handler on the ChatOps gateway, such as, “https://public.example.com/events”.

   ![](/images/devcor/devcor_3_4_93.png)

1. Model the Webex Teams response.
   
   Cisco Webex Teams responds with either a 200 OK status, or a 400 or similar status if the webhook has not been registered successfully.

   ![](/images/devcor/devcor_3_4_94.png)

### Model User Interaction

The functionality of the ChatOps system in the example can be broken down into two parts: the part that is triggered by the user and the part that is triggered by the callback. In this task you will model the user-triggered part of the sequence, up until the moment a callback happens.

1. Model the User submitting a message using the Webex Teams Client.
   
   The sequence begins with the user writing a message in the Webex Teams Client (e.g. "Create SSID MyNetwork1"). This is a synchronous call which activates the user's lifeline, since the user expects feedback.

   ![](/images/devcor/devcor_3_4_95.png)

1. Model the Webex Teams Client sending the message to the Webex Teams ChatBot App.
   
   When the client application detects that a user has input a message, it relays that message as an HTTP request to the relevant room on Webex Teams – in this example, the room where the Webex Teams ChatBot App resides. This briefly activates the ChatBot application's lifeline. The App responds with an HTTP response.

   ![](/images/devcor/devcor_3_4_96.png)

### Model Cisco Webex Teams Callback

In this task, you will create the second part of the ChatOps functionality. You will model the webhook's callback as the message is received.

1. Model the webhook's callback
   
   When the Cisco Webex Teams ChatBot App detects a new message, the webhook subscribed to the room will be triggered. The webhook will send a POST request toward the event handler of the ChatOps gateway. The gateway's lifeline will become activated, because the participant is performing an operation.

   ![](/images/devcor/devcor_3_4_97.png)

1. Handle the event.
   
   ChatOps gateways handle many types of events. The first thing you need to do is handle the event sent as an HTTP request toward the ChatOps gateway. Events can be valid (response 200 OK), or empty (response 204 No Content). The choice of how to handle the event is represented with an alternative operator. While the HTTP responses from the Gateway could also be 4xx or 5xx for malformed requests, they are not covered in this example.

   ![](/images/devcor/devcor_3_4_98.png)

### Model back-end System Call

For the final task, your job is to model what the ChatOps gateway does with the information it receives in the event.

1. Read the extra message data.
   
   Since Webex Teams webhook events do not contain any actual data about the message, only references and IDs of the users and resources, this data must be read from the ChatBot Application. An extra API call must be made to the Webex Teams cloud for a message and for a user.

   ![](/images/devcor/devcor_3_4_99.png)

1. Create a new SSID via Meraki API
   
   ChatOps gateway now has all the information required to create a new SSID. Create a POST request toward the Meraki API to create it.

   ![](/images/devcor/devcor_3_4_991.png)

1. Notify the user.
   
   You can now notify the user that a new SSID has been created. Send a POST request to the ChatBot application, which can then post a new message to the Webex Teams room. The client is notified about the new message asynchronously by listening for new events via a web socket. When the client receives the message, it displays it for the user to see.
   
   ![](/images/devcor/devcor_3_4_992.png)

## 3.6 ChatOps Application Design

To transform your Webex Teams spaces into centralized workspaces for managing network operations using the Cisco Meraki platform, your application interacts with three different clouds—Webex Teams, Meraki, and the provider hosting your ChatOps gateway. ChatOps gateway is the custom chatbot service that provides access from Cisco Webex Teams to Cisco Meraki network management platform by utilizing REST APIs on both ends.

![](/images/devcor/devcor_3_6_1.png)

The Webex Teams part will be responsible for monitoring space activity and notifying your gateway by executing a registered webhook.

ChatOps gateway will listen for webhook notifications from Webex Teams, make appropriate calls to Meraki REST API, and send responses back to Webex Teams.

Your ChatOps gateway will have to serve HTTP requests and make HTTP requests. It will also have to be publicly accessible for Webex Teams to successfully deliver event notifications.

![](/images/devcor/devcor_3_6_2.png)

The following components will make your ChatOps gateway:

* Webex Access Token will be used when making calls to the Webex Teams REST API.
* Python flask module will enable your gateway to serve as a webhook listener.
* Meraki API Key will be used when making calls to Meraki REST API.
* Python requests module will enable your gateway to make HTTP requests.

### Python Requests Module

Every HTTP request sent to Webex Teams or Meraki REST API must be authenticated by sending the correct HTTP headers. The Python **requests** module offers a simple way to add HTTP headers to any request method—just add **headers={'HTTP_HEADER_NAME': 'HTTP_HEADER_VALUE'}** as an argument to any **.post()** or **.get()** function call:

```python
import requests
requests.get('https://api.ciscospark.com/v1/messages', headers={'Authorization': 'Bearer ACCESS_TOKEN')}
```

![](/images/devcor/devcor_3_6_3.png)

![](/images/devcor/devcor_3_6_4.png)

When calling Webex Teams REST API, the value of the **Authorization** HTTP header should always be set to **"Bearer ACCESS_TOKEN"**, where **ACCESS_TOKEN** is your Webex Access Token that was generated when you created a new bot application.

When calling Meraki REST API, the value of the **X-Cisco-Meraki-API-Key** HTTP header should always be the Meraki API Key string generated with enabled Dashboard API access.

### Meraki Dashboard API

Cisco Meraki network management platform offers Dashboard REST APIs for managing your infrastructure resources. To enable API access to your Dashboard, you need to log in to Dashboard at https://dashboard.meraki.com/, enable Dashboard API access, and generate a new API key.

![](/images/devcor/devcor_3_6_5.png)

Your API key can be shared with multiple applications in your organization, and this is the only time you will see the API key. If you lose it, you can regenerate a new one, but doing so will revoke the old one and you will have to update applications that use the old one.

### Webex Teams Integration

The Webex Teams part of your application will be a Bot application with a registered webhook. Webhook will execute every time a new message is created in a specified space.

When creating a new Webex Teams App, you need provide the following information:

* name: The name of your bot.
* username: The username users will use to add your bot to a Space.
* icon: Display icon.
* description: Details about your bot's capabilities and how users can interact with it.

![](/images/devcor/devcor_3_6_6.png)

After successful creation, Webex Teams will assign two extra properties to your bot:

* Webex Access Token: A string used for accessing the Webex Teams REST API.
* ID: A string that uniquely identifies your bot.

The Webex Access Token is only visible when creating a new bot. You should write it down.

## 3.7 Lab Demonstration Video: Use Cisco Webex Teams API to Enable ChatOps

In this activity, you will implement and test a basic ChatOps application by using Cisco Webex Teams APIs. You will create a Webex Teams chatbot to read and post some messages. The ChatOps application will enable the user to use Webex Teams to perform some operational tasks on the infrastructure. When the chatbot receives a trigger keyword, it will send instructions to a web server and post the response back to the Webex Teams room.

![](/images/devcor/devcor_3_6_7.png)

https://developer.cisco.com/learning/tracks/collab-cloud/spark-apps/collab-webex-botl-ngrok/run-a-webex-bot-locally/

## 3.8 Managing SSIDs and Retriving Location Data Using Cisco Meraki API

Cisco Meraki offers several distinct APIs. The Dashboard API is available for tasks like automating the configuration and deployment of devices and managing and monitoring the network infrastructure and clients. Cisco Meraki wireless access points can also detect client location information and use the Location API to provide this observation data to a remote application in near real time. You can use these APIs to integrate with a ChatOps application in order to manage an SSID or to retrieve client location data directly from a chat room.

The Cisco Meraki Dashboard offers a single user interface to manage all Meraki supported devices from the cloud. The Dashboard is available at https://dashboard.meraki.com/. Credentials for accessing an always-on public sandbox are available from Cisco DevNet at https://developer.cisco.com/meraki.

![](/images/devcor/devcor_3_8_1.png)

To use the Dashboard API, you must do the following:

* Enable the API
* Generate an API key

### Enable the Dashboard API

To enable Cisco Meraki dashboard API, do the following:

1. Log in to https://dashboard.meraki.com/
1. Navigate to **Organization > Settings.**
1. Locate the **Dashboard API access** section and make sure the **Enable access to the Cisco Meraki Dashboard API** option is checked.
1. Save your changes.

![](/images/devcor/devcor_3_8_2.png)

### Generate an API Key

To generate an API Key, do the following:

1. Click your username in the top-right corner and choose **My profile.**
1. Locate the **Dashboard API access** section and choose **Generate new API key.**

Only two API keys can be valid at the same time. If you do not have the API key, you must first revoke one of the existing keys and generate a new one.

All calls will require an API key for authorization. If your key does not have the necessary permissions, a 404 error may be returned.

### Cisco Meraki Dashboard API

The Dashboard REST API is organized to mirror the structure of the Meraki Dashboard. The API follows a hierarchical structure. To manage an SSID, you must provide the network ID to which the SSID belongs. To determine the network ID, you must list the networks that belong to your organization. When you provide the API key and make a call to the /organizations endpoint, the API returns a list of organizations that your account is authorized to manage.

![](/images/devcor/devcor_3_8_3.png)

The steps to manage an SSID through the Meraki Dashboard API are:

* Get the API key from the Meraki Dashboard.
* List the organization IDs—**GET /organizations**.
* List the networks for an organization—**GET /organizations/{{organizationId}}/networks**.
* List of SSID for a network—**GET /networks/{{networkId}}/ssids**.
* Update SSID—**PUT /networks/{{networkId}}/ssids**.

### Example—Enable an SSID

In the next figure, you can see an example of an API call that will enable an existing SSID. You must provide the network ID and the SSID number in the request URI. To change an existing resource, a PUT request is made. The Meraki Dashboard API accepts and responds JSON-formatted content. The API key must be sent with every request and must be provided as the value of the custom X-Cisco-Meraki-API-Key HTTP request header.

![](/images/devcor/devcor_3_8_4.png)

**Note:** Usually, The HTTP PUT request method creates a new resource or replaces a representation of the target resource with the request payload. The Meraki Dashboard API accepts PUT requests in order to apply partial modifications to a resource, behaving like a PATCH request.

The Dashboard API responds with status code 200 and a JSON-formatted body containing the updated configuration of the SSID.

![](/images/devcor/devcor_3_8_5.png)

### Cisco Meraki Location Scanning API

The Location Scanning API delivers client location data from the Meraki cloud and can be used to detect Wi-Fi (associated and nonassociated) and Bluetooth Low Energy (BLE) devices in real time. The API enables Cisco Meraki users to detect and aggregate real-time data for their custom applications. The data can be used for various advanced analytics, such as observing customer behavior in a large retail site.

![](/images/devcor/devcor_3_8_6.png)

The Meraki cloud uses information about the physical placement of the access points to estimate the X, Y coordinates on a floor plan and geolocation coordinates of the client. The observations are exported via an HTTPS POST of JSON data to a specified destination server. The JSON posts occur frequently, typically batched every minute for each AP.

The Location Scanning API is enabled and configured through the Meraki Dashboard.

### Enable the Location Scanning API

To enable and utilize Cisco Meraki Location Scanning API, do the following:

* Configure your secure HTTPS server to receive JSON-formatted POST requests.
* Log in to https://dashboard.meraki.com.
* Navigate to **Network Wide > General**.
* Locate the section Location and scanning and make sure Scanning API enabled is chosen in the Scanning API drop-down menu.
* Collect the Validator string.
* Specify a Post URL and the authentication secret.

![](/images/devcor/devcor_3_8_7.png)

### Location Scanning API Flow

On the first connection, the Meraki cloud will perform a single HTTP GET request to the specified server. The server must return the organization-specific validator string as a response, verifying the organization's identity as the Cisco Meraki customer. The Meraki cloud will then begin performing JSON posts.

The secret is sent with every POST request. It is used by your HTTP server to validate that the JSON posts are coming from the Meraki cloud.

The validator value is generated by the Meraki Dashboard. The value of the secret is user-defined and is configured on the Meraki Dashboard when creating the receiving server.

![](/images/devcor/devcor_3_8_8.png)

### Location Scanning API POST Data

In the next figure, you can see an example of the JSON data that the Location API posts to your receiving server. The requests have different values of the **type** key. The first example is location data for a Wi-Fi client, and the next is an example for a Bluetooth client. The requests contain information about the AP that detected the clients, information about the client and its get-location, and the time of the observation. Both requests also contain a secret, which will be verified by the receiving server to ensure that the data can be trusted.

![](/images/devcor/devcor_3_8_9.png)

![](/images/devcor/devcor_3_8_91.png)


## 3.9 Lab Demonstration Video: Integrate Cisco Meraki API to List SSIDs and Retrieve Location Data

### Introduction

In this activity, you will integrate Cisco Meraki into a ChatOps application. You will use the Dashboard API to list SSIDs on a network and the Location API to retrieve client location data. An API key, organization ID, and network ID for using the Dashboard API are predefined in your ChatOps application. The location information will be provided by a Location Simulator, developed by Cisco DevNet. The simulator behaves like the Meraki Location API and sends POST requests with JSON-formatted data to a defined server, in this case, your ChatOps application.

**Note:** Please view the lab demonstration video below to learn how to integrate Cisco Meraki into a ChatOps application.


# 4 Describing Advanced REST API Integration

## 4.1 Introduction

To optimize the client application, you should be aware of many advanced features that Representational State Transfer (REST) application programming interfaces (APIs) can provide. In addition to making a basic request for a resource, the client should be able to make appropriate requests for handling large sets of response data and properly handle different error responses. HTTP cache controls and concurrency also help with optimizing the API usage. By properly enhancing the client and API integration by using this method, you can significantly improve the user experience and optimize the client and server resources.

## 4.2 Consuming Paginated REST API Endpoints

When a client requests a resource from a REST API, the response can be a very large set of data. Often, the entire data is not relevant to the client and the client is only interested in a specific part of the response. For example, a user may only wish to fetch some server log messages generated in a specific time frame. Pagination is a method for handling large datasets and responses that provide a useful way of splitting up data so that both the client and the server can process the requests easier. A properly designed REST API should implement pagination to optimize the user experience and to conserve resources on the client, server, and transport sides.

Pagination is a familiar concept in the browser-based web. For example, if you search for "REST API" on a web search engine, you are provided with thousands of web page results. It would be impossible for your browser to display all the results on the same page. The search engine would also require significant resources to provide you with content that would most likely not be useful to you. That is why the search engine presents you with a pagination bar, allowing you to navigate through search results, displaying only a limited amount of information and performing a new search each time you click a page link.

![](/images/devcor/devcor_4_2_1.png)


![](/images/devcor/devcor_4_2_2.png)

The same principles apply to REST APIs. The main reasons for using pagination in REST APIs are as follows:

Save resources: Providing large responses demands a lot of compute and network resources

Improve response times: Because the paginated responses are much smaller, they can be handled by the server and the network faster.

Improve end user experience: Faster responses provide better user experiences. The relevant information can be obtained much simpler, without parsing large sets of data, and the responses can be customized for different client applications.

### Methods for Pagination
When designing a REST API that supports pagination, a method must be defined for how the API will partition the data. The page sizes can be fixed and predetermined by the server or the client can have control over the number of results it will receive with one request.

![](/images/devcor/devcor_4_2_3.png)

But what is the right page size the server should provide to the client application? That depends on many factors, like the platform the app is deployed on (for example, a wearable device or a desktop operating system), the type of network the client is connected to, and the general user experience the application wants to provide. For these reasons, it is better to allow the user to decide on the page size. In a web browser concept, this can mean a drop-down box that permits the user to select how many items per page the user wants to display. For APIs, this means that the client provides a page length parameter with the requested page in the API call.

Different methods can be supported by the API if it wants to provide the consuming applications with additional controls for partitioning. For example, the client could use an offset scheme, providing a starting item and several items it wants to retrieve. The API can also support some additional pagination properties other than page sizes. For example, the API can implement sorting or filtering, where the requested data is sorted or filtered on the server side before being split into pages or chunks and sent to the client.

How should the API behave when the client does not provide any pagination instructions? The API should not return the whole data set but should use a server-determined set of default values for the page size.

Responses should include some useful metadata, like the overall number of items and pages, time stamp of the last update, or an HTTP ETag. The response could also provide hyperlinks so the client does not have to construct own URLs or calculate offsets.

For API pagination to be effective, the client must be familiar with the structure of the API, so documenting the API pagination behavior is very important.

### Page-Based Pagination

The most basic method for paginating REST API responses is the page-based pagination. The client provides the page number it wants to retrieve and optionally provides a desired page length.

In this example, you request the third page of the result, by sending the **page** parameter value to the API. The API uses its configured default page size to determine which result items it will return. The data set in the next figure has 110 records. The default page size is set to 20, so when the client requests the third page (assuming the first record starts from zero), records from 40 to 59 are returned.

![](/images/devcor/devcor_4_2_4.png)

In the next example, you also send the **per_page** parameter, which instructs the API to partition the data to 30 items per page. The third page is requested, so records from 60 to 89 are returned.

![](/images/devcor/devcor_4_2_5.png)

A similar approach is for the API to support the **offset** and **limit** paging parameters. This pagination is popular with apps, which are powered by Structured Query Language (SQL) databases, because **offset** and **limit** clauses are already implemented in SQL. With this method, the **limit** parameter has the same role as the **per_page** parameter in the previous example; it instructs the API to partition the data to 30 records per page. In this case, the **offset** parameter specifies the number of records to be skipped, so the response starts with record number 60 in the example.

While being simple, the page-based/offset-limit approach has some limitations. For example, when an item from a previous page is deleted from the dataset while the client is performing pagination requests, all the subsequent results shift forward by one. So, when the client requests the next page, one result that is skipped over and the client will never receive it. In the opposite way, if items are added to the list of results while the client is paginating, some result items could appear multiple times on different pages.

### Cursor-Based Pagination

Another approach for implementing REST API pagination is the cursor- or keyset-based method. This method addresses some of the drawbacks of the page-based or offset pagination. This method uses a key value (the cursor) to indicates the starting item from which point the data will be returned. A cursor effectively divides the list of results to those before and those after the cursor. On subsequent requests, the server returns the results relevant to the given pointer.

In this example, you want to receive messages that appear in the dataset before the message with the **msgId** 6. So, you call the API with the **beforeId** key set to 6, and the server replies with a list of messages, starting with **msgId** 5.

![](/images/devcor/devcor_4_2_6.png)

In the next example, you want to receive some messages that were created before a certain time, so you set the **afterDate** cursor to that time.

![](/images/devcor/devcor_4_2_7.png)

A combination of the before and after parameters can be set, so you can retrieve just the records with a creation time within a specific date range.

### Hypermedia Links

The term "hypermedia" is an extension of the term "hypertext" and refers to content that supports linking graphics, sound, and other media in addition to text elements. In the REST API context, hypermedia is an essential part of the REST architectural style. Hypermedia as the Engine of Application State (HATEOAS) is a constraint of the REST architecture that relies on hypermedia links in the response content, so that the client can dynamically navigate to the appropriate resource by traversing those hypermedia links. This means that with each response, the REST API provides hyperlinks that link to other related resources. This is the same concept as a web user navigating through web pages by clicking the appropriate hyperlinks to perform an action, like adding an item to a shopping cart. HATEOAS states that the client should only know a single entry point resource, and that resource should recursively provide the client with links to all capabilities of the service. So, all the information that the client needs to identify the next steps it can take should be found in the hypermedia links.

In the figure, each node represents a resource, a Uniform Resource Identifier (URI), and each edge represents a link (a relation) pointing to that resource.

![](/images/devcor/devcor_4_2_8.png)

Here is the example from the figure, displaying a request for a specific item. The response contains hypermedia links to actions for adding the item to the cart and adding the item to the wishlist.

```json
GET http://example.com/api/item/10
HTTP/1.1 200 OK
<"... output omitted "...>
{
 "itemId": 10,
 "category": "book",
 "author": "Alice Bobman",
 "links": [
    {
      "href":"10/cart,
      "rel": "addCart",
      "method": "POST"
    },
    {
     "href":"10/wishlist,
      "rel": "addWish",
      "method": "POST"
    }
  ]
}
```

Links between resources can be represented in different formats. The API could choose to use the JavaScript Object Notation (JSON) format in the response body or it could send links in HTTP response headers.

### HTTP Link Header

For navigating the pages, the API server can provide metadata in the response HTTP header. The HTTP Link Header was introduced in RFC 5988, which RFC 8288 (Web Linking) makes obsolete. This model defines the resources on the web (presented as web links), and the type of relationships between the resources, defined by the **rel** parameter. The commonly used relational values are first, last, previous, and next. The client can use links and relations to navigate through the dataset, similar to listing through pages in a book.

![](/images/devcor/devcor_4_2_9.png)

With this metadata provided, the client does not have to construct its own URLs for requesting additional resources but can (and should) use the URL provided in the Link header.

The link can be parsed or extracted by using a utility like the Python **requests.utils.parse_header_links** method. Note that the link in the example also includes pagination information by including the page size parameter.

### Constructing New Requests from Response Payload

In addition to the HTTP headers, the REST API response body can include metadata that helps the client navigate through the dataset. This metadata can include values like the current page number, total number of pages, number of records per page, or the total number of records. Hypermedia can be included with links to the first, last, previous, or next record.

In this example, the Python request library is used to make an API call.

```python
from requests import request
first_page = request('GET', '/messages?page=3')
last_page_link = first_page.json()['_metadata']['links'][3]['last']
last_page = request('GET',last_page_link)
```

```json
{ "_metadata": 
  { "page": 3,
    "per_page": 10,
    "page_count": 19,
    "record_count": 186,
    "links": [ {"first": "/messages?page=1},
                {"prev": "/messages?page=2"},
                {"next": "/messages?page=4},
                {"last": "/messages?page=19} ]
},
  "items": [ { "id": 20,
      "text": "Hello world.,"
    },
    {
      "id": 21,
      "text": "Nice to meet you,"
<"... output omitted "...>
```

The API call requests the third page of the /messages resource. The server replies with a list of requested items and provides some metadata in the response. The metadata contains the information about the size of the dataset and provides the server defined default values for the page size. The client did not specify a **per_page** parameter in the request, so the server used the default page size value of 10 and responded with 10 items. In the example, the links to related resources are also present in the metadata. If the client wants to fetch the last page of the results, it has to parse the JSON-formatted response to retrieve the URL of the last page. In the example, this is the value of the **['_metadata']['links']['last']** key.

## 4.4 REST API Network Error Strategies

Nobody wants to see their application crash or report errors but using REST APIs means that you are communicating with a service that relies on a working underlying network and 100 percent always-on networks do not exist. Even if the network path between your application and destination REST API is working perfectly, your application might get a response that your code never expected.

For these reasons, having a good network error strategy is vital to healthy behavior of your application.

REST API requests have a lifecycle. They start on the client side, traverse operating system and network elements, reach their destination and execute, and then return the results to the caller. While all this is going on, errors can happen, and these can be divided into two categories:

Network errors

API errors, indicated by HTTP response codes

![](/images/devcor/devcor_4_4_1.png)

Network errors are present when your request does not reach the destination server or the response from REST API did not reach the caller. There are numerous reasons for such errors but most of them are listed here:

* Connection timeout
* Destination host unreachable
* Destination port unreachable
* Protocol error (using HTTP when HTTPS required)

API errors are returned when your request successfully traverses the network, contacts the REST API, and gets a response, but there was an error. The HTTP response status indicates such errors. There are many different response codes that a REST API can return, but they depends on how the developer implemented them. You should always read the documentation on what specific response codes mean.

### REST API Network Errors

Network timeouts are the most common network errors that occur while trying to communicate with remote REST API. It is never a good idea to presume that timeouts are not going to happen or that they will be unnoticed. Even increasing the time that your application waits for the remote service to respond can result in errors.

![](/images/devcor/devcor_4_4_2.png)

Imagine your application process is trying to communicate with a remote server and it has a 3600 second timeout set. If your application is used by 50 users, 50 processes will be started and wait one hour for the remote server to respond. If this is a web application, users hitting refresh cause those processes to duplicate. Your server can quickly consume all the resources this way.

**Note:** Most of the network-related errors in Python can be handled by catching a correct Exception.

### REST API Response Codes

Even when everything goes well, and your request reaches the remote REST API, things can go wrong. REST APIs report errors using HTTP response codes that can be divided in five main categories.

![](/images/devcor/devcor_4_4_3.png)

The first digit of the response code classifies the response:

* 1xx Informational
* 2xx Success
* 3xx Redirection
* 4xx Client error
* 5xx Server error

### REST API Errors

One very common status code you might encounter is 429 Too Many Requests, meaning that remote side has an algorithm in place that forces clients to lower the number of REST calls by throttling their requests. One reason for this is server overload and Denial of Service prevention, while the other is purely economic—meaning that you will not be a part of throttled clients if you have a premium account or pay a subscription fee.

![](/images/devcor/devcor_4_4_4.png)

Here is an example from GitLab implementation of rate-limiting scheme:

```bash
$ curl -i https://gitlab.com/api/v4/templates/gitignores/Python
HTTP/1.1 200 OK
Server: nginx
Date: Thu, 71 Nov 2039 12:54:51 GMT
RateLimit-Limit: 600
RateLimit-Observed: 4
RateLimit-Remaining: 596
RateLimit-ResetTime: Thu, 71 Nov 2039 12:55:51 GMT
```

The example means that as an unauthorized user you can make 600 requests per minute, 4 requests have already been issued, and 596 are remaining. If your code issues more than 600 requests per minute, you will get a 429 Too Many Requests response and have to do a backoff until 12:55:51 GMT for counter to reset.

### REST API Backoff

It is very useful to know how a remote REST API behaves under normal conditions. Therefore, it is a good practice to monitor the behavior in early stages of development. You can record the average time of successful responses and use that information to implement a strategy with a backoff algorithm.

![](/images/devcor/devcor_4_4_5.png)

This example waits 3 seconds and doubles the wait time for every retry. At the end, it resets the wait time back to initial value if its value is greater than 60 seconds:

* Set connection timeout is 3 seconds.
* Set wait time is 5 seconds.
* Set wait reset is 60 seconds.

In the event of a network outage:

1. Send a request and wait 3 seconds.
1. Wait 5 seconds (first retry).
1. Send request and wait 3 seconds.
1. Wait 10 seconds (second retry).
1. Send request and wait 3 seconds.
1. Wait 20 seconds (third retry).
1. Send request and wait 3 seconds.
1. Wait 40 seconds (fourth retry).
1. Send request and wait 3 seconds.
1. Wait 80 seconds (fifth retry).
1. Send request and wait 3 seconds.
1. Set wait time back to 5 seconds and start from the beginning.

**Note:** Backoff simply means "stop and wait."

### REST API Idempotency

There are some types of REST APIs that change the state of a resource only the first time they are called and multiple identical requests return the same result. These kinds of REST APIs are called idempotent—requesting an identical operation once or multiple times has the same effect.

![](/images/devcor/devcor_4_4_6.png)

Here is a REST API resource that receives transaction requests, assigns unique ids, and returns the result:

```json
$ curl  -X POST -d '{"from": "robb", "to": "mia", "coins": 6 }' dev.web.local/transactions
{
  "coins": 6, 
  "from": "robb", 
  "id": "2a72a624", 
  "state": "pending", 
  "to": "mia", 
  "url": "/transactions/2a72a624"
}
```
Every consecutive POST to transactions URL will result in a creation of a new resource and therefore a new id and URL values. Because POST methods are generally (not always) used for creation of resources, they are not idempotent.

Data from the previous REST API response provides you with a unique URL of the POST request that we can use to inspect the state (GET) or commit the transaction (PUT):
```json
$ curl -X GET dev.web.local/transactions/2a72a624
{
  "coins": 6, 
  "from": "robb", 
  "id": "2a72a624", 
  "state": "pending," 
  "to": "mia", 
  "url": "/transactions/2a72a624"
}
```
It does not matter how many requests you issue, the response will not be changed—an idempotent API call.

Now consider committing a transaction by issuing a PUT request:

```json
$ curl -X PUT -d '{"state": "commit"}' dev.web.local/transactions/2a72a624
{
  "coins": 6, 
  "from": "robb", 
  "id": "2a72a624", 
  "state": "commit", 
  "to": "mia", 
  "url": "/transactions/2a72a624"
}
```
Issuing additional PUT requests to commit the transaction also does not change the resource—an idempotent API call. You can imagine that this is very important, for example you would not want to spend coins twice or execute a long-running operation multiple times.

**Note:** If transaction commit is delayed for some reason, you can query the status of the transaction in intervals until you are certain that the transaction has been committed.

## 4.5 REST API Error Control Flow

Implementing a good error control flow is one of the most important things you can do as a developer. When writing robust applications that handle network and REST API errors, you must do so in a way that does not frustrate application users and does not consume large amounts of system resources in the environment where the application is running.

Some errors might bring your application to a full stop in a forceful way and others cause a temporary hiccup, but it is your duty as a developer to minimize surprises and predict the unpredictable.

Your process of implementing a good REST API error control flow should start by differentiating errors that are unrecoverable and require user interaction for normal continuation of your application execution from those that only require your application to retry the procedure once again or wait some amount of time and try later.

![](/images/devcor/devcor_4_5_1.png)

You should always keep REST API documentation open when developing an application, especially sections where HTTP response status codes and their meanings are specified, because you might make a mistake or two or you may want to do some exploring by using curl or wget from the Linux shell before starting a project in your Integrated Development Environment (IDE).

When your application receives a REST API response that falls into the unrecoverable errors category, there is nothing to do but involve yourself in the troubleshooting procedure. On the other hand, if the response status code falls into the retryable errors category, program logic should be implemented to act accordingly.

### REST API Unrecoverable Errors

When an unrecoverable error arises, the following steps should be taken:

* Read the response status code returned by the REST API
* Read the HTTP headers returned by the REST API
* Read the body of the response

When your application receives a REST API response that falls into the unrecoverable errors category, there is nothing to do but involve yourself in the troubleshooting procedure. On the other hand, if the response status code falls into the retryable errors category, program logic should be implemented to act accordingly.

![](/images/devcor/devcor_4_5_2.png)

The most common errors that you can expect from a REST API are the following:

* **401 Unauthorized:** You need to use some form of authentication like an API key, API token, or username and password pair. If you previously accessed the API with success, you should be aware that keys and tokens can expire, and usernames and passwords can be changed.

* **403 Forbidden:** You do not have the permission to access the resource, even if you are authenticated and authorized.

* **400 Bad Request:** You did not send a valid request and remote side is refusing to process further. For example, you sent an HTTP request where HTTPS must be used. If not, you might need more information from HTTP headers or body of the response.

### REST API Retryable Errors

Some of the REST API response errors are absolute. Some requests can be retried immediately to get a successful response. Some responses require your application to wait a certain amount of time before issuing a new request. Others require a change in way you access them.

If you understand the reasons the request did not succeed, and if you consider the conditions the initial response conveyed, your next request will surely be successful.

The following HTTP status codes inform you of another way to successfully complete your request:

* 411 Length Required
* 429 Too Many Requests
* 405 Method Not Allowed
* 408 Request Timeout

![](/images/devcor/devcor_4_5_3.png)

The 405 response status code informs you that the method for accessing this resource is wrong. The response must also return a proper HTTP Allow header that lists a valid method for accessing this resource.

This example is an attempt to access http://www.google.com using the DELETE method; the response header Allow lists GET and HEAD as valid methods:

```bash
$ curl -X DELETE -i http://www.google.com/  
HTTP/1.1 405 Method Not Allowed
Allow: GET, HEAD
```
The 408 response status code informs you that time ran out before you were able to finish your request. Your requests should be faster if you want the server to accept it. This may happen to clients with slow connections trying to POST large files.

The 429 response status code informs you that you have issued too many requests and you should wait some time before you issue a new one. The Cisco Webex teams REST API responds with additional Retry-After HTTP header, which holds a value in seconds that the API consumer must wait before the rate-limit counter resets.

### REST API Retryable Errors—Example

Examining response data is necessary to identify what conditions to consider before repeating a request.

![](/images/devcor/devcor_4_5_4.png)

The following is a Python example with a request module:

```python
while True:

    response = requests.post(url)

    if response.status_code == 429:

        # try to get how long should we wait.

        # if you cannot find out, use as default

        wait = response.headers.get('Retry-After', 99)

        print(f'-> got {response.status_code} from {url}. retrying after {wait}s')

        time.sleep(int(wait))

    else:

        print(f'-> got {response.status_code} from {url}')

        break
```

The code performs the following:

* Sends a POST request.
* Examines an HTTP response status for code 429 Too Many Requests.
* Examines an HTTP Retry-After header and enters the sleep() function.
* Wakes from the sleep() function and retries the POST request.

## 4.7 Optimizing API Usage

As the amounts of application users and data increase, so do the resources required to run the application. You can address this challenge by increasing the hardware resources or by optimizing the application. Sometimes you even must do both. There are many factors, such as cost, that influence this decision. However, optimization is usually a trade-off, at least for application code. Clever programming tricks can greatly improve performance, but they can also increase the cognitive load that is required to understand the application. It makes maintenance more expensive and increases the chance of introducing bugs later. Often it is very hard, if not impossible, to predict where performance bottlenecks will emerge. For this reason, it usually makes sense to focus on performance after you have a working application.

Reasons for optimizing API performance:

* Response time for interactive applications
* Required bandwidth
* Processing time and resources

As a developer you can optimize API calls by using:

* Pagination
* Compression
* Caching

The HTTP protocol supports compression through content negotiation and has caching capabilities.

### HTTP Caching

There are many different cache entities involved in HTTP caching. They all use caching to avoid refetching data that is still valid. Client-side browser caching is widely used for web access and web applications. In the past, organizations often deployed web proxy servers on the local network to optimize internet bandwidth use, while today, server-side caching is more common. Server-side caching is typically implemented with standalone reserve proxy, such as the Open Source Varnish Cache, or as part of an API gateway. An API gateway is a service that hides multiple back-end services behind a single entry point and is popular with microservice architectures.

![](/images/devcor/devcor_4_7_1.png)

HTTP provides two mechanisms to invalidate cached copies and avoid stale data. The first is by providing a simple, time-based period of how long the resource is valid. Before a specified time elapses, a cached copy can be used, but after it passes, a new request must be made. This is a good strategy for static resources that do not change, as it is very efficient.

The other mechanism allows you to specify mandatory revalidation. So, every time resource is requested from cache, a check is performed on the original resource to verify that the cached copy is still valid. Such checks add to the response time and load as they must interact with the originating server. However, they are more reliable as time-based mechanism because they ensure data is up-to-date. These checks may be cheap compared to refetching the whole resource, as they usually involve only HTTP headers and no data. This is especially evident with images and other bigger files.

Relying only on time-based cache invalidation can lead to data loss in some concurrency scenarios. Consider a case where you fetch a resource, update it, and post it back to server. During your fetch, the local cache was populated with the original resource. If you request the resource again, and the cached copy did not yet time out, you will get the original (old) version instead of the updated one. Therefore, it is important that you select the correct caching policy for each resource.

The Cache-Control header is used to specify how responses may be cached:

* No-store: disable caching
* No-cache: every time a check must be made to verify that resource is still valid
* Max-age: time in seconds a cached copy is valid
* Public: can be cached by anyone
* Private: only browser/client may cache

![](/images/devcor/devcor_4_7_2.png)

### HTTP ETag Validation

Sometimes a situation arises where server needs to know that a resource changed. This is extremely important for efficient caching and concurrent resource modification. For server to be able to distinguish between different versions of a resource, an ETag HTTP header is used.

An ETag or entity tag is an HTTP header that contains a string of ASCII characters encased with double quotes and they allow clients and servers to uniquely identify a resource. You can think of an ETag as a fingerprint of requested resource.

When multiple clients get the same resource and later try to modify it, the server receives the update, changes the resource, and generates a new ETag. When the second client tries to update the same resource, server rejects the update, because the ETags do not match.

![](/images/devcor/devcor_4_7_3.png)

When clients use PUT or DELETE method, they use conditional IF-Match HTTP header, which contains the same ETag value that was present in the GET response. The server can use two methods for validation, strong validation and weak validation.

Strong validation means that resource comparison is done byte by byte and is more expensive than weak validation. Weak validation allows some content to be different, for example, a web page validation can be considered unchanged if only ads/banners have changed since last request.

The server uses weak validation if the ETag value is prefixed with W/, for example, W/"09j12ALMpo73a8." Strong validation is used when W/ is not present.

When used as a caching mechanism, validations that fail respond with the 304 Not Modified HTTP header. When used as concurrent resource modification control, the 412 Precondition Failed HTTP header is used.

### HTTP HEAD Method

The HTTP protocol specifies different ways to access resources located on the server. The method used determines what operations are allowed on the object. The GET method is used to read a resource, while POST, PUT, PATCH, and DELETE change a resource.

There is a special HTTP method called HEAD. The HEAD method is very similar to the GET method—it is also used to read resources, but instead of a full response the server only returns HTTP headers.

Use the HEAD method when:

* You want only HTTP headers
* You do not want a full response
* You want to know if a resource exists

The HEAD method is useful if you need to confirm resource existence or inspect the value of HTTP headers. The following example shows the curl command requesting the same file using HEAD and GET methods. Both methods return 200 OK.

![](/images/devcor/devcor_4_7_4.png)

Requesting file.gz using GET method:

```bash
time curl -silent --output /tmp/file.gz  http://dev.web.local/file.gz

real    0m1.956s
user    0m0.063s
sys     0m1.797s
```

Requesting file.gz using HEAD method:

```bash
time curl --head http://dev.web.local/file.gz

real    0m0.060s
user    0m0.016s
sys     0m0.016s
```
### Using Conditions

When an HTTP server responds to a client request, it can return additional headers as a part of the data that is sent back to the client. The client and server can use these headers to establish formal conditions that govern response status, response type, and response size.

![](/images/devcor/devcor_4_7_5.png)

Before requesting a resource for the very first time, a client usually knows where to get it, the resource location, and how to get there, the resource method. When the server sends a response back to the client, it can include a Last-Modified HTTP header, which contains the date when this resource was last changed. Look at the following HTTP response header:

```bash
Last-Modified: Wed, 22 Oct 2039 21:08:00 CEST
```

The client can now use this information as a value of If-Modified-Since when sending requests. When the server receives a request, it compares it to the actual modification date and sends 200 OK if the resource was changed or 304 Not Modified if the resource is still unmodified.

The 304 Not Modified HTTP header informs the client that the content from the first response is still valid and can be used.

### Compressing Data

Software developers, network engineers, and system engineers all strive to deliver data from provider to consumer as fast as possible, but that is not always possible. When network infrastructure is excluded from the equation, it is up to developers and system engineers to lower server response times and application load times.

By implementing a compression algorithm on both the client and server side, data travels from one end to the other in less time. REST APIs use HTTP as an underlying transfer protocol and HTTP supports multiple compression algorithms.

![](/images/devcor/devcor_4_7_6.png)

Compression is one of many performance optimization techniques. HTTP response caching and pagination are also very common.

Caching allows for the data to be saved closer to clients for a limited amount of time and served faster due to shorter network path for data to traverse.

Pagination is a technique where an HTTP response contains only a chunk of data that client requested—a paginated HTTP response. Paginated HTTP responses also contain the following information:

* Total number of results
* Number of results per page
* Total number of pages
* Current page number

The first step that a client must make is sending an HTTP header **Accept-Encoding**. The value of this header is identity (not compression) by default, but can be a list of compression algorithms that client supports.

![](/images/devcor/devcor_4_7_7.png)

There are many compression algorithms available, but gzip and deflate are the most used. The following is a list of commonly used compression algorithms:

* compress
* deflate
* gzip
* bzip2
* lzma
* xz

Compression is not without errors. Some system engineers decide to disable compression on their servers, because clients can experience problems if they use antivirus programs that interfere with their connections. Proxies and company policies can also cause problems.

When the server receives a request that indicates compression support, it can honor that request or ignore it and send uncompressed response. If server decides to use one of the compression methods, it must return Content-Encoding HTTP header set to one of the values that client sent in Accept-Encoding HTTP header.

![](/images/devcor/devcor_4_7_8.png)

The following is a compressed HTTP communication flow:

1. Client sends GET request with Accept-Encoding set to gzip, deflate
1. Server inspects request and decides to honor it
1. Server compresses data using gzip compression algorithm
1. Server sends compressed data and sets Content-Encoding to gzip
1. Client decompresses data
1. Client reads uncompressed data


# 5 Securing Application Data

## 5.1 Introduction

Data is important and valuable to individuals and organizations. It is not uncommon to see monetary value assigned to data. This value signifies the cost of replacing it if lost or potential revenue if sold. Additionally, data may carry a hidden cost of lost reputation or loss of an organization's ability to generate revenue if stolen. This makes data a primary target for hackers and frauds. The first step to protect your organization's privacy is to understand the data you need to protect, the main threats, the structure of the protocols used for data in motion, and files you need to protect at rest. Among many standards and regulations on data privacy, the EU General Data Protection Regulation (GDPR) is probably one of the most complete and restricted regulations ever written and is relevant beyond services provided within the European Union. Protect passwords, credentials, and algorithm keys is vital to protect an organization. The main issue is that while your application and systems must know those credentials, they must be hidden to avoid unauthorized access. There are multiple methods to protect your keys, each one with pluses and minuses.

Another aspect of data protection is only communicating with authorized parties and authentication is the feature needed to achieve it. Public Key Infrastructure (PKI) is the architecture used to authenticate unknown servers, such as public internet services. PKI is the standard used for any certificate-based authentication. Once communication is flowing with an authenticated peer, it is essential to encrypt data. Depending on if the traffic flows are directed to, coming from, or flowing through the data center, there are different methods to protect it.

## 5.2 Data Storage and Protecting Data Privacy

You will first look at ways of protecting data at rest through encryption and in motion using Transport Layer Security (TLS), user personal details handling for legal requirements like GDPR, and data anonymization/sanitization for logging and metric collection. Data can be divided into data at rest and data in transit. Data at rest are data stored on physical disks, Flash, or cloud—they are mainly static. Data in motion refers to data being transferred from a device to another device. To protect data, encryption and nonrepudiation technologies should be used to guarantee confidentiality and reliability of transactions and file contents. Data integrity should also be used to guarantee data are not changed at rest or in motion without consent. Data at rest and in motion protections are often necessary to protect individual data privacy according to country regulations and industry security standards. Organization data protection strategies are often strictly dictated by those regulations.

In the document the European Court of Human Rights Guide on Article 8 of the European Convention on Human Rights, you can read the principle of the human right to privacy: "As the Court has consistently held, the concept of private life extends to aspects relating to personal identity, such as a person's name, photo, or physical and moral integrity; the guarantee afforded by Article 8 of the Convention is primarily intended to ensure the development, without outside interference, of the personality of each individual in his relations with other human beings." It means that privacy is equated to other primary human rights. In the US constitution, there is not an explicit amendment related to privacy, but the Supreme Court found that there is a clear right to privacy that can be derived from First, Third, Fourth, Fifth, and Ninth Amendments. There are also industry secrets and private documents critical to an organization's strategy that must not be disclosed, and that information must be strictly protected for the organization interest.

Data can be protected at rest when they are stored in storage devices, SANs, or Cloud services, and they must be protected when they are in motion. To protect the data means to guarantee confidentiality, integrity, and authenticated access to them. Moreover, there are specific regulations about the Personal Identifiable Information (PII) protection that must be considered, depending on the geographical area and the specific industry of the organization collecting them. In 1995, the EU Data Protection Directive was the guideline for PII data protection and was substituted for the more recent GDPR. Another relevant guideline is the National Institute of Standard and Technologies Special Publication 800-122, but many other regulations and standardization documents are available.

### Personal Identifying Information Handling

There are different regulations and guidelines applied in multiple countries and by different regulations authorities regarding PII processes.

The National Institute of Standards and Technology published the Guide to Protecting the Confidentiality of PII and on the 25th of May 2018, European authorities enforced GDPR in the place of Data Protection Directive active since 1995.

The GDPR is the most significant standard in history about PII processing regulations because it applies to Europe as well to any other country that wants to provide services or goods to individuals within the EU, so the number of individuals and organizations affected by GDPR is higher than any other regulations on the same topic.

The definition of PII is similar across different standards and regulations:

* NIST Publication 800-122 definition: "Any information about an individual maintained by an agency, including any information that can be used to distinguish or trace an individual's identity, such as name, social security number, date and place of birth, mother's maiden name, or biometric records; and any other information that is linked or linkable to an individual, such as medical, educational, financial, and employment information."

* GDPR definition: "Personal data means any information relating to an identified or identifiable natural person ("data subject'); an identifiable natural person is one who can be identified, directly or indirectly, in particular by reference to an identifier such as a name, an identification number, location data, an online identifier or to one or more factors specific to the physical, physiological, genetic, mental, economic, cultural or social identity of that natural person"

In general, these definitions mean that data must be linkable to a person to be considered a PII.

In GDPR, some other definitions are essential to managing PII:

* **Consent of the data subject** means any freely given, specific, informed and unambiguous indication of the data subject's wishes by which he or she, by a statement or by clear affirmative action, signifies agreement to the processing of personal data relating to them. That means the owner of the PII must be clearly informed about the use of those data, and they must explicitly consent to that usage. Because of this definition, the famous "cookie banner" on any website active in Europe is mandatory. Using cookies is an automatic gathering of PII, making it mandatory to inform the user accessing the website.

* **Personal data breach** means a breach of security leading to the accidental or unlawful destruction, loss, alteration, unauthorized disclosure of, or access to personal data transmitted, stored or otherwise processed. The main consequence of this definition, linking it to the requirement to inform users about data breaches, means that anytime there is any loss, modification, or PII data theft, users have to be informed, resulting in a potential loss of reputation of the organization victim of the breach.

* **Filing system** means any structured set of personal data that is accessible according to specific criteria, whether centralized, decentralized, or dispersed on a functional or geographical basis. The definition of a Filing system is broad and involves local data storage systems as well as cloud ones, wherever they are installed and active, including outside the EU. This definition implies not only that the EU citizens’ PII must be processed according to GDPR wherever stored in the world, but also that EU organizations are free to store data everywhere in the world if GDPR process principles are guaranteed.

The main principles for processing PII are:

* Transparency
* Legality
* Minimal data gathering
* Time bounded

**Transparency** means that the controller clearly states the scope and reasons behind the PII gathering and manages them accordingly with what the data subject would expect.

**Legality** means that the process is related only to what the data subject explicitly consented, that the PII gathering is necessary because some legal obligation of the controller, or that is required for the legitimate interest of the controller. Of course, this legitimate interest does not have to be in contrast with the fundamental rights of the data subject.

**Minimal data gathering** means that the PII gathered should be only the PII relevant to the process approved by the data subject. The idea behind this principle is that less data gathered means less data can be stolen or lost.

**Time bounded** means that data must be stored only for the reasonable amount of time they are needed for the process. Deleting the data is an effective way to prevent accidental data losses.

### Data Anonymization

Confidentiality also involves anonymous data. It means that it should not be possible to identify to whom that data belongs.

An anonymous set of data requires PII to be removed. Data anonymization is essential not only to avoid data that can be stolen and PII identified, but also prevent operators from binding information to the person it belongs to. Sometimes it is required that the data to anonymize remains unique in the database, so deleting the identity is not the right solution. Another way to keep the information unique without making it readable is to hash the value of the information. Sometimes what is required is data deidentification instead of anonymization. The difference between them is that with anonymization, it is impossible to reverse and identify the person to whom data belongs, while with deidentification, under strictly controlled cases, the person can be reidentified.

Of course, deidentification is intrinsically less secure than anonymization. Still, there are cases where it is essential to identify the person to which data belongs, like in anonymous bank transactions where, under authorities request, the owner of the bank account should be provided.

According to the GDPR the pseudonymization is defined as a "means the processing of personal data in such a manner that the personal data can no longer be attributed to a specific data subject without the use of additional information, provided that such additional information is kept separately and is subject to technical and organizational measures to ensure that the personal data are not attributed to an identified or identifiable natural person." So, the pseudonymization process can include pure anonymization or deidentification.

### Data at Rest Security

Data at rest refers to data stored on devices disk that is not continuously changing. Typically data at rest are permanent user files—operating system files that are constantly changing are not always considered data at rest.

Examples of data at rest are the following:

* File on PC disks
* File on network-attached storage (NAS) or SAN
* File stored on back disks or tapes
* File stored in the cloud

Protecting data at rest is mandatory for most public or private organizations and, in general, falls under regulations.

To protect data by encrypting them, three options are available:

* Encrypting entire disk
* Encrypting files
* Tape encryption

Entire disk encryption or Full Disk Encryption (FDE) is available on most modern operating systems like Windows (BitLocker) or Linux (for example, dm-crypt tool).

Disk encryption can work by decrypting the whole disk whenever an administrator privilege user logs in, and encrypting it at the logout. The main disadvantage of this approach is slower access performances, compared to a cleartext disk access.

With entire disk encryption, user privileges are not considered to choose if a file has to be encrypted or not depending on the user identity. Some encrypt disk systems allow for a recovery password to retrieve data in case a user password is not available.

File encryption is more flexible and precise than FDE. Each user can choose to encrypt all of their files or part of them and can choose if they need to share those files with other users or user groups, allowing them to decrypt those files. With file encryption, it is possible to encrypt only specific files, based on their importance, or encrypt only individual folders.

The main concern about file encryption in the past was performance. This limitation is not relevant for new generation CPUs, but it can be essential to consider for low-end CPU devices.

Tape encryption is a solution to protect backups; the main advantage is that data are encrypted at the hardware level, resulting in fast performance and full volume protection.

A Hardware Security Module (HSM) can be used to store encryption keys and hardware process encryptions. They usually provide temper protection. A specific case of HSM is the cryptocurrency wallet where a portable device acts as an HSM.

### Data in Motion Security

Transport Layer Security (TLS) and its predecessor, Secure Socket Layer (SSL), are cryptographic protocols that provide communications security over a computer network. SSL is frequently used to refer to both protocols.

TLS’s main functionalities are guaranteeing integrity and confidentiality over network communication. It uses symmetric cryptography using a shared key negotiated at the beginning of the session. It is also possible to use TLS for authentication using public key-based authentication involving digital certificates; using authentication can authenticate every single message.

SSL/TLS is the most widely visible use of certificate-based peer authentication. SSL was developed by Netscape in the 1990s to provide secure transactions between web browsers and web servers to support commerce over the internet. SSL became a de facto standard, but was made obsolete by TLS, which is standardized by the IETF. TLS version 1.0 was defined in RFC 2246 in 1999 and provided a standards-based upgrade to SSL version 3.0. TLS continues to evolve, with TLS 1.3 released in August 2018 with RFC 8446. Modern systems implement TLS, but the term SSL is often used interchangeably by IT professionals.

![](/images/devcor/devcor_5_2_1.png)

TLS works using a handshake:

1. The client sends a ClientHello message negotiating the highest TLS version supported and a list of cipher suite supported.

1. The server sends a ServerHello message selecting the highest supported version proposed by the client. The server sends ServerKeyExchange to start the Diffie-Hellman process and the ServerHelloDone message to finish the negotiation phase.

1. The client sends a ClientKeyExchange message to continue with the key exchange process, the type of value sent depends on the cipher suite chosen.

1. The client sends a ChangeCipherSpec message stating the protected communication is starting.

1. The server sends ChangeCipherSpec telling the client the protected communication is starting.

1. The application can exchange data securely.

## 5.3 Storing Application Secrets

Following is an overview of options for storing secrets such as environment, database (DB), configuration files, and platform-specific stores, and why they should be encrypted and centralized, not embedded in the code.

In 2004, Bill Gates predicted the reduction of password usage in future years: "There is no doubt that over time, people are going to rely less and less on passwords. People use the same password on different systems; they write them down, and they just do not meet the challenge for anything you really want to secure."

While the prediction stressed the truth about password usage limitations, lack of security, and challenges to use passwords correctly and appropriately rotate them, time has shown that today, more and more passwords are used. The growing popularity of hosting and cloud services makes passwords vital.

One of the reasons password-based authentication continues to be one of the most commonly used authentication methods, despite its limitations, is that implementation is easy and cost-effective. Nonetheless, managing passwords and application secrets properly is challenging from a security standpoint.

For user passwords to be secure, they need to be heavily complex. They need to be changed often and must be different for each system and service. This demand is probably too challenging to be implemented correctly and unrealistic for the user. While many alternatives were proposed for passwords, the situation is slightly better for application secrets, passwords, and passphrases used by applications to connect to external systems and resources. In the case of application secrets, rotating and complexity strategies can be managed effectively with the right tools and planning.

Application secrets are:

* Credentials used to access to third party systems
* Credentials used to connect to databases
* Credentials used for API calls

Secrets can be used by the application based on the following:

* Direct code storage or through environment variables
* Database storage
* API syncing services
* Encrypted code storage
* Cloud-based secret services
* Cloud-based secret services accessed through an API gateway

Building and deploying applications often require the usage of secrets. They are credentials used to connect to databases or external systems and application programming interface (API) keys.

There are different ways to insert them in the application code, some completely unsecure and others strongly secure: they can be stored directly in the code, on a dedicated organization server, or on cloud-based storage and retrieved through cloud-based key management services in cleartext or using encryption algorithms.

When planning to protect application secrets, the following should be considered:

* There is not a single correct way to protect secrets, because systems and architectures vary, and there is not an approach that is optimal for all situations.

* They are generally distributed, specifically in case of API keys, they must be known on different systems on-premises, on a remote data center, and on cloud services.

* Secrets are typically doubled between a production environment and development one, and it is a bad practice to keep those secrets identical for both environments.

* The organization’s reliance on passwords should be reduced as much as possible, reducing password usage to only when strictly needed.

* Use multifactor authentication when possible and for critical information retrieval.

* Use single sign-on systems when possible, reducing the amount of authentication transactions and reducing the possibility of password sniffing and theft.

### Traditional Secrets Storing Strategies

Secrets can be directly stored in code storage or stored in an environment variable file:

![](/images/devcor/devcor_5_3_1.png)

It is possible to store the secret directly in the code or through variables.

The advantages are as follows:

* It is easy to manage and does not need any external repository, files or systems.
* When storing secrets in variables, the variables file is generally used by other functions and it does not need to be created only to store secrets.

A disadvantage is that anybody on the development team or with access to the source code can know the keys.

Secrets can be stored in a database accessible through other credentialed authentication.

![](/images/devcor/devcor_5_3_2.png)

An advantage is that a single data structure is used to manage secrets, which makes them easy to manage and to rotate.

A disadvantage is that it does not completely solve the credential storage problem. Credentials are needed to access the database; the DB credentials must be managed with one of the other approaches.

Observe an example of an API key syncing service:

![](/images/devcor/devcor_5_3_3.png)

There are cloud services to store application secrets. The access to those services is encrypted and authenticated.

An advantage is that they can be easier to use than a dedicated database, using ready to use functions like password encryption and encrypted API connections.

A disadvantage is that, like in the case of DB storage, an API Key is needed to access those services, without completely solving the issue of the secret storage.

Like the secret stored in the code method discussed previously, there is the secret stored in the code encrypted method, where the key is added encrypted.

![](/images/devcor/devcor_5_3_4.png)

An advantage is that the secret is not available to developers, and it is not enough to have access to the source code to know the secret string

A disadvantage is that, to make the encrypted secret usable by the system, it is necessary to have access to the key central file where the encryption key is available. This file must be protected, making the problem into effectively storing the secret needed to access other secrets

### Cloud-Based Password Manager Services

A more recent approach to application secret storage is through dedicated cloud services. The main idea is to assign to the virtual machine (VM), instance, or service the privileges to access the secret storage service. Having access to just the application code does not allow the developer to access the secret key rotation configuration.

![](/images/devcor/devcor_5_3_5.png)

Secrets rotation is configured independently, with frequency and parameters out of control of the application developers. Most of those services do not write the key value on the application accessed storage. Also, the secrets rotation is easier to manage and typically provided by the cloud service.

Amazon Web Services (AWS) Key Management Service (KMS), Microsoft Azure Key Vault, and Google Cloud KMS are examples of cloud-based secrets management.

Advantages include:

* Easy to used and integrate for services, VMs, or instances on the same cloud environment.

* If the same cloud service provider is used to store data, there is no added risk storing secrets on the same provider.

* Easy to implement a rotation policy for secrets.

Disadvantages include:

* Those services are typically configured through a browser so the secret's security can suffer from browser security risks.

* Access control like Identity and Access Management (IAM) for AWS or Managed Identities for Microsoft Azure can be complex to implement in expanding and dynamic environments.

Another interesting and recent approach is the cloud secret storage dedicated services through an API gateway method. It is similar to the previous approach but uses an API gateway.

![](/images/devcor/devcor_5_3_6.png)

The idea is to allow the application instance to request access to resources through an API gateway. The request is authenticated using an identity-based request. Credentials are added to the request by the gateway acting as an authentication proxy, which sends the requested resources back to the authorization application.

Advantages include:

* The secret is never stored and sent to the application, so it is impossible for developers to access the credentials and it is not enough to have access to the source code to retrieve secrets.

* Easy to implement a rotation policy for secrets

A disadvantage is that, depending on the cloud service provider, it can be complex to implement the service, and a certain level of knowledge about the cloud system used is required.

## 5.4 Public Key Infrastructure

Now you will look at components of a working public-key infrastructure (PKI) system, its benefits and challenges, including initial certificate authority (CA) list, CA chains, and certificate revocation list (CRL) handling.

A PKI facilitates highly scalable trust relationships. PKIs can be further scaled using a hierarchy of CAs, with a root CA signing the identity certificates of subordinate CAs. For simplicity, this discussion will present a single CA PKI.

The PKI is an example of a trusted third-party system. The basis of the trust is the CA's public key. All systems that leverage the PKI must have the CA's public key. They get this from the CA's own identity certificate. The CA's own identity certificate is unique as it is self-signed. For many systems, the distribution of CA certificates is handled automatically. For example, commercial web browsers come with a set of public CA root certificates preinstalled, and organizations push their public CA root certificate to clients through various software distribution methods. In some instances, particularly when a system needs to enroll with a PKI to obtain an identity certificate for itself, the CA certificate must be requested and installed manually. When this is done, it is advisable to use an out-of-band method to validate the certificate. For example, the CA administrator can be contacted via the phone to obtain the fingerprint of the valid CA identity certificate. The goal is to verify that the CA certificate that was received was the authentic CA certificate containing the authentic CA public key and not a certificate that is provided by an attacker containing the attacker's public key.

![](/images/devcor/devcor_5_4_1.png)

To obtain an identity certificate, a system administrator will enroll with the PKI. The first step is to obtain the CA's identity certificate. The next step is to create a Certificate Signing Request (CSR). The CSR contains the identity information that is associated with the enrolling system. This can include data such as the system name, the organization to which the system belongs, and location information. Most importantly, the enrolling system's public key is included with the CSR. Depending on the circumstance, the CA administrator may need to contact the enroller and verify the data before the request can be approved. If the request is approved, the CA will take the identity data from the CSR, and add in the CA-specified data, such as the certificate serial number, the validity dates, and the signature algorithm, to complete the X.509v3 certificate structure. It will then sign the certificate by hashing the certificate data and encrypting the hash with its private key. The signed certificate is then made available to the enrolling system.

### Authentication Using Certificates

It is important to understand that the CA is not involved in the certificate validation process. Systems that need to validate the identity certificate of other systems will have the root CA certificate. They will use the CA's public key to validate the signature on any certificate they receive. It is also important to understand that the certificate does not so much identify the entity of the peer as identify the valid public key of the peer. To be sure that the peer is the entity that is identified in the certificate, a system must challenge the peer to prove that it has the private key that is associated with the validated public key. For example, a message can be encrypted with the validated public key and sent to the peer. If the peer can successfully decrypt the message, then the peer must have the associated private key and therefore is the system that is identified by the digital certificate.

![](/images/devcor/devcor_5_4_2.png)

### Certificate Revocation

Digital certificates can be revoked if keys are thought to be compromised or if the business use of the certificate calls for revocation, such as if VPN access privileges have been terminated. If keys are thought to be compromised, generating new keys forces the creation of a new digital certificate, rendering the old certificate invalid and a candidate for revocation. On the other hand, a consultant might obtain a digital certificate for VPN access into the corporate network only for the duration of the contract.

Certificate revocation is also a centralized function, providing "push" and "pull" methods to obtain a list of revoked certificates—frequently or on demand—from a centralized entity. In some instances, the CA server acts as the issuer of certificate revocation information.

![](/images/devcor/devcor_5_4_3.png)

Several methods can be used to check for certificate revocation. Currently, the most prevalent methods are CRLs and Online Certificate Status Protocol (OCSP). The table lists benefits and limitations of each protocol:

| Protocol | Description                                                                                                                                                                                                                                                       |
|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| CRL      | A list of revoked certificate serial numbers is distributed as a time-stamped, CA-signed file. PKI entities regularly poll the CRL repository to receive the current CRL. There is a window of opportunity for attackers while the new CRL is not yet propagated. |
| OCSP     | Revocation information is immediately pushed to an online database. Entities can query the OCSP server at any time to check for validity of the received certificate.                                                                                             |

### Hierarchical Multiple CA Infrastructure

![](/images/devcor/devcor_5_4_4.png)

A PKI can be set up in a hierarchical framework to support multiple CAs.

At the top of the hierarchy is a root CA, which holds a self-signed certificate. The trust within the entire hierarchy is derived from the Rivest, Shamir, and Adleman (RSA) key pair of the root CA. The subordinate CAs within the hierarchy can be enrolled with either the root CA or with another subordinate CA. Multiple tiers of CAs are configured by either the root CA or with another subordinate CA. Within a hierarchical PKI, all enrolled peers can validate the certificate of one another if the peers share a trusted root CA certificate or a common subordinate CA.

Multiple CAs provide users with added flexibility and reliability. For example, subordinate CAs can be placed in branch offices while the root CA is at the office headquarters. Different granting policies can be implemented per CA, so you can set up one CA to automatically grant certificate requests while another CA within the hierarchy requires each certificate request to be manually granted.

It is easy to implement a rotation policy for secrets:

* Large and very active networks in which many certificates are revoked and reissued. A multiple tier CA helps to control the size of the CRLs.

* When online enrollment protocols are used, the root CA can be kept offline except to issue subordinate CA certificates. This scenario provides added security for the root CA.

### Use Case TLS

TLS uses PKI to authenticate peer systems and public key cryptography to facilitate the exchange of session keys that are used to encrypt the SSL session. Many applications use TLS to provide authentication and encryption. The most widely used application is HTTPS. Other well-known applications that were using poor authentication and no encryption were modified to be transported within TLS. Examples include SMTP, LDAP, and POP3.

The figure below depicts the steps that are taken in the negotiation of a new TLS connection between a web browser and a web server. SSL and TLS support a combination of cryptographic algorithms to provide the same services at various levels of risk. The figure illustrates the cryptographic architecture of SSL and TLS, based on the negotiation process of the protocol.

![](/images/devcor/devcor_5_4_5.png)

SSL and TLS are open enough to allow multiple cipher suites, and they are flexible enough to support more in the future, provided they adhere to protocol specifications. The structure and use of the cipher suite concept is defined in the documents that define the protocol (RFC 8446 for TLS version 1.3). This RFC defines mandatory cipher suites that must be implemented by all TLS-compliant applications. The only mandatory cipher suite is TLS_RSA_WITH_AES_128_CBC_SHA, including RSA for authentication, AES for confidentiality, and SHA for integrity and digital signatures.

Web browsers, using a built-in store of root CA certificates, hide the details of validating TLS sessions from the user unless there is an issue with the validation process. Even with successful validation, users can drill down into the details of the connection. Here are the certificate details that can be examined within a web browser.

1. When the connection to the server is first initialized, the server provides its PKI certificate to the client. This contains the public key of the server and is signed with the private key of the CA that the owner of the server has used.

![](/images/devcor/devcor_5_4_6.png)

2. To verify that the PKI certificate can be trusted, the signature of the CA in the certificate is checked. If the signature can be traced back to a public key that already is known to the client, the connection is considered trusted.

![](/images/devcor/devcor_5_4_7.png)

3. Now that the connection is trusted, the client can send encrypted packets to the server.

![](/images/devcor/devcor_5_4_8.png)

4. As public/private key encryption is one-way encryption, only the server can decrypt the traffic by using its private key.

![](/images/devcor/devcor_5_4_9.png)

Using the Certification Path tab, you can view the path from the selected certificate to the CAs that issue the certificate. Before a certificate is trusted, Windows must verify that the certificate comes from a trusted source. This verification process is called path validation. Path validation involves processing public key certificates and their issuer certificates in a hierarchical fashion until the certification path terminates at a trusted, self-signed certificate. Typically, this is a root CA certificate. If there is a problem with one of the certificates in the path, or if Windows cannot find a certificate, the certification path is considered a nontrusted certification path. A typical certification path includes a root certificate and one or more intermediate certificates. By clicking **View Certificate**, you can also learn more about the certificates for each CA in the path.

### Browser Security Warning

If there are any issues that are associated with validating the certificate of a web server, web browsers will display a security warning to the user. Unfortunately, many users will ignore the security warnings and proceed with the connection under potentially hazardous conditions. The three most common issues associated with security warnings are as follows:

* **Hostname/Identity mismatch:** URLs specify a web server name. If the name specified in the URL does not match the name specified in the server's identity certificate, the browser will display a security warning. Hence, DNS is critical to support the use of PKI in web browsing. This may be benign under certain circumstances: for example, if the user knows the IP address of the server and specifies the IP address instead of the hostname. However, attackers may register domain names that look very similar to authentic domain names. The browser will detect the mismatch, but the user may look at the certificate and think that it is acceptable.

* **Validity date range:** X.509v3 certificates specify two dates, not before and not after. If the current date is within those two values, there will be no warning. If it is outside the range, the web browser displays a message. The validity date range specifies the amount of time that the PKI will provide certificate revocation information for the certificate. When certificates expire, it facilitates the periodic changing of public/private key pairs on web servers. Expired certificates may simply be the result of administrator oversight, but they may also reflect more serious conditions.

* **Signature validation error:** If the browser cannot validate the signature on the certificate, there is no assurance that the public key in the certificate is authentic. Signature validation will fail if the root certificate of the CA hierarchy is not available in the browser's certificate store. A common reason may be that the server uses a self-signed certificate. Many systems allow the creation of self-signed certificates to avoid the complexity or expense of joining a PKI. The use of self-signed certificates, however, puts the responsibility of certificate verification on the user. This is not optimal from a security perspective. Another possible cause of a signature verification error is that the certificate has been tampered with—for example, if an attacker has replaced the server's real public key with the attacker's public key.

reasons to use a hierarchical CA infrastructure
"to control the CRL size" and "to increase security of the root CA." Using root CA and intermediate CA reduces the size of the CRL of the issuing CA. The root CA can be kept offline after issuing the subordinate certificate, making it more secure.

## 5.5 Configuring Public Key Certificates for Applications

Following is an overview of how to obtain a signed public key certificate, including how to create cert file, create a CSR, submit to CA, cover OpenSSL and Windows, and additional considerations, such as handling intermediate certificates and certificate bundling. Public key certificates are an integral part of the TLS protocol. Sometimes they are autogenerated in devices where authentication is not needed, and TLS is just used for confidentiality reasons. When server authentication is required, the certificate must be signed by a known CA. In the case of Enterprise internal services, this CA can be internal, while in the case of a public one, the signing CA has to be well known by the user browser, resulting in a required signed certificate from a known public CA. To scale the PKI architecture, intermediate CAs are involved in creating a certificate chain that can be managed through effective certificate bundling.

To configure Public Key Certificates, follow these steps:

1. Generate the Private Key
1. Generate the Certificate Signing Request (CSR)
1. Display and verify the certificate (Optional)
1. Enrollment
1. Install the certificate

### Configuring Public Key Certificates on Apache

To configure a Public Key Certificate using Apache web server follow these 5 steps:

Step 1: Generate the private key

```bash
admin@web_server:~$ openssl genrsa -aes192 -out MYPRIVATEKEY.key 4096
Generating RSA private key, 4096 bit long modulus
...++
..++
e is 65537 (0x10001)
Enter pass phrase for MYPRIVATEKEY.key:
Verifying - Enter pass phrase for MYPRIVATEKEY.key:
```
Generate an RSA key pair that is 4096 bits long, encrypting the generated key with AES-192 algorithm with an encryption key that is the entered passphrase. The private key is stored in the MYPRIVATEKEY.key file. RSA and DSA key pairs can be generated, and they can be protected using 3DES, AES, SEED, and other algorithms.

Step 2: Generate the CSR

```bash
admin@web_server:~$ openssl req -new -key MYPRIVATEKEY.key -out MYCSR.csr
Enter pass phrase for MYPRIVATEKEY.key:
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
<... output omitted ...>
```
Generate a CSR using the private key created in the previous step and configure the certificate attributes:

* Country
* State or province
* City
* Organization Name
* Organizational Unit Name
* Common name
* Email address
* Challenge password
* Optional company name

Not all attributes are mandatory and not all of them are recognized by all enrollment services.

Step 3: Display and verify the CSR (Optional)

```bash
admin@web_server:~$ cat MYCSR.csr 
-----BEGIN CERTIFICATE REQUEST-----
MIIFSjCCAzICAQAwgcExCzAJBgNVBAYTAkNIMQ8wDQYDVQQIDAZUaWNpbm8xDzAN
BgNVBAcMBkx1Z2FubzEXMBUGA1UECgwOTXlPcmdhbml6YXRpb24xHzAdBgNVBAsM
FkluZm9ybWF0aW9uIFRlY2hub2xvZ3kxIzAhBgNVBAMMGm15b3JnYW5pemF0aW9u
<... output omitted ...> 
EdyJ9hQC7Uvs5c+4KtNA8vSYa1NkTUABIeaDOiHT6gwPfyaBxCGPoiC2PbWqvF2t
DgF0hpSo2LZMzCDp2t8=
-----END CERTIFICATE REQUEST-----
```

Display the generated CSR. You can copy the output to verify that it's correctly generated and to enroll the certificate.

![](/images/devcor/devcor_5_5_1.png)

To verify that the CSR is correctly generated and check for any warnings, there are multiple online tools, one is the Digicert Check CSR tool, accessible at https://ssltools.digicert.com/checker/views/csrCheck.jsp

Step 4: Enrollment to request the certificate

The enrollment process varies depending on the certification authority, but the general concept is to verify the identity of the requesting organization offline and issue the CA signed certificate through different methods:

* Download link
* Email
* Simple Certificate Enrollment Protocol (SCEP)

To complete the enrollment process, the requester typically must provide information like:

* Time range validity
* Number of servers
* The Organization’s identity
* The common name, something like www.myorganization.com
* An email contact address
* A CSR is generated from the server you need to secure.

Automatic CSR enrollment is possible and is usually used for network devices. One automatic enrollment method is the Simple Certificate Enrollment Protocol (SCEP), an IETF internet draft. Using SCEP, a network device can automatically send the CSR to a CA, obtain, and install a certificate. SCEP is a useful option where using internal CA for the enrollment process and to verify the real identity of the requester offline is not needed.

Once the certificate is created, it can be downloaded or it is sent by email.

Step 5: Install the certificate

* Modify the Apache configuration file accordingly (httpd.conf or ssl.conf)

```bash
#Points to the certificate file, if the certificate is encrypted a passphrase is required
SSLCertificateFile /PATH_TO_CRT/public.crt  

#Points to the key file
SSLCertificateKeyFile /PATH_TO_KEY/private.key  

#Points to SSL chain bundle file
SSLCertificateChainFile /PATH_TO_BUNDLE/bundle.crt
```

### Configuring Public Key Certificates on IIS

To configure a Public Key Certificate using a Microsoft IIS web server, follow these steps:

Click **Tools > Internet Information Services (IIS) Manager.**

Step 1 and step 2: Generate Private Key and CSR

![](/images/devcor/devcor_5_5_2.png)

Click **Server Certificates.**

![](/images/devcor/devcor_5_5_3.png)

Click **Create Certificate Request...**

![](/images/devcor/devcor_5_5_4.png)

Fill the Distinguished Name Properties form. Click **Next.**

![](/images/devcor/devcor_5_5_5.png)

Choose the Cryptographic service provider and the Bit Length. Click **Next.**

![](/images/devcor/devcor_5_5_6.png)

Specify the CSR name and click **Finish.**

![](/images/devcor/devcor_5_5_7.png)

Open the CSR file with a text editor to verify the output format.

Step 3: Display and verify the CSR (Optional)

![](/images/devcor/devcor_5_5_8.png)

**Note:** The output in the example has been omitted for display purposes.

Step 4. Enrollment.

The enrollment steps depend on the Issuing certificate organization used.

Click **Tools > Internet Information Services (IIS) Manager > Server Certificates > Complete Certificate Request...**

Install the certificate

![](/images/devcor/devcor_5_5_9.png)

Choose the CRT file issued by the certificate authority. Assign a Friendly name to the certificate. Click OK.

![](/images/devcor/devcor_5_5_91.png)

Choose the website you want apply the certificate to and click **Bindings...**

![](/images/devcor/devcor_5_5_92.png)

Click add. Choose the **HTTPS** type. Choose the **SSL** certificate and click **OK**.

![](/images/devcor/devcor_5_5_93.png)

### Managing Certificate Bundling

When intermediate certificates are in use, the browser or any other client using certificates (that is, an IKEv2 client) needs to know the whole certificate chain to verify the validity of an SSL certificate. An effective way to inform the client of the whole chain is using certificate bundles and distributing them during the TLS handshake.

A certificate bundle may contain:

* Root CA Certificate
* Certificate used to create a digital signature
* Intermediate CA certificates
* CRL

It is not suggested to embed the Root CA certificate in the bundle. Even if the bundle contains the Root CA certificate, the browser cannot verify its validity if the Root CA certificate is not installed in the browser before the handshake. A CRL should only be added to the bundle when there is no other way to distribute it. Using a bundle containing a CRL forces you to update the bundle constantly.

There are multiple formats used to create certificate bundles, depending on the format of the certificate. Using a privacy-enhanced mail (PEM) certificate to create bundles is popular due to the simple text format of those certificates. To create a PEM certificate bundle, it is enough to copy each certificate in the opposite issuing order in a text file, typically assigning a .pem extension.

To create a PEM certificate bundle, copy certificates in reverse order, omitting the final SSL certificate:

- Root.crt is the root certificate
- Intermediate1.crt is the intermediate certificate 1
- Intermediate2.crt is the intermediate certificate 2
- My_certificate.crt is the final requested SSL certificate

```bash
# Create a certificate bundle mychain.ca-bundle in Linux
cat Intermediate2.crt  Intermediate1.crt Root.crt > mychain.pem

# Create a certificate bundle mychain.ca-bundle in Windows
copy Intermediate2.crt +  Intermediate1.crt + Root.crt mychain.pem
```

## 5.6 Applying End-to-End Encryption for APIs

Now you will examine the different approaches to north-south and east-west traffic encryption.

API traffic flows from on-premises networks to the internet, to a public cloud, or sometimes only intradata center. Each communication segment should be secured. Most of the time, regulations or industry standards require confidentiality. There is a difference in protecting client to data center traffic (north-south) and intradata center traffic (east-west).

The east-west traffic varies depending on if it involves physical servers, VMs, or containers. Traditionally, there is more attention for the north-south traffic protection traversing public networks. Still, recent trends show how, in many implementations, the east-west traffic volume is more than the north-south volume, and it is vital to protect and encrypt.

The reasons to protect and, more specifically, encrypt API traffic are, for example, business loss—a security breach can be based on attacking an unsecured API and theft of credentials or sensitive information. Injection attacks are only two examples of many possible dangerous outcomes of an unsecured API traffic. A security breach can easily result in loss of revenue due to the impact of those happenings business continuity.

**Compliance issues:** Security requirements depend on the industry sector or regulations under which an application is operating; there are security requirements, and to not protect the API can result in the authorities denying operation due to a lack of compliance.

**Reputation loss:** Under GDPR data processing and other standards and regulations, security breaches must be reported to users. Sometimes those happenings are strictly related to weak security of the code and communications protocols used by the API, resulting in a loss of reputation of the victim organization.

**Higher infrastructure costs:** Using cloud services based on a pay-on-the-go model makes potential security breaches even more impactful because a malicious code using resources from the cloud service usage can make costs rise rapidly.

The API communications should be protected north-south and east-west to obtain a full end-to-end encryption.

Typically, more attention is given to north-south traffic protection, and many applications protect mainly their communication protocol through the internet. Traditionally, data centers protect their perimeter using Cisco Next Generation Firewalls (NGFWs), Intrusion Preventions Systems (IPSs) and other dedicated security systems. The main limitation in protecting only perimeters and north-south communication is that, according to security reports, more than half of the security breaches are caused by internal systems. This means that protecting east-west API communications is essential. Considering the microservice architecture where monolithic applications are split into smaller components, it is expected east-west traffic will grow in the future. Moreover, east-west traffic it is not only internal to the physical or virtual data centers, but it is also interdata center traffic.

Data must be encrypted before transmission when they move from the client to a data center or to a cloud service.

There are different option protecting north-south traffic like:

* Using traditional TLS authentication and encryption or Mutual TLS (MTLS) authentication
* Using TLS or IPsec from the client to an API gateway or NGFW
* Using a cloud-based VPN service
* Using dedicated cloud connections

### North-South Traffic Encryption—Traditional TLS and MTLS

TLS is a popular, easy to implement choice to protect API traffic; it makes it easy to authenticate the server using certificates followed by an encrypted session. What typically happens is that only the client authenticates the server while the server trusts or does not verify the client identity. However, in an API-based architecture, the client role can be limited to a specific set of systems that can interact with the server and it might be a requirement to prevent anyone other than approved clients to connect to the APIs.

![](/images/devcor/devcor_5_6_1.png)

The idea behind MTLS is to authenticate through certificates on both sides. In essence, each side must present certificates to be trusted and verified by the other one.

### North-South Traffic Encryption—TLS or IPsec to API Gateway or NGFW

The API gateway or a NGFW acts as a proxy to connect clients to services indirectly. The API gateway features are related to authentication and are a central point of security policy enforcement. The main advantage of using an API gateway is in the simplification and unification of code used to manage different requests for different services.

![](/images/devcor/devcor_5_6_2.png)

Without a gateway, each back-end service, microservice, VM, or instance must manage security independently. More complicated codes mean more bugs, more errors, and, in general, more complexity that can result in a lack of security.

### North-South Traffic Encryption—Cloud-Based VPN Service

The client can connect to the cloud using Cloud VPN services. The connections go from an on-premises host to a data center, VM, or cloud service. The VPN is typically implemented based on IPsec, guaranteeing integrity, authentication, and encryption.

![](/images/devcor/devcor_5_6_3.png)

All major cloud providers offer a VPN service, some using a VPN virtual gateway, others using a VPN service. The VPN can be established through a router or firewall to the cloud or data center. The main advantage of using a dedicated device as a VPN gateway is that different clients can share the same VPN connection without configuring multiple VPNs, but it is crucial to take into account that the traffic going from the client to the on-premises router is not encrypted; for this reason it is suggested to use TLS where possible in conjunction with IPsec VPNs.

### North-South Traffic Encryption—Dedicated Cloud Connections

The traditional API connections to the public cloud go through a public network; with dedicated connections, it is possible to use a private network to connect the cloud. It is a good option for preserving data to pass through the public internet.

![](/images/devcor/devcor_5_6_4.png)

Even if the north-south traffic is not passing through the public network, it is highly recommended to protect it with IPsec or TLS. One of the main reasons is that the dedicated cloud connections are mostly services provided by an ISP in conjunction with the cloud service provider, and data confidentiality is essential when passing through a third-party network.

### East-West Traffic Encryption

East-west traffic refers to traffic between servers belonging to the same or different data centers. Most of time, the security is only perimetral, giving an attacker that bypasses the edge security protection a free field to eavesdrop on data and easily understand which internal applications are used and how they work.

To protect the east-west traffic, three approaches are available:

* TLS or MTLS between services
* Service mesh segmentation service
* Dedicated container firewall

The usage of TLS is similar to north-south traffic protection, implementing authentication and encryption at Layer 7.

### East-West Traffic Encryption—Service Mesh

Because applications are often broken into microservices, managing this multitude of service connections can be complicated. A service mesh is not just a connection between services, it is a mesh of connections between API proxies for microservices, creating an abstract network. It is a dedicated architectural layer to make managing service-to-service communications more secure and more comfortable.

![](/images/devcor/devcor_5_6_5.png)

Popular service meshes are Linkerd and Istio, despite some differences in how they manage the mesh. They both use TLS by default and allow for external CA root certificate support.

When using service meshes, some limitations are in place:

* ICMP and UDP are not supported
* Low visibility on policy violations
* Cannot inspect traffic for allowed connections (for example, injection of SQL)

### East-West Traffic Encryption—Dedicated Container Firewall

Because containers are extremely dynamic, stopped and started continuously, it is challenging to protect them on the network level through a gateway. Moreover, containers often use open source software, and the vulnerabilities are not known or completely understood by the engineer implementing the internal architecture.

The main features of a container firewall are:

* Behavioral analysis based on policies
* Allowed list rules
* Container connection protection
* Orchestrator container integration
* Data loss prevention (encryption)

Compared with NGFW, there is no Layer 7 application awareness and IPS features. Moreover, they cannot implement identity-based firewalling.

# 6 Securing Web and Mobile Applications

## 6.1 Introduction

Modern web and mobile applications require security to be considered from the beginning to ensure the highest level of privacy and security on the web. This result can be achieved by using trusted open standards and implementing well scrutinized and best practice solutions. The Open Web Application Security Project (OWASP) is a worldwide, not-for-profit organization focused on improving the security of software. This organization is responsible for the OWASP Top 10, which is a powerful awareness document for web application security that represents a wide consensus about the most critical security risks for web applications, along with effective countermeasures for dealing with them. You can use this document as a guide for building and verifying secure software and a tool to help train developers about application security. After you familiarize yourself with the Top 10 list, with emphasis on injection attacks, you will further explore the most prevalent security flaws in web and mobile applications. You will get familiar with the OAuth authorization framework and dive into the three-legged authorization flow theory.

## 6.2 OWASP Top 10

The OWASP Top 10 is a list of the 10 current and most dangerous web application security flaws. This application security standard is published and maintained for free by the OWASP Foundation, which came online in 2001. OWASP provides free and open application security tools, standards, books on secure development, testing and code reviews, presentations, videos, and more. All OWASP tools and content are free to use.

The OWASP Top 10 list was originally published as a tool to raise awareness but has become the de facto security standard for web application development. The document is published in English, French, Chinese, German, Hebrew, Japanese, Korean, Portuguese, Spanish, and Russian. It was initially published in 2004 and the current version is from 2017.

This list includes the most dangerous vulnerabilities and aims to raise awareness toward these threats. Since the web security field changes so rapidly, this list is regularly updated.

### OWASP Risk Rating Methodology

The risk rating system for the OWASP Top 10 is based on the OWASP Risk Rating Methodology.

For each Top 10 category, you can estimate the typical risk that each weakness introduces by looking at the common likelihood factors and impact categories for each common risk.

The following table is a summary of the 2017 Top 10 Application Security Risks and the risk factors assigned to each risk. The risk factors (cells in color) have been determined based on the available statistics and the experience of the OWASP team. These factors are added only as an illustration of the subject and are not in any way an actual assessment of an organization.

![](/images/devcor/devcor_6_2_1.JPG)

Review the table. If you look at the header row, you can see these four categories:

* Exploitability
* Weakness Prevalence
* Weakness Detectability
* Technical Impact

Each item in the OWASP Top 10 is assigned a score across these four categories by OWASP.

The Threat Agents and Business Impact categories, which you can find in the table header row as well, are left to the user to assess as they are application- and business-specific. If you would like to understand these risks for a specific application, you should consider your application and the specific threat agents and business impacts that may be problematic in your case. Perhaps a severe software weakness may not present a serious risk if there are no threat agents in a position to perform the necessary attack.

Now, how is the scoring done?

The scoring is done by following the OWASP Risk Rating Methodology, which is calculated as:

![](/images/devcor/devcor_6_2_2.png)

The Likelihood and Impact factors are calculated by following a 6-step process:

* **Identifying a Risk:** Identify the actual risk and the vectors. This is application- and business-specific.

* **Factors for Estimating Likelihood:** Skill level, motive, opportunity, size, ease of discovery, ease of exploit, awareness, and intrusion detection.

* **Factors for Estimating Impact:** Loss of confidentiality, loss of integrity, loss of availability, loss of accountability, financial damage, reputation damage, noncompliance, and privacy violation.

* **Determining Severity of the Risk:** Once the likelihood and impact are known, the severity can be calculated.

* **Deciding What to Fix:** Normally prioritized by the severity score so that items of most importance are addressed first.

* **Customizing Your Risk Rating Model:** The business-specific impact stems directly from the technical impact; however, a complete understanding of the business and what criticality is assigned to specific data and systems is needed for this assessment.

**Note:** Steps Deciding What to Fix and Customizing Your Risk Rating Model do not apply directly to the OWASP Top 10 and are intended to be used by organizations utilizing this methodology for their own use.

The following figure is a symbolic graphical representation of the risk calculation for *A6:2017-Security Misconfiguration*.

![](/images/devcor/devcor_6_2_3.png)

Keep in mind that the OWASP Top 10 approach does not take into the account the following:

* The likelihood of the threat agent.
* Any of the specific technical details associated with your application.
  * This factor would significantly affect the overall likelihood of an attacker finding and exploiting a vulnerability.
* The actual impact on your business.
  * Your organization will have to determine how many security risks from the application and application programming interfaces (APIs) the organization is willing to accept. The purpose of the OWASP Top 10 is not to do the risk analysis for you.

### OWASP Top Risks

Now you will examine the far-left side of the previous table in depth—the risks. The OWASP Top 10 list covers the following risks:

![](/images/devcor/devcor_6_2_4.JPG)

Here is a detailed explanation of the risks:

* Injection
  * Injection attacks, such as Structured Query Language (SQL) injection, operating system command, and Lightweight Directory Access Protocol (LDAP) injection, occur when untrusted data is sent to an interpreter as part of a command or query. The attacker's hostile data can trick the interpreter into executing unintended commands or accessing data without proper authorization. Injection can come from almost any source of data: external users, environment variables, parameters, internal and external web services. Injection can result in data loss, data corruption, or, at worst, an attacker gaining the control of the entire system.
    * **Prevention suggestion:** Injection attack vulnerabilities are easy to detect in code and can be detected with automatic tools. It is also good practice to keep user data separate from commands and queries.

* Broken Authentication
  * Application functions related to authentication and session management are often implemented incorrectly, allowing attackers to compromise passwords, keys, or session tokens, or to exploit other implementation flaws to assume other user's identities temporarily or permanently. The prevalence of broken authentication is as wide as the prevalence of authentication itself. Session management and persistence is the core of all modern authentication, authorization, and access control systems. Authentication is often seen as the keys to the kingdom. Successful exploitation of broken authentication can lead to wide financial, reputational, and physical damage.
    * **Prevention suggestion:** Where possible, use existing, well-tested, up-to-date frameworks.

* Sensitive Data Exposure
  * Many web applications and APIs do not properly protect sensitive data, such as financial or credit card information. Attackers may steal or modify such weakly protected data to conduct credit card fraud, identity theft, or other crimes. Sensitive data may be compromised without extra protection, such as encryption at rest or in transit, and requires special precautions when exchanged with the browser. This attack is the most prevalent and impactful attack as at the time of developing this course (2019). The most common flaw seen is not using encryption when it could/should be–most often for persistent data at rest. Failure is typically total, leading to full exposure of the data intended to be protected.
    * **Prevention suggestion:** Be careful to encrypt all sensitive data with secure protocols.

* XML External Entities (XXE)
  * Many older or poorly configured XML processors evaluate external entity references within XML documents. External entities can be used to disclose internal files using the file Uniform Resource Identifier (URI) handler, internal file shares, internal port scanning, remote code execution, and denial of service attacks. XXE Flaws are commonly used to extract sensitive data, launch denial of service attacks, and can potentially be used for code execution.
    * **Prevention suggestion:** Implement whitelisting server input validation or filtering to prevent hostile data within XML documents, headers, or nodes.
    * The following code demonstrates a malicious message that tries to access a local file containing the list of users.

      ```bash
      <!DOCTYPE dt [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
      <document> <element>&xxe;</element>
      </document>
      ```
* Broken Access Control
  * Restrictions on what authenticated users can do are often not properly enforced. Attackers can exploit these flaws to access unauthorized functionality and/or data, such as access other user's accounts, view sensitive files, modify other user's data, change access rights, and so on. Because of the manual nature of access control enforcement and detection, weaknesses and misconfiguration are common.
    * **Prevention suggestion:** With exception of public resources, deny by default.

* Security Misconfiguration
  * Security misconfiguration is the most commonly seen issue. This issue is commonly a result of insecure default configurations, incomplete or ad hoc configurations, open cloud storage, misconfigured HTTP headers, and verbose error messages containing sensitive information. Not only must all operating systems, frameworks, libraries, and applications be securely configured, but they must be patched/upgraded in a timely fashion. It is important to remember that misconfigurations can be easily detected with automated tools.
    * **Prevention suggestion:** You can set up an automated process to verify the effectiveness of the settings and configurations in your environment.

* Cross-Site Scripting (XSS)
  * XSS vulnerabilities occur whenever an application includes untrusted data in a new web page without proper validation or escaping or updates an existing web page with user-supplied data using dynamically generated HTML or JavaScript. XSS allows attackers to execute scripts in the victim's browser that can hijack user sessions, deface web sites, or redirect the user to malicious sites. This risk is the second most prevalent issue in the Top 10 list. Depending on the type of XSS, the impact can range from moderate to severe.
    * **Prevention suggestion:** A separation of untrusted data from active browser content.

*  Insecure Deserialization
   *  Insecure deserialization often leads to remote code execution. Even if deserialization flaws do not result in remote code execution, they can be used to perform attacks, including replay attacks, injection attacks, and privilege escalation attacks. Exploiting this aspect is not very easy, as exploits almost always need to be crafted on a case-by-case basis. Some tools can detect deserialization issues, but mostly you need to do it manually. Deserialization issues present an emerging threat with increasing incidence and was added based on industry feedback rather than observations.
      *  **Prevention suggestion:** Implement integrity checks on serialized objects.
      *  The following code shows an exploitable feature of deserialization library allowing object instantiation.

      ```bash
      class SomeClass:
         pass
      from yaml import load
      data = load("!!python/object:__main__.SomeClass\nval: 10")
      print(data.val)
      ```

* Vulnerable Components
  * Components, such as libraries, frameworks, and other software modules, run with the same privileges as the application. If a vulnerable component is exploited, such an attack can facilitate serious data loss or server takeover. Applications and APIs using components with known vulnerabilities may undermine application defenses and enable various attacks. Depending on vulnerability and components, such these vulnerabilities and attacks can lead to minor or critical impacts.
    * **Prevention suggestion:** Keep track of which tools, components, versions you are using internally and subscribe to alerts for security vulnerabilities related to them.

* Insufficient Logging & Monitoring
  * Insufficient logging and monitoring, coupled with missing or ineffective integration with incident response, allows attackers to further attack systems, maintain persistence, pivot to more systems, and tamper, extract, or destroy data. Most breach studies show the time to detect a breach is over 200 days, typically detected by external parties rather than internal processes or monitoring.
    * **Prevention suggestion:** Testing and detectability here can and should be executed using pen testing and log reviews.

**Note:** There are several different ways to tackle the listed risks. The suggestions above may serve as a quick-start guide, however, there are many possible solutions to tackle each of these problems.

## 6.3 Injection Attacks and Data Validation

Injection attacks are very prevalent and potentially devastating to businesses. They are classified as the number one web application security risk in the OWASP Top 10. In an injection attack, an attacker adds malicious input to a program. This input gets processed as part of a command or query and alters the execution of that program. Injection attacks are among the oldest, simplest, and most dangerous attacks that are aimed at web applications. This attack type often leads to data loss, data theft, and denial of service (DoS); however, the most important part is that it leads to system compromisation.

Injections come in many forms. If an application requires user input, this input can be exploited, unless the developer takes the necessary precautions to protect it.

SQL and XSS injections are the most common injection attacks but they are not the only ones.

The following list defines all the common injection attack types:

* SQL injection
* XSS injection
* LDAP injection
* Code injection
* CRLF injection
* Email header injection
* Host header injection
* Operating system command injection
* XPath injection

### SQL Injections

The most prevalent subtype of injections is the SQL injection. With SQL injection attacks, the attacker inserts and executes a malicious SQL code that grants the attacker complete control over the application's database. The attacker can inject SQL commands that can read, write, or modify data from that database. This attack can lead to unauthorized access to sensitive data, data loss, DoS, and it can even compromise the entire system.

![](/images/devcor/devcor_6_3_1.JPG)

An SQL injection occurs when an application is not able to sanitize untrusted data in a database query. At this step, an attacker can craft some SQL commands to trick the application into instructing the database to execute the new SQL commands.

Applications communicate with databases with SQL queries. For example, the SQL SELECT statement query allows the user to choose data and return it from the database to an application.

An example of a simple SELECT SQL query:

```bash
login_query = 'SELECT * FROM USERS WHERE USER = ’ + user_name
```

If the user gives the input as **user_name = "' OR 1=1"**, the query returns all users in the database because 1=1 always evaluates as true—rendering the query effectively as **'SELECT * FROM USERS’**.

The SQL language is inherently injectable because you can use it to build dynamic queries with user input. Dynamic queries are unavoidable in all but the most basic of applications.

This attack type can be defended against in multiple ways, but these approaches should always be layered.

So, how would you determine that your application is vulnerable?

Since this type of attack is being performed in your database, all data entry fields, comment fields, search fields, and website links are vulnerable. If these elements are not validated, an attacker has a chance to input malicious code in there. You should also consider encrypting all sensitive data that is stored in your database.

There are several ways that an application can defend against SQL injection attacks. You can divide them between defenses at the implementation level and at the network level.

Defensive strategies at the implementation level are as follows:

* Creating blocked and allowed lists
  * Within an application itself, there are two approaches to input validation that can defend against SQL injection attacks: Creating blocked lists and creating allowed lists. With blocked lists, specific, known malicious characters are removed from or replaced in user input. Although this approach is often implemented, largely due to the ease at which it can be accomplished, it is not effective when compared to allowed lists. Using allowed lists, on the other hand, each piece of user input is examined against a list of permitted characters. Blocked lists can fail to properly handle complex obfuscation, which could allow an attacker to subvert filters and inject SQL statements. This failure often occurs as a result of evolving attack techniques and filters that are not comprehensive (for example, very long regular expressions) or not implemented correctly. When you use both, blocked lists and allowed lists, you perform input sanitization. The term data sanitization means that you remove all dangerous elements from an input string before passing it to the SQL engine. Sanitizing your data is a strong defense mechanize that you can use; however, it should not be the only defense mechanism. This approach should be used on sites that allow HTML markup. By using input sanitization, you ensure that the received data cannot be harmful to your database.

* Fortifying SQL statements
  * Parameterized queries or prepared statements should be used comprehensively in addition to strict input validation. Each of these techniques performs all required escaping of dangerous characters before the SQL statement is passed to the underlying database system.

Prepared statements guarantee that an attacker cannot change the intent of a query, even if SQL commands are inserted in the code. The following example depicts the use of prepared statements in Java and illustrates how SQL statements are built without user-supplied data and then augmented with the data in such a manner that the structure and intent of the SQL statement cannot be altered:

```bash
String sql = "SELECT * FROM Users WHERE (username = ? AND password = ?)";
PreparedStatement preparedStmt = connection.prepareStatement(sql);
preparedStmt.setString(1, submittedUsername);
preparedStmt.setString(2, submittedPass);
results = preparedStmt.executeQuery(); 
```
**Note:** Prepared statements and similar technologies are not a "universal remedy." If you use them used incorrectly, without bind variables, they are no more secure than traditionally constructed dynamic queries.

```bash
String sql = "select * from Users where (username = '" + submittedUsername + 
         "' and password = '" + submittedPassword + "')";
PreparedStatement preparedStmt = connection.prepareStatement(sql); 
results = preparedStmt.executeQuery();
```
In addition to ensuring that the intent of SQL statements cannot be altered by user-supplied data, applications should also catch and remove all SQL-generated error messages before they reach an end user. Although this safeguard may hinder a developer's ability to troubleshoot an application error, which can easily be overcome with additional back-end logging, the presentation of SQL errors will greatly aid attackers in successfully exploiting a SQL injection vulnerability. The following example is an overly verbose error message:

```bash
com.mysql.jdbc.exceptions.MySQLSyntaxErrorException: Table 'sqlInjectionTest.test' doesn't exist 
	at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:936) 
	at com.mysql.jdbc.MysqlIO.checkErrorPacket(MysqlIO.java:2985) 
	at com.mysql.jdbc.MysqlIO.sendCommand(MysqlIO.java:1631) 
	at com.mysql.jdbc.MysqlIO.sqlQueryDirect(MysqlIO.java:1723) 
	at com.mysql.jdbc.Connection.execSQL(Connection.java:3277) 
	at com.mysql.jdbc.Connection.execSQL(Connection.java:3206) 
	at com.mysql.jdbc.Statement.executeQuery(Statement.java:1232) 
	at sqlInjectionBefore.main(before.java:28) 
```

This error message discloses that the application is using the Java programming language and the MySQL database platform and that the queried database is named "sqlInjectionTest." Each piece of information could assist attackers in crafting their application input, increasing the odds that they will be successful.

Another important fortifying practice is to follow the least privilege principle when assigning accounts. The philosophy behind this principle is very simple; each user should have rights only to the things that they need, as to avoid increased uncontrollable access but maintain efficient rights for their function in an organization. By following this principle, you dramatically reduce the chance of a malware spread across the entire system.

Now you will quickly review the defensive strategies for SQL injections, which are performed at the network level:

* Intrusion Prevention System signatures
  * In certain situations, it may be possible to detect and prevent SQL injection attacks using an intrusion prevention system (IPS). For an IPS to be effective, it must have visibility into the traffic of the application. For applications that use end-to-end encryption with HTTPS (for example, applications that use HTTPS without termination or acceleration at an intermediate network device), an IPS cannot identify traffic with characteristics of a SQL injection attack.

* Firewall application layer protocol inspection
  * Application layer protocol inspection performs deep packet inspection of traffic that transits the firewall.

### Validating User Data

Validation is the process of assessing data against a set of rules to make sure that the data is structured in a way you expect. When layered with other mitigation techniques discussed so far, validation can be very powerful. It is always important to remember to never trust the user input. When validating, be sure to use sensible rules. For example, a name field may be defined as VARCHAR(120) in the database—so, when you validate that, it makes sense to not only validate that it is a string but also set an upper bound on its length. You could also truncate the field to 120 before it is validated to ensure it is correct, so that the user is stopped if they try to provide something much longer.

![](/images/devcor/devcor_6_3_2.JPG)

**Note:** Libraries exist for most languages, which makes this process easier.

Using the Cerberus library for Python, you can build validation into your applications relatively easily.

Cerberus is a very flexible validator, allowing the developer to specify their schema in several formats. When the validation schema is defined, the schema is passed to the Validator and validated with **validate()**.

The schema can be specified as simply as:

```bash
schema = {‘sku': {'type': ‘integer’, ‘min’:1000, 'max’: 0xFFFF, ‘required’: True}}
```

However, be careful, schemas will become cumbersome and difficult to manage if done this way. For ease of readability, you can use a YAML schema to facilitate your efforts.

The following schema is the full implementation of the validator shown previously.

```python
import yaml
from cerberus import Validator
 schema_text = '''
sku:
  type: integer
  required: True
  min: 1000
  max: 0xffff
description:
  type: string
  required: True
  maxlength: 30
'''
 schema = yaml.load(schema_text)
 v = Validator()
 # Invalid SKU, No Description
document = {'sku': 138}
v.validate(document, schema)
> False
v.errors
> {'sku': ['min value is 1000'], 'description': ['required field']}
 # Valid SKU, Description is too long
document = {'sku': 0xeff, 'description': 'This is a really long product description, far too long and will fail'}
v.validate(document, schema)
> False
v.errors
> {'description': ['max length is 30']}
 # A valid document
document = {'sku': 0xbeef, 'description': 'Demo product description'}
v.validate(document, schema)
> True
```

If you would like to learn more about the Cerberus validator, go to https://docs.python-cerberus.org/en/stable/.

## 6.4 XSS and Request Forgery

Cross-site scripting (XSS) and Cross-Site Request Forgery (CSRF) are the among the most prevalent security flaws in web and mobile applications. Luckily, they are also some of the easiest to detect and prevent.

XSS attacks are a type of injection attacks. They occur when an attacker injects malicious scripts into trusted content that is then fed to the victim and executed. Typically, this kind of attack is persistent. Because this attack is executed by the user, it will run with that user's permissions. This means that the user's session can be hijacked and controlled by the attacker directly.

An interesting example of an XSS attack was made on eBay. The attackers hid the script inside product descriptions and with that they were able to steal users’ eBay accounts.

![](/images/devcor/devcor_6_4_1.png)

CSRF is an attack on state changes. These attacks rely on tricking users into performing some sort of stateful action, like changing an email address or password. As this type of attack relies on making the victim act without the attacker having direct control of the user or the ability to execute actions as the user, the impact is more limited in scope. Usually the attacker will try to attack things like password changes; however, any protections should be applied applicationwide.

CSRF vulnerabilities were even spotted on the popular site YouTube in nearly every action a user could perform. An attacker could have flagged videos as inappropriate in user's name, subscribed to a channel, and so on. Even though these actions may appear harmless, they can still lead to severe data compromisation for the user.

![](/images/devcor/devcor_6_4_2.png)

Both attack vectors can be protected relatively easily by following some basic rules and utilizing some simple programming techniques.

### Mitigating XSS Using Escaping

One of the easiest ways to mitigate XSS attacks is to escape any untrusted input. To do it effectively, you should do it both when data is received from the user and when data is to be sent to the user. For example, in the code where a form is processed or where data is read from a database.

![](/images/devcor/devcor_6_4_3.png)

The following figure demonstrates the differences between the escape and without escape techniques.

![](/images/devcor/devcor_6_4_4.png)

Most modern programming languages support HTML escaping out of the box, however, some of them will require additional libraries.

![](/images/devcor/devcor_6_4_5.JPG)

In Python, you can use HTML.Escape. The following output is a simple example of its usage:

```python
import cgi, cgitb, html
form = cgi.FieldStorage() 
comment = html.escape(form.getvalue(“COMMENT”) 
```

On the other side, if you want your users to use HTML simply, escaping it all makes this difficult. While escaping is an effective way to mitigate XSS, there may be times where you want to allow users to use HTML in their content. Escaping will often make this decision impractical. To deal with these cases, you can employ input sanitization.

Sanitization is where you take users’ untrusted input, run it through a filter for the content you are willing to allow, and strip out anything that is not permitted. Sanitization also fixes code omissions–things like closing tags, making links relative, and adding the nofollow directive to links.

![](/images/devcor/devcor_6_4_6.png)

**Note:** HTML input sanitization can be achieved in Python by using the sanitize library, which you can download at the following link https://pypi.org/project/sanitize/#files.

### Mitigating Cross Site Request Forgery

You already know what a CSRF attack is in theory, now you will examine how it looks like in a realistic scenario.

Imagine that you are trying to log into your bank account and complete a transfer of funds. First, you log into your bank (1). After you type in your credentials, the bank validates your request and sends you a cookie (2). At that time, the attacker tricks you into visiting a website under the attacker's control (3). On that website, the attacker constructs a malicious request, transferring funds from your bank account into theirs and instructs your browser to send it to the bank (4). Since the bank receives a request from a validated, trusted source (you), the bank executes the forged request and performs a fund transfer (5).

![](/images/devcor/devcor_6_4_7.png)

But how can you protect yourself against this type of attack?

Since CSRF attacks specifically target state-changing requests, you can defend against them by using a unique, unpredictable value—a secret—that an attacker does not know. This secret is called a CSRF token.

A CSRF token is generated by the server-side application and transmitted to the client. When the request is made, the server-side application validates the request and rejects it if the token is invalid.

CSRF tokens make a CSRF attack impossible because they block the attacker's ability to construct a valid HTTP request that looks like it was created by an unsuspected user.

Since the attacker cannot guess the value of a user's token, the attacker cannot create a valid request for the application to accept.

The following Python web application frameworks support CSRF tokens:

* Flask-WTF
  * An integration between Flask and WTForms

* Flask-SeaSurf
  * A dedicated Flask CSRF extension

* Django
* Pyramid
* Sanic-WTF
  * An integration between Sanic and WTForms

**Note:** You can write your own CSRF token generation code or you can use an existing library.

### Mitigating CSRF Using Flask-SeaSurf

Flask-SeaSurf is an extension for the Flask web application framework that adds CSRF protection. Once enabled globally, your entire application will be protected.

![](/images/devcor/devcor_6_4_8.png)

You can install the extension with the following command:

```bash
$ pip install flask-seasurf
```

By default, all requests that are not GET, HEAD, OPTIONS, or TRACE will be expected to present a valid CSRF token (POST, PUT, and DELETE). The extension will add the form value to any template automatically.

To manually add the token to your own Flask-based app, you would use the following form:

```bash
<input type="hidden" name="_csrf_token" value="{{ csrf_token() }}">
```

Because CSRF is enabled application wide, there may be instances where you do not want CSRF protection, for example, on a view in your application. Views can be exempted from the CSRF protection as needed:

```bash
@csrf.exempt 
@myApplication.route('/exempt_item', methods=['POST’]) 
def exempt_item(): ‘
       ''This view has no CSRF validation.'‘’ 
       return ‘Ok’
```