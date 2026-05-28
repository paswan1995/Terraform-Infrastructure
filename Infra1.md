# Traditional E-Commerce Application

* Idea: We want to setup an ecommerce website where we sell electronics, mobile phones etc

* Initial Decisions: 
    * Start with existing opensource ecommerce and start selling asap
    * Evaluate all possible infrastructures.

* Solutions:
    * Find an ecommerce application which fits my purpose
    * Team is tilted towards [nopCommerce](https://www.nopcommerce.com/en)

# Evaluating infrastructure

* Understand Application Architecture
![Preview](images/1.png)

* diagram it shows how an application is structured and deployed across servers, including:
    * Application layers
    * Operating systems
    * Database choices
    * Infrastructure decisions

* This diagram represents application architecture evaluation where we analyzed different OS and database options. Initially, the system supported both Windows and Linux along with multiple databases like MySQL, PostgreSQL, and SQL Server.
* After evaluation, we standardized the stack to Ubuntu Linux, Nginx, and MySQL for better performance, cost optimization, and scalability. 

* Made some os/software choices
![Preview](images/2.png)

* Possible Infrastructural choices
    * Buy Servers and run it in our organization (On-premises)
    * Try cloud 
    * Try SaaS(nopCommerce supported hosting)

* Managing Datacenters
    * Networking 
        * Switiching
        * Routing 
    * Racks 
    * Servers
    * Virtualization 

* From network perspective
    * we have 2 servers 
        * web: should be accesible from internet 
        * db: should not be accessible from internet 

* ``how to install mysql on linux and how to take mysql  database backup?``

* The above case poses a challenge in a on-premise environments as i need to design DMZs ( De militarized Zones)
  
  ![Preview](images/3.png)

    * This diagram represents a secure enterprise architecture using a DMZ. Incoming traffic from the internet first passes through an external firewall and reaches the DMZ where web servers are hosted.
    The DMZ isolates public-facing services from internal systems. Then traffic passes through an internal firewall to reach application and database layers inside the intranet.
    This layered approach ensures security, access control, and protects sensitive systems from direct exposure. 

* Cloud in this case seems sensible as we can come up with Application, Quickly and securely as well
* Cloud offers Platforms as a Service (PaaS) where we get databases directly and configuring backups is one-click
* To run website we have two options

    * IaaS:
        * We get ubuntu linux vm
        * installing all necessary softwares
        * maintaince (updates/patches) is our responsibility

    * PaaS
        * We get .net platform
        * directly deploy nopCommerce

* Summarize:
    * We prefer cloud
    * For mysql database we need a PaaS
    * For nopComemrce web application we got with IaaS (virtual machines)

* IaaS vs PaaS vs SaaS

![Preview](images/4.png)


