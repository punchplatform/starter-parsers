# Punch Parser

Quickstart example to create your parser development project. 

On the punch you develop parsers as part of simple Makefile based
project.

Together the punchlets parse, normalise and enrich the incoming logs, whatever their format is. 
The punch comes equipped with many parsers. Should you write your own, this project is for you.

## Layout

The layout for punch parser is straightfowrward:

```
.
├── INFO                
├── LICENSE
├── Makefile
├── metadata
│   └── metadata.yml     
├── README.md
├── src
│   ├── main
│   │   └── punchlang
│   │       └── com
│   │           └── analytics
│   │               └── web
│   │                   └── apache
│   │                       ├── enrichment.punch
│   │                       ├── http_codes.json
│   │                       └── parser.punch
│   └── test
│       ├── kooker
│       │   └── punchline.yaml
│       ├── puncher
│       │   └── unit.json
│       └── resources
│           └── ecs-fields.csv
└── target
    ├── apache-2.0.0-artifact.zip
    └── tmp
        ├── apache-2.0.0.zip
        └── metadata.yml
```

Where:

* 'src/main/punchlang' is the root folder containing your parser, patterns and resource files. 
* the recommended layout is to use folders com/analytics/web/apache that conform to your group and classifiers.
* `parser.punch` and `enrichment.punch` are sample punchlets. They illustrates the basics. This is where you
  write the actual logic of your log parsing.
* `groks/pattern.grok` is a sample grok pattern. The punch comes with many patterns directly loaded, but here is how you
  can add your own.
* `http_codes.json` is a sample resource files. Check how it is used in the enrichment.punch punchlet.

Tests are provided in the 'src/test' folder. In there:
* puncher is the root folder for unit test executed using the punch provided puncher tool.
* resources and kooker are optional folder to provide extra test facilities.  

You must conform to the src/main/punchlang and src/test/puncher root folders. 


## Test Your Parsers

To test your parsers with your unit tests file simply run :
```sh
make test
```

It will launch a punch tool called `puncher` in a docker container which is in charge of running your tests. Be sure to
have docker installed.

## Deploy Your Parsers

Once your parser is ready and packaged, you can simply refer to it in any punchline. 

Beofr going to a real K8 platform, you can start by executing a foreground docker
image of a punch pipeline that embeds your parser. 

### Foreground Docker Run

Remember a punchline is a log processing pipeline where you chain your parser.
Check out the [test/kooker/punchline.yaml](test/kooker/punchline.yaml) example.
A fake log is generated using the punch generator source, then the parser is invoked, and last a show
sink is used to print the result.

Try it using: 

```sh
make run
```
You will notice that punchline exits after the log has been fully processed. 

### Deploy To Kubernetes

First, deliver your parser to the target kubernetes cluster artifact registry. 
If you do not know what that is, we advise you to use [kooker](https://github.com/punchplatform/kooker).
Kooker is an small open source project to bootstrap a complete K8 cluster automatically equipped with the
additional punch service, including the artifact server. 

Once kooker is started; simply type in:

```sh
make upload
```

That will upload your parser to the artifact server running in Kooker.

From there you can 
You can apply your punchline, start a service and run the simulator by running :
```sh
make apply
```

When the simulator starts, you can exit this command using `Ctrl+c` and run :
```sh
make logs
```

This should show your punchline logs.

To delete everything, simply run :
```sh
make delete
```

### Using shell commands

The Makefile is only here to help. The shell underlying shell commands are the following: 

To upload your parser package to the artifact server:

```sh
curl -X POST "http://artifacts-server.kooker:4245/v1/artifacts/upload" -F artifact=@target/parsers-1.0.0-artifact.zip -F override=true
```

To start your punchline on kubernetes, de not forget to check the artifact service name in `punchline.yaml` file before
executing this command :

```sh
kubectl apply -f punchline.yaml
```

In one window, view your punchline log :

```sh
kubectl logs -f --tail -1 -l punchline-name=test-parser-punchline
```

