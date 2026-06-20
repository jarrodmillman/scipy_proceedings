---
# Ensure that this title is the same as the one in `myst.yml`
title: "From fMRI Frustration to Scientific Python: A Perspective"
abstract: |
  Open, community-owned scientific software does not emerge from good intentions alone; it takes real scientific needs, cross-domain collaboration, community coordination, and institutional anchors.
  This paper traces one thread of how that combination played out: how a concrete problem in neuroimaging---fMRI analyses locked in opaque, lab-specific toolboxes---led to a 2005 meeting at UC Berkeley, then to a series of collaborations across astronomy, neuroscience, physics, and statistics, and eventually to the institutions and patterns that now sustain the scientific Python ecosystem.
  It follows that journey from the first frustrations with unreproducible pipelines and single-owner software, through the architectural decisions and invisible maintenance work that turned volunteer projects into shared infrastructure, and into the deliberate replication of those early patterns at larger scale---drawing lessons along the way about what it takes for open, community-owned scientific software to last.
---

## Introduction

Open scientific software infrastructure often begins with a single actor---a researcher, a lab, a company---whose vision, energy, and resources make the first version possible.
That focus is not a flaw; it is a natural way new things get started.
But as the infrastructure grows, its survival depends on a shift: from single-actor ownership to community governance, and from informal community practice to institutional support.
When that transition happens, the infrastructure becomes durable.
When it does not, growth stalls and the burden of keeping it alive falls on a shrinking circle of people.

This paper is personal---shaped by the collaborations I was part of and the problems that were driving me.
Many of the people involved in this work would tell the story differently, from their own vantage points and concerns.
A message threaded through all of it is one the academic world still resists: open source scientific software is not a side effect of research---it is research infrastructure, and it needs to be anchored in the same institutions that support the science it enables.

## The Neuroimaging Software Problem

In 2003, I was the system administrator for the Brain Imaging Center at UC Berkeley, responsible for an fMRI analysis pipeline that was, in practice, an _ad hoc_ collection of MATLAB scripts, IDL routines, C/C++ programs, AWK one-liners, and shell glue that almost nobody outside the lab fully understood.
Published fMRI results from our lab and others were nearly impossible to verify: the typical methods section said something like "SPM2 was used," with no record of which parameters had been set, in which order, or by whom.
There was no mechanism to rerun an analysis from raw data.
We had a scientific integrity problem, exacerbated by technical constraints.
The academic incentive system made it worse: writing and maintaining analysis software did not appear in tenure files, open source authorship diluted traditional credit models, and reproducibility was not a recognized research output.

My colleague, Matthew Brett, had arrived at a related set of concerns by a different route.
His concern was less about reproducibility per se and more about the way neuroimaging software concentrated power: the dominant fMRI analysis tools were effectively single-owner open source, with one lab setting priorities and methods for the field.
If you disagreed with those methods, your options were to fork that single-owner project or to invest in a community-owned alternative that could be governed by the wider neuroimaging community.

Rather than accepting either a single-owner tool or an opaque pipeline, we sought to build a community-owned neuroimaging platform in Python that would make analyses scriptable from raw data and invite contributors from beyond a single lab.
The project took the name Neuroimaging in Python (NIPY) [@10.1109/MCSE.2007.46].
NIPY did not become the single unified platform we had initially imagined---like SciPy itself, it eventually dispersed into a collection of focused, thriving projects: nibabel, nilearn, nipype, dipy, and others [nipyorg].
That is a different story, with a surprisingly similar overall arc.
What matters here is what NIPY set in motion: it drew us into collaboration with the broader ecosystem, where we learned together, shared problems and solutions across domain boundaries, and built the relationships that the rest of this paper traces.

:::{important} Lesson 0
The governance structure of a scientific tool is a methodological choice, not a technical detail.
A single-owner tool concentrates not just code but scientific priorities; a community-owned tool distributes both.
Aiming for the latter---even when the result is not what you imagined---tends to generate more than you expected.
:::

## Scientific Python Before NumPy

