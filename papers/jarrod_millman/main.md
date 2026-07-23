---
# Ensure that this title is the same as the one in `myst.yml`
title: "One Problem, Many Projects: How Scientific Needs Built an Ecosystem"
abstract: |
  Open, community-owned scientific software does not emerge from good intentions alone; it takes real scientific needs, cross-domain collaboration, community coordination, and institutional anchors.
  This paper traces one thread of how that combination played out: how a concrete problem in neuroimaging led to a 2005 meeting at UC Berkeley, then to a series of collaborations across astronomy, neuroscience, physics, and statistics, and eventually to the coordination structures and academic homes that now help sustain the scientific Python ecosystem.
  It follows that journey from concerns with unreproducible pipelines and single-owner software, through the architectural decisions and invisible maintenance work that turned volunteer projects into shared infrastructure, and into the deliberate replication of those early patterns at larger scale---drawing lessons along the way about what it takes for open, community-owned scientific software to last.
---

# Introduction

Open scientific software infrastructure often begins with a single actor---a researcher, a lab, a company---whose vision, energy, and effort make its creation possible.
And, while that initial focused, isolated effort may be required to start, it matters greatly what happens next.
As the infrastructure grows, its survival depends on two shifts: from single-actor ownership to community governance, and from informal community practice to institutional support.
When those transitions happen, the infrastructure becomes durable.
When they do not, growth stalls and the burden of keeping it alive rests on a shrinking circle of people.

This paper is personal.
It is shaped by the collaborations I was part of and the problems that were in front of me at the time.
Many of the people involved in this work would tell the story differently, from their own vantage points and concerns.

Durable open scientific infrastructure is not built in a day; it has layers to it: a technical foundation, followed by community governance, and then, ideally, institutional support.
Each layer must be deliberately built, and rebuilt, as the infrastructure grows.
Each one marks a moment when a layer that had worked at one scale stopped working at the next, and the community had to decide, consciously or not, whether and how to rebuild it.
But describing those layers only explains how the ecosystem was built; it does not explain why researchers, scientists, and teachers who were not primarily programmers kept doing this work, often unpaid and unrecognized by the institutions that employed them.
That deeper question is the one this paper answers last, because the answer only becomes visible once the full three decades are in view.

# How to start

In 2003, I supported an neuroimaging analysis pipeline at Berkeley that was an _ad hoc_ collection of MATLAB toolboxes, IDL routines, C/C++ programs, AWK scripts, and shell glue that nobody fully understood.
Results from our center and others were difficult to verify.
There was no mechanism to rerun an analysis from raw data.
Scientific integrity was a clear concern, and it was made worse by technical constraints.
The academic incentive system amplified the problem: writing and maintaining analysis software did not appear in tenure files, open source authorship diluted traditional credit models, and computational reproducibility was not a recognized research output.

My colleague, Matthew Brett, was initially concerned less about reproducibility itself and more about the way neuroimaging software concentrated power: the dominant analysis tools were effectively single-owner open source, with one lab setting priorities and methods for the field.
If you disagreed with those methods, your options were to fork that single-owner project or to invest in a community-owned alternative that could be governed by the wider neuroimaging community.
That made the politics of the software visible in a way I had not thought much about before.

## A risky solution

Perhaps naively, Matthew and I set out to build a community-owned neuroimaging platform in Python that would make analyses scriptable from raw data to published results [@10.1109/MCSE.2007.46].
Python looked like the right language, but the surrounding platform was still only half-formed.
Travis Oliphant, Eric Jones, and Pearu Peterson had released `scipy` by combining packages that they each wrote as a "superpackage" of numerical integration, optimization, special functions, and more [@10.1038/s41586-020-2649-2; @10.1038/s41592-019-0686-2].
It was built on top of `numeric`, a multi-dimensional array package maintained by Paul Dubois at Lawrence Livermore National Laboratory.
Jonathan Taylor had already begun `brainstat` for fMRI analysis, John Hunter (`matplotlib`) was developing `pbrain` for EEG and ECoG analysis, and John introduced us to Fernando Pérez (`ipython` and later `jupyter`), connecting our neuroimaging effort to the broader Python tools that were taking shape around it.

