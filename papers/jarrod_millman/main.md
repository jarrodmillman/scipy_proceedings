---
# Ensure that this title is the same as the one in `myst.yml`
title: "From fMRI Frustration to Scientific Python: A Perspective"
abstract: |
  In 2003, Matthew Brett and I were facing a concrete problem: fMRI analyses were effectively locked inside lab-specific, GUI-driven toolboxes built on proprietary scientific computing platforms that made them extremely difficult to inspect, reproduce, or extend.
  Addressing that problem led to a 2005 meeting at UC Berkeley that brought together a small group of core scientific Python tool builders from astronomy, neuroscience, physics, and statistics, and then to a series of follow-up meetings alternating between Berkeley, Enthought's offices in Austin, and other venues.
  This paper traces how that ground-up, cross-disciplinary collaboration---motivated as much by scientific integrity and community ownership as by software aesthetics---helped a small set of unfunded, largely volunteer projects quietly supplant proprietary, well-established software platforms; transformed a small, informal gathering into a large, well-organized conference with an open, Git-based proceedings system; and how the same people, institutions, and patterns later shaped the Scientific Python project.
---

## Introduction

SciPy 2026 marks 25 years since a small group of scientists gathered to ask whether Python could become a serious tool for scientific computing.
This paper traces one thread of that history: the role of UC Berkeley and a question asked in the course of trying to do better science---not in the spirit of software craftsmanship, but out of working scientists' need to do better research.
It is not a complete history of scientific Python, and it cannot be.
Many others were present at the same formative moments, and each of them carries a different account of what happened and why it mattered.
What follows is a perspective shaped by where I was standing and what I could see from there.

This paper follows that thread from the neuroimaging software problem at Berkeley, through the early scientific Python environment and the 2005 Berkeley meeting, into the growth of the SciPy conference and scikits, and finally to the Scientific Python project and Berkeley's Open Source Program Office (OSPO).
It draws lessons about how domain-specific technical and methodological pressures, cross-disciplinary collaboration, and institutional support combined to turn a scattered set of volunteer projects into shared, community-governed infrastructure.

:::{important} Methodological Note
This paper draws on private emails as well as primary sources---mailing list archives, GitHub contributor histories, published proceedings, and grant records.
:::

## The Neuroimaging Software Problem

In 2003, I was the system administrator for the Brain Imaging Center (BIC) at UC Berkeley, responsible for an fMRI analysis pipeline that was, in practice, an _ad hoc_ collection of MATLAB scripts, IDL routines, C/C++ Programs, AWK one-liners, and shell glue that almost nobody outside the lab fully understood.
Published fMRI results from our lab and others were nearly impossible to verify: the typical methods section said something like "SPM2 was used," with no record of which parameters had been set, in which order, or by whom.
There was no mechanism for a reader---or even a returning postdoc---to rerun an analysis from raw data, and by then I had already spent time thinking about provenance tracking for fMRI data as part of a collaboration with the fMRI Data Center at Dartmouth, funded through the Human Brain Project (NIH P20 MH72580-01).
The tooling was a scientific integrity problem masquerading as a technical inconvenience.
The academic incentive system made it worse: writing and maintaining analysis software did not appear in tenure files, open-source authorship diluted traditional credit models, and reproducibility was not a recognized research output.

Matthew Brett, then a visiting researcher at BIC, had arrived at a similar set of concerns by a different route.
His concern was less about reproducibility _per se_ and more about the political structure of neuroimaging software itself: SPM---the dominant fMRI analysis package---was written in MATLAB, GPL-licensed in name but effectively controlled by a single lab at UCL, which meant that the tools an entire field depended on were governed by one group's research agenda.
If you disagreed with their methodological choices, your options were to fork a lab-governed project or start from scratch.

Matthew's instinct---to escape that lab-controlled structure by building something open and collaborative---and my instinct---to replace an unreproducible pipeline with something scriptable and auditable---converged on a concrete goal: to create a new, community-owned neuroimaging platform in Python that could support rapid development, integrate existing tools, and make full analyses from raw data reproducible.
That goal became Neuroimaging in Python (NIPY): a unified, open-source development environment for brain imaging that treated clearly written, clearly explained software and well-designed teaching materials as co-equal parts of doing better science [@10.1109/MCSE.2007.46].
Neither of us arrived at Python with a fully formed philosophy of reproducibility; that philosophy emerged as we worked on NIPY and joined a broader, cross-domain conversation with people in astronomy, biology, and other fields who turned out to share the same problems and saw Python as the way to address them [@millman2014developing].

