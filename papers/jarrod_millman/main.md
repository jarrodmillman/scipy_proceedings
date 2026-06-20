---
# Ensure that this title is the same as the one in `myst.yml`
title: "From fMRI Frustration to Scientific Python: A Perspective"
abstract: |
  In 2004, Matthew Brett and I were frustrated: fMRI analyses were effectively locked inside lab-specific, GUI-driven toolboxes, built on top of closed scientific computing platforms, that made them extremely difficult to inspect, reproduce, or extend.
  This frustration led to a 2005 meeting that brought together a small group of core scientific Python tool builders from astronomy, neuroscience, physics, and statistics, and then to a series of follow-up meetings alternating between Berkeley, Enthought's offices, and other venues.
  This paper traces how that ground-up, cross-disciplinary collaboration, motivated as much by scientific reproducibility as by software aesthetics, helped a small set of unfunded, largely volunteer projects quietly supplant proprietary, well‑established software platforms, transformed a small, informal gathering into a large, well‑organized conference with a cutting‑edge, open, Git‑based proceedings system, and how the same people and patterns later shaped the Scientific Python project.
---

## Introduction

SciPy 2026 marks 25 years since a small group of scientists gathered to ask whether Python could become a serious tool for scientific computing.
This paper traces one thread of that history: the role of UC Berkeley and a single question asked in frustration---not in the spirit of software craftsmanship, but out of a scientist's need to do better research.
It is not a complete history of scientific Python, and it cannot be.
Fernando Pérez, Matthew Brett, Travis Oliphant, John Hunter, Perry Greenfield, and many others were present at the same formative moments, and each of them carries a different account of what happened and why it mattered.
What follows is one perspective, shaped by where I was standing and what I could see from there.

The question that set this story in motion was scientific before it was technical.
fMRI analyses in 2004 typically ran through lab-specific, GUI-driven toolboxes such as SPM---GPL-licensed but written in the proprietary MATLAB environment and owned by a single lab---where a researcher would click through menus, adjust parameters by hand, and paste plots into a presentation, only to find months later that no record existed of what had been clicked or in what order.
The analyses were, in practice, irreproducible.
Matthew Brett's provocation---"Why don't we rewrite SPM in Python?"---was the engineering response to that scientific failure, not the other way around.
The tools we went on to build were the means; the end was brain imaging research that could be trusted, shared, and re-examined.

- Overview of the paper's argument and structure.
- Methodological note: this paper draws on primary sources---mailing list archives, GitHub contributor histories, published proceedings, and grant records.

## The Reproducibility Problem and the Early Ecosystem

<!--
The purpose of NIPY is to make it easier to do better brain imaging research. We believe that neuroscience ideas and analysis ideas develop together. Good ideas come from understanding; understanding comes from clarity, and clarity must come from well-designed teaching materials and well-designed software. The software must be designed as a natural extension of the underlying ideas.

- https://nipy.org/nipy/mission.html#nipy-mission
-->

- Neuroimaging context: why SPM and MATLAB dominated, what was structurally missing, and why domain scientists had little leverage over the tools they depended on.
  The canonical failure mode: a researcher "twiddling controls in a graphical user interface ... pasting plots into PowerPoint" with no record of what was done or in what order.
- The reproducibility problem as the generative frustration: published fMRI results were difficult to verify, pipelines were not scriptable, and no mechanism existed for a reader to re-run an analysis from raw data.
- Personal entry point: joining Berkeley's Brain Imaging Center, encountering the problem alongside Matthew Brett.
- Why the frustration was generative: domain scientists as reluctant but necessary software builders; the insight that bad tooling was a scientific integrity problem, not merely an inconvenience.
- The tension already present at the start: doing the right thing for science inside an academic incentive system that did not reward software work, diluted authorship in open-source models, or reproducibility as a research output.

- State of scientific Python: SciPy 0.1, the Caltech workshop (2002), a few dozen scientists experimenting with Numeric (and a move toward numarray).

:::{important} Lesson 0
The original motivation was not curiosity about Python; it was the realization that our fMRI analyses were effectively irreproducible, locked in opaque, GUI-driven workflows that we and others could neither rerun nor audit.
:::

## The 2005 Meeting and the Follow-up Series

- The 2005 Berkeley meeting: attendees (Hunter, Perez, Oliphant, Greenfield, Taylor, and others), context, and goals.
  The framing was explicitly cross-disciplinary: astronomy, neuroscience, physics, and statistics in one room, united by the same tool frustrations.
