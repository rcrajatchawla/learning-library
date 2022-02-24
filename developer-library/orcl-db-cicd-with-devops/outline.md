# Lab Outline: Livelab

## setups
- create compartment
- create database
- create users and groups
- create policies
- create DevOps Project
    - create DevOps repo
- connect to repo via cloud shell
    
## cicd
- connect to ADB as Admin
    - create users/schemas via scripts
- connect to ADB as user1
    - create objects
    - pull objects with SQLcl
- commit objects to repo
- connect to ADB as user2
    - apply code with SQLcl

## cicd part 2
- connect to ADB as user1
    - alter objects
    - pull objects with SQLcl
- commit objects to repo
- connect to ADB as user2
    - apply code with SQLcl
    - view change management tables

# Lab Outline: LARGE LAB

## setups
- create compartment
- create database
- create vault
    - add key
    - create secrets
- create artifact registry
- create users and groups
- create policies
- create github repository
    - clone sample
    - set up SSH access

## cicd
- connect to ADB
- create objects
- pull objects with SQLcl
- commit objects to repo
- create baseline/Version 1.0

## devops
- create project
- link github repo
    - create external
- create build pipeline
    - add stages
- test pipeline and view history
- build artifacts
    - add stage to pipeline
- create repo trigger
    - add trigger to pipeline

## Full Flow
- connect to ADB
- alter table
- pull objects with SQLcl
- commit objects to repo
- view pipeline

## Advanced
- setup oci cli in build pipeline
- trigger creation of ADB via pipeline
- deploy code to ADB via SQLcl in runner
    - install SQLcl
    - get wallet
- delete ADB
- check logs
- add event for logs to check for ORA errors