:::{important} Lesson 0
In fields where the primary scientific instrument is a computational pipeline, doing better science requires treating software as part of the method: analyses must be scriptable and rerunnable from raw data, and the tools that encode those analyses must be owned and governed by the communities that rely on them.
:::

## Scientific Python Before NumPy

In 1995, Jim Hugunin---then a graduate student at MIT---wrote the initial version of Numeric, the first widely used multi-dimensional array package for Python [@10.1038/s41586-020-2649-2].
When Hugunin shifted focus in 1997, Paul Dubois, working at Lawrence Livermore National Laboratory (LLNL), took over as maintainer and provided the kind of institutional support that volunteer projects rarely receive: LLNL computing resources, a home for documentation, and funding for the Numerical Python manual.

In 2001, Travis Oliphant, Eric Jones, and Pearu Peterson merged a collection of modules they had each written independently into a single "superpackage"---SciPy---that extended Numeric with numerical integration, optimization, special functions, and more [@10.1038/s41592-019-0686-2].
By the time of the first SciPy workshop at Caltech in 2002, there was enough critical mass to draw over fifty participants from several fields including astronomy, bioinformatics, physics---mostly researchers who were not primarily programmers, and who wanted Python to do what they needed even though the platform was not yet ready to do it [@scipy02report; @vannini2002scipy02gbb].

By 2004, SciPy as a scientific computing *environment*---distinct from SciPy as a collection of algorithms---had not yet succeeded [@harrington2004direction].
More concerningly, entering scientific Python at this point meant confronting a real architectural problem.
Numeric was the mature, widely used array library, and SciPy and many other early tools were written directly against its data structures [@oliphant2004status].
At the Space Telescope Science Institute (STScI), Perry Greenfield, Todd Miller, and Rick White had developed numarray as a more flexible and maintainable replacement, optimized for the very large astronomical images STScI processed.
New projects were encouraged to build on numarray's new array object, while established libraries such as SciPy remained tied to Numeric, and it was genuinely unclear how---or even whether---those legacy codebases could migrate.
The result was a split community: two incompatible array libraries, active development energy on both, and no agreed plan for unification [@oliphant2004comments].

:::{important} Lesson 1
Scientific Python did not begin with a single, coordinated design; it began with a shared foundation and a set of independent domain projects that each built what they needed.
By 2004, the result was an ecosystem that was succeeding as a collection of algorithms but failing as a platform: fragmented plotting, difficult installation, incompatible array libraries, no packaging infrastructure, and thin documentation.
Fixing the platform---not just the array split---was the prerequisite for further scientific work, and the need to address all of it together is what made the 2005 Berkeley meeting not just useful but necessary.
:::

## The 2005 Meeting and the Future of SciPy

By early 2005, the NIPY team was taking shape: Jonathan Taylor, a statistician at Stanford who had written BrainStat as a Python port of Keith Worsley's fMRI analysis tools, and JB Poline, an fMRI methodologist at INRIA in France who understood both the methods and the politics of the field.
From a different direction, in the course of finding people already using Python for scientific computing, we had begun collaborating with John Hunter (the creator of Matplotlib) and Fernando Pérez (the creator of IPython and later the co-founder of Project Jupyter)---and by February 2005 we were in active correspondence with them about inviting Travis Oliphant and Perry Greenfield to Berkeley.
Travis Oliphant's January 2005 Numeric3 design proposal [@oliphant2005updating] and February status update [@oliphant2005numeric3] had already sharpened the question we needed the meeting to answer: how and can we get Python to be a successful scientific computing environment.