- Eric Jones and Enthought: commercial support, Austin as the second node in the meeting rotation, and why Enthought's participation mattered structurally.
- Primary source evidence: the March 2005 scipy-dev threads document specific decisions---NumPy unification, SciPy core + domain packages, packaging as a first-class concern---that can be traced forward to their outcomes.
- The follow-up meeting rotation: Berkeley, Enthought's offices in Austin, collaborators at INRIA; not a one-off event but a deliberate series.
- The December 2007 Berkeley sprint: documented outcomes, Travis Oliphant's acknowledgment of the organizing role, and the announcement of a continuing informal developer meeting series.
- The April 2008 Berkeley sprint: Eric Jones, Robert Kern, Jonathan Taylor, Stefan van der Walt, Fernando Perez, Gael Varoquaux---the graph of connections made visible in one room.
- Technical decisions and their consequences: the NumPy naming decision, the sandbox model, the scikits architecture.
- NIPY: the domain project that motivated the question.
  - First NIH proposal (2005, rejected), second grant (2006, funded), dedicated developers hired (Christopher Burns, Tom Waite, 2007).
  - Concrete outputs: NiBabel, NiPype, DiPy, the core NIPY library.
  - Publication: Millman and Brett, *Computing in Science and Engineering* (2007)---one of the earliest public arguments that Python was ready for serious scientific use, and a direct statement of the reproducibility motivation [@millman2007analysis].
- The 2014 Millman--Pérez chapter "Developing Open Source Scientific Practice" (dedicated to John Hunter) as the retrospective written articulation of the philosophy implicit in these decisions: the "computational research life cycle," the argument that open-source practice *is* scientific practice, and the introduction of "literate computing" via IPython Notebook as distinct from mere literate programming [@millman2014developing].


:::{important} Lesson 1
Small, in-person meetings with mixed domain and tool participants produce durable roadmap decisions---and the mailing list archive provides the paper trail to verify this.
:::

## Building the Conference and Community

- Release management: volunteering for NumPy 1.0.3.1 and SciPy 0.5.2, the SciPy 0.6.x branching, and what it meant to take on that stewardship role without institutional mandate.
- SciPy conference: chairing 2008--2011 as it grew from a small workshop to an international venue.
- Proceedings machinery: the pre-Curvenote tooling; a concrete example of shared documentation infrastructure created before commercial tools existed for the job.
- The SciPy proceedings as a reproducibility experiment in miniature: open, GitHub-based, line-by-line review of scientific articles; reviewers acknowledged by name; source code required alongside papers.
  This was a direct institutional embodiment of the proposals later published in Millman (2012), "Learning from Open Source Software Projects to Improve Scientific Review," *Frontiers in Computational Neuroscience*---written while the proceedings machinery was being built and reflecting what was already being done in practice [@millman2012learning].
- Documentation marathons and doc-days: coordinated global efforts (2008 marathon, Paris doc-day with van der Walt and the INRIA group) as a pattern of collective action across time zones.
- NumFOCUS (2012): why a fiscal sponsor was needed, what it formalized, and the founding board (Oliphant, Perez, Greenfield, Hunter, Scopatz, Millman) as a roster of people who trace their connections partly to the 2005 meeting.
- An honest note on what took longer than expected: the "toward SciPy 1.0" thread dates from 2008; SciPy 1.0 shipped in 2017; packaging pain identified in 2005 was not resolved until wheels in 2014.
  Some problems require a decade of patient infrastructure work, not just a good meeting.
- The tension with academic incentives, revisited: building the conference proceedings, mentoring GSoC students, and maintaining release branches were invisible in tenure files; the community was doing important work that its institutions could not yet see or reward.

:::{important} Lesson 2
The conference and its proceedings are a coordination mechanism and a reproducibility experiment, not just a venue; peer review and citable papers changed who felt welcome to contribute and how contributions were recognized.
:::

## Three Scikits, One Question

- Frame: the "SciPy core + domain packages" model was designed to bootstrap projects until they were large enough to stand alone---and all three of the scikits below outgrew the namespace.
  Each was also a concrete answer to the reproducibility problem: scriptable, testable, auditable alternatives to GUI-driven or proprietary workflows.
- **statsmodels**: Jonathan Taylor's `scipy.stats.models` code, included in SciPy, then removed and dormant; revived via GSoC 2009; GitHub contributor graph (March 2008--May 2009) as primary source.
  Making statistical models inspectable and scriptable rather than produced by a point-and-click interface was itself a reproducibility contribution.
