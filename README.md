Updox Code Challenges
=====================

Hello Adam and other Updox employees who review this work. For convenience I'm placing both the Ansible and Python challenged in the same repo. I'll give sections in this readme to both challenges with some instructions, but mainly just explaining my thought process and where I would have made some changes with more time. Thanks for taking the time to review this!

Ansible Challenge
-----------------

I won't give instructions on how to run the Ansible playbook as I'm fairly sure whoever is reviewing this knows how to run a playbook. I used a hosts file for my testing and I will leave it blank in the repo in case you wish to plug in some of your own hosts. The challenge was:

`write an Ansible playbook that installs Filebeat, ElasticSearch & Kibana. Filebeat should be configured to index /var/log/syslog (or /var/log/messages). Include firewall rules, the ports exposed should be configurable with variables in Ansible.`

I did a couple extra things in here like install the dependency Java, made each of these a persistent service, and edited the configs for Filebeat and Kibana. This was my first time configuring this so if I had some more time I would have focused more on the configs and made them more production ready, but I felt it was too out of scope for the ask of the challenge. I looked through the default config for Elasticsearch and it seemed to be okay for the scope of this task so I left that alone.

Another thing I would have maybe done differently in a prod environment is make this more modular. So instead of one playbook that configures an ELK stack I would break each software install and config down into separate roles so they are more portable.

There's a couple ways to do variables in Ansible, but I feel the most common way in a prod environment is through a vars file which is included here. From what I read only Elasticsearch and Kibana should have ports exposed as Filebeat is just doing log exportation. In the config for Elasticsearch and Kibana I only have it listening on localhost so an external connection is not possible. I could open this up to all traffic or select private IP subnets but again I feel this decision is out of scope so I left it as localhost only.

I tested this against a RHEL box I have and the playbook assumes that firewalld is being used to manage the firewall. Here are some commands I used to test this playbook:

`lsof -i -P -n | grep LISTEN` - Used to confirm what ports are open and listening

`filebeat test output` - Shows that Filebeat is connected to elasticsearch

`curl -I http://127.0.0.1:5601` - Test Kibana make sure it's running and listening on port 5601

`curl http://127.0.0.1:9200` - Test if Elasticsearch is running and listening on port 9200

`curl http://localhost:9200/_aliases?pretty=true` - View all indexes and verify that a Filebeat index is present.
