# Day 1 Introduction

What is DevOps?
A bridge between Development and Operations. pipelines and tools. 

What is CI/CD?
Continuous Integration -- merge codes, commit codes, auto build and test, find bugs early
Continuous Delivery/Deployment -- publish into UAT(user acceptance testing) or production environment automatically

no need to roll back if Continuous Deployment is achieved ideally  
security concern

## Problems to Avoid
- out-of-order delivery -- manual confirmation
- try to solve all problems 
- speed over quality 
- Specialized DevOps team -- QA + dev + ops

## Why CI/CD?
- fast feedback
- visualization of build process
- early bug detection
- reduce delivery risk, frequent delivery

## CI/CD pipeline
- local development
- commit -- integrate new features and new codes, code quality check, unit test
- build -- jar or war package, product warehouse
- test -- alpha beta test
- production deployment 


Agile 
plan, design, develop, test, deploy, review

## DevSecOps
Sec for Security
SonarQube -- Code Quality Check, potential vulnerabilities check
configurations management, plain text key / password, vulnerable package detection ... 
- Code Analysis
- Modification Management(?)
- Compliance
- Threat Detection
- Vulnerabilities Assessment
- Training
Tools: Kibana, Grafana, StackStorm, Mirador...

## ChatOps
Use chatbot to do devops job

## GitOps
IaC, auto manage infrastructure
put yml on git, modification trigger CI/CD tools and update infrastructure

## Traditional Application Development Lifecycle
dev, ops, QA, a lot of manual operations, environment problem

DevOps Tools
Gitlab CI, Jenkins, Github Actions


# Day 1 Jenkins

## Why Jenkins
- Java-based integration, delivery and deployment tool chain (jdk required)
- Cross-platform, controller - agent nodes, 
- Open-source and free
- plugins
- pipeline concepts are transferrable to other platform
CI
- svn/git
- maven/ant/gradle/npm
- sonarqube
CDelivery
- SaltStack/Ansible
- Helm/Kubectl/Kustomize
CDeployment
- Jmeter/Soar/K8s

## Installation

Needs jdk -- java-based software.

Allow non-root user to run docker command
``` bash
sudo groupadd docker
sudo usermod -aG docker $USER
# Log out and log back

# difference between -it and -d
# this would immediately exit, cannot find terminal for bash
docker run -d alpine

```

Install Jenkins through Docker, 
```bash
# jenkins controller node
docker pull jenkins/jenkins:lts-jdk17

mkdir -p /data/cicd/jenkins
# chmod +x /data/cicd/jenkins
chmod 777 -R /data/cicd/jenkins

# run controller node -v <jenkins_work_dir>:/var/jenkins_home
docker run -itd --name jenkins \
-p 8080:8080 \
-p 50000:50000 \
--privileged=true \
--restart=on-failure \
-v /data/cicd/jenkins:/var/jenkins_home \
jenkins/jenkins:lts-jdk17

# -e JAVA_OPTS="-Dorg.apache.commons.jelly.tags.fmt.timeZone='Asia/Shanghai'"

docker logs -f jenkins

# run agent node
docker run --name agent --init jenkins/inbound-agent -url http://jenkins-server:port <secret> <agent name>

docker run --name agent --init jenkins/inbound-agent:jdk17 -url http://35.155.159.203:8080/ -workDir "/home/jenkins/agent" -secret 5dc157ace84f39c2c8c0111768e459d1ba5f52a3acc9eb59a42cf884537051c3 -name AgentNode

# Problem : http://<url>/tcpSlaveListener/ appears to be publishing an invalid X-Instance-Identity.
# Solved by installing instance identity plug-in


```

Set up swap file in linux
```
sudo /bin/dd if=/dev/zero of=/var/swap.1 bs=1M count=1024
sudo chmod 600 /var/swap.1
sudo /sbin/mkswap /var/swap.1
sudo /sbin/swapon /var/swap.1

# add this to /etc/fstab
/var/swap.1   swap    swap    defaults        0   0

# restart the container

```

## Jenkins Directories
Configuration are stored in xml files
hard to achieve high availability, better put at SSD
caches and workspace (?)

## Jenkins User Management
Jenkins maintains its own user database, also support LDAP, Github -- delegate to servlet container
Dashboard - Manage Jenkins - Users
Can configure token, default view for every user.

## Jenkins Project Types and Parameters
Default -- freestyle project
Configurations
- Can discard old builds to save space
- Can configure parameters (string, choice, boolean), which should be specified during building.
- Concurrent Build if necessary -- there would possibly be ``ItemName@time`` folder under ``/home/jenkins/agent/workspace`` if built concurrently
- Restrict where the project can be run (prefer labels to names)
- Build trigger -- remotely, after other built, periodically, poll SCM (overhead of scan, push trigger is preferred)
- Build step -- run shell command, notice that you can refer to parameters by `${parmName}`
- Post-build actions -- archive artifacts, trigger other built (notice that if you configure both upstream and downstream projects, only one build will be triggered; use join plugin if you want to trigger one downstream for multiple upstream, or turn to **pipeline**)

