# DNS 101 

Route53 - DNS is on the port 53 

#### What is DNS 
Like a phonebook, DNS is used to convert human friendly domain names (such as http://google.com) into an internet protocol (IP) address (such as http://82.125.123.1).

IP addresses are used by computers to identify each other on the network. IP addresses commonly come in 2 different forms, IPv4 and IPv6 

#### IPv4 vs IPv6
The IPv4 space is a 32 bit field and has over 4 billion different addresses. 

IPv6 was created to solve this depletion issue and has an address space of 128bits which in theory is 340 undecillion addresses. 

#### Top Level Domains
If we look at common domain names such as google.com, yahoo.com, etc, you will notice a string of characters separated by dots. The last word in a domain name represents the "top level domain". The second word in a domain name is known as a second level domain name (this is optional though and depends on the domain name).
- .com
- .edu
- .gove
- .co.uk
- com.au

These top level domain names are controlled by the Internet Assigned Number Authority (IANA) in a root zone database which is essentially a database of all available top level domains. 

#### Domain Registrars
Because all of the names in a given domain name have to be unqie there needs to be a way organize this all so that domain names aren't duplicated. This is where domain registrars come in. A registrar is an authority that can assign domain names directly under one or more top-level domains. These domains are gistered with InterNIC, a service of ICANN, which enforces uniqueness of domain names across the Internet. Each domain name becomes registered in a central database known as the WhoIS database. 

Some Domain Name resgistrars include
- Amazon
- GoDaddy
- Google Domains 
- etc

#### Start of Authority Record (SOA)
**The SOA record stores information about**
- The name of the server that supplied the data for the zone
- The administrator of the zone
- The current version of the data file
- The default number of seconds for the time-to-live file on resource records

#### NS Stands for Name Server Records
They are used by Top Level Domain servers to direct traffic to the Content DNS server which contains the authoritative DNS records 

![[firefox_COKwxisWRM.png]]

#### A Records 
**What's an A record?**

An "A" record is the fundamental type of DNS record. The "A" in A record stands for address. The A record is ued by a computer to translate the name of the domain to an IP address. 

e.g. www.google.com --> http://123.125.125.35 

#### What's an TTL?
The length that a DNS record is cached on either the Resolving Server or the users own local PC is equal to the value of the "Time To Live" (TTL) in seconds. The lower the time to live, the faster changes to DNS records to propogate throughout the internet. 

The default TTL is 48 hours. 

#### What's a CName?

A Canonical Name (CName) can be used to resolve one domain name to another. For example, you may have a mobile website with the domain name http://m.exampleme.com that is used for when users browse to your domain name on thier mobile devices. You may also want the name http://mobile.example.com to resolve to this same address

#### Alias Records 
Alias records are used to map resource record sets in your hosted zone to Elastic Load Balancers, CloudFront distributions, or S3 buckets that are configured as websites. 

Alias records work like a CNAME record in that you can map one DNS name (www.example.com) to another 'target' DNS name (elb123.elb.amazonaws.com)

Key difference - A CNAME can't be used for naked domain names (zone apex record). You can't have a CNAME for http://google.com it must either an A record or an Alias

### Exam Tips 
Route53 
- ELBs do not have a pre-defined IPv4 addresses; you resolve to them using a DNS name
- Understand the difference between an Alia Record and a CNAME
- Given the choice, always choose an Alias Record over a CNAME

Common DNS Types
- SOA records
	- Start of authority
- NS records
- A Records
- CNAMES
- MX records
	- used for mail
- PTR records 
	- essentially, the reverse of a A record; looking for a name against an ip address

# Register a Domain Name - Demo 
## Registering A Domain Name
AWS Console --> Route53 --> Register Domain Name --> enter domain name, check if it's available --> add it to cart and purchase the domain name 

Under Route53 --> Registered Domains --> you can view all your registered zones

**Provisiong our EC2 Instance**

EC2 Console --> Launch an instance, with an apacher server and create a simple web page --> Select the security group --> launch 

*Now do the same two more times in different regions*
- if you haven't used these regions before you'll have to setup security groups before; and get your key pairs
Make sure to use a different security group and open the HTTP ports
and create new keypairs 

### Exam Tips 
- You can buy domain names directly with AWS
- It can take up to 3 days to register depending on the circumstances 

# Route53 Routing Policies Available on AWS

The following routing policies are available with Route53

- Simple Routing
- Weighted Routing
- Latency-based Routing
- Failover Routing
- Geolocation Routing
- Geoproximity Routing (traffic flow only)
- Multivalue Answer Routing

# Simple Routing Policy - Demo 
If you choose the simple routing policy you can only have one record with multiple IP addresses. If you specify multiple values in a record, Route53 returns all values to the user in a random order

![[firefox_daCd5EwRVq.png]]

**Take a note of your EC2 Ip addresses**

Route53 Console --> Hosted zones --> Create Record Set --> Right hand pane: provide it a name, under value: paste in the EC2 instace ip addresses in separate lines --> Create 

### Exam Tips 
Simple Routing Policy 

If you choose the simple routing policy you can only have one record with multiple IP addresses. If you specify multiple values in a record, Route53 returns all values to the user in a random order

# Route53 Weighted Routing Policy - Demo 
Allows you to split your traffic based on different weight assigned. For example, you can set 10% of your traffic to go to US-EAST-1 and 90% to go to EU-WEST-1

![[firefox_WexcFMGAwS.png]]

Route53 --> Hosted Zones --> Create Record Set --> right hand pane: 
provide it name, in value: enter the EC2 ip address, just one of them EC2 ip address, in weight specify the percentage (e.g. 20), set id: just a name --> Create

Now do the same for another record set --> Enter a difference EC2 ip address, provide it a weight, and an ID --> Create

You can create more if you have more EC2 instances and wish to weight traffic out further.

#### Health Checks 
When creating a record set --> on the right hand pane: in the lower end, there is an option to assocaite the record set with "Health Check": this is when an EC2 instance is failing a health check, Route53 will remove it from the DNS. 

To enable this, we need to create a health check first.

**Creating Health Check**

Route53 Console --> Health Checks --> Create Health Check --> provide it a name, enter ec2 ip address, enter the domain name to run the check on, enter the path ("index.html") --> Next --> You can create an alarm as well --> Create Health check

### Exam Tips 
Health Checks
- You can set health checks on individual record sets
- If a record set fails a health check it will be removed from Route53 until it passes the health check
- You can set SNS notifications to alert you if a health check is failed

# Route53 Latency-Based Policy - Demo
Allows you to route traffic based on the lowest network latency for your end user (is which region will giev them the fastest response time) 

To use latency-based routing, you create a latency resource record set for the Amazon EC2 (ELB) resource in each region that hosts your website. When Amazon Route53 receives a query for your site, it selects the latency resource record set for the region that gives the used the lowest latency. Route53 then responds with the value associated with that resource record set. 

![[firefox_0Yi6gngvbn.png]]

AWS Console --> Route53 --> Hosted Zones --> Create Record Set --> in value provide an ec2 ip address, routing policy select latency, select region, and set ID --> Create

![[firefox_AerPLSRIdS.png]]
And it will choose the one that's lowest

**You can do the same for the other EC2 instances**

# Route53 Failover Routing Policy - Demo 
Failover routing policies are used when you want to create an active/passive set up. For example, you may want your primary site to be in EU-EAST-1 and your secondary DR site in AP-SOUTHEAST-2

Route53 will monitor the health of your primary site using a health check

A health check monitors the health of your end points

![[firefox_eYBIslbiLr.png]]

Route53 Console --> Hosted Zones --> Create Record Set --> provide name, set ec2 instance ip address, routing policy to "Failover", let it know if it's primary or secondary  --> Create 

![[firefox_UYJel1iG4P.png]]

Now the same but change the primary to secondary.

# Route53 Geolocation Routing Policy - Demo

Geolocation routing lets you choose where your traffic will be sent based on the grographic location of your users (ie the location from which DNS queries originate). For example, you might want all queries from Europe to be routed to a fleet of EC2 instances that are specifically configured for your European customers. These servers may have the local language of your European customers and all prices are displayed in Euros.

![[firefox_Z12EVsrXYB.png]]

AWS Console --> Route53 --> Hosted Zones --> Select Zone --> Create Record Set --> value: enter ec2 instance ipaddress, Routing: Geolocation, Location: can be continent or country, provide a set id --> Create

![[firefox_qESNntFCRd.png]]

Create another record set, and paste in a different region ip address, and select a different location. 

![[firefox_8s46AfDJo3.png]]

# Route53 Geoproximity Routing Policy - Demo 
Geoproximity routing Amazon Route53 route traffic to your resources based on the geographic location of your users and your resources. You can also optionally choose to route more traffic or less to a given resource by specifying a value, known as a bias. A bias expands or shrinks the size of the geographic region from which traffic is routed to a resource. 

**To use geoproximity routing, you must use Rout53 traffic flow**

Route53 Console --> Traffic Policies --> Create Traffic policies --> Provide it a name --> Next --> Add Ipv4, use groproximity rule --> associate the locations and health checks --> Create 

  # Route53 Multivalue Answer Policy - Demo 
  
  Multivalue answer routing lets you configure Amazon Route53 to return multiple values, such as IP addresses for your web servers, in response to DNS queries. You can specify multiple values for almost any record, but multivalue answer routing also lets you check the health of each resource, so Route53 returns only values for healthy resources.
  
  **This is similar to simple routing however ti allows you to put health check son each record set**
  
  Route53 Console --> hosted zone --> Create record set --> value: enter ec2 instance ip address, routing policy: Multivalue Answer, set id --> Create
  
  ![[firefox_qV7mbgu9xu.png]]
  
Do the same with a different region ec2 ip address, and set it to multivalue.

![[firefox_HkpZrSUidi.png]]
Basically simple routing with health checks

# DNS Summary 
Route53 
- ELBs do not have pre-defined IPv4 addresses; you resolve to them using a DNS name
- Understand teh difference between an Alias Record and a CNAME
- Given the choice, always choose an Alias Record over a CNAME

Common DNS Types 
- SOA Records
- NS Records
- A Records 
- CNAMES
- MX Records
- PTR Records

Routing Policies available with Route53
- Simple Routing
- Weighted Routing
- Latency-based Routing
- Filaover Routing
- Geolocation Routing
- Geoproximity Routing (traffic flow only)
- Multivalue Answer

Health Checks 
- You can set health checks on individual record sets 
- if a record set fails a health check it will be removed from Route53 until it passes the health check
- You can set SNS notifications to alert you if a health check is failed

Simple Routing Policy 
If you choose the simple routing policy you can only have one record with multiple IP addresses, If you specify multiple values in a record, Route53 returns all values to the user in a random order 
![[firefox_TPMkbznNDC.png]]

Weighted Routing Policy 
![[firefox_Y1mMMgUBCS.png]]

Latency Routing Policy 
![[firefox_lLNpok4qa2.png]]

Failover Routing Policy 
![[firefox_bJTimf2gOr.png]]

Geolocation Routing Policy 
![[firefox_r7QUWjaq6I.png]]

# Quiz 
1. Which of the following Route 53 policies allow you to route data to a second resource if the first is unhealthy, and route data to resources that have better performance? 
	- Failover Routing and Latency-based Routing
		- Failover Routing and Latency-based Routing are the only two correct options, as they consider routing data based on whether the resource is healthy or whether one set of resources is more performant than another. Any answer containing location based routing (Geoproximity and Geolocation) cannot be correct in this case, as these types only consider where the client or resources are located before routing the data. They do not take into account whether a resource is online or slow. Simple Routing can also be discounted as it does not take into account the state of the resources.
2. Route 53 is Amazon's DNS Service.
	- True
		- Route 53 is Amazon's highly available and scalable cloud DNS web service.
3. You have an enterprise solution that operates Active-Active with facilities in Regions US-West and India. Due to growth in the Asian market you have been directed by the CTO to ensure that only traffic in Asia (between Turkey and Japan) is directed to the India Region. Which of these will deliver that result?
	- Route 53 - Geolocation routing policy, Route 53 - Geoproximity routing policy
		- The instruction from the CTO is clear that that the division is based on geography. Latency based routing will approximate geographic balance only when all routes and traffic evenly supported which is rarely the case due to infrastructure and day night variations. You cannot combine blacklisting and whitelisting in CloudFront. Weighted routing is randomized and will not respect Geo boundaries. Geolocation is based on national boundaries and will meet the needs well. Geoproximity is based on Latitude & Longitude and will also provide a good approximation with potentially less configuration.
4. Your company hosts 8 web servers all serving the same web content in AWS. They want Route 53 to serve traffic to random web servers. Which routing policy will meet this requirement, and provide the best resiliency?
	- Multivalue Routing
		- R53 Multivalue lets you respond to DNS queries with up to eight IP addresses of 'healthy' targets. Plus it will give a different set of 8 to different DNS resolvers. The R53 Simple policy will provide a list of multiple instances in random order, but Multivalue is the AWS preferred option for this type of service. [https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-policy.html "null") [https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-failover-simple-configs.html](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-failover-simple-configs.html "null")
5. True or False: There is a limit to the number of domain names that you can manage using Route 53.
	- True and False. With Route 53, there is a default limit of 20 domain names. However, this limit can be increased by contacting AWS support.
		- The limit is 20 for new customers as of March 2021. If you have an existing account and your default limit is 50 now, it will remain at 50. Reference: [Amazon Route 53 Quotas](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/DNSLimitations.html#limits-api-entities "null").
6. You have created a new subdomain for your popular website, and you need this subdomain to point to an Elastic Load Balancer using Route53. Which DNS record set type (or DNS extension type) could you create? (Choose 2).
	- CNAME, Alias
		- CNAME maps to the host name. An alias could be created for the ELB. Alias records provide a Route 53–specific extension to DNS functionality
7. Route 53 is named so because **\_\_\_\_**.
	- The DNS Port is on Port 53 and Route 53 is a DNS Service.
8. You are hosting websites in the eu-west-2 and ap-southeast-2 regions, and would like visitors from United Kingdom to see a different site than those in Australia. Which Routing Policy would help you to accomplish this?
	- Geolocation, and Geoproximity routing policy
		- Correct - Geolocation routing lets you choose the resources that serve your traffic based on the geographic location of your users, meaning the location that DNS queries originate from. For example, you might want all queries from Europe to be routed to an ELB load balancer in the Frankfurt region. Correct - Geoproximity routing lets Amazon Route 53 route traffic to your resources based on the geographic location of your users and your resources
9. In AWS Route 53, which of the following are true?
	- Alias Records provide a Route 53–specific extension to DNS functionality
	- Route 53 allows you to create an Alias record at the top node of a DNS namespace (zone apex)
		- Alias Records have special functions that are not present in other DNS servers. Their main function is to provide special functionality and integration into AWS services. Unlike CNAME records, they can also be used at the Zone Apex, where CNAME records cannot. Alias Records can also point to AWS Resources that are hosted in other accounts by manually entering the ARN


