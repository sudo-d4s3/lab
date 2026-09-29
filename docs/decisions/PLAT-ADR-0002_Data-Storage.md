# PLAT-ADR-0002: Data Platform

## Status

Pending

## Context

I'm still using TrueNAS Core.
TrueNAS Core has be deprecated for a while so needs to be upgraded reguardless.

In terms of disks the new platform needs to handle ZFS pools and evrything that goes with that.

In terms of access the main things I need are:

 - S3: both blob and tables
    - Blob for backends for various services
    - tables for datalake
 - NFS: standard Unix file share
    - connecting this to the IDP and making accessible over the internet will likely be handled by a service

I would also like this new platform to be managed as IaC the same with the rest of the lab.

## Options

### Upgrade to TrueNAS Scale

#### Pros

 - Familiar UX
 - Easy upgrade
 - First Party Terraform Provider Exists

#### Cons

 - Wants to be more than a data plane
 - Click-ops focused (Terraform provider may lag)

### Move to NixOS + Seaweedfs

#### Pros

 - Declarative
 - Can Scope to exactly what is needed
 - Able to describe the complete system

#### Cons

 - Have to learn Nix
 - Different config lang from the rest of the lab

### Move to CoreOS + Seaweedfs

#### Pros

 - Declarative
 - Base OS automatically updates
 - Services are Containerfiles

#### Cons

 - Have to learn Butane 
 - Different config lang from the rest of the lab
 - defaults to podman/quadlet over docker

## Decision


## Consequences

