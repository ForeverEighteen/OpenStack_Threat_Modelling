Keystone Threat Modeling : Token API Service
=========================================
### Table of contents

[System Overview](#system)

[Implementation Overview](#implementation)

[System Assumptions](#assumption)

[Data Flow Diagrams](#dfd)

[Entry Points](#entry)

[Assets](#asset)

[Threats](#threats)


<a name="system"/>
###System Overview
####Application version
Keystone Havana Stable Release
   
####Application Description
Token API includes the manager and the driver. The manager receives requests and fulfills the request with the help of specific driver. The driver is configurable using keystone.conf.  The driver in turn fulfills the operation by calling a persistent storage layer (SQL alchemy or other backend)

####Additional Info
  

<a name="implementation"/>
###Implementation Overview
####Major Components
Keystone/token/core
 
Keystone/token/backends/driver {sql/kvs/memchace}
####Dependent components
Dogpile.cache, SQLAlchemy, IDENTITY_API

unique_id = cms.hash_token (for PKI token) = configurable hash algorithm (md5 default)

####Description
Major process: 

Create_token

Validate_token

Delete_token

Revocation_list

<a name="assumption"/>
###System Assumptions (External Dependencies)

 - Only SQL backend considered
 - The incoming request to TOKEN_API is trustworthy

###Security Objective

 - Fulfillment of intended API operation.
 - Auditablity of request/response.
 
Towards DB:

  - non-repudiation of request/response prodvided by DB (hard to proof, instead we can go for weak authenticity property and assume that DB will do its job (trust) and have audit log)


<a name="dfd"/>
###Data Flow Diagrams 
####Create_Token
![enter link description here][1]
####Validate_Token
![!enter link description here][2]
####Delete_Token
![!enter link description here][3]
####Revocation_List
![!enter link description here][4]

<a name="entry"/>
###Entry Points
####Name ID-01: Token Controller, Auth Controller
#####Description
Request from Controller
#####Accessible To
8) Keystone Process user

####Name ID-02: Cache
#####Description
Store and retrieve data from cache
#####Accessible To
8) Keystone Process user

10)System admin 


----------
<a name="asset"/>
###Assets
Full assets list is documented in [url][5]

(1) User (user_id)

(8) Token (Token_id, unique_id, Token_ref)

(26) Configuration Parameter (Expiry_Time) 

(8.1) Revocation List

----------
####ComponentName-001
Threats:
> 

Threat Agent:
> 

Attack Vectors:
> 

Security Weakness:
> 

Counter Measures:
> 

Extra:
>  Probability:

>   Impact:

>   Related Info:

>   Comments:
     Link to Bug/mailing list or Tracking 

#### Issues
Create Token:

1. Unique is now configurable, so hash size can vary. Check that hash length do bounded within the 
token_id field in DB
2. Cache set for new token, what about new cache security - we assume cache is trustworthy. 

Delete Token:

1. Order in which token deletion operation happens: 1) disable token in DB 2) remove individual token from cache 3) remove existing revocation list cache.  --- no notification to external services/endpoints ??? 





  [1]: images/DFD_Token_API_Create_Token.png
  [2]: images/DFD_Token_API_Validated_Token.png
  [3]: images/DFD_Token_API_Delete_Token.png
  [4]: images/DFD_Token_API_revoke_list.png
  [5]: Keystone_asset_library.md