## Jenkins Pipeline
Install pipeline plugins and look similar to freestyle. You can write pipeline script.

To trigger build, use curl/httpGET/Postman, with authentication information
``JENKINS_URL/job/MyPipeline/build?token=TOKEN_NAME`` 
When parameterized build enable, only use
``JENKINS_URL/job/MyPipeline/buildWithParameters?token=TOKEN_NAME``

``http://35.93.28.74:8080/job/MyFirstPipeline/buildWithParameters?token=devopsToken&Version=1.1.7&EnvType=stag`` should be quoted by double quotes.

In case 403 intercepted, install Strict Crumb Issuer plugin.

## View and Folder
For different users, we can create different **views** to limit their permissions

We can also create folders as items for better organizing, which can be further composed with views.

regex ``.*`` and ``.*?`` the former match greedily.

## Permission Management
Plug-in: Role-based Authorization Strategy
 Global roles overwrite item/agent roles
 Usually error is reported if global read is not granted

## Credentials Management
username and credentials need to be stored securely in Jenkins

Can be accessed through controller console or script in a pipeline, Groovy language

1. Script console
```Groovy
// In Dashboard - Manage Jenkins - Script Console
def jenkinsCredentials = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
  com.cloudbees.plugins.credentials.Credentials.class,
  Jenkins.instance,
  null,
  null
)

for (creds in jenkinsCredentials){
  println(creds.id);
  println(creds.username);
  println(creds.password)
}
```

2. Credential as a job parameter, only credential id needs to be provided

3. In pipeline script
Go to pipeline - pipeline syntax, generate scripts with credentials

```Groovy
// In pipeline steps
script{
	withCredentials([usernamePassword(credentialsId: "${CRED_ID}", 
				  passwordVariable: 'PASSWORD', 
				  usernameVariable: 'USERNAME')]) {

		// some block
		echo "${USERNAME}"
		// if echo "${PASSWORD}", the output will be masked
		writeFile file: 'tmp.txt', text: "${PASSWORD}"
	}
}
```

## BlueOcean UI

Better UI, visualization of pipeline editing, personalization, integration with SCM


# Day 1 GitLab CI/CD

## Why Gitlab CI/CD
- open source
- easy to learn, smaller toolchains
- integration with Gitlab (no web hook needed)
- scalable -- many runners (server-runner, like jenkins)
- faster results -- parallel (also supported by jenkins)
- optimization over delivery -- multi stage, manual deploy,  env and vars.

## Gitlab Instance Installation

First configure SSH port
either change server SSH port or gitlab shell ssh port after installation.

```bash
# after changing port in /etc/ssh/sshd_config.d/*.conf
cat /etc/ssh/sshd_config
sudo semanage port -a -t ssh_port_t -p tcp <newport>

sudo firewall-cmd --add-port=33000/tcp --permanent
sudo firewall-cmd --reload

sudo systemctl restart sshd
```

SSL/TLS
```
1. Client Hello -- TLS version, cypher suite, random number (not encrypted) Session ID if previously communicated (to reuse master key)
2. Server Hello -- supported conf, another random number
3. Server Cert -- Certificate
4. Server Key Exchange -- public key (possibly asking client cert)
5. Server Hello Done
6. Client Key Exchange -- premaster key (encrypted)
7. random 1 + random 2 + premaster = session key (after some function), symmetric from now on
```


```bash
docker pull gitlab/gitlab-ce:14.0.0-ce.0

mkdir -p /data/cicd/gitlab/{config,logs,data}
chmod 777 -R /data/cicd/gitlab/

docker run -d -p 443:443 -p 80:80 -p 222:22 --name gitlab \
--restart always \
-v /data/cicd/gitlab/config:/etc/gitlab \
-v /data/cicd/gitlab/logs:/var/log/gitlab \
-v /data/cicd/gitlab/data:/var/opt/gitlab \
gitlab/gitlab-ce:14.0.0-ce.0
```

configure gitlab instance
```bash
vi /etc/gitlab/gitlab.rb

# exterrnal url

gitlab-ctl reconfigure
```



## Gitlab Runners
Runner types can be instance, group and project-specific, with states locked or paused.

1. Create a runner in GitlabCI, specify tags, description, timeout etc.
2. Use the token to register on the runner machine. (registration token is deprecated )
3. Configure description, tags, executors (if docker, then further configure docker image etc)
4. After registry, you can set it to paused, protected, untagged-job runnable, or locked to current project, in Gitlab CI - Runner dashboard.