The March 2005 meeting at Berkeley ran for three full days.
The first day was a full-day hands-on laboratory, "Scientific Python for Neuroscience Research," taught by Fernando and John and designed to make SciPy, IPython, matplotlib, and related tools immediately usable for working neuroimaging scientists.
The next two days were split from the start into two parallel tracks: one, anchored by the neuroimagers and statisticians, worked on the structure and rationale of the NIH grant proposal; the other, Travis and Perry---including separate conversations with Guido van Rossum (the creator of Python) and Paul Dubois---discussing whether and how a unified array object could be built that preserved the strengths of both.
Each evening the two groups reconvened: what the grant proposed to fund and what the unification would require were, it turned out, deeply entangled questions, and teaching on day one had already tested how much of the existing tooling was legible to scientists who were not primarily programmers.

Within days of the meeting, two parallel written threads carried its decisions forward.
Travis Oliphant's "Future directions for SciPy in light of meeting at Berkeley" [@oliphant2005berkeley] opened a public discussion that quickly resolved into a concrete plan [@oliphant2005future]: matplotlib would be adopted as the scipy plotting solution; SciPy would be subdivided into a minimal scipy_core---essentially a cleaned-up successor to Numeric---and a separate package (or series of packages) of algorithms; and Travis committed to spending the following five to six months making scipy_core capable of replacing both Numeric and numarray for most users.
That commitment resolved, eighteen months later, in the release of NumPy 1.0 in October 2006 [@oliphant2006numpy1.0].

Perry Greenfield's notes from the separate meeting with Guido van Rossum [@perry2005guido] resolved the upstream question in the opposite direction: including an array package in Python core would, in Guido's assessment, sap energy from the unification work that actually needed to happen.
What Guido was receptive to was more targeted: a new __index__ special method that would let any Python object serve as a sequence or mapping index---closing a gap that rank-0 arrays had exposed---and improvements to the buffer protocol so that extension packages could share allocated memory without depending on a particular array C API.
Those two design decisions became PEP 357 [@oliphant2005pep357] and PEP 3118 [@oliphant2007pep3118], both of which were implemented in CPython and remain part of the language today.

:::{important} Lesson 2
A meeting's value is not only in what it decides but in how it decides: making decisions publicly, on an archived mailing list, with named participants and documented rationale, turns a private conversation into a shared commitment that the broader community can inspect, join, and hold accountable.
The 2005 and 2007 meetings worked not because the right people were in the room but because the room led to threads---and the threads led to NumPy.
:::

## Fostering the Ecosystem

For me, the 2005 meeting's most consequential outcome was personal: a deepening involvement with NumPy and SciPy that was not planned and that the NIPY work had not originally required.
NumPy and SciPy development depended almost entirely on volunteer labor---the main external source of paid development time was Google Summer of Code, administered through the Python Software Foundation.
In 2006, Albert Strasheim worked on integrating libsvm into SciPy using ctypes, mentored by Dave Kammeyer---an effort that left useful code in the sandbox without a clear long-term home [@scipyGSoCsvm].
In the summer of 2007, I mentored David Cournapeau's GSoC project `pymachine` [@cournapeau2007pymachine], which began as an effort to consolidate and expand the scattered machine learning code in the scipy sandbox---expectation-maximization modeling, support vector machines, and related tools---into a single, coherent package, and to give that package a richer notion of data through standardized datasets and readers.
As the work began, the scikits namespace was gaining traction as a home for exactly this kind of domain-specific package: specialized enough that it did not belong in SciPy proper, mature enough to deserve a stable address outside the sandbox.
We decided to place the package in scikits rather than scipy, and to rename it: `scikits.learn` [@millman2007scikitslearn].

Shortly after, I took on the role of NumPy and SciPy release manager [@millman2007releases], and the immediate consequence was a burst of releases that had been held up: NumPy 1.0.3.1 and SciPy 0.5.2.1 shipped in August, and I announced a new release plan---a branch for SciPy 0.6.0 on August 27th, with a target release date of August 31st, followed three months later by a 0.7.x branch [@millman2007releaseplan].
SciPy 0.6.0 shipped in September [@millman2007scipy060], NumPy 1.0.4 in November, and in December I organized the first Berkeley development sprint whose explicit purpose was to address what the March 2005 meeting had set in motion but not completed [@oliphant2007sprint].
Travis's post-sprint summary announced the outcome: a small, informal SciPy board consisting of Eric Jones, Robert Kern, Travis Oliphant, and me, committed to cleaning up SciPy and preparing it for a 1.0 release, and a standing commitment to at least one virtual doc-day or bug-day on the last Friday of every month.
The first doc-day followed within two weeks, on December 28th, coordinated over IRC; I used it to document the sprint's other major resolution: the sandbox would be closed [@millman2007endofsandbox].

