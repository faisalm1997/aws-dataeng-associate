# Networking and Content Delivery

---

## VPC, Subnets, IGW, NGW

- **VPC (Virtual Private Cloud):** Private network to deploy your AWS resources.
- **Subnets:** Logical subdivisions of a VPC, creating smaller, manageable network segments.
  - **Public Subnet:** Accessible from the internet.
  - **Private Subnet:** Not accessible from the internet.
- **Route Tables:** Define access between the internet and between subnets.
- **Internet Gateway (IGW):** Allows VPC instances to connect to the internet.
- **NAT Gateway (NGW):** Allows instances in private subnets to access the internet while remaining private.

---

## NACL, Security Groups, VPC Flow Logs

- **NACL (Network ACL):** Firewall controlling traffic to/from a subnet. Supports ALLOW and DENY rules, attached at subnet level. Rules only include IP addresses.
- **Security Groups:** Firewall controlling traffic to/from an EC2 instance. Only ALLOW rules, can reference IP addresses or other security groups. Operates at instance level.
- **VPC Flow Logs:** Capture information about IP traffic going into interfaces. Useful for monitoring, troubleshooting, and auditing. Logs can be sent to S3, CloudWatch Logs, or Kinesis Data Firehose.

---

## VPC Peering

- Connects two VPCs privately, making them behave as if on the same network.
- **Requirement:** No overlapping CIDR blocks (IP address ranges).

---

## VPC Endpoints

- Connect to AWS services through a private network instead of the public internet.
- **Benefits:** Enhanced security and lower latency.

---

## Site-to-Site VPN & Direct Connect

- **Site-to-Site VPN:** Connects on-premises VPN to AWS. Encrypted connection over the public internet.
- **Direct Connect:** Establishes a private, fast, physical connection between on-premises and AWS. Takes time to set up (about a month).

---

## AWS PrivateLink

- Secure, scalable way to expose a service to thousands of VPCs.
- **Does not require:** VPC peering, IGW, NAT gateways, or route tables.
- **Requires:** Network Load Balancer and Elastic Network Interface.

---

# DNS

- **Domain Name System:** Translates human-friendly hostnames into machine-readable IP addresses.
- **Hierarchical Structure:**  
  - Top-level domain: `.com`, `.org`, `.gov`, etc.
  - Second-level domain: `amazon.com`, `google.com`
  - Subdomains: `www.example.com`, `api.example.com`

**Key DNS Terminology:**
- **Domain Registrar:** Route53
- **DNS Records:** A, AAAA, CNAME, NS
- **Zone File:** Contains DNS records
- **Name Server:** Resolves DNS queries

### How DNS Works

1. **User Input:** User types a website address (e.g., `example.com`) in the browser.
2. **Cache Check:** Computer checks its DNS cache for the IP address.
3. **DNS Query:** If not cached, sends a query to the configured DNS server.
4. **Recursive Resolution:** DNS server checks its cache or queries other DNS servers.
5. **Root Nameserver:** Directs to the appropriate TLD server.
6. **TLD Server:** Directs to the authoritative nameserver for the domain.
7. **Authoritative Nameserver:** Provides the actual IP address.
8. **Response:** IP address is returned to the computer.
9. **Browser Connection:** Browser uses the IP to connect to the web server.
10. **Caching:** IP is stored for future use.

---

# Route53

- Highly available, scalable, and fully managed DNS service.
- **Authoritative DNS:** User can update DNS records.
- **Domain Registrar:** Can register domains.
- **Health Checks:** Can monitor resource health.
- **Availability:** 100% SLA.

## Records

- Each record has: domain/subdomain, record type, value, routing policy, TTL.
- **Supported Types:**  
  - **A:** Maps hostname to IPv4  
  - **AAAA:** Maps hostname to IPv6  
  - **CNAME:** Maps hostname to another hostname  
  - **NS:** Name servers for the hosted zone

## Hosted Zones

- **Public Hosted Zone:** Routes traffic on the internet.
- **Private Hosted Zone:** Routes traffic within one or more VPCs.
- **Pricing:** $0.50 per month per hosted zone.

---

# Amazon CloudFront

- **CloudFront:** Content Delivery Network (CDN) that improves read performance by caching content at edge locations, enhancing user experience.
- **Global Presence:** 216+ points of presence worldwide.
- **Security:** Built-in DDoS protection.

## Origins

CloudFront can use different origins:
1. **S3 Bucket:** Distribute and cache files at the edge; supports uploads to S3. Secured using Origin Access Control.
2. **VPC Origin:** For applications hosted in VPC private subnets.
3. **Custom Origin:** HTTP backend, such as an S3 website or any public HTTP server.

## CloudFront vs S3 Cross-Region Replication (CRR)

- **CloudFront:**  
  - Global edge network, caches static content for worldwide availability.
  - Best for static content that must be available everywhere.
- **S3 CRR:**  
  - Must be set up per region where replication is needed.
  - Files are updated in real time and are read-only.
  - Best for dynamic content that needs low latency in a few regions.

## Cache Invalidation

- When backend origin is updated, CloudFront may not fetch the new content until the TTL expires.
- **Cache Invalidation:**  
  - Force CloudFront to invalidate (remove) cached files or files on a specific path.
  - CloudFront then fetches the latest version from the origin.

---