Also register can be done non-interactively through cli, or http POST.
The configuration will be saved at ``/etc/gitlab-runner/config.toml``

concurrent (0 means no limit) and check interval (0 means 3 seconds)

CLI: ``gitlab-runner register/list/verify/unregister``

```bash
docker run -d --name gitlab-runner --restart always \ 
-v /var/run/docker.sock:/var/run/docker.sock \ 
-v /srv/gitlab-runner/config:/etc/gitlab-runner \ 
gitlab/gitlab-runner:latest

gitlab-runner register  --url http://172.17.0.2  --token glrt-t1_x9yLxrD-tvy3jzZknvt2
```

## Gitlab Pipeline

In a specific project,
Configuration file ``.gitlab-ci.yml`` and use template.

Settings
General - Public, redundant, git strategy, timeout, status (md or html)
Auto DevOps
Runners - project specific runners, or instance runners
Variables - Like credentials in jenkins, can be masked.
Trigger Tokens - http post url with token to trigger the pipeline

# Day 2 Jenkins Pipeline
## Jenkins Pipeline
Pipeline is the core feature of Jenkins, defined by Jenkinsfile. 
One pipeline consists of multiple stages.
Agents are the machines executing the jobs. Through containerization, agents and resources can be dynamically assigned. 

## Pipeline Project
Use code to configure the pipeline, no need to interact with AI and check configuration one by one. Pipeline is more flexible.

Editing in replay window won't reflect the changes into pipeline configuration.

syntax: declarative and scripted
```Groovy
pipeline{
	agent{ node{ label "build"}}

	options{
		skipStagesAfterUnstable()
	}

	stages{
		stage("Checkout"){
			steps{
				echo "This is not shell's echo"
				println("The same as echo")
				script{
					// Groovy script
					echo "Hello Pipeline"
					println("Groovy also support println")
				}
			}
		}
	}
	post{
		always{
			echo "always do it"
		}
		success{
			echo "do it only on success"
		}
		failure{
			echo "do it only on failure"
		}
		aborted{
			echo "do it only on aborted"
		}
	}
}
```

## Pipeline Syntax
Including snippet generator, declarative directive generator and a lot of useful tools to generate Jenkinsfile statements.

Sometimes the syntax may not be accurate.

## Global Variable Reference
1. ``env``: you can refer to variables as ``"${env.VARNAME}"`` or simple ``${VARNAME}``, you can also add variable into env.
2. ``parms``: can access build parameters
3. ``currenBuild``: properties of current build, like displayName, result etc. Some of them are writable.

## Pipeline Syntax
``pipeline{}`` block defines one whole pipeline.  A pipeline are made up of sections (agent, post, stages, steps) and directives (environment, options, parameters, triggers, ). 

``agent{}`` block defines the agents that can run this pipeline.  ``any`` means any node can run this pipeline; ``label LABEL`` specifies the node with certain labels that can run this pipeline; ``node`` in agent can customize pipeline working directories using ``customWorkspace dir`` in the block; ``none`` means there's no global runner, you have to define agent for each stage.

``stages{}`` block ``stages > stage > steps > scripts``

``post{}`` block, do different things after the pipeline according to the results.

```Groovy
pipeline {
    agent {
        node {
            label "build"
            customWorkspace "/root/agent/"
        }
    }
    
    stages {
        stage('stage1') {
            steps{
                echo "Stage 1, step 1"
                println("jenkinsfile println")
                script{
                    env.PARM="myParm"
                    println("Stage and script")
                    echo "Groovy echo"
                    echo "${currentBuild.result} and ${env.PARM}"
                    currentBuild.result = "FAILURE"
                }
                
            }
        }
    }
    
    post{
        failure{
            echo "Error exists."
        }
    }
}
```

``environment`` can be both pipeline level or stage level.

``options`` use Declarative Directive Generator to generate snippets. Need timestamper plug in if you want to show timestamp in logs. The options would be updated to the project configuration once run. (even in a replay run).

``parameters`` as the GUI parameters. To distinguish between environment variables and parameters, use ``${params.PARMNAME}`` to refer a parameter and ``${env.VARNAME}$`` to refer a global variable. Simply ``${VARNAME}`` would refer to **global variables prior than parameters**. Specially, ``${env.VARNAME}`` may refer to a parameter when there's no such a global parameter. 
In a word, a parameter can become a global variable but the opposite way does not work. 



 ``triggers`` trigger types: cron, github push, scm upstream etc.

``input`` Message and other options, wait for user input. The parameters should be referred as ``${env.VARNAME}`` or simply ``${VARNAME}``, but not ``${params.VARNAME}`` .