At the same time, Perry Greenfield's team at the Space Telescope Science Institute, had introduced `numarray`---a second, incompatible array implementation aimed at very large images; new projects were encouraged to use it, older ones could not easily migrate, and no agreed path to unification existed [@oliphant2004status; @oliphant2004comments].
Meanwhile, the `numpy-discussion` list and the SciPy conference acted as shared communication spaces across this divide, so the same people were discussing `numeric` and `numarray` even as their code bases remained split.

## The 2005 meeting

As we planned a neuroimaging meeting for March 2005 at Berkeley, we were watching the `numpy-discussion` threads that followed Travis's January `numeric3` design proposal [@oliphant2005updating] and February status update [@oliphant2005numeric3].
In those posts, Travis described `numeric3` as a bridge between `numeric` and `numarray`, based largely on the `numeric` code base, and emphasized the need to keep `numeric`'s tight homogeneous-array implementation moving forward while still learning from `numarray`.
The idea that `numeric` and `numarray` should eventually merge was there, but only as an aspiration.
By inviting Travis (`numeric3`) and Perry (`numarray`) to join the Berkeley meeting and to speak with Guido van Rossum (`python`) and Paul (`numeric`), we hoped to turn that aspiration into a shared plan for a unified array object and package.

The first day was a hands-on laboratory, "_Scientific Python for Neuroscience Research_," taught by Fernando and John and designed to make `numeric`, `scipy`, `ipython`, `matplotlib`, and related tools immediately usable for working neuroimaging scientists.
The next two days were split into two parallel tracks: one, focused on neuroimaging; the other, worked through whether and how a unified array object could be built that preserved the strengths of both `numeric` and `numarray`.
Each evening we reconvened to discuss the day's work.

After the meeting, two `numpy-discussion` threads communicated the outcomes and made the unification plan explicit.
Perry's "_Notes from meeting with Guido regarding inclusion of array package in Python core_" [@perry2005guido] concluded that including an array package in Python would be a distraction that would sap energy from the unification work that needed to happen.
Travis' "_Future directions for SciPy in light of meeting at Berkeley_" [@oliphant2005berkeley] opened a public discussion that quickly resolved into a modular plan for SciPy: a minimal, easy‑to‑install `scipy_core` that could replace both `numeric` and `numarray`; `scipy` itself as a separate package (or series of packages) of algorithms built on that core; plotting moved out into its own libraries such as `matplotlib`; and domain-specific packages, for astronomy or neuroimanging, living as independent projects rather than inside `scipy`.
Travis committed to spending the following five to six months making `scipy_core`.
That commitment culminated, eighteen months later, in the release of `numpy 1.0` in October 2006 [@oliphant2006numpy1.0].

## Building the foundation

While transformational in its unification of the ecosystem, `numpy 1.0` was just the beginning of what needed to be built.
A scientific computing platform, comparable to what MATLAB or IDL provided, required far more: a vast collection of mature, performant algorithms spanning optimization, signal processing, linear algebra, statistics, differential equations, and more; a plotting system; an interactive environment; and the documentation, testing, and packaging infrastructure that makes all of it installable, trustworthy, and teachable.
By resolving the array fragmentation that had made building on the ecosystem feel risky, it gave the community a single stable library to build on, together.

