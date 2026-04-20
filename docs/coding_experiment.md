---
sidebar_position: 4
---

import useBaseUrl from "@docusaurus/useBaseUrl"

# Getting started with OpenVIMO

In this tutorial we will go over coding and running OpenVIMO HITs. We will explain and execute two different designs:

- simple meeting room where anybody with the URL can join the conversation.
- more complex dyadic interaction where subjects fill individual questionnaires and participate in a joint videocall.

### Sample 1: meeting room

Here we create a simple covfee app for meeting rooms with shareable URLs. This can function as a flexible deployment for holding (regular) meetings in covfee and recording them into a dataset.

In covfee experiments (HITs) are always specified by a list of nodes or tasks, and a list of journeys or paths through those nodes. Each of these journeys maps to a URL, and normally to one participant. However, because in this case we want to allow an arbitrary number of participants to join the videocall using the same URL, we will create a single journey. The graph structure of this deployment is therefore very simple:

<img src={useBaseUrl("/img/g_meeting.png")} width="250" />

First we will create a folder for the covfee project. This folder may be anywhere in the file system. Then we will create the following structure:

```text
my_folder
    - app.py
```

Paste the following into `app.py`:

```python
from covfee import HIT, Project, tasks
from covfee.shared.dataclass import CovfeeApp

spec_meeting = {
    "name": "Videocall",
    "serverRecording": {
        "hasAudio": True,
        "hasVideo": True,
        "outputMode": "INDIVIDUAL",
    },
}

t1 = tasks.VideocallTaskSpec(**spec_meeting)
hit = HIT("Videocalling")
hit.add_journey(nodes=[t1])

projects = [Project("My Project", email="example@example.com", hits=[hit])]
app = CovfeeApp(projects)
```

Here we import the necessary classes, create a `VideocallTaskSpecs` object holding the specification of our videocalling task, and assign it to a journey, a HIT and a project ([more information](./concepts.md)). Finally, we create the `app` object. Covfee internally looks for this object by name (important that it is called `app`) and uses it as starting point to read the specification.

That is all! We are now ready to start Covfee:

```bash
# Edit covfee/dev/dev.env and set PROJECT_DIR=/path/to/my_folder
covfee-dev run
```

This will start webpack, the Flask backend, the Node Redux store service, and the standalone `www` fileserver.

After this the Covfee admin panel should be accessible in the URL displayed when running the Covfee server ([http://localhost:5001/admin#](http://localhost:5001/admin#) by default).

### Sample 2: dyadic conversations

This sample creates a more complex HIT structure. Here we want two subjects to fill in individual consent forms, then have a videocall together, and then fill in post-experiment surveys. The graph structure for this interaction is:

<img src={useBaseUrl("/img/g_dyadic.png")} width="600" />

We can code this structure with the following code:

```python
# from covfee import Task, HIT
from covfee import HIT, Project, tasks
from covfee.shared.dataclass import CovfeeApp

spec_consent_form = {
    "name": "Consent",
    "content": {"type": "link", "url": "$$www$$/consent.md"},
    "form": {
        "fields": [
            {
                "name": "name",
                "label": "Full name:",
                "required": True,
                "input": {"inputType": "Input"},
            },
            {
                "name": "consent",
                "label": "To proceed, you must expressly provide consent per the terms above.",
                "required": True,
                "input": {
                    "inputType": "Checkbox.Group",
                    "options": [
                        {
                            "label": "I consent to the sharing of my personal data.",
                            "value": "yes",
                        }
                    ],
                },
            },
        ]
    },
}

spec_instructions = {
    "name": "Instructions",
    "prerequisite": True,
    "content": {"type": "link", "url": "$$www$$/instructions.md"},
    "form": {
        "fields": [
            {
                "name": "agreement",
                "label": "To proceed, you must expressly agree to the provided instructions.",
                "required": True,
                "input": {
                    "inputType": "Checkbox.Group",
                    "options": [
                        {
                            "label": "I agree with the provided instructions.",
                            "value": "yes",
                        }
                    ],
                },
            }
        ]
    },
}

spec_videocall = {
    "name": "Videocall",
}

spec_final_survey = {
    "name": "Final Survey",
    "form": {
        "fields": [
            {
                "name": "enjoyment",
                "label": "Did you enjoy your interaction?",
                "required": True,
                "input": {
                    "inputType": "Radio.Group",
                    "options": ["Yes", "No"],
                },
            }
        ]
    },
}
j1_consent = tasks.InstructionsTaskSpec(**spec_consent_form)
j1_instructions = tasks.InstructionsTaskSpec(**spec_instructions)
j1_final = tasks.QuestionnaireTaskSpec(**spec_final_survey)

j2_consent = tasks.InstructionsTaskSpec(**spec_consent_form)
j2_instructions = tasks.InstructionsTaskSpec(**spec_instructions)
j2_final = tasks.QuestionnaireTaskSpec(**spec_final_survey)

videocall_task = tasks.VideocallTaskSpec(**spec_videocall)


hit = HIT("Dyadic videocall")
j1 = hit.add_journey(nodes=[j1_consent, j1_instructions, videocall_task, j1_final])
j1 = hit.add_journey(nodes=[j2_consent, j2_instructions, videocall_task, j2_final])

projects = [Project("My Project", email="example@example.com", hits=[hit])]
app = CovfeeApp(projects)
```
