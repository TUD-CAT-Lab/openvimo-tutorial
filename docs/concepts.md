---
sidebar_position: 3
---

import useBaseUrl from "@docusaurus/useBaseUrl"

# Concepts

## What is covfee?

Covfee is an extensible web framework for data collection and annotation, built for **crowd-sourcing** and other online uses. Covfee has the goal of providing a framework for:

- Researchers with little programming knowledge to be able to collect data online using a combination of surveys and more advanced configurable apps such as annotation interfaces, videocalling and online games.
- Programmers and esearchers with more advanced technical skills in Javascript to be able to efficiently prototype and deploy custom interfaces or apps for data collection and annotation; or to adapt Covfee's existing apps. Implementing custom covfee tasks has been boiled down to the writing of a single class (React component). Client-server communication and data storage are handled by Covfee.

#### What covfee is not:

- **A complete platform**. Covfee is still under heavy development and does not offer some features that other annotation software does have. The focus has been on producing a framework to enable developers to contribute such features to the community.
- **Enterprise-level software**. Covfee is meant to facilitate research and experimentation. It is not yet thoroughly tested and contains bugs.

## The covfee workflow

<img alt="Covfee workflow" src={useBaseUrl("/img/covfee_main.png")} width="800" />

Once covfee is installed, working with Covfee as a requestor generally means following the three steps shown in the picture:

1. The requestor creates a project specification in a Python file, which completely specifies the annotation interface.
2. The requestor runs `covfee make`, the script in charge of validating the specification and generating the covfee interface from it. The requestor can now enter the covfee admin panel and obtain anonymized links to each HIT in the specification. A CSV file with all the links can be downloaded to be uploaded to Amazon MTurk or otherwise shared with the annotators or study participants.
3. The requestor may keep track and manage the annotation process using the admin panel. At any time it is possible to download the raw data in JSON and CSV files, which can then be processed locally.

## Covfee Concepts

Here we define the nomenclature used within Covfee to talk about online experiments and their sub-parts.

### The HIT

The main unit in Covfee is possibly the HIT (Human Intelligence Task). Despite the name, in crowd-sourcing settings the term HIT is often used to denote **multiple** tasks performed by an annotator or experiment subject. What distinguishes the HIT is that it is independent of any other HITs and it cannot be (or may not be convenient to) split into independent sub-units. Each HIT is normally identified by a single URL and is paid when completed. In Covfee we generalize this idea by using the term HIT to denote one or more tasks performed by one or more experiment subjects or annotators. For example, a HIT may be an online video conversation, or a game of chess to be completed by two people. The same HIT may include separate demographic questionnaires and post-experiment questionnaires for both participants.

In covfee, a HIT is specified via it's graph structure. Our videocall example can be drawn as follows:

<img src={useBaseUrl("/img/covfee_entities_1.png")} width="800" />

### Journeys and Tasks

A HIT is composed of one or multiple Tasks, and one or multiple Journeys or paths through the Tasks. In Covfee, these paths must be acyclic but may be assymetric (ie. subjects may follow different paths). Each Journey is mapped to a URL by Covfee, which when opened accesses the current node/task for that HIT. In most cases tasks are to be completed in order.

Usinc Covfee consists in specifying one or multiple HITs. Once done, a HIT specification can be instantiated any number of times. Each time, a blank HIT instance with a new, unique set of Journey URLs will be generated.

:::caution
Note the subtle distinction between HIT specification and HIT instance. Strictly, using Covfee involves creating HIT specifications (generic templates). Covfee translates these internally into HIT instances, which are concrete HITs, with resolved URLs attached to their Journeys. In other words, researchers work with HIT specifications, experiment subjects solve HIT instances.

In practice, we might use the term HIT to refer to both specifications and instances when the context is clear.
:::

### Projects

A Covfee Project may contain multiple different HIT specifications. A common case is to specify a HIT per experimental condition. In summary the following is the hierarchy used in Covfee, with examples for a Project, HIT, Journey and Task:

<img src={useBaseUrl("/img/covfee_entities_2.png")} width="600" />

Finally, a Covfee app may also contain multiple independent Projects.
