# FAQ and Known Issues


## Common Questions

### 1. End user IP is blocked and I do not know why

If you use CSF, then try to find the IP in [CSF](/ids_integration/#csf-integration) Allow/Deny lists using their [documentation and support](https://support.configserver.com/knowledgebase/category/support%20). If not, then do the following:

* Go to cPanel Plugins section, choose Imunify360 and enter the Incidents page.

* Make sure that the IP checkbox at the top of the table is ticked. Enter proper IP or part of IP in the input field and click _Enter_.

  * If the IP was found, then follow instructions on [Incidents page](/dashboard/#incidents) and perform the actions you need, like: add IP to the Whitelist or disable the security rule that has detected this incident.

* If the IP was not found on the Incidents page, then go to Firewall page and using the same way as in the previous step try to find proper IP in Black List or Grey List.

  * If the IP was found then follow this instruction for [Grey List](/dashboard/#firewall) or [Black List](/dashboard/#firewall) and move the IP to the White List or just remove from the Black or Grey Lists.

If nothing helps, then [contact our support team](https://cloudlinux.zendesk.com/hc/requests/new).

### 2. Could I disable IPtables (firewall) or OSSEC, when using Imunify360?

No. Imunify360 will not be able to stop an attack without IPtables and will not be able to detect an attack without OSSEC.

### 3. Does Imunify360 log events such as adding or removing an IP to/from the Gray List?

Most Imunify360 logs are saved in `/var/log/imunify360/console.log`. For example, when IP is blocked and added to the Black List, the following lines are added:

``` Python
INFO [2017-04-15 18:30:00,889]
defence360agent.plugins.protector.lazy_init: IP 103.86.52.175 is BLOCKED
with 300 sec (expiration: 1492281300) (due to SensorAlert)
INFO [2017-04-15 18:30:00,889]
defence360agent.plugins.protector.lazy_init: Unblocking 103.86.52.175 in
CSF as it is already in our graylist
INFO [2017-04-15 18:30:01,663] defence360agent.internals.the_sink:
SensorAlert:
{'rule_id': 'LF_SMTPAUTH', 'timestamp': 1492281000.8720655, 'attackers_ip': '103.86.52.175', 'plugin_id': 'lfd', 'method': 'ALERT', 'ttl': '1'}
When user unblocks himself by captcha, logs look like this:
INFO [2017-04-17 00:51:26,956] defence360agent.internals.the_sink:
CaptchaEvent:
{'timestamp': 1492404686.9496775, 'errors': [], 'user_agent': 'Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36', 'accept_language': 'ru-RU,ru;q=0.8,en-US;q=0.6,en;q=0.4', 'event': 'PASSED', 'method': 'CAPTCHA', 'attackers_ip': '10.101.1.18'}
INFO [2017-04-17 00:51:26,967]
defence360agent.plugins.protector.lazy_init: IP 10.101.1.18 is UNBLOCKED
(due to ClientUnblock)
```

Adding and removing IPs from the Whitelist is only possible manually, no IPs will be added automatically.

### 4. Comodo WAF has a lot false positive and trigger the CSF blocking, will Imunify360 improve it?

CSF only blocks IPs by mod_security if mod_security configured with `SecRuleEngine On`. Imunify360 works a bit differently: it uses `SecRuleEngine DetectionOnly` in mod_security configuration and only blocks by mod_security events with high severity, thus decreasing false positives rate.

In some cases mod_security needs to be configured not to cause blocks by csf/lfd.

Possible solutions are:

* Set `SecRuleEngine` to `DetectionOnly` – this way CSF will not block IPs by mod_security events and Imunify360 will still block by mod_security events with high severity (preferable way).

* In `/etc/csf/csf.conf` set `LF_MODSEC` to `0` so that CSF will ignore mod_security events and Imunify360 will still block IPs as described above. But note that in this case requests causing mod_security events will still be blocked by mod_security itself.

### 5. To start using Imunify360 we need to know which information is sent to your servers. Could you please give us some more information?

The following info is sent to our server:

* all the messages from IDS OSSEC (can be found in OSSEC logs)
* all the messages from mod_security (can be found in `modsec_audit.log`)
* users domains (to be checked in reputation engine);
* CAPTCHA verification info
* all running scans for malware (maldet scans) and information on moving to the quarantine or discovering suspicious files
* optionally, suspicious files can be sent to us for the analysis. Files can be sent via UI by marking a proper checkbox

### 6. No valid Imunify360 License Found.

Check if the agent is running.

Check access to the central server (`IP: 148.251.130.176 port: 443)`.

Call `imunify360-agent rstatus` and ensure that status is `True`:

```
{'expiration': 0, 'status': True, 'user_count': None, 'user_limit': None, 'redirect_url': None}
```

Ensure that `/etc/sysconfig/imunify360/imunify360.id` file contains `server_id`.

If no, `imunify360-agent register KEY`.

### 7. I have an error peewee.DatabaseError: database disk image is malformed. What should I do?

Imunify360 uses SQLite database to store its data. Although this database has proved its reliability, database files become corrupted in rare cases. To restore data try to perform the following steps:

Stop the agent.

If you have sqlite3 application installed on your machine, try to make dump of Imunify360 database:

```
#sqlite3 /var/imunify360/imunify360.db
.mode insert
.output dump_all.sql
.dump
.exit
```

You should see new file `dump_all.sql` in the directory `/var/imunify/`

Create a new database from this dump file:

```
#sqlite3 imunify360.db.new < dump_all.sql
```

Replace old database with the new one:

```
#cd /var/imunify/
#mv imunify360.db imunify360.db.corrupt && mv imunify360.db.new imunify360.db
```

Start the Imunify360 agent.

If these steps have not solved the problem or no sqlite3 package is installed, then you should create a completely new database:

Stop the agent.

```
#rm /var/imunify/imunify360.db
#imunify360-agent migratedb
```

Start the agent

Please find more FAQs in our [Knowledge Base](https://cloudlinux.zendesk.com/hc/sections/115001538929-FAQ).