<!--
https://github.com/numpy/numpy/graphs/contributors?from=10%2F5%2F2006&to=3%2F13%2F2009
https://github.com/scipy/scipy/graphs/contributors?from=10%2F5%2F2006&to=3%2F13%2F2009
-->
In December 2007, I organized a Berkeley sprint with Travis Oliphant, Eric Jones, Robert Kern, and others, with newer contributors like David Cournapeau and Stéfan van der Walt participating remotely, to put together a `scipy 1.0` roadmap, improve the organizational structure around `scipy`, and plan more activities to help `scipy` development build momentum.
After the sprint, Travis' `scipy-dev` email communicated the outcomes and announced a new `scipy` board (Jones, Kern, Millman, Oliphant) with a specific commitment: monthly virtual doc-days and bug-days, coordinated over IRC, with at least one board member present at each [@oliphant2007sprint].
Over the following two years, David and Stéfan became two of the most active committers to both `numpy` and `scipy`, working alongside long‑time contributors, remote collaborators, and newer volunteers to restructure and expand the library, build and improve the test infrastructure, standardize and vastly expand the documentation, and create development processes and tooling that made contributions from a widely distributed, cross‑disciplinary community sustainable.
By the time `scipy 0.7` shipped in February 2009, the platform that had been rough and fragile in 2007 was something scientists could install, trust, and build on [@millman2009scipy070; @scipy070notes].

## Lesson 1

What started as a neuroimaging software problem did not stay one.
Looking for a way to build community‑owned neuroimaging tools, rather than relying on single‑owner lab software, pulled me and my collaborators into the broader scientific Python effort, because the array foundation that any such shared platform would need was itself unresolved.
Solving our problem meant first helping solve `scipy`'s.

The Berkeley meeting turned out to be pivotal.
What made it work was not the agenda but the room: a hands-on lab that got working scientists using the tools immediately, two parallel tracks that let the neuroimaging discussion and the array negotiation proceed side by side, and evenings when both groups came back together to compare notes.
Travis and Perry were in the building, not posting to a list---and the difference showed in what the discussion produced.

What it produced was a plan: a unified array object, a roadmap that ran through `numpy 1.0` and, two years later, the 2007 sprint's board.
What it did not yet produce was an ecosystem.
The people in that room---neuroimagers, astronomers, statisticians---were oriented around one shared technical problem: having a foundation solid enough to build on.
None of us was yet thinking about how to develop, or hold together, a distributed collection of projects across dozens of scientific domains.

:::{important} Shared problems need shared rooms.
The array unification, the plotting decision, the board and its commitments---none of it came from consensus reached asynchronously.
It came from a small, cross-disciplinary group choosing to be in the same building, more than once, until an aspiration became a plan and a plan became a foundation.
:::

# How to grow

For the first decade, `numpy` and `scipy` developers were essentially the same people, releasing two different packages from the same small, tightly connected group.
The `scipy` toolkits, or `scikits`, began as another layer that we built on the same shared infrastructure, largely with the same hands.
The SciPy conference was organized by that same small group as well.
Over the decade that followed, each of these developed a community of its own, increasingly separate from the others and, eventually, separate from the people who had founded them.
The clearest trace of that shift is almost bureaucratic: `numpy.scipy.org` became `numpy.org`; `ipython.scipy.org` became `ipython.org`; `astropy.scipy.org` became `astropy.org`; and `neuroimaging.scipy.org` became `nipy.org`.
Each rename looks like housekeeping.
Taken together, they mark something larger: SciPy stopped being one thing.
<!--
https://web.archive.org/web/20070622150504/http://new.scipy.org/
https://web.archive.org/web/20100211214922/http://new.scipy.org/
https://web.archive.org/web/20100419182500/http://new.scipy.org/content.html
-->

<!--
Travis Oliphant in his "Future directions for SciPy in light of meeting at Berkeley" 2005 email wrote:
"Many scientists used Python; few were SciPy devotees and even fewer contributed to SciPy."

Stéfan van der Walt in his "The future of SciPy and its development infrastructure" 2009 email wrote:
"SciPy has a large user community relative to the number of developers. A big library of code, used by many scientists, is supported by a small handful of people all over the world."
-->

## Infrastructure and diaspora