When we began working on NIPY, scientific Python was taking shape around Numeric and SciPy, but the ecosystem was not yet coherent.
Numeric, maintained by Paul Dubois at Lawrence Livermore National Laboratory, was the de facto multi-dimensional array package, and in 2001 Travis Oliphant, Eric Jones, and Pearu Peterson built SciPy on top of it as a "superpackage" of numerical integration, optimization, special functions, and more [@10.1038/s41586-020-2649-2; @10.1038/s41592-019-0686-2].
Early SciPy workshops at Caltech drew dozens of scientists from astronomy, bioinformatics, physics, and other fields---mostly researchers who were not primarily programmers and who wanted Python to solve concrete problems even though the stack was still fragile [@scipy02report; @vannini2002scipy02gbb].
For domain scientists, Python already looked promising, but the underlying stack was fragmented enough that building on it felt risky.

The fragmentation was architectural and social at the same time.
Numeric underpinned SciPy and many existing tools, while numarray---developed at the Space Telescope Science Institute under Perry Greenfield's leadership---introduced a second, incompatible array implementation aimed at very large images; new projects were encouraged to use numarray, older ones could not easily migrate, and no agreed path to unification existed [@oliphant2004status; @oliphant2004comments].
Meanwhile, the `numpy-discussion` list and the SciPy workshop acted as shared communication spaces across this divide, so the same people were debating Numeric vs. numarray in public even as their code bases remained split.

:::{important} Lesson 1
Infrastructure and communication must be solved together: a scientific ecosystem needs a single, reliable array foundation and common forums around it if it is to grow beyond scattered experiments into something people can trust and build on.
:::

## The 2005 Meeting and the Future of SciPy

By early 2005, the NIPY team was taking shape: Jonathan Taylor, JB Poline, Fernando Pérez (the creator of IPython and later the co-founder of Project Jupyter), John Hunter (the creator of Matplotlib), Matthew Brett, and me, together with a small international group of collaborators.
As we planned a NIPY meeting at Berkeley for March, we were watching the `numpy-discussion` threads that followed Travis's January Numeric3 design proposal [@oliphant2005updating] and February status update [@oliphant2005numeric3].
In those posts, Travis described Numeric3 as a bridge between Numeric and numarray, based largely on the Numeric code base, and emphasized the need to keep Numeric's tight homogeneous-array implementation moving forward while still learning from numarray.
The idea that Numeric and numarray should eventually merge was there, but only as an aspiration; by inviting Travis and Perry to join the Berkeley meeting and to speak with Guido van Rossum (the creator of Python) and Paul, we hoped to accelerate that aspiration into a shared plan for a unified array object and package.

The first day was a hands-on laboratory, "Scientific Python for Neuroscience Research," taught by Fernando and John and designed to make Numeric, SciPy, IPython, matplotlib, and related tools immediately usable for working neuroimaging scientists.
The next two days were split into two parallel tracks: one, anchored by the neuroimagers and statisticians, focused on an NIH grant proposal; the other, Travis and Perry---including separate conversations with Guido and Paul---worked through whether and how a unified array object could be built that preserved the strengths of both Numeric and numarray.
Each evening the two groups reconvened to discuss the day's work.

After the meeting, two `numpy-discussion` threads communicated the outcomes and made the unification plan explicit.
Perry's "Notes from meeting with Guido regarding inclusion of array package in Python core" [@perry2005guido] concluded that including an array package in Python core would be a distraction that would sap energy from the unification work that needed to happen.
Travis' "Future directions for SciPy in light of meeting at Berkeley" [@oliphant2005berkeley] opened a public discussion that quickly resolved into a concrete plan [@oliphant2005future]: matplotlib would be adopted as the SciPy plotting solution; SciPy would be subdivided into a minimal `scipy_core` and a separate package (or series of packages) of algorithms; and Travis committed to spending the following five to six months making `scipy_core` capable of replacing both Numeric and numarray for most users.
That commitment culminated, eighteen months later, in the release of NumPy 1.0 in October 2006 [@oliphant2006numpy1.0].

:::{important} Lesson 2
Durable architectural decisions need a prepared technical base, sustained in‑person negotiation, and public implementation, not just a single proposal or meeting.
:::

## Building the Foundation

