# My REST Payroll Service Demo

In this project, I create a simple payroll service that manages the employees of a company. I store employee objects in a (H2 in-memory) database, and access them via JPA. This is then wrapped using the Spring MVC layer to allow access over the internet.

The following tactics helped make my services less likely to break existing clients I may or may not control:

* Don’t remove old fields, but support them (i.e. field name evolving to fields firstName and lastName).
* Use relational-based links so clients don’t have to hard code URIs.
* Retain old links as long as possible. Even if the URI needs to be changed, keep the rels so older clients have a path onto the newer features.
* Use links, not payload data, to instruct clients when various state-driving operations are available.
  
Although building up RepresentationModelAssembler implementations for each resource type and to use these components in all of my controllers may take some extra time and energy, this extra bit of server-side setup (made easy thanks to Spring HATEOAS) can ensure that all clients (both in or out of my control) can upgrade with ease as I evolve my API.

A small vocabulary list:
nonrest — Simple Spring MVC app with no hypermedia
rest — Spring MVC + Spring HATEOAS app with HAL representations of each resource
evolution — REST app where a field is evolved but old data is retained for backward compatibility
links — REST app where conditional links are used to signal valid state changes to clients