In February 2009, Stéfan van der Walt posted to the `scipy-dev` list with a subject line that, unknown to him, echoed Travis Oliphant's email after the 2005 Berkeley meeting: "_The future of SciPy and its development infrastructure_" [vanderwalt2009future].
In Travis' 2005 email, he had worried that there were "few SciPy devotees" and even fewer contributors.
Now, four years later, Stéfan worried that `scipy` was a "big library of code, used by many scientists," but still maintained by only a few people.

Stéfan's diagnosis pointed to a different kind of crisis: getting commit access was too hard, and contributors' patches could sit for a year or more without feedback.
His proposed solution was distributed version control, formal code review, and higher standards for tests and documentation.
The thread that followed captured the tension the ecosystem was living through.
There was no community consensus about distributed version control at the time; for some of us it looked like necessary infrastructure, for others like unfamiliar machinery that might raise the barrier to contribution rather than lower it.
Travis worried that adding formal process would drive away contributors.
Stéfan and David Cournapeau argued that without it, the burden would become unsustainable.

Gaël Varoquaux---a member of the neuroimaging team---wrote to me the Saturday after SciPy 2009 [varoquaux2009scikitlearn].
He had just run a Birds of a Feather session on establishing a standard machine learning package, building on the `scikits.learn` code from David Cournapeau's 2007 GSoC project that I had mentored.
The enthusiasm had been "huge," he wrote, and he was ready to move quickly.
But "in order to streamline the process, we need a mailing list, version control and a website."

Getting a new project onto the SciPy infrastructure meant asking a busy administrator, sometimes including me, to add it.
The server itself was failing: `httpd` crashed spontaneously and had to be restarted manually, and adding new projects was difficult and time-consuming.
The infrastructure was not failing because anyone was neglectful.
It was old hardware, a layered history of configuration decisions, and an operating system that needed a full reinstall---all sitting under a fast-growing ecosystem and a small group who could no longer maintain it on the side.

Given the server situation, I recommended that Gaël look into other options.
That conversation was not unique; projects from statsmodels to `scikit-image` were reaching the same conclusion by the same route.
What followed was not merely a technical migration but an identity shift.
The `scikits` had started as extensions to `scipy`, built by much the same small group, on the same shared infrastructure.
Once they left, most stopped describing themselves that way at all.
`scikits.learn` became scikit-learn; other projects grew up with no connection to the `scikits` name or idea whatsoever.
What had been imagined as one layer of a tool stack (`numpy`, `scipy`, and the `scikits`) became part of a larger ecosystem of independent projects built on top of a shared foundation.

## Community and conference

For the first six years, Enthought had organized the conference and Caltech's Center for Advanced Computing Research had hosted it, with Enthought sponsoring students and handling logistics.
In 2008, Travis Vaught of Enthought and I served as co-chairs, Gaël Varoquaux as program chair, and a recruited program committee set the program---and, for the first time, a proceedings review process produced published conference proceedings [cite].
2008 was also the first EuroSciPy, held in Leipzig, Germany.
Travis gave the keynote.
In 2009, Prabhu Ramachandran and I co-chaired the first SciPy India Conference.
Travis gave the keynote.
<!--
https://web.archive.org/web/20190909080804/http://scipy.github.io/old-wiki/pages/EuroSciPy2008.html
https://web.archive.org/web/20251016230532/https://scipy.in/2009
https://www.space-kerala.org/first-indian-scipy-conference-held-trivandrum
-->

Conference proceedings, published and citable, were a mechanism for converting that invisible work into something a tenure file could recognize.
Papers were submitted as reStructuredText source in a public GitHub repository; review happened in the open, via pull requests attached to identifiable individuals; reviewers were acknowledged by name.
The toolchain---developed with Gaël Varoquaux and extended by Stéfan van der Walt into a system using Sphinx, LaTeX, custom scripts, and the `procbuild` preview bot---was an early instance of what would later be called "open peer review," embodying the principles I articulated in a 2012 paper titled "_Learning from Open Source Software Projects to Improve Scientific Review_" [cite].
Its goal was explicitly iterative: reviewers and editors worked with authors to guide papers toward acceptance, so that submitting to SciPy meant opening your writing to the same collaborative scrutiny that good open source development demands.

