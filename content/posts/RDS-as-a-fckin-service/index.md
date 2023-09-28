+++
title = "RDS as a service?"
date = "Thu, 28 Sep 2023 13:08:50 +0000"
authors = ["kn"]
cover = ""
tags = ["AWS", "RDS", "JOKE"]
keywords = ["AWS", "RDS", "JOKE"]
description = "The reality of RDS"
showFullContent = false
+++

**Jeff Bezos:** When the status changes to Available, you can connect to the DB instance. Depending on the DB instance class and the amount of storage, it can take up to 20 minutes before the new instance is available.

**Reality:**

- RDB instance status: creating
- RDS stuck in this fucking step nearly 2 hours and couting
- db-fuck-as-a-service

**What I think**:

- Somehow the init RDB button will trigger a form with all specs, then send sms directly to a fucking devops guy of AWS ;)))
- Then that guy will goto AWS console, init new EC2 manually by his hand, use rds-fucking-root.pem keypairs
- YEAH, then then then pick `ansible-playbook` from somewhere in the internet then run this fuck

Fact (trustme): nslookup of RDS domain, it will result EC2 domains
