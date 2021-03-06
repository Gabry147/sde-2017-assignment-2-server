# SDE Second Assignment Delivery - Server 
#### Gabriele Scarton
###### gabriele.scarton@studenti.unitn.it
This is the second assignment for IntroSDE course at Unitn.
The goal of the assignment is to create a rest service composed by a server and a test client.
The server is deployed on Heroku at **sde2-2017-scarton.herokuapp.com**
## Specifications
  - Dependency manager: Ivy
  - Build tool: Ant
  - Rest framework: Jersey 
  - Database: Postgresql (provided by Heroku)
  - Persistency unit implementation: Hibernate

##  The code structure
The server contains its _build.xml_ file, the _ivy.xml_ dependencies file, the source code and the _WebContent_ folder with the _web.xml_ file.
The _it.gabry147_ folder contains three packages:
  - **entities** contains all the entities used. All classes have Jaxb annotation for XML marshalling. For Json, Jackson used the same annotation, with some difference: the Date visualization requires a Json annotation and the classes wrapped in another class need a parametrized constructor to unmarshall them. Only Person, Activity and ActivityType are saved in the db using persistency annotation
  - **dao** contains the class with basic method for accessing the persistence data and a class for populate the db at deploy time. 
  - **resources** contains the Jersey-related code to process the request and a ServletContextListener implementation in order to call the class for db population
  - the last folder is the one which contains the persistence.xml file

## The code logic
- **entities** package has only the functions required to retrieve and save the data from/to the db.
- **dao** package has only two classes: the DAO, which provides basic function to access the db (such transaction managing) and the DBinitializer class. This class has the methods to create the ActivityTypes and to create random people with random activities to have a minimum number of person. If the db already has the minimum number of person, the function returns the initial people in the db. These methods are called at every deploy and restart of the server.
- **resources** package has a class for each iitial url (_/_, _/init_, _/activity_types_ and _/person_) and a implementation of the ServletContextListener. The class for base url return the string "Started" in order to tell the client that the dyno is restarted successfully, the class for init calls the methods in DBinitializer class, the class for the types return the type list at every GET request and the class for person implements all other request of the assignment. The class for init should be use manually by the client, in order to avoid client interaction the class MyServletContextListener implements ServletContextListener and put the DBinitializer methods in the contextInitialized() method. In this way the db is always filled with data, at the cost of checking it at every deploy (remember that the function creates random people only if the db doesn't have the minimum number of people).

## How to run the code
> For security reason, the persistence.xml file does not contain the data of the db used. If you want to use this code in a real life scenario, remember to modify the file, which is under /it/gabry147/META-INF

The server is packed into a .WAR file, automatically generated by Ant with the console command:
``` 
$ ant create.war 
```
