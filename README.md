# 6_1_ELK_STACK

### A. Create two linux servers,

    * server1 => install and configure kibana and elasticsearch with basic username and password authentication
    * server2 => install and configure metricbeat

##### Manually enabling password
![image](https://user-images.githubusercontent.com/23631617/144418992-2c1309f6-611b-43a4-9b7b-914f04507e67.png)

In order to use password authentication, we need to add `xpack.security.enabled: true` to `elasticsearch.yml`.

```bash
echo xpack.security.enabled: true >> config/elastic.yml
```
##### Setting up password
![image](https://user-images.githubusercontent.com/23631617/144420206-4c55274d-55e6-4a03-a640-2f35f037ac1a.png)

##### Logging in as user `elastic`
![image](https://user-images.githubusercontent.com/23631617/144420359-12ad95fb-bb1d-4401-9fd3-b7eb4c07bde8.png)

##### Logging-in to Elasticsearch Web Interface
![image](https://user-images.githubusercontent.com/23631617/144422074-edd9ef25-0d55-464a-8c56-498f7196846e.png)

Although SSH tunneling is not realiable and should not be used at production, I decided to use it since this is for learning
purpose. 

```console
ssh root@server.ip -p 11051 -L 9200:localhost:9200 -L 5601:localhost:5601 -C -N -4 2> /dev/null &
```
##### Setup on different hosts
![image](https://user-images.githubusercontent.com/23631617/144426620-84b43bb8-bd3e-4df8-854b-26abb3a12a7f.png)
![image](https://user-images.githubusercontent.com/23631617/144544545-e7b387ad-30a7-467c-a261-8a6f2f8d36dd.png)

##### Verifying Metricbeat is Setup Correctly
![image](https://user-images.githubusercontent.com/23631617/144431266-fcba5d6d-57c3-43f2-8565-abca74c009c1.png)

##### Viewing Metrics of Server Running Apache
![image](https://user-images.githubusercontent.com/23631617/144431650-9db59c33-3bbe-4ed6-81e3-aad3b88bf0fc.png)

##### Metricbeat Indices
![image](https://user-images.githubusercontent.com/23631617/144434298-2c3b60de-2bdd-4698-b360-22965568b019.png)

---

### Collect metric from following sources in server1 and send them to elasticsearch. Store them in an index named "server1-metrics".
    * Memory usage
    * Disk usage
    * Load average

```
metricbeat.modules:
        - module: system
          metricsets:
                  - memory
                  - diskio
                  - load
          index: "server1-metrics"
          enabled: true
setup.template.name: "server1"
setup.template.pattern: "server1-*"
```

![image](https://user-images.githubusercontent.com/23631617/144554166-c0ea1369-bd2f-4149-b23c-ce172e42762a.png)

### 1. Create a dashboard in kibana and generate visual report(line graph) for Memory usage and load average of server1 with relation to time

##### Memory Usage Line Graph
![image](https://user-images.githubusercontent.com/23631617/144556902-4ad295c2-9686-42e5-9203-523a65dd35cc.png)

##### Avaeage Load Line Graph (5 min average)
![image](https://user-images.githubusercontent.com/23631617/144557926-68593ffc-99ef-4569-81ad-490edad92705.png)

---

### 2. Generate alerts through kibana system for following thresholds
    * When memory usage > 80% for last 2 minutes send alert to a slack channel
    * When Disk usage > 70%   send alert to a slack channel
    * When load average > 1  for last 2 minutes  send alert to a slack channel

