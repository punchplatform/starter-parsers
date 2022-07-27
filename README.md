# punch-reference-parser

The proposed default standard layout for a punch parser. This repository is informational only.

## Getting started

A punch parser artifact is, at the end, a zip archive named using a group-id, artifact-id and version number. 
For example punch-sample-parser-1.0.0-artifact.zip.

To build that artifact simply type in 'make'. 
The makefile is simple enough, what it does is to: 

1. test your parser by playing any unit or log file tests you added to your repository
2. generate the metadata.yml descriptor file required to make your archive self documented and visible to the punch UI.
3. zip everything inside the target zip archive.

## Required and Optional files

A parser repository must have the follow mandatory files: 

* metadata/metadata.yml : required to advertise your parser to users. It can contain variables to be automatically filled with the correct version, group and artifact id.
* src/main/punchlang : put in there your punchlets, resource files. 
* src/test/puncher : this is optional but best practice. You can add here unit tests and sample log file tests. 