My last year as co-chair for the SciPy conference was 2011.
The people organizing the conference by then were no longer, by and large, the people maintaining the library.
That shift was deliberate in a way the infrastructure diaspora was not.
The `scikits` left `scipy.org` because the server and processes could no longer scale; the conference moved because we wanted it to---from company stewardship to community governance, from one annual meeting in California to a network of meetings on three continents.
Proceedings made it easier for academic developers and users of scientific Python to justify attending; as more of those people came, the organizers naturally became drawn from a broader pool.
By the end of the decade, "SciPy the conference" was clearly its own institution, with its own leadership and rhythms, loosely connected to the library and no longer run by the same small group.

## Lesson 2

A shared technical foundation is not just scaffolding; it is part of what holds a community together, but holding together and staying uniform are not the same thing.
When that foundation stopped scaling, the people who depended on it did not disappear; they found their own paths, and what followed looked, from the outside, like fragmentation.
From the inside it was something closer to growth: the same people, carrying norms first worked out together in Part I, building new things in new places, and discovering by experiment what a much larger ecosystem would need next.

The conference took a different route to the same outcome.
Rather than scattering under pressure, it was deliberately opened: from a company-run event serving one small circle to a community-governed institution with its own proceedings, its own international meetings, and eventually its own leadership drawn from far beyond the group that had built `numpy` and `scipy`.
Where the infrastructure diaspora and the `scikits`' departure were responses to something breaking, the conference's growth was a choice---proof that differentiation did not have to wait for a crisis to happen on purpose.

:::{important} One thing became many.
The `numpy` and `scipy` libraries largely split into communities of their own; the `scikits` scattered and mostly dropped the name; the conference outgrew the people who founded it.
Diaspora was part of that story, but not the whole of it---what actually happened was differentiation, and differentiation is what let a handful of people become an ecosystem.
:::

# How to last

At the end of the second decade we faced a new problem: there was no longer anyone with the mandate, or the mechanism, to hold the ecosystem together.
As individual projects matured, whole scientific domains---neuroimaging, geospatial data science, high-energy physics---developed their own internal coordination structures, mailing lists, and governance conventions.
This domain-level organization was healthy in many respects.
It also meant that an increasing amount of work that was really ecosystem-wide in scope, packaging standards, CI practices, documentation tooling, was being developed in domain-specific silos and reinvented independently across communities.
Newer maintainers and contributors built careers within a single project or domain rather than across several.
The cross-disciplinary experience that those of us who had been there at the beginning carried was no longer naturally transmitted to the next generation.
The `scipy.org` website---nominally maintained by the `scipy` library team, effectively serving as the ecosystem's front door---had become a symbol of that failure of transmission: a shared resource that no one had the mandate to govern.

## Ecosystem coordination

In 2020, Stéfan and I co-founded the Scientific Python Project (SPP) with support from the Alfred P. Sloan Foundation [@scientificpython2020planning] to provide what the ecosystem needed, not the SciPy server's centralized infrastructure, which individual projects had rightly outgrown, but a shared layer above the individual projects: cross-project standards through SPECs, developer summits that worked like the early sprints (small, cross-disciplinary, and work focused), shared tooling and discussion forums, and a common landing page for the ecosystem `scientific-python.org`.
SPECs differ from individual project enhancement proposals (NEPs, SKIPs, and their equivalents) in two important ways: they address ecosystem-wide architectural standards rather than single-project decisions, and because individual projects retain full autonomy, SPECs operate entirely by building trust and consensus; they have no enforcement mechanism.
Early community outreach by Juanita Gomez and project leadership by Brigitta Sipőcz were especially important in turning SPP from a small node into a functioning ecosystem hub.