``when`` for condition clause. Conditioning on env variables, expression, and some logic operation like ``not`` , ``allOf`` or ``anyOf``.  Also can specify the time to execute the condition statement, before options, before input, before agent, etc.
```
when{
	allOf{
		environment name: "NAME1", value: "value1"
		environment name: "NAME2", value: "value2"
	}
	beforeAgent true
}
```

``parallel`` for testing on different host and architectures. ``failFast true`` one failure would make the parallel stage aborted. 

To avoid ``name@3`` kind of workspace during concurrent build, use UUID
``env.newWorkspace = "/opt/agent/test/${JOB_NAME}-${UUID.randomUUID().toString()}"``
and use ``ws("${env.newWorkspace}"){}`` to change the workspace, and ``cleanWorkspace`` to clean the workspace after build finish (need plugin, the ``cleanWorkspace`` function should also be enclosed by ws block, for parameters settings, use snippet generator).  

```Groovy
env.MYWORKSPACE = "/opt/agent/test${JOB_NAME}-${UUID.randomUUID().toString()}"

ws(env.MYWORKSPACE){
	// steps
	cleanWs()
}
```

## Global Variables in Pipeline
Pipeline Syntax - Global Variables Reference
- ``env`` Environment variables are accessible from Groovy code as `env.VARNAME` or simply as `VARNAME`. You can write to such properties as well (only using the `env.` prefix).  Even though they are case-insensitive, they are case-preserving, 
- ``params`` case sensitive, immutable, **read-only map**. However, build params would be written to env as well, therefore refer to params as much as you can to avoid overwritten to params in env.
```Groovy
if (params.BOOLEAN_PARAM_NAME) {doSomething()}


if (params.getOrDefault('BOOLEAN_PARAM_NAME', true)) {doSomething()}

properties([parameters([string(name: 'BRANCH', defaultValue: 'master')])])
git url: '…', branch: params.BRANCH
```
for multibranch, ``properties`` takes effect when the step is run, but build parameters are consulted before the build begins. (That allows you write somehow)
- ``currentBuild`` some of the variables are writable like result, displayName, description and keepLog

Non-global variable can be defined like
```
def name1 = "devops"
String name2 = "devops"
name3 = "devops"
```

# Day2 Groovy

## Comment
```Groovy
// Single line comment

/*
Multiline
Comment
*/
```

## Identifiers
```Groovy
// Identifiers
def map = [:]
def $var = "dollar start is allowed"
map."quoted identifier" = "is allowed"
```

## String
``` Groovy
def varName = "valValue"
// String
String s1 = 'Single quote does not perform interpolation'
String s13 = '''Triple-single quote does not either'''
String s2 = "Double quote perform interpolation of ${varName}"
String s3 = "Use backslash to escape \", \${no_interpolation}, \u20AC"

String branchName = "release-1.1.1"
println(branchName.split("-"))
println(branchName.split("-")[0])
println(branchName.split("-")[-2] + ' is the branch name')

// Some methods
assert branchName.startsWith("relea")
assert branchName.endsWith("1.1")
assert branchName.contains("ease")
assert branchName.size() == 13
assert branchName.length() == 13
assert branchName.indexOf('ea') == 3

// minus the first match greedily
// no match, then no modification
// .plus is the same as + concatnation
println(branchName.minus('1.1'))  //release-.1
println(branchName - '1.') // release-1.1
println(branchName.minus('1.2')) // release-1.1.1
println(branchName.reverse())

// Get class name
Integer age = 18
println(age.getClass())
println(age.toString().getClass())
```

## List
``` Groovy

// list -- arraylist
def myList = [1,2,3,4,4,'devops']
println(myList.getClass())
// after +/- operator, original myList will not be modified
println(myList.isEmpty())
println(myList + "jenkins") // [1,2,3,4,4,devops,jenkins]
println(myList - 4) // [1,2,3,devops]
// after the following steps, myList will be modified
println(myList << 6) // [1,2,3,4,4,devops,6]
println(myList) // [1,2,3,4,4,devops,6]
println(myList.add(6)) // true
println(myList) // [1,2,3,4,4,devops,6,6]
println(myList.unique()) // [1, 2, 3, 4, devops, 6]
println(myList) // [1, 2, 3, 4, devops, 6]
println(myList.sort()) //[devops,1,2,3,4,6]
println(myList) //[devops,1,2,3,4,6]
// contains(element), size(), count(element)

// getClass() produces wierd output
String[] slist = ["only", "Strings", "are allowed"]
def nlist = [1,2,3,4,4] as int[]
```


