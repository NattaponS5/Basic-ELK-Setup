Install Component
--------------
elk-2-3
elk-ml
kibana-1
logstash-1

Install Elasticsearch & Generate Certificates and Key and transfer them among ELK VMs & Configure Elasticsearch VMs
--------------
elk-1

Try starting Elasticsearch & Generate Elastic Password --> keep password in a safe place
--------------
./elasticsearch-8.17.2/bin/elasticsearch --> of all elasticsearch nodes

./elasticsearch-8.17.2/bin/elasticsearch-setup-passwords auto --> note the password

or

./elasticsearch-8.17.2/bin/elasticsearch-reset-password -u kibana_system
./elasticsearch-8.17.2/bin/elasticsearch-reset-password -u elastic

Configure Kibana VM, Logstash VM and HAProxy Services
--------------
logstash-2
kibana-2
fleetcert

Run Elasticsearch and Kibana as a service.
--------------
elasticsearch-service
kibana-service

./kibana-8.17.2/bin/kibana-encryption-keys generate

copy password to kibana-8.17.2/config/kibana.yml