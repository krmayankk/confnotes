6/6/2019 Systems@Scale 2019
---------------------------

## Benjamin Reed, Assistant Professor, San Jose State University
 He was talking about scale up vs scale out and walked through a few lessons.

### lesson1: scale up is rarely an option, today app needs to adapt to problems and be manageable

### lesson 2: doc isnt only for users . It can help others engage with your poroject 

- shmproxy: at yahoo ,abstracted scale out portion of servers, came with 100 pages of documentation
- changing configuration files needed coordination with lot of people

classic controller architecture of hadoop
- controlelr ran on single machine, scale up for controller
- manage workers, configuration 

### lesson 3: at scale, app developers should be focussed on app logic not implementing disributed system primitives

- zookeeper invented at yahoo
- the idea of a general purpose dedicated scale out controller that other people can use -zookeeper

### lesson4: the perfect solution doesnt look that great to people who havent seen the problem

### lesson5: picking the right abstraction makes all the difference
### lesson6: keep it simple and scoped to the resources you have

- talking about split brain in zk, at yahoo someone shipped a single server zk configuration to multiple zk servers which all believed
they were the master
- this was in early days when zk was just invented

### lesson7: developing critical reliable services is exciting at first but terrfiying when deployed

final lesson: open source and sharing of ideas allows coopetition and allows the industry to progress as a whole

## Cloudera talk on Apache Hive
- low latency sql engine for hadoop
- option 1: presto
- option 2: hawq or hive

## Delos - storage for fb control plane
All the details of this talk published here https://code.fb.com/data-center-engineering/delos/

## Better mem organization for lsm key value stores - Verizon
Talked about https://en.wikipedia.org/wiki/Log-structured_merge-tree

- Accordion - lsm key value store
- LSM in hbase
- talked about flattening the skip list to a flat list to reduce fragmentation
- https://dl.acm.org/citation.cfm?id=3275553

## Distributed Tracing at Uber and Fb

- Tracing at scale
- observablity of distributed transactions is paramount
- observability requires instrumentation
- software is high composable from open source software
- tracing breaks if components dont understand each other
- Canopy at facebook for tracing
- Uber using trace visualizations to discover root causes
- Uber used it for distributed debugging while facebook for performance analysis and regressions- different use cases
- Some interesting REST APIS on the slides /eats/v1/eaters/:eaterUUid/order
- Uber difference of approach
- surface less information
- condense the structural representarion
- emphasize the differences
- compare trace with aggregate traces as example of successfull behavior over time
- scale your infra-by identifying perf and efficiency bottlnecks
- scale your org- by helping automate root cause anlysis

## Continuous Deployment at Facebook
- Conveyor, compared to jenkins and spinnaker
- Sandcastle for build, integrate and test

## Observability Infra at Affirm
- deals with financing when buying things
- affirm downtime incident discussion
  - tasks for underwriting users was down
  - because of no monitoring, notificationsm, alerting
- metrics goal
  - observability, reliability and usability
- talked about 99th percentile latency increase correlated with disk queue length
- root cause was misconfigured EBS volumes, ssd was mounted in the wrong place
- and reads were happening from root folder rather than ssd disk
- disk queue length showed same pattern as latency graph which led to diagnosis
- took a sprint to work on reducing error rates and improving ops
- loved how they were using very arcane and old tools but doing the write things
  for their instrumentations and observability
- takeways
  - grow monitoring solution as business grows
  - build a metric system with good observaility, reliabulity and usability
  - automate metrics creation for important use cases

## PYMK from linkedin
- help recommending other members to connect to 
- venice --->key value store with scoring
- PYMK offline infgerence
 - candidate generation
 - feature generation
 - scopring and ranking

## Scaling Cluster Management at Facebook with Tupperware
Kenny Yu, Software Engineer, Facebook  

### Random notes
- 8 yr old
- All the details of this talk published here https://code.fb.com/data-center-engineering/tupperware/
- Transparent Sharding of the Control Plane 
- A single control plane managing servers across data centers
- Ability for a server to be dynamically managed by different control planes
- Ability of a Job to move across clusters 
  helps automate cluster decommission and maintenance, and allows dynamic capacity shifts via elastic compute.
- Seamless support for Stateful Services
- A TaskControl Interface that allows the TupperWare Control plane to communicate with Stateful Services about update,           restart, maintenance and migration events
- Not Only spread stateful services across fault domains, but using the ShardManager framework, allow developers to specify     their intent for how data shards should spread across containers
- Dynamically adjust the speed of update across containers
- They are consolidating their infrastructure into a large global shared fleet of servers from hundreds of small server pools
- Their use of small power efficient servers and Hence, they encourage developers of large services to heavily optimize their   services to utilize entire servers to prevent fragmentation

### Some Notes from Happy Hour Chats
- Talked to Kenny Tupperware(Container Orchestrator for Facebook) around lifecycle of sidecars and here are some quick points   I learned
- they dont dynamically inject sidecars, or in other words, they dont have a webhook injection model
- they inject sidecars at compile time when creating the declarative spec like how we would do in Helm charts
- when testing a new version of sidecar , they start injecting the new version in x% of new deployments happening  where x% is   configurable
- the model is not forced, so the new sidecar is only injected when the customer pipeline runs to deploy a new version of       their app
- they assume that their new version of sidecar is backward compatible with old versions

### My observations
- They do spreading across multiple topology domains, but they didnt have full details on the behavior
- Their support for stateful apps seemed very comprehensive


## Preemption in Nomad
- Nomad is container orchestrator from Hashicorp(https://www.nomadproject.io/)
- declarative, takes description of app and nodes and deploys
- just one binary which can act as client and server as well run as dev mode
- based on borg, omega and UC berkely sparrow research papers
- 15k+ node deployments
- containerized as well as legacy apps
- multi region federation
- use a single job spec in HCL
- primary one is bin packing algorithm
- priority inversion solved using preemption
- they think about workflows rather than technology first(which is just a enabler for workflows)

## Disaster recovery at Facebook
- forest city dc was closed to hit by sandy
- forest city had primary data
- can we run facebook without one of our dc's
- want to drain dc's when hurricane is approaching a dc, to direct traffic from that dc to other dc's 
- service dependencies lead to draining systems gracefully
- DC's have minimum number of services to survive dC failure
- talked about partially draining dc
- approach
 -- cap proivisioning and data replic
- more evenly hw spread to be less impacting because of hw failurea
- even hw spread doesnt mean even software spread
- most stateless services are managed by tupperware
- constraint specification language to enable app to require availability requirements
- max_unavailable_hosts =