## Map
```Groovy
// Keys can be string or number
// 
def map = ['name' : 'John', 'age' : 18, 117 : 118, addr : 123]
println(map)
println(map.getClass())

// Get value, 
println(map.name)
println(map."name")
println(map["age"])
// println(map[addr]) this is illegal
println(map.get("age"))
println(map[117])

// Set value
map.name = "John Doe"
map["name"] = "Jane Doe"

// getOrDefault() 
// keySet() containsKey() 
// values() containsValue()
// remove()


```


## Control Structures

``` groovy
if (){
	// some block
} else if （） {
	// some block
} else{
	// some block
}

switch(var){
	case "val1":
	case "val0":
		// some block
		break
	case "val2":
		// some block
		break
	default:
		println("Case not considered")
}

for (i=0; i<10; i++){}

while(boolean_exp) {}

5.times{}

5.times{i -> println(i)}

for (i in list){}

map.each{k,v ->
println(k + ":" + v)
}
 
```

## Exception Handling
```groovy
try{}
catch (ExceptionClass e){
	println(e)
	throw e
	// or use
	//error "error message"
}
```

## Functions
```Groovy
// defined outside the pipeline
// input type is optional
def PrintMsg(String value){
	println(value)
	return value
}

public String printMessage(){}
```

# Day2 SharedLibrary

Like python import, you can write your own package in Groovy. Every file in shared library is a class in Groovy, and every file includes one or many methods. With the help of SCM, introducing shared library can make codes clean.

Library Structures Example
- src > org > devops > Tools.groovy (like java src, will be loaded into class path when executing pipeline)
- vars > foo.groovy (store **global variables** and light-weight functions that can be called by ``foo.funcName``), getHost.txt (for doc)
- resources > org > devops > foo.json (store resource files like configurations)

Library Setups
In Manage Jenkins - System - Global Trusted Pipeline Libraries, add your repo URL and configure the credentials you use 

Some configuration need to be done: Security - Git Host Key Verification Configuration - Accept first connection.

```Groovy
// src/org/devops/Tools.groovy
package org.devops
class Tools implements Serializable{
	def steps
	Tools(steps) {this.steps = steps}
	def printMsg(value){
	    steps.println(value)
	    return value
	}
}

```

```Groovy
// Declarative Pipeline
// Jenkinsfile in repo
@Library("mylib@main") _
import org.devops.Tools
def tools = new Tools(this)


pipeline{
    agent any
    options {
      skipDefaultCheckout true
    }
    stages{
        stage("build"){
            steps{
                script{
                    value = tools.printMsg("Successfully")
                    println(value)
                }
            }
            
        }
    }
}
```
 
 For multiple shared library
 ``@Library(["myLib1@main", "myLib2@test"]) _``

Underscore means importing every class in the library
```Groovy
// Either this
@Library("mylib@main")
import org.devops.Tools
def tools = new Tools()

// Or this
@Library("mylib@main") _
def tools = new org.devops.Tools()
```

Making use of global variable (improve code reusability)
```Groovy
// Jenkinsfile
// To use global variables, it is recommended to use under score
@Library("mylib@main") _

demoPipeline(
	name: "John Doe",
	id: "001"	
)
```

```Groovy
// vars/demoPipeline.groovy
// can import without annotation
import org.devops.Tools
// pass parameters
def call(Map args){
	def tools = new Tools(this)

	pipeline{
		agent any
		stages{
			stage("build"){
				steps{
					script{
						tools.printMsg("Welcom! " + args.name)
						tools.printMsg("Your id is " + args.id)
					}
				}
			}
		}
	}
}
```

Making use of resources
```Groovy
@Library("mylib@main") _
import org.devops.Tools

def tools = new Tools()

pipeline{
    agent any
    options {
      skipDefaultCheckout true
    }
    stages{
        stage("build"){
            steps{
                script{
	                // There 's no leading blackslash in the relative path'
                    config = libraryResource 'org/devops/foo.json'
                    println(config)
                    // need pipeline utils plugin
                    config = readJSON text: config

					println(config.name)
					println(config."name")
                }
            }
            
        }
    }
}
```

# Day 3 Gitlab CI Pipeline Syntax

## Pipeline Structure:
pipeline > stages > jobs (run on runners)
- pipeline: defined by ``.gitlab-ci.yml``
- stage: basic components of pipeline, run serially
- job: set of scripts to run, jobs are independent (within a stage)
Visualization, lint and build logs

Types: **branch** pipeline, tag pipeline and **merge request** pipeline.

## Syntax

Single-line Comment: start with ``#``

First define the order of stages
```yaml
stages:
  - build
  - test
  - deploy
  - ".post"
```