The 18-month push from August 2007 to February 2009 was the most concentrated period of platform-building the ecosystem had yet seen.
In 2008, I mentored Alan McIntyre's GSoC project standardizing the NumPy test framework [@mcintyre2008numpytests], work that brought the test infrastructure to a state from which it could eventually migrate---years later---from nose to pytest.
Dag Seljebotn's GSoC project, mentored by Robert Bradshaw, produced Cython integration for NumPy [@seljebotn2008cython]; the SciPy 1.0 release notes later identified 2008 as the year of "first Cython code added" to SciPy---a technical turning point in how the ecosystem handled performance-critical extensions [@scipy2017history].
The April 2008 mini-sprint brought David Cournapeau back to Berkeley along with Eric Jones, Robert Kern, Peter Wang, and Jonathan Taylor for a week of work toward NumPy 1.0.5.
The November 2008 sprint ran simultaneously across two nodes---Berkeley, led by me, and Stellenbosch, South Africa, led by Stéfan van der Walt---the first sprint in which the distributed geography of the network became a deliberate organizational feature rather than a coincidence.
NumPy 1.1.0 shipped in May 2008 and 1.2.0 in September; SciPy 0.7.0 followed in February 2009 [@millman2009scipy070], carrying `scipy.spatial`, unified sparse matrix support, and the move to nose as the standard test runner---the concrete deliverables of the 18-month push [@scipy070notes].
NumPy 1.3.0, released in April 2009 by David Cournapeau [@cournapeau2009numpy130], closed the loop on the mentorship-to-stewardship pipeline: the same person whose GSoC project I had mentored two years earlier was now a release manager in his own right.

The SciPy 2009 Birds of a Feather session on machine learning surfaced enough enthusiasm---and enough competing efforts---to convince Gaël Varoquaux that coordinating around scikits.learn was more productive than starting fresh.
In 2010, Fabian Pedregosa, Gaël Varoquaux, Alexandre Gramfort, and Vincent Michel at INRIA---with Pedregosa now employed full-time on the project---revived scikits.learn and made its first public release on February 1st.
The code Cournapeau had written for a 2007 GSoC mentorship became, under sustained institutional support and within the network the 2005 meeting had seeded, one of the foundational libraries of modern machine learning.

:::{important} Lesson 3
A network without projects dissipates; a project without a network stalls.
The scikits model provided both, and the graduation of successful projects out of the scikits namespace---into scikit-learn, scikit-image, statsmodels, and beyond---is evidence that the design worked.
The survival condition for orphaned scientific code is not technical quality alone but the existence of a network that knows the code exists and can activate it at the moment it becomes maintainable.
:::

## Building the Conference and Community

2008 [@10.25080/HXDH2061] state of scipy [@10.25080/HNCD8803] documentation [@10.25080/PHXV5628; 10.25080/MJHW7647]
2009 [@10.25080/GKRF4753]
2010 [@]


Chairing the SciPy conference from 2008 through 2011 was the organizational parallel: as the meeting grew from a small workshop of a few dozen attendees into an international venue drawing hundreds, someone had to manage the program, establish review standards, and make sure the infrastructure existed for the following year.
The pattern that emerged---voluntary stewardship of shared infrastructure by scientists who were primarily researchers---was not unique to me.

