# My REST Payroll Service Demo

In this project, I create a simple payroll service that manages the employees of a company. I store employee objects in a (H2 in-memory) database, and access them via JPA. This is then wrapped using the Spring MVC layer to allow access over the internet.

How to run and interact with the application:
1. After forking the repository and running the initial build, right click the PayrollApplication class and click Run. In the output log, you'll notice the preloaded data (i.e. first employees and first orders).
2. To launch the application, right click the main() method in the PayrollApplication class and click run.
3. When the app starts, we can interrogate it with the following terminal command: curl -v localhost:8080/employees . You will see the preloaded data but in a compacted HAL form. If you try to query an employee that doesn't exist, a nice HTTP 404 error with a custom message (defined in EmployeeNotFoundAdvice).
4. Create a new employee record with the following terminal command: curl -v -X POST localhost:8080/employees -H 'Content-type:application/json' -d '{"name": "Samwise Gamgee", "role": "gardener"}'
6. To update the newly added employee, run the following terminal command: curl -X PUT localhost:8080/employees/3 -H 'Content-type:application/json' -d '{"name": "Samwise Gamgee", "role": "ring bearer"}'
7. You can delete the employee with the following terminal command: curl -X DELETE localhost:8080/employees/3
8. To interact with the order side of the application, you can run the above commands but tweaked for orders. For example, curl -v http://localhost:8080/orders .
9. If you notice an order with status IN PROGRESS, you can run the following command: curl -v -X DELETE http://localhost:8080/orders/4/cancel . Be careful to use the right id number!
10. The response to successfully canceling an order should be an HTTP 200 response. Otherwise, an HTTP 405 Method Not Allowed response will be outputed.

TIP: When your curl output gets more complex it can become hard to read. Use the | json_pp to make your JSON output more readable: curl -v localhost:8080/employees/1 | json_pp

The following tactics helped make my services less likely to break existing clients I may or may not control:

* Don’t remove old fields, but support them (i.e. field name evolving to fields firstName and lastName).
* Use relational-based links so clients don’t have to hard code URIs.
* Retain old links as long as possible. Even if the URI needs to be changed, keep the rels so older clients have a path onto the newer features.
* Use links, not payload data, to instruct clients when various state-driving operations are available.
  
Although building up RepresentationModelAssembler implementations for each resource type and to use these components in all of my controllers may take some extra time and energy, this extra bit of server-side setup (made easy thanks to Spring HATEOAS) can ensure that all clients (both in or out of my control) can upgrade with ease as I evolve my API.

A small vocabulary list:
* nonrest — Simple Spring MVC app with no hypermedia
* rest — Spring MVC + Spring HATEOAS app with HAL representations of each resource
* evolution — REST app where a field is evolved but old data is retained for backward compatibility
* links — REST app where conditional links are used to signal valid state changes to clients
* HAL - a lightweight mediatype that allows encoding not just data but also hypermedia controls, alerting consumers to other parts of the API they can navigate toward.
* HATEOS - Hypermedia as the Engine of Application State