Then define jobs, specify the stage it belongs to. tags are for runner to pick up jobs
```yaml
jobBuild:
# tags must be a list of tag names, they are logically connected by AND
  tags:
    - build
# stage contains a single object
  stage: build
# script may contain a single object or list of objects
  script:
    - echo "Build"

jobPost:
  tags:
    - build
  stage: .post
  script:
    - echo "Stage .post, pipeline ends."
```


## ``stages``
There are implicitly ``.pre`` and ``.post`` stages for each pipeline, quote then with double quote if you want to refer them in ``stages``.

For multiple jobs in the same stage, configure ``/etc/gitlab-runner/config.toml`` to allow concurrent run on runner.

## ``variables`` 

```yaml
# variables should be a hash
# defining default value
variables:
  BUILD_TOOLS: "maven"
  DEPLOY_ENV: "dev"
stages:
  - build
jobbuild:
  stage: build
  variables:
    BUILD_TOOLS: "ant"
  tags:
    - build
  script:
    - echo "${BUILD_TOOLS} ${DEPLOY_ENV}"
```

Variables defined in ``run pipeline`` will overwrite the default values in yaml file (both pipeline-level and job-level).

## Job config
- ``variables``: job-level variables
- ``stage``: the stage that the job belongs to
- ``tags``: to match runner, the runner must have **all the tags** that the job requires. Since 14.1, tags can refer to variables through ``${VAR_NAME}``
- ``before_script``, ``script``, ``after_script``

## Job control

### ``allow_failure``
- ``allow_failure``: if set to true, then the failure of the job would not effect the following stages (i.e. considered successful), even if they failed.. However, there would be a yellow exclamation mark in pipeline status. 

### ``when``
- ``when: on_success, on_failure, always, manual, delayed, never``
	- ``on_success`` (default): will be run if the previous stage is success or considered success (i.e. ``allow_failure`` set to true)
	- ``on_failure``: will be run if the previous stage is failure, the current job will not be bypassed, but the stage following the current one may still be bypassed. If the previous stage has some job ``allow_failure`` set to true, then the job is considered success.
	- ``delayed``: use with attributes like ``start_in: '30 minuites'``, ``'1 second'``, etc.
	- ``never``: can only be used in a ``rules`` section or ``workflow:rules``

- Skipped jobs in earlier stages, for example manual jobs that have not been started, are considered successful.

- There are two types of manual jobs: optional and blocking, depending on the value of ``allow_failure``.
	- Optional manual job: ``allow_failure`` is true, which is the default setting for jobs that have ``when: manual`` defined outside ``rules``. The status does not contribute to the overall pipeline status.
	- Blocking manual job: ``allow_failure`` is false, which is the default setting for jobs that have ``when: manual`` defined inside ``rules``. The pipeline **stops** at the stage where the job is defined, showing a status of blocked.

### ``retry``
```yaml
# the retry time can be set up to 2
job1:
  script: echo test
  retry: 2

job2:
  script: echo test
  retry:
    max: 2
    when: runner_system_failure
    exit_codes: 137
```

``when`` and ``exit_codes`` are logically OR connected. ``exit_codes`` may have a list as its values.
### ``rules``
``rules`` should contain a list of conditions start with ``if``, ``changes``, ``exists`` or ``when``. 

``rules:if`` should contains CI/CD variable expressions, use ``$VARNAME`` (no curly bracket) to refer to variables, ``==, !=, =~, !~`` for the relations (last two for regex), ``&& ||`` for logical operation.
The order of ``rules:if`` matter! The following expressions would not be evaluated once it enters some ``if`` clause.

``rules:changes`` For new branch pipelines or when there is no Git `push` event, `rules: changes` always evaluates to be true and the job always runs. That explains why as a downstream pipeline, ``rules:changes`` would not work.

``rules:exists`` as it is. ``if``, ``changes``, ``exists`` can be combined together and become a complex rules (logical AND).

``rules:when`` usually be used as the last element of the array of rules, indicating 

``rules:allow_failure`` rule level overrides job level attributes.

``rules:variables`` change variable values in job-level. When ``variables`` and ``rules:variables`` exist at the same job, the latter (if reached) would **overwrite** the former.

``rules:needs`` rule level overrides job level attributes. Allow out-of-order execution.

``rules:interruptible``

```yaml
job:
  rules:
    # manually start job when variable starts with feature
    - if: $CI_MERGE_REQUEST_SOURCE_BRANCH_NAME =~ /^feature/
      when: manual
	# "when" is used alone, always match once reached
    - when: never
```


## Pipeline Control
### ``workflow:rules``
Define whether **the pipeline** would be run or not. May be used with **predefined CI/CD variables**, (not the variables only defined when jobs start). ``when`` in workflow can only be set to ``always/never``. ``exists`` ``changes`` are also supported in ``rules``.

