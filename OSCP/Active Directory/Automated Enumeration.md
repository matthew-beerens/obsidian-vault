

### Collecting Data with SharpHound

installation:
```
git clone https://github.com/SpecterOps/BloodHound.git /opt/BloodHound
cd /opt/BloodHound/
cp examples/docker-compose/* ./
sudo docker-compose up
```

You will see the initial password as part of the docker-compose output:
```
Initial Password Set To:    hXkCJlCM7VMDymtCfZ6PcAiRWdpYWu7q
```

```
login: admin
password: usual password after reset
```

once logged in click on the settings cog and click `download collectors`

#### Using Sharphound

located: `/usr/share/windows-resources/sharphound`

```
Import-Module .\Sharphound.ps1
```

To start using sharphound to collect data, we must first invoke bloodhound:
```
Get-Help Invoke-BloodHound
```

```
Invoke-BloodHound -CollectionMethod All -OutputDirectory C:\path\to\output -OutputPrefix "suitable name"
```

This command will essentially take a snapshot of the domain from the user were logged in as to enable us to analyze everything we have access to with BloodHound



### Analyzing with BloodHound

To use bloodhound we must first start the neo4j service:
```
sudo neo4j start
```

The credentials:
- user: neo4j 
- pass: neo4j - changed to neo4j123

once neo4j is started run:
```
bloodhound
```

and import our scan from SharpHound

Once a scan is import and opened:
```
search a user you know about and check their information to see what their outboud control is and go from there
```