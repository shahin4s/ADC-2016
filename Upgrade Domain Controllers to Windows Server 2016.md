# Upgrade Domain Controllers to Windows Server 2016 : 
# There have two way for Domain Controller Upgrade .

Upgrade domain controller OS (recommended )
Upgrade database to another server  (recommended  it will be HA) like ADC

 # Install Server 2016 as ADC for an existing 2012 R2 Domain below step

Server 2016 member of  Domain Controller (2012 R2)
Prepare Server 2012 R2 Domain for Server 2016 Domain Controller
Promote the Windows Server 2016 as domain controller
Install Server 2016 as ADC for an existing 2012 R2 Domain
FSMO role Transfer to new server 
Demote old DC server 2012 R2


# Install server 2016 and complete pre-installations using sconfig  commend.
 Hostname, Timezone, Network, Remote Desktop, firewall.

# Prepare server 2012

# check current functional level:
    PS C:\Users\Administrator> Get-ADDomain | select domainMode, # DistinguishedName
    PS C:\Users\Administrator> Get-ADForest | select forestmode
Or (Get-ADForest).ForestMode, (Get-ADDomain).DomainMode

# prepare schema Object:
# Checking schema version:

Get-ADObject (Get-ADRootDSE).schemaNamingContext -Property objectVersion

objectVersion     : 69 ( we have to upgrade 87)

# Update schema: 
Update forest :
cd E:\support\adprep
adprep /forestprep

# Domain and Group policy update:

cd  E:\support\adprep
adprep /domainprep
adprep /domainprep /gpprep[ its for earlier server 2012R2]
 [here E:\ = server 2016 iso moundated drive ]

# Verify schema version : 
Get-ADObject (Get-ADRootDSE).schemaNamingContext -Property objectVersion
objectVersion     : 87 [ schema upgrade successful ]


# Install Server 2016 as ADC for an existing 2012 R2 Domain:




# FSMO role Transfer to new server 2016:
     Login domain administrator account. 
# Open active directory powershell console 
Move-ADDirectoryServerOperationMasterRole -Identity "DC02" -OperationMasterRole 0,1,2,3,4
0=PDC
1=RID pool manager
2=Infrastructure master
3=Schema master
4=Domain naming master
# Check FSMO:
    netdom.exe query fsmo

# Demote old DC server 2012 R2