```yaml
workflow:
  rules:
  # Think what would happen if the following two terms are exchanged
    - exists:
	    - Dockerfile
	  when: always
	- if: $CI_PIPELINE_SOURCE == "push"
	  when: never
	
```

### Git control
Adding ``[ci skip]`` or ``[skip ci]`` in commit message will skip the pipeline(still, new pipeline would be created.)

`git push -o ci.skip` may also do the job.

`git push origin main -o ci.variable="BUILD_TOOL=npm"` to specify CI variables.

### ``trigger``
Trigger downstream multi-project pipeline or sub (aka child) pipeline (useful in microservices). ``tags`` configuration is not allowed in a trigger job (trigger job is not run on any runners), the runner is decided by downstream pipeline.

``strategy``: By default, once downstream pipeline is created, the job would be considered successful. To wait for downstream pipeline to finish, use ``strategy:depend``.

Downstream (multi-project) pipeline
```yml
triggerJobName:
  stage: deploy
  trigger:
    project: 'groupName/projectName'
    branch: main
    strategy: depend

triggerJobName2:
  stage: deploy
  trigger:
    include:
      project: 'groupName/repoName'
      ref: 'main'
      file: '/path/to/child/pipeline'
```


Sub-pipeline (child pipeline)

```yaml
# the root repo has several subdirectories
# within each subdirectory there is a ci.yml

app01build:
  stage: build
  trigger:
    include:
      - local: app01/ci.yml
    # trigger job status depend on the downstream pipeline
    strategy: depend

app02build
  stage: build
  # use rules to only rebuild modified sub-project
  rules:
    - changes:
    # rules:changes only support path not starting with /
        - app02/**
      when: always
    - when: never
  trigger:
    include:
    # trigger:include supports path starting with /
      - local: /app02/ci.yml
    strategy: depend
```


## Gitlab CI environment variables