The proceedings machinery built during those conference-chairing years was more than a publishing convenience: it was a reproducibility experiment in miniature.
Papers were submitted as reStructuredText source in a public GitHub repository; review happened in the open, via pull requests attached to identifiable individuals; reviewers were acknowledged by name; and source code was required alongside each paper.
The toolchain---built before any commercial infrastructure existed for the job, using Sphinx, LaTeX, and custom scripts---was an early instance of what would later be called "open peer review," and it instantiated in a specific workflow the principles I was articulating in parallel in a 2012 paper in *Frontiers in Computational Neuroscience*, "Learning from Open Source Software Projects to Improve Scientific Review" [@10.3389/fncom.2012.00018].
That paper was not proposing a hypothetical; it was describing what the proceedings were already doing.
The pre-Curvenote tooling was clunky, slow, and required contributors to understand a build system---but it worked, and it made the conference a place where submitting a paper meant opening your analysis to the same kind of collaborative scrutiny that good open-source development demands.

NumFOCUS, incorporated in 2012, gave the community its first legal and financial home.
The founding board---Travis Oliphant (chairman), Fernando Pérez (secretary), Perry Greenfield, John Hunter, Anthony Scopatz, and myself as president---was not assembled by design; it was assembled by trajectory, a roster of people whose connections could largely be traced back to the 2005 meeting and the follow-up sprints.
But the formation of NumFOCUS also marked an honest acknowledgment of something that had become impossible to ignore: the community was doing substantial, consequential infrastructure work that none of its members' institutions were prepared to recognize or reward.
Conference proceedings, release branches, documentation marathons, and Google Summer of Code mentoring appeared nowhere in tenure files; the people doing this work were doing it out of commitment to the science, not because the academic incentive system had made room for it.
The distance between community capacity and institutional recognition had a precise technical analogue: a "toward SciPy 1.0" thread on the scipy-dev list dates from 2008, but SciPy 1.0 shipped in November 2017---a nine-year gap that reflects not a failure of effort but the reality that some infrastructure problems require a decade of patient, unglamorous work before they are ready to be declared done.
Packaging, the problem Travis had identified in 2005 as the prerequisite for everything else, was not genuinely resolved until the widespread adoption of wheels in 2014.
Some problems cannot be sprint-solved.

:::{important} Lesson 4
The conference and its proceedings are a coordination mechanism and a reproducibility experiment, not just a venue: open peer review and citable papers changed who felt welcome to contribute and how contributions were recognized.
Volunteer stewardship without institutional mandate can build infrastructure that matters---but it accumulates invisible debt, and eventually that debt requires either an institution (NumFOCUS) or a decade (SciPy 1.0) to settle.
:::

## Deliberately Repeating the Pattern

By 2020, the scientific Python ecosystem had succeeded in ways that the 2003 problem had not anticipated: NumPy, SciPy, matplotlib, scikit-learn, scikit-image, and a dozen other projects were each well-maintained, widely used, and internationally recognized.
But their success had reproduced, at the ecosystem level, the same structural problem that the 2005 meeting had been called to solve at the project level.
The ecosystem was "sustained by independent volunteers with separate mailing lists, websites, roadmaps, documentation, engineering and packaging solutions, and governance structures," producing "duplicated effort, disorganized documentation, breakage upon new releases, unintended performance regressions, and user confusion"---the grant's own words, describing not a failure but a coordination gap that individual project health could not close.
There was, as the 2020 planning grant noted, "no venue for developing a formal, shared vision of the future."
The informal coordination layer that the 2005--2008 meeting rotation had provided---the quarterly sprint in which Travis Oliphant, Perry Greenfield, Fernando Pérez, and a dozen others sat together and made the decisions that the mailing lists then ratified---had dissolved as the participants moved to different institutions, took on different roles, and as the ecosystem grew too large for any single room to hold its core maintainers.
The problem Stéfan and I set out to address with the Scientific Python project was structurally identical to the problem the 2005 meeting had been called to address: a distributed network of people who needed to coordinate, and no institutional home in which to do it.

