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

![](/images/devcor/devcor_6_3_1.png)

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

![](/images/devcor/devcor_6_3_2.png)

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

# 7 Automating Application Release

## 7.1 Introduction

Modern approaches to software development promote early delivery and continuous, incremental improvement of software, allowing you to better meet the demands of your users and customers. However, you can get the best results out of these practices only if you test and release software frequently. A standardized and automated process will help you create consistent releases with minimal effort.

## 7.2 Release Packaging and Dependency Management

As a developer, you rarely encounter simple programming tasks that could be done as a self-standing entity using only built-in standard Python environment. Most of the time you are creating a more complex software by combining your task-specific logic with existing building blocks. Those blocks can be pieces of code that others have already implemented, mostly called libraries or modules, or other independent applications accessible via their interfaces.

A typical Python project is a bundle of files in a specific hierarchical folder structure. Most of them are reusable modules, executable scripts, and resource files. This is a part that you develop yourself or together with your team. But there is also another important thing that you need to control and maintain. That is your working environment. Every time you import some specific library in your code, you must make sure that it is already installed.

Eventually your project is ready for further phases of the continuous development cycle. That means that you need to assign a release version, document changes, and deliver it to other environments, like testing or production networks. There are several ways you can pack it up and ship it. Considering your circumstances, you can decide on the optimal packaging and delivery method.

The goal is that the software works as expected on the target system, but that is achievable only if the software is placed into a compatible environment. That is because your application depends on several important things, like the version of the Python interpreter and installed libraries, for example. Not only must the required libraries be installed, they must also be the compatible version.

Does it matter if the target system has a newer version of the required library than your development environment? It does. The so-called application programming interface (API) of the library might be changed in a way that your usage of the library functions in your code become obsolete. It is still possible that newer version is compatible. However, there is no guarantee, until you check the changelog and thoroughly repeat your testing process. This also applies if the target environment library is an older version.

Missing, outdated, or incompatible dependencies can result in installation problems, stopping of the software, unexpected exceptions, crashes or otherwise impair performance. Isolated virtual environments and dependency requirement lists are commonly used for dependency management.

### Installing Python Packages

The installation process of the Python package is rarely manual work. There are several package managers that you can use to control and automate the installation. Those managers require that you pack all used Python libraries, modules, and other resource files into a single versioned archive file, named distribution package.

The archive file is what an end user downloads from the internet or local repository and installs on the target system. Generally, there are two approaches with the packaging. The archive can be a source distribution, which contains only source files. Those are later compiled and built on the target system during the installation, which can be costly in testing and production workflows. Alternatively, there is a binary distribution with a precompiled object for a specific target architecture and operating system. This approach is faster, but you need to create as many binary distributions as you have different target systems to cover.

UNIX-like operating systems have package managers of their own. If you know exactly which operating system you are targeting, you can use a distribution format like deb (Debian) or RPM Package Manager (Red Hat) and use that built-in package manager to take care of the installation.

However, developers mostly use a standard Python package manager named pip, which is already part of the newest versions of the Python installer. A huge repository of reusable Python packages named Python Package Index (PyPI) is the primary package source where it is used, but it can also install packages from many other sources, like a Git repository or directly from source files.

General Linux repositories provide packages only for some selected Python libraries or applications and, notably, they hold only one, usually the latest, version of the package. On the other hand, PyPI hosts a much broader range of Python modules with all the historical versions, which is extremely helpful in case of dependency conflicts.

One of the Python's biggest advantage is the ability to integrate with other programming environments, specially libraries written in C, C++, Fortran, Rust, and other languages. Not everyone has the knowledge and tools to build these components, so Python created the wheel, a package format designed to ship libraries with the compiled objects.

```bash
  $ pip install requests
  Collecting requests
    Downloading 
  <... output omitted ...>
  $ pip show requests
  Name: requests
  Version: 2.22.0
  Summary: Python HTTP for Humans.
  Home-page: http://python-requests.org
  Author: Kenneth Reitz
  Author-email: me@kennethreitz.org
  License: Apache 2.0
  Requires: chardet, urllib3, idna, certifi
  Required-by:
```

### Creating Python Packages

The Setuptools library is a package development tool for packaging Python projects. Most Python libraries and applications today use it as their build system. The file setup.py must be included in the root of the project; it describes package metadata and can contain custom build process instructions. When the package is installed from the source distribution, this script is executed to perform the dependencies check and installation, building of the package, and other related tasks.

Here is the minimal content of the setup.py script:

```bash
  from setuptools import setup, find_packages
  setup(
      name='my_package,"
      version="1.0.0,"
      packages=find_packages(),
  )
```

You can add more informational and control data by applying additional arguments to the setup() function. For example, you can add the description, keywords, data about the author, license info, location of the documentation and many other specifiers.

To control the installation, you can define these arguments:

* **include_package_data** and **exclude_package_data:** Control which resource data to include
* **install_requires:** Specify which dependent libraries and their versions must be installed during the process
* **python_requires:** Define which interpreter is supported
* **dependency_links:** Specify source locations for the dependent libraries

The **find_packages()** function searches the target directory recursively and finds Python packages for inclusion into a common package. Packages are only recognized if they include the __init__.py file. You can also set some inclusion and exclusion parameters that are applied during this search process.

It is always a good idea to first update pip, setuptools, and related libraries to the latest version:

```bash
  python3 -m pip install --user --upgrade setuptools wheel
```

Then you can start by running the following command from the same directory where setup.py is located:

```bash
  python3 setup.py sdist bdist_wheel
```

This action takes some time and there will be lots of output texts that guide you through the process steps and help you with potential troubleshooting. Once completed, it should result in two new files in the dist directory:

```bash
  dist/
    my_package-0.1.0.0-py3-none-any.whl
    my_package-0.1.0.0.tar.gz
```

The tar.gz file is a source archive and the .whl file is a Python built distribution called the wheel. You do not have to create both if you do not need them. Use the **sdist** and **bdist_wheel** keywords respectively to your requirements.

### Dependency Control

When pip gets a general request to install or update a package, it, by default, uses the latest published version of that library or an application. Pip does not differentiate whether the install was triggered by you on purpose or was it a part of some application's dependency installation. It does not care that the code in your current project might not be compatible with the recently updated library or even if some other nonrelated Python application stops working on your system. Dependency control is something you should take care of yourself.

Basically, you must consider two challenges. The first is that you must define and enforce the versions of used libraries that are compatible with your application. The second is that you must find a way that other Python applications can coexist with yours, even in the case of conflicting dependencies. For example, a required library version for your application is not compatible with some other application on your system.

Pip has a feature to manage full lists of libraries and corresponding version numbers through a requirements file. You can install everything in that file by using:

```bash
  $ pip install -r requirements.txt
```

The same is true for the **setuptools** that are used for package handling. There you can define the list in the **setup.py** script using the **install_requires** argument. However, unlike using pip install, where the file is used only for the corresponding command, **setup.py** uses this info for the whole Python project.

Typically, the list consists of all Python libraries that your project uses. You can simplify the creation of the list by using the **pip freeze** command, which gives you the installed libraries in the current environment and their corresponding versions. You can assume that if the same libraries with the same versions are installed on the target system, your application will work there as expected. You should check the list for unrelated libraries and remove them before using the list.

```bash
  $ pip freeze > requirements.txt
  certifi==2019.9.11
  chardet==3.0.4
  idna==2.8
  requests==2.22.0
  urllib3==1.25.6
  $ cat requirements.txt
  certifi>= 2019.9.11
  chardet>=3.0.4
  idna>=2.8
  requests>=2.22.0, <3.0
  urllib3>=1.25.1
  $ pip install -r requirements.txt
```

In the example, the standard output of the freeze command is redirected to a file. The convention is to name the file requirements.txt, but you can name it whatever you like. The requirements file format allows you to specify dependency versions using logical operators that give you a bit of flexibility If you want to update libraries with newer but still compatible versions.

### Isolating Dependencies

By default, pip is installing everything onto the machine in a single operating system-dependent location. This presents a serious limitation because with global installations, only a single version of the package can be installed at one time for the given Python interpreter.

The reality is that such a global installation results in dependency conflicts if multiple libraries or applications require different versions of the same dependency. You also must understand, that even if everything is working as expected at first, any future upgrade of the dependency can break these applications or libraries. Do not be surprised if that happens accidentally while installing another totally unrelated package.

To have a complete control, you can use one of the dependency isolation solutions. For example, you can add a parameter **-user** with the **pip install** command, which installs packages locally into your own userspace. However, that does not solve most of the dependency problems. The best way to isolate dependencies is to use virtual environments, which have their own Python interpreter and an independent collection of the installed libraries.

![](/images/devcor/devcor_7_2_1.png)

Virtualization can be done on different levels and scopes. For example, you can use Vagrant for virtualization of the operating system or the virtualized container Docker on the userspace level. However, for the development phase, the most common isolation method is using the virtualenv Python package that allows you to create named "virtual environments."

With this tool, you can install a separate set of libraries for each environment that you create. You can create as many as you need. One for each specific purpose, or, if you want, one for each project that you work on. It does not matter how many Python interpreter versions you have installed in your system. Each environment will have a symbolic link to only one Python interpreter. That is the one that you used to create that specific environment.

```bash
  $ cd my_project
  $ python3 -m venv myenv
  $ source myenv /bin/activate
```

Virtual environment enables pip a separate installation of third-party libraries and applications. Using the requirement list as an input parameter, you can specify versions to be installed in your development environment. However, as good as it sounds, it is still not a complete solution for all possible dependency challenges.

The **pip install** command does not always result in the same environment even if you use the requirements list. Each of the imported libraries can have its own dependencies, with each dependency having further dependencies and so on. At the end, you get a full graph of libraries where one can appear more than once with different or even unspecified versions. The more specific and consistent you are with the versions in the requirement list, the more deterministic the install process is. But, by being too specific, you also take all the burden of the checking for new versions and update decision making on yourself.

Pipenv is a dependency manager that is gaining popularity due to its practical tools. It is built on top of the pip and the virtualenv, which are used together with a single CLI. Pipenv automatically creates the virtual environment and handles the dependencies with several tools. Instead of a single requirements list, it uses two different files, each one for a different task.

The first file is Pipfile with a project library’s minimum requirements, usually listed without any versioning specified. It is auto populated whenever you install a new package. If Pipfile does not have an exact version specified, the install command enables all dependencies to update their versions.

When you are done with developing, updating libraries, and testing the application, you are ready to run the **lock** command. With that action, a snapshot of currently installed versions is stored in the Pipfile.lock. This file can then be used to replicate the exact environment. Such an install process is deterministic, because in case of conflicting dependencies Pipenv does not create a lock file in the first place. Instead it displays a warning message with troubleshooting information. You can use the **graph** command to investigate the dependency tree in detail and resolve the conflicts.

```bash
  $ pipenv graph
  numpy==1.17.2
  request==2019.4.13
  —get [required: Any, 
  installed: 2019.4.13]
    —query-string [required: Any, 
  installed: 2019.4.13]
      —public [required: Any, 
  installed: 2019.4.13]
        —setuptools [required: Any, 
  installed: 41.2.0]
      —setuptools [required: Any,
  installed: 41.2.0]
  <... output omitted ...>
```

Pipenv also offers you a simplified separation of the development and production environment. For example, if you need some library only for testing, you can add **--dev** argument to the **pip install** command. The package will be listed in a separate section of the Pipfile, guaranteeing it will not be installed in the production environment.

Besides those useful dependency handling features, Pipenv also upgrades the overall security by using verification hashes and simplifies the use of the private package repositories.

### Versioning Schemes

When you create a new Python package using setuptools, the version specifier is a mandatory argument in the setup.py script. At that point, you must choose your versioning scheme and stick with it for the whole lifetime of the project. As a Python developer, it should already be clear to you that versions are a vital part of the dependency management tools.

Choosing of the versioning scheme for your project depends on the its characteristics. The preferred one is semantic versioning, but you can also choose others as long as they comply with the flexible public version scheme specified in PEP 440 standard (https://www.python.org/dev/peps/pep-0440). If not, you can expect troubles using pip and setuptools because they enforce these rules.

Date based versioning is a suitable choice for projects with a regular and frequent time-based release. Version numbers typically take the form of YEAR.MONTH (for example, 12.04, 15.10).

Serial versioning is the simplest versioning scheme and consists of a single number which is incremented every release. It is not really the best practice for public releases, but it is simple and sufficient for developing a tool for yourself. Also, there are some cases where it is very practical to combine serial with some other versioning scheme like, in the form of YEAR.SERIAL, for example.

However, the recommended versioning scheme for new Python projects is based on Semantic Versioning 2.0.0 specification in the form of MAJOR.MINOR.PATCH. You can start with versions that have the MAJOR set to 0 and work on initial development until you come so far that your API can be well defined. At that point, you have the 1.0.0 release and you must start following these rules:

1. Increase MAJOR version when you make incompatible API changes.
1. Increase MINOR version when you add a new functionality, but the implementation is still backward-compatible.
1. Increase PATCH version when you make backward-compatible bug fixes.

This approach enables you to easily define compatible dependencies for the requirements.txt and setup.py files using logical operators. Simplified definition would be that as long a MAJOR stays the same, all versions that have only bug fixes and new functionalities are compatible.

Regardless of the base versioning scheme, which is used for final releases, you can append additional identification strings that define all sorts of nonfinal releases. For a single release, you can have several development releases, prereleases, release candidates, alphas and betas, or even post-releases. The appended string can start with the character '.', '-','_' or even nothing at all. It continues with the descriptive characters and ends with the serial enumeration.

On top of everything, you can also use an extra local version identifier which takes the form <public version identifier>+<local version label>.

Here are some examples of compliant version numbers:

| Version   | Type                |
|-----------|---------------------|
| 2.3.0dev1 | Development release |
| 2.2.0_a1  | Alpha release       |
| 2.2.0.rc1 | Release Candidate   |
| 2.2.0     | Final Release       |
| 25.10     | Data-based release  |
| 18        | Serial release      |

### Documenting Changes

When a new version of your software is released, end users and all others that are involved in the project, want to know why and how the changes were made. One of your responsibilities as a developer is to document the changes that were made in each version of the project. The changelog file plays a crucial role in the overall development and maintenance process of the project. The changelog often reaches even further into other business processes.

The file should include every single release version in a chronologically ordered list with a correct release dates, preferably with the latest version on top. For each version there should be a separate list that is comprised of all important changes, that were implemented between the current and previous versions. If you use the semantic versioning scheme, it is a good idea to announce it with a statement.

The changelog is meant for a wide public use, that is humans and not machines, and should not include long details about each change. Instead, descriptions should be short and clear so that a reader can quickly realize the meaning (new feature description, bug id that was fixed), importance, and the scope of the change. In order to improve the readability, changes of each version should not be mixed randomly or chronologically, but instead should be listed in groups of the following types:

| Name       | Description                     |
|------------|---------------------------------|
| Added      | for new features                |
| Changed    | for changed functionalities     |
| Deprecated | for soon-to-be-removed features |
| Removed    | for removed features            |
| Fixed      | for bug fixes                   |
| Security   | vulnerability fixes             |

If developers or operations need to backtrack, who made the change, what exactly did he or she do, at what date and time, what was the commit message and similar, the changelog is not the right place to search. The changelog is more of a summary report, while all other details can be found in the source versioning and issue tracking tools.

## 7.3 Advanced Version Control with Git

Git is a distributed Version Control System (VCS) that you can use to track and manage the development environment. Each member of your team has a separate local Git environment and can contribute the code or other project files to the common remote repository.

Git tracks all the changes of the files using a series of small Git objects, called commits, interlinked into a branch. Those commits are lightweight and are comprised mostly of the pointers to other Git objects, like snapshots, files, and parent commits. The actual objects are stored separately in a fast content-addressable file system and are easily accessible with those pointers. That makes the creation, management, and switching of the branches fast and easy.

The efficient branching support is a foundation for the development workflows that branch and merge often. In this respect, Git is a perfect match for continuous integration and continuous deployment (CI/CD) operations.

Although Git uses the server as a centralized collaboration point, every local repository has its own copy of the project files. That enables you to have the full control of your local environment and work independently at your pace. However, Git tracks and control every contributor’s individual changes, and when you decide to synchronize with the remote repository or merge your work to different branches, Git guides you through the process of code integration and conflict resolution.

![](/images/devcor/devcor_7_3_1.png)

Git Architecture is composed of the remote repository and several distributed local Git environments, where each of them includes a local repository, staging area, and working directory:

* **Remote Repository:** A remote repository is where the files of the project reside and where all other local copies are pulled from. It can be stored on an internal private server or hosted on a public repository such as GitHub or BitBucket.

* **Local Repository:** A local repository is a clone of the remote repository where commits are stored on each individual person's local machine. It can also include additional branches that you created locally and decided not to share with others. The commits that you have done locally are synchronized with the other repositories with the use of **git fetch**, **git pull**, and **git push** commands.

* **Working Directory:** A Working Directory is the actual file system directory that is controlled by Git. It is a sandbox where you do the actual work. Files that are tracked by Git are continuously compared with a snapshot found in the Staging area. In case of any changes, Git marks those files with the modified flag. Other changed or new files are marked as untracked. You can check the status of the files using the git status command.

* **Staging Area:** The Staging Area is where all the changes that you want for your next commit are placed. You can use the **git add** command to stage the files from the working directory. By moving untracked files to the staging area, those files become tracked and are included in further Git checkups.

With the **git commit** command, you move the snapshot of the Staging area to the local repository. There it is saved as a new commit in the currently used branch. Files that are not staged are not included in the commit.

By checking out or merging a different branch, you override the working directory. If you still have some modified or staged files left, Git does not allow you to switch branches.

### Linking Objects

Git operates mainly with the Git objects and their pointers. Git objects are files which are stored in the content-addressable file system. A 40-character SHA-1 hash, called a pointer, is calculated for each of them and it is used for multiple purposes. Git uses it for quick change comparison, fast retrieval from the file system, and object linking.

There are three basic types of Git objects:

* **Blob:** A file containing the actual file (your project file).
* **Tree:** A file containing a list of pointers to other blobs (files) or subtrees (subdirectories).
* **Commit:** A file containing:

  - Pointer to a Git tree, which is a snapshot of all files at the time of the commit
  - Date and time of the commit
  - Pointers to parent commits
  - Human readable commit message and
  - Data about the author and the committer

Git makes the pointers more human-friendly with the use of partial hash. To identify a Git object, the partial hash must be unique and include at least four starting characters of the original hash. In all examples, you will see 5 instead of 40-character long hashes, which is usually enough and makes the example more readable.

![](/images/devcor/devcor_7_3_2.png)

Snapshot is a Git tree object that contains pointers of all project files that are tracked with Git. It can be used to recreate the working directory and discover changes between commits.

Each time you create a commit, the pointer of the previous commit is stored as a parent pointer. Eventually you build a long chain of commits called a branch. Initial commit has zero parents, normal commit only one parent and the commit, resulting from a merge of two or more branches, has multiple parents.

You can check the history by using the **git log** command, which by default lists the commits in reverse chronological order. Starting with the most recent commit you can see its hash, the author's name and email, the commit time, and the commit message. The **git log** command is equipped with several useful options that you can use to check exactly what you need.

### Snapshot Example

You can check the content of the commit with the **git cat-file** command. The Git tree associated with the commit is a snapshot. That is a structure of pointers that define your project files at that exact time. Git uses it for multiple purposes, for example, reconstruction of the working directory, comparison, and merging of commits.

![](/images/devcor/devcor_7_3_3.png)

With the use of **git ls-files** command, you can see the current state of your working directory **(--modified)** or staging area **(--stage)**. You can try to change some files and stage them. Comparing the hashes, you can see that only the modified/staged files have different hashes, while other untouched files have the same ones. That means that when committing only a few new Git objects are added to the file system, that is commit object, snapshot object and the files that were changed.

### Using Branches

A Git branch is very simple. It has its name and a pointer to one of the commits. The initial repository branch is named master and is automatically created at the very beginning. Thus, you are able to make an initial commit right away and continue developing your software in a series of commits. Meanwhile Git automatically updates the branch pointer in a way that it always references to the latest commit.

![](/images/devcor/devcor_7_3_4.png)

Git has a very useful tagging feature, which enables you to tag a specific commit with human readable text. Usually that is used to tag the releases, when the code in specific branch is stable enough to be released but can also be used for other purposes. For example, if your project undergoes some manual testing, the testers make new builds only every few commits and tag it with a build number. They include this build number in the issue report, so that developer can later use the same build for troubleshooting. The tag is a reference pointer like the branch, with the difference that, once created, they are not changeable.

Git uses a yet another special reference pointer named HEAD, which is the one that tells where in the repository are you in this current moment. With other words, it defines what your current working directory should match. You can manipulate it by using the **git checkout** command.

![](/images/devcor/devcor_7_3_5.png)

Sooner than later, you want to diverge from the main branch and create a new one, where you can work without affecting other branches. For that you can use the **git branch** command, which simply creates a new branch pointer, that is referencing to the current commit. If you checkout a branch, the HEAD starts mirroring the branch pointer, which means that it is pointing to the tip of the branch. In this case you are in the, so called, attached state. When you create another commit, both, the branch pointer and the HEAD, will update their pointers to reference it.

If when you need to check out a specific commit, you can use the **git checkout** command, adding a specific commit hash or a tag, if available. In this case, HEAD is pointing directly to a commit rather than mirroring a branch. The detached state, as this is called, allows you to run, test, or troubleshoot the software. However, if you want to commit a change, you first need to return to the attached state. To do that you can create a new branch in the detached state or check out an existing one.

It sometimes happens that you are in the middle of work when you need to do some urgent fixes or even switch branches. Before doing that, you first need to clean your working directory. Instead of creating a temporary branch and committing unfinished work, you can choose to save all your modified files into separate snapshot called stash. By default, the **git stash** command saves both modified and staged files. Because you can save multiple stashed independently, it is a good idea to also put a description as an argument to the **git stash** command.

You can reapply the stash anytime using the **git stash pop** command. This is also possible in different branches than where the stash originated.

```bash
  $ git status -s
  M my_network.py
  $ git stash push -m "Work in progress"
  Saved working directory and index state On feature: Work in progress
  $ git status
  On branch feature
  nothing to commit, working tree clean
  $ git stash pop
  On branch feature
  Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git checkout -- <file>..." to discard changes in working directory)

          modified:   my_network.py
```

### Integrating Changes

As you can see, branching with Git is easy and you should use it often. For example, if you are working on some new functionality, you can do it in a separate feature branch. If you push it to the remote repository, your teammates are also able to contribute their part or make a code review before you integrate the changes into the main development branch. There are several options for how to integrate changes from one branch into another. Let's first look at the merge.

To merge two branches, you must do the following:

* Checkout the main development branch
* Pull the latest commits
* Make sure that your working directory is clean
* Use the **git merge feature** command

In the ideal case, no one will work on the main branch while you struggle to develop the new feature. Although that will happen rarely, look at what occurs in that case. Git discovers that all commits in your feature branch are directly ahead of the main branch and the merge can be done by replaying feature branch commits. In this simplified case, Git does not actually merge but instead moves the main branch pointer forward. That is called a "fast-forward" merge.

![](/images/devcor/devcor_7_3_6.png)

In other situations, branches can further diverge with more unrelated commits. In this case, Git cannot simply move the pointer but must perform a three-way merge. The algorithm compares and merges snapshots from the common ancestor of both branches toward the last branch commits. If an automatic merge goes well and there are no conflicts detected, a new merge commit is created. This commit joins those branches and obviously has two parent pointers. If any merge conflicts are detected, merge commit is not created.

![](/images/devcor/devcor_7_3_7.png)

It is quite bold to let the merge also automatically commit. Even without any detected conflicts, it happens that developers do some incompatible changes in parallel. Therefore, it is not a bad idea to stop the automatic commit with the **--no-commit** option. That way you have time to double check what was prepared in the staging area, try to compile and build the application, or even test it before manually committing the merge. If you are not satisfied with the results or if there were conflicts detected, you can still decide not to commit, but rather abort the merge with the **$git merge --abort** command. This action tries to revert your working directory back to the previous state before merge was invoked.

Git merge preserves the complete history and chronological order of the commits in the repository. That is a nice feature, especially when you are working on a code that originally was not yours or the code is too old to remember why you made it as you did. With the full history, you can review the intermediate commits and sense the context more easily. However, working with lots of contributors, history can become overpopulated with numerous merge commits.

![](/images/devcor/devcor_7_3_8.png)

On the other hand, you can keep a more linear history by using the rebase integration method. The idea is to rebuild your feature branch on top of the main one with the **git rebase** command. In this process, Git first finds the common ancestor and remembers all the subsequent snapshots (not commits) that were made in the feature branch. Original commits in that branch are than ultimately removed from the Git history and the branch pointer is moved to the tip of the main branch. Then the remembered snapshots are replayed again, resulting with almost identical, but still different, commits to the ones that have been previously removed. When you resolve the potential rebase conflicts, you can switch to the main branch and perform a fast forward merge. Although the history has been forged, you end up with a clear linear repository history.

In both cases, using merge or rebase, you end up with the same results. The difference is just the history and potential problems you can encounter with a distributed teamwork. Rebase can be a perfect choice in cases where you have a short-lived branch that you do not push to the remote repository. However, once you push your work with all the history of your branch to other teammates, forging of the history in a distributed environment can become a nightmare. Just imagine, for example, that also someone else works on that branch and you, without knowing, perform the rebase action.

There are also some other options for integrating work from one branch to another. One of them is cherry-picking. With that method you can choose a single commit from one branch and apply it onto another. In this case, Git does not search for a common ancestor, but instead just tries to reapply the selected snapshot. First you switch to the branch you want to apply the commit to, then you run the **git cherry-pick <commit-hash>** command.

### Resolving Conflicts

Git performs an automatic merge almost every time you merge, rebase, cherry pick, revert, and even when you pull a remote branch. During that merge, Git must decide at each step if the new file change is conflicting any previous change.

Most of the conflicts happen when developers introduce changes to the same part of the same file in parallel or if one developer deletes a file that another developer modified. In these cases, Git needs your feedback because it cannot automatically determine what is correct.

When changes are on different lines, or even in different files, Git boldly navigates through the applied snapshots and finishes the merge without conflicts. This is great for most situations. However, you should always watch for errors that Git cannot detect. For example, it might happen that after a successful merge, your project does not compile anymore. With troubleshooting and checking of the commit history, you might discover that one developer has changed a function prototype, while another developer was using the function the old way.

In case of merge conflicts, Git inserts special conflict markers in the affected files and continues with the merge process. The ending result later indicates that the automatic merge has failed and that the final commit has not been done. If you run the **$git status** command, you can see that the files with conflicts have not been staged yet, but instead are marked with the unmerged flag and are waiting for your intervention. Using the **--merge** option with the **git log** command produces a log with a list of commits that conflict between the merging branches.

```bash
  $ git status
  On branch master
  You have unmerged paths.
    (fix conflicts and run "git commit")
    (use "git merge --abort" to abort the merge)
  
  Unmerged paths:
    (use "git add <file>..." to mark resolution)
  
          both modified: my_network.py

  no changes added to commit (use "git add" and/or "git commit -a")
```

If you open those files with a text editor, you can see that each problematic part is marked with the conflict markers. In the simplest and most common situation, those markers separate the code into two parts. Each part consists of the text from the relevant snapshot. The upper one is from your HEAD, the current branch, while the lower one is from the merging branch. Remember that Git performs a three-way merge starting from the common ancestor. Therefore, sometimes, you could see more than two parts, with additional ||||||| markers used.

Merge markers:

```bash
  <<<<<<<
  Changes made on the current branch.
  |||||||
  The common ancestor version.
  =======
  Changes made on the merging branch. 
  >>>>>>>
```

To resolve the conflict, you must decide and change the file to its desired state. There are a several ways to do this. You can simply use an editor, search for the conflict markers, and make all necessary modifications. Alternatively, if you know that one version of the complete file is better, you can simply choose one of the versions, called "ours" (HEAD) or "theirs" (feature branch).

```bash
  $git checkout --ours my_network.py
```

After resolving all conflicts, you also must stage the unmerged files with **$git add** command and finalize the merge with **$git commit command**. If you are not satisfied with the merge results or you just do not have time to solve the conflicts, you can, at any time, abort the merge with the **git merge --abort** command or reset conflicted files to the last commit.

You should understand that resolving conflicts can take quite some time or require additional consolidation with your teammates. It can happen that you will have to ask your teammates not to commit to the main development branch while you are struggling to resolve the merge conflicts. You should avoid this complication and unnecessary delay by first merging the main development branch into your feature branch. That way you can resolve the conflicts without time pressure and repeat the merge multiple times. When everything works as it should, you just switch to the main development branch and perform the fast forward merge.

### Undoing Changes

Making mistakes is unavoidable in the development process. Users make lots of them while coding and when using Git. Whether you accidentally commit changes, or realize your previous committed code is not what you want, you need to undo the changes. Git offers several ways to revert already committed work.

If you have not yet pushed your commits to a remote repository, you can rework the most recent commit by first staging changes that you want to add to the last commit and later commit again with the **--amend** option.

![](/images/devcor/devcor_7_3_9.png)

If you need to revert several commits, you can use the **git reset <hash-or-ref>** command. This action moves the reference branch pointer backward to the desired commit in the branch history. The unwanted commits are not deleted, but they are not part of any branch anymore.

You have three options for instructing Git how to handle the snapshots from detached commits and the working directory while performing the reset:

1. **Hard reset: (--hard)** overrides everything. Branch and HEAD pointers are moved to reference the chosen commit, while the working directory and staging area are synchronized with that old snapshot. To avoid losing any working changes, you can use the **git stash** and **git stash pop** commands accordingly.

1. **Mixed reset: (--mixed)** Resets the index but not the working tree (i.e., the changed files are preserved but not marked for commit) and reports what has not been updated. This is the default action. If -N is specified, removed paths are marked as intent-to-add.

1. **Soft reset: (--soft)** works similarly to a mixed reset, with the addition that the working directory is preserved. Soft or mixed reset can be used to make the history cleaner by squashing several commits into a single commit.

If you have already pushed your commits to a remote repository, the reset becomes trickier. Undoing changes without rewriting history is usually a better option. The **git revert** command does not remove any previous commits, but creates a new commit that only reverts the changes of the specified commit. This works well for published changes because then the true history of the repository is preserved.

If you do want to revert only changes of a single file and not the whole commit, you can use a **git checkout** with the specified file path.

### Remote Branches

Git uses a distributed collaboration model, which means that every developer gets their own copy of the repository with the complete history and branch structure. Both remote and local repositories are technically the same. They all operate with standard Git objects like blobs, trees, commits, branches and tags.

The difference is more in the context of use. Remote repositories are only meant for sharing and exchanging code between developers, whereas almost all work on files happens in developer computers in the local repository. Each developer can work independently because they have everything they need on their computer. They do not even need a network connection to the remote server until they decide to share their work or synchronize their repository.

To check which remote servers you have configured, you can run the **git remote** command. If you initialize your Git repository by cloning a remote one, you have that specific remote server already configured. Git, by default, names the server origin and automatically does some additional tasks that enable the developer immediate working environment. However, for a better understanding of what is happening in the background, look at how to configure and use the remote repository.

First, you need to add a new connection to the remote repository using **git remote add** command, where you specify the repository name and source URL. Remote connections are more like shortcuts rather than actual links into other repositories. By design, Git entirely isolates the local environment for the developer and does not automatically pass any information between repositories. Instead, you must manually request the data by using the **git fetch** command. That action downloads all the data from the remote project that you do not have yet.

```bash
  $ git remote
  origin
  $ git remote add work https://github.com/company/test
  $ git remote
  origin
  work

  $ git checkout --track work/feature
  Branch feature set up to track remote branch feature from origin.
  Switched to a new branch 'feature'
```

Now you should be able to see all published branches from the remote repository. The names of the remote branches are made up of the name of the remote connection (for example, origin) followed by "/" and then the name of a branch in that remote repository. If you switch to one of those branches, Git notifies you that you are in the detached state. That means that Git will not allow you to do any commits directly into the remote branch. You can think of remote branches as read-only branches that represent a snapshot from the last time you ran the **git fetch** command.

In order to contribute to the remote branch, you need to create a new local branch based on the remote branch. You can do that with the **git checkout** with **--track** option. That which creates a new local branch with the same name as remote, if not specified different. The tracking relationship between the new local branch and the remote branch is then established. With that you can start working in the local branch and when needed you can submit your work with the **git push** command. The Git server will accept your changes as long as it can fast forward the remote branch. Otherwise, someone has probably already successfully pushed their commits before you, and first, you must do the **git fetch** again. By default, the **git push** command does not transfer tags to remote servers. You must push tags to a shared server yourself after you have created them.

Git fetch changes only your remote branch and leaves the local tracking branch untouched. You can see the changes by comparing branches with the **git diff** command. It is up to you to manually merge the remote branch into the local tracking one, before trying to push the change again. There is also a **git pull** command that combines **git fetch** and **git merge** together.

A local branch created in your environment is kept private until you explicitly decide to publish it. To publish the local branch, you can use **git push -u <remote> <branch name>** command. Option -u sets the tracking relationship between local branch and newly created remote branch.

## 7.4 Resolve Merge Conflicts with Git

Lab

## 7.5 Branching Strategies

Development of software is an ongoing process that involves much more than just understanding programming language and tools. Most projects are now so complex that they cannot be done individually anymore. Instead, a whole team needs to be gathered and managed to work consistently and efficiently.

Projects are rarely totally defined from the start and later there is usually no time or reason to define everything in detail prior to the actual implementation. Rapid software development considers the fact that project requirements can and will change frequently and gradually focus on what the customer actually needs. The software is expected to be well tested before putting it into production, it is supposed to be constantly upgraded and improved, and, if any error is noticed, the new release needs to be ready and deployed as soon as possible.

Git, as a distributed VCS, is the most crucial collaboration tool in a development team. Together with the issue tracking system, it defines the working environment, where all these high expectations can be met. On top of these tools, the team needs to organize themselves to follow the defined rules of the working process.

Git offers you an efficient branching support that makes the creation of branches and switching between them easy and fast. That leverages the use of Git to all new perspectives. Different strategies have been developed, based on the simplicity of branching, that you can use to utilize your development team in the most consistent and productive manner.

Common to all strategies is that everybody on the development team must be in an agreement with how the contributions will be applied to the project or, in other words, which Git workflow will be strictly followed. The strategy must be conveyed to each member using a reference document like a description on the wiki page, an email, or at least it should be talked through on a meeting. A well-defined branching strategy greatly improves both the quality of the work and the communication of the team. It becomes clear to everyone how development is organized, and developers can act toward the common goal.

The chosen workflow might change during the development process. New circumstances can arise that change the work dynamics, but it might be a simple realization that the team is not performing optimally. In that case, it is not a bad idea to regroup and evaluate whether to simplify or extend the complexity of the Git workflow. When doing that you should consider your team's culture, project requirements, and keep in mind that the workflow:

* Should cope well with the team's size (current and future)
* Should not create too much unnecessary overhead that limits productivity
* Should have proper mechanisms to easily amend mistakes and enable rapid response for the potential changes in the project requirements

Technically, Git does not differentiate between master branches and any other branch. The way you work with any of them can be defined with one of two opposite collaboration approaches, the centralized and feature branch workflows.

### Centralized Workflow

Your team can use Git only as the single point of entry for all the changes to the project. Centralized workflow, as this branching strategy is called, uses only one Git branch for all changes. With this approach you get the ability to work in parallel and merge all work into a single branch. The workflow, as simple as it is, can be enough for some small projects or projects that are more document oriented. For an example, it is suitable for some infrastructure as code projects, where machine-readable definition files present most of the files under source control.

For larger projects that are more application oriented, using only a single Git branch severely limits your flexibility. For example, a dilemma arises once the software is released. What will your development team do afterward? Work on the next scheduled release or wait in standby to fix potential problems after the product deployment? The reality is that every code change potentially has some side effects. With the centralized workflow, the branch becomes unstable the moment your team continues implementing new features and is not release-ready anymore.

![](/images/devcor/devcor_7_5_1.png)

In the basic centralized workflow, developers must wait for each other to finish their work before the software can be released. Imagine that two of them are working on different new features—one simple and the other a complex feature that requires a lot of development time. In CI/CD environments, the idea of waiting for all developers to finish is not acceptable.

Feature Branch Workflow
Instead of using only the master branch, developers can follow the Feature branch workflow and work independently in a dedicated branch for each feature. With that approach, the code in the master branch can be left untouched and therefore stable until the new feature is finished. Only then it is merged back to the master branch. There is no need to wait anymore, since each merged branch represents a finished work that makes the master branch ready for further CI processes and potentially a new release.

![](/images/devcor/devcor_7_5_2.png)

You can also push the feature branch to the remote repository. Giving everyone access to it enables you to do the following:

* Create a distributed backup
* Share the workload by allowing others to contribute
* Get feedback, like code review or general approval
* Put your implementation to the testing processes

During the lifetime of the project, you can expect several priority shifts. It may be decided that the feature you are currently implementing is not needed anymore or that some other one has a higher priority. In that case, you do not need to abandon your work. You can just simply commit your work, get back to the master branch and create a new feature branch.

Overall it is a good idea that you occasionally perform a merge of the master branch into your feature branch. That way you can handle conflicts gradually when they occur and not all at once when you merge your finished work back to the master. You can also detect other incompatible changes introduced by your teammates that, in combination with your contribution, break the code. It is perfectly fine if your feature branch is temporarily broken. However, if that happens on the master branch, it might result in several wasted hours and angry reactions.

### GitFlow—Master and Development Branches

Most development work is done by implementing new features and merging them into the main branch. That is nicely handled with the Feature branch workflow. However, it is not enough to cover all scenarios that you can encounter during integration process. Other Git workflows use the Feature branch workflow as a foundation and expand it with abstract definitions of branches and rules on how to use them.

GitFlow is one of those branching strategies that cover all requirements for CI/CD operations. Overall, it is a comprehensive workflow with many different types of branches and a set of rules for how to work with them. You might think that it is too complex for your small project, but it is important that you understand why some rules are introduced and what are they achieving. Then you can decide whether to use it in full or only a subset of it.

![](/images/devcor/devcor_7_5_3.png)

The first obvious improvement is that, instead of a single master branch, this workflow uses two main branches to record the history of the project. The master branch is used for maintaining release history, where each release is tagged with the assigned version number. The second branch is the development branch, an integration branch where all feature branches are merged in.

There are significant differences between these two branches. While the development branch is a never-ending work in progress with lots of merges and direct commits, the master branch only has merge commits with stable code and each correlates with an actual release. The master branch contains only the code and other resource files that are necessary for a release and its deployment. On the other hand, the development branch can include additional tools and resources that are only relevant for the development process and not for the end user. For example, there might be some testing tools that are never released to public and are therefore filtered out from the master branch.

### GitFlow—Release Branches

Once the development branch has enough features implemented or a scheduled release is needed, your team must work on getting the branch stable enough for a release. Instead of stopping all new development, you should fork out a release type branch. Its sole purpose is to prepare the code for the actual release.

![](/images/devcor/devcor_7_5_4.png)

The basic idea is that development branch can still be further developed. Most of your team does not have to slow down and can continue with the feature branch workflow on the development branch by committing features that are meant for the next release.

However, from the perspective of the newly created release branch, everything slows down. Committing of new features is stopped and only bug fixes and other release related commits are allowed. To avoid getting any unstable commits prior to the release, the merging is limited to only one direction, backward to the development branch.

Once the release branch is stable enough, a "code freeze" is declared, restricting all further commits without specific approval. After thoughtful evaluation, a decision must be made to merge this release branch into the master branch. The merge commit gets tagged with the assigned version number for the actual release. If necessary, you should merge the release branch to the development branch for the last time.

### GitFlow—Hotfix Branches

Sometimes a critical bug arises after the release. It does not matter whether the report came from the end users or your testing process, if it came five minutes or a few months after the release. In all cases, a rapid response with a completely new release is expected.

The only way to do it fast is to start with an otherwise stable master branch and fork a dedicated hotfix branch out of it. When the fix is done and verified, it should be merged into both master and developing branches. Version number should be updated accordingly and used as a release tag on the master branch.

![](/images/devcor/devcor_7_5_5.png)

### GitFlow—Overview

GitFlow maintains two main branches for a very good reason. In order to be a highly responsive development team, you always need to have one branch that is stable enough to be release ready. With the master branch you can, at any moment, make a rapid improvement in the currently released version. With hotfix branches, urgent fixing, testing, and delivering, which might be very chaotic, is still transparently done. The rapid response can be orchestrated in a controlled manner, limiting the changes only to the relevant issue and thus narrowing the area of retesting.

On the other hand, the development branch is a never-ending work in progress. In contrast to the master branch, it does not need to be stable at any time, because the release branches take that burden away from them. That way the team members can work very fast without interruptions or time-consuming focus on polishing the details.

Details are later polished in the release branches, where it is easier to enforce some needed restrictions, like defining what will be included in the release and what should be left for the next one. The testing process suddenly narrows down, becomes manageable, and gradually comes to a halt when the branch is considered stable. The stable release branches are then merged back to the development branch, which significantly improves them.

All these different branch types offer a clear separation of the development phases and with that more efficient management of the teams working process.

## 7.6 Continuous Testing and Static Code Analysis in CI Pipeline

Using maintenance or bug fix releases that require various branching strategies for versioning your code, you should try to avoid introducing too many changes at once. You should do this to limit the possibility of creating new bugs with your bug fixes. You can further improve on that by providing automated tests that run continually, on every code change, making sure your software runs the way it is supposed to.

Not only do tests verify the code, they also demonstrate how to use it and often lead to better design. Code that is tested tends to be more modular and decoupled, as it is harder to test otherwise. Most importantly, code with accompanying tests provides the development team with the confidence required to easily change or refactor it.

Covering the required functionality with tests (high code coverage) means the number of tests also increases. It is not unusual to have hundreds of tests in the code base. This presents a different challenge: how to run all these tests? Testing manually is impossible. So, tests must be automated, allowing developers and testers to run them effortlessly on demand. Once you have automated tests, it takes little extra effort to set up a system to perform continuous testing.

The benefits of automated, continuous testing are numerous:

* Provide visibility: Is an authoritative source on what state the code is in, such as whether tests are passing and how stable the code base is.

* Identify breaking changes: By having tests performed on every commit, you will know exactly when a problem first occurred. This will reduce the time it takes you to fix the problem, as the potentially problematic changes are limited to the specific commit.

* Verify test fixtures: Some tests may require additional setup to function correctly and automated testing ensures this is an integral part of the test. For example, some equipment credentials may be required for a certain test. If you wrote a test that relied on credentials being provided on your local machine, the test would fail when run elsewhere. Such a test is broken, as it cannot be run by other developers, and a system would let you know about it.

* Handle concurrency: Tests may require interfacing with limited resources, such as shared equipment, and many of the automated testing systems already have integrated locking mechanisms.

### Static Code Analysis

Tests exercise your code by running it with test inputs and verifying the outputs. Another type of analysis that you can perform on the code looks at the code itself and does not require running it. This process is called static code analysis and is usually performed by an automated tool. It is sometimes called lint or linter and it is good practice to use it in addition to code review and automated tests.

You can find static code analysis tools to test a wide range of functionalities. The most popular are code style checkers that enforce given code conventions. In the Python community, PEP8 is a very popular style guide and there are multiple tools that check consistency with this standard.

Many of these tools will also identify common errors and easily misused programming constructs, allowing you to spot bugs early. Similar, yet distinct tools, are tools dealing with potential security issues. They can detect certain known vulnerabilities, such as the Open Web Application Security Project (OWASP) top ten list (https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), and leaking credentials in the source code.

A code complexity metric can also be very useful and point to the parts of code that would benefit the most out of refactoring. Likewise, unused code could be removed. Static analysis tools exist to find both.

Automated tools analyze code without running it to find bugs early and improve code quality.

| Checks                                           | Python package       |
|--------------------------------------------------|----------------------|
| Potential bugs missed by tests and common errors | prospector, pyflakes |
| Code complexity                                  | mccabe               |
| Unused code                                      | vulture              |
| Security vulnerabilities                         | bandit               |
| Leaking credentials                              | dodgy                |
| Incorrect type usage                             | mypy                 |
| Code style inconsistencies                       | pylint, pycodestyle  |

While it is possible to run any of these tools manually, you and your team will benefit the most if you select the ones that are useful to you and run them continuously on your codebase, just like your tests.

### Pipeline, Stages, Jobs

When talking to your colleagues about automating the test or build process to run in a continuous fashion, what you are trying to say is that you want to build a CI pipeline. This process is typically integrated with CD into a CI/CD pipeline.

There are three basic ingredients to a CI/CD system:

* Pipelines
* Stages
* Jobs

![](/images/devcor/devcor_7_6_1.png)

The smallest unit of these three is a job. It can be composed from single or multiple steps, performing any kind of action. It can compile code, build docker images, run tests, or deploy to server -- it basically does whatever you instruct it to do. They can run on a specific repository branch and can be constrained by different conditions. A job in a CI/CD process can either succeed or fail.

Each job belongs to exactly one stage. A stage usually contains multiple jobs, depending on its goal. All jobs in a stage run in parallel. There can be multiple stages and they are executed in sequence one after the other. When a single job inside a stage fails, the entire stage is considered failed, resulting in stopping the CI/CD process. Stages usually carry a name that represents the common goal of all jobs running under it. For example, build stage, test stage, deploy stage, and so on.

A pipeline is a top-level component of CI/CD that encapsulates jobs and stages into a continuous workflow, orchestrating all the steps included in it. A pipeline runs when you push changes to the registered source code repository. Pipelines are usually declared in a file, documenting all stages and their jobs, along with the commands that are needed to execute and perform a specific job, like running tests or a lint tool.

The implementation of each of the ingredients and the pipeline documentation in a descriptor file vary depending on the provider of the CI/CD system, but the basic idea stays the same in all of them.

A typical process in a CI/CD pipelines build process for the containerized applications is the following:

* Create an application image
* Run tests against the newly created image
* Push image to a remote registry
* Deploy the new image to a server

### CI/CD Pipeline Steps

The first stage in most CI/CD pipelines is the build stage. This is where you take the source code of the application you want to run through the pipeline and transform it into an executable that can be tested and later deployed in as few steps as possible. If you are writing a NodeJS application, this is where you would run the npm package manager to setup the application. If you were thinking ahead and used containerization technologies such as Docker, you build the applications images in this step using the **docker build** command and the Dockerfile provided in the source code repository.

As mentioned, a multistage pipeline is triggered by a push to a code repository. It can also be executed manually on existing source code. Many CI/CD systems can define criterion on which a new pipeline is triggered. The starting point is typically a push of the code to the repository, but this can be refined into rules such as starting the pipeline only if somebody merges a feature branch into development, and so on. How to start the pipeline also depends on the tools you are using. If your CI/CD system is located outside the hosted VCS, then you will need to trigger that tool remotely. Repository hosting services such as GitHub, GitLab, and Bitbucket include their own pipeline mechanisms. Examples of nonrepository services that provide CI/CD pipelines (or at least CI) are Jenkins, Travis CI, and Semaphore. The following figure showcases the triggering of the pipeline and the first build stage that leverages a Docker based application.

![](/images/devcor/devcor_7_6_2.png)

A classic Docker based build stage is composed of jobs that prepare a Docker image from the code repository that triggered the pipeline. Depending on where your pipeline runs, you might need to fetch the code from a remote repository before building an image. If you are using a repository service such as GitLab, the pipeline will be triggered by a specific repository and branch, so you do not need to fetch code manually. Note that running the **docker build** command in a job implies that your codebase includes a Dockerfile. If an image is successfully built, it gets pushed to a container registry. A job can also create one or many artifacts that are attached to a job after it finishes. Artifacts can be regular files or binaries that, when generated, are passed to other stages and jobs where they can be used. For example, you could build an image and instead of pushing it to a registry, you would create an artifact, and then you would use it in all consecutive jobs.

**Note:** Do not unnecessarily split tasks into too many jobs. Many CI/CD systems that leverage Docker must spin up a new Docker container for every job, fetch the build results from previous jobs, and re-upload the cache when the job is finished. Having too many jobs can lead to overhead on the system where the pipelines are running.

If all jobs in the build stage succeed, the pipeline can safely continue to the next stage.

When the build process successfully prepares an image from the Dockerfile instructions inside the codebase, it moves on to the next, typically test stage.

![](/images/devcor/devcor_7_6_3.png)

The idea behind the test stage is to make sure that developers do not introduce any bugs to the application’s source code. This can be checked by running various tests. Tests such as unit tests and code coverage are usually included in the codebase itself. You need to instruct a job inside the test stage where to find the implemented tests and how to run them. How the tests are executed depends on the technologies and languages you used for the project. For example, if you have a Python application, you can include a folder called test at the root of your applications directory, and inside you have a file test_suite.py which you can execute in a job with the **python ./test/test_suite.py** command.

**Note:** Each job, depending on the pipeline implementation, will have to run the container application to perform the test on a running instance of an application. Since the previous stage already built and pushed the image to a registry, you can reference it in your container creations. Because the layers of images are cached, it will be a reasonably quick process to start an application container.

One or more jobs in this stage would then perform static source code analyses, using various tools mentioned above. If there is no separate stage for security testing, it might be included here as well.

Security testing often consists of two parts, which would typically be implemented as separate jobs. First, static application security testing (SAST) tools analyze source code and compiled versions of code to help find security flaws. Second, an active scan of the web application may be performed, normally from the outside, to look for security vulnerabilities such as Cross-site scripting, Structured Query Language (SQL) Injection, Command Injection, Path Traversal and insecure server configuration. This is referred to as dynamic application security testing (DAST).

SAST or DAST scanners are also mostly running in containers. Setting them up requires pulling their images, running the container, and configuring it to scan your application. These jobs then usually generate artifacts with reports about application vulnerability for you to inspect.

![](/images/devcor/devcor_7_6_4.png)

The benefit of incorporating these tests into the CI/CD pipeline is that they verify your code all the time, letting you know right away when a problem comes up. For example, the SAST tool that checked your application against the OWASP top ten found a vulnerability and marked the job as a failure. Because a job failed, implicitly the entire stage failed. Pipeline execution stops, and a report is generated. Now you need to return to your code and figure out how to fix this vulnerability.

Having implemented a fix, you commit your new code and a new pipeline process starts from the beginning. It will run all the stages again, including all the tests. If tests finish successfully, that ensures you have a working codebase and have not inadvertently introduced another bug. After that, additional stages, such as deploy, may run.

# 7 Identifying CI/CD Pipeline Failures

A CI/CD pipeline is a complex and verbose process. The quality of the project depends a lot on the quality of the CI/CD pipeline. So, in that regard, you need to be able to identify and fix pipeline failures as quick as possible. As an IT engineer, you will surely find yourself in such a position.

Last week you had a meeting and you discussed some new features that your team needs to implement. Since then, your teammates and you worked very hard on developing said features. One of your recent commits triggered a CI/CD pipeline which failed.

![](/images/devcor/devcor_7_7_1.png)

Most of the CI pipelines provide a notification via email. This way, you do not have to wait and look at the pipeline process the whole time and you can calmly continue with your work in the meantime.

As you begin to investigate the CI/CD pipeline failures, you need to first identify the problem. The mail itself will provide enough information, for now, to acknowledge the pipeline's problem (error).

The most common failures are:

* Pipeline configuration (file)
* Missing dependencies
* Static code analysis errors
* Incompatible versions
* Unit and integration tests
* Deployment failures
* Other

Note that there are precautions you can do even before you deploy the code to the repository. Every developer needs to develop, build, and test code locally in the same environment as the one on the CI server. This helps identify errors and bugs much faster.

Pull requests are used to merge new code from developers into the codebase. This way senior or more experienced developers can review the code first and the same code can be run through the developers branch pipeline first. If the pipeline passes, it can later be merged to the Master repository, where only the working code is stored.

CI/CD pipelines mostly run automated tests (unit and integration) and create metrics for said tests. Those metrics show a lot more than solely which tests passed or failed. Static code analysis and code duplication, for example, are metrics that should also be produced and regularly examined. Discuss the metrics frequently at meetings.

You can choose between a lot of CI/CD pipeline tools and servers. They can be self-hosted or on the cloud. It is important to adjust the tools to the needs of the project and to do some research before you decide which platform works best for you. Some CI servers do not provide a code repository. In those cases, the codebase is hosted somewhere else and the pipeline, on another server, is then triggered via the codebase APIs. On the other hand, there a couple of CI servers that do provide a code repository. Take time and evaluate different CI servers and tools and choose the one that fits your project well.

### GitLab CI/CD Pipeline

When you first started the project, your team decided that the best CI server for your team is GitLab, because it provides a CI server and a code repository. This way you do not need to host the codebase somewhere else.

GitLab is a single place out-of-the-box DevOps application. GitLab, for example, already provides you with the Container registry (such as, Docker Container registry).

A GitLab CI/CD pipeline is configured using a YAML file (.gitlab-ci.yml) in the root of the project. The YAML file defines what GitLab Runner executes and what happens if a process succeeds or fails. A YAML file has a more straightforward syntax.

GitLab also provides pipeline as code, an upgrade from the GUI type of pipeline. This gives DevOps teams more freedom in the pipeline process. It also makes rollbacks a lot easier, provides a built-in Lint tool that makes sure the YAML file is valid, version control, and audit trails.

GitLab pipeline consist of:

* **Commit:** A change in the code.
* **Job:** Runner instructions.
* **Pipeline:** A group of jobs divided into different stages.
* **Runner:** A server or agent that implements each job separately and can spin up or down if needed.
* **Stages:** Parts of a job (for example, build or tests). Multiple jobs inside the same stage are executed in parallel.

![](/images/devcor/devcor_7_7_2.png)

From the figure above you can see that the failure happened inside the first stage, which is usually named Build. The rest of the stages were not even started. This is reasonable since tests will surely fail if the build stage does not pass. GitLab has a simple and straightforward GUI which makes identifying and fixing pipeline errors a lot easier.

### Pipeline Configuration

Upon closer inspection, you see that some errors have been found in your gitlab-ci.yml file. This result could have been easily avoided as GitLab CI/CD pipeline provides an online YAML syntax checker.

![](/images/devcor/devcor_7_7_3.png)

Found errors are displayed with the location (lines) of the errors inside the file. For further information, you can visit GitLab's documentation page that provides you with a CI/CD pipeline configuration references and some examples.

### Missing Dependencies

As you run the pipeline again, you see that the build stage still fails. This time the configuration file is not the problem. The build stage fails because of missing dependencies. This can happen when developers use tools like the Python package development tool (pipenv). Pipenv uses a Pipfile where it keeps dependencies and requirements. The developer installs said requirements locally but either forgets to commit the Pipfile itself or add the requirements into a requirements file, like, requirements.txt.

![](/images/devcor/devcor_7_7_4.png)

You can see in the figure that the Python code imports some packages/modules and you need to have them all installed on GitLab's CI runner. If they are not present, the build stage cannot pass. You need to install these packages and modules to fix this kind of problem.

The same problem can happen with incompatible versions. Encourage your team members to build and test code locally before they deploy it to the project's repository. Always provide the prerequisites for the project to the IT engineer that is responsible for your project's CI/CD pipeline.

Static Code Analysis
Static Code Analysis is also very important. Once the code is successfully built and has no errors or bugs, it is important to run static code analysis with a linter. You need to define code conventions, coding styles, and design for your team.

Note that this step can be done before the code is built and depends a lot on what you are trying to achieve. If you have strict guidelines on how the code needs to be written, you need to enforce said restrictions. The linter can check the code for errors and bugs or code conventions. It is up to you and your team to decide what is best for the project you are working on.

![](/images/devcor/devcor_7_7_5.png)

The figure shows successfully built Python code that has some trailing whitespaces, unnecessary code and undefined variables which may not be used. Since the production code needs to be kept clean, you can restrict the Build stage so that it does not pass even if there are no bugs or errors outside of this step. This is a good practice because it makes the codebase easier to maintain in the future.

### Testing Failures

Once the build stage is successful, the most common practice is to move to the Test stage. The committed code is tested with unit and integration tests. Unit tests are used for testing a specific part or function of a code and integration tests are used for a more holistic approach. The Test stage is an important part of the CI/CD pipeline. The structure and number of said tests are up to your team to decide and should be handled according the needs of the project.

In the following figure, you can see a failed unit test done in Python. You can use tests frameworks made for a specific programming language you use or write your own tests, depending on the needs, team, budget, and the time you have for this specific step. Either way, the tests should have clean and straightforward output of the test failures. It should be simple and specific so you can see the error straight away. If the tests are done correctly, you need to address the committed code and fix the code itself. Sometimes, the opposite of this can happen. The tests may be written wrong. In this case, you and your team need to rewrite the tests and discuss the fixes.

![](/images/devcor/devcor_7_7_6.png)

### Deployment Failures

After all the tests have passed and the code is ready to be deployed, there are still some things that can go wrong. When you are deploying into a staging or a production server, always make sure you have enough resources on the server. The staging server can hold some additional tests, code or files for testing engineers so that they can test the project more inside a safe environment. The production environment, on the other hand, should only contain a clean and working project (code, requirements, and so on).

![](/images/devcor/devcor_7_7_7.png)

It is important that the whole team is communicating regularly and that everyone has the details they need in order to do their job properly. Good communication within the team can help you fix errors and bugs a lot faster or even before they are made. Take time to discuss the project holistically, dive into every detail and possible failure scenario before you start working on a CI/CD pipeline process.


# 8 Deploying Applications

## 8.1 Introduction

The way you develop, build, and deploy your applications has significantly changed over the past couple of years. Applications are becoming more complex and so is the infrastructure where these applications are deployed. Rapid development requires rapid deployment, resulting in the discovery of new automation tools and practices. Guidelines such as the 12-factor app methodology can help you think about application development in a more scalable and less error-prone way. It gives you an idea of how to package your applications for efficient delivery. Containerization opens a new world of rapid setups of environments. Using a tool like Docker becomes inevitable to cope with modern-day development and deployment practices. But then somebody needs to organize all these containers on the infrastructure, and you do not want to do that manually—time to expand your knowledge on Kubernetes. It does not have to stop there. What if you could test and deploy your applications automatically by just pushing the code to a central repository? Continuous integration and continuous deployment (CI/CD) practices can teach you to be aligned even more with the never-ending practices of the DevOps world.

## 8.2 12-Factor App Methodology

Applications today are commonly treated as services and are typically accessed via web interfaces. The era of software as a service (SaaS) brought ease of access for the users and many new technologies and different approaches to the development and deployment of such software. The 12-factor app Methodology is a series of advice that is based on practical experience in the operational side of developing, deploying, and scaling web applications.

The collection of the 12 principles and best practices are known as the 12-factor app methodology. This methodology was written and published in 2012 by Adam Wiggins, who worked for the company Heroku. It is available on the website https://12factor.net/. The experience that the company received from running their platform as a service (PaaS) solution drives the principles.

The objective of these principles is to teach developers how to build software that is highly portable between different running environments. Developers should also know how to build software that is suitable for cloud deployments, but these principles are also beneficial for deploying applications on your own infrastructure. The goal is also to enable CI/CD. This continuity minimizes the need for system administration and scales up applications with ease, without the need for changing architecture or the need for new tools when traffic increases.

These guidelines are not limited to a specific programming language or technology and can be incorporated into any stack that a company uses for their development and deployment. The principles can also help to minimize time and cost for onboarding new developers on the project. They minimize time and cost by suggesting using declarative ways of automating the setup of development and production environments.

The 12-Factors App Methodology has not changed much since 2012, but it is still very relevant in modern software development. Now you will dive into each of the 12 factors and learn their applicability and benefits.

### Codebase

Applications that are compliant with the 12-Factor Methodology are tracked in a version control system (VCS) such as Git, Subversion (SVN), or Mercurial. The first factor in the methodology states that there should be one codebase that is tracked in a revision control, many deploys.

A codebase is any single source code repository in a centralized VCS such as Subversion. It's also any set of repositories that share a common root in a distributed VCS like Git.

**Note:** A centralized VCS uses a client-server approach and is considered as a single source of truth from where developers check out the code and later add (commit) their changes. Changes are located on a single server, so it is easier to follow what is being done. In a distributed VCS, a peer-to-peer approach is used, where a central server stores the latest code. However, every local machine developers work on can have a copy of the entire codebase and the entire history of changes. Changes can be merged from one repository into another.

The Codebase Factor goal is to encourage developers to use one version control (VC) repository (codebase) per application. The application can be from the same codebase and deployed many times to different environments. A deploy is a setup of a running instance of an application that can serve requests. A deploy can also be made to a production, testing, staging, or local development environment.

![](/images/devcor/devcor_8_2_1.png)

An example of violating this guideline is when your application is made of many source code repositories. This situation makes it very difficult to automate the build and deploy process of the application. Another way to break the guideline is if your codebase is used to produce multiple different applications. In cases like this, the shared code is not always a self-standing service (microservice). Rather, it can be separated into libraries that can be versioned separately and included in the applications as dependencies (discussed in the next factor).

To sum up, this factor is telling you to use the one codebase, one application approach. However, this approach does not mean that the code cannot be shared across different applications, it means that the shared code is supposed to become another codebase. While the codebase should be the same across the deploys, it does not limit you in using different versions of the same codebase. For example, a staging environment might have features that should not be active yet on production. Another example is a developer can locally deploy a new version of the application before testing it in the test environment and pushing it to the preproduction staging environment.

### Dependencies

The second factor in the 12-Factors series is about explicitly declaring and isolating dependencies.

Most systems have a dependency management tool that can install dependencies. These dependencies are explicitly declared in a manifest file, which comes together with the application source code or can be stored separately. This tool releases the burden of having to manually install dependencies on all libraries of a project. Instead the developer can run the package manager against a predeclared dependencies file and the manager will install the libraries systemwide (site packages) or scope them into the directory that contains the application (vendoring, bundling). You should never rely on the existence of systemwide packages like curl, wget, and similar, but should always completely and exactly declare them in a dependency declaration manifest.

The second part of this guideline states that you should isolate the dependencies. Dependency isolation is used to ensure that dependencies in your application are isolated from other services and their dependencies, residing close to the resource requiring them. This situation preventing leakage from or in the system surrounding your application. When there are multiple services running on the same system, you should be able to use different versions of libraries in different services. You should be able to do so without fearing that a dependency will be overwritten when installing or updating a new service. Dependency packages should be bundled (vendored) into some substructure with the application itself.

![](/images/devcor/devcor_8_2_2.png)

There are several tools that can handle package management. When developing for Microsoft platforms (.NET), you will encounter the NuGet package manager where the dependency manifest file can be declared in JSON format. The package references can be marked as private, resulting in bundling the dependencies into the application and isolating them from the outer world. In Java, Maven or Gradle package managers are used. When developing for the web with Node JavaScript run time, the npm package manager is used to manage and download dependencies. It manages and downloads dependencies using a JSON dependency declaration file and a single **npm install** command.

If you use Python, then Pip package management tool can be used for installing the dependencies of a program. All the dependencies can be gathered in a manifest text file. Observe an example of a **requirements.txt** file that the Pip manager will use later.

```bash
  #### requirements.txt ####
  # Requirements without specific versions
  requests
  scrappy
  nose

  # requirements with specific versions
  netaddr == 0.7.19	# must be version 0.7.19
  ncclient >= 0.6.1	# minimum version is 0.6.1
```
Once the requirements are collected, you can run the Pip package manager wherever you need to set up the environment. The installation process starts with the command **pip install -r requirements.txt**. By default, Pip searches and installs packages from the Python Package index (PyPI), but it can also be used to integrate with a specific code repository. For isolating dependencies in Python, you can use Virtualenv tool. Virtualenv creates an environment that has its own installation directories and does not share libraries with other virtualenv environments.

The basic workflow when using the Virtualenv tool is to first create an environment with the **virtualenv project_name** command. When the new environment exists, you activate it by sourcing the activate script, for example, **source ~/project_name/bin/activate.** For more information, refer to the official documentation available on the website https://virtualenv.pypa.io/en/latest/.

One of the benefits of following this guideline is that a developer can check the program code into their environment. In this environment, the only requirement is to have the language run time and the selected dependency management tool. The entire environment that is needed for running the application should be build using a single command that installs the dependencies and prepares the application for run time. Properly handling the dependencies of an application permits you to have repeatable deployments.

### Configuration

The third factor states that you should store configuration in the environment.

![](/images/devcor/devcor_8_2_3.png)

Configuration of an application is everything that is expected to be altered in the applications lifecycle. The idea of this instruction is that you do not store any configuration data within the codebase alongside your application. To follow this guideline, you first need to understand which configuration items you should separate. A configuration specifies values that vary between deployments to different environments.

An application is not considered a 12-factor app if it stores configuration items as constants. The 12-factor methodology requires a strict separation of configuration from an application’s source code because configuration can vary across deployments, but code should not. If a certain value stays the same across all deployments, then you do not have to track it as a configuration item.

Credentials are sensitive information that should be handled with care. Extracting the credentials from the source code and adding them into XML or JSON files that are bundled together with the application does not solve the problem. Bundled resources are part of the codebase and anyone that has access to the codebase itself can access them. A test for whether your application correctly factors the configuration is to treat your application like it is open source. If you could make your codebase open source without compromising any credentials, then you are probably succeeding in isolating the code from the configuration and credentials.

So, how do you store configuration externally? The 12-factor app suggests storing it in environment variables (env vars). Env vars can be easily changed between the deploys without changing any code. The benefits of using env vars are that you will not accidentally push them to your applications code repository, and they are a language agnostic standard. As an example of configuration storage that is not language agnostic, this data is stored in Java System Properties files in the Java programming language. Similarly, .NET applications get their configuration from web.config file, which is XML based. These configuration storages are not preferred since they prevent you from varying configuration across environments while maintaining your application core.

Another way of storing configuration data is to use a specialized product that is designed to expose configuration. Alternatively, if your application is being deployed to a cloud provider, there might be a service that is prepared by the cloud provider. This service can be used in your application deployments.

Proper external configuration supports deploying immutable application packages to different environments automatically with a deployment pipeline, which makes deployment management much simpler and more scalable.

### Backing Services

The next factor is about treating backing services as attached resources.

Many times, your applications will need to access some service that is external to the current running environment. External, backing services can be accessed over the internet or they can be located on another network. Some of the most common types of backing services are data stores (MySQL), messaging systems, SMTP services (Postfix), caching systems (Memcached), external authentication providers (Auth0).

An external service might require username, password, and a URL that your application will use for accessing a specific resource. Resources that are likely to vary because of different environments or because you might change the provider of the service should be stored in configuration. You should be able to swap a locally managed MySQL database with some other third-party provider of the same datastore technology just by changing the resource handling in the configuration. Changing an attached service for a different one should not require any code changes—your application should stay immutable even in such conditions.

![](/images/devcor/devcor_8_2_4.png)

To conform to this guideline, your app should follow the rules. The rules include making sure that the binding of your application is done through an external configuration. It should be possible to attach new backing services or detach old ones without having to redeploy the application. Imagine that a database that your application relies on stops responding. In a tightly coupled system, where the code refers to the database directly, it would cause you troubles to make that application work again. You would need to stop the application, fix the code, redeploy it, and then start the application again. But, if you followed the suggestions in this principle, you would be able to start a new instance of a database. You could also point your application to it through the configuration settings. You could even have a database, or other service, in a high availability cluster and automate the change of the backing service if there is a failure, minimizing downtime.

These rules affect how you write your source code in the first place. The source code should be generic enough that it does not care who provides a service and enables hot swapping of different backing services. The code becomes more loosely coupled with the surrounding services, which is generally a good software design approach.

### Build, Release, Run

Factor five of the 12 factors suggests that you should strictly separate build and run stages.

The main takeaway from this principle is that each of the stages in the lifecycle of an application are isolated and approached independently. Once the design is ready, the next steps are developing the application in a specific technology stack and then making it available to end users through an automated process.

An application following the 12 factors strictly separates the build, release, and run stages. For example, it is not accepted that the code can be changed during the run time—that is, directly in the run-time environment. Any changes that are required on a running release should repeat the stages.

![](/images/devcor/devcor_8_2_5.png)

The first, build stage, is responsible for taking the codebase and transforming it to something executable that can run on a server. During the deployment process, a specified version of your codebase will be built with the dependencies that are read from the dependencies declarations and bundled with the application’s build artifact. Ideally, a developer deploying new code initiates the process of building an artifact from the code repository, which is handled by a continuous integration pipeline and includes automated tests. You should be able to release a deploy to any number of different environments. The immutability of a build artifact should ensure that it works on all environments when combined with correct configuration.

The release stage is a process of taking an environment-specific configuration and combining it with the executable binaries of your application. Every release has a unique release ID, which can be an increment number, a time stamp of the release, or a special versioning strategy, such as numbering releases based on major, minor, or patch changes (for example, v1.0.1). Releases cannot be mutated once created. If a change on a release is required, the process of different stages should repeat, producing a new release version. You want to version your releases because it should be possible to roll back to a previous version if necessary. Therefore, releases should be uniquely identified and immutable, enabling audit of the applications history.

The last stage is running the application on the execution server and exposing the service to the end users. Alternatively, if the application is still in the development phase, the run stage includes starting the service for testing purposes. The run stage should be simple, with as few moving parts as possible, and not require developers to assist with starting it. The build process can be more complex, because errors, being in foreground, are easier to detect and resolve. When the application is running, the environment, provided by cloud or local infrastructure, is responsible for maintaining its lifecycle. The environment is also responsible for making sure it is available. It scales up and down based on the number of requests, aggregating logs, and similar operational tasks.

### Processes

The sixth factor talks about processes being stateless, without sharing memory or storage.

The simplest example of executing an application is a standalone script on a local computer with the installed run time. The process can be launched from the command line, so, if you developed your script in Python, you would run the script like this: **python script.py.** A more complicated program could also be started with one command, but the result would be many different concurrently running processes.

For a process to be stateless, as the guideline suggests it should be, it must not rely on contents of memory when handling requests. It also cannot make any assumptions about memory after the request is processed. The memory space or file system of the process can be used as a momentary cache. For example, an application reads data from a remotely accessed service, operates on that data, and then stores the result to the backing database. After the processes are finished, the application following these guidelines should not assume that the previous data still exists on the disk or in the memory.

![](/images/devcor/devcor_8_2_6.png)

The operating system where the application is running handles a process and can stop or restart without any warning. There are chances that future requests are served by a different process that does not hold the same information in the memory. A restart of a process can wipe out the memory, so your application should not rely on the existence of data that was previously known.

If processes need to share data, in a 12-factor app, this sharing would be done via an external backing service. In developing web-based systems, it is common to cache user session data in the memory of the application’s process. This cache is a violation of the sixth factor; you cannot rely on traffic being routed to the same process. Such state data is a good candidate for introducing an in-memory session database, such as Memcached or Redis, as a backing service.

### Port Binding

The seventh guideline of the 12-factor app says that you should export services via port binding.

Web applications that run on a server are usually executed inside a web server container such as an Apache HTTP server or NGINX. These web servers are some of the most popular open source web servers. In the world of Java development, containers like Tomcat and JBoss are popular, and Microsoft Internet Information Server (IIS) could be used when working with the .NET platform.

A 12-factor app, in contrast, should be completely self-contained. It should not rely on run-time injection of a web server into the execution environment to create a web service. A web application should know how to export HTTP as a service by binding to a port on the server and then listening to that port for new requests. A self-contained application declares web server libraries as dependencies. In Python, such a dependency could be a library called Tornado, or Jetty in Java-based web applications.

![](/images/devcor/devcor_8_2_7.png)

Port binding allows such applications to be tied to a port based on the environment where they are deployed, without any code changes needed. With port binding in mind, you can access services via exposed ports (for example, http://localhost:8080) on your local machine. On staging servers, it might be on some other port, like 192.168.5.2:1000, for example. While in production, domain name resolution (DNS) is normally used for translating a URL to a specific host address and port.

Port binding is not limited to web protocols like HTTP. Binding a process to a port and awaiting incoming requests can manage almost any kind of service. When enabling applications to support this kind of run-time port binding, you are also allowing an application to act as a backing service for other applications.

### Concurrency

The next guideline suggests that you should scale out via the process model.

To understand this suggestion, you should first understand what scaling is and the different ways of approaching scaling. When an application reaches its limit of capacity, then you should think in increasing the size (scaling up) of the application. An example of reaching its capacity is when requests cannot be handled anymore because there are suddenly too many of them. Increasing the size can have multiple meanings. You can scale vertically by adding more resources such as RAM or CPU, or you can scale horizontally (scaling out) by creating multiple simultaneously running processes. The load that the application receives can be distributed among all processes.

An application that is written with 12-factors in mind considers processes as first-class citizens, similarly to how UNIX-based systems handle service daemons. Using this model, you can architect your app to handle different workloads by assigning each type of work to a process type. A process can be, for example, part of web processes that serve HTTP requests, or part of worker processes that handle background tasks. The array of process types and number of processes of each type is known as process formation.

![](/images/devcor/devcor_8_2_8.png)

The individual processes can still handle internal multiplexing using threads and similar tools. However, vertical scaling is limited, so it is important to be ready to start multiple processes for splitting workload across multiple physical or virtual machines. Processes should be stateless, so not sharing anything and relying on the operating system process manager (systemd, Foreman) makes it simpler to add concurrency to your application.

### Disposability

The next factor is about maximizing robustness with fast startup and graceful shutdown.

Previously, you discovered that applications consist of multiple running processes that should be easy to replicate to scale when needed. This ability implies that processes are disposable, and they can be started or stopped at any time. This facilitates elastic scaling that is based on demand and rapid deployments of new code. This kind of elasticity requires processes to start up quickly and shutdown gracefully, releasing all resources that a process might use, without interrupting the application.

![](/images/devcor/devcor_8_2_9.png)

Ideally, a process should take just a few seconds from the time it is launched until it is ready to receive requests. When your application is faster to start, it is more suitable for rapid deployments and easier scaling. Your applications become more robust because the process manager can move existing process to new machines whenever needed.

Graceful shutdown should be performed after receiving a SIGTERM signal from the operating system process manager. If there is a web process, a graceful shutdown can be achieved by first removing the binding to a port, which results in refusing any new requests. Next, finish the current requests before stopping the process. Other worker processes should follow the same approach to make sure that existing requests are handled, and new ones rejected. This can be achieved by acquiring and releasing locks on resources, following the transaction model.

**Note:** The SIGTERM signal is sent to a process to request its termination. Unlike the SIGKILL signal, the process can catch, interpret, or ignore it. This ability allows the process to perform nice termination releasing resources and saving state if appropriate.

Besides gracefully shutting down an application, you should also be prepared for sudden death of application processes because of failure in the environment or underlying hardware. In such cases, a background processing queueing software such as Beanstalkd might be used to return the unfinished jobs to the queue until a process is available again.

A 12-factor app should be architected to quickly and reliably handle processes when starting the application. It should also be prepared for graceful or nongraceful terminations without the fear of losing data.

### Dev/Prod Parity

The next factor instructs you to keep your development, staging, and production environments as similar as possible.

Organizations used to make gaps between the different environments an application must go through in its lifecycle. The development environment might use different database drivers from the staging environment or the operating systems might differ. The developer writes the application on Windows operating system, but in production it gets deployed on Linux operating system, which creates a gap between the tools. This gap results in inconsistent deployments, where you cannot guarantee that your application will work the same way in all target environments.

Traditionally, deployment of applications into production environment takes days, weeks or even months, creating a time gap between development, staging, and production environments. In such organizations, developers write code and operations engineers deploy it, creating a personnel gap with possible problems in communication and delayed fixes of issues.

![](/images/devcor/devcor_8_2_10.png)

The idea of the tenth factor is to make the personnel, time, and tools gaps as smaller as possible. When developer writes an application or creates a fix, the code should be deployed in minutes. Developers should also be a part of the deployments process, watching the behavior of the application in production to understand if something misbehaves and acting quickly when that is the case. The gap between tools should be minimized, and divergent application environments should be a thing of the past. Today, they should be as similar as possible.

Usually your applications will use some sort of backing service such as a database, queueing mechanism, or cache. There are many different technologies, tools, and associated adapters in every backing service type. It is appealing to use a more lightweight version of a database on a local machine, such as SQLite. It can be appealing to use a more powerful PostgreSQL in production, or local process memory for caching on your laptop and Memcached in staging and production.

If you want to follow the rules that the 12-factor app methodology sets, then you should avoid using different backing services in different environments. Adapters like ActiveRecord help when you decide to change the backing service entirely because it will not be necessary to rewrite the code. These adapters abstract differences in communicating to different database backing services. But using adapters to facilitate different backing services across environments can cause problems. Accessing the service is the same because of an adapter, but different technologies behave differently, so tiny incompatibilities might cause the code working in a development environment to suddenly fail in production.

These kind of errors create friction in continuous deployment pipelines and are easily avoidable when you make the gap between the tools small. There are provisioning tools such as Chef and Puppet that can standardize the creation of an environment. Containerization technologies such as Docker also make it simple to match environments, making this guideline easy to follow.

### Logs

Guideline 11 of the 12-factor app methodology talks about treating logs as event streams.

Logs are used for determining behavior and identifying problems of a running application. They can be used for auditing, data inspection, or troubleshooting purposes. They are usually written to one or more files on a disk, which is only the final output format. Logs should be treated as a sequence of aggregated, time-ordered events coming from the output streams of running services. There is no beginning or an end, it is an ongoing flood of events that you want to inspect in the real time as they happen.

A 12-factor app does not concern itself with the management of output streams. Storing log files is not something that a 12-factor application is supposed to take care of, instead, each process of an application should write its event stream to the standard output (stdout). To observe the behavior of an application during the development, you can view the stream in the foreground of the terminal or operate on this output in some other way.

![](/images/devcor/devcor_8_2_11.png)

On UNIX-based systems, you have different tools that you can use for managing streams outside the application. By default, there are two default streams, stdout and stderr, available to all programs. You can redirect the output of these streams to a file, watch it via real-time tail in a terminal, pipelining it to analysis system such as Splunk for further processing, or to a central syslog server. The application is not aware of such operations, all it does is output the stream on a standard output. Any other log management tool can use the application’s stdout however it is needed.

**Note:** Syslog is a widely used standard for message logging. It is used on various devices such as printers, routers, and other applications. It enables the consolidation of logging data from different types of systems in a central repository (syslog server).

Applications that send their logs directly to a log file lose the flexibility of UNIX streams. You do not have to reinvent the capabilities that a system, on which your application is deployed, already has.

### Admin Processes

The last of the 12-factors talks about running admin and management tasks as one-off processes.

In some of the previous factors, you learned the process formation of an application. The application processes are used to deliver regular application functionalities, which the source code defines, such as handling web requests, communicating with the database, and other background workers functionalities.

Developers often need to perform one-off administrative tasks such as running database migrations or using a console, also known as Read Eval Print Loop (REPL) shell, for running arbitrary code. You could also be responsible for executing one-time scripts for resource cleanup or general maintenance of some sort.

![](/images/devcor/devcor_8_2_12.png)

The key of the admin processes used for a 12-factor app is to run them in identical environment to the regular long-running processes of the application. The one-off tasks should be executed against the same codebase and release as the current target application. Administrative scripts should be a part of the same repository as the application code. Being a part of the repository means that they are versioned together with the application, making sure that they work with the desired application version.

Many languages such as Python and Ruby provide a REPL shell out of the box, which makes it easier to run one-off scripts. In the local development environment, you can run admin processes by a direct shell command inside the app directory. However, in a production-deploy, you will need remote command execution mechanism provided by the environment or facilitate a secure shell (SSH) connection to the server to access the scripts.

## 8.3 Containerizing Applications Using Docker

Containers run isolated processes on an operating system, providing reliable and consistent deployments regardless of the environment. They provide a standard way of packaging your application code and all the dependencies into a single object. Shift to using containers is the next infrastructural shift after virtualization and cloud technologies, ensuring speed, better performance, and consistency in different stages of application development and deployment. There are several, mostly open sourced, containerization technologies available, with Docker being one of the most popular products in this field.

Understanding the concepts of containerization and learning how to use Docker containers is essential in a modern applications lifecycle.

Virtualization was one of the first infrastructural shifts enabling on demand compute power that was relatively cheap and disposable. With the help of the hypervisor software, the idle time on servers was minimized by reserving parts of hosts compute power, to create multiple isolated entities called virtual machines (VMs), on which users can run variety of operating systems such as Linux, Windows, and operating system X, resulting in better utilization of the resources on the bare metal servers.

In a traditional enterprise environment, a virtual environment is created once and then used throughout the lifecycle of a software product while the operations team manages it. This creates a gap between the development of an application, testing, and deployment. Containers are a technology that unites the two teams, developers, and operations, enabling better communication, agility and flexibility on both the development and operational levels.

Docker serves as a tool for packaging and running applications in a loosely isolated object that is called a container. You can run multiple containers simultaneously on a host operating system without the need for a hypervisor. The advantage of containers is in speed. They are more lightweight, require fewer resources, and typically only take seconds to start. The development, building, and testing of an application becomes quicker, and the deployment and updating of an application is easier and faster than in traditional deployments. Docker can also reduce the cost of managing an application. You can use fewer operating systems to run more applications, saving costs on the infrastructure side.

![](/images/devcor/devcor_8_3_1.png)

You have learned from the 12-factor App methodology that your development, staging, and production environments should be as similar as possible. With a VM only approach, this is possible to achieve but requires more dedication by the teams to keep all the environments identical. If you are developing an application on your laptop, you will most likely have a slightly different configuration of the system than is on the production server. During the development of the application, you might introduce a feature that requires some new dependency. If you do not properly document this requirement and propagate it across the environments, you risk having a gap between your development environment and staging or production settings. This kind of situation can result in different behaviors and bugs in the application that are discovered in later stages.

Docker allows you to package your application and all the dependencies into a single immutable object that stays consistent. It helps you to run the application the same way everywhere you ship it and has good scaling predispositions. It separates the applications from the infrastructure, enabling you to handle the infrastructure the same way as you handle the applications. The only requirement is to have Docker run time installed, which reduces a lot of maintenance tasks that an operation team usually needs to work on. This simplifies the workflow of CI/CD, reducing the time between development and publishing of an application. It also brings the development and operations teams closer together into the paradigm of the DevOps practice.

The Docker platform is open sourced, licensed under the Apache 2.0 license. It comes in three editions: engine community, engine enterprise with enterprise grade SLA in mind, and enterprise with full management support. It can be installed on Windows, macOS, and Linux operating systems with the operating system native package managers or using a script located on the URL https://get.docker.com (edge release).

The host where Docker is running provides the compute power for running the containers. The difference between classic VMs and Docker containers is that the processes running inside a container are not much different from the processes running directly on the host, they are just very isolated from each other. If there is a VM, a Type-1 or Type-2 hypervisor is needed to isolate resources to a specific compute entity. When using Docker, the host running the Docker run time manages how the CPU and Memory is scheduled.

When an application does not have many components, it is acceptable to have a dedicated virtual machine for each component, but when the number of components starts growing and they keep on getting smaller, like in the case of microservices, then it does not make sense to have a VM for each component. It is a waste of compute and human resources, because each VM needs to be configured and managed individually, increasing the workload of the operations team. Docker has proven to be great for such high-density environments and for smaller to medium deployments where you need to do more with fewer resources.

![](/images/devcor/devcor_8_3_2.png)

Docker uses a client-server architecture, where the client can be facilitated using a CLI for running commands against the server—Docker daemon. Docker also supports a Representational State Transfer (REST) application programming interface (API). The Docker daemon is responsible for creating and managing Docker objects such as containers, images, networks, and volumes. You use commands such as **docker run**, **docker stop**, or **docker build**, which clients send to the Docker daemon for execution.

Using these commands you can run container objects, stop them, pull new images, or build your own custom Docker images. While the container is the object that represents a running instance that you interact with, an image presents the object that every container is based on. The image is a read-only template that specifies the flavor of the operating system, the applications that are installed on top of that operating system, and instructions on how the container should use that image. Docker images are located on registries that can be public or private. An example of a public image registry is the official Docker registry—Docker Hub (https://hub.docker.com), which is used by default when you require an image that does not exist locally on your host.

Next, you will learn more about docker containers and how to interact with them to manage applications.

### Docker Containers

A container is an isolated running instance of an image and runs as a process on a Docker host machine. A container's image defines it along with any configuration options that are provided to it when it is created or started. You may have multiple containers that are created from the same image object. They share some similarities with virtual machines, but they work differently. Containers are processes running on an operating system host, and not hidden from the host like a VM. If you run Docker on Linux you can use the **ps aux** command to list all processes, among which you will find the running containers. They share the compute resources available on a host and they stop when the process stops. A running container process has its own file system and networking. Observe a typical scenario of running a Docker container.

![](/images/devcor/devcor_8_3_3.png)

When an image that is used for a container is not available locally, it needs to be fetched from the image registry. This can be done beforehand with the **docker pull** command, or it will be done automatically when using the **docker run** command, which starts a new container. The default image registry is the Docker Hub, but you can also use self-hosted solutions such as Harbor or GitLab Container Registry. You can also build your own images using the **docker build** command and push them to the registry of choice.

The **docker run** command is used for creating and running a new container. Typically, it includes a couple of attributes that control how the container behaves and an image that is used for the current container. When an image serves for an application that is tied to a certain port, you might want to publish that port outside the container. You can do that while running the container by specifying **--publish** (or shorter **-p**) followed by a host port and a container port. For example **-p 8080:80** would translate all the requests that come to the host on port 8080, to the port 80 inside the container.

The result of executing this **docker run** command is a new Docker container, exposing HTTP service that can be accessed by going into the browser and entering <host>:8080. Next, you will go through an example of executing a simple command on CLI, for running Docker container based of an Apache httpd image.

Starting a web server container for local development purposes and testing is a very common use case. To run a new fresh instance of an Apache httpd web server using Docker, you can simply execute the CLI command **docker run -dit --name web-container -p 8080:80 httpd:2.4**. The result of executing this command will be a new running container that serves web pages using the httpd web server. How this result is achieved is explained in the next steps.

![](/images/devcor/devcor_8_3_4.png)

Every client command starts with the keyword docker. Following the initial keyword are arguments that are instructing the docker daemon on operations that should be performed. For starting a new container, you would use the docker run command (1). Every time the run instruction is used, a new docker container will be created. If you want to run an existing container that is currently stopped, you would use the docker start combination. You could also create a new container without starting it, for that you can use the docker create sequence.

The command in the example is followed by a couple of arguments. First -dit (2), which is actually three arguments. One is -d(--detach), which stands for detached mode and runs container in the background. The others are -i(--interactive) and -t(--tty). These two keep the session to the standard input of the container terminal open, making sure that you can attach to an already running container. You can add a name to your container using the --name argument (3), but it is not mandatory. A unique name is generated for the container if one is not specified explicitly.

As discussed previously, when you are starting container services that are accessible over a certain port you can expose that port using the -p (--publish) argument (4). With the argument -p 8080:80, you open port 8080 on the host machine, making sure that all requests coming to that port are redirected inside the container on port 80. The last argument in this example (5) is the image that you want to use for the new container, in this case httpd version 2.4. You may also leave out the version, which will result in pulling the latest version. A list of all available public images and their versions can be found on Docker Hub registry (https://hub.docker.com/).

**Note:** With newer versions of Docker, new management commands were introduced to group specific commands by their usage. This was done to cope with the rising number of commands available to the client. New management commands typically start with the name of an object they are dealing with, for example, **docker container run**, or **docker image** build. Docker strives to be backward-compatible, so all the previous commands can be used without the management commands, while the new ones are available only under the appropriate management command. You can find more commands by running **docker --help.**

The docker daemon looks for the httpd:2.4 image in the local image cache (6), since it does not find anything, it contacts the remote image repository, by default the Docker Hub. Once the image is pulled, it continues with creating and running a container, the last line shows the unique id (7) generated by docker. The unique id is used for managing the container, e.g. to stop a container from the figure you can execute the **docker stop 629** command. You do not need to write the entire id; the first three characters are enough.

When you want to list the running containers, you can use the **docker container ls** command, and if you want to list all containers, including the ones not currently running, use the **docker container ls -a** command.

The previous example started the container and the httpd service was running immediately. The reason for this is that the run command did not specify any custom command to be executed when the container starts. Effectively, the container used the command that the official httpd image specified, which is the instruction for starting the Apache httpd service. You can change the default behavior by adding a custom command right after the image name.

![](/images/devcor/devcor_8_3_5.png)

As the example shows, the command **bash** is added at the end of the **docker run** instruction. The default starting command of httpd image is now replaced with the command **bash**. Instead of running the httpd process when the container starts, it jumps into the Bash shell, enabling you to work inside the container as if you would connect to the container over Secure Shell (SSH). This is also possible because of the attributes **-it**, which enable simulation of the terminal and the communication over the standard input. This change also means that the httpd service is not running inside the container, you need to start it manually from the Bash shell.

The Docker image spawns the process or the **docker run** command gets the process identification number (PID) 1. This means it is the first process that the Linux kernel starts and the UNIX signals affects. When the process with PID 1 stops, all the other processes are killed with the KILL signal. In the Docker scenario, when the process with PID 1 that runs the container stops, the container will stop as well. In the previous example, the container would stop if the process running the httpd server was killed. In this example, the container stops if you close the connection to the terminal running Bash shell.

Since the containers can be very lightweight, you could use this kind of approach to run one-off containers with diagnostic or maintenance commands on servers. This is useful on hosts where you do not want to install third-party tools. Instead, you could run them inside containers and then safely remove them without messing with the host system itself.

It is also possible to run commands inside the container after the container is started. If you create and start a container without any command, you could still access the Bash shell later by using the command **docker exec -it <containerid> bash**. You may run any command that the operating system or application inside the container supports.

Next, you will learn about the Docker images, why can they be so lightweight, and how can you build your own image.

**Docker Images**

A Docker image is an immutable object representing an application with all the dependencies and instructions on how to use it in the container run time. Images do not include the complete operating system with kernel and drivers, only the binaries that an application needs, while the host provides the kernel. This is what enables Docker images to be much smaller and faster than their VM equivalents. A container does not need to boot an operating system, it is essentially just starting an application.

Docker images that are based on operating systems such as Ubuntu can be very small because they do not include all the tools and software that come with normal operating system distribution that you install on a VM. It can happen that an image that is based on some Linux distribution does not even include the Bash shell binaries. You would need to manually install the missing tools when you run a container from the minimalistic image. Alternatively, you could build your own image using an existing operating system image as a base and package all the tools into a new image.

The images that you build should generate containers that are ephemeral by nature, meaning that a container can be stopped, removed, re-created, and replaced with minimum configuration needed. This follows the sixth factor of the 12-factor app methodology, which preaches about processes being stateless.

![](/images/devcor/devcor_8_3_6.png)

When working with Docker for packaging your application into containers, the normal process is to build a new image whenever there is a need for change inside the container. When you need to install new tools inside the container running your application, or the codebase of the application must be modified, instead of doing it from the inside, it is better practice to burn these modifications into a new image version. This way you maintain transparency across environments, making sure that the container on staging environment, running the same image version as the one on production, behaves the same. Following this approach also helps you to test and propagate changes easily across environments in an automated CI/CD pipeline.

Docker uses Union File System (unionFS) which allows it to present files and directories of separate files systems to be transparently overlaid, forming a single coherent file system—an image. Docker images are not Binary Large Objects (BLOB), they are composed from branches of file systems that are called layers or intermediate images. An image starts with a blank layer called a **scratch** or it can take an existing parent image for a base. For example, an httpd image can have Ubuntu operating system image for a base or your custom application image might use httpd image as the base image.

**Note:** When an image starts with the layer **scratch**, it is referred to as the base image. If your image starts with an existing Docker image, then that image is referred to as the parent image. Most images that you base your images from are parent images, so the terms are sometimes used interchangeably.

Every subsequent modification that you add to the parent image represents one layer of change, resulting in a new image. Each layer is stored in the cache and has a unique Secure Hash Algorithm (SHA) number. The advantage of layers is that Docker does not need to download or upload layers that are already in the cache. It never stores image data more than once on your file system and SHA numbers help with identification of existing layers on the system.

![](/images/devcor/devcor_8_3_7.png)

As the figure shows, the python:3.8 image is built on top of Ubuntu18.04 and the user made another custom image example-app:1.0, which uses the python image as the parent. If you need to build another custom image, Docker does not pull the python and ubuntu layers again, even though the object is brand new, because those layers exist. During the build you would see messages such as "using cache," meaning that the Docker daemon already has that layer that your new image requires. As a result, any consecutive calls for building an image will be faster because of the cached layers. However, whenever there is a change to one of the layers, that layer and all the layers below must be recalculated.

The process of building an image begins with a Dockerfile. A Dockerfile is a text document that contains instructions for assembling an application image.

A Dockerfile starts with a base, or parent image, which can be an image representing an operating system like Ubuntu, or it can be an image of an existing application. Examples of such images are NGINX, httpd, NodeJS, or simply a Python image that has all the necessary tools for playing around with the Python programming language. Many of the images available on Docker Hub are official images that Docker publishes, which are prepared and tested for optimal experience. You will usually take the official image and make your own image out of it with the settings and the codebase that your environments and deployments require.

![](/images/devcor/devcor_8_3_8.png)

Every command line in the file presents a layer of changes that adds up to the final image. The parent image is selected using the **FROM** keyword. The instructions that follow are the command-line instructions for installing required packages inside the image, setting environment variables and copying application code from the source folder of your host to the desired destination inside the Docker image. Using the **RUN** instruction, you can run any kind of commands inside the container. A common use case is using it for installing tools with the apt-get package manager, fixing the permissions (chmod), and similar operational tasks that you perform over CLI.

To run the container from the image without specifying a run command, either ENTRYPOINT or CMD must be defined on an image, otherwise it will throw an error. How to use the two commands depends on how you are trying to run the container. There can be only one of the two specified or both at the same time. There are also two modes, **shell** and **exec**, that change the behavior of the process execution. The next example of containerizing a Python application will show you the usage.

The image can be build using the command **docker build -t <image-tag> -f /path/to/Dockerfile**. The **-f** flag sets the build context to the path specified, by default it is assumed that the build context is in the location where you run the command. When building an image, Docker steps through the instructions in the Dockerfile, executing each in the order specified. As each instruction is examined, Docker looks for an existing image layer in its cache that it can reuse. If the cache is invalidated, all subsequent Dockerfile commands generate new layers and the cache is not used.

When building a Dockerfile, the best practice is to order instructions from the less frequently changed commands and dependencies, to the more frequently changed. This order ensures that the build cache is reusable and the build process takes less time. A typical Dockerfile would, after the parent image entry, install the tools that are needed for building the application. It would then install or update library dependencies, include the application code, and at last run the application.

Once the image is built locally, it can be pushed to a remote registry using the **docker push <image-tag>** command. Now you will go through an example of packaging and running a Python Flask application inside a Docker container.

### Application Container Example

As an example of application that will be containerized, observe the following code.

```python
  from flask import Flask
  import os

  app = Flask(__name__)


  @app.route("/")
  def home(): 
      course = os.environ['COURSE']
      return f'Welcome to Cisco DevNet {course}!'


  if __name__ == "__main__":
      app.run(debug=True, host='0.0.0.0')
```
It is a very simple web application that is written in Python Flask framework. The functionality that it has is printing a message that is composed from one system environment variable, when a user opens the web page. Besides the application code, the code repository includes the Dockerfile with the instructions on how to prepare this application into container. It also includes a **requirements.txt** file that the Dockerfile uses to install the Flask dependency into the image.

Observe the following Dockerfile that is included in the code repository.

![](/images/devcor/devcor_8_3_9.png)

The image will be based on the python:3.8 parent image. Next instruction is setting a COURSE environment variable to have the value “DEVCORE”. It will be used by the Flask application for printing out a message. The **COPY** command copies all the contents of the current folder (**program.py** and **requirements.txt**) into the /app folder inside the image. After copying the files, it changes the working directory with the **WORKDIR** command so that any consecutive commands will be executed inside that folder. The **RUN** command in the Dockerfile is used for installing the Flask framework with Python pip package manager.

If not explicitly specified, the Flask application listens on port 5000. The Dockerfile **EXPOSE** command in this case has more of a documentation value, you will still need to use the --publish argument when running a container. The end of the Dockerfile is typically reserved for instructions on how to run the container when you start it. In this example, since you will be working with a Python application, the ENTRYPOINT uses the python3 executable for making the container start. The **CMD** is used to append an argument to the **ENTRYPOINT**. So, whenever a container based from this image starts, the **python3 program.py** command will run the Flask application. When the application is stopped the container will stop as well.

As mentioned before, **ENTRYPOINT** and **CMD** can be used interchangeably. In this example, ENTRYPOINT is used to set the image main command when the container starts, and CMD sets the default arguments to the ENTRYPOINT. You could also use only ENTRYPOINT with value “[“python3”, “program.py”]”, but then you would not be able to append a custom command when running a container. This way you can change the default “program.py” argument when running the container with another module e.g. “test.py” and execute a set of test cases. In the example, they both use the exec format, which can be identified by arguments inside the square brackets. The exec form is preferable because it starts the specified command process as PID 1, making sure that the UNIX signals sent to the container are sent to your spawned process.

Observe the image being built using the tag “example-app”.

```bash
  ➜  docker build -t example-app .
  Sending build context to Docker daemon  4.096kB
  Step 1/8 : FROM python:3.8
  ---> d6a7b0694364
  Step 2/8 : ENV COURSE DEVCORE
  ---> Running in e2cd1d068a69
  <…output omitted …>
  Step 8/8 : CMD [ "program.py" ]
  ---> Running in 81b848208485
  Removing intermediate container 81b848208485
  ---> a87dd285f32a
  Successfully built a87dd285f32a
  Successfully tagged example-app:latest
```

The command was executed in the current folder because the “.” (dot) was used for the path.

After the image was successfully built, you can create a new running container that serves the Python Flask application on port 5000.

![](/images/devcor/devcor_8_3_10.png)

### Docker Networking

Docker should run one process per container. Your applications will usually require multiple running processes to perform its functionality. For example, a containerized web application can use one container for the front end, one for back-end functionality, another container for messaging queue, one for database, and so on. These containers usually must be connected to each other, and some of them need to connect to the non-Docker workloads. For such purposes, Docker networks can be configured, to provide more isolation and security for the containers.

A Docker networking subsystem is pluggable using network drivers that provide the core networking functionality.

The network drivers are as follows:

* Bridge: The default network driver, usually used when your applications run in standalone containers that need to communicate.

* Host: Removes the network isolation between the container and the Docker host to use the hosts networking directly.

* Overlay: Overlay networks connect multiple Docker daemons together and enable swarm services to communicate with each other.

* Macvlan: Macvlan networks allow you to assign a MAC address to a container, making it appear as a physical device on your network. The Docker daemon routes traffic to containers by their MAC addresses.

* None: Disables the networking for the container

**Note: **Swarm Mode is a clustering solution that is built inside Docker for automating the container lifecycle. It can automatically scale containers in or out and re-create them when they fail.

When you start Docker, a default bridge network is created automatically. All new containers are, by default, connected to the bridge network unless otherwise specified. You can create your own custom bridge networks that are superior to the default network called **bridge**. You can list Docker networks using the command **docker network ls**.

Containers connected to the same user-defined bridge network expose all ports to each other, and no ports to the outside world. This setup enables containerized applications to easily communicate with each other without exposing themselves out to other workloads by default. If you created two containers, web front-end and web back-end, users from the outer world will need to access the front-end service, but not the back-end directly. When starting the front-end container, you would publish its ports using the **-publish** flag, but not the ports of the back-end container.

![](/images/devcor/devcor_8_3_11.png)

A new container network can be created using the command **docker network create --driver**. The driver flag is optional and used when you want to create a nonbridge network.

When you want to connect existing containers to a network, you use the command **docker network connect <net-id> <container-id>**. You disconnect a container from a network using **docker network disconnect**. Containers can be attached to a network when they are created using the **--network** flag, for example **docker run -it -p 5000:5000 --network custom-network example-app**.

The custom user-defined bridges provide automatic DNS resolution between containers. If you are using the default bridge network that Docker creates, a container in that network can only access other containers in the network by their IP addresses. When naming your containers and using a custom bridge network, you can reference another container by using its name, which Docker will know how to resolve using the built-in DNS.

By default, traffic from containers that are connected to the default bridge network is not forwarded to the outside world. To enable forwarding, you need to change two settings affecting the kernel on the Docker host. First, you need to allow IP forwarding on Linux kernel with the command **sysctl net.ipv4.conf.all.forwarding=1**,and second, you need to change the iptable's FORWARD policy to ACCEPT the requests. You can do the latter using the command **iptables -P FORWARD ACCEPT**.

**Persistent Data**

Containers are meant to be immutable and disposable by design. You have learned how easy it is to create a new container whenever you want from the provided image. When containers are removed, all the data that was stored inside the container is lost. If your application is storing data in the running container, persistent data is something that you will need to maintain the state of the application throughout its lifecycle.

In the world of containers and their autoscaling and recovery features, persistent data is a unique problem. Docker enables you to recycle containers without losing any unique data that was generated in the meantime.

File system data is not lost when you restart a container, it is lost when you remove a container.

Docker uses three built-in solutions for persisting data:

* Volumes
* Bind mounts
* tmpfs mounts

Volumes are a configuration option for containers that create a special location outside of the container’s Union File System (unionFS) to store unique data. This configuration preserves data across container removals and enables you to attach the Volume to any container you want. On a host, the volumes are stored under the path **/var/lib/docker/volumes** where the non-Docker processes should not be able to change the data. A container will see a Volume like a local file path. Volumes are the best way to persist data in Docker.

Bind mounts are simpler way of sharing a host directory file into a container. A Bind Mount maps a Docker host file path into the container file path. The difference between Volumes and Bind mounts is that the latter can be stored on any path in the host system. Another difference is that non-Docker processes can modify the data on that path at any time.

A tmpfs mount is stored in the host system's memory only and is never written to the Docker host file system.

A Volume would typically be included in a Dockerfile when defining an image. For example, if you find the official MySQL image on the Docker Hub, it will have a line that says **VOLUME** **/var/lib/mysql** in the Dockerfile. When you run the MySQL container, a new volume will be created. You can list the volumes using the command **docker volume ls**. Inspect the next example.

```bash
  docker volume ls     
  DRIVER              VOLUME NAME
  local               0a7fc5b9aa490e06edd95fb7e7693c03b3061ee75cf7abb2056f45d20abee292
```

You can list additional information about the volume using the following command:

```bash
  docker volume inspect 0a7fc5b9aa490e06edd95fb7e7693c03b3061ee75cf7abb2056f45d20abee292
  [
      {
          "CreatedAt": "2019-11-11T10:54:38+01:00,"
          "Driver": "local,"
          "Labels": null,
          "Mountpoint": "/var/lib/docker/volumes/0a7fc5b9aa490e06edd95fb7e7693c03b3061ee75cf7abb2056f45d20abee292/_data,"
          "Name": "0a7fc5b9aa490e06edd95fb7e7693c03b3061ee75cf7abb2056f45d20abee292,"
          "Options": null,
          "Scope": "local"
      }
  ]
```

The interesting bit here is the Mountpoint tag. It shows you where on the host file system the volume was mounted. If you navigated to that path, you would find the mysql database data, which is stored inside the container under the path **/var/lib/mysql**. The data outlives the executable. If you stop and remove the container, the Volume will remain there.

You can create a volume explicitly using the docker volume create command, or Docker can create a volume during container or service creation. A volume can be mounted into multiple containers simultaneously. You can create a named volume using the command docker volume create or when starting the container, for example, **docker run -d -v example-volume:/var/lib/app-data example-app**. This command would create a new **example-app** container with a named volume **example-volume**, that maps the host local path **/var/lib/docker/volumes/example-volume/** into the container at path **/var/lib/app-data**. Any data change on the path inside the container will be reflected in the host stored data. When the same volume name is used on a different container, Docker will not create a new Volume. Instead, it will use the existing one, mapping the existing data into the new container at the same, or if required, a different path.

![](/images/devcor/devcor_8_3_12.png)

Bind mounts have limited functionality compared to volumes. They basically point two locations, host and container, to the same directory. The file or directory's full path on the host machine references it. The file or directory does not need to exist on the Docker host already. It is created on demand if it does not yet exist. A Bind mount cannot be specified in a Dockerfile like a volume, they are always specified with the **docker run** command. The Bind mount is specified with the **-v** flag, and the path of the Bind mount always starts with the forward slash (/), for example, **docker run -d -v /home/cisco/app-folder:/var/lib/app-data example-app.**

## 8.5 Kubernetes Introduction

The way applications are developed and deployed has changed rapidly in recent years. Every aspect of the application lifecycle is happening faster than ever. Change requirements come in smaller batches, which mean that the deployment model needs to follow that trend as well. Applications are not big monoliths anymore. You chunk them into pieces called microservices. Deploying microservices in such a fashion requires special attention to have a system that is functional and always available. Many operational tasks cannot be done manually anymore; the systems are simply too complex and intertwined. Kubernetes is a tool that helps to manage such rapid deployments with ease. It is an essential tool in modern software development practice.

More flexible architectures are slowly replacing monolithic applications running as a single or a few processes on a few servers. Traditional monoliths tend to be less frequently updated. Changes usually come in bulk and are handed over from the development team to the operations team. The operations team is responsible for deploying and monitoring the package that is prepared by the development team. The deployment is typically manual or supported by some arbitrary scripts, and healing and migration of monolithic applications in case of detected failures is still a manual process.

More new application developments take the microservices architecture into the consideration. For legacy monolithic applications, there are efforts to break the tightly coupled components into smaller, independently running services. Microservices enables each component to develop, deploy, update, and scale individually. While this process requires you to follow rules during the development, such as making sure that your applications are stateless, it enables you to change them as often as needed.

Containerizing applications helps to minimize the gap between the development and operations teams. With a larger number of deployable services, data centers, and a higher frequency of requests, it becomes increasingly difficult to keep the entire system running smoothly. With microservices, there can be dozens of containers that work cohesively to deliver application functionalities. It’s important to know where to put components to achieve higher resource utilization, how to scale when traffic grows, and how and when to interfere when components are performing poorly. Doing all these tasks seems impossible to do manually, and it probably is; therefore, some sort of automation is required. The individual services should be automatically assigned to your available servers, when a container stops working, another needs to start, when higher load is observed, the scaling of critical components should be automated. This is where Kubernetes (K8s, pronounced "kates") can help you.

![](/images/devcor/devcor_8_5_1.png)

Kubernetes (K8s) is an open source orchestration tool for automating deployment, scaling, failover, and management of containerized applications. It is the third container-management system developed at Google after Borg and Omega and was released in 2014. The main design goal behind K8s is to make use of deployment patterns for seamless deployment and management of complex distributed systems, while still benefiting from the improved utilization that containers enable.

K8s is a system that helps you with deployment and management of containerized applications. It works in harmony with Docker but is not limited to orchestrating only Docker containers. You can use other container technologies, such as LXC Linux Containers or rkt.

Because of isolation, containerized applications do not interfere with other applications running on the same server, which is especially important for cloud providers so they can utilize the same hardware for different customers and still achieve the best utilization possible. Kubernetes can be the brain behind running applications from different customers on thousands of server nodes. It can do so by abstracting the set of computational resources for the user as if it was all one big compute node. K8s makes the development and deployment of applications easier by simplifying management of the infrastructure and hiding complexities of distributed systems behind a simple declarative interface.

![](/images/devcor/devcor_8_5_2.png)

The figure displays the high-level operational view of the K8s tool. After the application is composed and packaged into containers, you need to prepare a deployment descriptor for the K8s. You will dive into the details of the deployments later, for now think of it as a recipe for K8s that instructs them how the components in your application should work together. The declarative way of application deployment is especially valuable when your applications follow the microservices architecture, where there are many different, small components talking to each other via HTTP REST or some other messaging interface. You need to instruct K8s how these components can reach each other and how they operate. While there are many details you can specify in these templates, your descriptors will be short for basic management of the applications lifecycle.

The communication with the cluster of the compute nodes will be done via the Kubernetes control plane node. The control plane node has units that know how to accept and interpret your requests and then make the appropriate steps to execute the desired actions. The control plane node does many things by itself, automating the details of the applications lifecycle. For example, you do not need to worry about where exactly your application is being deployed; for you, the cluster of worker nodes behaves as a single entity. When an application failover happens, K8s will detect it and automatically heal the faulty container. If the entire server node supporting your applications collapses, K8s will redeploy the applications on a healthy node without any interference from the operations team. You can, of course, guide these operations and set some rules by which K8s operates, but most of the work is then done entirely by the K8s system.

K8s is used extensively by cloud providers. It enables them to provide users with a platform for deploying applications, which is, from the user's perspective, easy to use, and, from the operations perspective, easy to manage. It simplifies their work by not needing to know any details about the applications running on their cloud platform. Though K8s shines on cloud infrastructure, it can be very useful on any kind of local on-premises infrastructure where you have multiple server nodes for application deployment.

So, what exactly can K8s do for you? Containerization helps with packaging applications for rapid and consistent deployments, Kubernetes helps you make sure those deployments end up on an optimal compute node, which is exposed to the public with enough resources, acting as an API between the development process and the infrastructure where the applications are being deployed.

Kubernetes offers services such as the following:

* Service discovery and load balancing
* Storage orchestration
* Automated rollouts and rollbacks
* Automatic bin packing
* Self-healing
* Secret and configuration management

![](/images/devcor/devcor_8_5_3.png)

The list roughly covers the functionalities of K8s. When deploying application containers onto a K8s cluster, the containers need a way to communicate amongst themselves and with the outer world. K8s gives each component in a deployment its own IP address and a single Domain Name System (DNS) name for a set of containers that work with the same application. It also load balances the traffic among containers residing behind a DNS name.

The state of the application inside containers is ephemeral, and when the container is moved or it dies, you lose that state. In K8s, you can mount various local or a cloud provided storages, which deployments can use through the K8s Volumes abstraction.

When deploying a new set of containerized components, you can describe the desired final state of the containers and K8s will automatically adjust the actual state to the desired state. For example, you can define that there should always be two back-end application containers, if it happens that one shuts down, K8s will automatically spin up a new one without any manual interactions from the operations team. It can also autoscale up or down based on the desired performance indicators.

When deploying a K8s system, you provide the cluster of compute nodes that it can use for running containerized tasks. Deployment descriptors can specify how much CPU and memory each container needs and K8s will place containers onto the right cluster nodes to make the best use of the available resources. You can also define health checks that tell K8s if a container is really performing its function or not. Even if the container is running, it does not mean it is working correctly. For example, a health check can be a simple REST call to the application’s API to see if the correct response comes back. If the health check fails, K8s tries to repair the situation by first stopping the advertisement to the clients and replacing the malfunctioning container with a new one.

K8s also lets you store and manage sensitive information such as passwords, OAuth tokens, and Secure Shell (SSH) keys. You can deploy and update secrets and application configurations without rebuilding your container images, and without exposing secrets in your stack configuration, thus following the third, configuration factor in the 12 factors methodology.

Kubernetes generally simplifies the development and deployment of an application. A lot of work from the days prior to K8s is now automated, you do not need to figure out which node the cluster-specific container should be installed on to utilize your servers optimally, all this is done by K8s. It also enables you to do canary deployments with minimum effort.

**Note:** Canary deployments is a practice of pushing a new version of your application to only a part of all users, to prevent possible problems to be exposed to all. Once you are confident that the application is stable, and users do not complain, you roll out changes to everybody. In K8s, this can be done by balancing traffic between pods where the new version is running.

You will continue to explore the K8s tool by going through its architecture and the components that enable its functionalities.

### Kubernetes Architecture

When Kubernetes is deployed, you get a cluster of nodes. There are two types of nodes in a K8s cluster. Worker nodes in a cluster are sets of machines that run containerized applications that K8s manage. There must be at least one. The other node is the Control plane node that is hosting the K8s control plane, responsible for managing the worker nodes and the deployed objects on them. There is typically one control plane node, but you can have multiple control plane nodes for high-availability setup.

![](/images/devcor/devcor_8_5_4.png)

The control plane node makes global decisions about the cluster and it detects and responds to events coming from the worker nodes. There are several components that can run on a single control plane node or they can be split on multiple nodes.

The components on the control plane node are:

* API server (kube-apiserver)
* etcd
* Scheduler (kube-scheduler)
* Controller Manager (kube-controller-manager)

The API server is a component of the K8s control plane that serves as the front end for communication with the control plane. The implementation of the Kubernetes API server is called kube-apiserver. The component etcd is a distributed key-value store that is used for persistent storage of the cluster configuration and data. The scheduler unit, called kube-scheduler, schedules your applications by assigning a worker node to each component in your deployment. It takes factors such as application resource requirements, policy constraints, and other specifications when deciding on which worker node an application should be scheduled. The fourth component, controller manager, named kube-controller-manager, performs cluster-level functions, such as handling node failures, maintaining the correct number of deployed components, replicating them if necessary, and so on. The components on the control plane node are used for controlling the state of the cluster, but the actual applications run on the worker nodes.

There are also several components on each of the worker nodes in a K8s cluster that take care of the run-time environment. They provide services to your containerized applications, monitoring them, making sure that they are running, and that you can access them on the network.

The components on these nodes are:

* kubelet
* kube-proxy
* Container Runtime Interface (CRI)

The **kubelet** component is an agent that runs on every node in a cluster. It makes sure that containers created by K8s are running and healthy (based on the provided specifications). The **kube-proxy** component is a K8s Service proxy that is used for maintaining network rules. These rules are used for communication to your application containers from inside and outside of the K8s cluster and for load-balancing of the network traffic between these containers. The container run time is software that is used for running the containers. K8s supports Docker, containerd, cri-o, rktlet, and any implementation of the Kubernetes CRI.

Other components available on the K8s cluster include a web UI, which allows users to manage and troubleshoot applications running in the cluster and the cluster itself. A DNS server CoreDNS component is also included in the cluster, by default, and containers that K8s start include this DNS server in their searches.

The basic workflow of deploying an application to a K8s cluster is to first containerize the application components using your preferred provider of this technology, and then make the images available on a chosen image registry. The K8s cluster needs to have access to that registry so that the kubelet component on the worker nodes can pull images and create containers. Once the containers are ready, the application is described in a deployment template that specifies all the containers that an application consists of and the desired state of the running instances. The descriptor is then passed in a request to the control plane node API server.

As discussed, a single application can consist of multiple containers that together present the entire application. Especially in a microservices architecture, components can be divided into dozens of small functionalities, each packaged into its own container. A definition of an application on your K8s cluster must include the container images that the application needs, how the containers are related to each other, which of them must run on the same worker node, and which can be deployed anywhere in the cluster. You can specify many other details of the deployment, such as how many instances, Pods, of the individual containers you want to run (replication). For example, you expect to have higher traffic, so you specify that you want to have at least two load balanced back-end containers. Other details that can be specified include how the components communicate, which container exposes a service to the external world, and the minimum computational requirements that are needed for a specific container can be included, and many other specifics.

![](/images/devcor/devcor_8_5_5.png)

The figure shows the process of deploying an application on the K8s cluster. When you describe your application and package it into a request for a deployment, the Scheduler component on the control plane node starts the procedure of scheduling the selected containers on the worker nodes. The Scheduler’s decisions are based on the availability of resources on the cluster nodes and any special requirements that are provided in the deployment template. When the containers are scheduled successfully, the kubelet component on the worker nodes does the work of pulling the container images, via the installed container environment, and running the containers.

The example in the figure shows a deployment template with three different containers. Each of the containers in K8s is logically encapsulated into an object called a pod. Pods are the smallest deployment units on K8s and define specifics of individual container deployments. You may group multiple containers, which are by their nature that is tightly coupled, inside the same pod. When multiple containers are grouped in a single pod, the Scheduler will assign them to the same worker node. When you scale on K8s, you scale the pods. Multiple containers inside the same pod would mean that they cannot scale separately. A more common use case is to have only one container per pod. The template that you prepare also specifies the number of replicated pods. When running replicas of pods, the traffic is load balanced between them.

The second container will be deployed in four instances in this example. Depending on the type of deployment, K8s will maintain the state that you specified at the time of the deployment. This means that if you always require four pods of the second container to be up, and K8s will make sure that, if a container inside one pod goes down, it will restart it, or it will provision a new pod. But K8s does not maintain the state of the deployment when the traffic exceeds the capabilities of single or multiple containers. For example, when the CPU load or memory usage is increasing, K8s will scale up by deploying extra pods to load balance the requests. It can also automatically scale down when the traffic is not at its peak, releasing resources to other consumers.

### Deploying with Kubernetes

Before you start playing around with a Kubernetes cluster, you need to have one. The installation of a multinode K8s is not a simple exercise, especially if you are not fluent in Linux administration and networking. If you have the resources and you are willing to go through that path, there is a list of installation procedures on the official documentation site (kubernetes.io).

Luckily, there are easier ways to get a small local K8s cluster, and getting a bigger, full-fledged cluster is also possible to get without dealing with the installation procedures. The simplest way to a functional K8s cluster is by using the Minikube tool. Minikube is great for local testing and development on the K8s cluster. It does not have all the features that a full multinode installation has, but it is enough to get you going and it can be installed on a single Linux, macOS, or Windows machine. The installation procedure can be found on the https://github.com/kubernetes/minikube site. For a more mature cluster, you may use a platform such as the Cisco Container Platform or existing cloud providers such as Google Kubernetes Engine (GKE) or Amazon Elastic Kubernetes Service (EKS). With cloud providers, you can deploy a K8s cluster in minutes by providing some basic information about the node’s flavor and hitting the deploy button.

Whatever technology you will use to set up a cluster, you will most likely interact with it from your local machine using the kubectl CLI client.

![](/images/devcor/devcor_8_5_6.png)

The setup of the kubectl tool is done on the machine you want to use for the management of the cluster, and it must be configured to work with a specific K8s cluster. The setup and the configuration instructions can be found on the K8s documentation pages. Once you have a running K8s cluster and a client that can interact with the API server, you can deploy your containerized applications on top of it. There are several ways of doing that, and the most common and advised procedure is to create a Kubernetes Deployment configuration. The Deployment tells K8s how to create instances of your application and how they should behave once they are deployed. When the K8s scheduler decides on which worker nodes the components will end up, a Deployment Controller continuously monitors the state of these components. You will hear more about Pod management and the controllers in the following examples.

It is time to put the theory to work and deploy an application onto the K8s cluster. For that you will use the kubectl CLI tool to describe and deploy the application. As you will learn later, this request could also be documented in a YAML file. You will push a previously created Python Flask application to the K8s cluster using the **kubectl run devnet-example --image=gcr.io/devnet-258113/devnet-flask-example:latest --port=5000 --replicas=3** command.

The deployment performed the following things:

* It searched for a suitable node where the pods could run.
* It scheduled the application to run on those worker nodes.
* It configured the cluster to monitor, heal, and reschedule pods on a new node when needed

The issued command created a Deployment Controller, which is a higher-level concept that manages, first, a dedicated ReplicaSet Controller, and second, three pods running the containerized application. In a few words, the controllers are K8s way of providing automated functions to the deployed containers such as, automatic scaling, replication, updates, and similar. The pods are replicated, meaning that they run the same container in multiple instances on the cluster and the traffic can load balance between them. The controllers set up the contract between the controllers and the pods, making sure that the actual state matches the desired state. The desired state is defined by the deployment descriptor at the time of deployment, but it can also be changed during the lifecycle of the application. The purpose of the ReplicaSet Controller is to guarantee that a pod or a homogeneous set of pods is always up and available. In this example, the ReplicaSet Controller will make sure that there are always three pods running somewhere in the cluster.

![](/images/devcor/devcor_8_5_7.png)

To inspect whether your request was successful, you can retrieve information about the deployed objects using the **kubectl get** command. If the deployment was successful, you should see a Pod and ReplicaSet on your cluster. Observe how to query for this information.

```bash
  kubectl get pods
  NAME                               READY   STATUS    RESTARTS   AGE
  devnet-example-6bf9b76dbd-445wc  1/1        Running    0             2m6s
  devnet-example-6bf9b76dbd-5knnx   1/1        Running    0             2m6s
  devnet-example-6bf9b76dbd-kbqm5  1/1        Running    0             2m6s
```

There are three Pods with the status 'Running', meaning that they are working. Examine the state of the ReplicaSet. Use the following command:

```bash
  kubectl get rs
  NAME                          DESIRED   CURRENT   READY   AGE
  devnet-example-6bf9b76dbd   3               3           3          1h
```

As expected, there is a single ReplicaSet object that handles three pods, shown in the output. All three are ready, but you already know that since you have inspected the pods before.

**Note:** Object names can be abbreviated when using them with the kubectl tool. ReplicaSet can be replaced with rs, deployment with deploy, etc.

So, there are three active pods, which means you should be able to access the containerized application through any of them. It was mentioned previously that each Pod gets its own IP address, but that address is internal to the cluster and is used for communication between containers, so you will not be able to access the application from your local machine at that address.

To expose the application to the outside world, you will need to create a Service object. A service object is not useful only for exposing an application to the external network, but also is used internally. As you know, the Pods are mortal, they can die and respawn on another location and by doing that they also change their IP addresses. A Service exposes a single address that other containers can rely on that it will not change. There are multiple types of Services that can offer service exposure such as NodePort, ClusterIP, and LoadBalancer. You will use the LoadBalancer type in the next example. You will hear more about the Service objects later, now, observe the command for exposing the deployed application using a cloud provider public IP address of a load balancer.

```bash
  kubectl expose deployment devnet-example --type="LoadBalancer" --name devnet-svc
  service/devnet-svc exposed
```

When the request for this new service is processed, you may get the state of the service by issuing the following command:

```bash
  kubectl get service               
  NAME          TYPE           CLUSTER-IP   EXTERNAL-IP    PORT(S)                AGE
  devnet-svc   LoadBalancer     10.0.6.246      34.89.63.240      5000:32167/TCP   13m
  kubernetes   ClusterIP            10.0.0.1          <none>             443/TCP               27h
```

It may take a while for the external IP to show up if you are using a cloud provided K8s cluster, since a load balancer needs to be provisioned first. When the cluster is ready, it will show you the external IP on which you can access the deployed container.

```bash
  curl 34.89.63.240:5000
  Welcome to Cisco DevNet DEVCORE!
  running on devnet-example-6bf9b76dbd-5knnx
```

The deployed application is now publicly available over the load balancer IP address. Even if the Pod is moved and its internal address changes, a new one will still be available under the same service IP address as the figure displays. The request to that IP address is also load balanced between the three Pods.

![](/images/devcor/devcor_8_5_8.png)

To get more information about the deployment, such as on which worker nodes your pods are running, use the following command:

```bash
  kubectl describe pod devnet-example-6bf9b76dbd-5knnx
  Name:           devnet-example-6bf9b76dbd-5knnx
  Namespace:      default
  Priority:       0
  Node:           gke-devnet-cluster-default-pool-ee4f7bd9-9kkj/10.154.0.11
  Start Time:     Wed, 06 Nov 2019 23:30:13 +0100
  Labels:         pod-template-hash=6bf9b76dbd
  run=devnet-example
  Annotations:    kubernetes.io/limit-ranger: LimitRanger plugin set: cpu request for container devnet-example
  Status:         Running
  IP:             10.20.0.9
  IPs:            <none>
  Controlled By:  ReplicaSet/devnet-example-6bf9b76dbd
  <"... output omitted "...>
```

### Pods and Controllers

By now you should have a rough picture of the K8s components and how to create a simple deployment. Understanding how the most basic components such as Pods and Controllers work is crucial when setting up an application for deployment. Pods represent the basic deployable unit in K8s. Instead of deploying containers individually, as you are used to from Docker, K8s deploys a Pod, which encapsulates one or more containers and behaves as a single entity. But why does K8s use the Pod objects instead of using containers directly? From the 12-factor methodology and the basic principles of building Docker images, you learned that containers should be designed to run only a single process (plus the spawned child processes). If you want to manage multiple containers as a single unit in K8s, you need a higher-level construct, and this is where the Pods come into the picture.

Pods give you the advantage of process isolation that containers provide, while still being able to deploy them together to make them look like they are working together. An example of using a pod in K8s is when you have a main web application process running in one container, supported by a reverse proxy service for logging and limiting the HTTP requests running in the second container. Web application and reverse proxy containers can be bundled in the same pod so that K8s can manage the lifecycle of both containers as a single entity. The containers inside a Pod also share the same hostname and IP address, so you need to make sure that those containers bind on different ports. The containers in a Pod can also communicate via localhost address, because they share the same loopback address.

Until now, you deployed the application using **kubectl run** command, which gives you a limited set of properties that you can configure. Pods and other Kubernetes resources, such as Deployments and Services, are usually created by posting a YAML or JSON manifest to the API server. The request with the manifest can still be performed with the kubectl command, but it also gives you the flexibility to use the REST interface. Manifest files are the preferred option because they can be versioned in a Version Control System (VCS).

Here you will find examples that will set you on the path of writing YAML manifests for different deployment objects, but for all the properties and examples you will have look into the documentation (http://kubernetes.io/docs/reference/). Since some of the Pods exist from the previous CLI deployments, you can export a YAML descriptor for reference using the **kubectl get** command.

```bash
  kubectl get pod devnet-example-6bf9b76dbd-5knnx -o yaml
  apiVersion: v1
  kind: Pod
  metadata:
    annotations:
      kubernetes.io/limit-ranger: 'LimitRanger plugin set: cpu request for container
        devnet-example'
    creationTimestamp: "2019-11-06T21:54:23Z"
    generateName: devnet-example-6bf9b76dbd-
    labels:
      pod-template-hash: 6bf9b76dbd
      run: devnet-example
    name: devnet-example-6bf9b76dbd-5knnx
    <"... output omitted "...>
  spec:
    containers:
    - image: gcr.io/devnet-258113/devnet-flask-example:latest
      imagePullPolicy: Always
      name: devnet-example
      ports:
      - containerPort: 5000
        protocol: TCP
      resources:
        requests:
          cpu: 100m
  <... output omitted ...>
```

As you can see, there are a lot of properties for a single Pod. Luckily, when deploying a new Pod, you do not need to specify them all. Most of them are automatically added by the K8s system. Observe how to prepare a YAML descriptor for a Pod. Create a file named devnet-pod.yaml and add the following content.

![](/images/devcor/devcor_8_5_9.png)

The YAML Pod descriptor starts with the version (1) of the K8s API. Under the metadata, there is only a name (2) for a Pod, but you could include other parameters such as labels. Under the spec definitions, you specify the containers (3) that will be part of the devnet-pod Pod. In the example, there are two containers running homogenously inside the same Pod. You can specify the port on which a container will listen for requests, but you could also add other details such as liveness or readiness probes, the exact worker node on which you want to push the Pod, and similar details.

Once the YAML manifest is ready, you can simply deploy it using the command **kubectl create -f devnet-pod.yaml.**

**Note:** Commands like **kubectl create, run, scale,** and **expose** are examples of imperative commands and imperative objects (create -f). They overwrite changes when applied, and they raise an error if the resource exists. With imperative commands, you tell the K8 API what you want to create, replace, or delete. The other approach is declarative object management using the **kubectl apply** command. This approach is suitable for incremental changes, where changes on the live objects are maintained even if you apply other modifications to the same object. In declarative management, you define what you want your cluster to look like and not what you want to create at a given moment. The declarations are done in the YAML files that are edited and applied as needed.

So, now you know why Pods exist and how you deploy them individually. In your work, you will almost never deploy Pods directly. As seen in the example when you used the **kubectl run** command, there were three different objects created: Deployment, ReplicaSet, and the Pods. If you create Pods that are not controlled by some other entity such as ReplicaSet, you lose the magic of automatic replacement of Pods when a node becomes unavailable. Entities such as ReplicationController or ReplicaSet are referred to as Controllers.

![](/images/devcor/devcor_8_5_10.png)

At the beginning of this K8s journey, it was preached how K8s keeps the Pods healthy and automatically reschedules them when necessary. When deploying uncontrolled Pods, such as in the previous YAML example, you cannot create multiple replicas with one Pod deployment. You could deploy the same Pod multiple times by hand, but then there is no means of automatic preservation of the number of replicas or redeployment of Pods when nodes go offline. Instead of deploying Pods directly, you can use a Controller such as ReplicaSet, as shown on the figure, that can deploy multiple replicas of the same Pod while preserving the desired number of replicas throughout entire lifecycle of the deployment. You could prepare a YAML descriptor that defines a ReplicaSet deployment, but the suggestion is that you write an even higher-level Deployment controller that automatically creates the ReplicaSet controller.

**Note:** The ReplicationController was the first controller on K8s that enabled replication and rescheduling of Pods. While it is still in use in many production systems, it is being deprecated and replaced by the ReplicaSet. The usage and function of them is almost identical, with ReplicaSet having more expressive pod selectors.

Observe the example of creating a new YAML descriptor using the Deployment controller.

![](/images/devcor/devcor_8_5_11.png)

If you go through Deployment YAML descriptor, you can see it is similar to the earlier Pod descriptor. The change is in the setting that tells the control plane that this is a Deployment (1) and not a simple Pod. With the Deployment, you can now specify the number of replicas (2), which in this case is set to three. Three identical Pods will be created so that K8s can load balance the request between them. The next change is that you need to define a template tag, inside which you specify the Pods (3) that compose your application. It is also advised that you label your Pods (4). It is easier to list Pods by filtering when they are labeled, and sometimes you will need to use selectors in other objects and controllers for selecting a specific set of Pods based on a label, such as, when manually updating the Pods.

So, when you execute the **kubectl create** command, the Deployment will create a ReplicaSet and three Pods. You have seen this in the previous example when you used the **kubectl run** CLI command. Now, inspect what happens if you remove a Pod.

```bash
  kubectl delete pod devnet-deploy-7bcbd9bdfd-plmc8
  pod "devnet-deploy-7bcbd9bdfd-plmc8" deleted

  kubectl get pod
  NAME                             READY   STATUS    RESTARTS   AGE
  devnet-deploy-7bcbd9bdfd-27hcd   1/1         Running    0           2m6s
  devnet-deploy-7bcbd9bdfd-9n8vz   1/1         Running    0            3s
  devnet-deploy-7bcbd9bdfd-wzxf5   1/1         Running    0            2m6s
```

After the Pod was deleted, the ReplicaSet controller automatically replaced it. You can see that the new one has been running for only a couple of seconds. It is also not the same Pod as before, as shown by the name of the Pod. This is what a ReplicaSet controller provides you; no matter what happens with the controlled Pods, it will maintain the defined number until it has the resources on the cluster. But if this is the function of the ReplicaSet, why would you not create a ReplicaSet descriptor instead of Deployment?

The Deployment Controller is useful when it is time to update the application. Eventually you will make some changes on the application containers, either to introduce bug fixes or new features. The Deployment controller, sitting on top of ReplicaSet, simplifies application updates. Observe how easy it is to change a version of the container image.

```bash
  kubectl set image deployment devnet-deploy devnet-container=gcr.io/devnet-258113/devnet-flask-example:1.1 

  deployment.extensions/devnet-deploy image updated
```

These steps are all that you must do to update the application running in Pods, the K8s engine will take care of the rest. Behind the scenes K8s is doing a rolling update. It deploys a new ReplicaSet controller with the new container image, as shown in the figure.

![](/images/devcor/devcor_8_5_12.png)

It slowly subtracts the replicas from the old ReplicaSet while adding the Pods with updated images on the new ReplicaSet. The result is that the old controller stays without any managed Pods, and the new ReplicaSet manages all of them, as seen in the next element.

```bash
  kubectl get rs 
  NAME                                     DESIRED   CURRENT   READY   AGE
  devnet-deploy-545d96b59f   3            3         3           49s
  devnet-deploy-7bcbd9bdfd   0             0         0           5h7m
```

The old ReplicaSet controller is now inactive, which is ok because you did not create it manually, it is a referenced property of the Deployment. You could also follow the rollout using the next command, which gives you more insight into the timeline of the performed steps.

```bash
  kubectl rollout status deployment devnet-deploy                                                             
  Waiting for deployment "devnet-deploy" rollout to finish: 1 out of 3 new replicas have been updated...
  Waiting for deployment "devnet-deploy" rollout to finish: 1 out of 3 new replicas have been updated...
  Waiting for deployment "devnet-deploy" rollout to finish: 1 out of 3 new replicas have been updated...
  Waiting for deployment "devnet-deploy" rollout to finish: 2 out of 3 new replicas have been updated...
  Waiting for deployment "devnet-deploy" rollout to finish: 2 out of 3 new replicas have been updated...
  Waiting for deployment "devnet-deploy" rollout to finish: 2 out of 3 new replicas have been updated...
  Waiting for deployment "devnet-deploy" rollout to finish: 2 out of 3 new replicas have been updated...
  Waiting for deployment "devnet-deploy" rollout to finish: 1 old replicas are pending termination...
  Waiting for deployment "devnet-deploy" rollout to finish: 1 old replicas are pending termination...
  Waiting for deployment "devnet-deploy" rollout to finish: 1 old replicas are pending termination...
  deployment "devnet-deploy" successfully rolled out
```

When using the Deployment controller, it is simple to roll back to the previous version. To execute the rollback changes, the command is below:

```bash
  kubectl rollout undo deployment devnet-deploy

  deployment.extensions/devnet-deploy rolled back
```
Controllers are basically objects that encapsulate other, simpler objects, such as Pods, to extend the basic functionalities with the ones that a controller offers.

### Exposing Services

Once you have Pods with your application containers running and K8s controllers managing their lifecycle, you will have to think about exposing the Pods to external services. Exposing them to external services allows them to accept connections from inside and outside of your cluster. You have already seen the **kubectl expose** command, which is just one of the ways to create a service for exposing a running application. So, what is a Service in Kubernetes terminology?

A Service is an abstraction that provides a consistent endpoint (address), used by the internal and external applications that need to access some application running in a Pod. When creating Pods on K8s, they do not automatically get an external resolvable name and IP address, you need to create a service on top of a Pod or any of the other controller types. Services are also useful because Pods are disposable. It is difficult to rely on an IP of a Pod if it changes every time it is re-created. A Service can be an abstraction on top of Pods, which creates a single IP address that does not change when the Pods behind it move around the cluster.

The different types of services that exist on K8s:

* ClusterIP
* NodePort
* LoadBalancer
* ExternalName

The default ClusterIP type of service is only available inside the cluster. It is used on a single K8s cluster for communication from one set of pods to another set of pods. It creates an internally resolvable name and a single internal IP address that other Pods can use to reach a service on an application’s exposed port number.

NodePort can be used to get to the application service from outside of the cluster by using an IP address and a port of the Node where the application Pod is running. Anyone who can access the cluster Nodes can access the application that is exposed on the port defined by the NodePort services.

The LoadBalancer type of service is mostly used in the cloud. The goal of this service is to control an external load balancer device through the K8s command line. This service is only available if the infrastructure where K8s is deployed enables the use of a load balancer. This service creates NodePort and ClusterIP services in the background and instructs the load balancer to send requests to the specific NodePort. This service is useful when you want to provide external access to the application services running inside the cluster.

The ExternalName type of service is used less often. It is used for creating DNS names in the CoreDNS system so that the cluster can resolve the names that you do not control externally.

Observe the following YAML file for creating a new LoadBalancer service.

![](/images/devcor/devcor_8_5_13.png)

The service definition is using a selector to match the deployment object that this LoadBalancer service will expose. In this case, since you already created a Deployment that is named **devnet-deploy**, you can use an **app** selector with the name of that deployment. You create this service using the command **kubectl create**. After you create it, new load balancer will be provisioned on the K8s cluster provider and an external IP address should be dedicated to the deployed application. You will access applications running in Pods through a single IP address and the requests will be load balanced across the set of Pods (if replicaSet is used). You can examine the services running on your cluster using the command **kubectl get service**. Inspect the next output.

```bash
  kubectl get services
  NAME                     TYPE                 CLUSTER-IP   EXTERNAL-IP      PORT(S)            AGE
  devnet-lb-service   LoadBalancer   10.0.9.174      35.246.107.150    80:30095/TCP   2m3s
```

The field EXTERNAL-IP will store the public IP address that you can use to access the attached application deployment. It can take couple of minutes for provisioning a new load balancer on the infrastructure. Note that there is a CLUSTER-IP address available and a Node port (30095). The three services types, ClusterIP, NodePort, and LoadBalancer are additive. Additive means that each one creates the one before it to provide the connectivity to the running containers inside Pods.

Typically, you prepare Services for every Deployment on K8s, for both internal and external access to the Pods.

## 8.6 Integrating Applications into Existing CI/CD Environment

Deploy your containerized applications efficiently by using a CI/CD approach. Investing time into preparing pipelines for seamless automation of testing and deployment will save you time and headaches on the long run. The CI/CD methodology requires continuous building, testing, and deploying code changes at every small iteration. This process reduces the possibility of introducing bugs into the code when there are many developers working on the same project.

So far, you have seen the advantages of packaging your applications into containers. You have also seen how container orchestration platforms, such as Kubernetes (K8s), can easily manage clusters of machines for quick and easy deployment of those containerized applications. Docker, or containers in general, and Kubernetes are two very important building blocks in declarative, quick, and reliable application delivery. There is another part that makes the story complete. The source of most of the actions on these two platforms is the application code.

When you finish writing your application, how would you package it into Docker container, publish it on an image registry, and deploy it to the K8s cluster? Even though you learned the steps of this process using the CLI and the API of these tools, you can still be even more efficient. The first rule of the 12-factor app methodology suggests you manage your application code in a single codebase that a Version Control System (VCS) such as Git tracks. Considering this rule, you can make the code updates the trigger for automating the packaging, testing, and deployment of new changes.

The CI/CD methodology of software development is based on automating the execution of scripts to minimize the chance of introducing errors while developing applications. The methodology includes continuously building, testing, and deploying code changes at every small iteration. This method reduces the possibility of introducing a bug into production and prevents developing new code that is based on a faulty codebase. Observe the figure showing CI/CD strategies.

![](/images/devcor/devcor_8_6_1.png)

Continuous integration and continuous delivery are two practices that are not always used at the same time. Continuous integration is a practice of continuous building and testing of your application’s source code, using an automated process. It ensures that the introduced changes passed all tests, guidelines, and code compliance standards.

Continuous delivery is an extension of continuous integration and its goal is to release new changes to your customers quickly and in a sustainable way. Once the code passed all the tests, the workflow continues to the next step, which is deployment of the application. With continuous delivery, the deployments are triggered manually and strategically. Organizations usually establish release days, so a couple of times per week (or some other interval) a human needs to push the latest code to the production environment.

You can go one step further to the continuous deployment approach. With this practice, every change that is pushed to a repository, then built and tested successfully, is automatically released to the final environment. This process happens without any human intervention. There are no release dates anymore; developers can see the results of their work in production in couple of minutes.

A whole new set of tools and best practices have emerged because organizations are starting to migrate to container-based developments and microservice architecture. Strategies such as red/black and canary deployments are developed to optimize and safeguard the process of delivering a service to the end customer. The infrastructure is getting immutable with applying container technologies on top of clusters of machines. Lightweight approach of containerization technologies enables CI/CD workflows to really take off.

### Continuous Delivery

Following best practices, you should set up a continuous integration pipeline for your project to build and test your code. Once you have a working pipeline with tests passing, you can extend it to perform continuous delivery. Typically, the pipeline comes to the Staging stage. The idea of this stage is to deploy your tested application on a server that is available to a set of human workforces that inspects if the new deployment works. Remember, continuous delivery defines that the final deployment to the production environment is done periodically and manually as seen in the next figure.

Before your application can be deployed, it must be packaged. This packaging can be done as part of the Staging stage or in an entirely separate stage. If your application uses containers to run tests, then you have already packaged your code into container images and should be able to reuse them. This scenario emphasizes one of the benefits of containerization: you build an image once, verify it by running automated and any manual tests, and then use it for deployment.

![](/images/devcor/devcor_8_6_2.png)

If the testers and reviewers are happy with what they see, they approve the push to production. The person responsible for the pipeline then presses the deploy button and the stage stepping continues. If you are ready to implement the continuous deployment strategy, then this step becomes automatic. It is still recommended that the pipeline includes a staging-like deployment, but now the reviews will be automatic. Tools such as dynamic application security testing (DAST) come into the play, scanning for different vulnerabilities of your application and, if this step goes through, the pipeline continues without any human intervention.

How the application gets deployed on a server depends on the infrastructure and how you manage your servers. A primitive way would be copying binaries to the remote server using a tool like scp, then ssh into the server inside a job. Next, run the necessary sequence of commands that start the application. Hopefully, you do not have to do that. There are many automation tools that can declaratively set up your environment. Tools like Ansible and Terraform can help you achieve the same in a more controlled way. Perhaps you have a Kubernetes cluster running that you want to use for containerized application deployments. In this case, you need to prepare the Deployment controllers and Service objects and pushed them in a job to the K8s cluster. Some CI/CD systems are already so mature that they integrate with K8s natively. So, you do not even have to provide any Deployment or Service objects, everything will be automated for you.

So, when the deploy happens, either in manual or automatic (delivery or deployment) fashion, it goes to the next stage, which will perform the final deployment of an application.

![](/images/devcor/devcor_8_6_3.png)

As one of the final steps of the pipeline, the push to the production environment is made. As mentioned before, if using a Kubernetes cluster, you can push it directly to the cluster by specifying deployment templates and applying them on the cluster control plane node. From then on, your application is serving requests and waiting for another iteration of the pipeline process.

Sometimes the push to production is not the final stage in the pipeline. Some organizations perform further testing, e.g. browser performance testing once the application is deployed. Cleaning up the resources after the main goal is achieved is also a viable option for a stage in a pipeline. A cleanup stage would, for example, remove the staging environment, which is not used after the application gets deployed.

### Integrating Application with GitLab CI/CD

GitLab is a Git-based repository hosting service that is available in self-hosted or cloud variants. Besides hosting your source code, it enables many other features. For you, the most important ones are continuous integration service and native integration with Kubernetes.

The steps needed to have a working CI can be summed up to:

* Add .gitlab-ci.yml to the root directory of your repository
* Configure a Runner

![](/images/devcor/devcor_8_6_4.png)

The .gitlab-ci.yml file is where you configure what CI pipeline works with your application project. The file itself is version controlled together with your application codebase. It must reside in the root of your codebase repository. The file is written in YAML language and inside it you define stages, jobs, commands that jobs perform, restrictions on stages, etc. On any push to your repository, GitLab will look for the .gitlab-ci.yml file and start jobs on Runners according to the contents of the file for that commit.

In GitLab CI, Runners run the code that is defined in .gitlab-ci.yml. They are isolated (virtual) machines that receive jobs through the API of GitLab CI. A Runner can be specific to a certain project or serve any project in GitLab CI. Ideally, the GitLab Runner should not be installed on the same machine as GitLab.

Observe an example of a GitLab CI pipeline file.

![](/images/devcor/devcor_8_6_5.png)

The **image** keyword at the beginning of the CI file (1) is the name of the Docker image that is used for to performing the CI tasks. By default, GitLab CI gets its images from Docker Hub. The **services** keyword (2) defines another Docker image that is run during your job and is linked to the Docker image that the image keyword defines. This allows you to access the service image during build time. In the example, a docker-in-docker image is used, so each job will be isolated in a container. There can be multiple service images and they can run any application. A common use case is to run a database container so that you do not have to install it every time a project is built. Next, under variables you can specify custom environment variables (3).

The **before_script** (4) is used to define a command that should be run before each job, including deploy jobs, but after the restoration of any artifacts. There is also option to define an **after_script**. In the example, a test command **docker info** is used. Since you can inspect the output of each script and job, you can see if Docker is working correctly on your runner. You can also log in to your container registry of choice. The CI syntax in GitLab can make use of predefined variables that are autopopulated based on the context or are populated manually on your GitLab profile. In this case, they are used for login credentials and later for image tagging and pushing.

The only stage that is called **build-test** is used to build an image and run the container by overriding the default command with the path to the test files.

This gitlab-ci.yml uses a single stage in a pipeline to build an image and run the container with the tests inside. This pipeline is very short, but it is useful when you want to start as simple as possible and do not require any special features except running the tests. You can find a reference documentation for the gitlab-ci.yml on the GitLab's documentation pages (https://docs.gitlab.com/ee/ci/yaml/).

When your YAML gitlab-ci file is ready and waiting in your application codebase, GitLab will automatically start pipelines for you. By default, any commit to CI enable repository triggers the pipeline creation. A compact view of a more complex pipeline in GitLab is shown in the following figure.

![](/images/devcor/devcor_8_6_6.png)

After the pipeline finishes, either successfully or not, a report is prepared. In this example, all stages went through, so the pipeline is marked as passed. You can observe the commit that triggered the pipeline creation and the stages that passed. You can also observe the run time of the entire pipeline and if there are any artifacts. Examples of artifacts are built images or any other file or report, which are one click away. If you open this pipeline, you get a more detailed view of the pipeline history.

![](/images/devcor/devcor_8_6_7.png)

These are the stages of the previously shown pipeline. The first, **Build** stage, prepares the Docker image (1) and uploads it to the GitLab registry. If you click a specific job, you get a real-time output of logs. If something goes wrong in a specific job, you can start searching here. Once the image was built, it was passed to the **Test** stage (2). This one has multiple jobs that run simultaneously on GitLab runner. They test a variety of things, from code quality to static application security testing (SAST) and custom unit tests. The special testing tools that are needed for a job are started in a container. Next, the **Review** stage is used for setting up Dynamic Application Security Testing environment (3). This practice can be compared to the classic staging deployment practice, but in this case, testing and various checks are done entirely in an automated fashion. The **Review** stage sets up Pods on Kubernetes cluster with the DAST tools that are then used in the next stage (4) to perform DAST vulnerabilities inspection and generate a report (4).

If no vulnerabilities were found, the pipeline moves into the **Production** stage, which deploys the application to the Kubernetes cluster and creates an artifact with the URL for application access (5). This pipeline clearly follows the deployment pattern since it all happened without any human intervention. After the application is deployed to production, the last, **Cleanup** stage is performed, which stops and removes the Pods that were created for DAST inspection (6).

Implementation details of the **Production** stage will obviously depend on your environment. If your organization is using Kubernetes, deployment may be simple. Deployment could be a couple of **kubectl** calls to create a Kubernetes deployment or updating an existing one with the new container image.

![](/images/devcor/devcor_8_6_8.png)

If creating a new deployment, you can store the URL as a pipeline artifact for easy access, as depicted in the figure.

This last step closes the loop of a fully automated CI/CD cycle. In the organizations that strictly follow the DevOps practices, this kind of automatic deployment happens regularly during the week. Developers can see their work without knowing all the details of what had to happen to publish the changes. These practices enable developer teams to shift their focus into improving quality and features of their code instead of dealing with the low-level operations tasks.

## 8.8 Hosting Applications on Network Devices

Configuration, operation, and workflows of networking devices can all be automated with applications hosting on said devices. With application hosting on the Cisco Catalyst 9000 Series, the cloud is extended to the edge of the network, thus, enabling data processing closer to the source in an allocated form. The Cisco Catalyst 9000 Series supports native and third-party off-the-shelf applications that are built with the set of Linux programming tools. Custom applications can also be run, cross-compiled with the software development kit (SDK) that the Cisco Catalyst 9000 provides.

Application hosting is provided in two ways:

* Native (Cisco)
* Container (Docker, KVM)

Network administrators do not need to concentrate on the command-line interface anymore if configuration and management applications manage network devices. Examples of applications are Puppet and Chef. Since the application gives the abstraction layer, the network administrators can concentrate on the higher-level tasks like design, while the application itself does its job.

Applications that are hosted on network-edge devices can function as:

* Automation tools
* Configuration tools
* Management monitoring tools
* Combining existing tools

Cisco Catalyst 9000 Series is built on the x86 architecture and are all running on the open Cisco IOS XE operating system with Linux underneath and native Docker. The application hosting environment has its own resources (that is, CPU) and is completely isolated from the devices operating system. It has no access to the devices kernel so that any malware that the app could bring along will never affect the device itself. The application hosting environment can expand to free resources, but the switch operating system has the priority on the resources that the device needs. The device is always able to run its critical tasks.

![](/images/devcor/devcor_8_8_1.png)

Cisco IOx is an end-to-end application framework (environment) that combines Cisco IOS and Linux (open-source tools). It gives application hosting capacity for many application types on Cisco network platforms. One of those applications is the Cisco Guest Shell, a special container that is effective in system use or deployment.

Cisco Guest Shell is a virtualized Linux-based environment, intended to operate custom Linux applications and Python for automated control and management. Using Cisco Guest Shell, network engineers can also update, operate, and install third-party Linux applications. It is combined with the system and installed with the **guestshell enable** IOS command.

Before the applications are installed on the switch, they can be built and tested in an Application Hosting Sandbox. The Application Hosting Sandbox is a development lab that is intended for code integration, testing and discovering new application hosting features.

The applications can be orchestrated with Cisco DNA Data Center. Cisco DNA Center uses REST APIs and provides a simple provisioning with a couple of clicks so that you do not need to use the device's CLI for installation of multiple applications.

External storage options for containers, third-party applications, and local logging on the Cisco Catalyst 9000 Series include:

* USB 3.0—up to 120 GB
* SATA SSD—up to 1 TB

The storage is encrypted with Advanced Encryption Standard (AES) 256. The USB is password-protected, which means that you cannot take the USB and install it on another switch.

### Application Hosting Framework

An application existing on a Cisco Catalyst 9000 can be in any of the following states:

* **DEPLOYED:** Application is installed on the device. Resources that the application needs are not yet committed to the application.

* **ACTIVATED:** The resources that the application requires are now committed to the application. The associated container artifacts are generated.

* **RUNNING:** The application is now running.

* **STOPPED:** The application is stopped.

### Cisco Catalyst 9000 Containers Networking

All the possible network modes on the Cisco Catalyst 9000 Series support for application hosting include:

* Dedicated, internal AppGigabitEthernet interface
* Management Interface

![](/images/devcor/devcor_8_8_2.png)

The AppGigabitEthernet interface is an internal hardware data port. The interface connects the applications to the HW switch fabric connected to the front panel data ports. The Container is Layer 2 bridged to Front Data ports through the dedicated, internal AppGigabitEthernet interface that is configured on Cisco IOS Software. Containers also have Layer 3 routed access to an IOS Switch Virtual Interface (SVI) with app-vnic "trunk" mode. The AppGigabitEthernet interface also provides the same features as the Cisco Catalyst 9000 Series front-panel GigabitEthernet ports. For example, **switchport mode trunk** and **switchport trunk allowed vlan**. Note that **switchport mode access** is not intended for use because it limits the containers access to only one VLAN.

The Management Interface has the following characteristics:

* Container is Layer 2 bridged to Management I/F.
* Needs to be on the same subnet as Management I/F. For example, the subnet 172.20.0.0/24 (IOSd mgmt-if 172.20.0.1; Container 172.20.0.10)
* "Ge0/0" is the physical Management I/F port.
* All Cisco Catalyst 9000 Series platforms support Management Interface connectivity.

For a single app, only the vlan-access or trunk mode can be configured. Cisco IOS XE Release 16 forbids the mixing of the two modes for a single application. This restriction will be removed in the upcoming Cisco IOS XE Release 17.

Yet, for multiple applications, both trunk and vlan-access mode configs can exist side by side on the AppGigabitEthernet.

### Enabling Application Hosting

Before you can start enabling application hosting on the Cisco Catalyst 9000 Series, there are a couple of perquisites:

* The device must be running release version 16.12
* A Cisco certified USB3.0 Flash Drive must be installed in the devices back-panel USB3.0 port.

Smart Licensing is also required for the Cisco Catalyst 9000 series platforms. DNA-Advantage licensing is required to enable Application Hosting on the network device.

```bash
  CAT9K# conf t
  CAT9K(config)# license boot level network-advantage addon dna-advantage
  % Reboot the switch to invoke the highest activated License level 
  ...
  CAT9K# write memory 
  Building configuration.
  [OK]                     
  #reload
  ...
  CAT9Kh# show version     
  ...
  Technology Package License Information:
  -----------------------------------------------------------------                                                                                        
  Technology-package                   Technology-package   
  Current             Type             Next reboot                      
  ------------------------------------------------------------------                       
  network-advantage   Smart License        network-advantage 
  dna-advantage       Subscription Smart License     dna-advantage
```

The Application Hosting framework is not enabled by default.

Following is an example of the Cisco IOS XE CLI configuration to set up Application Hosting infrastructure:

```bash
  cat9000# conf t
  cat9000(config)# iox
  cat9000(config)# exit
  cat9000# show iox-service
  ...
  IOx Infrastructure Summary:
  ---------------------------
  IOx service (CAF)    : Running
  IOx service (HA)     : Running
  IOx service (IOxman) : Running
  Libvirtd             : Running
  Dockerd              : Running
```

### Deploying Application

To deploy an application on the networking device, you need to first download the application on the USB, configure the interface, and then install the application on the networking device.

In the following example, the application to be used is called Perf3. Perf3 (iPerf v3) is an application that runs inside a Docker container and is used to actively measure maximum achievable bandwidth on IP networks. It supports tuning of various parameters that are related to timing, buffers, and protocols (TCP, UDP, with IPv4 and IPv6). For each test, it reports the bandwidth, loss, and other parameters.

Observe how to generate a Docker tarball file in the following example:

```bash
  #docker pull mlabbe/iperf3
  #docker save mlabbe/iperf3:latest -o iperf3.tar
```

The proper interface needs to be configured for the application to work.

The IP address for the IPerf3 application container is set as a static IP address and a default gateway is configured to route the traffic from the example application. The run-time parameter that is required for the Docker container is configured in the **app-resource** in **run-opts**.

The Docker tar needs to be copied to the networking device (usbflash1).

Copying and installation of the app:

```bash
  cat9k# copy scp: usbflash1: vrf Mgmt-vrf
  cat9k# app-hosting install appid iperf package usbflash1:<path to tar file
  cat9k# app-hosting activate appid iperf
  cat9k# app-hosting start appid iperf
  cat9k# show app-hosting list
    App id                      State
  --------------------------------------------------
    iperf                      RUNNING
```

The application can also be stopped and installed simply with the CLI.

Stopping and uninstallation:

```bash
  cat9k# app-hosting stop appid iperf
  cat9k# app-hosting deactivate appid iperf
  cat9k# app-hosting uninstall appid iperf
```

# 9 Understanding Distributed Systems

## 9.1 Introduction

Distributed systems allow you to scale applications and provide redundancy and resiliency if they are properly designed. However, they also present additional constraints, some of which you cannot avoid and require you to make trade-offs. In other words, there is no known design that would solve all challenges of using distributed systems. So, let's look at different architectures, their strengths, and shortcomings. This evaluation allows you to select the best solution to fit your application's needs.

## 9.2 Distributed Application Concepts

Distributing an application across multiple compute nodes is necessary when you expect a higher number of requests. It is a preferred way to approach designing such an application because they can scale better and are more reliable and efficient. How much you want to distribute your application depends on the type and amount of traffic you expect. When dealing with streams of data, a streaming architecture can be used with components such as Apache Kafka to manage such workloads. For a simple client/server use case, a basic two-tier architecture might be enough. Many distributed systems have a component of load balancing in common. Load balancing enables you to split traffic across multiple nodes that perform the same computation. You will learn the concepts of such systems and what kind of load balancers you can use when approaching a distributed application architecture.

When cloud computing offerings were less common, and cat videos were not streamed by millions of people every day, the application designs and the overall architectures of the systems were different. Serious applications were built mainly for enterprises and were not available to the general public over the internet. This availability meant that the demands for application services were stable and relatively small, depending on the size of the enterprise. In terms of software design, big monolithic applications ruled the software world. Monolithic designs enabled organizations to deploy the application on a single, or a few, reliable, and expensive machines. The main goal of the operations team was to keep machines up and running at any cost. Failure was possible but unlikely. However, if a failure happened and the machines were lost, the consequences could have been catastrophic.

An example of a very centralized application would mean that one machine delivers the entire functionality of that application, including the access to it. This kind of application is meant for single user access. Most modern applications require multiuser access to the application service. When multiple connections happen to a single source of service, it is a sign that the system uses a concept of a client/server software architecture.

![](/images/devcor/devcor_9_2_1.png)

The simplest shape of a client/server architecture is the two-tier architecture as shown on the previous figure. This architecture describes how application access and processing is divided into two parts. As shown in the figure, the two parts are client applications used for accessing a service and an application server that offers that service. The client side is also sometimes referred to as the presentation layer. This layer can vary from browser clients to mobile native applications or CLI clients. The simplest example of a two-tier client/server application is an application such as PuTTY, which facilitates Secure Shell (SSH) protocol to access a server. Also, a web application for displaying simple static pages does not require more than what two-tier architecture offers, since displaying static HTML pages requires little data manipulation. You can simply use a web browser, which acts as a client, that can access a web server with static pages; no more complexity is required in this case. Even if you need a simple database for storage, it might still be enough to have a two-tier setup.

The two-tier architecture handles the entire logic and the storage capabilities at a single node. When you need more data processing capabilities, then a two-tier architecture might not be enough for the use cases you have prepared. Adding additional tiers sets the architecture for a more flexible approach to distributing your application.

![](/images/devcor/devcor_9_2_2.png)

The previous figure shows the three-tier architecture. This architecture isolates the data processing part of the application and sets the data storage as a separate component of the application. The middle layer, also known as a logic tier or the business tier, does all the processing of commands, makes logical decisions, and performs necessary calculations. It is used to call the data tier layer where the database is present to persist the state of the application.

The two architectures described so far belong to the family of N-Tier architectures, where N is the number of layers of making up an application architecture. This architecture family is also called layered architecture. Layered architecture is probably the most widely used type of architecture today. The separation of concerns among the different tiers sets a playground that is more flexible and convenient for development and scaling. To truly take the advantage of the concepts of distributed application architecture, you must understand the use cases and the consequences that those use cases bring with them.

Today, there are not only enterprise applications being built, but also web and mobile applications available on the internet, accessed by thousands and millions of users daily. The request count for a single service and the data that is being transferred can be enormous. The demands are also dynamic. On some days you may experience peaks in user requests, and on other days there could be no traffic at all. Utilization of machines running your applications becomes more important. You start to learn that just pushing the entire application processing logic on a single machine does not cope with the demands of the modern-day use cases.

If an application’s business tier is deployed on a single machine, your application can become unavailable at any time if that machine stops responding. Imagine having a three-tier web application. The users access the application through a web browser and a domain URL which is tied to the public server hosting your applications business logic. All those requests are processed by that single server; if the machine stops working, the requests will not be processed anymore and the service becomes unavailable. The presentation layer also cannot access the data on the data tier because the middle layer is not available to perform the necessary logic queries. The distributed application in this case needs a little bit of work on the operational side of things so that it becomes more fault tolerant. A high-availability approach can be taken in this case. Duplicating the layer that is most important for the service can solve the problem of a faulty server. In this example, having multiple business tiers deployed on different machines will do the trick if you properly set up the high-availability cluster and all the operational tasks that come with it.

Another example of a problem that can happen is your application starts receiving more requests than a single machine can handle. In this case, you will need to scale your application. You can take two approaches—vertical scaling or horizontal scaling. Vertical scaling means adding more resources to a single machine that holds the core of your application. Adding more RAM or CPU can only go so far before you hit the limits of the machine. Horizontal scaling is favorable in this case. With horizontal scaling, you increase the number of nodes that serve the same core application component and you split the workload between the replicated component using a load balancer.

A modern application architecture is influenced by several principles that guide the development of larger scale distributed applications.

These principles are as follows:

* Availability
* Performance
* Transparency
* Scalability

Uptime is important for many types of applications. Minutes or even hours of downtime can result in revenue loss or similar unwanted scenarios. Modern applications are designed to be constantly available and resilient to failures. Critical components of applications can be added to the high availability cluster to make sure that users can always access the service, even when one of the machines becomes unavailable. Performance is also an important factor to consider when building distributed applications. How your application is divided and distributed among different nodes affects the overall performance of the accessed service and defines how easy it is to manage your distributed application. You can develop a system with better separation of concerns and therefore easier management of the tiers by the teams developing the product. For example, the layers in a layered architecture can all be developed by separate teams. Front end, or the presentation tier, is usually developed by a separate team that has expertise in delivering the best user experience. The business tier, also called the back end, can be developed by a separate team that uses different technologies to provide processing and data management of the application.

You could examine all layers in a typical N-tier architecture and see that each tier could be separated by the functionalities it provides and the technologies that enable that functionality. The interfaces between the different components should be well-defined so that different teams can integrate their work together into a cohesive system providing service to its customers. Even though the components are distributed across multiple machines, the complexity of the system is hidden and shows itself as a single application.

The layered architecture is only one of architectures that separates the components in such fashion. There are others that do the same in slightly different way, such as microservices or streaming architecture. What all architectures that enable such separation have in common is that you can manage different components of the application separately. This feature especially shines when you want to efficiently scale your system horizontally.

### Load Balancing in a Distributed Application

The next figure shows a more modern three-tier architecture, with the possibility to scale the most critical components of the system.

![](/images/devcor/devcor_9_2_3.png)

In the example in the figure, the different layers of the application can be scaled separately for the best utilization and user experience. The front end of the application can be, in the case of a web application, based on the HTML pages and other front-end languages or a mobile native presentation layer can be developed. The front end can scale almost automatically, so to speak. When using a JavaScript library such as react.js, you can move some of the workload from the back-end side to the front end. This means that not all calculations need to be performed on the back-end server application, but can be performed on the client devices such as laptops or mobile phones. As the system is distributed across different nodes, you can make the most out of each layer that exists.

In the previous example, the clients in the presentation tier communicate with the business logic over a network. The business tier is replicated to enable balancing of the workload across multiple machines. The client applications do not communicate directly with each of the replicated machines that serve the business logic, but they are relayed via load balancer devices. In this example of a layered architecture, the load balancer balances the traffic across the nodes that serve the business logic. In other architectures, such as microservices, a load balancer can be used for any single service that you want to scale for better availability and performance. There can be many load balancers in a distributed application, which handle the access and workload balancing and are one of the primary components that enable scaling of a distributed application component.

Elastic scaling of the distributed components is also made possible by the load balancer, because the addresses of the individual nodes that were part of scaling are not important anymore as they are not accessed directly. The load balancer exposes a single address that stays the same no matter the scaling action that is performed behind the scenes. This address is used by the clients, and load balancer redirects the received requests according to the configured rules.

In a distributed application, the nodes are not treated as special anymore. They become disposable. Losing a compute node does not affect access for the users. If the system is set for scaling and the load balancers are in place, a user should not even notice that the compute nodes are being replaced. When using orchestrators such as Kubernetes, you can make sure that the desired set of nodes is always up. The virtualization technology and containers enable you to do this operational task as quickly and automated as possible.

A modern application also needs to scale the data tier. This tier may be the trickiest part of a distributed system. When distributing data across multiple machines that act as datastores, you need to ensure the reliability of data. When users write and read data, they should always get the data they expect. Databases such as MongoDB and Cassandra are gaining popularity because of their scalability and performance. Other types of architectures, such as microservices or streaming architecture, will also implement load-balancing techniques. You can see the differences in the architecture types by looking into where and how they incorporate the workload balancing. In a streaming architecture, you will also find tools like Apache Kafka, which is used for reading and writing an enormous number of data streams in a distributed system.

A popular open source solution for load-balancing the traffic is High Availability Proxy (HAProxy). Its most common use is to improve the performance and reliability of a server environment by distributing the workload across multiple servers such as web or application servers and databases. It is used in many high-profile environments, such as GitHub, Instagram, and Twitter.

In relation to load balancing, access control lists (ACLs) are used to test some condition and perform an action based on the result of that condition. For example, blocking a request from a selected network or selecting a specific server for request handling is a typical use for an ACL. ACLs allow flexible network traffic forwarding based on various parameters like pattern-matching and deciding to which server to send the request based on the number of connections on a back end.

In HAProxy terminology, the back end is a set of servers that receive forwarded requests from the load balancer. To describe a back end, you specify the load-balancing algorithm and a list of servers together with their ports. A back end can contain one or many servers in it. Adding more servers to your back end increases the potential load capacity because the load can spread over more servers. Observe the following example of a simple back-end configuration in HAProxy.

```bash
  backend web-backend
    balance roundrobin
    mode http
    server web1 web1.example.com:80 check
    server web2 web2.example.com:80 check
```

The first line with the keyword ***balance*** specifies the load-balancing algorithm. A round robin algorithm is the default and the simplest one. With the round robin algorithm, the requests are allocated to a single server from a configured back end. The servers are allocated in a random or rotating sequential manner. For example, the first request gets the IP address of server 1, the second request gets the IP address of server 2, and so on, with requests starting again at server 1 when all servers have been assigned an access request during a cycle. There is also the ***leastconn*** option that selects the server with the least number of connections to serve the request, and the ***source*** option for a balancing type, which selects the server based on a hash of the source IP. The ***source*** method ensures that a user always connects to the same server from a set of servers.

The **mode** ***http*** specifies that Layer 7 proxying will be used. You will learn about application layer load-balancing later. After the mode setting, the list of servers and their domain reachable URLs are specified. The check keyword at the end of the server directives specifies that health checks should be performed on those back-end servers. By using health checks, you avoid having to manually remove a server from the back end if it becomes unavailable. The default health check is to try to establish a TCP connection to the server.

In contrast to a back end, a front end can also be configured. A front end defines how requests should be forwarded to back ends. Front ends are defined in the ***frontend*** section of the HAProxy configuration. Their definitions are composed of the following components:

* A set of IP addresses and a port (for example, 192.168.1.5:80, *:443)

* Access Lists

* ***use_backend*** rules, which define which back ends to use depending on which ACL conditions are matched

You will see later what a front-end configuration looks like.

When talking about load balancing in Open Systems Interconnection (OSI) layer terminology, there are two types of load balancers. Layer 4 (Transport layer) and Layer 7 (application layer) load balancers. The simplest way to load balance network traffic to multiple servers is to use Layer 4 load balancing. Observe the figure showcasing the Layer 4 load balancing.

![](/images/devcor/devcor_9_2_4.png)

Load balancing this way will forward user traffic based on IP range and port. If a request comes in for http://example.com/users, the traffic will be forwarded to the back end that handles all the requests for example.com on port 80. The user accesses the load balancer, which forwards the user's request to the ***web-backend*** group of back-end servers. Whichever back-end server is selected will respond directly to the user's request. All servers in the ***web-backend*** should be serving identical content, if not, users might receive inconsistent content between requests. In this example, both web servers connect to the same database server for simplicity.

A more flexible way to load balance the requests to your application is to use Layer 7 (application layer) load balancing. This behavior is enabled in HAProxy using the **mode** ***http***. Observe the next figure that shows the process of Layer 7 balancing.

![](/images/devcor/devcor_9_2_5.png)

Using Layer 7 allows the load balancer to forward requests to different back-end servers based on the content of the request. This mode of load balancing allows you to run multiple web application servers under the same domain and port. For example, when users enter the home page, the load balancer will direct the request to a server in a set of servers that handle the home page. If a user wants to check, for example, images on a website, and they use the ***/images*** slug, the load balancer redirects the request to a different set of servers.

**Note:** A slug is the part of a URL which identifies a page on a website in an easy to read form. For example, in http://example.com/images, the "images" is the slug.

As discussed previously, a front-end configuration needs to be provided to perform handling of the traffic toward the back ends. A sample of front-end configuration could look like this:

```bash
  frontend http
    bind *:80
    mode http

    acl url_img path_beg /images
    use_backend img-backend if url_img

    default_backend web-backend
```
This configures a front end named **http**, which becomes responsible for handling all incoming traffic on port 80. An access list is used to match user requests and redirect them to the desired back end. In this example, if a user's request URL begins with the ***/images*** string, it is redirected to the servers handled by ***img-backend***.

Other solutions like HAProxy are Linux Virtual Servers (LVS), which can act as a simple Layer 4 load balancer and is included in many Linux distributions, and nginx, which can be used as a web server and for proxy and load-balancing purposes.

## 9.3 Custom Dashboard Example

Custom dashboards can be developed to customize the way you interact with the existing systems. When the applications are distributed, the front-end and back-end components interact with each other, typically by using a protocol such as Representational State Transfer (REST). Your custom dashboard can integrate with any system that provides a REST interface on the back end. One such system is Cisco DNA Center, which you can integrate to for querying and presenting data on the presentation view of your custom dashboard. The application programming interface (API) also supports performing any actions that can be taken on the native user interface. A custom dashboard can integrate with multiple services and become a single pane of glass for the functionalities these services provide.

The distributed applications divide the presentation and logic into several components, sparse across the network. The components of an application are divided based on the architectural design you choose to use. From a user's standpoint, the architecture does not define what the user is seeing when accessing the application. The user interacts with the front-end application, which is in a distributed system separated from the back-end logic. If you forget about the architecture details, you may simply call this approach a client-server application. Observe the following figure showcasing a client-server communication.

![](/images/devcor/devcor_9_3_1.png)

In a distributed application, the front end is an application which can be separately developed and deployed. In the front-end application, you will find technologies and languages such as HTML, Cascading Style Sheets (CSS), JavaScript, .NET, react.js, Angular, and similar frameworks whose goal is to present the data to the user in a structured, understandable way, aiming for the best user experience. The front-end components are accessed by users to lookup data or modify a state of the application using, typically, a graphical interface. The presentation interface can be written for a web application, mobile, or desktop application. If an application serves only static data which does not change, there is no need for any back-end system and a datastore, but most applications need to maintain a state, and the data should be dynamically managed and presented to the user.

The data on which the front end operates comes from an application’s back-end component. The requests that the back-end components receive can be queries for data, data payloads that need to be processed and returned to the call, or data which is meant to finish in a datastore. The typical technologies and languages that operate on the back-end side are PHP, Python, Ruby, Java, .NET, and so on. The back-end sets the default behavior of the application state. It defines rules what can be done on the state and how the front end can interact with the core of the application.

The distributed components in an application allow you to change the front-end and back-end components at will. You may have multiple front-end presentation components, for example, a web application, mobile application, and desktop application, which all operate on the same data set coming from the same back-end system. A single front-end component could also access data from multiple back-end systems when different data needs to be gathered on a single presentation component. Look at the next figure, showcasing the flexibility of the distributed front-end and back-end components.

![](/images/devcor/devcor_9_3_2.png)

To successfully communicate between the front-end and back-end components, you need to set up a standard way of communication with clearly set message formats and options. The messaging standard is set on the back-end components which handle the state of the application. For integrating a front end to a back-end system, the latter defines an API, which describes the ways of querying the data and manipulating with the state of the application.

The possibilities of integrating components in such fashion, open the door for developers to create new ways of presenting and manipulating data for the users accessing the application on the front end. For example, an application for storing documents has a front end with generic HTML forms for uploading the document and adding some basic information to it. This front end communicates with the back-end component over a defined API to fulfill the user's requests for storing documents. You could develop another front-end component, which has a more modern presentation, with a drag and drop function for the documents, a progress bar and a history view. You are creating a better experience for the user accessing the presentation layer, but in the back end, the same API calls are made as with the previous front end.

### Integrating Custom Dashboard with Application API

As discussed so far, the front-end components can access the back end using the API calls. There are a couple of technologies that you can use for exposing the applications back end using an API. The most popular technology for the API, at least for web applications, is REST. REST is a programmatic interface that can be used on web services for providing interoperability between applications components. API requests may be read-only queries with no side-effects or produce modifications of the resources.

REST APIs have become a standard for interacting with web services because of the following features:

* Lightweight: You don't need to install special libraries, compile code, or have expert skills in any programming language to use a REST-enabled web service.

* Flexible: You can use REST for simple requests or for complex operations.

* Scalable: REST requests run on the web server, so their ability to scale is entirely a function of the server's abilities.

* Platform-agnostic: Virtually any HTTP-enabled host can send REST requests and receive REST responses, whether on Windows, macOS, Linux or on a mobile device, such as a smart phone or a tablet.

REST is based on the HTTP request-response model. This model uses Hypertext Transfer Protocol (HTTP or HTTPS) to send a request to a web service, which returns a response.

The requests can be made with the following REST resource methods:

* GET
* PUT
* PATCH
* POST
* DELETE

The request is made by specifying the resource method and the URL of the resource you want to access. For example, a simple query to an application could be specified like in the next figure.

![](/images/devcor/devcor_9_3_3.png)

A simple GET request is made for a user with an ID of 32. This request happened when a user was chosen from a list of users on the front-end presentation. The return message from the back-end server is a JSON object, containing the user information that the query was requesting. The communication goes in the other direction as well. The front end can collect data that a user entered on the front end, serialize it in a JSON or XML object, and send it over by using a POST, PUT, or PATCH method. When a back end receives the payload, it examines its content, typically performs a validation on the data and stores the new state of the application. This are the basics of communication between application components using a protocol such as REST. The other protocols alternative to REST are gRPC, SOAP, CORBA, or any other language-specific remote procedure calls (RPC) supported on the back end.

Sometimes you need to create an application that integrates with existing back-end systems that already implement some of the features you need. Such systems normally have their own front-end dashboards for interacting with the functionalities of the system. When you want to prepare a customized solution that uses the existing back end but presents and interacts with data in a different way, you would develop your own dashboard application, and integrate with the existing services over their API. The API of the system you want to integrate with is normally documented, so that you know how to construct the API calls.

For example, a customer wants to have a customized dashboard which presents the data they have on the Cisco Digital Network Architecture (DNA) Center platform. Cisco DNA Center allows you to manage the enterprise network over a native, centralized dashboard, and helps with rapid deployment of networks, using intuitive workflows. It provides a single pane of management to design, provision, enable policy, and assure network services. Examine the next figure showcasing the Cisco DNA dashboard which comes with the product.

![](/images/devcor/devcor_9_3_4.png)

The native dashboard also communicates with the back-end system’s API to present the data on the page and to issue other requests to the back-end components that know how to process and store the state. If you want to create a custom dashboard, you will most likely use the same API calls from your front-end application to interact with the product.

When talking about APIs, there are several directions where the API can be exposed. When you want to integrate your own custom application with an existing system, you are integrating with the northbound API of that system. The northbound API of Cisco DNA is called Intent API. The Intent exposes specific capabilities of the Cisco DNA Center platform. The Intent API provides policy-based abstraction of business intent, allowing focus on an outcome rather than struggling with individual mechanisms steps. The RESTful Cisco DNA Center Intent API uses HTTPS verbs (GET, POST, PUT, and DELETE) with JSON structures to discover and control the network.

Most of the time, when using remote API calls to a system, that back-end system will need authentication details to serve your requests. This is typically done by generating an access token which is used throughout a period of API calls until it expires. When a token expires, a new one must be generated. For Cisco DNA, the token can be generated using the following resource method and URL.

```bash
  POST <cluster-ip>/dna/system/api/v1/auth/token
```

Together with the resource URL, a basic authorization header with username and password must be provided. If a request was successful, meaning that the request was authorized with the correct credentials, a token is returned. The token is serialized in a JSON message, which can be easily interpreted by any programming language. The successful response is received with an HTTP response code 200, and a JSON message with a token similar like this:

```json
  { 
      "Token": "eyJ0eXA ... O5uEMR-tc“
  }
```

This token is used inside X-Auth-Token header with the next request you perform against the REST API of the Cisco DNA Center. For example, the next request returns the Cisco DNA Center Device Inventory, which is used for viewing information about the devices added to the product.

```bash
  GET <cluster-ip>/dna/intent/api/v1/network-device
```

The response from the previous GET request is a JSON object with a list of items. The items are device objects with all the parameters that the back-end system knows about the device. Examine the output of the previous query.

```json
  {
      "response": [
          {
              "family": "Routers",
              "type": "Cisco ASR 1001-X Router",
              "errorCode": null,
              "location": null,
              "macAddress": "00:c8:8b:80:bb:00",
              "hostname": "asr1001-x.abc.inc",
              "role": "BORDER ROUTER",
              "lastUpdateTime": 1576499161959,
              "serialNumber": "FXS1932Q1SE",
              "softwareVersion": "16.3.2",
              "locationName": null,
              "upTime": "38 days, 12:12:12.99",
              <... output omitted ...>
              "instanceUuid": "1cfd383a-7265-47fb-96b3-f069191a0ed5",
              "id": "1cfd383a-7265-47fb-96b3-f069191a0ed5"
          },
          {
              "family": "Switches and Hubs",
              "type": "Cisco Catalyst 9300 Switch",
              "errorCode": null,
              "location": null,
              "macAddress": "f8:7b:20:67:62:80",
              "hostname": "cat_9k_1.abc.inc",
              "role": "ACCESS",
              "lastUpdateTime": 1576499308577,
              "serialNumber": "FCW2136L0AK",
              <... output omitted ...>
          }
      ],
      "version": "1.0"
  }
```

Data in such format can be used in any application to read the values and present them in a customized way. Your custom dashboard back-end code could make such REST calls to get data in a raw JSON format, make any necessary transformations, and then use any kind of web design technique to present the data intuitively on the front-end customized web pages.

If you are using Python on the back end of your own custom application, you could use the requests module to interact with the REST API of Cisco DNA Center. For example, getting information about the health of wireless devices in Python would require the following two REST calls:

![](/images/devcor/devcor_9_3_5.png)

The corresponding Python integration code can be summed up as follows:

```python
  import requests

  base_url = 'https://<IP or hostname>'
  r = requests.post(f'{base_url}/dna/system/api/v1/auth/token',
                          auth=('<USERNAME>', '<PASSWORD>'))
  r.raise_for_status()
  token = r.json()['Token']

  headers = {'X-Auth-Token': token}
  r = requests.get(f'{base_url}/dna/intent/api/v1/site-health', headers=headers)
  r.raise_for_status()
  sites = r.json()['response']
```

The above code is using the get method of the requests module to retrieve data about all the sites. The method expects the resource URL and takes the authorization token, which must be obtained beforehand by calling the ***post*** method. A typical approach would be to obtain the token once, store the token in memory, and use it throughout the calls. The information can be then used on the front end to display the information to the user.

This is just one of the examples for integrating with a remote Cisco DNA Center. There are many other REST resources on the Cisco DNA Center side that can be used for querying and manipulating with the state of the system. More information about the REST API can be found on https://developer.cisco.com/docs/dna-center/api/1-3-1-x/.

Integration is usually done in the back end, so it is easier to use software development kits (SDKs); Cisco Intersight API requires request signing, which many HTTP libraries do not yet support:

```python
  from intersight.intersight_api_client import IntersightApiClient
  import intersight.apis.network_element_summary_api as summary_api

  client = IntersightApiClient(
      host='https://intersight.com/api/v1',
      private_key='<KEYFILE>',
      api_key_id='<ID>'
  )
  api = summary_api.NetworkElementSummaryApi(client)
  summary = api.network_element_summaries_get()
```

Once you know what kind of data you want to present on the custom dashboard, you can use the documentation of the product you want to integrate with to get the details of how to construct the REST API calls and the format of the payloads you need to send to modify the state of the application. Your custom dashboard can integrate to multiple services at the same time. This way you can provide a single pane of glass to all services from the same dashboard to users, including only the functionalities that an organization needs.

## 9.4 Event-Driven Architecture Concepts

Software applications function primarily by changing state and reacting to those state changes, which means that most state changes inside a system are meaningful events for that application. Event-driven architecture aims to make sense of those state changes by creating a system where the flow of information is determined by the stream of events. Interest in such an approach is growing, since event-driven architecture fits very well with a distributed, cloud-based environment.

To understand event-driven architecture (EDA), you first must understand what an event is. Events are significant state changes inside a system. They occur inside a system and do not exist outside that system. Events do not travel outside the system, event notifications (or messages) do, however. All events are not the same, since they represent different occurrences.

They can be either:

* **Atomic:** Atomic (or simple event) events represent single, indisputable facts, that show that something has happened (for example, order created, user deleted). They describe what happened.

* **Related:** Related events (or event stream) are a sequence of simple events, that describe events in a certain domain (for example, an error happens every 24 hours, an item was added and removed from the shopping cart). These events describe when something happened.

* **Behavioral:** Behavioral events (or complex events) accumulate facts to capture behavior (for example, users always remove items from their cart after reaching checkout page). Through these events, a reason why an event happened can be inferred.

As you can see, events range from less complex and more granular (atomic) events, to more aggregated and high-level events (behavioral). Several atomic events that describe the same occurrence in a specific context form a related event. Several related event streams form together a behavioral event.

Some event examples (and reactions to these events) include:

* A new user was created—The user is sent a confirmation email.
* Inventory count in warehouse updated—Place orders for items that are running low.
* A new virtual machine is created—The IP for the machine is reserved in an IP Address Manager (IPAM).
* Payment is successfully completed—Send the request to the dispatching center.
* An unauthorized access attempt is detected—Security department receives an alert.
* The shipment arrives at the destination—Sales ticket is closed.
* User logs in for the first time of the day—Display daily news to the user.

![](/images/devcor/devcor_9_4_1.png)

Event-driven architecture is very loosely coupled and well distributed. Loose coupling is caused by the fact that the events themselves do not know anything about the consequences they cause. No event should explicitly reference another event; this functionality should be left to event handlers. This allows different service groups to be deployed on their own and minimizes the need for costly refactoring when adding new functionalities. Since the events already exist in your system, you only need to add new components that consume those events and react accordingly.

Development for an event-driven system is agile, since functional components are very loosely coupled. This ensures that any potential changes only affect one component and cause almost no downtime. Each component can also be tested individually but requires specialized tools (for generating and catching emitted events) to test. End-to-end testing is more complex though, due to the inherent difficulty in testing distributed systems, where the events being sent only once and the correct order of events plays a big role.

Scalability and performance both benefit from EDA. Performance is improved by the fact that EDA has the capability to perform asynchronous actions in parallel, which results in improved speeds. Scalability is a consequence of the extremely distributed and decoupled environment. It scales very well for scaling any type of component, be it event emitters, consumers, or the number of events that the system can handle.

The architecture is comprised of three components:

* Event Emitter: Event emitters gather the state changes required for an event to happen within a system and send them to the event channel.

* Event Channel: Event channel is used to forward the events to an event consumer. It can also do some preprocessing of the events before they are sent. Event channels often include messaging queues, to distribute messages horizontally or to prevent the channel from being overwhelmed.

* Event Consumer: Event consumers are components that can perform a specific task, based on the event that is processed.

EDA features the following characteristics:

* Asynchronous communication: Event emitters do not wait for event consumers to process the event before sending the next one.

* Real-time transmission: Emitters send events when they occur in real time. The opposite of this (real-time processing) is batch processing.

* Fine-grained communication: Events are fine-grained and are sent as they happen, instead of being aggregated.

* Multicast communication: Event emitters have the capability to send events to multiple consumers. The opposite approach is called unicast, where the emitter can only send data to a single receiver.

* Event Ontology: Events inside a system are classified in a hierarchical/group form. Based on their common characteristics. This allows the consumers to receive only the events they are interested in.

The biggest challenges when implementing EDA are guaranteeing the events are delivered exactly once and in the correct order.

If the events are duplicated and sent multiple times, this can change the final result and cause unwanted consequences. For example, in an e-commerce system, duplicate "order paid" events could cause multiple orders being shipped to the customer. The solution is to enforce a single acknowledgment policy in your architecture. Data may be sent and acknowledged at least, at most, and exactly once.

Events arriving in an incorrect order also cause unwanted consequences, errors, and logical fallacies. For example, users could be modified before they are even created.

The EDA architecture can be implemented with two different types of topologies.

The mediator topology is used when there is a need for you to orchestrate several event steps through a central mediator, and the broker topology is better suited for chaining events together without a central mediator. It is important for you to know the differences between these two topologies in order to understand which one is better for your situation.

Event-driven architecture is usually thought of as an opposite of request-response based architecture.

While in the request-response architecture (for example, consuming APIs to form microservices) the information flow inside the system is symmetrical, event-driven architecture functions on asymmetrical communication. Event processors return the information via callback, or new emitted events.

In request-response based architecture, information flow is more tightly coupled, since the requesting component expects an immediate response from the response component. On the other hand, event-driven components do not care or know who consumes the event.

Events should also include all the information that the event consumer needs to process the event. Request-response-based architecture commonly only implies where the data required can be found (for example, by supplying a resource ID).

### Mediator Topology

Mediator topology is useful for events that have multiple steps and require some level of orchestration by a central event mediator to process them.

![](/images/devcor/devcor_9_4_2.png)

For example, new virtual machine provisioning might require you to allocate resources, reserve an IP address, provision the virtual machine, and configure the operating system. All these steps need to be orchestrated to determine which of the steps can be done first, last, which one can be executed in parallel, and which ones need to be done sequentially (for example, you cannot configure the OS if the virtual machine (VM) is not yet provisioned).

Mediator topology contains four different architecture components, where each one has its distinct role:

* **Event queue:** A message queue that is used as an endpoint for events to be transported to the event mediator.

* **Event mediator:** Central orchestration component that receives the initial event(s) and makes organized asynchronous calls to different event channels. The event mediator does not contain the logic to process the initial event, it only knows which steps it must take to process it.

* **Event channel:** Used to pass processing events from the mediator to an event processor. These are usually implemented as message queues or event processors.

* **Event processor:** Listens on event channels and executes suitable business logic, depending on the event. Each event processor should be a self-contained, independent, and loosely coupled component, that performs a very specific task within the system. It should not rely on other components, to perform its tasks.

This topology differentiates between initial and processing events. The initial event is the event. The processing events are the events generated by the event mediator, that represent processing steps that implement the business logic of the response to the initial event.

Coming back to the example of VM provisioning, the event queue might receive an event "VM requested," with some additional parameters. The event queue then passes the event to the event mediator. The mediator knows the steps required to provision the VM. It sends the processing events to the following event channels—the VM resource allocation processor and the IPAM processor. This, in turn, creates resource and IP address reservations. The event mediator then waits for those two events to be processed. How feedback is sent back to the mediator depends on the implementation—event processors commonly send the response events back to the mediator. When the two events are processed (for example, the "VM resources allocated" and "IP address reserved" events), the mediator can continue with execution and send the event to VM provisioning event channel. After the processor returns the "VM provisioned" response event, the last step, operating system configuration, can be started by the mediator. The process continues in this way until all the steps to process the initial event have been completed.

### Broker Topology

The broker topology is different from the mediator topology in the sense that it features no central component that orchestrates the message flow through the system. It follows the natural flow of events.

![](/images/devcor/devcor_9_4_3.png)

The message flow is instead chained together by a message broker, which is commonly implemented as a simple message queue. The broker component contains all the channels that are used by the message flow. The initial event is sent directly to a component, instead of to a mediator. In practice, the event is posted to the broker—a message queue's channel. The component then processes the event and emits a new one. This goes on and on so that a chain of events is formed that performs a certain business function. Once an event processor finishes processing the event, it is no longer involved in processing that event.

A good analogy to the broker topology is a relay race. There, the runners (event processors) each must do their part and run a portion of the total track, then they pass the baton (event flow) to the following runner.

If you consider the previous example of virtual machine provisioning, the message broker would receive the event "VM requested." It would direct the event to the proper message queue channel and to a VM resource allocation processor. The processor would allocate VM resources and emit the event "VM resources allocated" to the message broker. The event would be directed toward the IPAM processor, which would in turn emit the "IP address reserved" event, and so on, until the VM is provisioned and the operating system configured.

## 9.5 Microservice Architecture Concepts

Microservice architecture supports the modern application lifecycle by separating the functionalities into small deployable and independent services. This architecture brings many benefits including better availability and more defined scaling. But the flexibility of microservices also brings complexities. Automating the infrastructure and deployments is a must if you want to keep sanity in your application development and deployments.

The purpose of software architecture is to make your product more manageable, resilient, and fault tolerant. What is the best way to achieve that? It depends on the application’s goals. Modern applications are composed of distributed components, living on separate nodes, but working together to deliver a complete service to the users. Architecture does not change the user's perspective on accessing an application, but it can help to improve the experience when the workload increases on a complex back-end system. Architecture should encourage understanding and debugging the system, and it should promote easy management and change of code. It should help teams to work together efficiently.

There are many architectures which promote the above, one of the latest architectures that tries to cope with modern challenges is microservices. Microservices is an architectural approach to developing a single application as a suite of small services. Each service in microservice architecture runs in its own process and is independently deployable. The services can communicate together with a lightweight mechanism such as REST API. An easy and simple way to understand microservices is to compare them to the exact opposite, a monolithic design. Observe the high-level figure that compares these two approaches.

![](/images/devcor/devcor_9_5_1.png)

A monolithic system is a collection of functionalities that are running on a single machine, typically as one or a few processes. The characteristic of a monolith is that you cannot slice and deploy the components separately. The components in a monolith are more coupled together and communicate by Remote Procedure Calls (RPC) within the object namespace that a programming language defines. In contrast, microservices architecture promotes the distribution of different functionalities, or services, across multiple machines, where each service runs independently in its own process. Each service could be written in a different language, but they should know how to interact over the network using well-defined interfaces.

Both monolithic and microservices approaches have their benefits and flaws. One is easier to implement but less flexible. The other is highly flexible and tailored for a modern scalable system but can be complicated to develop and to manage. When designing software, you as a developer need to produce clever solutions to occurring problems. If you list the specifications that an application should follow, system and code modularity is probably high on the list. Microservices architecture specifically promotes a modular approach to development.

### Microservice Architecture Characteristics

The characteristics of the microservices architecture can be summed up by the following list:

* Independent evolution and deployment
* Independent scalability
* Availability
* Modularity preservation
* Independent technology and language stack

The word independent seems to come up frequently when talking about microservices. If you know about some other architectures, such as layered architecture, you might remember that the layers in that approach are also independent from each other. The separation of concern guides you to develop the presentation layer separately, the business, and data tier independently and so on. Microservices go even further and emphasize a system’s individual features. The individuality really shines when you need to make some changes in a system. Because a single service in a microservices architecture is considered as an independent component of a system, it can also be developed and deployed that way.

![](/images/devcor/devcor_9_5_2.png)

As the previous figure shows, with the microservices architecture you can get very flexible in how you scale your components. Each service could be replicated as many times you want, on any of the machines in the cluster, and put behind a load balancer. The important player here is to stay as stateless as possible with your services. A single service can be replaced at any time with a different implementation or the same one if the current run time fails. If you store the state of the application within the component, the users could encounter inconsistency when accessing the service. If you are already familiar with containerization, you might think that containers are the perfect match for deployment of such small services, and you would be right. The immutability that containers promote is a great match for deploying a microservice architecture component. With containers, you could prepare small immutable service objects that can be deployed many times and will always behave the same.

Another aspect of microservice architecture is availability: even if one microservice goes down, others might still work, and the users could still access those ones. For example, you are building a web store with the microservices architecture. It consists of many small services that are independently managed. The compute node (VM or container) where the payment service is running shuts down. The result is that users cannot purchase items, but they can still search through the page and add items to their carts because these services are alive. In a monolithic design, this would not be possible, since the entire logic runs on a single machine. Note that critical components such as a payment service should be replicated to multiple virtual machines or containers in order to ensure that users are able to finish their order. When deploying microservices in containers, they could also be replicated on different machines to further secure the availability.

The services are also technology independent. A search service on your web store application can depend on code written in Python and an Elastic Search datastore. The payment might be written in Java, front-end services in NodeJS, and the recommendation engine service in C#. But they need means of communication, which are typically done with HTTP services such as REST APIs. While this all sounds great, you might start thinking, who handles all these operational tasks and development? This is becoming more complex than monolithic designs. Organizations even have special teams that are working on such tasks; they are called DevOps and they combine the previously divided worlds of software developers and operations teams. Many tools and practices emerged that help you with development, testing, and management of such deployments. Deployment orchestrators such as Kubernetes enable production grade deployment with elastic scaling, Layer 7 load balancing, and many other features, by just writing a couple of lines of code in a config and running the deployment command.

The market today requires rapid changes, either in the business model, or in addition to the functionalities that are currently available. When there are thousands or millions of users accessing your application, you will get feedback for improvement on a daily basis. Users will tell you what is wrong with your application, and you should respond quickly to fulfill the needs of your clients and beat the competition. Such requests require rapid changes on the system. The code must be updated and tested as fast as possible. A new, agile approach is the way to answer the demands coming from the outside world quickly.

The reality of software development is that at the beginning of the project, there is never a full understanding of where your product will end up. Because of that, you need constant refactoring of the application code. If you are not careful when doing such rapid changes, you can end up with tightly coupled and confusing code that becomes harder to manage with every new deployment.

Microservices architecture can cope with such pressures well. The services are loosely coupled, meaning that not all services need to be updated at the same time when you need to make change to a functionality of the system. The loose coupling principle also defines that the services should not know too much about the surrounding services. The independence and immutability of services means that you can respond with changes only to a single service that needs to be updated. Microservices help to maintain clarity and isolation of the code. Services, as already mentioned, are decoupled from each other. It is still important that you follow software development patterns and best practices in your code, but the mess you can create in your code is now contained within the service and not propagated to the entire system. If you keep your services relatively small, you can focus on refactoring that single service without the fear that you will affect something else in the system.

Microservices help you to comply with the following characteristics:

* Rapid provisioning: New machines used for services in development, testing, and production environments need to be deployed rapidly.

* Rapid application deployment: The services should be deployed automatically and rapidly to the provisioned machines.

If you want to scale your microservices efficiently, you need to be able to rapidly provision new machines that will run the services. If a couple of decades ago it took 6 months to get a new application server, an hour for a new server a couple of years ago, it now takes couple of minutes to be up and running with the virtualization and containerization technologies. Containers especially are the main drivers of rapid provisioning.

![](/images/devcor/devcor_9_5_3.png)

As the previous figure shows, in microservice architecture you do not deploy the entire system again because you changed couple of lines in the search service of your web store. There should be no, or minimum, downtime when updating the components in the microservices architecture. Automation is not a pet project anymore it becomes a necessity. With microservices, there are more things to deploy, the services need to be monitored in a more sophisticated way, the number of things that can fail is higher than in a monolithic design, and the infrastructure where the services are being deployed is, as a result of flexibility, more complex.

Now that you understand the concepts and characteristics behind a microservice architecture, you might ask yourself, is microservice architecture the go-to template today? For many cases it is, but it is not always the preferred approach. Sometimes it is better to start with a simpler monolith and follow the good practices of loosely coupled design. Later if you decide you would gain from the microservice architecture you could split the monolith into independent services, if you were successful in keeping the monolith loosely coupled, at least this is something that the industry experts suggest. But you might want to start with microservice, nothing can stop you from that. In any case, you should write a modular application that can be easily managed in the future.

Before you decide that you will go for microservice architecture, you need to understand the business capabilities of the product. Would the consumers benefit from the extra work you will now put into the development? Do you expect to grow fast, and do you need more flexible ways of scaling your application? You also need to think about the data. Each service in microservice architecture owns their own data and has their own database. If you want to have a more centralized, relational data store, microservices might not be for your use case. The services must talk to each other over a network, and they should be monitored to achieve better resiliency and availability. One important part of microservices is constant improvement. The flexibility of microservices enables the developers to constantly improve the system with new tools, new language improvements and so on, meaning that the team on working on the project should be keen to learning new technologies.

### Microservice Automated Deployment and Management

Continuous integration and deployment (CI/CD) pipelines are becoming more mature and complex, enabling them to handle more tasks automatically. They are also becoming a prerequisite for supporting microservice deployment. When developers push code changes to a central Git repository, a pipeline with many tasks is triggered. The new change in a service is thoroughly tested with the predefined suite of tests, before it is packaged into images and deployed to the end machines. The time from the developer pushing the code to the change being alive on production is counted in minutes. This also increases the confidence in deploying faster and more often.

![](/images/devcor/devcor_9_5_4.png)

As the previous figure shows, a typical pipeline that gets triggered by code updates performs some basic unit tests, then more serious integration and static or code testing, and it first gets deployed to a staging server, where a reviewer can examine the new version of the application before it is accepted for production. Even this step could be automated by performing more automated tests instead of manual inspection. As the last steps of the pipeline, a service is packaged. If you are using Docker containers, the tasks should prepare a new versioned image. Then this image is being deployed to the necessary server in the cluster to replace the existing version of the service.

Infrastructure automation plays big role in deploying microservices efficiently. The architecture provides guidelines on how to split the applications into services for better availability and scalability. Your infrastructure should be able to follow the speed of development of the applications, and it should be able to automatically react to dynamic demands from the clients to perform elastic scaling of critical services of the system. Infrastructure where microservices are deployed should allow cheap replacement of individual services, quick scaling, be fault tolerant, and allow you to work as fast as possible.

If you return to the previous example of your web store developed as microservices, you need an infrastructure that can efficiently handle the workloads. In a monolith world, one or few machines are enough to run your entire application. The objects in a monolith are communicating in memory on the same machine and maybe requesting a few external services on the network. The communication model is quite simple. When it comes to microservices, all the positive features the architecture has bring complexities. You simplify many things if you decide to deploy services with containers. Containers are fast, immutable, and the provider of the containerization technology, such as Docker, provides options for communication between different containers.

Then comes scaling. How do you scale up a critical payment service when more resources are needed on Black Friday and scale down when the demands ease off? Custom solutions using scripting in Python could work, up to a point. But now you handle your business application and slowly create a new application that handles the infrastructure. Luckily, there are orchestrators such as Kubernetes (K8s) that you can use instead. With K8s you can define the characteristics of an infrastructure with an abstract configuration without knowing all the details on how to deploy a load balancer or do elastic scaling.

Of course, you first need infrastructure with servers that you can put in a Kubernetes cluster. You can also use cloud providers for your services deployment. Some of them also use Kubernetes as the native orchestrator, so you could use it without doing any installation.

## 9.6 Effective Distributed Application Logging Strategies

Logging used to be a functionality that was usually not considered very complex. An application would write logged data into one or more log files. The database, server, and any other components would do the same. Today, applications tend to reside in the cloud and use a distributed architecture. This takes logging to a whole new level, since applications often spin up new or turn off old instances of servers or containers. Knowing how to effectively create and store logs in a distributed environment is very important for the observability of the application and helps you determine the cause of problems and errors faster and easier.

Logging in a distributed environment can be complex. The number of application components has increased greatly, and so did the number of logs. Although you will usually check a smaller number of log files, since errors usually occur on one component, it is important to log all of the possible metrics that your application can produce. Many, possibly nonpersistent application components, produce loggable data. The logs in a distributed environment always have to be aggregated on a centralized logging component, since manually connecting to all the application components, and then opening and reading all their individual logs would be impractical. The goal of distributed logging is to give you one place where you can find all the logs.

In addition to logging, a monitoring solution should also be in place for a distributed environment, since it can provide you with data that the logging components are oblivious to.

In a distributed environment, the following types of logs are collected:

* **Application logging:** The application itself often produces the most relevant data about what is going on. If nothing else, it makes it easier to figure out where errors originate. Application logs provide insight into the operational side of the application and their entries can range from informational data all the way to critical errors. Informational entries contain data about the application starting and shutting down, any state changes that occurred, information about successful and failed requests, user input, and much more.

* **System logging:** System logging observes what is happening with the operating system where the application resides. System logs contain information about device changes, device driver operation, system changes, system events, operations, and more. A standard log called syslog is often used with UNIX-based systems and Windows-based systems use the Windows Event Log. Since an application tends to use multiple different systems, using standardized system logging simplifies aggregating this data.

* **Database logging:** Database logs provide you with the information about any executed queries or abnormalities in your database. General log queries allow you to analyze and troubleshoot executed queries. The error log shows you queries that failed due to incorrect parameters, accessing data structures that do not exist, or other problems. Slow query logs are also used for queries that perform suboptimally and need to be examined.

* **Web access logging:** Web access logging is as much of a tool for observability as it is for security. These logs contain data about every request that arrives to the application, and contain information such as the IP address, user agent data, request data, responses, requested resource, and more.

* **Event logging:** Events in a distributed environment can be difficult to track and log: they can originate in multiple sources, be it the client's browser, internal application event emitters, external third party systems, and more. They tell you information about the interactions that are happening inside your application and can be important in determining why something is not working in a distributed environment that uses event-based architecture. These logs can be extracted from application logs and event queues with tools like Google Analytics.

The most important part of logging in a distributed architecture is proper log aggregation. The inherent problem of logging in a distributed architecture is that it uses different systems that tend to present data in different formats. You might end up with different logs on each component of your application, meaning you could have to go through many servers or containers and know how logging works on each subsystem to get the whole picture. Being able to aggregate logs in a centralized location, ideally as close to real time as possible, allows you to easily and swiftly troubleshoot problems, do some additional analysis, and isolate anomalies.

![](/images/devcor/devcor_9_6_1.png)
![](/images/devcor/devcor_9_6_2.png)
![](/images/devcor/devcor_9_6_3.png)

Managing logs in a distributed environment can be implemented as simply as sending the logs to a central location or by using a more structured approach with dedicated agents that do the collection. In general, three distinct techniques are used to aggregate logs:

Log replication: One of the simplest ways to aggregate logs is to use file synchronization tools like rsync to sync the latest logs to the centralized location. This only solves the problem of the logs residing on multiple different servers, the same number of log files exist in these servers. In addition, synchronization has its own problems, such as a server being shut down before the logs had the chance to synchronize and the relatively big traffic overhead that comes with constant synchronization.

Centralized syslog repositories: These centralized repositories are set up to accept log entries from other systems. Instead of the logs being written to a log file locally, they are sent via the network to a centralized log server. Often, the syslog standard is used for this and small agents called syslog daemons, that are located on application components, are used to send the logs forward. While this is a definitive improvement from log replication, since logs are now real-time, centralized, and not just replicated, the log format is still in the hands of the individual component. The result is that any advanced analysis of the logs takes a lot of extra effort to do. In addition, centralized repositories often only support vertical scaling and do not distribute the logs horizontally, leaving you with a choice of archiving older logs somewhere else or paying an ever increasing price for logging.

Distributed log collection: This type of aggregation is much more flexible than the previous two approaches, as it uses distributed collectors for collecting logs. It allows you to first process the logs, such as converting them to the same log format no matter where the logs originated, to distribute these logs over many servers, and the ability to integrate other tools, such as monitoring tools or data/log analysis tools into the system. In addition to simple log entries, distributed log collection also supports capturing events and event streams.

![](/images/devcor/devcor_9_6_4.png)

A proper implementation ideally uses log collectors and divides the process of distributed logging into the following five stages:

1. **Collection stage:** First, the logs have to be collected. This is done by utilizing collector agents that work in various ways such as subscribing to a log file, offering an API endpoint for the logs to be sent to, or watching the local log files for changes.

1. **Forwarding stage:** Collected logs are then forwarded from the collector agents to a log aggregator. The log aggregator in a distributed environment is usually implemented as a standalone application component, or, in other cases, just another collection agent.

1. **Storage stage:** Logs are then persisted in an object storage, which is implemented as a local or a shared file system. Object storage features different settings for configuring log policies, such as how long are the logs stored, how granular are the aggregated logs, what happens when the retention period ends, and so on.

1. **Indexing stage:** The indexing (and alerting stage) is optional with distributed logging, but can be extremely useful, since it allows you to browse logs faster and more practically. It indexes the logs into different data sets, based on time or some other kind of a schedule. These indexed logs are then more easily consumed by searching and analytics tools such as Elasticsearch.

1. **Alerting stage:** The alerting stage checks various thresholds and performs pattern analysis to find patterns that might be of interest to people supporting the application. Alerts can then be raised automatically to notify the administrators of any abnormalities.

Some additional good practices in distributed logging include:

* Have a single, shared logging service and technology in place. Distributed logging is often used with microservice architecture. One of the defining characteristics of that architecture is that the underlying technology can be changed without affecting the application. Having a different logging technology for each microservice is not the best idea though, and, if a certain technology is chosen for the logging microservice, all other microservices should use that underlying technology.

* Use a correlation ID. Actions in a distributed application often span several different services. Correlating the logs between those services can be a nightmare if no identifiable information is included. Therefore, a correlation ID is often appended to each log, so the administrator can filter the logs by the correlation ID and quickly access relevant information.

* Do not trust more than one clock. While Network Time Protocol (NTP) can be used to synchronize clocks in the systems within the network, it can sometimes be unreliable. This is why it is better to only rely on a single clock as a source of truth, instead of relying on every clock from every system that sends data to a log aggregator. Relying on a single clock helps you avoid logical fallacies in logs, like a user being edited before it was even created.

* Expect and handle logging failures. As with any other component or service, logging can and will break. If you are using a shared logging service, it then becomes a single point of failure in the application. It is a good idea to keep local copies of logs on individual components, or to cache them, until the logging service becomes available again.

* Include a lot of information. When something goes wrong, you not only want to know that it went wrong, but also why it went wrong. By including some context in the logs, it becomes much easier to troubleshoot specific errors. You might want to include information such as date and time, stack traces, service information, function or class names, IP address and user-agent data of the client, and so on.

* Use tools that allow querying logs. By itself, application logs can contain an incomprehensible amount of information. By using tools that allow you to make meaningful queries toward a log, you can determine the answers to questions such as "Which service tends to fail the most during the peak hours of the day?" or "What's the average time that the service X needs to process a request?".

### ELK Stack

A popular logging strategy for distributed architecture is implementing an Elasticsearch, Logstash and Kibana (ELK) Stack. The "Stack" is a set of solutions that work very well together and is needed to create a complete environment in which the application can run without any additional support applications. ELK stack is therefore used as a complete solution for distributed logging.

ELK stack consists of three open source products that make up the acronym—Elasticsearch, Logstash and Kibana:

* Elasticsearch is a full text search and analysis engine based on the Apache Lucene search engine.

* Logstash is a log aggregation tool that collects data from various input sources, performs some transformations of the data, and then sends it to various supported output destinations.

* Kibana is a visualization layer that works on top of Elasticsearch, providing users with the ability to analyze and visualize the data.

* ELK stack uses beats as collector agents to centralize data in Logstash. They reside on your servers, on your containers, or can be deployed as functions.

![](/images/devcor/devcor_9_6_5.png)

ELK stack is gaining traction and popularity since it offers a powerful platform that is able to collect and process data from multiple different data source types, is designed with scalability in mind, and, very importantly, is open source.

## 9.7 Using Distributed Logging to Diagnose Problems

Modern applications are no longer monolithic applications where all code resides on a single server and runs the code for the complete application. Modern applications consist of multiple microservices, where each is designed for its own tasks.

In theory, the log data produced by a monolithic application is easier to track, since the complete application code runs on the same system and all logs have the same format. On the other hand, when using a distributed application, this can be quite different. Logs from multiple systems can have their own formats. When your application is under heavy load, it is hard to correlate individual requests. So, if you collect logs from all those distributed systems, it might be hard to read and analyze logs.

Therefore, it is very important that your logging systems provide enough information for you to be able to drill down on each request and correlate with requests from different systems.

There are multiple logging methods for distributed applications based on the microservice architecture. The simplest method is to monitor individual services and applications. This approach uses the same concepts as monitoring traditional monolithic applications. The problem of this approach is that it is hard to identify the root cause of the problem based on logs of an individual service. It might work for some types of failures, but it might be problematic if you want to see the full picture.

Since using logs from individual services is no longer enough, you may want to combine logs from many services and collect those logs in a single point. You can achieve this by collecting logs from multiple sources and stored in the single location. There are a lot of different solutions, but many of them are based on the Elasticsearch, Logstash, and Kibana (ELK) stack. The ELK stack allows you to collect, store, maintain, query, and visualize log records. The problem with this approach is that you can only see log entries for individual services.

![](/images/devcor/devcor_9_7_1.png)

The problem with these methods is that they do not monitor an application as a whole but only monitor individual components. In distributed application, the problem might be present in multiple mircoservices and it is important to know how to find the problem. One of the most important methods for finding a problem is tracing. A trace follows a request from when the request first appears in the application to the request's destination. Since monolithic applications run on a single system, you can easily observe the trace of an individual request. On the other hand, this might be harder in a distributed system. Therefore, a new concept of tracing is used in distributed applications. It is called distributed tracing. Distributed tracing allows you to profile and monitor microservice-based applications, locate failures, and improve performance. Distributed tracing follows the progress of a single request throughout the system from the source to the destination.

The primary concept of distributed tracing is to label the log when a request first appears in the system. As the request makes its journey, each log entry contains the same label that was assigned at the source of the request. This label indicates an activity that the system performs on each request. By using the label and the time stamp, you can quickly analyze the path or the activity that was performed on each request over multiple microservices.

![](/images/devcor/devcor_9_7_2.png)

The example shows how you can label a request when it is received by the system and use the same label across all calls.

![](/images/devcor/devcor_9_7_3.png)

There are multiple approaches to implementing distributed tracing:

* Implement your own solution.
* Use a distributed tracing framework.
* Use an automated distributed tracing solution.

When you choose to take the path to implement your own solution, you need to build your own internal tracing system from scratch. The system tracks requests and identifies messages and requests that are used across your system. When taking this path, you need to adapt your code to perform the request tracing. The main advantage is that you can adapt the tracing system to your needs, infrastructure, knowledge, and skill set. However, you will need to put in time and effort to implement this solution, so it may not be feasible for every organization.

The second approach is similar to the first one, but you use frameworks that already exist. In this approach, the framework takes care of all the tracing of your requests. Although this approach reduces the amount of code that you need to write for tracing, you still need to adapt your code. There are many different frameworks that you can use. Two examples of distributed tracing frameworks are OpenTracing and OpenCensus. Both frameworks support most popular programming languages, which allows you to build your tracing and logging tools and integrate them into your existing applications.

**Note:** OpenTracing and OpenCensus distributed tracing frameworks are now merged into a single project called OpenTelemetry, which is meant to replace both projects in the future.

Another approach is to use a cloud-based service. These services offer automatic monitoring of any request that is generated by the code and allow you to track the requests over multiple systems.

## 9.8 Diagnose Problems Using Application Logs

Modern applications typically use distributed architecture, where the application is composed of multiple microservices. With distributed applications, troubleshooting is more challenging, since you need to verify multiple systems. To perform efficient troubleshooting, it is very important that you collect logs centrally and that you are able to correlate the log entries.

In this lab activity, you will use a central log collector to troubleshoot issues in a distributed application.

## 9.9 Application Monitoring with Cisco AppDynamics

While the primary purpose of aggregating log data from different parts of a distributed application is to aid you in troubleshooting and provide an audit trail, this same data can be used in other ways. For example, it contains time stamps and therefore allows you to measure response times of different parts of your application. Various metrics can be calculated similarly.

As there are many function calls for many different requests in a typical application, it is easy to get overwhelmed with all the data. Purpose-built application monitoring software, such as Cisco AppDynamics, can process all this data and present it in a meaningful way.

![](/images/devcor/devcor_9_9_1.png)

Additionally, Cisco AppDynamics stores historic data to calculate baselines. When metrics fall below determined thresholds, the system displays alerts and notifies administrators. However, the true strength of Cisco AppDynamics lies in the way it can automatically correlate function or web service calls across multiple systems and intelligently visualize the aggregate data. The latter is invaluable during incidents and other troubleshooting sessions, significantly lowering the time to resolution.

Traditionally, data is obtained by ingesting logs. This is how many Security Information and Event Management (SIEM) systems work. Cisco AppDynamics does not rely on logging, but instead uses code instrumentation techniques to "hook into" application code. This approach relies on extending the programming language libraries to support collecting metrics. This way there is very little extra coding required in the application itself, and the collected data can be more detailed and complete than logs, since it does not rely on programmers to log it all.

Using instrumentation for web applications, Cisco AppDynamics can automatically discover business transactions. A business transaction is any kind of data processing flow through the application. It is usually a direct result of a user action, but it may also be in response to an API call. Examples include a user trying to log in, a user adding an item to a shopping cart, or a user requesting technical support.

![](/images/devcor/devcor_9_9_2.png)

Cisco AppDynamics will list all business transactions with the default, discovered names, which often include URL paths. As these are usually not very informative or user-friendly, you can give them more useful names. In the figure, both are listed.

As instrumentation code can observe the communication of application code with other systems, Cisco AppDynamics is able to create a map of application components and graphically represent how they communicate—an application topology, if you will.

![](/images/devcor/devcor_9_9_3.png)

A flow map, representing application topology, contains the following elements:

* **Node:** A monitored application server. A virtual machine (VM) network server may contain multiple application servers: for example, a Java and a Python application server.

* **Tier:** A logical grouping of nodes, typically a redundant set of nodes running the same service. For example, a three-tier application might have front-end and back-end tiers defined.

* **Back end:** A "black-box" component that is part of the processing flow but is not instrumented, such as a database or message queue system.

* **Application:** A separate set of components (nodes and back ends) representing a different business application. Found in complex environments with integration between applications.

### Monitoring Agents

Data collection is performed by monitoring agents, which forward it to the Cisco AppDynamics controller where data processing happens.

The Controller receives data from three different types of agents:

* **Application Server Agent:** For analyzing and monitoring code
* **Machine Agent:** For monitoring server utilization
* **End User Agent:** For performance visibility from the end user perspective

An Application Server Agent runs alongside application servers and is required for code instrumentation. Each programming language, or language run time specifically, requires a corresponding agent. The currently supported languages are Java, .NET, Node.js, Python, PHP, Go, and others.

Cisco AppDynamics also supports monitoring agents for operating system that provide server performance data, including CPU busy and idle times, network utilization, latency, loss, retransmissions, and disk and memory utilization.

Agents running on end user devices may also be employed. While the other two types of agents provide server-side visibility, the goal of end user agents is to measure performance as experienced by users. These agents run in the web browser, as additional JavaScript libraries deployed on the application web site, or as an integral part of a mobile application (iOS or Android). A C++ library is also provided for desktop or Internet of Things (IoT) applications.

Now look at how you can integrate an existing Web Server Gateway Interface (WSGI) application with Cisco AppDynamics to provide visibility into application performance.

To instrument a Python application, follow these steps:

1. Install Python Agent: **pip install -U appdynamics\<4.5**
1. Create a configuration file with application name, node, tier and controller info.
1. Start application with **pyagent**: **pyagent run -c <config> -- <command>**

As the first step, use **pip** to install the **pyagent** command, required Python libraries, and Java-based agent proxy. Agent proxy enables the Python Agent to communicate with the controller.

Next, you must create a configuration file for the Python Agent. This file includes a node name, as well as the application and tier it belongs to. It also contains credentials, location, and other information necessary for establishing a connection to the controller. The following is a configuration file skeleton.

```bash
  [agent]
  app = <app_name>
  tier = <tier_name>
  node = <node_name>

  [controller]
  host = <controller_host>
  port = <controller_port>
  ssl = on|off 
  account = <your AppDynamics controller account name>
  accesskey = <your AppDynamics controller account access key>
```

Alternatively, you can use a wizard to generate and download the agent package. This package already contains a populated configuration file. To access the wizard, click Getting Started on the controller user interface.

Lastly, start your application with the **pyagent** wrapper. This command also starts the Java agent proxy with your application.

### Cisco AppDynamics Controller

The Controller is the component of the system that processes and analyzes data gathered by different monitoring agents. It presents aggregate data in the form of dashboards and application health indicators, while still allowing you to "drill down" all the way to raw data if necessary.

Cumulative data can provide interesting business insights, such as the percentage of users that complete the purchase in relation to all the users that landed on the web site, shown in the following figure.

![](/images/devcor/devcor_9_9_4.png)

This data allows you to identify and evaluate individual steps, identifying potentially problematic areas.

You can host the controller yourself (on-prem) or use the officially provided cloud instance. Both options support access through the web UI or by using a mobile application.

For an on-premises installation, a free Lite version of the software is available. It only supports limited data history and lacks some of the features of the full Pro edition, but it may fit smaller applications.

## 9.10 Limitations of Distributed Systems and CAP Theorem

A distributed system resides on multiple separate nodes (computers or servers) and communicates by passing messages through the network. Unfortunately, communication is not perfect. Interruptions may occur frequently. In contrast to single node programming, you must be aware of theoretical and practical limitations when programming for distributed systems. Otherwise, you may inadvertently introduce hard-to-find bugs or other unexpected behavior.

A fundamental property of computer networks is their dynamic nature. Clients connect and disconnect from the network all the time. Sometimes they connect to the same service through different networks with different network addresses. Even servers may be relocated from time to time, possibly requiring new addressing.

Networking equipment fails and networks adapt to the changed topology by reconfiguring network routes. However, this process takes time. In the meantime, packets may be lost or routed through a different path. Having taken a different path, packets may take longer to arrive and arrive after packets that were sent later. Likewise, faulty hardware may change data in transmission, resulting in corruption. If you are not relying on a protocol such as TCP to handle these cases for you, you must remember to take care of it yourself.

![](/images/devcor/devcor_9_10_1.png)

### Networking Pitfalls

Unlike single-node systems, network programming introduces its own set of challenges.

In addition to networks being dynamic and unreliable in nature, consider the following list of network programming pitfalls:

* The network is insecure.
* Networking systems are diverse.
* Systems may fall under different administrative domains.
* The network introduces latency.
* The network bandwidth is limited.

While secure private networks may exist, many recent and historical incidents have shown us time and again that majority of networks, and the internet in particular, are not secure. Users from all over the world have access to the internet and may, either consciously or inadvertently, try to use your applications in an unforeseen way. As mentioned, sometimes data gets corrupted and may break your application.

Alternatively, someone may actively try to disrupt your service by using various denial of service (DoS) attack techniques, such as overloading it with spurious requests.

Users do not necessarily have malicious intent. They may just use a variety of diverse clients. Mobile applications are very popular, but every platform requires its own executable. As mobile operating systems implement differing network stacks, you should keep in mind that some incompatibilities may surface. This holds true for server operating systems.

Multiple server operating systems will help you guard against a single devastating bug taking down all of the nodes; however, administrative load will likely increase. Perhaps you will need to employ different administrators, specializing in different systems. With multiple administrators, it becomes increasingly important to provide them with the necessary tools to pinpoint a problem. This importance increases when multiple parties or organizations are involved. Otherwise, you can easily find yourself in a situation where an issue is always "someone else's problem".

As an inherent limitation, network communication introduces latency and imposes slower speeds compared to communication within a single node. While we may not always be aware of it, there are orders of magnitude of difference between the two. It may not matter whether a single, simple query returns in 0.1 or 10 milliseconds, but it does matter if you perform thousands of such queries sequentially.

In the recent years we have seen an increase in communication speeds, and you can find 100Gbps links in modern data centers. Regardless, there still exist many situations where network bandwidth is limited. For example, network clients may be in areas with low wireless signal strength or their connections could be metered due to the cost of transporting data to remote locations. You should not forget that, most of the time, bandwidth is shared by many users and applications on the network. When designing distributed systems, you must account for these factors.

### CAP Theorem

It is realistic to expect available bandwidth to further increase in the coming years. However, there are fundamental limitations to networks. Minimal latency is bounded by the time it takes electrical or optical signals to travel the distance between two systems and they cannot travel faster than light. In other words, signals will always take at least 30ms to travel from Germany to California (approx. 9000km).

CAP, or Brewer's, theorem describes another limitation concerning the behavior of distributed systems:

A distributed system can provide at most two out of the following three guarantees:

* **Consistency:** Responses provide up-to-date data or no data at all.

* **Availability:** Responses always provide data, even if it is not up-to-date.

* **Partition tolerance:** System continues operation in face of network failure.

![](/images/devcor/devcor_9_10_2.png)

The theorem was formally proven in 2002 and states that distributed systems cannot always fulfill requests (be available) with the up-to-date data (be consistent) when the network is unreliable, and messages are delayed or lost (network partition occurs).

The following figure demonstrates this.

As the network partition occurs, communication between the servers is disrupted. The client sends an update request to the first server, updating a value from X to Y. Since the first server cannot propagate the update to the second one, the second server retains the old value X.

![](/images/devcor/devcor_9_10_3.png)

The client requests the value from the second server, which returns the value X, even though the value should be Y. The system is not consistent.

The second server could instead refuse to provide client with the value, since it cannot know if it was updated on the other server. But then such a system would not be available.

## 9.11 Overcoming Challenges in Distributed Systems

Because you cannot prevent network failures, you must design distributed systems to be able to deal with them. Take a look at the available options.

Using a fully consistent approach guarantees data will always be accurate. Some use cases require this guarantee, such as transferring funds between bank accounts, for example. However, this approach may introduce additional latency into the system, as updates (data writes) must be synchronized across the network. Moreover, during a network partition, you will not be able to perform writes at all, in order to ensure systems always have the same data.

For other use cases, such strong guarantees may not be required. Users of a restaurant rating website may prefer to see some restaurant ratings, even if the data is not complete and is missing one or two of the most recent ratings. The same may be true for news or social media applications, where showing an hour old content is better than showing no content at all.

![](/images/devcor/devcor_9_11_1.png)

You can achieve this with eventually consistent systems. While the newest data may take time to synchronize and be consistent only at some time in the future, some version of data is always available.

Many systems provide this functionality but limit writes during loss of connectivity to avoid data conflicts. If you set a parameter to two different values on different servers, how will the system know which values to use when connectivity between these two servers is restored? Without any additional precautions, this will result in confusion, and the scenario is also called a split-brain scenario.

One possible solution is to allow changes to be performed only when the system can achieve quorum, meaning that the majority of the nodes agree on the change. For example, in a distributed system of 3 nodes, at least 2 nodes would have to agree on a change for it to be accepted. This makes the state of the system consistent; however, nodes with lost connectivity will not be able to perform changes and therefore lose availability.

![](/images/devcor/devcor_9_11_2.png)

In the figure, updates on the left-hand server are allowed because it has connectivity with one other server, resulting in quorum (2 out of 3 servers). The right-hand server does not fulfill quorum (1 out of 3) and therefore does not allow writes.

As an alternative, some systems always allow data writes on all the nodes, making the system fully available, but require you to select a conflict resolution mechanism. Often, time stamps are used, with newer values taking precedence. This results in the "last write wins" scenario, where the latest change is implemented. The downside of this approach is that some changes that came before may be lost.

### Robust Failure Handling

Regardless of the chosen approach to replicating your data, a single failure may still bring down your whole application. Consider the following scenario. A front-end web server encounters a software or hardware bug, perhaps a broken loop, that keeps opening new connections to back-end data servers, without waiting on and processing the replies. This overloads the back-end servers, making them unable to respond to legitimate requests from other front-end servers. So, even though you have multiple redundant servers, your application is down. This is not good. Is there something you can do to prevent such failures?

Ship builders long ago started building ships by sectioning them into bulkheads, which are isolated compartments. In case of an accident where a ship's hull was breached, the water would flood only the affected section. Since the ship would made of multiple sections, the other sections would not be affected, allowing the ship to still float.

You can implement the same approach, called the bulkhead pattern, for your applications. In the example of a front-end server causing trouble for all of the other servers, you could group back-end and front-end servers into multiple, individual resource pools, limiting the damage a faulty front-end server can cause to a single pool.

![](/images/devcor/devcor_9_11_3.png)

You can apply the same principle in other areas as well, such as using different connection pools for different services in your code. This way, a single faulty service may not impact other, healthy services (for example, by exhausting all the resources).

Isolating the impact of faults to a smaller number of nodes is the basis for a resilient operation and works well for all kinds of faults. However, for the case of exhausting resources while waiting for timeouts, we might implement additional, better error handling.

It makes sense for your applications to retry network connections and wait a while for responses. Many of the faults in the network are temporary and are quickly resolved by rerouting traffic. While some packets may be lost in the short time before a new path is established, the networking stack can resend them and recover from the fault without your application having to deal with it. On the other hand, some faults may need to be resolved manually and take longer to resolve. Here, waiting will not help, as the request will still fail, only a bit later. In the meantime, it may block other requests or unnecessarily tie up resources.

Impact of timeouts can be further alleviated by using the Circuit Breaker pattern: when too many calls start failing, fail fast and only attempt a retry after a while.

![](/images/devcor/devcor_9_11_4.png)

The circuit breaker will start in a normal, nontripped state and perform the calls on remote system. If the amount of failed calls per time unit exceeds a defined threshold, it will trip the circuit breaker. In this tripped state, all calls automatically fail for a specified amount of time. After enough time has passed, the system will again begin forwarding the calls.

This will ensure a prompt response for the majority of requests that are bound to fail, yet will still allow the application to recover eventually.

### Service Mesh

Software libraries are available to help you implement bulkhead and circuit breaker patterns in your code. However, you may instead consider using a service mesh platform which provides these mechanisms, as well as other features like service discovery.

A service mesh represents a network of interconnected, distributed services that you will typically find in applications with a microservice design. As the number of services and workloads increases, their management, as well as robust failure handling, becomes more demanding. Using a service mesh platform, you can control the way application services interact. It is often implemented as a infrastructure layer that is transparent to the application to provide safe and reliable communication between distributed services.

![](/images/devcor/devcor_9_11_5.png)

Implementation of a service mesh is commonly provided by a network of lightweight proxies, deployed alongside your application services, and managed centrally for better coordination.

# 10 Orchestrating Network and Infrastructure

## 10.1 Introduction

Efficient management of network and infrastructure is important in today's world. To improve efficiency and scalability, you can use a programmatic approach to manage your infrastructure. Most of the modern systems today offer some sort of application programming interface (API) to push the configuration changes. There are a lot of different tools to manage infrastructure consistently and efficiently.

This section describes how to use APIs to manage infrastructure and which tools you can use to manage infrastructure efficiently and with scalability in mind.

## 10.2 Configuring Servers Using Cisco UCS APIs

The Cisco Unified Computing System (UCS) is designed with programmability in mind. The Cisco UCS Manager provides a full-coverage API, which allows you to manage the compute, network, and storage resources with the same API.

The API accepts XML documents through HTTP or HTTPS. You can use any programming language to generate XML documents. Configuration and state information for Cisco UCS is stored in a hierarchical tree structure that is known as the management information tree (MIT), which is completely accessible through the XML API.

The Cisco UCS Manager XML API supports operations on a single object or an object hierarchy. An API call can initiate changes to attributes of one or more objects such as chassis, blades, adapters, policies, and other configurable components. When you initiate the change on the objects, you specify the parameters in an XML document. Cisco UCS replaces missing attributes with the default values. Incorrect attributes are ignored. API is transactional. If you change multiple managed objects (MOs), the API operation stops if any of the MOs cannot be configured. Cisco UCS rolls back the MIT to the previous state.

The API has the following programmatic entities:

* Classes: Define the properties and states of objects in the MIT.
* Methods: Actions that the API performs on one or more objects.
* Types: Object properties that map values to the object state.

### Cisco UCS Management Information Model

Cisco UCS represents all physical and logical components in a hierarchical management information model (MIM), which is also referred as MIT.

Each node in the tree represents a managed object or group of objects that contains its administrative state and its operational state.

The hierarchical structure starts at the top (sys) and contains parent and child nodes. Each node in this tree is a managed object and each object in the Cisco UCS has a unique distinguished name (DN) that describes the object and its place in the tree. Managed objects are abstractions of the Cisco UCS resources, such as fabric interconnects, chassis, blades, and rack-mounted servers.

The data management engine (DME), a user-level process running on the fabric interconnects, manages and centrally stores the information model. When a user initiates an administrative change to a Cisco UCS component (for example, applying a service profile to a server), the DME first applies that change to the information model and then applies the change to the actual managed endpoint. This approach is called a model-driven framework.

The following figure shows an example of the Cisco UCS management information model.

![](/images/devcor/devcor_10_2_1.png)



You can identify a specific object by its distinguished name (DN) or by its relative name (RN).

The distinguished name enables you to unambiguously identify a target object. The distinguished name has the following format, consisting of a series of relative names:

```bash
  dn = {rn}/{rn}/{rn}/{rn}...
```

In the following example, the DN provides a fully qualified path for adaptor-1 from the top of the object tree to the object. The DN specifies the exact managed object on which the API call is operating.

```bash
  dn =”sys/chassis-5/blade-2/adaptor-1”
```

The relative name identifies an object within the context of its parent object. The distinguished name is composed of a sequence of relative names. For example, the following distinguished name:

```bash
  dn = "sys/chassis-5/blade-2/adaptor-1/host-eth-2"
```

The distinguished name is composed of the following relative names:

```bash
  topSystem MO: rn="sys"
  equipmentChassis MO: rn="chassis-<id>"
  computeBlade MO: rn ="blade-<slotId>"
  adaptorUnit MO: rn="adaptor-<id>"
  adaptorHostEthIf MO: rn="host-eth-<id>"
```

### Cisco UCS Manager API Methods

Cisco UCS API Methods are used to perform actions on one or more objects.

There are four API method categories:

* **Authentication methods:** Used for authentication purposes
* **Query methods:** Methods to obtain information on the current state of a managed object
* **Configuration methods:** Methods to make configuration changes to managed objects
* **Event subscription methods:** Methods to subscribe to events

Authentication methods authenticate and maintain the session. The following list shows couple of most commonly used methods:

* aaaLogin
* aaaRefresh
* aaaLogout

Use the aaaLogin method to get a valid cookie that you can use in subordinate API calls. Use the aaaRefresh method to maintain the session and keep the cookie active. To terminate the session, you should use the aaaLogout method.

Query methods are used to get data from the Cisco UCS Manager. The following list shows a couple of examples of query methods:

* ***configResolveDn***
* ***configResolveDns***
* ***configResolveClass***
* ***configResolveClasses***
* ***configResolveChildren***
* ***configResolveParent***
* ***configScope***

Most query methods have the argument inHierarchical (a Boolean true/yes or false/no). If true, the inHierarchical argument returns all child objects. The amount of data that are returned from Cisco UCS can be quite large, especially if you use a query method on a class or DN that refers to an MO that is located high in the MIT.

The API also provides a set of filters to increase the usefulness of the query methods. These filters can be passed as part of a query and are used to identify the desired result set. You can use the following filter types:

* **Simple filters:** You can use True or False filters to specify the set of objects with True or False condition.

* **Property filters:** The property filters use the values of an object's properties as the criteria for inclusion in a result set. To create most property filters, you need to provide the classId and propertyId of the target object/property along with a value for comparison. You can use filters like the equality filter, not equal filter, greater than filter, less than filter, and others.

* **Composite Filters:** The composite filters are composed of two or more component filters. They enable greater flexibility in creating result sets. For example, a composite filter could restrict the result set to only those objects that at least one of the contained filters accepts. You can use the AND filter, OR filter, between filter, or XOR filter.

* **Modifier Filter:** A modifier filter changes the results of a contained filter.

There are several methods to make configuration changes to managed objects. These changes can be applied to the whole tree, a subtree, or an individual object. The following are examples of configuration methods:

* **configConfMo:** It affects a single managed object (for example, a DN).

* **configConfMos:** It affects multiple subtrees (for example, several DNs).

* **configConfMoGroup:** It makes the same configuration changes to multiple subtree structures (DNs) or managed objects.

Applications get state change information by regular polling or event subscription. For a more efficient use of resources, event subscription is the preferred method of notification. Polling should only be used under very limited circumstances. Use eventSubscribe to register for events. To receive notifications, open an HTTP or HTTPS session over TCP and keep the session open. On receiving eventSubscribe, Cisco UCS starts sending out all new events as they occur. You can unsubscribe from these events using the eventUnsubscribe method. Each event has a unique event ID. Event IDs operate as counters and are included in all method responses. When an event is generated, the event ID counter increments and is assigned as the new event ID. This sequential numbering enables tracking of events and ensures that no event is missed. An event channel connection that a user opens is closed automatically by Cisco UCS after 600 seconds of inactivity associated with the event channel session cookie. To prevent automatic closing of the event channel connection by Cisco UCS, the user must either send the aaaKeepAlive request for the same event channel session cookie within 600 seconds or send any other XML API method to Cisco UCS using the same event channel session cookie.

When you use any of the methods above, you receive a response. The response can be successful or you can receive an error. When a request has executed successfully, Cisco UCS returns an XML document with the information requested or a confirmation that the changes were made. A successful response indicates only that the request is valid, not that the operation is complete. For example, it may take some time for a server to finish a power-on request. The response to a failed request includes XML attributes for errorCode and errorDescr. A query request for a nonexistent object is not treated as a failure by the DME. If the object does not exist, Cisco UCS returns a success message, but the XML document contains an empty data field (<outConfig> </outConfig>) to indicate that the requested object was not found.

The following figure shows an example workflow for retrieving and configuring a managed object from the Cisco UCS Manager API.

![](/images/devcor/devcor_10_2_2.png)

### Cisco UCS Manager Emulator

The development of applications that use Cisco UCS Manager API can become difficult if you need to develop on the production systems. Therefore, Cisco offers a UCS platform emulator that emulates the actual UCS system. The Cisco UCS emulator runs as a virtual machine (VM) with the CentOS open source Linux distribution. This VM can run a Cisco UCS Manager multichassis, multiblade simulation on a laptop or desktop computer and does not require an internet connection. Cisco UCS platform emulator is delivered as a zip file and as an .ova file. The zip file includes the .vmx file.

The Cisco UCS platform emulator is built on the same code as Cisco UCS Manager. So, if you have written an application using the emulator, you can assume that code will work on actual UCS Manager. Cisco UCS emulator has a complete management information tree, so you can emulate every aspect of the UCS Manager. You can download the emulator from cisco.com.

After importing the Cisco UCS platform emulator and logging in, you can see the following menu in the console:

![](/images/devcor/devcor_10_2_3.png)

To retrieve data and configure objects, you can access the Cisco UCS platform emulator by using a web browser.

![](/images/devcor/devcor_10_2_4.png)

### Case Study: Cisco UCS Manager API Usage

The following case study will show how you can use the Cisco UCS Manager API to create a script that will automatically deploy service profiles from the templates.

The following are the assumptions for the automation script:

* The script will use Cisco UCS Manager API.
* The script will be written in Python.
* You want to provide command-line parameters for script execution:
  * template: Template name
  * prefix: Prefix for the service profile name
  * count: Number of service profiles

First create a skeleton for a UCS class that will be used as the Cisco UCS Manager API client and prepare the __init__() method:

```python
  class UCS:
      def __init__(self, host, username, password):
          # Store argument values and instantiate the HTTPConnection class
          self.host = host
          self.username = username
          self.password = password
          self.cookie = None
          self.conn = http.client.HTTPConnection(self.host)

      def api_request(self, body):
      def login(self):
      def logout(self):
      def get_service_profile_templates(self):
      def create_service_profile(self, name, template):
```

The code lists the methods that will be used in the UCS class. The __init__() method will accept host, username, and password as arguments. The __init__() method will also instantiate the HTTPConnection class toward Cisco UCS Manager API.

Implement an api_request() method that will be used for all requests.

```python
  def api_request(self, body):
      # Initiate the request
      self.conn.request('POST', '/nuova', body)
      
      # Read the response
      api_response = self.conn.getresponse()
      
      # Store the status and data
      status = api_response.status
      data = api_response.read()

      return (status, data)
```

Implement login() and logout() methods.

```python
  def login(self):
      body = f'<aaaLogin inName="{self.username}" inPassword="{self.password}" />'

      response = self.api_request(body)
      if response[0] == 200:
          response_xml = xml.etree.ElementTree.fromstring(response[1])
          self.cookie = response_xml.attrib['outCookie']
          return self.cookie

  def logout(self):
      body = f'<aaaLogout inCookie="{self.cookie}" />'
      
      self.api_request(body)
```

The login() uses the aaaLogin API method. The API call retrieves the cookie, which is then used in all other calls. Therefore, the cookie is stored in the class variable. The logout() method uses the aaaLogout API method. It is used to invalidate the cookie.

Implement the get_service_profile_templates() method.

```python
  def get_service_profile_templates(self):
      body = f'<configResolveClasses cookie="{self.cookie}"><inIds><classId value="lsServer"/></inIds></configResolveClasses>'

      response = self.api_request(body)
      response_xml = xml.etree.ElementTree.fromstring(response[1])
          
      templates = {}
      out_configs = response_xml.find('outConfigs')
      for server in out_configs:
          if server.attrib['type'] == 'initial-template':
              templates[server.attrib['name']] = server.attrib['dn']

  return templates
```

To retrieve data from the Cisco UCS Manager API, you can use the configResolveClasses method. The example shows how you can use the method to retrieve service profiles and service profile templates. In the next part, the code sends the request to the Cisco UCS Manager API and parses the output. The data is stored under the outConfigs tag.

Implement the create_service_profile() method that will create service profiles from a template.

```python
  def create_service_profile(self, name, template):
      body = (
          f'<configConfMo dn="" cookie="{self.cookie}"><inConfig>'
          f'    <lsServer dn="org-root/ls-{name}"'
          f'                     name="{name}"'
          f'                     srcTemplName="{template}"/>'
          f'  </inConfig></configConfMo>'
      )
      response = self.api_request(body)
      return response
```

The create_service_profile() method uses the configConfMo API method to configure managed objects on the Cisco UCS Manager. To define the service profile from the server, you need to specify the DN, service profile name, and source template.

Finally, implement the logic that configures the service profiles from the command-line arguments.

```python
  if __name__ == "__main__":

      # Parse command line arguments
      parser = argparse.ArgumentParser()
      parser.add_argument('--template', required=True)
      parser.add_argument('--prefix', required=True)
      parser.add_argument('--count', type=int, default=1)
      args = parser.parse_args()

      ucs = UCS(HOST, USERNAME, PASSWORD)
      ucs.login()
      if args.template in ucs.get_service_profile_templates():
          for i in range(args.count):
              name = f'{args.prefix}{str(i)}'
              response = ucs.create_service_profile(name, args.template)
              if response[0] == 200 and 'errorCode' not in str(response[1]):
                  print(f'The service profile {name} created successfully.')
      ucs.logout()
```

The code for parsing command-line arguments uses the argparse Python module. The code then establishes the connection, retrieves the templates, configures the service profiles, and logs out from the Cisco UCS Manager API.

Before executing the code, check the service profile templates on the Cisco UCS Manager.

![](/images/devcor/devcor_10_2_5.png)

There are two templates: template-app and template-web.

Execute the code in the terminal.

```bash
  ~/$ python application.py --template template-web --prefix web-server --count 3
  The service profile web-server0 created successfully.
  The service profile web-server1 created successfully.
  The service profile web-server2 created successfully.
```

You need to pass two required arguments: template and prefix. The count argument is optional. In this case, there will be three service profiles with the prefix web-server and these service profiles will use the service profile template template-web.

Verify the profiles' status on the Cisco UCS Manager.

![](/images/devcor/devcor_10_2_6.png)


## 10.3 Infrastructure as Code with Terraform

Terraform is an open source software that HashiCorp created. It is an infrastructure as code (IaC) software, which allows you to manage your data center or cloud environments.

Terraform uses configuration files to describe the desired state of the infrastructure. Terraform takes the configuration files and generates an execution plan that describes what Terraform needs to do to reach the desired state. After plan is confirmed, Terraform executes the required actions to reach the desired state. It can also determine what was changed and create an incremental execution plan. You can also remove the created infrastructure.

You can keep configuration files under version control, allowing you to quickly migrate to the "previous version" of the infrastructure.

The following figure shows the high-level workflow to deploy a desired infrastructure:

![](/images/devcor/devcor_10_3_1.png)

Terraform Configuration
To describe the infrastructure, Terraform uses configuration files.

Terraform uses its own configuration language:

* Syntax is called HashiCorp Configuration Language (HCL).
* The configuration is declared in .tf files.
  * You can split your infrastructure declaration into multiple files for better organization.

* The basic syntax is as follows:

```bash
  <block type> "<block label>" "<block label>" {
    <identifier> = <expression>
  }
```

Blocks are defined with their block type and label. Blocks are used to define the configuration object. Blocks have a block type, which defines the type and can have zero or more labels. The block body begins with "{" and ends with "}". Between the curly braces, you can find the block body, which defines different parameters.

The most important constructs of the Terraform configuration file are resources:

* Resource block describes infrastructure objects.
* Resource block requires resource type and resource name
* Resource type and resource name must be unique within module.
* Resource arguments are defined inside the resource body

The example shows the resource block. The resource type is ucs_service_profile and the resource name is service_profile_01. Inside the resource block, you can see multiple arguments that are defined for this resource block.

The resource block defines the intent for infrastructure object. When Terraform creates an infrastructure object, it saves the state into the Terraform state. This state describes the current state of the object and it is used to update or destroy the infrastructure object in the future.

Terraform uses providers to deploy the configuration to the infrastructure:

* Providers define resources and arguments.
* Each provider can correspond to multiple resources.
* Provider is used to communicate with the infrastructure.
* Provider typically has its own configuration:
  * For example, URLs, credentials, and so on.

* Provider is defined with the provider block type.

The figure shows how Terraform matches providers with resources.

![](/images/devcor/devcor_10_3_3.png)

### Terraform Commands

Terraform is controlled through CLI commands. There is only one basic command to control Terraform: **terraform**. You can see the list of options by executing this command without any options.

```bash
  ~/$ terraform
  Usage: terraform [-version] [-help] <command> [args]

  The available commands for execution are listed below.
  The most common, useful commands are shown first, followed by
  less common or more advanced commands. If you're just getting
  started with Terraform, stick with the common commands. For the
  other commands, please read the help and docs before usage.

  Common commands:
      apply              Builds or changes infrastructure
      console            Interactive console for Terraform interpolations
      destroy            Destroy Terraform-managed infrastructure
      env                Workspace management
      fmt                Rewrites config files to canonical format
      get                Download and install modules for the configuration
      graph              Create a visual graph of Terraform resources
      import             Import existing infrastructure into Terraform
      init               Initialize a Terraform working directory
      output             Read an output from a state file
      plan               Generate and show an execution plan
      providers          Prints a tree of the providers used in the configuration
      refresh            Update local state file against real resources
      show               Inspect Terraform state or plan
      taint              Manually mark a resource for recreation
      untaint            Manually unmark a resource as tainted
      validate           Validates the Terraform files
      version            Prints the Terraform version
      workspace          Workspace management

  All other commands:
      0.12upgrade        Rewrites pre-0.12 module source code for v0.12
      debug              Debug output management (experimental)
      force-unlock       Manually unlock the terraform state
      push               Obsolete command for Terraform Enterprise legacy (v1)
      state              Advanced state management
```

The following are the main options with the **terraform** command:

* **terraform init**
  * It is used to initialize a working directory that contains configuration files.
  * You should use the command when adding a new configuration to the working directory.

* **terraform apply**
  * This command applies the changes to reach the desired state.

* **terrraform plan**
  * It is used to create an execution plan.

* **terraform show**
  * This command shows the current state from the state file.

### Case Study: Deploying Cisco UCS Service Profiles With Terraform

Terraform allows you to manage many different infrastructure elements. Some providers are already part of the Terraform base installation, but you can easily add your own providers.

The following case study will show how you can use Terraform to deploy Cisco UCS Manager service profiles from the service profile templates.

The figure shows the workflow that will be used in this case study.

![](/images/devcor/devcor_10_3_4.png)

**Note:** Terraform is not distributed with the Cisco UCS Manager provider. You can get the simple provider at the following URL: https://github.com/CiscoUcs/UCS-Terraform. You will also get the instructions on how to build the UCS provider and use it.

The first step is to prepare the provider's configuration. The configuration is stored in .tf files. This case study will cover how to store the provider's configuration in the main.tf file.

The output shows the configuration for the provider:

```bash
  ~/$ cat main.tf
  provider "ucs" {
    ip_address   = "192.168.1.1"
    username     = "ucspe"
    password     = "ucspe"
    log_level    = 1
    log_filename = "terraform.log"
  }
```

Cisco UCS Manager has two service profile templates (template-web and template-app) and no service profiles.

![](/images/devcor/devcor_10_3_5.png)

Next, you should configure resources. You will first add one service profile to the configuration file.

The output shows a configuration file for the service profile resource.

```bash
  ~/$ cat profiles.tf
  resource "ucs_service_profile" "web0" {
    name                     = "web-server0"
    target_org               = "org-root"
    service_profile_template = "template-app"
  }
```

The configuration is ready to be deployed to the infrastructure. At this phase, add these configuration files into the Git repository to manage the infrastructure configuration files under the version control. To start working with Terraform in this working directory, first initialize the working directory by using the **terraform init** command.

The following is the output of the **terraform init** command:

```bash
  ~/$ terraform init
  Initializing the backend...
  Initializing provider plugins...

  Terraform has been successfully initialized!

  You may now begin working with Terraform. Try running "terraform plan" to see
  any changes that are required for your infrastructure. All Terraform commands
  should now work.

  If you ever set or change modules or backend configuration for Terraform,
  rerun this command to reinitialize your working directory. If you forget, other
  commands will detect it and remind you to do so if necessary.
```

Now that working directory was successful initialized, you can use the **terraform plan** command to see the required changes to achieve the desired state of the infrastructure.

The following is the output of the **terraform plan** command:

```bash
  ~/$ terraform plan
  <... output omitted ...>
  Terraform will perform the following actions:

    # ucs_service_profile.web0 will be created
    + resource "ucs_service_profile" "web0" {
        + dn                       = (known after apply)
        + id                       = (known after apply)
        + name                     = "web-server0"
        + service_profile_template = "template-app"
        + target_org               = "org-root"
      }
  Plan: 1 to add, 0 to change, 0 to destroy.
  <... output omitted ...>
```

Terraform will add the configuration in the output to the Cisco UCS Manager when the **terraform apply** command is executed. Now that you know what will be configured, you can use the **terrform apply** command.

The following is the output of the terraform apply command:

```bash
  ~/$ terraform apply
  <... output omitted ...>
  Do you want to perform these actions?
    Terraform will perform the actions described above.
    Only 'yes' will be accepted to approve.

    Enter a value: yes

  ucs_service_profile.web0: Creating...
  ucs_service_profile.web0: Still creating... [10s elapsed]
  ucs_service_profile.web0: Still creating... [20s elapsed]
  ucs_service_profile.web0: Creation complete after 20s [id=web-server0]
  Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

The Terraform plan is displayed again. You need to confirm the plan by entering **yes** in the CLI. After a couple of moments, Terraform creates the service profile.

There is now one service profile on the Cisco UCS Manager.

![](/images/devcor/devcor_10_3_6.png)

Now, you can add additional service profiles. You will add two additional profiles into the profiles.tf file.

Three service profiles are now defined in the Terraform configuration file.

```bash
  ~/$ cat profiles.tf | grep web
  resource "ucs_service_profile" "web0" {
    name                     = "web-server0"
  resource "ucs_service_profile" "web1" {
    name                     = "web-server1"
  resource "ucs_service_profile" "web2" {
    name                     = "web-server2"
```

The following example shows the output of the **terraform apply** command.

```bash
  ~/$ terraform apply
  ucs_service_profile.web0: Refreshing state... [id=web-server0]

  An execution plan has been generated and is shown below.
  Resource actions are indicated with the following symbols:
    + create

  Terraform will perform the following actions:

    # ucs_service_profile.web1 will be created
    + resource "ucs_service_profile" "web1" {
        + dn                       = (known after apply)
        + id                       = (known after apply)
        + name                     = "web-server1"
        + service_profile_template = "template-app"
        + target_org               = "org-root"
      }

    # ucs_service_profile.web2 will be created
    + resource "ucs_service_profile" "web2" {
        + dn                       = (known after apply)
        + id                       = (known after apply)
        + name                     = "web-server2"
        + service_profile_template = "template-app"
        + target_org               = "org-root"
      }

  Plan: 2 to add, 0 to change, 0 to destroy.

  Do you want to perform these actions?
    Terraform will perform the actions described above.
    Only 'yes' will be accepted to approve.

    Enter a value: yes

  ucs_service_profile.web1: Creating...
  ucs_service_profile.web2: Creating...
  ucs_service_profile.web2: Still creating... [10s elapsed]
  ucs_service_profile.web1: Still creating... [10s elapsed]
  ucs_service_profile.web1: Still creating... [20s elapsed]
  ucs_service_profile.web2: Still creating... [20s elapsed]
  ucs_service_profile.web2: Creation complete after 21s [id=web-server2]
  ucs_service_profile.web1: Still creating... [30s elapsed]
  ucs_service_profile.web1: Still creating... [40s elapsed]
  ucs_service_profile.web1: Creation complete after 41s [id=web-server1]

  Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

Terraform will add two additional service profiles to the Cisco UCS Manager.

After issuing the **terraform apply** command, there are now three service profiles on the Cisco UCS Manager.

![](/images/devcor/devcor_10_3_7.png)

You can add or remove service profiles from the Terraform configuration file and you need to run the **terraform apply** command to apply the changes. Since everything is under version control, you can easily revert infrastructure to the previous state. You need to revert configuration files using standard Git commands and you need to run the **terraform apply** command again. Terraform will calculate the difference to the desired state and apply the required changes.

If you want to remove the configuration, you can use the **terraform destroy** command. Terraform will remove all configurations from the Cisco UCS Manager.

The example shows an output of the **terraform destroy** command.

```bash
  ~/$ terraform destroy
  <... output omitted ...>
    # ucs_service_profile.web0 will be destroyed
    - resource "ucs_service_profile" "web0" {
  <... output omitted ...>

    # ucs_service_profile.web1 will be destroyed
    - resource "ucs_service_profile" "web1" {
  <... output omitted ...>

  Plan: 0 to add, 0 to change, 3 to destroy.
  <... output omitted ...>
  Destroy complete! Resources: 3 destroyed.
```

There are no service profiles on the Cisco UCS Manager.

![](/images/devcor/devcor_10_3_8.png)


## 10.4 Differentiating Configuration Management Solutions

When you start managing your infrastructure as a code, it is important to choose the right tool for the job. There are many different tools available today. Each tool has a different strength.

It is important to differentiate between configuration management and orchestration:

* **Configuration management:**
  * It is used to manage resources itself.
  * The following actions can be performed: install software, provide configurations, and manage services.
  * Examples of the configuration management tools are Ansible and Puppet.

* **Orchestration:**
  * It is used to provision resources.
  * The typical action would be to create a new virtual machine.
  * Terraform can be used as an orchestration tool.

The main difference is that orchestration tools focus on bootstrapping and initializing resources, while configuration management tools configure the resources that exist in the environment. There is some overlap between tools. Configuration management and orchestration tools can work together to manage the infrastructure as code. Therefore, you can use both type of tools in your environment.

The figure shows the typical workflow when you manage your infrastructure as code.

![](/images/devcor/devcor_10_4_1.png)

In the first phase, the infrastructure is provisioned with the orchestration tool. For example, you can use Terraform to create new virtual machines on the infrastructure. After the infrastructure is provisioned, you can use the configuration management tool to install software, deploy the required configuration, or start services on the provisioned virtual machines. You can use tools like Ansible or Puppet to perform this task.

### Procedural vs. Declarative

There are some differences in the way that tools define how to perform the actions.

The management configuration and orchestration tools are grouped in two categories that describe the way that actions are performed:

* Procedural tools
* Declarative tools

The procedural tools describe the steps that are required to achieve the desired state of the infrastructure. For example, if you want to create 10 virtual machines, you would need to specify that you need to create 10 virtual machines. If you need to reduce the number of virtual machines to 3, you would need to create instructions that remove 7 virtual machines. Ansible is an example of a tool that can work procedurally.

On the other hand, declarative tools describe the desired state of the infrastructure. For example, if you need 10 virtual machines, you will add 10 virtual machines to the specification. If you need to reduce the number of virtual machines to 3, you will change your specification to 3. Examples of declarative tools are Puppet and Terraform.

The boundary for particular tools is not always strict. For example, Ansible uses modules to define the actions. Some modules are written in such way that you describe the desired state. It is responsibility of the module to check the current state, compare the current state with the desired state, and perform actions if needed. Therefore, Ansible can be treated as hybrid tool that can work procedurally or declaratively.

### Configuration Management Tools

The goal of configuration management tools is to configure the specified resources. Both Ansible and Puppet can be used to specify the configuration and apply the configuration to the nodes. However, these tools use different approaches to perform the actions.

The following table summarizes the key differences between Ansible and Puppet:

| Ansible             | Puppet                     |
|---------------------|----------------------------|
| Procedural Language | Declarative Language       |
| Client Only         | Client/Server architecture |
| Push model          | pull model                 |

Ansible actions are described in tasks. Each task defines an action that needs to be performed on a node to apply a change. Tasks are defined as part of the Ansible playbook, which describes a procedure that needs to be executed to apply changes. Playbooks are written in YAML syntax.

Ansible uses an agentless architecture, which means that you do not need an agent to be installed on a node to manage that node. You need to create an inventory of nodes, where each node is described. You then execute an Ansible playbook based on the specified inventory.

When you execute an Ansible playbook, Ansible executes task by tasks in order. Ansible connects from the host machine to defined nodes and executes actions on that particular node.

Puppet on the other hand uses declarative language. You declare the desired state of a resource on a node and then Puppet makes sure that resource is in a desired state. Puppet uses its own syntax to describe a desired state. A desired state is written in files called manifests. Manifests describe how to configure resources. Puppet compiles manifests into catalogs, which are then shared across the network.

Puppet uses client-server architecture to manage an infrastructure. You need to deploy a master node, which controls an infrastructure. You also need to install an agent on each managed node to allow you to manage resources on that node.

Puppet uses the following procedure to apply changes to nodes:

An agent node gathers facts and sends facts to a master node. At the same time, it requests a catalog from a master node.

A master node creates a catalog from the manifests and sends a catalog to an agent node.

An agent checks a catalog and applies required changes to resources that are described in a catalog.

An agent responds to a master with a report.

This procedure is executed periodically, which means that all your manual changes on a node will be reverted in a future period. On the other hand, when you use Ansible, you need to explicitly run a playbook to revert manual changes on a node. Puppet uses the pull model, while Ansible uses the push model.

The following figure shows the difference in the push and pull models that are used by Ansible and Puppet.

![](/images/devcor/devcor_10_4_2.png)


## 10.5 Configuring Network Parameters Using Puppet

Puppet is a configuration management tool that you can use to automate configuration deployment on your infrastructure.

Puppet configuration management tool has the following features:

* Puppet is an open source project managed by the company called Puppet.
* It is written in C++, Clojure, and Ruby.
* It uses Puppets's declarative language to describe a desired configuration.
* It can be used on many different systems:
  * Various Linux distributions, like Red Hat, Ubuntu, and so on.
  * Various UNIX systems
  * Microsoft Windows
* It uses client/server architecture
  * Client pulls the required configuration from the server.

### Puppet Architecture

Puppet has a client/server architecture. The client is called agent in Puppet terminology, while the server is called master. The master controls the complete configuration. The agent periodically pulls the configuration from master.

The following workflow is used to update the agent's configuration:

* An agent node requests catalog from the master.
* The master compiles the Puppet code and returns the catalog with required configuration.
* The agent applies the configuration by checking the state of the resources and applying the required changes.
* The agent responds with the report.

![](/images/devcor/devcor_10_5_1.png)

The procedure is repeated periodically. By default, an agent requests the configuration every 30 minutes.

Typically, you would want to manage the complete infrastructure with one tool. Puppet requires that nodes have an agent installed. However, you cannot install an agent on some devices, such as switches, routers, and firewalls. For those devices, you need to configure one of the nodes as a proxy for devices. The proxy performs all communication with the master, connects to the device, and applies the configuration.

The figure shows the architecture when you use a proxy node to manage devices without agents.

![](/images/devcor/devcor_10_5_2.png)

### Basic Puppet Concepts

When you use Puppet, it is important to that you are familiar with basic concepts that are used by Puppet.

Puppet uses the following concepts:

* **Resource:**
  * Portion of Puppet code that describes the particular aspect of a system.

* **Class:**
  * Collection of resources that can be reused multiple times.

* **Manifest:**
  * Configuration file that describes how to configure your resources.

* **Catalog:**
  * Compiled configuration for each individual node.

* **Module:**
  * Collection of manifests, templates, and other files that is used to configure multiple options of a specific feature and allows reuse.

The most elementary entity is called a resource. It is a unit of configuration that you use in configuration files. You need to define a resource type, a title, and one or more attributes with specific values. The resource is defined with the following syntax:

```bash
  <TYPE> { '<TITLE>': <ATTRIBUTE> => <VALUE>, }
```

You can list multiple attributes, separated with commas. You can also write attributes in multiple lines. Resources must be unique, which prevents conflicting values for the same resource. The uniqueness is achieved with the resource title and the name attribute. If resources are not unique, you will get an error when compiling the configuration.

There are multiple resource types. Some resource types are built-in, but you can also add new resource types.

The following list shows some built-in resource types:

* **exec:** Used to execute command.
* **file:** Manages files.
* **service:** Manages running services.
* **user:** Manages users on a system.

The following snippet shows the file resource type example:

```bash
  file  { 'myfile_present':
    ensure => present,
    path => '/root/myfile',
    owner => 'root',
    group => 'root',
  }
```

You can group resources into classes. Classes are similar to classes in regular programming languages, where you can use classes to better organize the code.

Before you want to use a class, you need to define a class. Defining a class means that you specify a class's configuration and make it available for later use. The class definition does not specify any actual configuration for nodes. The following snippet shows an example of the class definition.

```bash
  class save_date (String $file_path='/root/date.log') {

    file { 'log_file_present':
      ensure => present,
      path => $file_path,
    }

    exec { 'save_date_cmd':
      command => "/usr/bin/date > $file_path"
    }

  }
```

The class in the example checks that a file exists and stores the date to that particular file. The example also shows how you can define a class that accepts parameters. The class in the example accepts file_path as a parameter. If parameter is not passed to the class, the default value ('/root/date.log') is taken.

The class definition only specifies the class. To use a class in the configuration, you need to declare a class. There are ways to declare a class:

* Include-like declaration
* Resource-like declaration

Include-like declaration is the most common type of declaration. This type of declaration allows you to declare class safely multiple times. The most common way to declare a class with include-like declaration is to use **include** function. The following snippet shows an example of declaring a class with the **include** function.

```bash
  include save_date
```

Another way to declare a class is to use resource-like declaration. This declaration type is useful if you want to pass parameters to the class to override the default parameters. Resource-like declaration must be unique. The following snippet shows an example of resource-like declaration where you pass variable to the class.

```bash
  class { 'save_date':
    file_path => '/root/date_file.log'
  }
```

The main configuration of the infrastructure is specified in a manifest. The manifest stores the Puppet code that describes how resources should be configured. The manifest is a text file with the .pp extension. It uses UTF-8 encoding. The manifest can be a single file or you can specify configuration in a directory that contains multiple manifests. Puppet treats multiple files as one. The main manifest is usually called site.pp.

The configuration in manifests is compiled into a catalog for each individual node. The catalog describes the desired state of a specific node and it is generated from the logic that is defined in the manifest. The master node sends the catalog to the agent. The agent applies the required configuration to the resource to bring the node to the desired state.

![](/images/devcor/devcor_10_5_3.png)

When you start writing more complex manifests, you probably want to store your code in modules. Modules allow reusability. They are used to configure a specific part of the infrastructure. Modules contain all previously described elements like resources, classes, manifests, and so on. You can write your own modules. You must follow the defined structure of the module and place the module in the correct location on the system. There are also a lot of modules available from the Puppet Forge. Puppet developers and the open source community write these modules.

When you develop or install your module, the module gives you classes and resources that you can use in your configuration code. These classes are used in the same way as you would define classes inline in the main manifest.

**Case Study: Deploying Basic Network Configuration on Servers**

The following case study will show you how you can use Puppet to configure basic network settings on a Linux server.

The following are the goals of this case study:

* Develop the Puppet code to configure basic networking on two CentOS servers.
  * IP address on the network interface
  * Default gateway
  * DNS server
  * NTP configuration

The following are the prerequisites:

* The master server and two nodes are already installed with clean CentOS installation.
* The Puppet is already installed on all nodes.
* The basic Puppet configuration is already done on the servers.

**Note:** Puppets already require some basic configuration on the servers. You need to configure Puppet master and you need to install Puppet agent on the nodes. Additionally, you need to install certificates on the master and agents. For complete installation steps, verify the official documentation.

Add the configuration to the **site.pp** file under the **/etc/puppetlabs/code/environments/production/manifests** folder.

First, create placeholders for different nodes in **site.pp**.

```bash
  node 'puppeta01' {

  }
  node 'puppeta02' {

  }
```
Use a node definition that will apply the Puppet code to certain node. For example, an IP address is a unique property of a node, therefore you need two different configurations.

To set the IP address on the interface, provide a configuration file and place the configuration file to the correct location. The content of the file will be generated from the template.

Store the following template in the **network-if.cfg** file:

```bash
  TYPE=Ethernet
  BOOTPROTO=none
  DEFROUTE=yes
  IPV4_FAILURE_FATAL=no
  NAME=enp0s3
  UUID=
  DEVICE=enp0s3
  ONBOOT=yes
  IPADDR=<%= @ip_address %>
  PREFIX=24
  GATEWAY=192.168.1.1
```
The IPADDR parameter will be configured as parameter in this template. Everything else will be statically configured.

Next, create a Puppet class that will create a required file, disable the interface, and enable the interface.

Add the following class to the **site.pp** file:

```bash
  class conf_if (String $ip_address) {
    file { 'if_config':
      path => '/etc/sysconfig/network-scripts/ifcfg-enp0s3',
      content => template("$settings::manifest/network-if.cfg"),
      ensure => present,
    }
    exec { 'if_down': command => '/usr/sbin/ifdown enp0s3',
      require => File['if_config'],
    }
    exec { 'if_up': command => '/usr/sbin/ifup enp0s3',
      require => Exec['if_down'],
    }
  }
```

The class accepts the **ip_address** variable. The first action is to create a file in the correct location, which is specified with the **path** attribute. The **content** attribute specifies the content of the file. The content is created with the **template** function, which evaluates the specified template with the defined variables. In this case, you need to pass the **ip_address** variable. The content attribute uses the **$settings::manifest** variable to point to the correct location on the file system. Next, you need to disable and enable the interface to apply the new configuration. The **exec** resource uses the **require** attribute, which adds a dependency. The code first creates a file, then executes the **ifdown** command, and finally executes the **ifup** command.

Now that class is ready, you can declare the class.

Add the following code to **site.pp** to declare the classes.

```bash
  `node 'puppeta01' {
    class { 'conf_if': ip_address => "192.168.1.11" }
  }
  node 'puppeta02' {
    class { 'conf_if': ip_address => "192.168.1.12" }
  }`
```

The example shows the resource-like declaration. Each server will use a unique IP address in class declaration.

Next, create a class for DNS configuration. First, create a template that will be used in **/etc/resolv.conf** file.

Add the file **dns.cfg** with the following content to specify the template for the DNS configuration.

```bash
  <% [@dns_servers].flatten.each do |server| -%>
  nameserver <%= server %>
  <% end -%>
  ```

  The template will use a loop to add multiple entries to the file. The template expects the **dns_servers** list variable.

  Next, create a class in the **site.pp** to configure DNS servers.

  Add the following class to the **site.pp** file.

```bash
  class conf_dns {

    $dns_servers = ['192.168.1.253', '192.168.1.254']

    file { 'dns_config':
      path => '/etc/resolv.conf',
      content => template("$settings::manifest/dns.cfg"),
      ensure => present,
    }

  }
```

Next, you need to declare classes inside node configuration.

Update the **site.pp** file to declare the conf_dns class.

```bash
  node 'puppeta01' {
    class { 'conf_if': ip_address => "192.168.1.11" }
    include conf_dns
  }
  node 'puppeta02' {
    class { 'conf_if': ip_address => "192.168.1.12" }
    include conf_dns
  }
```

Since you will not add any additional parameters to the class, you can use include-like declaration.

Finally, create a configuration for NTP servers. This time, you will use a Puppet NTP module.

To use the NTP module, first install Puppet NTP module.

```bash
  [root@puppetm manifests]# puppet module install puppetlabs-ntp
  Notice: Preparing to install into /etc/puppetlabs/code/environments/production/modules ...
  Notice: Downloading from https://forgeapi.puppet.com ...
  Notice: Installing -- do not interrupt ...
  /etc/puppetlabs/code/environments/production/modules
  └─┬ puppetlabs-ntp (v8.1.0)
    └── puppetlabs-stdlib (v4.25.1)
```

After the installation, you can use a Puppet NTP module directly in the configuration.

Update the configuration in **site.pp** file to declare the ntp class.

```bash
  node 'puppeta01' {
    class { 'conf_if': ip_address => "192.168.1.11" }
    include conf_dns
    class { 'ntp': servers => ['192.168.1.250'] }
  }
  node 'puppeta02' {
    class { 'conf_if': ip_address => "192.168.1.12" }
    include conf_dns
    class { 'ntp': servers => ['192.168.1.250'] }
  }
```

The ntp class from the Puppet NTP module will be used. The class accepts the **servers** variable, which is the list of servers.

The following is the complete configuration:

```bash
  class conf_if (String $ip_address) {
    file { 'if_config':
      path => '/etc/sysconfig/network-scripts/ifcfg-enp0s3',
      content => template("$settings::manifest/network-if.cfg"),
      ensure => present,
    }
    exec { 'if_down': command => '/usr/sbin/ifdown enp0s3',
      require => File['if_config'],
    }
    exec { 'if_up': command => '/usr/sbin/ifup enp0s3',
      require => Exec['if_down'],
    }
  }

  class conf_dns {
    $dns_servers = ['192.168.1.253', '192.168.1.254']
    file { 'dns_config':
      path => '/etc/resolv.conf',
      content => template("$settings::manifest/dns.cfg"),
      ensure => present,
    }
  }

  node 'puppeta01' {
    class { 'conf_if': ip_address => "192.168.1.11" }
    include conf_dns
    class { 'ntp': servers => ['192.168.1.250'] }
  }

  node 'puppeta02' {
    class { 'conf_if': ip_address => "192.168.1.12" }
    include conf_dns
    class { 'ntp': servers => ['192.168.1.250'] }
  }
```

Now that the configuration is ready, you can test the configuration. You have two options. You can either execute the **puppet agent -t** command on the node to run the pull request and update the configuration, or you can wait until the next pull period.

Use the **puppet agent -t** command on the first server to apply the configuration.

```bash
  [root@puppeta01 ~]# puppet agent -t
  2019-10-01 15:54:06.456334 WARN  puppetlabs.facter - locale environment variables were bad; continuing with LANG=C LC_ALL=C
  Info: Using configured environment 'production'
  Info: Retrieving pluginfacts
  Info: Retrieving plugin
  Info: Retrieving locales
  Info: Loading facts
  Info: Caching catalog for puppeta01.local
  Info: Applying configuration version '1569889091'
  <... output omitted ...>
  Notice: Applied catalog in 0.56 seconds
```

If everything went correctly, you can see the "Applied catalog" entry in the output.

You can check, if configuration was correctly applied to the server.

Use the **ip address** command to verify the IP address on the interface.

```bash
  [root@puppeta01 ~]# ip address show dev enp0s3
  2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
      link/ether 08:00:27:e4:38:63 brd ff:ff:ff:ff:ff:ff
      inet 192.168.1.11/24 brd 192.168.1.255 scope global noprefixroute enp0s3
        valid_lft forever preferred_lft forever
      inet6 fe80::a00:27ff:fee4:3863/64 scope link noprefixroute
        valid_lft forever preferred_lft forever
```

Check the content of the **resolv.conf** file to see that all servers were added.

```bash
  [root@puppeta01 ~]# cat /etc/resolv.conf
  nameserver 192.168.1.253
  nameserver 192.168.1.254
```

Check the **ntp.conf** file and the status of the NTP service.

```bash
  [root@puppeta01 ~]# cat /etc/ntp.conf | grep ^server
  server 192.168.1.250
  [root@puppeta01 ~]# systemctl status ntpd
    ntpd.service - Network Time Service
    Loaded: loaded (/usr/lib/systemd/system/ntpd.service; enabled; vendor preset: disabled)
    Active: active (running) since Tue 2019-10-01 15:54:11 CEST; 6min ago
    Process: 26754 ExecStart=/usr/sbin/ntpd -u ntp:ntp $OPTIONS (code=exited, status=0/SUCCESS)
  Main PID: 26755 (ntpd)
    CGroup: /system.slice/ntpd.service
            └─26755 /usr/sbin/ntpd -u ntp:ntp -g
  <... output omitted ...>
```

All configuration was correctly applied to the server when you ran the **puppet agent -t** command. The other server will be updated with the required configuration in the next pull request, so you do not need to do anything to set the server to the required state. If you change something manually on the server, the manual configuration will be overridden in the next pull period.

## 10.7 Configuring Network Parameters Using Ansible

Ansible is a configuration management tool for managing all aspects of infrastructure and applications.

Ansible configuration management tools have the following features:

* Ansible is an open source project that the company Ansible (now part of Red Hat) manages
* It is implemented in Python.
* It uses procedural language to describe desired configuration
* It runs on UNIX-like systems and can be used to manage many different systems, such as:
  * UNIX-like operating systems (Red Hat, Ubuntu, and so on)
  * Microsoft Windows
  * Network devices
  * Storage systems
* It is an agentless tool

Ansible was designed with simplicity in mind, and it can fit many different systems. You do not need any daemons or agents on a managed system to make Ansible work. Everything is controlled from the host system. The advantage of this approach is that you can manage many different systems without worrying about installing agents. Ansible typically uses SSH to connect to the managed systems, but can use other management methods, such as NETCONF, Representational State Transfer (REST) APIs, and so on.

### Ansible Architecture

There are two basic elements in Ansible architecture: the core engine and modules. The core engine is responsible for connecting everything together, parsing configuration files, executing the code, and so on. Modules are elements that add support for different managed systems and features.

![](/images/devcor/devcor_10_7_1.png)

A module is a standalone piece of code that Ansible uses to configure some aspect on the managed system. It can be executed locally or remotely on the end system. A module can be as specific as changing the password on the end system or can be very general, such as pushing a complete configuration to the router, for example. Modules have a standard interface that accepts arguments and returns some pieces of information to Ansible. There are a lot of different modules available that cover nearly any aspect of the infrastructure. If something is not supported already, you can write your own module, by using Python and a defined Ansible API.

### Basic Ansible Concepts

It is important that you are familiar with basic concepts that are used by Ansible.

Ansible uses inventory to store info about managed systems:

* Inventory can be specified in INI or YAML format.
* Defines hosts that Ansible manage.
* Hosts can be added to groups for better organization.
* Allows assigning a variable to host or group
* Supports dynamic inventory:
  * Executable script that prints hosts in defined format to the standard output.

![](/images/devcor/devcor_10_7_2.png)

By default, Ansible searches for an inventory file in the location **/etc/ansible/hosts**. You can specify different inventory files by using the **-i** parameter when executing Ansible from the command line. You can define multiple hosts and groups in the inventory file. You can also specify host and group variables next to the entries in the inventory file. Sometimes you need to add more configuration parameters. Ansible allows you to organize variables in separate files. You can define a **host_vars** folder and store files in that folder that have the same names as hosts in the inventory file. You can then add host-specific variables to those files. Similarly, you can create a **group_vars** folder and files with names that correspond to the group names in the inventory file. You can define group-specific variables in those files.

Ansible uses a playbook to describe a configuration language. Playbooks are the instructions that define how to configure your infrastructure.

The basic characteristics of a playbook are:

* The Playbook defines a set of instructions to configure the infrastructure.
* The Playbook is human-readable and uses basic text language.
* The Playbook is expressed in YAML format.
* The Playbook is a list of plays.
* A Play is a collection of tasks that perform different actions on the infrastructure.

![](/images/devcor/devcor_10_7_3.png)

A Play has these characteristics:

* It maps a group of hosts to defined tasks.
* You need to provide a **hosts** parameter and a **tasks** parameter that defines the list of tasks for execution.
* Each task executes a module with specific arguments.
* The following is a basic syntax of a play:

```bash
  - hosts: <HOST_OR_GROUP_NAME>
    tasks:
      - name: <TASK NAME>
```

The list of tasks defines the actions that should be performed on defined managed systems. The tasks are executed in order, one at a time. Ansible executes tasks against all hosts that are defined in a play. A task must be finished on all defined hosts before moving to the next task. If a task fails on one of the hosts, the next tasks are not executed on that particular host.

The figure shows an example of how a playbook is executed on multiple hosts.

![](/images/devcor/devcor_10_7_4.png)

The tasks consist of the name and the module. The module definition accepts the module-specific parameters. There are also multiple different parameters and programming constructs on the task level. You can use conditionals and loops, you can save the task output to a variable, and so on.

The snippet shows an example of the play.

![](/images/devcor/devcor_10_7_5.png)

In the example, the play has two tasks. The first task copies the file from the local machine to the remote machine in the specified location. It also makes sure that correct owner and group are configured. The next task executes the command on the remote machine to store the current date to the file.

Ansible relies heavily on using Jinja2 templating language to enable dynamic expressions and access to variables. You can use many constructs that are used by Jinja2. For get more information about Jinja2, visit the official web page (http://jinja.pocoo.org).

This example of a play shows how you can use variables and Jinja2 templating language.

![](/images/devcor/devcor_10_7_6.png)

In the example above, the variable **file_path** is first defined in the play header. The variable is then used in two places in the play. Jinja2 uses double curly braces to specify the variable. The variable is dynamically replaced with the actual value at execution time. You can specify variables in multiple places, like the inventory file, **host_vars** or **group_vars** files, or in the play. You can also use task to define a variable to be used later in the play. You have a lot of flexibility here. You can also use different Jinja2 filters to dynamically perform some actions on the variables. It is a very powerful templating language. The advantage of using variables instead of hardcoded values is that you can abstract logic from data. So, you can change values on one place that will be dynamically replaced all over the play.

Ansible also allows you to create reusable playbooks. There are three options: includes, imports, and roles.

Imports and includes allows you to split one large playbook into more smaller playbooks. You can then reuse those smaller playbooks in other parent playbooks. The difference between include and import is that imports are processed at the playbook parsing time, while includes are processed when specific task is encountered.

A role is more like package, where you can group tasks, variables, templates, and so on. A role is typically defined to configure certain aspect of the infrastructure. A role consists of multiple files and it has a defined directory structure.

Another concept that is used by Ansible is collections. Collections are a new way to pack Ansible content in a reusable format and allow simple distribution. A collection may include playbooks, roles, modules, and plugins. You can install an Ansible collection from a community hub, a hosted server, or from tarball directly.

Case Study: Deploying Basic Network Configuration on Servers
The following case study will show you how you can use Ansible to configure basic network settings on a Linux server.

The following are the goals of this case study:

* Develop the Ansible configuration to configure basic networking on two CentOS servers.
  * IP address on the network interface
  * Default gateway
  * DNS server
  * NTP configuration

The following are prerequisites:

* The management station and two nodes are already installed with a clean CentOS installation.
* Ansible is already installed on the management station.

Ansible requires the inventory file, where managed hosts can be found. By default, Ansible will try to use the **/etc/ansible/hosts** inventory file. You can use the **-i** parameter to specify a different inventory file. For example, you can have one inventory file for production and another for testing. You can run same Ansible playbooks on different sets of hosts by only using **-i** parameter.

First, create inventory file with the name **hosts** and it will be saved in the same folder as playbook.

Add managed hosts to the inventory file.

```bash
  [servers]
  server01
  server02
```

The inventory file includes two hosts under the group servers.

In the first step, set the IP address on the interface. Create a template file with the required interface configuration. The IP address will be specified as a parameter in the template.

Add the following template to the **network-if.cfg** file:

```bash
  TYPE=Ethernet
  BOOTPROTO=none
  DEFROUTE=yes
  IPV4_FAILURE_FATAL=no
  NAME=enp0s3
  UUID=
  DEVICE=enp0s3
  ONBOOT=yes
  IPADDR={{ ip_address }}
  PREFIX=24
  GATEWAY=192.168.1.1
```

Ansible uses the Jinja2 templating language, so the variable is specified as a Jinja2 variable.

Before creating the playbook, you need to define variables. Use Ansible file system structure to define variables for managed servers.

Create the directory **host_vars** and add two files with host's variables into that folder:

```bash
  [root@ansible]# ls -l host_vars/
  total 8
  -rw-r--r--. 1 root root 29 Oct  7 09:12 server01
  -rw-r--r--. 1 root root 30 Oct  7 09:12 server02
```

```bash
  [root@ansible]# cat host_vars/server01
  ---
  ip_address: 192.168.1.11
```

```bash
  [root@ansible]# cat host_vars/server02
  ---
  ip_address: 192.168.1.12
```

Ansible will add those variables to the correct hosts at the run time. The variable in the template will be replaced with the actual value when executing the playbook.

Next, create a file **configure_servers.yml**, where you will store the Ansible plays.

Add the following content to the **configure_servers.yml** file:

```yaml
  ---
  - hosts: all
    tasks:
      - name: Create the interface configuration from the template
        template:
          src: network-if.cfg
          dest: /etc/sysconfig/network-scripts/ifcfg-enp0s3
          owner: root
          group: root
          mode: 644
```

The above is the basic structure of a play. You are using the **hosts: all** variable to specify that the play will run on all hosts that are defined in the inventory file. Next, you will define the tasks. The first task will use the **template** module. The **template** module is used to generate files from the defined template. The **template** module accepts a couple of parameters. The **src** parameter specifies the location of the template on the local machine, the **dest** parameter specifies the location on the remote machine, where file should be created. You can also specify the file's owner, group, and mode.

After adding the configuration file to the servers, you need to disable and enable the interface.

Add the tasks to disable and enable the interface on the servers.

```yaml
      - name: Disable interface
        shell: /usr/sbin/ifdown enp0s3

      - name: Enable interface
        shell: /usr/sbin/ifup enp0s3
```

This time use shell module, which is used to execute the command on the remote server.

Next, create a template for DNS configuration. Create a template file called **dns.cfg**.

Add the following content the **dns.cfg** file:

```bash
  {% for server in dns_servers %}
  nameserver {{ server }}
  {% endfor %}
```

The template uses Jinja2 syntax for the for loop.

You need to specify the values for the DNS servers. Create a folder for group variables. The name of the folder is **group_vars**. Add the **all** file to the **group_vars** folder. The variables that are specified in the **all** file are added to all managed servers.

Create the **group_vars/all** file

```bash
  [root@ansible]# ls -l group_vars/
  total 4
  -rw-r--r--. 1 root root 53 Oct  7 09:44 all
```

The following content will be added to the file:

```yaml
  ---
  dns_servers:
    - 192.168.1.253
    - 192.168.1.254
```

Now, you need to add the task to the playbook, that will take the template and create the correct DNS configuration on the servers.

Add the following task to the playbook:

```yaml
      - name: Create the DNS configuration
        template:
          src: dns.cfg
          dest: /etc/resolv.conf
          owner: root
          group: root
          mode: 644
```

Again you are using the template module. This time **dns.cfg** is a source and the destination will be **/etc/resolv.conf**.

Finally, create a configuration for NTP servers. You will create the reusable Ansible role. You can use the **ansible-galaxy** command to create a role skeleton in the file system.

Create **roles** folder and use the **ansible-galaxy init ntp** command under the **roles** folder to create a role skeleton:

```bash
  [root@ansible roles]# ansible-galaxy init ntp
  - Role ntp was created successfully
  [root@ansible roles]# ls -l ntp/
  total 4
  -rw-r--r--. 1 root root 1328 Oct  7 10:22 README.md
  drwxr-xr-x. 2 root root   21 Oct  7 10:22 defaults
  drwxr-xr-x. 2 root root    6 Oct  7 10:22 files
  drwxr-xr-x. 2 root root   21 Oct  7 10:22 handlers
  drwxr-xr-x. 2 root root   21 Oct  7 10:22 meta
  drwxr-xr-x. 2 root root   21 Oct  7 10:36 tasks
  drwxr-xr-x. 2 root root   30 Oct  7 10:35 templates
  drwxr-xr-x. 2 root root   37 Oct  7 10:22 tests
  drwxr-xr-x. 2 root root   21 Oct  7 10:22 vars
```

Create the template for NTP configuration. Take the default configuration file and replace the **server** section with the Jinja2 variables. Add the template file under **roles/ntp/template/ntp.conf.template**.

Add the following Jinja2 syntax to the **ntp.conf.template** file.

```bash
  {% for server in ntp_servers %}
  server {{ server }}
  {% endfor %}
```

Now you can create main role logic. The main role logic is specified in the **tasks/main.yml** file.

Add the following content to the **tasks/main.yml** file to handle the **ntp** package:

```yaml
  ---
  - name: Install NTP
    package:
      name: ntp
      state: present

  - name: Start NTP service
    service:
      name: ntpd
      state: started
```

Use the package module to make sure that the ntp package is installed and the service module to start the ntpd service.

Create the logic for generating the configuration file from the template and restart the ntp service:

```yaml
  - name: Create the NTP configuration from the template
    template:
      src: ntp.conf.template
      dest: /etc/ntp.conf
      owner: root
      group: root
      mode: 644

  - name: Restart NTP service
    service:
      name: ntpd
      state: restarted
```

The role is now ready. You can include the role in the main playbook. First, add NTP server information into the **group_vars/all**.

Add NTP information into **group_vars/all**.

```yaml
  ntp_servers:
    - 192.168.1.250
```

Include the role in the main playbook.

```yaml
    - include_role:
        name: ntp
```

The following is the complete playbook that is used in this case study:

```
  ---
  - hosts: all
    tasks:
      - name: Create the interface configuration from the template
        template:
          src: network-if.cfg
          dest: /etc/sysconfig/network-scripts/ifcfg-enp0s3
          owner: root
          group: root
          mode: 644

      - name: Disable interface
        shell: /usr/sbin/ifdown enp0s3

      - name: Enable interface
        shell: /usr/sbin/ifup enp0s3

      - name: Create the DNS configuration
        template:
          src: dns.cfg
          dest: /etc/resolv.conf
          owner: root
          group: root
          mode: 644

      - include_role:
          name: ntp
```

Now, you can run the playbook to configure the servers.

Use the **ansible-playbook -i hosts configure_servers.yml** command to execute the playbook.

```bash
  [root@ansible]# ansible-playbook -i hosts configure_servers.yml
  <... output omitted ...>
  TASK [Create the interface configuration from the template] ******************************************
  changed: [server02]
  changed: [server01]
  <... output omitted ...>
  PLAY RECAP *******************************************************************************************
  server01                   : ok=10    changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
  server02                   : ok=10    changed=8    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

When you run Ansible with your playbook, Ansible will execute tasks one by one. At the end, you will see the recap. Ansible will mark a task as changed only when the task did some changes. If you, for example, run your playbook again, some tasks will not change anything so you will see a different output. You would see something similar to the following output:

```bash
  [root@ansible# ansible-playbook -i hosts configure_servers.yml

  PLAY [all] *******************************************************************************************

  <... output omitted ...>

  PLAY RECAP *******************************************************************************************
  server01                   : ok=10    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
  server02                   : ok=10    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## 10.9 Defining Network Automation Source of Truth

When you perform any sort of network automation, it is important to have a single source of truth. The term single source of truth defines a practice where you manage and store your data on a single place. Having multiple sources for your data can cause data conflicts, which can cause wrong configuration on your network, or even failure in a network operation.

A single source of truth stores all required parameters, templates, and other elements to configure and operate your network. It describes everything about your network. You can have all pieces of information in one system or you can distribute your data over multiple systems. At the end, it is important that there is no data duplication and that you know were a single piece of information can be found.

Before storing the data, it is important to define where you will store your data.

You have multiple options to deploy the single source of truth:

* Configuration files
  * Multiple formats are available: YAML, JSON, XML, and so on.

* Databases
  * Relational databases: MySQL, PostgreSQL, MariaDB, and so on.
  * Nonrelational databases: MongoDB, Cassandra, and so on.

* Off-the-shelf solutions
  * Open source examples: Network Source of Truth (NSot), NetBox, and so on.

It is recommended to use the single place where you will store your data as shown by the following example.

![](/images/devcor/devcor_10_9_1.png)

You can also use a combination of multiple sources. For example, you can store VLAN parameters in the database and IP addressing parameters in the configuration files.

![](/images/devcor/devcor_10_9_2.png)

You should avoid having the same parameters in multiple locations.

![](/images/devcor/devcor_10_9_3.png)

The problem in the example is that you cannot reliably determine which is the correct VLAN parameter that you need to configure on the device. Therefore, if possible, try to avoid adding the same parameters to different places.

### Using Configuration Files as Source of Truth

When you start a network automation, configuration files will probably be the first source of truth. The configuration files offer the most simple and easiest start, since you do not need any infrastructure to start working. It is also easy to read the configuration files with any popular programming language. However, there are limitations with the files. When the amount of data is large, the management of configuration files becomes harder and it is harder to traverse through the long files, among other issues. You can solve those issues with the appropriate initial design. You can also split your configuration into multiple files. For example, you can have per device configuration files, per service configuration files, etc.

Before you start using files to store your network parameters, you need to determine the format of your configuration files. The most popular formats for storing data are YAML, JSON, XML, and CSV. The format of the configuration depends on your preference. If you are unsure, YAML is probably your best bet, since it is readable and its syntax is very simple.

![](/images/devcor/devcor_10_9_4.png)

The snippet shows an example of the data in YAML format.

As an alternative, you can use JSON or XML to store your data in the configuration file.

![](/images/devcor/devcor_10_9_5.png)

After you have determined the format for your configuration files, your next step is to determine the data model. You can simply create some examples and use those examples as source for your data model. On the other hand, you can go as far as defining the data model in a data modeling language. YANG modeling language is typically used in network automation. The YANG modeling language allows you to formalize your configuration structure, define parameters, and add constraints to parameters. Once you have a YANG model for your configuration, you can use it to validate your configurations before you even start configuring your devices.

```bash
  module vlans {
    namespace "http://example.com/vlans";
    prefix vlans;

    list vlans {
      key vlan_id;

      leaf vlan_id { type uint32; }
      leaf vlan_name { type string; }
    }
  }
```

The snippet shows an example of the YANG model.

When your data is defined in a configuration file, you should always have your files managed in a version control system, like Git. Having files under version control brings a lot of benefits. Version control systems allow you to compare files between different versions and identify differences. This comparison can be very valuable during troubleshooting. You can simply compare two different versions of your configuration and you can quickly spot the difference between working and nonworking configurations. If you discover that some configuration broke the network, you can simply switch back to previous version and deploy the old configuration to restore the system. If you store your configuration file under version control, you can easily regenerate configuration for your devices at any point. Also, having version control on your configuration allows you to implement a process like continuous integration and continuous deployment (CI/CD) in your network operation process. Since there are so many benefits of having your configuration files under version control, it is recommended to implement this practice when you are using files as source of truth.

The following figure shows an example how you can use Git as a version control to store the different versions of the configuration files.

![](/images/devcor/devcor_10_9_6.png)

If something goes wrong with the configuration, you can simply revert to the previous known working configuration. The example on the figure shows how you can revert the configuration to v2 and redeploy the configuration to the network using old version.

![](/images/devcor/devcor_10_9_7.png)

To use configuration files in automation scripts, you need to read the configuration file and parse the parameters from the configuration files. If you are using one of the standard formats, you would probably be able to find the library that already parses the configuration files for you. The following examples show how you can parse the configuration files in YAML, JSON, and XML formats using Python.

To parse the YAML files, use Python yaml library.

![](/images/devcor/devcor_10_9_8.png)

To parse the JSON files, use the Python json library.

![](/images/devcor/devcor_10_9_9.png)

To parse the XML files, you have many options. The example shows how you can use the Python xml library.

![](/images/devcor/devcor_10_9_10.png)

The library actually creates the Element object. The Element object has many methods to read the tags, attributes, and values of the particular object. You can also search through the child objects. There are many other options. Consult the documentation for detailed documentation.

### Using Database as Source of Truth

When your solution grows, you probably want to save your configuration parameters to the database. Storing the configuration parameters into the database increase scalability, but also increases complexity of your environment, since you also need to manage your database. Before storing your configuration parameters into the database, you need to select the database. There are two types of databases, relational and nonrelational.

Relational databases store data in tables and rows. Data is stored in a structured way and you can link information from different tables by using indexes. Relational databases use Structured Querying Language (SQL). Some examples of relational databases are MySQL and PostgreSQL.

Nonrelational databases represent data in collections of JSON documents. You can store structured, semistructured, and unstructured data. An example of a nonrelational database is MongoDB.

Relational databases are typically used when you need (or already have) structured data, or when you require transactional safety, or when you need to make complex queries. On the other hand, when you need to save a large amount of data with little structure, you should use a nonrelational database, which allows more flexibility.

There are many libraries for accessing different databases programmatically. The following example will show how you can use Python to access the data in a MySQL database. The example will use the MySQLdb Python module.

The following table shows the VLAN configuration parameters:

```sql
  mysql> SELECT * FROM vlans;
  +---------+-----------+
  | vlan_id | vlan_name |
  +---------+-----------+
  |      10 | test      |
  |      20 | prod      |
  +---------+-----------+
  2 rows in set (0.00 sec)
```

To access the VLAN parameters in a database, you can use the following Python code:

```python
  >>> import MySQLdb
  >>> db = MySQLdb.connect(host='localhost', user='student', password='student', db='networkconfig')
  >>> c = db.cursor()
  >>> c.execute('SELECT * FROM vlans')
  2
  >>> vlans = c.fetchall()
  >>> for vlan in vlans:
  ...  print(vlan)
  ...
  (10, 'test')
  (20, 'prod')
```

First, you need to make a connection to your database. To perform a query, you need to create a cursor object with the cursor method. Once you have a cursor object, you can use the execute method to execute the query. To read results, you can use methods like fetchone, fetchall, fetchmany, and so on. The results are returned as tuples, which you can use later to execute some actions.

## 10.10 Creating and Deleting Objects Using Firepower Threat Defense API

You can use the Firepower Threat Defense (FTD) REST API over HTTPS to interact with a FTD device through a client program. The REST API uses JavaScript Object Notation (JSON) format to represent objects.

The API has its own version number. There is no guarantee that a client designed for one version of the API will work for a future version without error or without requiring changes to your program.

To access the resources, you need to construct the URL. The following is an explanation of the URL nomenclature.

![](/images/devcor/devcor_10_10_1.png)

Interaction with the API is done by using HTTP requests with the correct method and correct URL.

The following is the general process to interact with the API:

* Obtain an access token to authenticate your API calls.
* Build a JSON payload if needed (except when you read data).
* Send the request to the FTD device using one of the HTTP methods and the correct URL for the resource.
* Consume the returned JSON response.
* If you make configuration changes, deploy the changes.

### API Explorer

Firepower Device Manager is a web-based configuration interface that is included on FTD devices. Firepower Device Manager includes an API Explorer that explains the resources and JSON objects available for your programmatic use. The Explorer provides detailed information about the attribute-value pairs in each object, and you can experiment with the various HTTP methods to ensure that you understand the coding that is required to use each resource. The API Explorer also provides examples of the URLs required for each resource.

![](/images/devcor/devcor_10_10_2.png)

To access the API explorer, use the https://FTD-HOSTNAME/#/api-explorer URL. Before you can access the API explorer, you need to authenticate yourself using your username and password.

### Authenticating Your REST API Client

The FTD REST API uses OAuth 2.0 for authenticating calls from API clients. OAuth is an access token-based method and FTD uses JSON web tokens for the schema.

To obtain the token, you need to send a request for token to the FTD device with your username and password.

![](/images/devcor/devcor_10_10_3.png)

To obtain the token, use the POST method. The path is https://FTD-HOSTNAME/api/fdm/v2/fdm/token. You also need to provide the username and password in a request. Use the following syntax for a request body:

```bash
  {
      "grant_type": "password",
      "username": "USERNAME",
      "password": "PASSWORD"
  }
```

The following is an example of the request for token:

```bash
  curl -k -X POST -H 'Accept: application/json' -H 'Content-Type: application/json' -d '{
      "grant_type": "password", 
      "username": "admin", 
      "password": "pass123"
  }' https://192.168.1.1/api/fdm/v2/fdm/token

  {   
      "access_token":"TOKEN",
      "expires_in":1800,
      "token_type":"Bearer",
      "refresh_token":"TOKEN",
      "refresh_expires_in":2400
  }
```

To use the token in subsequent requests, you need to include the Authorization header. You need to construct the Authorization header with the following syntax:

```bash
  Authorization: Bearer TOKEN
```

The following is an example of the curl command for requesting a resource using token:

```bash
  curl -k -X GET
      -H 'Accept: application/json'
      -H 'Content-Type: application/json'
      -H 'Authorization: Bearer TOKEN' 
  https://192.168.1.1/api/fdm/v2/fdm/devicesettings/default/devicehostnames
```

By default, the token is valid for 30 minutes. If you need the token for more time, you can either request a custom token with a custom expiration time, or you can use a refresh token. You need to use the refresh token that was supplied in the original grant. A refreshed access token is different from the original access token. “Refreshing” actually supplies a new pair of access and refresh tokens, it does not simply extend the life of the old access token. Here is an example of the JSON body that you should use to refresh the token:

```bash
  {
    "grant_type": "refresh_token",
    "refresh_token": "REFRESH_TOKEN"
  }
```

When you are stop using a token, it is recommended that you "logout". In this case, you need to revoke the token. The following is an example of the JSON body that you should use to revoke your token:

```bash
  {
    "grant_type": "revoke_token",
    "access_token": "TOKEN",
    "token_to_revoke": "TOKEN",
  }
```

### Using the Methods and Resources

To read and manipulate the data, you can use the GET, POST, PUT, and DELETE methods. You also need to construct the correct URL to access the data. Each call must also include the authentication token.

To test API calls, you can use the curl tool from the Linux command line interface.

You can read the data using the API with the GET HTTP method.

The following example shows the curl command to read all URL objects on Firepower Device Manager.

![](/images/devcor/devcor_10_10_4.png)

You will get a response for your request. Each response includes the HTTP status code. These are the standard HTTP status codes. The status code 200 typically indicates a successful GET/PUT/POST call, and 204 a successful DELETE call.

You will get the requested data in the response body. The response body returns the response in JSON format. The specific content that is returned is based on the resource model. You can get the documentation about the resource model in the API explorer.

If a resource can include multiple objects, you get a list of items on a GET request. You can include query parameters on the URL to control the number of objects returned. The default is to return 10 objects from the start of the object list.

Typically, each of the items includes the URL, which you can use to request the date for a single object, or you can use it for updating or deleting the object.

The output shows an example of the response body that you can receive when you request the URL objects from Firepower Device Manager.

![](/images/devcor/devcor_10_10_5.png)

To create a new object, you should use the POST method.

The example shows how you can create a new URL object.

![](/images/devcor/devcor_10_10_6.png)

The response to this request would be a JSON object that was created on Firepower Device Manager. The object will also include the URL of this object, which you can use later to update or delete the object.

To update the object, you need to use the PUT method. The PUT method replaces the entire object. You cannot simply change one attribute. Thus, you must ensure that your JSON object includes the old values that you want to preserve. Use the GET method for the parent resource to obtain a copy of the existing state of the object. You must include the correct values for the version and id parameters from the received JSON object.

The following figure shows an example of a PUT request to update the object. The URL includes the ID of the object. You can also see the body of the PUT request. The body uses the same version and id parameters as in original object. You can update other values according to the data model.

![](/images/devcor/devcor_10_10_7.png)

To delete an object, you need to use the DELETE method. Before you can delete the object, you need to have the URL of the specific object. No content is needed for the delete request. The HTTP status code of the successful delete is 204.

The following request shows an example of the DELETE operation.

```bash
  curl –k -X DELETE
      -H 'Accept: application/json’
      -H 'Content-Type: application/json’
      -H "Authorization: Bearer TOKEN” 
  https://192.168.1.1/api/fdm/v2/object/urls/e84ed095-f3ff-11e9-8e31-6d4c225a2852
```

### Deploying Configuration Changes

Although POST, PUT, and DELETE calls directly update the FTD device, they are not immediately active. You must deploy configuration changes before the device uses your new settings when processing traffic.

To deploy the configuration, use the POST method on the /operational/deploy resource.

```bash
  curl -k -X POST
      -H 'Accept: application/json'
      -H 'Content-Type: application/json' 
      -H "Authorization: Bearer TOKEN"
  https://192.168.1.1/api/fdm/v2/operational/deploy
```

When you request the deploy job, you will get a response. In the response, you can find the state, which tells you the state of the job. If there were no errors, the job will be QUEUED. You will also get the URL to the deploy job.

The following example shows the state of the deploy job.

![](/images/devcor/devcor_10_10_8.png)

```bash
  curl -k -X GET https://192.168.0.40/api/fdm/v2/operational/deploy/7bee8c23-f407-11e9-8e31-0750847c3ef4

  {
    "id" : "7bee8c23-f407-11e9-8e31-0750847c3ef4",
    <... output omitted ...>
    "state" : "DEPLOYING",
    "name" : "User (admin) Triggered Deployment",
    "links" : {
      "self" : "https://192.168.1.1/api/fdm/v2/operational/deploy/7bee8c23-f407-11e9-8e31-0750847c3ef4"
    }
  }
```

The state of the deploy job is DEPLOYING until the status is changed to DEPLOYED. The status DEPLOYED indicates that the job completed successfully. The modifiedObjects parameter lists the objects that were changed in the deployment job.

The following snippet shows the response when the job is successfully deployed.

```bash
  {
    "id" : "7bee8c23-f407-11e9-8e31-0750847c3ef4",
    "statusMessage" : "Deployed Successfully",
    "statusMessages" : [ "Deployed Successfully" ],
    "modifiedObjects" : {
      "URLObject" : [ "dummy_web_page" ]
    },
    "cliErrorMessage" : null,
    "queuedTime" : 1571664859268,
    "startTime" : 1571664859510,
    "endTime" : 1571664904616,
    "state" : "DEPLOYED",
    <... output omitted ...>
```

### Case Study: Synchronize Firepower Threat Defense Configuration with Local Configuration File

The following case study will show how you can use the FTD API to synchronize the FTD configuration with the local configuration file. The local configuration file will serve as a single source of truth.

You will synchronize URL objects. You will define the required URL objects in the local configuration file. You will then obtain the currently configured URL objects from FTD. The code will need to compare the current state with the local configuration file. If changes are needed to reach the desired state, you will use FTD API to perform the synchronization of the locally defined URLs. Finally, you will deploy the configuration.

You will use Python programming language for the configuration synchronization script.

The name of the file that you will use for your script will be config_sync.py. You will add the code to the file.

You will first add the code to obtain the authentication token from FTD.

The following function will be used to obtain the authentication token from FTD.

```python
  def get_token():

      url = f'https://{HOST}/api/fdm/v2/fdm/token'
      headers = { 'Content-Type': 'application/json', 'Accept': 'application/json' }
      body = {
          'grant_type': 'password',
          'username': f'{USERNAME}',
          'password': f'{PASSWORD}',
      }
      response = requests.post(url, verify=False, headers=headers, json=body)
      return response.json().get('access_token')
```

The function will use the /api/fdm/v2/fdm/token path to access the token resource. You need to define the body, which should be in the JSON syntax. The grant_type attribute must be set to password. You also need to send the username and password. You will use the Python requests module to send the POST request to FTD. Finally, you need to read the response to the access_token attribute. The function will return the access token.

You will define configuration variables like HOST, USERNAME, and PASSWORD on the top of the script in the configuration section.

The next step is to get the current configuration from FTD.

The get_urls function is used to retrieve all configured URL objects from FTD.

```python
  def get_urls(token):

      url = f'https://{HOST}/api/fdm/v2/object/urls'
      headers = {
          'Content-Type': 'application/json',
          'Accept': 'application/json',
          'Authorization': f'Bearer {token}'
      }
      response = requests.get(url, verify=False, headers=headers)
      return response.json()
```

The function accepts token as input and returns the response body as output. The function uses the /api/fdm/v2/object/urls path to access the URL objects. When you define HTTP headers, you also need to append the Authorization header, which includes the access token. Since you are reading the state from device, you will use the GET method.

Once you have the current FTD configuration, you need to read the local configuration file.

The read_config function reads the desired configuration from the configuration file.

```python
  def read_config():
      with open(CONFIG_FILE) as f:
          data = json.load(f)
      return data
```

The function uses the Python with sentence, which opens a file, and makes sure that the file is closed at the end. Inside the with sentence, the function uses the Python json module to read the file. The function returns the content of the file.

Next, you need to add two functions. The first function will delete the URL object from FTD. The second function will add the URL object.

The delete_url function deletes the URL object from FTD.

```python
  def delete_url(token, url):

      headers = {
          'Content-Type': 'application/json',
          'Accept': 'application/json',
          'Authorization': f'Bearer {token}'
      }
      response = requests.delete(url, verify=False, headers=headers)
```

The delete_url function accepts token and url parameters. You can get the exact URL for a particular URL object in the list of all URL objects that you receive with the get_urls function. To delete the URL object, use the DELETE HTTP method.

The add_url function adds the URL object to FTD.

```bash
  def add_url(token, url_string):

      url = url = f'https://{HOST}/api/fdm/v2/object/urls'
      headers = {
          'Content-Type': 'application/json',
          'Accept': 'application/json',
          'Authorization': f'Bearer {token}'
      }
      body = {
          'name': f'{url_string.replace(".", "_")}',
          'url': f'{url_string}',
          'type': 'urlobject'
      }
      response = requests.post(url, verify=False, headers=headers, json=body)
```

The add_url function accepts token and url_string parameters. The function uses the /api/fdm/v2/object/urls path. Inside the body you need to define three parameters: name, url, and type. The name will be the same as the URL, except the dots will be replaced with underscores. To add the URL object, you need to use the POST HTTP method.

Finally, add the code to deploy the configuration.

The deploy function deploys the configuration to FTD.

```python
  def deploy(token):

      url = f'https://{HOST}/api/fdm/v2/operational/deploy'
      headers = {
          'Content-Type': 'application/json',
          'Accept': 'application/json',
          'Authorization': f'Bearer {token}'
      }
      response = requests.post(url, verify=False, headers=headers)
```

The deploy function accepts the token parameter. It uses the /api/fdm/v2/operational/deploy path to request the deployment of the configuration.

Once you have all required functions, you can add a code for execution.

The following code is executed for synchronizing the configuration on Firepower Threat Defense.

```python
  if __name__ == "__main__":
      token = get_token()
      urls = get_urls(token)
      urls_config = read_config()
      for url in urls['items']:
          if url['url'] not in urls_config:
              delete_url(token, url['links']['self'])
          else:
              urls_config.remove(url['url'])
      for url in urls_config:
          add_url(token, url)
      deploy(token)
```

The code first requests the token from FTD. Once the token is received, the code retrieves the complete URL objects configuration from FTD. The next step is to read the desired URL objects from the local configuration file. The code then loops over all URL objects that are configured on FTD. If the URL object is not found in the list of desired URL objects, delete the URL object. Otherwise, remove the URL object from the list of the desired URL objects. Once the loop is finished, the code loops over the remaining desired URL objects. It adds each particular URL object to FTD. At the end, the code needs to deploy the configuration to FTD.

Now, you are ready to run the script. But before executing the script, you need to define the configuration file. Create a configuration file and add couple of URLs to the list.

An example of the configuration that will be pushed to FTD.

```bash
  [
    "www.cisco.com",
    "www.devnet.com",
    "www.example.com",
    "www.dummy.com"
  ]
```

Execute the script with the **python config_sync.py** command.

Once the script is executed, you can check the status on FTD. Log in to Firepower Threat Defense and navigate to Objects > URLs.

You should see the defined URL objects in the web user interface on FTD.

![](/images/devcor/devcor_10_10_9.png)

You can test if the synchronization script works correctly. You can remove some URLs from local configuration file and add new URLs. Then you can run the script again and verify the status on FTD.

![](/images/devcor/devcor_10_10_10.png)

You should see that changes in the local configuration file should be seen on FTD, which confirms that script is working as expected.

# 11 Modeling Data with YANG

## 11.1 Introduction

"Yet Another Next Generation" (YANG) is a formal contract language with rich syntax and semantics on which you can build applications. It provides these rich semantics that offer constraints, but it also provides reusable structures that can be used within and between YANG models. YANG has been around since 2010 but has been tightly coupled with the Network Configuration Protocol (NETCONF) management protocol. It can be seen by the name of RFC 6020 YANG, a data modeling language for NETCONF. More recently, YANG models are being used independently of NETCONF and for other protocols such as Representational State Transfer Configuration Protocol (RESTCONF) and gRPC.

## 11.2 YANG Overview

YANG is a full-blown language. For example, it is not used to express a process nor is it used for actual transport. A data model describes how data is represented and accessed. In YANG, data models are represented by definition hierarchies, which are called schema trees. Instances of schema trees are called data trees and are encoded in XML.

YANG is in fact protocol independent, although it was originally designed specifically for NETCONF. It is also being used with Representational State Transfer (REST) interfaces. In fact, the YANG model-driven interfaces that use REST are now called RESTCONF. YANG modules are for RESTCONF/NETCONF what Management Information Bases (MIBs) are for Simple Network Management Protocol (SNMP), with readability being its top priority.

The following are the main characteristics of YANG:

* Data modeling language defined in RFC 6020
* Analogous to XML schema and SMI for SNMP
* Models configuration, operational, and RPC data
* Provides semantics to better define data
* Extensible and modular (constraints, reusable structures, and built-in and derived types)
* Protocol independent

![](/images/devcor/devcor_11_2_1.png)

YANG data modeling is tree-structured. The original creators of YANG wanted YANG to be very close to the device instrumentation; a lot of time was spent with network operators and architects to ensure that any construct in YANG reflects what is running in both standard features (common protocols) and proprietary features.

### Modules and Submodules

The module is the base unit of definition in YANG. A module defines a single data model. A module can define a complete, cohesive model, or augment an existing data model with additional nodes. Submodules are partial modules that contribute definitions to a module. A module may include any number of submodules, but each submodule may belong to only one module. The names of all standard modules and submodules must be unique. It is recommended that developers of enterprise modules choose module names that have a low probability of colliding with standard or other enterprise modules—for example, by using the enterprise or organization name as a prefix for the module name.

A module uses the include statement to include its submodules and the import statement to reference external modules. Similarly, a submodule uses the import statement to reference other modules and uses the include statement to reference other submodules within its module. A module or submodule must not include submodules from other modules, and a submodule must not import its own module.

YANG model hierarchy guidelines:

* Imported fragments are just referenced, not included.
* Modules are used to group reusable definitions that can be reused by many other modules.
* Modules are similar to libraries in programming languages.
* Module and submodules share the same namespace.
* A submodule can never be included in any other module.
* Submodules are useful to split large modules into smaller, more manageable files.

![](/images/devcor/devcor_11_2_2.png)

The import and include statements are used to make definitions available to other modules and submodules:

* For a module or submodule to reference definitions in an external module, the external module must be imported.
* For a module to reference definitions in one of its submodules, the module must include the submodule.
* For a submodule to reference definitions in a second submodule of the same module, the first submodule must include the second submodule.

### Module Structure

A module has a specific structure. In terms of structure, there is a lot that is borrowed from MIBs.

![](/images/devcor/devcor_11_2_3.png)

![](/images/devcor/devcor_11_2_4.png)

Each module must have the following sections:

* **Module name:** Must be the same as the module’s filename. Case-sensitive equality between module name and filename prevents a warning message when validating a module.
* **Header information:** Describes the module and gives information about the module itself:
  * **Namespace (mandatory):** Defines the Uniform Resource Identifier (URI) of the XML namespace.
  * **Prefix (mandatory):** The prefix statement is used to define the prefix that is associated with the module and its namespace. The prefix statement’s argument is the prefix string that is used as a prefix to access a module.
  * **Organization, contact (optional):** Describes the module origin (such as company and author).
  * **Description (optional):** Provides a description of the module (what it is, what it does, and so on).
* **Revision information:** Provides versioning of the module and facilitates the importing of modules based on revisions.
* **Imports and includes:** The import and include statements are used to make definitions available to other modules and submodules.
* **Type definitions:** In addition to built-in data types, custom types can be defined and later used in the data models.
* **Reusable node declarations:** In addition to type definitions, a more complex set of YANG code can be defined and later reused.
* **Configuration and operational data declarations:** This is the main part of the module where the actual data model is defined.
* **RPC, action, and notification declarations:** Defines NETCONF Remote Procedure Calls (RPCs) and notifications to be used within the module.

### Module Header

The first part of a module is to understand the header information. The revision statement is important to understand and is heavily used in YANG. It is supposed to be reflected in the filename of a YANG model. In this case, you can see that this module is named acme-module and it is a revision 2007-06-09, thus this file is supposed to be called acme-module@2007-06-09.yang. It allows you to see the date of the last revision directly in the filename.

The module header definition of the acme-module@2007-06-09.yang

```bash
  module acme-module  {
      namespace "http://acme.example.com/acme-module";
      prefix acm;

      import "ietf-yang-types"  { prefix yang; }
      include "acme-system";

      organization "ACME Inc.";
      contact joe@acme.example.com;
      description "Module describing the ACME products";
      revision 2007-06-09  {
          description "Initial revision.";
      }
  <... output omitted ...>
  }
```

Another important aspect is that you make heavy use of namespaces, which is borrowed from the XML world. The acme-module namespace here (http://acme.example.com/acme-module) is a string that is supposed to be globally unique to this module, combined with its version, so it should allow people to reference this module uniquely using a namespace.

### Data Types

Just like programming languages have standard data types, such as strings and integers, so does YANG. Base types are part of the YANG language itself. Derived types are something where users would start with base types and then derive things out of the base types. Of course, there are several basic YANG types for networking, such as IPv4 addresses, fully qualified domain names (FQDN), and IPv6 addresses.

The YANG model can use the following sources for data types:

* Built-in data types: YANG has a set of built-in types, similar to those of many programming languages, but with some differences due to special requirements from the management domain.
* Common data types: RFC 6991 defines additional data types (IETF YANG data types and Inet data types).
* Derived data types: Derived (custom) data types can be defined as part of the data model, or they may be available by importing other modules that contain additional data type definitions. For example, Cisco Network Services Orchestrator (NSO) comes with a set of additional data types that are useful in describing device or service configurations.

### Built-in Data Types

The following table lists the built-in data types as defined in RFC 6020.

| Type                | Description                 |
|---------------------|-----------------------------|
| int8/16/32/64       | Integer                     |
| uint8/16/32/64      | Unsigned integer            |
| decimal64           | Noninteger                  |
| string              | Unicode string              |
| enumeration         | Set of alternatives         |
| boolean             | True of false               |
| bits                | Boolean array               |
| binary              | Binary BLOB                 |
| leafref             | Reference "pointer"         |
| identityref         | Unique identity             |
| empty               | No value, void              |
| union               | Choice of member types      |
| instance-identifier | References a data tree node |

To reference built-in data types, use:

```bash
  type uint32;
```

### Common Data Types

The following table lists some of the common data types (IETF YANG data types) that are defined in RFC 6991.

| Type                    | Description                                                                                    |
|-------------------------|------------------------------------------------------------------------------------------------|
| counter32/64            | Non-negative 32/64-bit integer that monotonically increases                                    |
| zero-based-counter32/64 | A counter32/64 that has the defined initial value zero                                         |
| gauge32/64              | Non-negative integer, which may increase or decrease                                           |
| data-and-time           | ISO 8601 standard for representation of dates and times                                        |
| timeticks               | TimeTicks (SNMPv2-SMI)                                                                         |
| time stamp              | Time stamp (SNMPv2-TC)                                                                         |
| phys-address            | Colon-separated hexadecimal pairs (for example, 1a:ba:da:ba:d0) PhysAddress (SNMPv2-TC)        |
| mac-address             | Six colon-separated hexadecimal pairs (for example, 1a:ba:da:ba:d0:00) MAC address (SNMPv2-TC) |
| xpath1.0                | XPath 1.0 expression                                                                           |
| hex-string              | Colon-separated hexadecimal pairs of arbitrary length                                          |
| uuid                    | Universally unique identifier (RFC 4122)                                                       |

First, you need to import the IETF YANG data types module:

```bash
  import ietf-yang-types { prefix yang; }
```

To reference IETF YANG data types, use the following:

```bash
  type yang:counter64;
```

The following table lists some of the common data types (IETF Inet data types) that are defined in RFC 6991.

| Type            | Description                                             |
|-----------------|---------------------------------------------------------|
| ip-version      | IP protocol version: 1=IPv4, 2=IPv6, 0=unknown          |
| dscp            | Differentiated Services Code Point valueL 0 to 63       |
| ipv6-flow-label | 32-bit integer in the range from 0 to 1048575           |
| port-number     | 16-bit integer in the range from 0 to 65535             |
| as-number       | 32-bit integer representing 2 or 4 octet BGP AS numbers |
| ip-address      | IPv4 or IPv6 address                                    |
| ipv4-address    | IPv4 address (for example, 10.1.2.3)                    |
| ipv6-address    | IPv6 address (for example, fd85:b310:6513:194b::1)      |
| ip-prefix       | IPv4 or IPv6 prefix                                     |
| ip4-prefix      | IPv4 prefix (for example, 10.1.2.0/24)                  |
| ip6-prefix      | IPv6 prefix (for example, fd85:b310:6513:194b::/64)     |
| domain-name     | DNS domain name                                         |
| host            | IP address or DNS domain name                           |
| uri             | Uniform Resource Identifier                             |


First, you need to import IETF Inet data types module:

```bash
  import ietf-inet-types { prefix inet; }
```

To reference IETF Inet data types, use the following:

```bash
  type inet:ip-address;
```

### Derived Data Types

YANG also provides a mechanism through which additional data types may be defined; they are called derived data types. Except for built-in data types, all other data types are derived. A derived type inherits base type substatements, which are restrictions to limit the value of the stored data for the specific data type. There is a simple rule: restrictions of the derived type must be the same as or stricter than for the base type.

In general, you can divide restrictions in two categories:

* **Numeric restrictions:** range and fraction digits substatements
* **String restrictions:** length and pattern substatements

### Typedef Statement

The typedef statement defines a new data type (derived data type), which can be used inside the module or submodule. It can be imported to other modules according to the import rules. The type from which you are deriving a new type is called the base type. The type statement of the typedef statement defines the base type for the derived type.

Four substatements of the type statement:

* **Range statement:** Takes as an argument a range expression string. It is used to restrict integer and decimal built-in types, or types that are derived from those. A range consists of an explicit value, or a lower-inclusive bound, two consecutive dots (..), and an upper-inclusive bound. Multiple values or ranges can be given, separated by a pipe (|). The min and max keywords refer to the minimum and maximum values accepted for the type being restricted, respectively.

* **Length statement:** Takes as an argument a length expression string. It is used to restrict the built-in type string, or types derived from string. A length statement restricts the number of Unicode characters in the string. A length range consists of an explicit value, or a lower bound, two consecutive dots (..), and an upper bound. Multiple values or ranges can be given, separated by a pipe (|). The min and max refer to the minimum and maximum length accepted for the type being restricted, respectively.

* **Pattern statement:** Takes as an argument a regular expression string, as defined in [XSD-TYPES]. It is used to restrict the built-in type string, or types derived from string, to values that match the pattern. If the type has multiple pattern statements, the expressions are ANDed together—that is, all such expressions must match. If a pattern restriction is applied to an already pattern-restricted type, values must match all patterns in the base type, in addition to the new patterns.

* **Fraction-digits statement:** Must be present if the type is decimal64. It takes as an argument an integer between 1 and 18, inclusively. It controls the size of the minimum difference between values of a decimal64 type, by restricting the value space to numbers that are expressible as "i x 10^-n", where n is the fraction-digits argument.

In the following example, you are constructing derived data types, which are representing three notions of the access control list (ACL), with corresponding restriction for each of it. For the base type, you can also use a previously defined derived type.

```bash
  typedef acl-num-type {
    type int32 {
      range "1..199 | 1300..2699";
    }
  }

  typedef std-acl-num-type {
    type acl-num-type {
      range "min..99 | 1300..1999";
    }
  }

  typedef ext-acl-num-type {
    type acl-num-type {
      range "100..199 | 2000..max";
    }
  }

  typedef ext-acl-name-type {
    type string {
      length "1..50";
      pattern "[a-zA-Z][a-zA-Z0-9-_]*";
    }
  }
```

The following example represents the usage of the fraction-digits substatement.

```bash
  typedef const-type {
    type decimal64 {
      fraction-digits 4;
      range "2.7182 .. 3.1415";
    }
  }
```

In the following list, you have two YANG built-in types, which are substatements to the type statement. They allow you to construct the combined derived types:

* **Enumeration built-in type:** Represents values from a set of assigned names. An enumeration cannot be restricted. The enum statement, which is a substatement to the type statement, must be present if the type is enumeration. It is repeatedly used to specify each assigned name of an enumeration type. It takes as an argument a string, which is the assigned name. The string must not be empty and must not have any leading or trailing whitespace characters.

* **Union built-in type:** Represents a value that corresponds to one of its member types. When the type is union, the type statement must be present. It is used to repeatedly specify each member type of the union. It takes as an argument a string that is the name of a member type. A member type can be of any built-in or derived type, except that it must not be one of the built-in types empty or leafref. When a string representing a union data type is validated, the string is validated against each member type, in the order in which they are specified in the type statement, until a match is found. Any default value or units that are property defined in the member types are not inherited by the union type.

In the first output, you can observe the definition of the acl-id-type data type. This type is able to store the string value for all notations of the ACL. The second output demonstrates the usage of the union statement in a combination with the enum statement.

```bash
  typedef acl-id-type {
    type union {
      type std-acl-num-type;
      type ext-acl-num-type;
      type ext-acl-name-type;
    }
  }

  typedef intf-ip-addr-type {
    type union {
      type inet:ip-address;
      type enumeration {
        enum "none";
        enum "unnumbered";
        enum "dhcp";
      }
    }
  }
```

## 11.3 XPath Query Language

XPath is an XML Path Language that is defined by the World Wide Web Consortium (W3C). It uses expressions to reference or extract parts of XML documents. It also contains several useful functions for nodes, numbers, and strings. Although there are newer versions of XPath, YANG uses XPath version 1.0. XPath is often used with leafrefs when referencing Leaf or Leaf-List values elsewhere in the data tree, with when and must statements to add constraints to data model definitions.

Expressions address an XML document and return a result that can be of one of the following type categories:

* **Node set:** multiple elements extracted from the XML document
* **String:** a single string element
* **Number:** a single number element
* **Boolean:** true or false

![](/images/devcor/devcor_11_3_1.JPG)

The most common path expressions are the following:

![](/images/devcor/devcor_11_3_2.JPG)

The following XML source data will be used to demonstrate the usage of the XPath expressions.

```xml
  <Loopback>
    <name>0</name>
    <ip>
      <address>
        <primary>
          <address>10.1.1.17</address>
          <mask>255.255.255.255</mask>
        </primary>
      </address>
    </ip>
  </Loopback>
  <GigabitEthernet>
    <name>0/1</name>
    <ip>
      <address>
        <primary>
          <address>10.1.2.1</address>
          <mask>255.255.255.252</mask>
        </primary>
      </address>
    </ip>
  </GigabitEthernet>
  <GigabitEthernet>
    <name>0/2</name>
    <ip>
      <address>
        <primary>
          <address>10.1.2.5</address>
          <mask>255.255.255.252</mask>
        </primary>
      </address>
    </ip>
  </GigabitEthernet>
  <... output omitted ...>
```

The example extracts IP addresses from all interfaces that are taken from the configuration of a Cisco IOS router encoded in the XML format, with the following XPath expression:

```bash
  /*/ip/address/primary/address
      or
  //primary/address
```

In the following output, you can see the result of the XPath expression:

```bash
  <address>10.1.1.17</address>
  <address>10.1.2.1</address>
  <address>10.1.2.5</address>
  <address>10.1.2.9</address>
  <address>10.1.2.13</address>
  <address>10.1.3.1</address>
  <address>10.1.3.5</address>
  <address>10.1.3.9</address>
  <address>10.1.3.13</address>
```

The figure illustrates how you now only filter out the GigabitEthernet interface nodes, so you do not get the Loopback IP address in the result set.

```bash
  //GigabitEthernet//primary/address
```

In the following output, you can see a result of the XPath expression:

```bash
  <address>10.1.2.1</address>
  <address>10.1.2.5</address>
  <address>10.1.2.9</address>
  <address>10.1.2.13</address>
  <address>10.1.3.1</address>
  <address>10.1.3.5</address>
  <address>10.1.3.9</address>
  <address>10.1.3.13</address>
```

The next example further filters the result by displaying only the IP addresses of GigabitEthernet interface nodes that represent line card 0 on the router. You can match those nodes of line card 0 by looking for those Gigabit Ethernet interface nodes that start with (0).

```bash
  //GigabitEthernet[starts-with(name,’0’)]//primary/address
  ```

In the following output, you can see a result of the XPath expression:

```bash
  <address>10.1.2.1</address>
  <address>10.1.2.5</address>
  <address>10.1.2.9</address>
  <address>10.1.2.13</address>
```

You can filter the results down to a single result by using a positional function or a counter, as shown in the example below.

```bash
  //GigabitEthernet[position()=1]//primary/address
      or
  //GigabitEthernet[1]//primary/address
```

The following output is a result of the XPath expression.

```bash
  <address>10.1.2.1</address>
```

## 11.4 YANG Language Syntax

The most basic and commonly used statements when building a data model:

* The leaf statement represents the simplest piece of information—a single value of a specific type. This concept is similar to scalar variables in programming languages.

* The leaf-list statement represents a list of values (zero or more) of the same type. This concept is similar to an array of scalar variables in programming languages

* The container statement represents a more relaxed structure that can contain various other types of statements. The container itself does not hold any value and hence does not have any type. This is similar to a record definition in programming languages.

* The list statement represents a list of records (zero or more). This is similar to an array of records in programming languages.

* The leafref built-in type points to the actual data elsewhere in the data model. This is similar to the symbolic link in the Linux file system.

### Leaf Statement

The leaf statement is used to define a leaf node in the schema tree. It takes one argument, which is an identifier, followed by a block of substatements that holds detailed leaf information. A leaf node has a value but no child nodes in the data tree. A leaf node exists in zero or one instance in the data tree. The leaf statement is used to define a scalar variable of a particular built-in or derived type.

In the example, you see a leaf value of the type int32 that is limited to a range between 0 and 2147483647. These values are the valid numbers in Cisco IOS to number loopback interfaces.

The following figure illustrates loopback with the ID: 1

![](/images/devcor/devcor_11_4_1.png)

1.  The output shows the YANG model.
   
   ```bash
    leaf loopback {
      type int32 {
        range "0..2147483647";
      }
    }
   ```
2.  The output shows the XML data representation.

    ```bash
      <loopback>1</loopback>
    ```

3.  The output shows the JSON data representation.

    ```bash
      {
        "loopback": 1
      }
    ```

4.  The output shows the XPath expression to request the data.

    ```bash
      /loopback
    ```
![](/images/devcor/devcor_11_4_2.JPG)

The following list describes the Leaf optional substatements; only the type substatements is required for a Leaf definition:

* **config:** Takes as an argument the string true or false. If config is true, the definition represents configuration. Data nodes representing configuration will be part of the reply to a <get-config> request, and can be sent in a <copy-config> or <edit-config> request. If config is false, the definition represents state data. Data nodes representing state data will be part of the reply to a <get> but not to a <get-config> request, and cannot be sent in a <copy-config> or <edit-config> request. If config is not specified, the default is the same as the parent schema node’s config value. If the parent node is a case node, the value is the same as the case node’s parent choice node. If the top node does not specify a config statement, the default is true. If a node has config set to false, no node underneath it can have config set to true.

* **default:** Takes as an argument a string that contains a default value for the leaf. The value of the default statement must be valid according to the type specified in the leaf’s type statement.

* **mandatory:** Takes as an argument the string true or false and puts a constraint on valid data. If not specified, the default is false. If mandatory is true, the behavior of the constraint depends on the type of the leaf’s closest ancestor node in the schema tree that is not a nonpresence container.

* **must:** Takes as an argument a string that contains an XPath expression. It is used to formally declare a constraint on valid data. When a data store is validated, all must constraints are conceptually evaluated once for each data node in the data tree, and for all leafs with default values in use. If a data node does not exist in the data tree, and it does not have a default value, its must statements are not evaluated. All such constraints must evaluate to true for the data to be valid.

* **type:** The built-in or derived data type, as described earlier, and is required.

* **when:** Makes its parent data definition statement conditional. The node defined by the parent data definition statement is valid only when the condition specified by the when statement is satisfied. The statement’s argument is an XPath expression, which is used to formally specify this condition. If the XPath expression conceptually evaluates to true for a certain instance, the node defined by the parent data definition statement is valid; otherwise, it is not.

* **description:** Takes as an argument a string that contains a human-readable textual description of this definition. The text is provided in a language (or languages) chosen by the module developer. For the sake of interoperability, it is recommended that you choose a language that is widely understood among the community of network administrators who will use the module.

* **reference:** Takes as an argument a string that is used to specify a textual cross-reference to an external document, either another module that defines related management information or a document that provides additional information relevant to this definition.

* **units:** Takes as an argument a string that contains a textual definition of the units associated with the type.

* **status:** Takes as an argument one of the strings current, deprecated, or obsolete. The argument current means that the definition is current and valid. The argument deprecated indicates an obsolete definition, but it permits new or continued implementation to foster interoperability with older or existing implementations. The argument obsolete means that the definition is obsolete and should not be implemented and/or can be removed from implementations. If no status is specified, the default is current. If a definition is current, it must not reference a deprecated or obsolete definition within the same module. If a definition is deprecated, it must not reference an obsolete definition within the same module.

### Leaf-List Statement

Where the leaf statement is used to define a simple scalar variable of a particular type, the leaf‑list statement is used to define an array of a particular type. The leaf-list statement takes one argument, which is an identifier, followed by a block of substatements that holds detailed leaf-list information. The values in a leaf-list must be unique.

In the example, you see a list of leaf values of the type int32 that are limited to a range between 0 and 2147483647.

The following figure illustrates four loopback IDs: 1, 2, 101, and 102.

![](/images/devcor/devcor_11_4_3.png)

The following output shows the YANG model.

```bash
  leaf-list loopback {
    type int32 {
      range "0..2147483647";
    }
  }
```

The following output shows the XML data representation.

```bash
  <loopback>1</loopback>
  <loopback>2</loopback>
  <loopback>101</loopback>
  <loopback>102</loopback>
```

The following output shows the JSON data representation.

```bash
  {
    "loopback": [
      1,
      2,
      101,
      102
    ]
  }
```

The following output shows the XPath expression to request the data.

```bash
  /loopback
```

### Container Statement

The container statement is used to define an interior data node in the schema tree. It takes one argument, which is an identifier, followed by a block of substatements that holds detailed container information. A container node does not have a value, but it has a list of child nodes in the data tree. The child nodes are defined in the container’s substatements.

In the example, you see an expansion to the previous example, where you define two parameters to represent a loopback interface:

* Loopback interface number
* Loopback interface’s IP address

The following figure shows one instance of the container that describes loopback1 having IP address 10.1.1.1.

![](/images/devcor/devcor_11_4_4.png)

The output shows the YANG model.

```bash
  container loopback-ipv4 {
    leaf loopback {
      type int32 {
        range "0..2147483647";
      }
    }
    leaf ip-address {
      type inet:ipv4-address;
    }
  }
```

The output shows the XML data representation.

```bash
  <loopback-ipv4>
    <loopback>1</loopback>
    <ip-address>10.1.1.1</ip-address>
  </loopback-ipv4>
```

The output shows the JSON data representation.

```bash
  {
    "loopback-ipv4": [
      {
        "loopback": 1,
        "ip-address": "10.1.1.1"
      }
    ]
  }
```

The output shows the XPath expression to request the data.

```bash
  /loopback-ipv4
  /loopback-ipv4/loopback
  /loopback-ipv4/ip-address
```

### List Statements

The list statement is used to define an interior data node in the schema tree. A list node may exist in multiple instances in the data tree. Each such instance is known as a list entry. The list statement takes one argument, which is an identifier, followed by a block of substatements that holds detailed list information. A list entry is uniquely identified by the values of the list’s keys, if defined.

The key statement, which must be present if the list represents configuration, and may be present otherwise, takes as an argument a string that specifies a space-separated list of leaf identifiers of this list. A leaf identifier must not appear more than once in the key. Each such leaf identifier must refer to a child leaf of the list. The leafs can be defined directly in substatements to the list, or in groupings used in the list. A leaf that is part of the key can be of any built-in or derived type, except that it must not be the built-in type empty.

The example illustrates a similar setup as in the previous example using the container statement. This time the list statement enables many instances of loopback data where uniqueness requires only the loopback number to be unique (that is, IP addresses do not have to be unique).

![](/images/devcor/devcor_11_4_5.png)


The output shows the YANG model.

```bash
  list loopback-ipv4 {
    key loopback;
    unique loopback;
    leaf loopback {
      type int32 {
        range "0..2147483647";
      }
    }
    leaf ip-address {
      type inet:ipv4-address;
    }
  }
```

The output shows the XML data representation.

```bash
  <loopback-ipv4>
    <loopback>1</loopback>
    <ip-address>10.1.1.1</ip-address>
  </loopback-ipv4>
  <loopback-ipv4>
    <loopback>2</loopback>
    <ip-address>10.2.2.2</ip-address>
  </loopback-ipv4>
```

The output shows the JSON data representation.

```bash
  {
    "loopback-ipv4": [
      {
        "loopback": 1,
        "ip-address": "10.1.1.1"
      },
      {
        "loopback": 2,
        "ip-address": "10.2.2.2"
      }
    ]
  }
```

The output shows the XPath expression to request the data.

```bash
  /loopback-ipv4
  /loopback-ipv4/loopback
  /loopback-ipv4/ip-address

  /loopback-ipv4/[loopback='1']/ip-address = 10.1.1.1
```

Shown in the list are substatements, which apply to the list and leaf-list statements:

* **Max-elements:** Maximum number of elements in the list. If max-elements is not specified, there is no upper limit—that is, unbounded.

* **Min-elements:** Minimum number of elements in the list. If min-elements is not specified, there is no lower limit—that is, 0.

* **Ordered-by:** List entries are sorted by system or user. System means that elements are sorted in a natural order (numerically, alphabetically, and so on). “User means that the order in which the operator entered them is preserved. "ordered-by user" is meaningful when the order among the elements has significance—for example, a DNS server search order or firewall rules.

The combined values of all the leafs specified in the key are used to uniquely identify a list entry. All key leafs must be given values when a list entry is created. Thus, any default values in the key leafs or their types are ignored. It also implies that any mandatory statement in the key leafs is ignored.

This example shows how key combinations can be used to define more complex list entry identifiers. The prefix and prefix length combination must be unique.

The output shows combined key declaration in data model.

```bash
  list route {
    key "prefix length";
    ...
  }
```

The output shows the XML data representation.

```xml
  <route>
    <prefix>192.0.2.0</prefix>
    <length>24</length>
    <next-hop>10.100.1.2</next-hop>
    <metric>20</metric>
  </route>
  <route>
    <prefix>198.51.100.0</prefix>
    <length>24</length>
    <next-hop>192.168.31.5</next-hop>
    <metric>40</metric>
  </route>
  <route>
    <prefix>198.51.100.0</prefix>
    <length>25</length>
    <next-hop>10.100.1.2</next-hop>
    <metric>30</metric>
  </route>
  <route>
    <prefix>203.0.113.0</prefix>
    <length>24</length>
    <next-hop>172.31.11.1</next-hop>
    <metric>30</metric>
  </route>
```

The output shows the JSON data representation.

```json
  {
    "route": [
      {
        "prefix": "192.0.2.0",
        "length": 24,
        "next-hop": "10.100.1.2",
        "metric": 20
      },
      {
        "prefix": "198.51.100.0",
        "length": 24,
        "next-hop": "192.168.31.5",
        "metric": 40
      },
      {
        "prefix": "198.51.100.0",
        "length": 25,
        "next-hop": 10.100.1.2,
        "metric": 30
      },
      {
        "prefix": "203.0.113.0",
        "length": 24,
        "next-hop": "172.31.11.1",
        "metric": 30
      }
    ]
  }
```

The XPath output shows how data can be referenced using the combined key values.

```bash
  /route/
  /route/prefix
  /route/length
  /route/next-hop
  /route/metric

  /route [prefix='198.51.100.0']/length                          = 24, 25
  /route [prefix='198.51.100.0'][length='24']/next-hop  = 192.168.31.5
  /route [prefix='198.51.100.0'][length='24']/metric      =  40
```

## 11.5 Data Model Modularity

Among the most important aspects when you are modeling a large data model are code reusability and flexibility. These two common statements help you to achieve that goal:

* The **grouping** statement is used to create reusable chunks of data model, which can be reused later in a module or a submodule.

* The **leafref** built-in type points to the actual data elsewhere in the data model. This is similar to the symbolic link in the Linux file system.

### Grouping Statement

Groups of nodes can be assembled into reusable collections with the use of the grouping statement. A grouping defines a set of nodes that are instantiated with the uses statement.

![](/images/devcor/devcor_11_5_1.png)

The example illustrates the usage of a template (grouping) that is then used twice to describe the IP and TCP port used for two services. Both services further refine the template by adding their default TCP port values

The output shows the YANG model.

```bash
  grouping app {
    leaf address {
      type inet:ip-address;
    }
    leaf port {
      type inet:port-number;
    }
  }

  container http-server {
    uses app {
      refine port {
        default 80;
      }
    }
  }

  container smtp-server {
    uses app {
      refine port {
        default 25;
      }
    }
  }
```

The output shows the XML data representation.

```xml
  <http-server>
    <address>10.1.1.1</address>
    <port>80</port>
  </http-server>
  <smtp-server>
    <address>10.1.2.1</address>
    <port>25</port>
  </smtp-server>
```

The output shows the JSON data representation.`

```json
  {
    "http-server": [
      {
        "address": "10.1.1.1",
        "port": 80
      }
    ],
    "smtp-server": [
      {
        "address": "10.1.2.1",
        "port": 25
      }
    ]
  }
```

The XPath output shows how data can be referenced using the combined key values.

```bash
  /http-server
  /http-server/address
  /http-server/port
  /smtp-server
  /smtp-server/address
  /smtp-server/port
```

### Leafref Built-in Type

The leafref type is used to reference a particular leaf instance in the data tree. The path substatement selects a set of leaf instances, and the leafref value space is the set of values of these leaf instances. If the leaf with the leafref type represents configuration data, the leaf that it refers to must also represent configuration. Such a leaf puts a constraint on valid data. All leafref nodes must reference existing leaf instances or leafs with default values in use for the data to be valid. There must not be any circular chains of leafrefs. The path keyword is used to match the position in the tree, with one or more keys referring to a leaf-list or leafs within a list (all key leafs must be referenced).

The example illustrates how the app container references a specific value in the service list based on the combined key using the ip and port leafs.

![](/images/devcor/devcor_11_5_2.png)

The output shows the YANG model.

```bash
  list services {
    key "ip port"
    leaf ip {
      type inet:ipv4-address; 
    }
    leaf port {
      type uint16; 
    }
  }

  container app {
    leaf address { 
      type leafref {
        path "/services/ip";
      }
    }
    leaf port { 
      type leafref {
        path "/services[ip=current()/../address]/port";
      }
    }
  }
```

The output shows the XML data representation of the stored values for the services list. Currently there is no data stored in the app container.

```bash
  <services>
    <ip>192.0.2.213</ip>
    <port>16772</port>
  </services>
  <services>
    <ip>198.51.100.22</ip>
    <port>19234</port>
  </services>
  <services>
    <ip>203.0.113.89</ip>
    <port>22315</port>
  </services>
```

The output shows the JSON data representation of the stored values for the services list. Currently there is no data stored in the app container.

```json
  {
    "services": [
      {
        "ip": "192.0.2.213",
        "port": 16772
      },
      {
        "ip": "198.51.100.22",
        "port": 19234
      },
      {
        "ip": "203.0.113.89",
        "port": 22315
      }
    ]
  }
```

The output shows the XPath data representation.

```bash
  /services
  /services/ip
  /services/port
  /app
  /app/address
  /app/port
```

The address leaf is defined with the path "/services/ip" XPath expression. The result of the expression is a node set of values 192.0.2.213, 198.51.100.22 and 203.0.113.89. These values are only valid values for an address leaf in an app container. The address is populated with the"198.51.100.22" value.

The output shows the XML data representation of the stored values for the app list.

```xml
  <app>
    <address>198.51.100.22</address>
  </app>
```

The output shows the JSON data representation of the stored values for the app list.

```json
  {
    "app": [ 
      { 
        "address": "198.51.100.22" 
      } 
    ] 
  }
```

The port leaf is defined with the XPath expression ***path ""/services[ip=current()/../address]/port"***. If you decode the expression, which uses current() XPath function, it translates to the following XPath expression ***/services[ip=198.51.100.22]/port***. The result of the expression is a value 19234, and this value is only valid for a port leaf in an app container.

The output shows the XML data representation of the stored values for the app list.

```xml
  <app>
    <address>198.51.100.22</address>
    <port>19234</port>
  </app>
```

The output shows the JSON data representation of the stored values for the app list.

```json
  {
    "app": [ 
      { 
        "address": "198.51.100.22", 
        "port": 19234 
      } 
    ] 
  }
```

### Cisco IOS XE Data Model

The GitHub repository https://github.com/YangModels/yang/tree/master/vendor/cisco/xe is the best starting point to explore the Cisco IOS XE YANG data model. In the following examples, you will use the data model for the software version 16.9.1, which is located in the location https://github.com/YangModels/yang/tree/master/vendor/cisco/xe/1691.

For the navigation over the YANG data model, XPath path is used. As you already know from the RESTCONF, the URI for the Cisco IOS XE full device configuration is ***https://172.16.1.57:443/restconf/data/native.*** The following snippet shows the JSON RESTCONF replay from the device.

```bash
  {
    "Cisco-IOS-XE-native:native": {
      "version": "16.9",

  <... output omitted ...>

      },
      "interface": {
        "GigabitEthernet": [

  <... output omitted ...>

  }
```

The construct in the second line of the output gives as information about YANG module ***Cisco-IOS-XE-native***. You should look in the Cisco-IOS-XE-native.yang for the ***native/interface/GigabitEthernet*** definition.

```bash
  module Cisco-IOS-XE-native {
    namespace "http://cisco.com/ns/yang/Cisco-IOS-XE-native";
    prefix ios;

    import ietf-inet-types {
      prefix inet;
    }


    import Cisco-IOS-XE-types {
      prefix ios-types;
    }

    import Cisco-IOS-XE-features {
      prefix ios-features;
    }

    import Cisco-IOS-XE-interface-common {
      prefix ios-ifc;
    }

    include Cisco-IOS-XE-parser;
    include Cisco-IOS-XE-license;
    include Cisco-IOS-XE-line;
    include Cisco-IOS-XE-logging;
    include Cisco-IOS-XE-ip;
    include Cisco-IOS-XE-ipv6;
    include Cisco-IOS-XE-interfaces;

    organization
      "Cisco Systems, Inc.";

  <... output omitted ...>

    container native {

  <... output omitted ...>

      /////////////////////////////////////////////////////////
      // native / interface
      /////////////////////////////////////////////////////////
      uses config-interface-grouping;

  <... output omitted ...>

    } // native
  }
```

As you can establish from the output, the grouping statements define the structure for the interface. Because you do not have the definition for the config-interface-grouping in the Cisco-IOS-XE-native.yang, it means that it must be defined in one of the included submodules. If the structure is imported ***prefix:structure*** notation is used. The best candidate for the definition is the submodule ***Cisco-IOS-XE-interfaces***. The following snippet shows the ***Cisco-IOS-XE-interfaces.yang*** data model content.

```bash
  submodule Cisco-IOS-XE-interfaces {
    belongs-to Cisco-IOS-XE-native {
      prefix ios;
    }

    import ietf-inet-types {
      prefix inet;
    }


    import Cisco-IOS-XE-types {
      prefix ios-types;
    }

    import Cisco-IOS-XE-features {
      prefix ios-features;
    }

    import Cisco-IOS-XE-interface-common {
      prefix ios-ifc;
    }

    organization
      "Cisco Systems, Inc.";

  <... output omitted ...>

  /////////////////////////////////////////////////////////
    // native / interface
    /////////////////////////////////////////////////////////
    grouping config-interface-grouping {
      container interface { 
        description
          "Configure Interfaces";

  <... output omitted ...>

        // interface GigabitEthernet
        list GigabitEthernet {
          description
            "GigabitEthernet IEEE 802.3z";
          key "name";
          leaf name {
            type string;
          }
          leaf media-type {
            description
              "Media type";
            type enumeration {
              enum "auto-select";
              enum "rj45";
              enum "sfp";
            }
          }
          leaf port-type {
            type enumeration {
              enum "nni";
            }
          }
          uses interface-common-grouping;
          uses interface-switchport-grouping;
        }
        
  <... output omitted ...>

      }
    }
  }
```

### Backward Compatibility

Over the time the data model will evolve. To control backward compatibility, there are two main concepts:
* Import and include revision
* Status statement

### Import and Include Revision

To allow evolution of the data models, modules need to be imported with the revision information (revision-date). When the newer version of the data model is created, the revision statement must be updated. The existing module, which imports the module with revision-date is unaffected. When you are prepared to implement the most recent version of the data model, the import revision-date statement is updated.

In the snippet below, you can see an example of the import revision statement.

```bash
  module a {
      revision 2019-01-01 { ... }
      grouping a {
          leaf eh { .... }
      }
  }

  module b {
      import a {
          prefix p;
              revision-date 2019-01-01;
      }
  }
```

### Status Statement

The status statement takes as an argument one of the strings:

* **Current:** definition is current and valid (default)
* **Deprecated:** obsolete definition, but it permits new/continued implementation to foster interoperability with older/existing implementations
* **Obsolete:** definition is obsolete and SHOULD NOT be implemented and/or can be removed from implementations

```bash
  typedef my-type {
      status deprecated;
      type int32;
  }
```

## 11.6 Network Configuration Using RESTCONF

HTTP-based RESTCONF provides a programmatic interface based on standard mechanisms for accessing configuration data, state data, data model-specific RPC operations and events, defined in the YANG model.

RESTCONF offers the following characteristics:

* Functional subset of NETCONF
* Exposes YANG models via a REST API (URL)
* Uses HTTP(S) as transport
* Uses XML or JSON for encoding
* Developed to use HTTP tools and programming libraries
* Uses common HTTP verbs in REST APIs

![](/images/devcor/devcor_11_6_1.png)

RESTCONF is just using REST principles and therefore uses HTTP verbs appropriately. The most important operation to take note of is the use of the PUT operation. The PUT operation has the ability to replace entire sections of a configuration that is based on what you send. It is analogous to declarative network configuration. For example, if you PATCH one static route, it will add the route. If you PUT the route, you will end up with just one route configured.

![](/images/devcor/devcor_11_6_2.png)

A RESTCONF operation is derived from the HTTP method and the request URI, using the following conceptual fields:

* **Operations:** derived from HTTP Methods
* **Request headers:** optional request headers that are of interest because some functionality is obtained by their use:
  * **Authorization** value consists of credentials containing the authentication information of the user agent for the realm of the resource being requested. Usually, but not necessarily, after receiving a 401 (Unauthorized) response.
  * **Accept** specifies the content types that are valid in the response message. If the server cannot respond with the requested content type, the 406 Not Acceptable HTTP status message is returned.
  * **Content-Type** Indicates the content type that is used in the body of the request. The supported content type is XML. or JSON

* **Response headers:** optional response headers that are of interest because some functionality is obtained by their use:
  * **ETag** (entity-tag) is a string representing the specific version of the resource and it is used to cache unchanged resources
  * **Last-Modified** contains the time stamp for the last modified date of the requested resource.
  * **If-Modified-Since** is sent by the client to specify the last modified date in the cache.

* **Entry:** the well-known RESTCONF entry point (/restconf)
* **Resources:** path expression identifying the resource that is being accessed by the operation; represented by Media Type (application/yang-data+json)
* **Query:** set of parameters associated with the RESTCONF message
* **Error responses:** formatted in either XML or JSON, depending on the preferred type in the request.

### Operations

The RESTCONF protocol uses HTTP methods (RESTCONF operations) to identify the create, read, update, and delete (CRUD) operation requested for a particular resource.

* **GET** method is sent by the client to retrieve data and metadata for a resource. It is supported for all resource types except operation resources. The request must contain a request URI that contains at least the entry point component (/restconf).
  
* **POST** method is sent by the client to create a data resource or invoke an operation resource.
  
* **PUT** method is sent by the client to create or replace the target resource. The request must contain a request URI that contains a target resource that identifies the data resource to create or replace.
  
* **PATCH** method is used to create or update a subresource within the target resource. If the target resource instance does not exist, the server does not create it.
  
* **DELETE** method is used to delete the target resource. If the DELETE method succeeds, a 204 No Content Status-Line is returned, and there is no response message body.
  
* **OPTIONS** method is sent by the client to discover which methods are supported by the server for a specific resource. The supported methods are listed in the ALLOW header.
  
* **HEAD** method is sent by the client to retrieve only the headers that would be returned for the comparable GET method, without the response body. The access control behavior is enforced as if the method were GET instead of HEAD. The server will respond the same as if the method were GET instead of HEAD, except that no response body is included.

### Media Types and API Resources

A RESTCONF supports an application/yang-data+json or application/yang-data+xml media type for the following application programming interface (API) resources:

* **Data:** A root resource for the RESTCONF datastore
* **Operations:** RESTCONF root resource for the RESTCONF operations

### Query Parameters

Each RESTCONF operation allows zero or more query parameters to be present in the request URI. They can be given in any order, but they can appear only once in the requested URI. The specific parameters that are allowed depend on the resource type, and sometimes on the specific target resource used, in the request.

The list represents the the supported query parameters:

* **Content (GET):** For the GET request specifies the type of child nodes.
  * **Config:** Returns configuration data nodes
  * **Nonconfig:** Returns nonconfiguration data nodes
  * **All:** Returns all (config and nonconfig) data nodes

* **Depth (GET):** Retrieve a resource with all subresources inline in GET requests.
  
* **Fields (GET):** Used to specify which subset of events for stream resources will be inserted.

* **Filter (GET):** Used to specify which subset of events for the notification streams will be inserted.

* **Insert (POST/PUT):** Used to specify how a resource should be inserted in an ordered-by user list. This query parameter is used together with the resource query parameter. Possible values are after, before, first, and last.

* **Point (POST/PUT):** Specifies the path where a resource should be inserted in an ordered-by user list or in a leaf-list.

* **Start-time (GET):** Used to specify the start time for notification stream.

* **Stop-time (GET):** Used to specify the stop time for notification stream, it must be used with start-time parameter.

* **With-defaults (GET):** Used by the client to control display of default data in GET requests.

### Error Responses

Error responses are formatted in either XML or JSON, depending on the preferred MIME type in the request. The REST server will return standard HTTP response codes, as follows:

* **200 OK:** The request was successfully completed, and a response body is returned containing a representation of the resource.

* **201 Created:** A resource was created, and the new resource URI is returned in the "Location" header.

* **204 No Content:** The request was successfully completed, but no response body is returned.

* **400 Bad Request:** The request could not be processed because it contains missing or invalid information (such as validation error on an input field, a missing required value, and so on).

* **401 Unauthorized:** The request requires user authentication. The response includes a "WWW-Authenticate" header field for basic authentication.

* **403 Forbidden:** Access to the resource was denied by the server, due to authorization rules.

* **404 Not Found:** The requested resource does not exist.

* **405 Method Not Allowed:** The HTTP method specified in the request (DELETE, GET, HEAD, PATCH, POST, PUT) is not supported for this resource.

* **406 Not Acceptable:** The resource identified by this request is not capable of generating the requested representation, specified in the "Accept" header or in the format query parameter.

* **409 Conflict:** This code is used if a request tries to create a resource that exists.

* **415 Unsupported Media Type:** The format of the request is not supported.

* **500 Internal Error:** The server encountered an unexpected condition that prevented it from fulfilling the request.

* **501 Not Implemented:** The server does not (currently) support the functionality required for fulfilling the request.

* **503 Unavailable:** The server is currently unable to handle the request due to the resource being used by someone else, or the server is temporarily overloaded.

### Error Responses

Error responses are formatted in either XML or JSON, depending on the preferred MIME type in the request. The REST server will return standard HTTP response codes, as follows:

* **200 OK:** The request was successfully completed, and a response body is returned containing a representation of the resource.

* **201 Created:** A resource was created, and the new resource URI is returned in the "Location" header.

* **204 No Content:** The request was successfully completed, but no response body is returned.

* **400 Bad Request:** The request could not be processed because it contains missing or invalid information (such as validation error on an input field, a missing required value, and so on).

* **401 Unauthorized:** The request requires user authentication. The response includes a "WWW-Authenticate" header field for basic authentication.

* **403 Forbidden:** Access to the resource was denied by the server, due to authorization rules.

* **404 Not Found:** The requested resource does not exist.

* **405 Method Not Allowed:** The HTTP method specified in the request (DELETE, GET, HEAD, PATCH, POST, PUT) is not supported for this resource.

* **406 Not Acceptable:** The resource identified by this request is not capable of generating the requested representation, specified in the "Accept" header or in the format query parameter.

* **409 Conflict:** This code is used if a request tries to create a resource that exists.

* **415 Unsupported Media Type:** The format of the request is not supported.

* **500 Internal Error:** The server encountered an unexpected condition that prevented it from fulfilling the request.

* **501 Not Implemented:** The server does not (currently) support the functionality required for fulfilling the request.

* **503 Unavailable:** The server is currently unable to handle the request due to the resource being used by someone else, or the server is temporarily overloaded.

### RESTCONF Examples and Utilities

Here are some RESTCONF examples for Cisco IOS XE Software:

* GET https://172.16.1.57:443/restconf/data/native/
  * Retrieves a full device configuration.

* GET https://172.16.1.57:443/restconf/data/native/interface/GigabitEthernet?fields=name
  * Retrieves a list of GigabitEthernet interfaces.

* GET https://172.16.1.57:443/restconf/data/native/interface/GigabitEthernet=0%2F0%2F2
  * Retrieves interface-specific attributes for the GigabitEthernet0/0/2.

* GET https://172.16.1.57:443/restconf/data/native/interface/GigabitEthernet?fields=ip/address/primary
  * Retrieves a primary IP address for the GigabitEthernet interfaces.

Same utilities and tools that are used for the REST interfaces are used for the RESTCONF:
* Postman
* Firefox RESTClient
* Python requests module

## 11.8 Model-Driven Telemetry

Telemetry is an automated communications process by which measurements and other data are collected at remote or inaccessible points and transmitted to receiving equipment for monitoring. Model-driven telemetry (MDT) provides a mechanism to stream data from a model-driven telemetry-capable device to a destination.

Telemetry uses a subscription model to identify information sources and destinations. Model-driven telemetry replaces the need for the periodic polling of network elements; instead, a continuous Request for Information to be delivered to a subscriber is established upon the network element. Then, either periodically, or as objects change, a subscribed set of YANG objects are streamed to that subscriber.

The data to be streamed is driven through subscription. Subscriptions allow applications to subscribe to updates (automatic and continuous updates) from a YANG datastore, and this enables the publisher to push and in effect stream those updates.

A subscription is a contract between a publisher and a subscriber that stipulates the type of data to be pushed and the associated terms. Subscriptions allow clients to subscribe to event streams that contain automatic data updates.

The core components used in streaming model-driven telemetry data are:

* **Session:** Specifies one or more destinations to collect the streamed data and is based on two modes:
  
  * **Dial-in mode:** The receiver dials in to the router and subscribes dynamically to one or more sensor paths or subscriptions. The router acts as the server and the receiver is the client. The router streams telemetry data through the same session. The dial-in mode of subscriptions is dynamic. This dynamic subscription terminates when the receiver cancels the subscription or when the session terminates.

  * **Dial-out mode:** The router dials out to the receiver. This is the default mode of operation. The router acts as a client and receiver acts as a server. In this mode, sensor paths and destinations are configured and bound together into one or more subscriptions. The router continually attempts to establish a session with each destination in the subscription, and streams data to the receiver. The dial-out mode of subscriptions is persistent. When a session terminates, the router continually attempts to re-establish a new session with the receiver every 30 seconds.

* **Sensor path:** The sensor path describes a YANG path or a subset of data definitions in a YANG model with a container. In a YANG model, the sensor path can be specified to end at any level in the container hierarchy.

* **Subscription:** Binds one or more sensor paths to destinations and specifies the criteria to stream data. In cadence-based telemetry, data is streamed continuously at a configured frequency.

* **Transport and encoding:** The router streams telemetry data using a transport mechanism. The generated data is encapsulated into the desired format using encoders.

Model-Driven Telemetry data is streamed through these supported transport mechanisms:

  * **gRPC:** used for both dial-in and dial-out modes.
  * **TCP:** used for only dial-out mode.
  * **UDP:** used for only dial-out mode.
  * **NETCONF/YANG push:** used for only dial-out mode.
  * **gNMI:** used for both dial-in and dial-out modes.
  
  * **GPB** encoding
    * Configuring for GPB encoding requires metadata in the form of compiled .proto files. A .proto file describes the GPB message format, which is used to stream data.

  * **JSON** encoding

## 11.9 Streaming Telemetry with gNMI

gRPC Network Management Interface (gNMI) is a management protocol for streaming telemetry and configuration management developed by the OpenConfig community. gNMI provides mechanisms to install, manipulate, and delete the configuration of network devices and to get operational data.

The content provided through gNMI can be modeled using YANG. As a transport protocol, Google remote procedure call (gRPC) is used. gRPC is a remote procedure call developed by Google for low-latency, scalable distributions with mobile clients that are communicating to a cloud server. gRPC carries gNMI, and provides the means to formulate and transmit data and operation requests.

Cisco implementation of gNMI offers the following features:

* gRPC provides a general (Open Source) RPC framework
* Interface definition in Cisco specifies device operations
* Provides simple client development
* High performance
* Functional subset of NETCONF
* Runs over HTTP/2 (originally named HTTP/2.0)

![](/images/devcor/devcor_11_9_1.png)

gNMI is, like RESTCONF, a functional subset of the NETCONF protocol and uses HTTP/2 for transport protocol. Major differences here include improved security and support for bidirectional streaming. Primarily, it is used for telemetry data streaming but not limited only to it.

For each gRPC, a corresponding message is sent from the client toward the target. The target must reply with an appropriate message, which includes the gNMI service version.

![](/images/devcor/devcor_11_9_2.JPG)

The following table describes the Cisco-specific gRPC operations for gNMI implementation.

![](/images/devcor/devcor_11_9_2.JPG)

# 12  Using Relational and Nonrelational Databases

## 12.1 Introduction

Data is all around; it may be unstructured and unformatted data, but it is still data. But data that has no structure and no purpose is of little use. To get real value from a collection of data, it must be standardized and formatted in an optimal way.

In the context of IT, databases take care of data standardization, access, and storage. Many different types of databases have evolved over time and it is crucial to be able to evaluate your data and choose a suitable database type for your application. When choosing a database type, one of the most important decisions is whether to choose a relational or a nonrelational database.

## 12.2 Evaluating Database Types to Meet Application Needs

When evaluating which type of database to choose for your application, many things must be taken into consideration:

* What kind of data will my database contain?
* Will I mostly be using a database to write or to read?
* How many users will be using it at once?
* How will my queries look like?
* Will the model stay unchanged or are changes unavoidable?
* Does my database need to be persistent?
* How quickly will database need to be scaled up?

![](/images/devcor/devcor_12_2_1.png)

As a software developer, you will encounter many different types of data structures. To avoid spending late nights troubleshooting database problems, you must be aware that the choice of a database type is one of the most important things that you must consider when designing any application. Data-modeling techniques can help you narrow the choice down to a suitable database type.

Data modeling is usually one of the first steps in choosing the optimal database type. Data modeling is a very abstract process that often includes the involvement of business units and is a part of the design phase. The goal is to map the requirements for the data contained in the database, so that you can define the relationships between entities and set any necessary constraints on the data. Data modeling results in data models—a conceptual data model is translated into one or more logical data models (LDM) that are then implemented with a physical data model (PDM). A data model can be something as simple as a single table with time-stamped entries, or a huge model with over 100 tables and complex dependencies and constraints.

![](/images/devcor/devcor_12_2_2.png)

A conceptual model defines only entities, attributes, and relations. A logical model improves on that by indexing data, setting column types, and defining constraints. A physical model represents the actual database implementation with a specific syntax.

### Entity Relationship Modeling

Entity relationship modeling (ERM) is a data modeling technique that defines entities with their attributes and the relationship between them. Using ERM is a good place to start when modeling a database because you will get a basic idea of the kind of database that you need.

ERM uses an entity relationship diagram (ERD) to represent the information, which contains these elements:

* **Entity:** Something that can be uniquely identified in the context of the domain. An entity exists either physically or logically. Entities are capable of independent existence, implemented with a table in a physical data model.

* **Attribute:** A property of the entity. One entity usually has many attributes. Special attributes are called ***primary keys***, which are unique to each entity and are used to identify the entity. Similarly, ***foreign keys*** are attributes that reference primary keys from other entities. Entities that must be defined by foreign key relationships are called ***weak entities***.

* **Relationship:** Relationships convey how the entities are related to each other. They are usually verbs like contains, owns, supervises, and so on. Each relationship has a defined ***cardinality***, which specifies how many instances of an entity relate to one instance of another entity (for example, one-to-one, minimum-zero-to-many, many-to-many, and so on) and ***modality***, which describes whether a relationship is mandatory or optional, often depicted with a notation such as a Crow's foot.

Relationships between entities can easily be described in a natural language. For example, “One manager supervises one or many employees” and “Zero or one or many different GPUs can fit into a motherboard.”

The following is an example of an entity-relationship diagram for a computer parts web store.

![](/images/devcor/devcor_12_2_3.png)

Entity relationship modeling works well for information that can be represented in a relational database. It is, however, inadequate for systems which describe semistructured data. Complex schema constrains the semistructured data too much and makes changes to the model nontrivial.

The following is a diagram of Crow's foot notation. Modality describes if an entity instance is mandatory (one or zero) for the relationship or not, while cardinality defines quantifiability (one or many).

![](/images/devcor/devcor_12_2_4.png)

A process of optimizing the ERM is called normalization. The goal of normalization is to free the model of undesirable dependencies and data duplication, reduce the need for restructuring as new data types are introduced, and make the model more informative to users. The normalization process is usually used in relational databases, while denormalization is more common in nonrelational databases. While normalization uses strict rules for the process, denormalization is more loosely defined. Denormalization is based on your use case—data can be aggregated, duplicated, or formatted to make it easier to use.

The example below displays how a normalized model of a computer component compares to an aggregated one.

![](/images/devcor/devcor_12_2_5.png)

### Relational vs. Nonrelational Databases

Relational design starts with a focus on describing the entities and their relationships, and the queries and indexes are designed later. Due to normalization, data is atomic and nonredundant, which makes storage very efficient. However, to make any use of the data, it must usually come back together, which is done using joins. Joins are operations where a query is executed on multiple combined tables. Using joins can create bottlenecks and limit horizontal scalability.

That, and the incredible growth of data in the last years is why nonrelational database designs started to appear. Nonrelational databases are often called NoSQL databases as a counter-movement to Structured Query Language (SQL), because relational databases use SQL to manage the data and NoSQL databases often only use the paradigms of SQL that support and enhance the database’s functionality.

Nonrelational databases usually put the emphasis on scalability and flexibility—sometimes referred to as the 3 Vs: volume, velocity, and variety.

Nonrelational databases also rarely use fixed schemas and define the structure of the data very loosely.

Today, it is not uncommon for a startup company to grow at a very rapid pace—from a few hundred users to a few million—often offering a cloud-hosted globally available application. Usually, the speed at which startups achieve this growth can be partly due to the flexibility of nonrelational databases.

Relational databases choose a query-first approach and are driven by the structure of the data. A good question for relational database design is "What answers do I have?" Nonrelational databases often start with "What questions do I have?" Data modeling techniques used by a nonrelational design include the following:

* **Denormalization:** Data can be copied into multiple tables to simplify complex queries and reduce the number of queries. The same attribute can be found in multiple places, depending on the applications particular data model, which increases the total amount of data that you must store, but storage costs are rarely a bottleneck anymore. Denormalization also increases complexity, because multiple write operations may be needed to update just one parameter. Most modeling techniques use denormalization to some degree.

* **Aggregation:** Data aggregation is the process of modeling data structures as a unit. For example, a simple "order" entity, modeled with relational design, can consist of multiple tables and joins using a fixed schema. Using data aggregation and a semistructured (soft) schema, you can store all the orders in a single table. Aggregation is useful for models with a lot of one-to-many relationships (therefore, many joins) and for models where interaggregate interactions are not common (such as only printing the Order entities).

* **Data distribution:** Data aggregation is also a great baseline for distributed database models, where relational databases fall short due to the Atomicity, Consistency, Isolation, Durability (ACID) compliance. Instead, Consistency, Availability, Partition (CAP) theorem is used, which states that, with distributed systems, only two of the former attributes can be satisfied. Two styles of distributing data are called ***sharding*** and ***eplication***.

Sharding (or ***horizontal partitioning***) is the process of distributing data across multiple servers, so that each server acts as a single resource for a subset of data. Only a single instance of an entity exists in the system. A great example is using distributed servers for users who access the application from around the world, because the database can be sharded geographically, which reduces response times. Sharding is sometimes implemented with an additional lookup table, containing the references to where specific shards can be found.

The drawbacks are that the shards can eventually become unbalanced, negating any benefits, because that shard becomes a hotspot for processing. It is also very difficult to return the sharded database back to an unsharded version.

![](/images/devcor/devcor_12_2_6.png)

Replication is the process of storing data across multiple servers so that each entity can be found in multiple places. Primary-secondary replication uses an authoritative node to handle and dispatch queries to secondaries and their synchronization. Replication runs on specialized equipment to handle the increased load. Peer-to-peer replication allows reads and writes to any node and the nodes coordinate synchronization themselves. The synchronization is not immediate, so this type is not suitable for applications where up-to-date information is crucial, such as banking. Replication is sometimes used on relational databases too—especially for high availability—but it is more costly to ensure ACID compliance.

![](/images/devcor/devcor_12_2_7.png)

Referring to the previous example of a web store, a relational database is a suitable choice for a product database, because components have functional relations between them, and the product can be thought of as structured data. However, there are several different types of nonrelational databases, and each one is specialized to solve a different problem around storing data. The following lists some typical use cases for different database types:

* **Key-value database:** A travel blog on a web site, which uses a key-value database. Each value (in this case, a blog post) is stored under a different key, and the keys represent URIs on which the blog is available.

* **Document-based database:** Suitable for a successful startup company that uses an application with fast-changing specifications. Data is saved as semistructured documents, facilitating change management.

* **Column-based database:** For business analysis, where huge amounts of data need to be processed, a column-based database is perfect because the data is stored in columns, instead of rows. Most of the operations are performed on only one column, so performance is much greater than with row-based databases.

* **Time-series database:** With the rise of smart cars and the Internet of Things (IoT), gigabytes of telemetry data are being generated each day. The data is sent back to a vendor for analysis and stored in a simple time-series database.

* **Graph-based database:** Social media has become very popular in the past few years. Due to a huge number of complex relations between entities, the social media application uses a graph-based database, where data is defined and traversed via nodes (entities) and edges (relations).

As you can see, not just data itself, but also the use case and the potential for scaling and changes down the road, play a major part in choosing the correct database design. Usually, a good place to start is to create an ERM diagram and think about how, why, how often, and how fast the data will be needed. An ERM diagram should give you an idea whether a relational database is enough, or if a nonrelational database suits your application better. Never choose a database type just because you are familiar with it—an unsuitable choice can cause major problems down the road.

![](/images/devcor/devcor_12_2_8.JPG)

## 12.3 Relational Database Concepts

Relational databases are based on the relational model of the data, described by Edgar F. Codd in the 1970s. Relational databases are, by far, the most used database model in the world and have a mature and well-documented ecosystem.

The data is organized into one or more tables, which consist of columns (that define attributes) and rows (that define records) that represent a relation.

All the rows in the table can be identified by a **key**. Keys are one or more attributes that uniquely define the record in the table. Most tables feature a unique **primary key**, which is a minimal set of attributes that together create a unique combination. Other keys are called **alternate keys**. Usually, the primary key features only a single ID attribute, which is an auto-incrementing natural number. If a key is more complex and is composed of multiple different attributes, it is called a **composite** key. Primary keys that consist of real-world information are called **natural keys** such as national identification number, time and location of one's birth, and so on. **Foreign keys** are attributes whose values match the values of a primary key in another relation, and are useful for joining two or more tables together.

![](/images/devcor/devcor_12_3_1.png)

Data in the tables is managed with ***transactions***. Transactions symbolize a unit of work in a database management system and generally represent any change in the database (creating new data, changing schema, and so on). Transactions have two purposes:

* Provide isolation between operations. Multiple concurrent operations will produce errors.

* Ensure reliability and consistence in the event of a system failure or incomplete operation.

![](/images/devcor/devcor_12_3_2.png)

While a transaction is a single unit of work, it can consist of multiple operations. However, it uses an "all or nothing" approach. Either the whole transaction is successful or nothing is. When a transaction fails, the database should be rolled back to before the start of the transaction. If a transaction succeeds, it is then committed to the database checkpoint, which applies the changes to the database itself.

After a transaction has reached the "point of no return," it cannot be rolled back anymore.

ACID is a set of properties that are used to guarantee database reliability. A relational database transaction should be ACID-compliant, but many are not (at least not natively), because they sacrifice a part of the ACID principle for better performance. Use of ACID depends heavily on the use case. A financial system will not consider a non-ACID database, but a database that is used mostly for writing information will probably benefit from the increased performance by not conforming to ACID principle.

* **Atomicity:** Guarantees that the transactions are treated as single units that either succeed or fail completely. ACID must guarantee atomicity in any case, even in the case of power failures or crashes.

* **Consistency:** Ensures that a transaction only changes the database in allowed ways. All the written data must adhere to the defined rules and constraints. Consistency does not guarantee that the transaction is correct (mistakes can still be made by administrators while defining rules), only that a transaction can bring the database from single valid state to another.

* **Isolation:** Ensures that concurrent transactions leave the database in the same state as sequential transactions would. Concurrent processing of transactions is not uncommon: some databases handle thousands of operations per second or more. Several isolation levels are used, from read- and write-lock to being able to read uncommitted transactions.

* **Durability:** Guarantees that once a transaction has been committed, it will remain that way—even if the system fails. Some databases run in-memory for better performance or other reasons, so they must handle saving the data to a nonvolatile storage in a system crash.


### Database Normalization
Relational databases often use the process of normalization to optimize the structure of the database. The goal of normalization is to increase consistency and eliminate redundancy of your data, which results in a smaller database size and increases the flexibility of your database. Normalized databases are much easier to change or redesign because the relations have only the dependencies that they need. A normalized database also prevents various anomalies that are caused by modification of data with undesirable dependencies or duplicate data.

Database normalization is done following a series of normal forms (NFs). There are now multiple NFs, but usually only the first three must be satisfied.

Relational databases should always be normalized up to the third NF, if no reason is present as to why you should not do it (for example, a normalized model becoming too complex or not performing well with specific queries). Most simple designs already satisfy the third NF.

A database satisfies the third NF with the following:

* First NF:
  * Tables should have only atomic (single) valued columns.
  * Values stored in a column should be of the same domain.
  * Columns in a table should have unique names.

* Second NF:
  * Tables should be in the first NF already.
  * There should be no partial dependencies.

* Third NF:
  * Tables should be in the second NF already.
  * There should be no transitive dependencies.

First NF enforces atomic values.

![](/images/devcor/devcor_12_3_2.png)

Second NF ensures that all nonkey attributes depend on primary key.

![](/images/devcor/devcor_12_3_4.png)

Third NF eliminates transitive dependencies.

![](/images/devcor/devcor_12_3_5.png)

### Managing a Relational Database

Relational databases use one of many versions of a relational database management system (RDBMS) to communicate with and maintain a relational database. An RDBMS contains a set of functionalities that help you operate a relational database. While the structure of various nonrelational database management systems is similar, they do feature different options for scalability and fewer constraints on data. These systems often support multiple running database instances and manage access for multiple users. Some RDBMSs also keep part of the database in the computer's memory for greater performance (usually before a transaction is committed).

RDBMS uses SQL to manage data that is contained in the database. RDBMS supports queries, data manipulation (insert, update, delete), and data definition (schemas). SQL is considered a declarative programming language: it expresses the logic of a computation without describing its control flow (focuses on what, not how), although some functionalities (such as stored procedures) add imperative functionality support.

RDBMSs have many flavors of implementation and usually represent what people mean by "database." Notable examples include MySQL, PostgreSQL, Oracle, MS-SQL, and more. While the query syntax is very similar from implementation to implementation and is based on SQL, differences between RDBMSs exist, especially using advanced functionalities and features that are not specified in SQL standard. Following is an example of limiting the number of results in a query and reading the first 10 product entries.

![](/images/devcor/devcor_12_3_6.png)

First, a table must be created. For this example, Transact-SQL (T-SQL) syntax is used on MS-SQL Server.

![](/images/devcor/devcor_12_3_7.png)

Each attribute must have a name and a type. Additional parameters are optional.

Relational databases have strictly defined data types. Again, while they differ between different SQL implementations, some general types are used:

* **Boolean:** Stores a variable with two possible values, often **TRUE/FALSE** or **1/0**.

* **Integer:** Represents a whole number. Some RDBMSs use just **INT**, while some use types that are further defined, such as **SMALLINT**, **BIGINT**.

* **Float:** A floating point number. Represents numbers that have greater accuracy and decimal values. Sometimes implemented with **REAL**, **FLOAT**, **DOUBLE**, and so on.

* **Datetime:** A date-time format that stores time to the millisecond. Different formats may be used for different time formats: time, date, and so on.

* **Char:** Defines a character string with a fixed length.

* **Varchar:** Used for variable length strings, although the maximum length must be defined by the user, when creating the schema.

* **Binary:** Binary string with a fixed length.

* **Null:** Represents an absence of value. Not a space, not "null," but no value at all.

An example **INSERT** statement is used to create new row entries.

![](/images/devcor/devcor_12_3_8.png)

And the **SELECT** statement is used to read these entries.

![](/images/devcor/devcor_12_3_9.png)

Update one or more attribute values with the **UPDATE** statement.

![](/images/devcor/devcor_12_3_10.png)

Remove table entries with the **DELETE** statement. Keywords such as **AND**, **OR**, and **NOT** help form logical statements.

![](/images/devcor/devcor_12_3_11.png)

Update the table schema with the **ALTER** keyword. **DEFAULT** sets a default value for an attribute when none are entered.

![](/images/devcor/devcor_12_3_12.png)

Deleting columns while altering a table is not as simple as creating them, especially if the column represents a primary key or a foreign key to some other table.

If you want to delete the data inside a database, use **TRUNCATE**.

![](/images/devcor/devcor_12_3_13.png)

However, if you want to delete the whole table, use **DROP**.


![](/images/devcor/devcor_12_3_14.png)

Use SQL syntax to create Stored Procedures, which are redefined procedures that can be executed at will.

![](/images/devcor/devcor_12_3_15.png)

You can also define constraints on your table. For example, you can only allow predefined types.

![](/images/devcor/devcor_12_3_16.png)

Use the **AS** keyword to create aliases and works on columns and tables.

![](/images/devcor/devcor_12_3_17.png)

Some constraints can also be defined on a table:

* **PRIMARY KEY:** Sets a primary key.
* **UNIQUE:** Enforces only unique values in a column.
* **NOT NULL:** Does not accept a NULL value.
* **CHECK:** Checks if value is valid.
* **DEFAULT:** Assigns a default value if none is supplied.
* **FOREIGN KEY REFERENCES:** Sets a foreign key.

Queries can also be nested where needed and the results cast to a named result set (with alias: **AS** keyword).

SQL supports a set of commands, known as set functions, that are operations which are performed on the specific data set inside a query that returns a result. The following are set functions:

* **COUNT (DISTINCT):** Obtains the number of (distinct) values in column.
* **SUM:** Sums all the values in the row.
* **AVG:** Returns the average of the column's values.
* **MIN / MAX:** Obtains the minimum / maximum value in the column.

Pattern matching is also supported. While not ideal for performance-based queries, pattern matching is still useful. You can also order the displayed results by using **ORDER BY** keyword.

![](/images/devcor/devcor_12_3_18.png)

Use **JOIN** to display related data from multiple tables. The order table has a foreign key reference to the product table via the ProductID attribute.

![](/images/devcor/devcor_12_3_19.png)

SQL leverages the relational model and strictly defined data with joining tables. **JOIN** is used to combine rows from two or more tables based on a related column (key) between them. Four types of joins are used, depending on where the records are taken from.

**JOIN** operations are one of the most resource-consuming types of operations, especially when combined with **LIKE** commands that use a wildcard and are often one of the first things to be optimized.

![](/images/devcor/devcor_12_3_20.png)

## 12.4 Key-Value Database Concepts

The key-value pair is a very well-established IT concept. In programming, data that is stored in key-value pairs is also called an associative array (or map, dictionary, hash table and so on) and is supported in almost all programming languages. A key-value pair is often considered a primitive data type and is already included with the language. But associative arrays are not the same as key-value databases, which offer more functionalities than a simple array.

![](/images/devcor/devcor_12_4_1.png)

Key-value databases are storage paradigms, where each data entry is saved under a specific key. Each key uniquely defines the record and, while the same value can be used for multiple keys, entries are not in relation as they are in relational databases. Each entry is its own entity, so the keys cannot appear more than once in a database (like primary keys).

Data in a key-value database is not constrained by type, format, or size, and is considered opaque. Each value can be different and unrelated to other values.

![](/images/devcor/devcor_12_4_2.png)

Key-value data storage is also a commonly used concept in real life. A good example is the phone book, which stores a phone number under a unique key (a person).

Address Resolution Protocol (ARP) tables use key-value mapping for quick resolution between MAC and IP addresses. Web pages are often a collection of keys (URLs) that point to some values (HTML content).

As with relational databases, many implementations of the key-value concept exist. Still, a set of features exists that is common to all of them.

Compared to RDBMS, key-value databases focus on:

* Simplicity
* Speed
* Scalability

![](/images/devcor/devcor_12_4_3.png)

Simplicity in key-value databases is reflected in a very simple relational model, consisting of only a key and a value. Keys are usually strings, but values can be anything. Due to the nature of key-value databases, values are commonly stored as a Binary Large Object (BLOB). Then it is up to the application to figure out what data is stored inside, which means that key-value databases use unstructured data.

Overall, a lot of functionalities (validation, joins) that other databases provide is offloaded to the application using the database, to keep simplicity high. A downside is that you cannot search by values directly, only by a key. Range-based queries are also not supported, due to the nature of keys.

Key-value databases use no relational dependencies, so all read and write requests are considered independent, supporting both horizontal and vertical scaling. Hashing keys also allow for easy scaling out as the hash function transforms the key into an integer, which can then be put under a modulus operator to determine the location of a value: **hash(KEY) % number_of_servers**.

Query speed is a very important factor in key-value databases. Relational databases mostly use the B-Tree structure to index its data, which has a logarithmic cost of searching: **O(log(n))**, while key-value databases use hashing functions to find the element with a time complexity of **O(1)**.

Key-value databases support only basic data manipulation operations.

![](/images/devcor/devcor_12_4_4.png)

**GET** returns the value for a key, if it exists. **PUT** creates a new entry and saves the value under that key. If a value already exists, it is overwritten, since key-value databases treat values as a whole. Therefore, you can not update only a part of a value, you can only update the whole value. **DELETE** removes the value and the key from the database.

### Key Design

Keys may seem simple enough at first, but considering that they are the only things that enable you to retrieve or change data, an optimal key naming strategy is imperative.

Every key is not the same! Keep in mind that values will be read programmatically.

* Bad key:

```bash
  "UserBobSmith" : "Bob Smith, London"
```

* Better keys:

```bash
  "User:1:FirstName" : "Bob"
  "User:1:LastName" : "Smith"
  "User:1:Location" : "London"
```
By breaking down a key and its value, you can access atomic data via a single key with no additional parsing. Keys can also be generated or accessed programmatically (for example, "for" loops), because you only need to know the structure of the key and not the key names themselves.

The above approach uses keys composed of prefixes **(User)**, identifiers **(1)**, and suffixes **(FirstName)**. In a way, this approach mimics relational database design, where a prefix represents a table, an identifier represents a primary key, and a suffix represents an attribute. However, using a key-value database to store relations is not recommended and a relational database should be used in such case. Values that reference other keys should be avoided and the data should rather be duplicated instead.

Consider the following key and how the design can be scaled out horizontally:

```bash
  DB.get ("User:8:Personal:Name")
```

![](/images/devcor/devcor_12_4_5.png)

Key design should also take potential scaling into account, because it is easier to shard a database based on structured partial keys than on unstructured ones. The diagram above is an example of both ranged and hashed sharding.

### Value Design

Designing how you store your values depends mostly on the use case of your applications.

You should design and aggregate your values around your use cases to make your read/write operations more efficient:

* Big aggregates of unstructured data? Consider using JSON/XML.
* Ultra-fast caching needed? Keep your values as small as possible.
* Groups of values commonly read together? Try using denormalization.

Example A:

* "Invoice:5:TotalSum" : "50 "
* "Invoice:5:Date" : "15.08.2019"
* "Invoice:5:VAT" : "15%"
* "Invoice:5:Customer" : "Bob"

Example B:

* "Invoice:5" : { totalSum: "50", date: "15.08.2019", VAT: "15%", customer: "Bob" }

Example A shows deaggregated data. The format is useful when the data will be read atomically, such as to add up all the invoice sums. The deaggregated format increases the number of read operations, but uses much less overhead for processing the value. However, if your goal is to display the invoice, then Example B is more useful, because the invoice is read with a single query, which reduces the load on the database server and moves the processing to the application.

### In-Memory Caches

When storing data, you can choose the kind of medium that you want that data to be saved and read from.

Traditional databases keep their data on nonvolatile memory (NVM), which means that the data persists through system shutdowns, power outages, and so on. A portion of the running database is kept in current working memory (RAM) for write buffers, performance optimization, and some response caching, but the data is stored somewhere.

In-memory databases sacrifice the persistence of data for a huge performance boost, and operate (almost) exclusively in memory, so the data is lost at the end of the application lifecycle (server restart, scheduled dump, and so on). Similar to traditional databases, in-memory databases keep the logs and snapshots on NVM; otherwise, there would be no point in having them.

![](/images/devcor/devcor_12_4_6.png)

Using in-memory databases removes all the I/O operations, which are some of the costliest operations when querying a database. A typical HDD storage has an average random-access time of 10 milliseconds; SSD storage has an access time of a few microseconds; while RAM random access time can be a nanosecond or less. It all depends on the hardware manufacturer, but read/write operation costs can differ in the order of magnitude.

Due to the increased operational speed, in-memory databases are perfect for caching. Caching is a process of storing data for future requests, so that data can be served to the client without actually processing the requests.

![](/images/devcor/devcor_12_4_7.png)

First, the application (or a database management system with built-in cache) checks the cache for a specific key, which is a very fast operation. If the value for a key exists, then it is returned (called a cache hit). If it is not returned (cache miss), the data is read from the source (a database, a web service, or dynamic generation, depending on the application), although much slower than from a cache.

Using a cache does not guarantee up-to-date data though, so a cache expiry time is usually set. The expiry time depends on the application, but it can be very short for time-sensitive or security-sensitive data, or very long, even indefinite (for example, static web content that does not change).

Key-value in-memory databases are very useful for data that is simple, does not need to be persistent, and benefits greatly from a high write/read speed. What are such use cases?

In-memory caches are popular for:

* Session caching
* Multiplayer gaming
* Web page caching
* Enhancing database performance
* Spike management in applications
* Domain Name System (DNS) caching
* And much more!

Session management can be quite intensive for processing because authentication tokens are passed with every HTTP request and would usually need to be decrypted, parsed, and checked against an RDBMS containing users and sessions. With cache, you can simply find the token in a session cache and check if it is still valid. Applications must support session caching though, because it is a security vulnerability if configured improperly.

Scoreboards are commonly cached in online multiplayer games. Scoreboards support millions of users, so dynamic generation would be a nightmare. To reduce the number of queries to the server, scoreboards are usually cached every few minutes and accessible from the cache.

Web pages usually have some content that is static, such as user interface elements, HTML elements, and so on. It is not practical to request this content every time that you visit the web page, so the content is cached and can be regenerated or emptied when needed, which greatly improves performance and the search engine optimization (SEO) scores of a web page. Usually, shopping carts are also cached.

Databases use caching for achieving better performance. Relational databases can become slow when working with millions of rows and badly optimized indexes. Many **SELECT** queries can be executed frequently, or at least have static responses, so query results are cached for some time to ease the load on the database. Note that caching can provide false data if incorrectly implemented.

Some applications use caching to overcome temporary traffic spikes. Less accurate noncritical data for an hour each day is a better trade-off than buying three times more hardware to make sure that your web application operates during the rush hour.

DNS entries are commonly cached too. IP addresses for most web pages do not change daily or even yearly.

Redis and Memcached are two of most popular in-memory key-value databases today, offering many more functionalities than a plain key-value database, such as strongly typed values, persistence, and some transaction support.

## 12.5 Document-Based Database Concepts

Document-based databases are a subclass of key-value databases and are in a way the next step from key-value model in the database evolution. They are ideal for storing and retrieving semistructured data and have become incredibly popular in the last few years due to the rise of agile development methods and the need to store semistructured data (multimedia, social media, and so on).

Documents are a basic building block of document-oriented databases. They are a similar concept to an object in programming, because one of the main incentives for document-based databases was a need for easy integration. They are loosely defined but are not required to follow a standard schema, nor are they required to have the same elements as other objects. Even objects from the same class can look very different (like Interfaces in object-oriented programming). Documents are stored similarly, with an encoding schema to store the information (XML, YAML, JSON, BSON, and so on) and a set of attributes which may or may not be mandatory.

Documents are considered independent, so if some information is added to some documents, it is not necessary to update all the documents of the same type.

Some metadata is also provided with the document, which may be related to document organization, security, or other implementation specific features.

![](/images/devcor/devcor_12_5_1.png)

![](/images/devcor/devcor_12_5_2.png)

In the example, you can see two documents that define a person. The first uses XML encoding for the ***document content***, while the second one uses JSON. Some structural elements are the same, and others are unique to the document.

![](/images/devcor/devcor_12_5_3.png)

Documents in a database are organized into collections, which are a similar to tables in traditional databases.

Collections are used to group documents, making it convenient to do some operations on them, such as updating, subscribing to changes, and so on. Typically, documents of the same namespace will be included in a collection: users, products, posts, and so on. A collection can also be based on geolocation, document reading frequency, and anything that fits your use case. Each document can be a member of only one collection.

![](/images/devcor/devcor_12_5_4.png)

![](/images/devcor/devcor_12_5_5.png)

If you want to include data from another document, you have two options:

* Embed the data, which essentially means that you write the data to multiple documents. Be careful when updating data, your application must be written in a way that it can update all the occurrences of the data. You can also end up with documents that are extremely large and consist of a lot of duplicates. The queries must also filter out embedded data to prevent duplicate results. Because all the data required for a certain action is inside a single document, speed of access is improved.

* Save a reference to the document somewhere in a document. As document-based database uses keys to store data, simply save a reference to a document in your document and query the referred document, which keeps the data at least somewhat consistent and the data is easier to update, but more read operations are required to access it. This method is more efficient regarding hardware resources and is also more suitable if you are using caching. Referring documents is much more like a relational model than embedding them.

![](/images/devcor/devcor_12_5_6.JPG)

***Note:***The field of document-based databases is one of the fastest evolving (and one of the most useful) fields in information technology. It is hard to predict what the technology will look like in five years, especially with data becoming the center point of businesses.

### Document-Based Database Examples

Document databases support similar operations to relational databases: create, read, update, and delete (CRUD).

MongoDB, an open source document-based database, is used with MongoDB Compass, which is a GUI tool for MongoDB and MongoDB shell. As you can see, queries have a programmatic approach to the data.

Document-based databases are quick to set up and start because hard schema definition or relations are not needed. If IDs are not defined, then they are autogenerated (_id in the example).

```bash
  > db.createCollection( “People“ )

  > db.People.insertOne( { name: "Ann", location: "UK", age: 30 } )
  > db.People.insertMany( [ { name: “Bob", location: "UK", age: 35 },
        { name: “Clyde", location: “USA", age: 20 } ] )

  > db.People.find( { location: "UK“ } )
  { "_id" : ObjectId("5d80c44a7bf25753c91c899f"), "name" : “Ann", "location" : "UK", "age" : 30 }
  { "_id" : ObjectId("5d80c9d87bf25753c91c89a0"), "name" : "Bob", "location" : "UK", "age" : 35 }
```

Query operators ($) allow you to create advanced queries.

```bash
  > db.ShoppingCart.find( { status: ”Completed”, product: { $all: [ “Intel I7”, “Intel I9” ] } } )
  > db.Materials.find( { length: { $elemMatch: { $gt: 50, $lt: 100 } } } )
  > db.Person.find( { “children”: { $size: 5 } } )
```

Embedded documents can be queried directly or using dot notation. Queries resemble programmatical function calls.

```bash
  > db.Inventory.find( { stock: { location: “UK”, quantity: { $lt: 5 } } } )
  > db.Inventory.find( { $and: [ { stock.location: “UK” }, { stock.quantity: { $lt: 5 } ] } )
```

Query operators allow for functionalities, like SQL language.

* First query finds all the shopping cards, whose status is C  ompleted and have bought both Intel I7 and Intel I9 CPUs.

* Second query lists materials that are between 50cm and 100cm long.

* Third query returns people, who have exactly 5 children.

* Fourth and fifth queries both find all the products, located in London, of which there are less than 5 in stock.

Similarly, you can use **b.update** statements to update certain values or just add / remove attribute to documents—it is that easy!

```bash
  > db.Inventory.updateMany( { type: ”CPU” }, { $set: { stock: false } } )
  > db.Inventory.updateMany( { }, { $set: { discount: 0 } } )
  > db.Inventory.updateMany( { type: “RAM” }, { $unset: { cores: “” } } )
```

You can use the delete statement similar to find statement.

```bash
  > db.Inventory.deleteMany( { material: [ “Wood”, “Iron” ] } )
  > db.Inventory.deleteMany( { material: { $and: [ “Wood”, “Iron” ] } )
```

* First query in this case sets the stock to false for all CPU items in the inventory.
* Second one adds a discount attribute to all the documents in the collection.
* Third example removes cores attribute from documents of RAM type.
* Fourth query deletes items from inventory, that are made exclusively from wood and iron.
* Fifth query deletes items that are made from wood and iron in addition to other materials (note the $and keyword).

Updating schema could be very painful to do in a relational database, especially if it's already in use. Note the syntax: ***db.updateMany( {query}, {value})***. The ***updateMany*** function updates all the available fields, while ***updateOne*** (or ***update***) updates only the first occurrence.

### Advanced Data Analytics

More services are becoming digital and migrating to a digital environment. Therefore, the amount of data to be stored and processed has increased greatly. Think about social networks, location-based services, IoT, semantic web—they all store huge amounts of data, which is also one of their main business assets. Effectively managing and processing this data becomes imperative.

The amount of data that is being stored is increasing exponentially. With huge amounts of data, traditional data processing is no longer feasible.

* Some databases contain Petabytes of data or more.
* There is often a need for full-text searches on unstructured data.
* Queries on a single machine would take days.
* A field of big data emerged.

Specialized tools like Elasticsearch and MapReduce have evolved for this purpose. To solve these problems, either the amount of hardware resources can be increased or the way the resources are processed can be improved. Elasticsearch and MapReduce use both of those techniques.

Most of the popular web services contain a search field, and common techniques to query the underlying data would not be usable. Imagine using a search engine and waiting three and a half hours for it to crawl all the web pages. Searching for specific attributes is also not practical; sometimes users do not know exactly what they are searching for or they use phrases. Therefore, a full-text search is optimal.

Elasticsearch is a scalable, distributed, open source full-text search and analytics engine, which allows you to store and analyze large amounts of data. It can be a standalone data storage tool, however it is used to index and search for specific sets of data.

MapReduce is a programming model and implementation for processing big data sets using parallelism and load distribution.

![](/images/devcor/devcor_12_5_7.png)

![](/images/devcor/devcor_12_5_8.png)

An inverted index is a concept that MapReduce and Elasticsearch use to organize and search data. A regular (forward) index uses keys (in this case, documents) to index the data, but an inverted index indexes keywords by documents.

To find how many times the word "Dog" occurs in the example using a forward index, you would have to search through all the index entries for the specified word. An inverted index would use only a single key ("Dog"), which then references all the occurrences in documents.

Regular indexing is done quickly, but the consequent searching is not as efficient as with an inverted index (which has a slower indexing speed).

In addition to heavy use of indexing, data sharding and replication is also used.

Elasticsearch and similar tools often use terms instead of keywords. Human language deals with many variables, such as tense, gender, number, and capitalization, so it makes sense to generalize keywords into terms for easier indexing. Keywords are generalized by text processing: stemming words and using synonyms and prefixes.

![](/images/devcor/devcor_12_5_9.png)

![](/images/devcor/devcor_12_5_10.png)


If a user does a full-text search for a phrase "Will there be snowfall on Saturday," the user may not get any matches. If the user searches for a more generalized phrase, such as "Snow on weekend," more results are likely.

By using stemming, you only keep the "stem," or root, of the word. It makes more sense to index data by these terms than by the original words, including commonly used synonyms. For example, "snow" synonyms include snowfall, snow flurry, blizzard, and so on.

Using prefixes is also popular, since a prefix often denotes a word family. Words like uncertain, unsuccessful and unfriendly share a prefix of "un" and have a negative, unwanted meaning. While prefixes can only be used in the front of the word, it often makes sense to reverse the word and index the data under the suffix to avoid using a full-text search for the characters at the end. For example, words with the suffix "ful" are often adjectives, describing something.

Prefixes are useful when dealing with numerical and sequential data.

Phonetic matching is another good example of terms. Every human has different pronunciation of words, so it makes sense to break them down into phonetic components to use as terms.

Of course, all these techniques can cause incorrect results for searching (usually too many results), but it is a good trade-off to the general improvement and speed of searches.

MapReduce was invented by Google in order to solve unique problems you encounter when processing huge amounts of data.

Data is usually stored on a platform that is called Hadoop, which is a software library that allows for the distributed processing of large data sets across clusters of thousands of machines using simple programming models.

Following is a (very) simplified case of MapReduce stages: ***map***, ***shuffle***, and ***reduce***.

![](/images/devcor/devcor_12_5_11.png)

The map stage processes the input data. First, the data split into manageable pieces (usually line by line, file by file, and so on) and distributed over the network to mapper nodes. Mappers generate key-value pairs by performing a certain function on the split input.

This example shows the map function counting the number of word occurrences. MapReduce handles much more complex cases. After that, the data is shuffled and sorted from mapper to reduce nodes. Reducer then takes the key-value pairs and runs a reduce function on each pair to produce the final output.

At first glance, MapReduce seems like a simple algorithm that could be created by a fresh college graduate; however the real complexity lies in workload and optimal processing power distribution over thousands of nodes, so writing good mapper and reducer functions is very important.

Match the nonrelational database concept with its correct explanation.

* Data processing technique that distributes processing = MapReduce
* Data analytics tool for fast full-text queries = Elasticsearch
* Data sequencing strategy = Inverted Index
* A copy of data contained in another entry = Embedded Document

### 12.6 Graph-Based Database Concepts

Modeling relations between data is usually secondary to the data itself, but relations can play an important part in adding value to data. Graph-based databases put the focus back on relations and are used when regular databases cannot adequately represent the state of data by itself.

Think about this example from a social media platform.

Bob, Alice, and Clyde follow each other on social media. Alice is a members of a group called "Funny Cats," that was created by Clyde. Recently, Clyde has added a new photograph to the group, which Alice liked.

![](/images/devcor/devcor_12_6_1.png)

How would you model a relation like that using an entity relationship model? Is it even possible?

You would need a table for people, a table for groups, and table for photographs to define the entities. You would also need a table to map each between people. And more tables to map ownership of groups, photographs, and so on.

Modeling relations with regular databases can become too complex very quickly. And when a model is made, queries can be slow, because of the number of **JOIN** operations that are needed to connect all the tables.

Graph databases are based on graph theory, so the data is stored within nodes and edges.

![](/images/devcor/devcor_12_6_2.png)

The concept of nodes is similar to a database record (entity, row, and so on) with a number of key-value properties (attributes). Nodes are often tagged with a label that represents their role in a domain (type). Labels are also used to attach metadata that can contain index or constraint information.

Edges represent directed, semantically relevant relations between nodes (for example, manages, follows, and so on). Each relation must have a direction (or be omnidirectional), a type, a start node, and an end node. Edges can have properties too, but due to the way they are structured, these properties are quantitative (for example, earns, weights, and so on).

Graph databases do not exist by themselves; they are always implemented with another type of database, which at least hides the complexity and simplifies usage. So the underlying representation and storage mechanisms of data vary between different graph databases. Most use document-based databases, while some use a relational engine in the background.

Unlike other databases, relations take priority in graph databases: The connected data is more important than individual entries. Relations are persistent through every part of the data lifecycle, from logical to physical model, and do not play a secondary role as foreign keys.

Graphs that define relations can be divided to represent five broad types. The lines between categories are sometimes not clear and many times several graph types are combined into one:

* **Social graph:** Describes social relations between entities and models social networks. A social graph can be referred to as the mapping of everybody and how they are related. "Lives with," 'is married," and "is friends with" are all examples of social relations. Social graphs are widely used on social media platforms like Facebook.

* **Intent graph:** Describes what people do and why. Intent graphs are tightly bound to personal experience and are commonly a result of other graph analysis. They can tell who wants to buy a new car, or who plans to get married. A common use case is using this type of data for ads and recommendation engines.

* **Consumption graph:** Also known as payment graph, deals with the consumer-vendor relation and their related patterns. A consumption graph is commonly used for marketing purposes and describes terms such as "has bought," "uses daily," and so on.

* **Interest graph:** Represents a person's interests (such as books, rock music, and so on). They are used for creating recommendations and marketing purposes.

* **Mobile graph:** A newer type of graph that is built by mobile data, such as smart phones. In the future, a mobile graph may be built by smart chips, wearables, and IoT. A mobile graph is considered a map of interactions within the given context and collects data such as GPS and location check-ins, web application usage, digital wallet usage, and more. It deals with relations such as "travels to work," "visits webpage weekly," "stays here for 5 hours," and so on.

![](/images/devcor/devcor_12_6_3.png)

![](/images/devcor/devcor_12_6_4.png)

Relational databases rely on indexes and table joins to query data, which becomes a problem when there are several tables with millions of rows involved and you want to run a query that involves a complex relationship between stored data. Graph databases are much faster when working with associative data sets, scale naturally to large data sets, and do not use costly **JOIN** operations.

Relations between nodes are stored in the edge, that is, the reference (or pointer) to the related node is stored explicitly and can be followed directly. There is no need for the database to search for that record in another table as with relational databases. Where a relational database would need to search the whole foreign key database for the specific entry, a graph database can follow the pointer to the designated place in memory, which increases performance and eliminates data redundancy.

True value becomes apparent when queries, more than one level deep, are performed and their usefulness grows with the complexity of the query.

While graph databases may seem to be an improvement compared to classic databases, they have some drawbacks.

* Generally, graph databases do not perform well when processing high volumes of transactions and are not suitable for data warehouses or large volume analytical queries.

* Graph-based databases are good at performing a query that is related to one individual record, but are not useful for mass analytics of data across all the relations.

* Graph-based databases lack standardization and are relatively new, so using a graph-based database may prove harder in the end.

* What are two use cases where a graph-based database is more suitable than another type of database?
  * fraud detection
  * recommendation engine

The correct answers are "fraud detection" and "recommendation engine." Graph-based databases are often used for fraud detection because they can analyze the relations between multiple entities and isolate suspicious entities. Graph-based databases also are used for recommendation engines because they can simply and inexpensively analyze relationship-based queries in real time.

## 12.7 Columnar-Based Database Concepts

Imagine that you are managing a relational database that contains a huge number of rows. You often execute queries on a single parameter, such as calculating the average age of people in your database. Such queries are very slow because every row must be accessed for the age parameter to be read. Improve your queries by using columnar-based databases.

Columnar-based databases (sometimes referred to as wide column databases) are databases that store data in columns instead of rows in contiguous blocks.

![](/images/devcor/devcor_12_7_1.png)

In a regular database, data from each row is stored close together on the disk. With the columnar databases, data from each column is stored together. Each scheme is better suited to the problem that they are trying to solve.

For example, if you are running a web store and use the database for your product inventory, it would make more sense to use a regular row-based database, because most of your queries will be for whole entities. For example, if a customer wants to view a product, a query is run to return all the parameters of the product. But if you want to use the same data for business analysis, it makes more sense to use a columnar-based database, because queries will be run against an attribute (for example the average price of all products), not an entity. So only one row needs to be read, not of all of them. Conversely, accessing all attributes of a particular entity in a columnar database is proportionally slow.

The idea of storing data in columns was born from the fact that seek operations are the most expensive executed on storage. Hard disks are organized into a series of blocks of a fixed size, typically big enough to store a few rows, so related data groups try to fit into the smallest number of blocks possible. Reducing the operational data needed for queries to the relevant elements lets you store all the operational data directly in the memory.

Data structures can be columnar or row-based. A row-based structure uses column indexing, but performance is not as good as using a native columnar database.

It is possible to migrate from a relational row-based database to a column-based database, but the process can be complex and lengthy.

The columnar-based database model allows a few operations to be executed very fast, but other operations are suboptimal.

**INSERT** and **UPATE** operations are usually slower (except maybe for bulk operations on a single attribute). Reading an entity as a whole is also slower. Joining tables together also has worse performance on column-based databases, but JOIN operations are not common there. Online transaction processing (OLTP) systems are better off choosing a row-based database.

Columnar databases perform exceptionally well with range-based queries. All the data is provided in a single row, so doing aggregate operations (AVG, SUM, MIN...) and reading entire data sets (columns) are more suitable for online transaction analysis (OLTA) systems.

Most of the columnar-based databases use a SQL-like syntax, which means you should be familiar with SQL concepts while working with them. As with time series databases, columnar databases can use either the relational or nonrelational model. Deciding which to use is based mostly on the intended use and the potential volume of data.

Normally, database types are not exclusive. It makes sense to combine, for example, columnar and document-based database models to leverage the advantages (and disadvantages) of both.

Columnar-based databases are often used in:

* **Data warehouses:** Structured data repositories that combine hardware and software to enable storage and analysis of data. Columnar-based databases are used as a data source in those warehouses. Data can be uploaded directly from the operational systems or migrated from another type of data storage.

* **Business intelligence (BI):** Often focuses on analysis of a single column or column family of millions of records. Columnar-based databases are perfect for BI, which is why databases such as Cassandra are often used in BI applications by default.

* **Big data:** Often requires both a high volume of data gathering and processing. Databases such as HBase offer out-of-the-box scalability and high availability, with tools for real-time and historical analysis.

* **Library card catalog:** Used to sort books and other library material by categories such as author, subject, or title. The category, by which the books were sorted, is an equivalent of a column in a database.

* **Ad hoc query systems:** Queries that cannot be determined before the moment the query is issued. They are often constructed programmatically in applications and each execution might produce different results (for example, query of several active users on a specific system, to determine where the load balancer should point the next request).

The specific way in which column-based databases store their data gives you even more opportunities for optimization. Entries consist of a value and an internal ID, that is used by the system to reference the row in which the entry is found. This ID is usually a 64 or 128-bit integer.

Compression of columnar data is very efficient due to uniformity of the data.

![](/images/devcor/devcor_12_7_2.png)

Compression algorithms, such as LZW or run-length encoding, perform much better, and have better compression ratios on data that is similar in structure. This characteristic of structure gives better performance than an algorithm handling seemingly random data, because the data contains many similar sequences. This compression ratio improvement reduces the footprint on a hard drive, but retrieval is less efficient. Although some column-based operations can run on compressed data, others need to decompress the data first.

To maximize compression efficiency, one should also use low-cardinality (for example, Boolean values, enumerators, and so on) columns first.

Columnar databases often support parallel processing of queries with Single Instruction Multiple Data (SIMD) instructions and vector processing.

![](/images/devcor/devcor_12_7_3.png)

Single Instruction Multiple Data (SIMD) instructions allow you to execute some queries in parallel, using a single CPU instruction for multiple values.

Instead of using a CPU instruction for a single value, values are vectorized and processed as a vector, which saves processing power since each CPU operation has some overhead.

Which two statements about columnar databases are true?
* Data is saved in columns
* Aggregation queries are fast

## 12.8 Time Series Database Concepts

Time series databases are an example of specialized databases. They are designed to efficiently store any kind of data that changes over time.

At first glance, time series databases are essentially key-value databases. The key represents a specific time and date notation. This time and date scale can differ greatly and depends on the use case, however the same amount of time usually passes between each entry in a time series database.

A sensor that measures laser temperature in a factory will operate on a much smaller timescales and date time notations than a thermometer that logs the daily average temperatures in a region.

The value is a metric—usually a kind of measurement, system state, or something more complex, and can be a single value or a collection of values. An example of a single value could be the total amount of energy that is created by a power plant. An example for a complex value could contain sensor ID, sensor location, and a sensor measurement or a model of river flow over time, when metric values represent vectors that the system can create a 3-D time model.

![](/images/devcor/devcor_12_8_1.JPG)

![](/images/devcor/devcor_12_8_2.png)

Time series databases have solidified their place in the industry and are popular in:

* **System monitoring:** Each system typically contains some run time and hardware metrics like component temperature and load, disk I/O operations per second (IOPS), free and used memory, and so on. Storing this data can allow you to predict usage spikes, detect hardware malfunctions, performance bottlenecks and much more.

* **Finances:** Finance applications use time series data to either keep track of assets like stocks and currencies, their price and the trends over time, or can be used to store financial transactions and their metadata. Autonomous trading algorithms can use this data.

* **IoT:** With IoT becoming more present in everyday lives, the number of sensors and their data points increases. An IoT device that measures air quality can contain multiple sensors like temperature, pressure, humidity, pollution, and CO2 levels. Each one gets logged with a time stamp and an ID of the device and the sensor.

* **Asset management:** Time series data is often a part of business applications for real-time and historical data analysis, which helps identify trends and manage assets.

* **Machine learning:** Predictive analysis often uses machine learning and neural networks combined with time series data to make predictions about future trends and warn you about anomalies.

Time series databases have two main goals:

* First goal:
  * Identifying the nature of the phenomena under observation.

* Second goal:
  * Forecasting future values of the time series.

* Both require an identified pattern of observed data!

![](/images/devcor/devcor_12_8_3.png)

A pattern in time series data consists of two components:

* **Trend:** Represents a general systematic change of a component that does not repeat within the selected time frame. An example of a trend would be the average yearly temperature on Earth, since it is increasing. Trends are mostly nonlinear, since it is hard to find linear data in real-life examples.

* **Seasonality:** Has a similar nature as a trend, but repeats itself in systemic intervals. For example, the traffic density in an area varies throughout the day, but has a noticeable increase during the morning commute to work. Such a pattern can be observed every working day.

![](/images/devcor/devcor_12_8_4.png)

![](/images/devcor/devcor_12_8_5.png)

To get the best representation of your data, separate the long term trends and seasonal influences from random fluctuations and errors by smoothing, which is a process of localized averaging of data.

If the time series follows a single trend, smoothing can be achieved by using a linear or polynomial function.

For less predictable data, other smoothing techniques are used:

* **Moving average:** A moving window with an average of its values with size of n is used to replace each value in the time series. The wider the window, the smoother the data will be. This is a very simple method and easy to implement, however it is not particularly good at detecting short-term anomalies.

* **Exponential smoothing:** Time windows are used to average data, but not all entries are weighted equally. The model uses an exponentially decreasing weight for past observations. Specifically, past observations are weighted with a geometrically decreasing ratio.

Time series databases have some unique benefits and challenges to their implementation.

Data points are immutable, which means they do not change anymore when written. Most of the time they are already organized into a series too. If a data point is missing here and there (because of bad connectivity, hardware errors on sensors) this also creates little to no impact on the overall data quality.

ACID compliance is also not enforced due to those reasons.

Most of the operations are simple **INSERT** commands, performed at regular time intervals. Since time series track changes to data over time, **UPDATE** statements should not be used, because when preserving the time series nature of the data, the information that it contains is preserved too. Smoothing techniques are commonly used to create new representations of time series, while preserving the original data.

Time series is exceptionally fast and very scalable (which it needs to be), because it does not depend on existing data.

Modern telemetry and logging systems produce an incredible amount of data every day. Self-driving cars produce a few thousand gigabytes of data each day and even though NoSQL databases are made to handle large amounts of data, time series outperforms them in this case.

Time series data is usually very compressed.

### Round-Robin Databases

![](/images/devcor/devcor_12_8_6.png)

Sometimes you are only interested in detailed operational data. The precision of data points that are older than a day, month, or year, offers no real advantages over averaged data of that time frame.


Round-robin databases are often used to store monitoring data such as temperature, CPU load, or network bandwidth. In the short term, each data point is much more interesting than individual data points from some point in the past, where a general trend matters more.

Round-robin databases store data points at a regular interval (called a heartbeat) for a limited amount of time, then use a consolidating function to store the consolidated data in round-Robin archives, which are separate files that are then used to query the data or use it for a visual representation.

The database footprint on the system is static and relatively small, but still gives you useful data.

The term round-robin comes from the fact that each data point points to the next one. Because the database size is determined at creation, the last data point points to the first one again, forming a circular model.


### Time Series Database Types

Various time series database management systems (TSDBMSs) have evolved primarily around their use cases. Some of them come with built-in advanced functionalities such as data visualization, web dashboards, and indexing and querying tools.

![](/images/devcor/devcor_12_8_7.png)


### InfluxDB

```bash
  value = from(bucket: "sensorData") 
      |> range(start: -1h) 
      |> filter(fn: (r) =>
            r._measurement == “cpu" and 
            r._field == “load" )
```

### TimescaleDB

```bash
  SELECT time, cpuLoad AS value FROM sensorData 
  WHERE time > now() - '1 hour';
```

In the example, a query is made that displays the CPU load in the last hour, which shows, that time scale databases can be implemented either as a relational database (TimescaleDB) or as a NoSQL database (InfluxDB). The former are a bit less popular, since relational databases tend to be worse at scaling and handling large amounts of data, but are still used where using relations is a requirement, since data points often contain multiple IDs and complex queries that can be beneficial.

While you can index data, the amount of it will keep growing and growing and the index will become slower and slower. A solution, often supported out-of-the-box in TSDBMS, is to internally split table rows into chunks with database partitioning, where a chunk represents a fixed size or an adaptive data set which can be based on time, location, function, and so on. A fixed size chunk will store data for a fixed set (monthly tables for data), but an adaptive chunk will be mostly limited by the data set size (limiting log files to 10 MB). Chunks are then stored in their own tables and not as a part of the primary table, which makes batch operations and indexes faster, and data set management easier (for example, backing up data sets separately, deprecating older data sets, and so on.

Which three concepts are related to a time series database? 
* trend
* seasonality
* metric