NumPy 1.0 was not the foundation---it was what made the foundation possible.
A scientific computing platform, comparable to what MATLAB or IDL provided, required far more: a vast collection of mature, performant algorithms spanning optimization, signal processing, linear algebra, statistics, differential equations, and more; a plotting system; an interactive environment; and the documentation, testing, and packaging discipline that makes all of it installable, trustworthy, and teachable.
The 2005 meeting had answered the plotting question---Matplotlib would be the SciPy solution---and IPython had been providing the interactive environment since 2001.
What NumPy 1.0 did was unlock pent-up energy: it resolved the array fragmentation that had made building on the ecosystem feel risky, and it gave the developer community a single stable target to build on together.

<!--
https://github.com/numpy/numpy/graphs/contributors?from=10%2F5%2F2006&to=3%2F13%2F2009
https://github.com/scipy/scipy/graphs/contributors?from=10%2F5%2F2006&to=3%2F13%2F2009
-->
In December 2007, I organized a Berkeley sprint with Travis Oliphant, Eric Jones, Robert Kern, and others, with newer contributors like David Cournapeau and Stéfan van der Walt participating remotely, to put together a SciPy 1.0 roadmap, to improve the organizational structure around SciPy, and plan more activities to help SciPy development build momentum.
After the sprint, Travis' `scipy-dev` email communicated the outcomes and announced a new SciPy board (Jones, Kern, Millman, Oliphant) with a specific commitment: monthly virtual doc-days and bug-days, coordinated over IRC, with at least one board member present at each [@oliphant2007sprint].
Over the following two years, David and Stéfan became two of the most active committers to both NumPy and SciPy, working alongside long‑time contributors, remote collaborators, and newer volunteers to restructure and expand the library, build and improve the test infrastructure, standardize and vastly expand the documentation, and create development processes and tooling that made contributions from a widely distributed, cross‑disciplinary community sustainable.
By the time SciPy 0.7 shipped in February 2009, the platform that had been rough and fragile in 2007 was something scientists could install, trust, and build on [@millman2009scipy070; @scipy070notes].

While cleaning up SciPy and preparing for 1.0 release, there was useful code too specialized for `scipy` proper or too immature for `scipy` at the time.
This lead eventually to the birth of the SciPy Toolkits (scikits).
In a personal email, Travis wrote "the role of scikits should not be: 'It goes into scikits because scipy is not modular enough'---we can and should fix the modularity issues of scipy".
That qualifier mattered: the scikits were designed to grow the ecosystem, not to relieve pressure on SciPy's own technical debt.

:::{important} Lesson 3
A stable foundation enables fast-moving experimentation above it---but only if the two are kept separate.
NumPy had to live outside Python core so that it could be updated independently of Python's release cycle; the scikits had to live outside SciPy so that domain-specific code could change its API, move quickly, and take risks that a stable foundation cannot afford.
:::

## Infrastructure and Diaspora

Between conferences, the community did not just meet---it worked together on top of a common technical substrate.
The SciPy server infrastructure, maintained by Enthought, was the shared home for much of the ecosystem: mailing lists, Subversion repositories, issue trackers, and project websites, with projects living at addresses like `ipython.scipy.org`, `numpy.scipy.org`, and `neuroimaging.scipy.org`.
Getting a new project onto that infrastructure meant emailing an Enthought administrator; getting SVN access, a mailing list, or a project page meant waiting on someone else's schedule.
By 2007, the server itself was failing: DNS zones were inconsistent across name servers, `httpd` crashed spontaneously and had to be restarted manually, adding new projects through the Virtualmin control panel "broke the system," and the administrator's own priority list opened with "httpd stops responding spontaneously" and "server load average is frequently too high" [emails2007admin].
The infrastructure was not failing because Enthought was neglectful---they were doing the community a genuine service under real constraints---but it had reached the limits of what a small team supporting a fast-growing ecosystem could maintain on the side.

The second problem was less about hardware and more about process.
In February 2009, Stefan van der Walt posted to the scipy-dev list with a subject line that---unknown to him---echoed Travis Oliphant's from four years earlier: "The future of SciPy and its development infrastructure" [vanderwalt2009future].
Stefan's diagnosis named a different kind of crisis: SVN-based workflows made contribution too hard; patches sat in Trac for a year or more without feedback; and the maintenance burden was concentrated on too few volunteers.
His proposed solution was process---distributed version control, formal code review, higher standards for tests and documentation.
The thread that followed captured the tension the ecosystem was living through: Travis worried that adding formal process would drive away contributors; Stefan and David Cournapeau argued that without it, the burden would become unsustainable.

