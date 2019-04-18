## Functions

  

<dl>

<dt><a href="#admin">admin()</a></dt>

<dd><p>Initialize the worker-specific admin features</p>

</dd>

<dt><a href="#createContracts">createContracts(enigmaContractAddr, tokenContractAddr)</a></dt>

<dd><p>Initialize the Enigma and Enigma token contracts</p>

</dd>

<dt><a href="#createTask">createTask(fn, args, gasLimit, gasPx, sender, scAddrOrPreCode, isContractDeploymentTask)</a> ⇒ <code>Task</code></dt>

<dd><p>Create a base Task - a wrapper for a task (either contract deployments or regular tasks)</p>

</dd>

<dt><a href="#createTaskRecord">createTaskRecord(task)</a> ⇒ <code>EventEmitter</code></dt>

<dd><p>Create and store a task record on chain (ETH). Task records are necessary for collecting the ENG computation fee

and computing the immutable taskId (a unique value for each task computed from hash(hash(encrypted function

signature, encrypted ABI-encoded arguments, gas limit, gas price, user&#39;s public key), user&#39;s nonce value

monotonically increasing for every task deployment). Thus, task records have important implications for task

ordering, fee payments, and verification.</p>

</dd>

<dt><a href="#createTaskRecords">createTaskRecords(tasks)</a> ⇒ <code>EventEmitter</code></dt>

<dd><p>Create and store task records on chain (ETH). Task records are necessary for collecting the ENG computation fee

and computing the immutable taskId (a unique value for each task computed from hash(hash(encrypted function

signature, encrypted ABI-encoded arguments, gas limit, gas price, user&#39;s public key), user&#39;s nonce value

monotonically increasing for every task deployment). Thus, task records have important implications for task

ordering, fee payments, and verification.</p>

</dd>

<dt><a href="#getTaskRecordStatus">getTaskRecordStatus(task)</a> ⇒ <code>Promise</code></dt>

<dd><p>Get the Task&#39;s task record status from Ethereum</p>

</dd>

<dt><a href="#getReport">getReport(custodian)</a> ⇒ <code>Promise</code></dt>

<dd><p>Find SGX report</p>

</dd>

<dt><a href="#getWorkerParams">getWorkerParams(blockNumber)</a> ⇒ <code>Promise</code></dt>

<dd><p>Given a block number, obtain the worker parameters. These parameters remain the same for a given secret

contract and epoch (fixed number of blocks). These parameters are cached until the epoch changes.</p>

</dd>

<dt><a href="#selectWorkerGroup">selectWorkerGroup(scAddr, params, workerGroupSize)</a> ⇒ <code>Array</code></dt>

<dd><p>Select the workers weighted-randomly based on the staked token amount that will run the computation task</p>

</dd>

<dt><a href="#sendTaskInput">sendTaskInput(task)</a> ⇒ <code>EventEmitter</code></dt>

<dd><p>Send Task to Enigma p2p network for computation</p>

</dd>

<dt><a href="#getTaskResult">getTaskResult(task)</a> ⇒ <code>EventEmitter</code></dt>

<dd><p>Get task result from p2p network</p>

</dd>

<dt><a href="#pollTaskStatusGen">pollTaskStatusGen(task, withResult)</a></dt>

<dd><p>Generator function for polling the Enigma p2p network for task status</p>

</dd>

<dt><a href="#innerPollTaskStatus">innerPollTaskStatus(task, generator, emitter)</a></dt>

<dd><p>Inner poll status function that continues to poll the Enigma p2p network until the task has been verified</p>

</dd>

<dt><a href="#pollTaskStatus">pollTaskStatus(task, withResult)</a> ⇒ <code>EventEmitter</code></dt>

<dd><p>Poll the Enigma p2p network for a TaskInput&#39;s status</p>

</dd>

<dt><a href="#serializeTask">serializeTask(task)</a> ⇒ <code>Object</code></dt>

<dd><p>Serialize Task for submission to the Enigma p2p network</p>

</dd>

<dt><a href="#obtainTaskKeyPair">obtainTaskKeyPair()</a> ⇒ <code>Object</code></dt>

<dd><p>Deterministically generate a key-secret pair necessary for deriving a shared encryption key with the selected

worker. This pair will be stored in local storage for quick retrieval.</p>

</dd>

<dt><a href="#deploySecretContract">deploySecretContract(fn, args, gasLimit, gasPx, sender, preCode)</a> ⇒ <code>Task</code></dt>

<dd><p>Create a task to deploy a secret contract - creates base task, creates task record, and sends task to the

Enigma network.</p>

</dd>

<dt><a href="#computeTask">computeTask(fn, args, gasLimit, gasPx, sender, scAddr)</a> ⇒ <code>Task</code></dt>

<dd><p>Create a compute task - creates base task, creates task record, and sends task to the Enigma network.</p>

</dd>

<dt><a href="#version">version()</a> ⇒ <code>string</code></dt>

<dd><p>Return the version number of the library</p>

</dd>

</dl>

  

<a name="admin"></a>

  

## admin()

Initialize the worker-specific admin features

  

**Kind**: global function

<a name="createContracts"></a>

  

## createContracts(enigmaContractAddr, tokenContractAddr)

Initialize the Enigma and Enigma token contracts

  

**Kind**: global function

  

| Param | Type |

| --- | --- |

| enigmaContractAddr | <code>string</code> |

| tokenContractAddr | <code>string</code> |

  

<a name="createTask"></a>

  

## createTask(fn, args, gasLimit, gasPx, sender, scAddrOrPreCode, isContractDeploymentTask) ⇒ <code>Task</code>

Create a base Task - a wrapper for a task (either contract deployments or regular tasks)

  

**Kind**: global function

**Returns**: <code>Task</code> - Task with base attributes to be used for remainder of task lifecycle

  

| Param | Type | Description |

| --- | --- | --- |

| fn | <code>string</code> | Function name |

| args | <code>Array</code> | Inputs for task in the form of [[arg1, '<type>'], ..., [argn, '<type>']] |

| gasLimit | <code>Number</code> | ENG gas limit for task computation |

| gasPx | <code>Number</code> | ENG gas price for task computation |

| sender | <code>string</code> | ETH address for task sender |

| scAddrOrPreCode | <code>string</code> | Either secret contract address or precode, depending on if user is running a contract deployment or regular task |

| isContractDeploymentTask | <code>boolean</code> | Is this task a contract deployment task (if not, it's a regular task) |

  

<a name="createTaskRecord"></a>

  

## createTaskRecord(task) ⇒ <code>EventEmitter</code>

Create and store a task record on chain (ETH). Task records are necessary for collecting the ENG computation fee

and computing the immutable taskId (a unique value for each task computed from hash(hash(encrypted function

signature, encrypted ABI-encoded arguments, gas limit, gas price, user's public key), user's nonce value

monotonically increasing for every task deployment). Thus, task records have important implications for task

ordering, fee payments, and verification.

  

**Kind**: global function

**Returns**: <code>EventEmitter</code> - EventEmitter to be listened to track creation of task record. Emits a Task with task

record creation attributes to be used for remainder of task lifecycle

  

| Param | Type | Description |

| --- | --- | --- |

| task | <code>Task</code> | Task wrapper for contract deployment and regular tasks |

  

<a name="createTaskRecords"></a>

  

## createTaskRecords(tasks) ⇒ <code>EventEmitter</code>

Create and store task records on chain (ETH). Task records are necessary for collecting the ENG computation fee

and computing the immutable taskId (a unique value for each task computed from hash(hash(encrypted function

signature, encrypted ABI-encoded arguments, gas limit, gas price, user's public key), user's nonce value

monotonically increasing for every task deployment). Thus, task records have important implications for task

ordering, fee payments, and verification.

  

**Kind**: global function

**Returns**: <code>EventEmitter</code> - EventEmitter to be listened to track creation of task records. Emits Tasks with task

record creation attributes to be used for remainder of task lifecycle

  

| Param | Type | Description |

| --- | --- | --- |

| tasks | <code>Array</code> | Task wrappers for contract deployment and regular tasks |

  

<a name="getTaskRecordStatus"></a>

  

## getTaskRecordStatus(task) ⇒ <code>Promise</code>

Get the Task's task record status from Ethereum

  

**Kind**: global function

**Returns**: <code>Promise</code> - Resolves to Task wrapper with updated ethStatus and proof properties

  

| Param | Type | Description |

| --- | --- | --- |

| task | <code>Task</code> | Task wrapper for contract deployment and regular tasks |

  

<a name="getReport"></a>

  

## getReport(custodian) ⇒ <code>Promise</code>

Find SGX report

  

**Kind**: global function

**Returns**: <code>Promise</code> - Resolves to SGX report for the worker

  

| Param | Type | Description |

| --- | --- | --- |

| custodian | <code>string</code> | Worker's address |

  

<a name="getWorkerParams"></a>

  

## getWorkerParams(blockNumber) ⇒ <code>Promise</code>

Given a block number, obtain the worker parameters. These parameters remain the same for a given secret

contract and epoch (fixed number of blocks). These parameters are cached until the epoch changes.

  

**Kind**: global function

**Returns**: <code>Promise</code> - Resolves to the worker params, which includes a seed (random int generated from the principal

node), first block number for the epoch, list of active work addresses (ordered list of workers that were logged

in at the start of the epoch), and list of active worker balances

  

| Param | Type | Description |

| --- | --- | --- |

| blockNumber | <code>int</code> | Block number of task record's mining |

  

<a name="selectWorkerGroup"></a>

  

## selectWorkerGroup(scAddr, params, workerGroupSize) ⇒ <code>Array</code>

Select the workers weighted-randomly based on the staked token amount that will run the computation task

  

**Kind**: global function

**Returns**: <code>Array</code> - An array of selected workers where each selected worker is chosen with probability equal to

number of staked tokens

  

| Param | Type | Default | Description |

| --- | --- | --- | --- |

| scAddr | <code>string</code> |  | Secret contract address |

| params | <code>Object</code> |  | Worker params |

| workerGroupSize | <code>number</code> | <code>5</code> | Number of workers to be selected for task |

  

<a name="sendTaskInput"></a>

  

## sendTaskInput(task) ⇒ <code>EventEmitter</code>

Send Task to Enigma p2p network for computation

  

**Kind**: global function

**Returns**: <code>EventEmitter</code> - EventEmitter to be listened to track submission of Task to Enigma p2p network. Emits

a response from the ENG network indicating whether client is ready to track the remainder of the task lifecycle

  

| Param | Type | Description |

| --- | --- | --- |

| task | <code>Task</code> | Task wrapper for contract deployment and regular tasks |

  

<a name="getTaskResult"></a>

  

## getTaskResult(task) ⇒ <code>EventEmitter</code>

Get task result from p2p network

  

**Kind**: global function

**Returns**: <code>EventEmitter</code> - EventEmitter to be listened to track getting result from Enigma network. Emits

a response from the ENG network.

  

| Param | Type | Description |

| --- | --- | --- |

| task | <code>Task</code> | Task wrapper for contract deployment and regular tasks |

  

<a name="pollTaskStatusGen"></a>

  

## pollTaskStatusGen(task, withResult)

Generator function for polling the Enigma p2p network for task status

  

**Kind**: global function

  

| Param | Type | Description |

| --- | --- | --- |

| task | <code>Task</code> | Task wrapper for contract deployment and regular tasks |

| withResult | <code>boolean</code> | Task wrapper for contract deployment and regular tasks |

  

<a name="innerPollTaskStatus"></a>

  

## innerPollTaskStatus(task, generator, emitter)

Inner poll status function that continues to poll the Enigma p2p network until the task has been verified

  

**Kind**: global function

  

| Param | Type | Description |

| --- | --- | --- |

| task | <code>Task</code> | Task wrapper for contract deployment and regular tasks |

| generator | [<code>pollTaskStatusGen</code>](#pollTaskStatusGen) | Generator function for polling Enigma p2p network for task status |

| emitter | <code>EventEmitter</code> | EventEmitter to track Enigma p2p network polling for Task status |

  

<a name="pollTaskStatus"></a>

  

## pollTaskStatus(task, withResult) ⇒ <code>EventEmitter</code>

Poll the Enigma p2p network for a TaskInput's status

  

**Kind**: global function

**Returns**: <code>EventEmitter</code> - EventEmitter to be listened to track polling the Enigma p2p network for a Task status.

Emits a Task with task result attributes

  

| Param | Type | Default | Description |

| --- | --- | --- | --- |

| task | <code>Task</code> |  | Task wrapper for contract deployment and regular tasks |

| withResult | <code>boolean</code> | <code>false</code> | Task wrapper for contract deployment and regular tasks |

  

<a name="serializeTask"></a>

  

## serializeTask(task) ⇒ <code>Object</code>

Serialize Task for submission to the Enigma p2p network

  

**Kind**: global function

**Returns**: <code>Object</code> - Serialized Task for submission to the Enigma p2p network

  

| Param | Type | Description |

| --- | --- | --- |

| task | <code>Task</code> | Task wrapper for contract deployment and regular tasks |

  

<a name="obtainTaskKeyPair"></a>

  

## obtainTaskKeyPair() ⇒ <code>Object</code>

Deterministically generate a key-secret pair necessary for deriving a shared encryption key with the selected

worker. This pair will be stored in local storage for quick retrieval.

  

**Kind**: global function

**Returns**: <code>Object</code> - Public key-private key pair

<a name="deploySecretContract"></a>

  

## deploySecretContract(fn, args, gasLimit, gasPx, sender, preCode) ⇒ <code>Task</code>

Create a task to deploy a secret contract - creates base task, creates task record, and sends task to the

Enigma network.

  

**Kind**: global function

**Returns**: <code>Task</code> - Task with attributes necessary for task record and Enigma network

  

| Param | Type | Description |

| --- | --- | --- |

| fn | <code>string</code> | Function name |

| args | <code>Array</code> | Inputs for task in the form of [[arg1, '<type>'], ..., [argn, '<type>']] |

| gasLimit | <code>Number</code> | ENG gas limit for task computation |

| gasPx | <code>Number</code> | ENG gas price for task computation |

| sender | <code>string</code> | ETH address for task sender |

| preCode | <code>string</code> | Precode for contract deployment |

  

<a name="computeTask"></a>

  

## computeTask(fn, args, gasLimit, gasPx, sender, scAddr) ⇒ <code>Task</code>

Create a compute task - creates base task, creates task record, and sends task to the Enigma network.

  

**Kind**: global function

**Returns**: <code>Task</code> - Task with attributes necessary for task record and Enigma network

  

| Param | Type | Description |

| --- | --- | --- |

| fn | <code>string</code> | Function name |

| args | <code>Array</code> | Inputs for task in the form of [[arg1, '<type>'], ..., [argn, '<type>']] |

| gasLimit | <code>Number</code> | ENG gas limit for task computation |

| gasPx | <code>Number</code> | ENG gas price for task computation |

| sender | <code>string</code> | ETH address for task sender |

| scAddr | <code>string</code> | Secret contract address |

  

<a name="version"></a>

  

## version() ⇒ <code>string</code>

Return the version number of the library

  

**Kind**: global function

Ainsleys-MacBook-Pro:api ainsleyenigma$
