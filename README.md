# azure-mono-repo
This is azure learnings 

AZ 204 
Serverless is  Scale to zero

Function as a service or FAAS
piece of code  which is event driven
It is only scale to zero if it is managed like that . Functions are deployed as containers .
Functions has one or many  input binding for input which triggers an event driven function adn sends output to an output binding

Functions depend on storage 
Types of storage available are 
1. Blob storage which maintain bindings state and function keys.
2. Azure files is used to store and run the function app code in a consumption plan and premium plan . 
3. Queue storage is used by task hubs in Durable functions.
4. Table storage is used by task hubs in Durable Functions. 


Code for function 
function.json 
Code 
.funcignore 
host.json 

Local project  which mirrors the serverless code on local 

Authorization levels 
1. Anonymous 
2. Function - a function specific API key is required (default value)
3. admin - The master key is required 


How to debug the application 
1. Start streamin logs 
2. Live metrics steam 


To use Functions in VS code we need to install Azure Tools (combo) or Azure Functions 

The shortcut for opening the extension is shift+alt+A

Application Runtime is compute environment configured with the necessary software and system configuration to run a specific type of application code . 

The provided Runtime stack are .NETNode.js Python Java Powershell Core Custome Handler 

If you have issues logging in vs code azure use the following link.

https://github.com/microsoft/vscode-azure-account/wiki/Troubleshooting#unable-to-sign-in-while-using-a-proxy

For me the following worked. 

1 . Ctrl + Shift + P
2. Azure Sign in with Device code.
3. Copy the code and click on the link . 
4. Enter the code and complete the login . 

Azure provides the function templates . Important ones are 
HTTP Trigger 
Timer Trigger 
Blob storage trigger 
Cosmos db trigger 
Queue storage trigger for queue messages 
Event Grid : it is basically an serverless event bus integrated 
with azure services.
Many Azure services can trigger a function through event grid .

Event Hub  streaming events 

Service bus queue triggered by a message in a Bus queue 
Service Bus Topics 

SendGrid : triggered by email event 



Function configuration  are available in function.json

bindings have the following details 

type is the name of binding
direction can be in and out 
name is used for bound data in the function 


Hosts configuration is included in hosts.json

Plan Services available for the functions are 
1. Consumption(Serverless) 
This has cold start i.e it can scale to zero and may take time when the first request comes . you only pay for the executions and running and scale out is done automatically 
2. Functions Premium 
This has pre-warmed instances so it is always available and can react instantly to requests . 
Scale out depends on the number of incoming requests. 

3. App service plan 
This is a dedicated app service plan and it all depends on vm sharing . 
The benefit here is that you can use underutilized VMs in your app service plans to run the azure functions . 
It is little bit difficult to set up but provides great flexibility.

There are multiple Supported bindings for Azure functions .

Important thing to understand is that current version of Functions is 4.0 and some may be supported on v1.0 only and some may be suppported using api only . The list is really long . 

Similar to Java Functions direction can be in / out / inout 

type can be made by appending type and "Trigger".

For example httpTrigger / blobTrigger etc 

Trigger binding is always in .

Trigger and bindind scenarios 
1. Cron jobs / Scheduled Jobs

2. Everytime signs up and email should be triggered 
httpTrigger -> function -> SendGrid to send emails 
in direction is None as we are not accessing any data 
3. For Asynchronous tasks , lets say we have a queue to create invoices and send invoices to the email
queueTrigger -> function -> SendGrid 


Binding Expressions

In function.json we can use binding expressions 

For example container/{queueTrigger}

Types of binding exressions are 
App settings 
Trigger File Name 
Trigger Metadata 
JSON payloads 
New GUID - this is for globally unique identifier 
current date and time 

Basically to pick up dynamic data the binding expressions can be used . 

For App Settings
When you want to change configuration based on the environment Uses percentage signs(%) instead of curly braces 
so "queueName" : "%input_queue_name%"

This is the only case when you use % instead of curly braces 

Trigger filename 

"path" : "sample-images/{filename}"

New GUID - this is for globally unique identifier 

Example "path" : "my-output-container/{rand-guid}.txt"

{DateTime} to get DateTime format in 2024-06-26T17-59-55Z

Here Z refers to ZULU which refers to  Coordinated Universal time UTC  and is used as common military time zone 

Local Settings File

local.settings.json is the file which contains the settings used by local development tools 

This is expected at the root of the project folder and may contain the secrets such as connection strings . So this should never be committed to remote repository and should be added in .gitignore 

Azure Functions core tools is a cli to develop and test your functions locally 

Top level commands

func init - ceate a new function project in a specific language 
func logs - gets logs for functions running in a kubernetes cluster
func new - creates a new function in the current project based on a template .  
func run  - enables you to invoke a function directly similar to using the test tab in portal . This si only available in version 1.x only 
func start - starts the local runtime hots and loads the function project in current folder 

func deploy - replaces with func kuberneted deploy 

There are some command groups 
func azure 
func durable 
func extensions
func kubernetes 
fun settings - for managing local functions host environment settings 
func templates - for listing available templates 

Custom handlers are lightweight web servers that receive events from the Functions host. 


Function app will pass along payload to the custome handler and receive the response and then it passes on the output to the output bindings. 

custom handler are normally executables 

 Unreachable Function can have many reasons 

 1. Most importantly if it has lost access to its storage account , it will not be able to start as it will loose its binding data etc. 
 2. Daily execution quota is full . 
 3. App is behind a firewall 



Durable Functions are stateful functions 
There are 2 types of functions 

1. Orchstrator function which define stateful workflows (implicitely representing state via control flow )
2. Entity functin - manages the state of an entity 

They define workflow in code . No json schema or designers are needed 
can call synchronously or asynchrously 
The output from the called functions can be saved to local variables . 
They automatically checkpoint therire progress whenerver the function awaits 
Local state is never lost of ythe process recycles or the VM reboots 

C javascript python F powershell powershell 7 
To use durable functions we need to install library specific to your language 

npm install durable-functions

There are some patterns of Durable functions 
1. Function chain

2. Fan-out/Fan-in to run the functions in parallel and  then wait for them to finish

3. Fanning out can be completed with normal functions by having the function send multiple message to a queue . 

4. Fanning in is much more difficult because we have to write code to track when the queue-triggered functions end and store function outputs . 


5. The Async HTTP API pattern address the problem of coordinating the state of long running operations with external clients . 


A common way is to implement this pattern is to have an HTTP call trigger the long running action , then redirect the client to a status endpoint that they can poll to learn when the operation is complete . 


Durable Functions provide built in apis that simplify the code we write for interacting with long-running function executions 

The Monitor pattern referes to a flexible recurring process in a workflow such as pilling until certain conditions are met . 

a periodic cleanup job can be done using a timer-trigger but its interval is fixed making managing instance lifetimes difficult. 

Here Durable functions provides the flexible recurrence intervals , task lifetime management and ability to create multiple monitor processes from a single orchestration . 

 To avoid issues with Human intervention we can use timeouts and compensation logic to automate the processes.

 The Aggregator(stateful entities)  pattern is about aggregating event data over a period of time into a single addressable entity 

 Azure functions on kubernetes We can deploy Azure function to a kubernetes cluster running KEDA 
 KEDA stands for Kubernetes Event Driver Autoscaling 

 func kubernetes deploy \
 --name <name-of-function-deployment> \ 
 --registry <container-registry-username>


Durable Functions extension handled this pattern with relatively simple code.

----------------------------------------------------------------


AZ 104 





AZ 305 





AZ 400