**predefined** CI/CD variables, check documents [document](https://docs.gitlab.com/ee/ci/variables/predefined_variables.html).

## Pipeline Templates

Like Jenkins library, gitlab ci supports templates.

### ``extends`` 
Job name starting with ``.`` is a template job, and can be extended by normal job. Normal job can be extended as well. Maximum allow 11 levels of inheritance. 

**Array is overwritten, hash is merged.** If you want to merge array, use **yaml anchors**.

## ``include``
write template job in a separate file and use ``include`` to import the file.
```yml
include:
  - project: "groupName/projectName"
    ref: "main"
    file: "template.yml"

stages:
  - build

buildJob:
  extends:
    - .mavenBuild
  tags:
    - build
```

Also you can use some pipeline template, but the repo where the template belongs to should be **public** (so is the group)


# Day 3 Trigger Pipeline

## Jenkins
plugin name: **Generic Webhook Trigger**

Use HTTP **GET** request to trigger pipeline. 

If there are variables to specify, use HTTP **POST** and **JSON in the body**, or **key-value pairs in header**, or **request parameter**. 

Use with REST API parameter (key-value) ``token`` to specify **token**. The token can also be set in ``headers`` or ``authorization: bearer token``.

To get the data in the json body, there are different ways.

1. In Jenkins webpage, setting - build trigger - generic webhook trigger - post content parameters. Fill in ``Variable``, ``Expression`` (JSONPath), ``Value Filter`` (regex) and ``Default Value``. 
	In JSONPath, ``$`` means root element, ``.<name>`` or ``['<name>']``for child and ``[<number>]`` for element in an array. To access the variable in the pipeline, use the name that you fill in ``Variables``. For debugging, check the ``Print post content``, ``Print contributed variables`` checkbox. (resolved variables will also be shown in the HTTP response)

2. Make use of readJSON plugin in script section, you only need to specify a single post content parameter, like, ``POST_DATA``
```GROOVY
script{
    // pipeline: utilities steps plug in needed
	jsonResult = readJSON text: "${POST_DATA}"
	id = jsonResult["students"][0]["id"]
}
```


plugin: rebuilder -- ``replay`` cannot access the variables specify in the webhook request, ``rebuild`` support that functions. 

## Gitlab CI/CD
Project Settings - CI/CD - Pipeline Triggers

Token would be generated. In HTTP POST request, specify token and ref. Also add the token into the gitlab CI/CD variables for security.

To pass variables add ``variables[VARNAME]=VALUE`` to the API request.

webhook: when an event happens, post to the url.

# Day 4 Gitlab and Pipeline Triggering

## Source Code Management System

Group creation -> project creation -> import code (empty or existing) -> pull feature branch -> develop and push -> merge request

Merge Request: like pull request in GitHub, merge request is used for merging branches.

Branch based development: main branch; feature branch and main branch; feature, release and main branch.

Integrate Gitlab repo with Jenkins: use snippet generator "SCM checkout", with proper credentials.

Pipeline on pushing: clone codes, build, unit test, scan, informing

## Gitlab Push Event Triggers Jenkins Pipeline (via Webhook) 
Use Gitlab Webhook to trigger jenkins pipeline, information would be included in the body of the HTTP POST request, use readJSON to get required information (i.e. commit ID, git url, branchName etc.)

Optimization-1: 
New branch would also trigger pipeline. Check ``"before"`` in request body, it would be all ``0`` in the case of branch creation. Merge request would also send a trigger request with ``"after"`` all ``0`` for the deleted branch. To avoid these kinds of pipeline triggering, use optional filter in webhook of jenkins.

Optimization-2:
There could be multiple webhooks to support multi-branch (like ``feature`` and ``release``) triggering. Or you can do the filtering at Jenkins side.

To support both webhook and manual triggering in Jenkins, use try-catch block when you phrase request body.

## Jenkins Email Notification

plug-in: extended email

Configure the plug-in in ``Manage Jenkins - System - Extended E-mail Notification``, apply and use **app password** for Gmail or the smtp server will reject the request.

## Gitlab Trigger Gitlab Pipeline

Simply add ``.gitlab-ci.yml`` to the root of repository. 

Optimization-1:
Use ``workflow:rules`` and variables like ``$CI_COMMIT_BEFORE_SHA`` to filter out branch creation or deletion cases.

``script: - export`` to print environment variables.

Optimization-2-1:
To avoid **repeatedly checking out repo and deleting temporary files** after switching jobs, change the ``GIT_CHECKOUT`` variable to ``false`` globally, and in a job in ``.pre`` stage, locally set the variable to be true. This may lead to problems when there are multiple jobs running **parallelly** within one stage.

OR: use artifacts (Optimization-2-2).

## Artifacts and Dependencies

```yml
job1:
  tags:
    - build
  stage: build
  script:
    - echo Hello > hello.txt
  artifacts:
    name: "Custom Name"
    when: always
    paths:
      - hello.txt
    expire_in: "1 week"

job2:
# hello.txt will first be deleted then be downloaded again
  tags:
    - build
  stage: test
  dependencies:
    - job1
  script:
    - cat hello.txt

job3:
# To avoid downloading, explicitly specifying dependencies to be empty
  tags:
    - build
  stage: test
  dependencies: []
  script:
    - echo "hello.txt will be deleted"
```

``dependencies`` and ``needs``
``dependencies`` are for earlier stages but ``needs`` are for jobs in the same stage
``dependencies`` and ``needs`` are not advised to use in combination


## SMTP Configuration for Gitlab (Email Notification)
Configure ``gitlab.rb`` file, use app password for third-party SMTP service.
```rb
gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.gmail.com"
gitlab_rails['smtp_port'] = 587
gitlab_rails['smtp_user_name'] = "username@gmail.com"
gitlab_rails['smtp_password'] = "AppPassword"
gitlab_rails['smtp_domain'] = "smtp.gmail.com"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true
gitlab_rails['smtp_tls'] = false
gitlab_rails['smtp_pool'] = 'peer'
```



# Day 4 Build Tools

## Maven

Download a demo project from spring initializr
Download maven binaries and add the directory to the environment variables
```bash
mvn clean # clean build dir
mvn clean package # package the project
mvn clean install # package and delopy
mvn clean test # unit test
mvn clean package -f /path/to/pom.xml # package with specific pom.xml file
mvn clean package -DskipTests
mvn clean package -Dmaven.test.skip=true # skip unit test
mvn deploy
mvn clean package -s /path/to/settings.xml
```

In maven installation directory, change your settings in ``conf/settings.xml``, typical settings include mirrors, servers, and local repository.

In jenkins pipeline, to specify interpreter, shebang should be in the same line of triple quotes.

```Groovy
sh'''#!/bin/bash
source ./.profile
'''
```

```Groovy
// notice that you cannot use ./ prefix here
junit 'target/surefire-reports/*.xml'`
```

To show unit test results in gitlab-ci, configure gitlab-ce server

```
$ gitlab-rails console
...
irb(main):001:0> Feature.enable(:junit_pipeline_view)
```


```yml
jobname1:
  stage: build
  ...
  artifacts: 
    paths:
      - target/*.jar

jobname2:
  stage: test
  ...
  artifacts: 
    reports:
      junit: target/surefire-reports/TEST-*.xml

# In case of http 500 for artifacts uploading,
# change bind-mount to docker volume for gitlab ce container
```

Gradle: another java dependency management, quite similar to maven.

# Day 5 SonarQube and Nexus