The projects that felt this friction most acutely were the ones that left first.
Gaël Varoquaux---a member of the NIPY team and a collaborator on the neuroimaging problem that had motivated the whole enterprise---wrote to me on August 29, 2009, the Saturday after SciPy 2009 [varoquaux2009scikitlearn].
He had just run a Birds of a Feather session on establishing a standard machine learning package, building on the `scikits.learn` code from David Cournapeau's 2007 GSoC project that I had mentored.
The enthusiasm had been "huge," he wrote, and he was ready to move quickly---but "in order to streamline the process, we need a mailing list, version control and a website."
He had originally planned to use the SciPy server infrastructure, "but I am starting to feel that there is too much friction (eg I can't easily open up a mailing list, or give someone SVN access, let alone shell access, I have to ask someone)."
His four requirements were direct: a mailing list, version control with a Trac-like interface, web hosting, and---"very important"---"ability to control and give the control to the previous myself, without having to beg and cry."
This conversation was not unique: it was one of many happening across the community at the same moment, as projects from statsmodels to scikit-image were arriving at the same conclusion by the same route.

The response to that friction was a diaspora.
Projects left the SciPy server infrastructure---to Luanchpad, to SourceForge, to Google Code, to GitHub---each choosing a platform that let them control their own mailing lists, repositories, and administration without depending on an external gatekeeper.
What looked from the outside like fragmentation was also, from the inside, a necessary experiment: the community did not yet know what the right development infrastructure and process looked like, and the diaspora gave different projects the freedom to try different answers.
Over the following years the community largely coalesced: GitHub for version control, continuous integration for tests, and rigorous documentation standards---practices that eventually spread back to NumPy and SciPy themselves.

:::{important} Lesson 4
A shared technical infrastructure is not just scaffolding---it is part of what holds a community together.
When it stops scaling, projects will find their own paths, and the diaspora will look like fragmentation.
But if the ecosystem's foundation is solid and its norms are shared, diaspora is also proliferation: the same people, carrying the same practices, building new things in new places---and, in the process, working out by experiment what the next generation of shared infrastructure should look like.
:::

## Stewarding Community and Conference

The SciPy server infrastructure was not the only thing holding the community together.
From 2002 onward, the annual SciPy conference had been a different kind of shared home---not a technical substrate but a social one, the one place each year where astronomers, neuroscientists, statisticians, and physicists who were building tools for each other would actually be in the same room.
The 2005 Berkeley meeting had been possible, in part, because the conference had already created the network: Travis Oliphant, Perry Greenfield, and Fernando Pérez all knew one another through SciPy.
The scikit-learn Birds of a Feather session at SciPy 2009 that reinvigorated scikits.learn was not an accident of scheduling; it was the conference doing what it had always done---putting people in a room and letting them find the next problem.

While the SciPy server infrastructure was dispersing the community toward GitHub, the conference itself was moving in the opposite direction: from company ownership toward community ownership.
From 2002 to 2007 the conference had been organized by Enthought and hosted by Caltech's Center for Advanced Computing Research, with Enthought sponsoring students and providing local logistics.
In 2008 that changed: Travis Vaught of Enthought and I served as co-chairs, Gaël Varoquaux as program chair, a recruited program committee set the program, and---for the first time---a proceedings review process produced the first published SciPy conference proceedings [cite].
I continued as co-chair through 2011 to ensure continuity and to refine the process [cite], and later NumFOCUS took over formal stewardship of the conference, completing the transition that Enthought had generously begun.

The proceedings machinery we built was designed to solve a problem the paper has already named: academic researchers who contributed to open source software could not get formal credit for it.
Conference proceedings, published and citable, were a mechanism for converting that invisible work into something a tenure file could recognize.
Papers were submitted as reStructuredText source in a public GitHub repository; review happened in the open, via pull requests attached to identifiable individuals; reviewers were acknowledged by name; and source code was required alongside each paper.
The toolchain---developed with Gaël Varoquaux and extended by Stéfan van der Walt into a system using Sphinx, LaTeX, custom scripts, and the `procbuild` preview bot---was an early instance of what would later be called "open peer review," embodying the principles I articulated in a 2012 paper titled "Learning from Open Source Software Projects to Improve Scientific Review" [cite].
Its goal was explicitly iterative: reviewers and editors worked with authors to guide papers toward acceptance, so that submitting to SciPy meant opening your analysis to the same collaborative scrutiny that good open source development demands.
This paper itself sits inside that lineage.

