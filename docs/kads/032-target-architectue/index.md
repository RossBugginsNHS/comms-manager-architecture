# 1. KAD-CM-032 - Maturing Architecture



## 1.1. Summary
To aid the future development of Communications Manager this outlines guidance for how to approach and mature architectural decisions.

Clarify existing Comms Manager Enterprise Architecture - Core capabilities and requirements, how it interacts with people and external systems

1. Identify Styles, Practises and Patterns suitable for Communications Manager
1. Provide Strategic architectural designs, following the identified styles, practises and patterns
1. Outcomes / Decisions from discussions around Maturing Architecture => Target Architecture 

### 1.1.1. Why?
[At some point](https://ddd.mikaelvesavuori.se/readme/on-design), you won't have technical problems, only design problems relating to poor models, poor decisions, and poor structure.

To help avoid this, all projects require a plan, setting out a target that should be followed.

"Responding to change over following a plan" - this is often quoted back when there is talk of an architectural plan being created. However, this is always mis quoted.

Without having a plan, how can you decide not to follow one?...

This part of the agile manifesto actually aims to convey that you should not blindly follow a plan if it is not working for changes that have been introduced that contradict the plan that you have. Not one member of the original Agile manifesto group considered that people would take this to mean that you don't need a plan in the first place!  

Having a plan does not mean you cannot change direction. Having a plan does not mean that you cannot iterate. 

To be able to react to change, you must have a plan. And to be able to iterate effectively, you need a plan that outlines the goals for what you are iterating towards.

### 1.1.2. Background Research

It has included:

- system context review
- architectural document review,
- infrastructure review and
- code base review

### 1.1.3. Intended Audience

- Architects
- Tech Leads
  
### 1.1.4. Key Takeaways

*Document is not a review of the effectiveness of current Architecture / Tech.*

*Communications Manager has evolved organically since inception. To ensure it is able to evolve and sustain capability for future feature requests and increased demands, it's architecture should to be reviewed and target changes suggested.*


**Architectural Guidance on:**

- Target System Context 
- Styles
  - EDA
  - Better defined Microservices
  - Strategic DDD
- Patterns
  - Tactical DDD
  - API First 
  - Choreography
  - CQRQ
  - EventSourcing
  - Async Request Reply

## 1.2. Table of Contents

- [1. KAD-CM-032 - Maturing Architecture](#1-kad-cm-032---maturing-architecture)
  - [1.1. Summary](#11-summary)
    - [1.1.1. Why?](#111-why)
    - [1.1.2. Background Research](#112-background-research)
    - [1.1.3. Intended Audience](#113-intended-audience)
    - [1.1.4. Key Takeaways](#114-key-takeaways)
  - [1.2. Table of Contents](#12-table-of-contents)
  - [1.3. System Context \& Enterprise Architecture Capabilities / Requirements](#13-system-context--enterprise-architecture-capabilities--requirements)
    - [1.3.1. System Context : As Is](#131-system-context--as-is)
      - [1.3.1.1. System Context: As IsDiagram](#1311-system-context-as-isdiagram)
    - [1.3.2. System Context: Target](#132-system-context-target)
      - [1.3.2.1. Context - Consumer, Run Time, Interface - Currently MESH or Single API](#1321-context---consumer-run-time-interface---currently-mesh-or-single-api)
      - [1.3.2.2. Context - Consumer, Configuration, Interface - Does not exist](#1322-context---consumer-configuration-interface---does-not-exist)
      - [1.3.2.3. Context - Management Interface - Does not exist](#1323-context---management-interface---does-not-exist)
      - [1.3.2.4. Context - Development Interface - Does not exist](#1324-context---development-interface---does-not-exist)
      - [1.3.2.5. System Context: Target Diagram](#1325-system-context-target-diagram)
      - [1.3.2.6. Summarised Contexts for integration with Comms Manager](#1326-summarised-contexts-for-integration-with-comms-manager)
      - [1.3.2.7. Why / How change?](#1327-why--how-change)
  - [1.4. Strategic Architecture](#14-strategic-architecture)
    - [1.4.1. Architecture](#141-architecture)
      - [1.4.1.1. Review of NHS D Architectural Principles](#1411-review-of-nhs-d-architectural-principles)
        - [1.4.1.1.1. Comments](#14111-comments)
    - [1.4.2. Domain Analysis: Overview](#142-domain-analysis-overview)
      - [1.4.2.1. What is / Why Domain Analysis is important:](#1421-what-is--why-domain-analysis-is-important)
      - [1.4.2.2. Core Domains](#1422-core-domains)
      - [1.4.2.3. Supporting Sub Domains](#1423-supporting-sub-domains)
      - [1.4.2.4. Generic Domains](#1424-generic-domains)
    - [1.4.3. Domain Analysis: Communications Manager](#143-domain-analysis-communications-manager)

## 1.3. System Context & Enterprise Architecture Capabilities / Requirements

###  1.3.1. System Context : As Is

#### 1.3.1.1. System Context: As IsDiagram

![System Context As Is](static/context-as-is.png)

- When provided with(from a customer):
  - with a batch of multiple NHS numbers,
  - a routing plan id,
  - and associated (non demographic) parameter values
- For each NHS number, Communications Manager will:
  - Acquire demographic information
  - Validate key demographics (eg Alive / Dead)
  - Choose appropriate dispatch providers for the given routing plan
  - Choose the template for that message for the chosen dispatcher (based on the channel - type and the media type() 
  - Load the template with the customer provided non demographic parameter values, and with - demographic parameter values
  - Dispatch the message to the associated user

### 1.3.2. System Context: Target

Currently, (outside of the actual requests to send messages) there is very limited interaction with Communications Manager, it is all with AWS directly. For changing routing, changing templates, getting detailed logs, getting detailed metrics, getting management reports - this all needs tech time to work with AWS directly. 

#### 1.3.2.1. Context - Consumer, Run Time, Interface - Currently MESH or Single API

- Send send message requests

#### 1.3.2.2. Context - Consumer, Configuration, Interface - Does not exist

- Self Service change routing 
- Self service change templates

#### 1.3.2.3. Context - Management Interface - Does not exist

- Onboarding
- Change routing and templates
- Basic Runtime stats

#### 1.3.2.4. Context - Development Interface - Does not exist

- Get detailed metrics 
- Get debug logs

#### 1.3.2.5. System Context: Target Diagram

![Context Target](static/context-target.png)

Integration with Communications Manager, not AWS directly

####  1.3.2.6. Summarised Contexts for integration with Comms Manager

![Context Target Summary](static/context-target-summary.png)

#### 1.3.2.7. Why / How change?

- This can be achieved iteratively
- Will help move towards goal of API driven for customers
- APIs should be created for all interactions with Comms Manager
- Start with dev only apis - ie replace a manual task direct to AWS with an internal use  API
  - APIs can evolve, from dev / support => CM management => customer facing 
- Will better support future teams working with CM
- Reduce need for AWS understanding / daily use
- Eventually only APIS CI/CD should interface between Comms Manager and AWS

## 1.4. Strategic Architecture

### 1.4.1. Architecture 

Architectural <Principles> are adhered to by following Architectural <Styles> that are implemented whilst applying Architectural <Patterns>

*"The Stuff that's hard to change" - Dylan Beattie*

#### 1.4.1.1. Review of NHS D Architectural Principles

1. Deliver Sustainable Services
1. Put our tools in modern browsers    
1. Internet First    
1. Public Cloud First    
1. Build a data layer with registers and APIs
1. Adopt appropriate cyber security standards     
1. Use Platforms    
1. Ask what the user need is    
1. Interoperability with open data and technology standards
1. Reuse before Buy/Build  

NHS Digital provides high level Architectural Principles, however, these are at a high level and are only nominally useful for real world guidance . They require supporting with clear Architectural Styles and Patterns that should be followed.  

##### 1.4.1.1.1. Comments

*todo: where should this go? *

5. Build a data layer with registers and APIs

- We should be API First.
- But should specify EOL / minimum support.
- Should have multiple version support.
- Do not agree with Store once.
- In distributed systems need to allow for eventual consistency and restoring data that - from another system in another bounded context.
- As long as a source of truth is identified, and that process is in place for eventual consistency update on source changing.
- 
8. Ask what the user need is 

- Ensure that User Need and User Want are not interchangeably used / confused

### 1.4.2. Domain Analysis: Overview

#### 1.4.2.1. What is / Why Domain Analysis is important:

- Design so that each service forms a natural fit to a functional requirement. 
- Avoid the trap of letting organizational boundaries or technology choices dictate the - design
- Which functions are closely related
- Which functions are core to the service, and which provide ancillary services
- What is the dependency graph
- Not concerned with technologies or implementation details

Domain Analysis looks at the "Domain", which typically is classified as the "Problem Space".  The current application has its "Solution Space" which maps to / overlays this Domain Analysis.  In the future, their will likely be a different "Solution Space" which maps to this same Domain Analysis, but in a way that aligns with the Architectural guidance in this document.

n.b. "Problem Space" vs "Solution Space" isn't always a great way to consider things, as one persons "Solution" if often another person / teams "Problem".

However, there needs to be a way to show how different parts of the "solution" may overlap different parts of the "problem".  

#### 1.4.2.2. Core Domains

Core domains are the essence of the application, they are what makes Communications manager what it is, and without them the application is something different, or non existent. 

#### 1.4.2.3. Supporting Sub Domains

Supporting subdomains are needed for the Core Domain to fully function, but they could maybe be provided by a more generic solution, but with some customisation.

#### 1.4.2.4. Generic Domains

Generic subdomains are needed for the Core Domain and Supporting Subdomains, but are going to likely be off the shelf systems that provide the functionality.

### 1.4.3. Domain Analysis: Communications Manager 

The communications manager application is split into a number of sub domains. Core domains are the essence of the application, they are what makes Communications manager what it is, and without them the application is something different, or non existent. 

This outline sketch is of the Communications Manager application separated into separate domains.

*(may be better as a photo of a white board, rather than a c4)*

![Domain Analysis](static/cm-domains.png)