- **scikit-learn**: David Cournapeau's GSoC 2007 project; mailing list evidence of Berkeley mentorship (Millman and Perez advising the sandbox-to-scikits migration); INRIA taking over leadership in 2010---not a coincidence, as Varoquaux and the INRIA group were already part of the Berkeley/Austin/INRIA meeting rotation.
- **scikit-image**: Stefan van der Walt starting the project at Stellenbosch in 2009; already a close collaborator (March 2008 Paris doc-day); his eventual move to BIDS closes the loop.
- **DiPy**: direct heritage of the original neuroimaging question; Eleftherios, Ariel, and others building on the NIPY foundation.
- **Paul Ivanov**: bridging neuroimaging and IPython/Jupyter; another Berkeley-connected figure whose contributions are often invisible in retrospectives.
- **GSoC as a mechanism**: both statsmodels and scikit-learn were bootstrapped partly through GSoC, which provided funded student labor to activate dormant or early-stage code; Berkeley's mentorship role in GSoC was deliberate.
- The "activation of latent code through community" pattern: all three core scikits involved code that existed but was orphaned or dormant before the network activated it.
  Survival depended not on the quality of the code alone but on the existence of a network of people who knew the code existed and cared enough to revive it.
- The proof that the tools worked: by 2015, the full reproducibility pipeline that motivated the original frustration was teachable to undergraduates in a single semester.
  The 2018 paper "Teaching Computational Reproducibility for Neuroimaging" describes a UC Berkeley course (STAT 159/259, Fall 2015) in which students with no prior neuroimaging background "reproduced" published fMRI analyses using Git, Python, NumPy, SciPy, NiBabel, and scikit-learn---graded on whether the instructors could reproduce the students' results [@millman2018teaching].

:::{important} Lesson 3
Domain packages need a network and a home before they can flourish; the scikits model provided both, and the graduation of successful projects out of the scikits namespace is evidence it worked.
:::

## Deliberately Repeating the Pattern

- The problem it was designed to solve: ecosystem fragmentation, duplicated governance overhead, absence of the cross-project coordination that the early SciPy workshops had provided informally.
- SPECs (Scientific Python Ecosystem Coordination documents) as a formal version of the kind of cross-project decisions made in the 2005--2008 mailing list threads.
- Developer summits deliberately modeled on the 2005--2008 meeting rotation: small, mixed, in-person, focused on shared roadmap decisions rather than presentations.
- Cross-project governance: shared infrastructure, common contributor guidelines, coordinated release cycles.
- BIDS as institutional anchor: hosting key maintainers (Perez, Ragan-Kelley, van der Walt, Millman), providing stable employment for open-source work, and making the pattern part of the university's fabric.
  The first-ever dedicated NumPy funding grants (Moore/Sloan, 2017) were hosted through BIDS---a direct line from the informal 2005 meeting to formal institutional support.
- Berkeley OSPO: formalizing the university's commitment so that the collaboration does not depend on individual grants or individual people.
  An explicit answer to the academic incentive tension identified at the start: the university now has a standing commitment to open-source stewardship as an institutional function, not a personal favor.

:::{important} Lesson 4
The original "spirit" is a deliberately replicable pattern, not a historical accident; the Scientific Python project is the most explicit attempt yet to institutionalize it, and OSPO is the attempt to make the institution outlast any single grant cycle.
:::

## What the Berkeley Graph Reveals

- A figure rendering the network: people as nodes, collaborations and projects as edges, institutions (BIC, Enthought, INRIA, BIDS, OSPO) as clusters.
- Key observation: the density and reach of connections that passed through Berkeley---from BIC to BIDS to OSPO---is not obvious from any single project's history but becomes visible only when the whole graph is drawn.
- The graph as a reproducibility argument in its own right: the resilience of the ecosystem derives partly from personal relationships forged at in-person meetings, and those relationships are themselves a kind of undocumented infrastructure.
  Drawing the graph makes that infrastructure visible and checkable.
- Honest caveat: many connections in the graph involve people who did important work largely independently of Berkeley; the claim is not that Berkeley was the only hub but that it was an underacknowledged one, and that underacknowledging it has made the community's own history harder to learn from.

## Conclusion

- Return to the dual central question and its 25-year arc: the engineering question was answered (Python is the language of scientific computing); the scientific question is still being answered (reproducibility remains contested and incomplete).
- What the patterns suggest for future community building: the value of in-person meetings, mixed domain/tool participation, institutional homes that outlast individual grants, and deliberate mechanisms (GSoC, scikits, SPECs, summits) for activating and graduating community work.
- The academic incentive problem is not solved: software, reproducibility infrastructure, and community building are still underrewarded in most institutions.
  OSPO and NumFOCUS are partial answers; the community still needs better ones.
- An invitation to the audience: what is your naïve question---and who do you need to invite to a small meeting to answer it?