The 2023 Scientific Python Developer Summit illustrated what this cross-project coordination makes possible.
Henry Schreiner had developed a comprehensive development guide for the `scikit-hep` community, high-energy physics, that covered modern packaging, testing, CI, documentation, and more; in parallel, Dan Allan at the National Synchrotron Light Source II had developed complementary guidelines for his community.
Both guides addressed concerns that had nothing to do with the specific scientific domain and everything to do with the shared challenge of building maintainable scientific Python packages.
At the summit, that work was merged, rebranded as an ecosystem-wide resource, and released as the Scientific Python Development Guide [sppdevguide2023]---a signal, concrete and deliberate, that domain-specific silos could be dissolved when the right forum existed to bring people together.
The summit worked because it worked like the early meetings and sprints: small, cross-disciplinary, organized around real work rather than reports.

The ongoing migration from scipy.sparse matrix objects to sparse arrays is a second example of what cross-project coordination makes possible, and of how the summit mechanism accelerates work that had already begun [sppsparsegrant2022; sppsparsesummit2023].
The migration touches `numpy`, `scipy`, `scikit-learn`, `scikit-image`, `neworkx`, and dozens of downstream libraries simultaneously.
No individual project could plan or execute it alone [scipysparsearray].
Dan Schult---a mathematician and one of the ecosystem's contributors since the early days, whose work on `scipy.sparse` has drawn him into active contribution across `networkx`, `scipy`, and the broader ecosystem---is one example of the cross-project contributor the SPP model is designed to support.
The sparse work had begun before the first summit, but bringing a cross-domain group into the same room accelerated it in the same way the early Berkeley sprints had accelerated `numpy`.

## Academic anchoring

The story so far has been mostly about software: an array object, a library, an ecosystem, a software conference, a coordination layer.
But this ecosystem changed more than code: it changed the people and the places that built it.
Our neuroimaging collaboration brought Fernando Pérez to Berkeley's Brain Imaging Center (BIC) as a research scientist around 2008, where he continued developing IPython and, not long after, co-founded Project Jupyter.
Over the years that followed, scientific Python and Jupyter colleagues who came to Berkeley for a meeting, a sprint, or a few months of collaboration became part of life at the BIC and, later, at Berkeley more broadly.
Min Ragan-Kelley and Stéfan van der Walt were among those who first came to work alongside us this way, one of many such visits I won't try to enumerate.
The pull reached graduate students, too.
Kirstie Whitaker, then a neuroimaging PhD student, has described how conversations with Cindee Madison (part of our neuroimage collaboration) during this period reshaped the direction of her career: one version of a story that played out, in smaller ways, across generations of Berkeley researchers who found themselves drawn into the ecosystem they had only meant to borrow a tool from.

That gravitational pull eventually helped reorganize the university around it.
The same community of scientific Python and Jupyter contributors that had been informally passing through Berkeley helped establish the Berkeley Institute for Data Science (BIDS), which Fernando co-founded, and BIDS' early success fed into the university's decision to launch the College of Computing, Data Science, and Society and, within it, a Data Science major that is now the largest on campus [cite].
The undergraduate curriculum built on that foundation, Data 8 and Data 100, uses NumPy, SciPy, Matplotlib, and Jupyter notebooks as the substrate for the largest introductory and largest upper-division courses at Berkeley, where thousands of students each semester do hands-on computational work without sacrificing rigor [cite].
That curriculum did not stay on campus: more than fifty universities and colleges have since adopted Data 8 or its components, several through Berkeley-supported partnerships with the National Science Foundation's CloudBank project that extended the same open-source, cloud-based teaching stack to community colleges and other UC campuses [cite].

