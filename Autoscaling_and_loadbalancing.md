# Autoscaling and Loadbalancing

Auto scaling, also referred to as autoscaling, auto-scaling, and sometimes automatic scaling, is a cloud computing technique for dynamically allocating computational resources. Depending on the load to a server farm or pool, the number of servers that are active will typically vary automatically as user needs fluctuate.

Autoscaling is a cloud computing feature that enables organizations to scale cloud services such as server capacities or virtual machines up or down automatically, based on defined situations such as traffic ir utilization levels. Cloud computing providers, such as Amazon Web Services (AWS), Microsoft Azure, and Google Cloud Platform (GCP), offer autoscaling tools.

[What is  Auto scaling?](https://avinetworks.com/glossary/auto-scaling/)

Useful for managing microservices. We also do not know when demand for service changes.

Auto scaling group - configures launch templates then spins up the instances. Has an autoscaling policy.

Autoscale policy - Specify the launch templates. Set threshold - when it checks to make more instances (scale out), what the minimum and maximum number of instances. (Ours will be min 2, desired 2, max 3, Scales out at CPU 50%).

Loadbalancer - receives then directs/distributes traffic to instances.