:::{important} Lesson 5
The conference is a generative mechanism, not just a reporting venue: it supports the networks through which cross-domain collaboration happens, and it gives the dispersed community a fixed annual point of reconvening.
The proceedings it developed applied the same open, iterative practices to scientific publishing that the community had already applied to software---and in doing so gave academic contributors a formal, citable record of work that tenure files had no other way to recognize.
:::

## Anchoring the Ecosystem in Academic Life

By 2012, the ecosystem had a technical foundation, a community, and a conference, but the work that held it together---maintenance, teaching, reproducibility experiments, cross-project coordination---had no institutional home that recognized it as part of the job.
That gap closed in two complementary ways.

NumFOCUS, incorporated in 2012, gave the community its first legal and financial home outside any single university or company.
The founding board was largely composed of people who had first met at the 2005 Berkeley meeting, and its formation completed a transition that had begun in 2008: the SciPy conference, which Enthought had organized and hosted since 2002, passed into community ownership under NumFOCUS, governed by the same people who built and used the tools.
NumFOCUS could hold fiscal sponsorship, accept donations, and give projects a portable legal identity---everything a volunteer ecosystem needs to persist without being owned by a corporation.

The following year, the Berkeley Institute for Data Science (BIDS) opened as a cross-disciplinary research institute at the center of a public research university.
BIDS was not a computer science department or a statistics group; it was deliberately discipline-agnostic, a convening space where researchers from astronomy, neuroscience, genomics, and the social sciences worked alongside one another and alongside the people building the tools they used.
Fernando Pérez, who had been doing much of this work informally at Berkeley since 2008, became faculty director; Stefan van der Walt and I work there.
That co-location mattered because it kept ecosystem leadership embedded in the environment where the scientific and pedagogical consequences of software decisions are directly felt---where a poorly documented release breaks a graduate student's analysis, and where a well-designed reproducibility workflow can be tested on real courses with real students.
By 2015, STAT 159/259 ("Reproducible and Collaborative Statistical Data Science") was doing exactly that: students reproduced published fMRI results from raw data, graded in part on whether their instructors could reproduce the students' own results [@10.3389/fnins.2018.00727].

:::{important} Lesson 6
Open source scientific infrastructure must remain anchored in academic life---not because universities are better-managed than companies or foundations, but because reproducibility and teaching are academic concerns.
Industry partners contribute tools, compute, and engineering talent; foundations provide legal and financial continuity.
But the communities that depend on this ecosystem for research and for training the next generation of scientists need institutions that are accountable to those same goals.
NumFOCUS and BIDS provided that anchor: one portable and project-facing, one embedded in the research and teaching environment where the work is ultimately judged.
:::

## Bringing It All Back Home

The diaspora that scattered projects away from the SciPy server infrastructure was not only a loss---it was also a release.
Projects that moved to GitHub gained control of their own mailing lists, repositories, and administration, exactly the four requirements Gaël Varoquaux had articulated in 2009.
The freedom to experiment with different development practices, release cadences, and governance models was, in the years immediately following, genuinely productive: scikit-learn, scikit-image, statsmodels, and dozens of domain projects each found the workflows that fit them, cross-pollinated practices across project boundaries, and grew faster than they would have inside a shared but constrained infrastructure.
The annual SciPy conference and, later, BIDS provided the recurring forums where maintainers across projects could still compare notes, share problems, and keep a loose shared vision alive.

Over time, that shared vision became harder to sustain.
As individual projects matured, whole scientific domains---neuroimaging, geospatial data science, high-energy physics---developed their own internal coordination structures, mailing lists, and governance conventions, filling the gap that had opened when the ecosystem's shared infrastructure dispersed.
This domain-level organization was healthy in many respects, but it meant that an increasing amount of work that was really ecosystem-wide in scope---packaging standards, CI practices, documentation tooling---was being developed in domain-specific silos and reinvented independently across communities.
Newer maintainers and contributors built careers within a single project or domain rather than across several; the cross-disciplinary experience that those of us who had been there at the beginning carried was no longer naturally transmitted to the next generation.
The `scipy.org` website---nominally maintained by the SciPy library team, effectively serving as the ecosystem's front door---had become a symbol of the problem: a shared resource that no one had the mandate to govern.