BIDS today is where that convergence is easiest to see.
Fernando is now the Faculty Director, a decade after co-founding it; Stéfan and Min work there on the technical and infrastructure side; Kirstie Whitaker, the graduate student whose career had once been redirected by conversations with Cindee, returned in 2025 as BIDS's Executive Director; and I lead Berkeley's Open Source Program Office from within it: each of us arriving by a different route, at a different time, back to the same institution.
Fernando has written that Berkeley's leadership in this stack "is not 'owned' by Berkeley," but built by scientists who "partner with an extended, distributed community of other researchers and developers to build an ecosystem that benefits all" [cite].
This is the same multi-owner argument this paper opened with in 2003, now stated at the level of a university rather than a single lab.
What comes next is not yet settled.
Fernando has described BIDS as a space for open scholarship, open source, and interdisciplinary collaboration on AI in science and society [cite], and how artificial intelligence reshapes the way scientists write code, teach it, and learn it is the open problem this community will now have to work out together, in much the same way it once worked out the future of the array object in a room in Berkeley.

## Lesson 3

:::{blockquote}
People call us an fMRI lab, but I get really angry with that framing.
If fMRI stopped being the best method to answer questions about the brain and mind, I'd move to another method.
:::

Mark D'Esposito, who directed Berkeley's Brain Imaging Center through the years this story covers, said something close to that to Kirstie Whitaker when she was a graduate student.
He did not care about neuroimaging methods.
He did not care about software.
He cared about answering questions about the brain and mind, and he was happy to use whatever method (and software) best served that aim.

That indifference is what made the ecosystem possible.
D'Esposito hired Matthew Brett, Fernando Pérez, and others as research scientists and gave them room to work on scientific Python and, later, Jupyter.
He did not hire them to build libraries and tools, and he would not have funded them if that had been the pitch.
He hired them because they could help his lab do better science.
Building and sustaining scientific Python was the means by which they did that job, not the job itself.

The same logic is written into our neuroimaging collaboration's mission statement, drafted in the project's early years:

:::{blockquote}
The purpose of NIPY is to make it easier to do better brain imaging research. We believe that neuroscience ideas and analysis ideas develop together. Good ideas come from understanding; understanding comes from clarity, and clarity must come from well-designed teaching materials and well-designed software. The software must be designed as a natural extension of the underlying ideas.

--- https://nipy.org/nipy/mission.html
:::

Software, in that framing, is not the point.
It is what clarity looks like once you have it, and clarity is what better science requires.
We did not set out to build community-owned infrastructure for its own sake; we built it because unreproducible pipelines and single-owner tools were getting in the way of the neuroscience.

That same logic scaled up as the ecosystem did.
Two decades later, the Chan Zuckerberg Initiative's Essential Open Source Software for Science program and the Open Source for Science coalition fund scientific software explicitly as a means to better science, not as an end in itself.
These are institutionalized, field-level versions of exactly what D'Esposito was doing informally in one lab in 2003: funding people to solve scientific problems, and treating the software that results as a byproduct worth sustaining, not the mission itself.

Seen this way, the first two lessons look different in hindsight.
Shared rooms mattered in 2005 not because co-location is inherently better than a mailing list, but because the people in that room shared a scientific problem---a fragmented array foundation was standing between them and the research they actually wanted to do.
The scikits' departure and the conference's growth were not fragmentation for its own sake either; they were differentiation driven by each domain's own science, as astronomers, neuroimagers, and statisticians discovered that solving their problems well required tools shaped by their own questions.
Coordination, proceedings, BIDS, and the OSPO are all real, and all necessary---but none of them explain why any of this started, why it grew, or why it has lasted for three decades.
The explanation is the one Mark D'Esposito gave for his own lab: this ecosystem persists because it remains the best available way to do science and to teach the next generation how to do it, and it will keep persisting only for as long as that stays true.

:::{important} It was never about the software.
People built and sustained this ecosystem because it was, and remains, the best available way to do better science and teach the next generation to do it. The tools lasted because the science needed them to.
:::

# Acknowledgements

I am grateful to Matthew Brett, Stéfan van der Walt, and Kirstie Whitaker for their careful reading of the manuscript and for thoughtful comments that helped clarify the narrative and sharpen several of its central arguments.