The Scientific Python project, co-founded by Stéfan and me in 2020 with support from the Chan Zuckerberg Initiative and the Alfred P. Sloan Foundation, was not a reinvention of the 2005 pattern but a deliberate formalization of it.
SPECs---Scientific Python Ecosystem Coordination documents---are the formal version of the kind of cross-project decisions that had been made in the 2005--2008 scipy-dev and numpy-discussion threads: opt-in, explicitly multi-project, authored in the open on GitHub, and archived as part of the ecosystem's governance record rather than buried in list archives from 2007.
A SPEC goes through the same process a good mailing list decision always required: proposal, discussion across projects, explicit adoption by each project that chooses to implement it, and a public record of who adopted what and when---but with tooling that makes the process legible to a contributor who was not present at the original conversation.
The annual developer summit, first held in Seattle in May 2023, was modeled explicitly on the 2005--2008 meeting rotation: Stéfan and I spearheaded it precisely because "before the developer summits started in 2023, communication was often confined to specific projects" and people needed a space to come together across project boundaries.
The first summit brought together 34 developers from across the ecosystem for a week-long, in-person sprint on shared infrastructure---build systems, continuous integration, release tooling, sparse arrays---the same unglamorous prerequisite work that the 2005 meeting had identified as the necessary foundation for everything else.
Cross-project governance infrastructure---shared contributor guidelines, coordinated release cycles, common packaging tooling---was the engineering parallel: the answer to the same problem that Travis had named in 2005, now addressed with a decade of additional experience and a community large enough to share the maintenance burden.

The proof that the model had worked appeared in a form the 2003 problem had specified: in the fall of 2015, at Berkeley, a semester-long course (STAT 159/259, "Reproducible and Collaborative Statistical Data Science") centered on a group project in which students with no prior neuroimaging background chose a published fMRI paper with publicly available data, attempted to reproduce its results from raw data using Git, Python, NumPy, SciPy, NiBabel, and scikit-learn, and were graded in part on whether the instructors could reproduce the students' own results.
Matthew Brett and JB Poline---both participants in the 2005 meeting---were on the teaching team.
The 2018 paper describing the course, co-authored with Brett, Ross Barnowski, and Poline, argued that this model---project-based, reproducibility-graded, built on open tools---should become the foundation for future neuroimaging training programs, and proposed it as a transferable model for other fields of computational science [@10.3389/fnins.2018.00727].
The NIPY mission statement's claim that "good ideas come from understanding; understanding comes from clarity, and clarity must come from well-designed teaching materials and well-designed software" had, fifteen years after it was written, become demonstrably true in a graded university course.

The institutional anchor that the 2005 pattern had lacked was provided, finally, by BIDS and Berkeley's OSPO.
The Moore/Sloan grants of 2017, hosted through BIDS, were the first-ever dedicated funding for NumPy---not a side effect of a grant for something else, but a direct investment in the maintenance of shared infrastructure---and they gave the project something it had never had: paid time for Nathaniel Smith and others to work on the technical debt that fifteen years of volunteer stewardship had accumulated.
BIDS as an institutional anchor gave the ecosystem's key maintainers---Pérez, Ragan-Kelley, van der Walt, and myself---stable employment at a research university where open-source work was recognized as a research contribution, and it gave the Scientific Python project a home that was neither a company nor a foundation but a university institute, which meant it could credibly host cross-institutional work without any one project's commercial interests shaping the agenda.
Berkeley OSPO, established in April 2024 within BIDS and part of a coordinated UC-system network of open source program offices, is the explicit institutional answer to the academic incentive tension that has threaded through every section of this paper.
As executive director, I now hold a university role whose job description is open-source stewardship---the same work that conference proceedings, release branches, and documentation marathons represented in 2008, now recognized as a university function rather than a personal favor.
The problem identified in 2003---tools that could not be maintained because institutions did not recognize maintenance as work---now has an institutional form.
The original pattern is deliberately replicable; Scientific Python is the most explicit attempt yet to institutionalize it; and OSPO is the attempt to make the institution outlast any single grant cycle, to make open-source stewardship a function of the university rather than a favor of individuals within it.

:::{important} Lesson 5
The same pattern that produced the 2005 breakthrough---cross-domain, in-person coordination, grounded in shared frustrations and formalized in public archives---can be deliberately replicated when the ecosystem grows beyond what informal networks can sustain.
The difference between 2005 and 2020 is not the pattern but the institution: BIDS and OSPO provide the stable employment, the university recognition, and the cross-grant continuity that the early pattern depended on individuals to supply.
:::

## Acknowledgements

I am grateful to Matthew Brett and Stéfan van der Walt for their careful reading of the manuscript and for thoughtful comments that helped clarify the narrative and sharpen several of its central arguments.