In 2020, Stéfan and I co-founded the Scientific Python Project (SPP) with support from the Alfred P. Sloan Foundation [@scientificpython2020planning] to provide what the ecosystem needed---not the SciPy server's centralized infrastructure, which individual projects had rightly outgrown, but a shared layer above the individual projects: cross-project standards through SPECs, developer summits that worked like the early sprints (small, cross-disciplinary, and work focused), shared tooling and discussion forums, and a common landing page for the ecosystem `scientific-python.org`.
SPECs differ from individual project enhancement proposals (NEPs, SKIPs, and their equivalents) in two important ways: they address ecosystem-wide architectural standards rather than single-project decisions, and because individual projects retain full autonomy, SPECs operate entirely by building trust and consensus---they have no enforcement mechanism.
Early community outreach by Juanita Gomez and project leadership by Brigitta Sipőcz were especially important in turning SPP from a small node into a functioning ecosystem hub.

The 2023 Scientific Python Developer Summit illustrated what this cross-project coordination makes possible.
Henry Schreiner had developed a comprehensive development guide for the scikit-hep community---high-energy physics---that covered modern packaging, testing, CI, documentation, and more; in parallel, Dan Allan at the National Synchrotron Light Source II had developed complementary guidelines for his community.
Both guides addressed concerns that had nothing to do with the specific scientific domain and everything to do with the shared challenge of building maintainable scientific Python packages.
At the summit, that work was merged, rebranded as an ecosystem-wide resource, and released as the Scientific Python Development Guide [sppdevguide2023]---a signal, concrete and deliberate, that domain-specific silos could be dissolved when the right forum existed to bring people together.
The summit worked because it worked like the early meetings and sprints: small, cross-disciplinary, organized around real work rather than reports.

The ongoing migration from scipy.sparse matrix objects to sparse arrays is a second example of what cross-project coordination makes possible---and of how the summit mechanism accelerates work that had already begun [sppsparsegrant2022; sppsparsesummit2023].
The migration touches NumPy, SciPy, scikit-learn, scikit-image, NetworkX, and dozens of downstream libraries simultaneously; no individual project could plan or execute it alone [scipysparsearray].
Dan Schult---a mathematician and one of the ecosystem's contributors since the early days, whose work on scipy.sparse has drawn him into active contribution across NetworkX, SciPy, and the broader ecosystem---is one example of the cross-project contributor the SPP model is designed to support.
The sparse work had begun before the first summit, but bringing a cross-domain group into the same room accelerated it in the same way the early Berkeley sprints had accelerated NumPy.

:::{important} Lesson 7
Ecosystem health is a shared problem, not a project-level detail.
A diaspora that distributes ownership across many projects and domains is productive---until the shared vision that held the ecosystem together can no longer be transmitted across project boundaries, and domain-specific solutions start duplicating work that belongs to everyone.
The answer is not recentralization but deliberate coordination: cross-project standards that succeed by consensus rather than authority, summits that work like the early sprints, and shared resources that make explicit what had always been true---that the packaging guide for a particle physicist and the packaging guide for a neuroimager are the same guide.
:::

## Conclusion

The story in this paper is one route by which open, community-owned tools became the backbone of scientific computing, but it is not the last.
If you are a user or teacher, you can help by treating open source stewardship and open review as scientific contributions in their own right, by citing and teaching the tools you rely on, and by asking your institutions to recognize and support the people who keep them working.
If you maintain or build scientific Python projects---foundational libraries or domain-specific tools---you are invited to join Scientific Python's discussions, adopt shared SPECs, web infrastructure, and documentation practices, and use them as a common layer rather than rebuilding them alone.
SciPy 2026 is one of the places where this coordination still happens face to face; Scientific Python is where it continues throughout the year, so that the pattern we discovered around NumPy and SciPy can keep evolving with the ecosystem that rests on it.

## Acknowledgements

I am grateful to Matthew Brett and Stéfan van der Walt for their careful reading of the manuscript and for thoughtful comments that helped clarify the narrative and sharpen several of its central arguments.
