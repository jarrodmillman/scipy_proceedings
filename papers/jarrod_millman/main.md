---
# Ensure that this title is the same as the one in `myst.yml`
title: "One Problem, Many Projects: How Scientific Needs Built an Ecosystem"
abstract: |
  Open, community-owned scientific software does not emerge from good intentions alone; it takes real scientific needs, cross-domain collaboration, community coordination, and institutional anchors.
  This paper traces one thread of how that combination played out: how a concrete problem in neuroimaging---fMRI analyses locked in opaque, lab-specific toolboxes---led to a 2005 meeting at UC Berkeley, then to a series of collaborations across astronomy, neuroscience, physics, and statistics, and eventually to the institutions and patterns that now sustain the scientific Python ecosystem.
  It follows that journey from the first frustrations with unreproducible pipelines and single-owner software, through the architectural decisions and invisible maintenance work that turned volunteer projects into shared infrastructure, and into the deliberate replication of those early patterns at larger scale---drawing lessons along the way about what it takes for open, community-owned scientific software to last.
---

# Introduction

Open scientific software infrastructure often begins with a single actor---a researcher, a lab, a company---whose vision, energy, and effort make its creation possible.
And, while that initial focused, isolated effort is required to start, it matters greatly what happens next.
As the infrastructure grows, its survival depends on a shift: from single-actor ownership to community governance, and from informal community practice to institutional support.
When that transition happens, the infrastructure becomes durable.
When it does not, growth stalls and the burden of keeping it alive rests on a shrinking circle of people.

This paper is personal.
It is shaped by the collaborations I was part of and the problems that were in front of me at the time.
Many of the people involved in this work would tell the story differently, from their own vantage points and concerns.
A message threaded through all of it is one the academic world still resists: open source scientific software is not a side effect of research; it is research infrastructure, and it needs to be anchored in the same institutions that support the science it enables.

The argument this paper makes is specific about *how* that anchoring happens.
Durable open scientific infrastructure is not built in an day; it has layers to it: a technical foundation, followed by community governance, and then, ideally, institutional support.
Each layer must be deliberately built, and re-built, as the ecosystem grows.
Each one marks a moment when a layer that had worked at one scale stopped working at the next, and the community had to decide, consciously or not, whether and how to rebuild it.

# How to Start

In 2003, I was the system administrator for the Brain Imaging Center at UC Berkeley, responsible for an neuroimaging analysis pipeline that was, in practice, an _ad hoc_ collection of MATLAB scripts, IDL routines, C/C++ programs, AWK one-liners, and shell glue that almost nobody outside the lab fully understood.
Published results from our lab and others were nearly impossible to verify: the typical methods section said something like "SPM2 was used," with no record of which parameters had been set, in which order, or by whom.
There was no mechanism to rerun an analysis from raw data.
Scientific integrity was a clear concern, and it was made worse by technical constraints.
The academic incentive system amplified the problem: writing and maintaining analysis software did not appear in tenure files, open source authorship diluted traditional credit models, and computational reproducibility was not a recognized research output.

My colleague, Matthew Brett, had arrived at a related set of concerns by a different route.
His concern was less about reproducibility itself and more about the way neuroimaging software concentrated power: the dominant analysis tools were effectively single-owner open source, with one lab setting priorities and methods for the field.
If you disagreed with those methods, your options were to fork that single-owner project or to invest in a community-owned alternative that could be governed by the wider neuroimaging community.
That made the politics of the software visible in a way I had not thought much about before.

Rather than accepting either a single-owner tool or an opaque pipeline, we set out to build a community-owned neuroimaging platform in Python that would make analyses scriptable from raw data and invite contributors from beyond a single lab.
The project took the name Neuroimaging in Python (NIPY) [@10.1109/MCSE.2007.46].
NIPY did not become the single unified platform we had initially imagined---like SciPy itself, it eventually dispersed into a collection of focused, thriving projects: nibabel, nilearn, nipype, dipy, mne-python, and others [nipyorg].
That is a different story, but with a surprisingly similar overall arc.
What matters here is what NIPY set in motion: it drew us into collaboration with the broader ecosystem, where we learned together, shared problems and solutions across domain boundaries, and built the relationships that the rest of this paper traces.

## Scientific Python Before NumPy

When we began working on NIPY, scientific Python was taking shape around Numeric and SciPy, but the ecosystem was not yet coherent.
Numeric, maintained by Paul Dubois at Lawrence Livermore National Laboratory, was the de facto multi-dimensional array package, and in 2001 Travis Oliphant, Eric Jones, and Pearu Peterson built SciPy on top of it as a "superpackage" of numerical integration, optimization, special functions, and more [@10.1038/s41586-020-2649-2; @10.1038/s41592-019-0686-2].
Early SciPy workshops at Caltech drew dozens of scientists from astronomy, bioinformatics, physics, and other fields, mostly researchers who were not primarily programmers and who wanted Python to solve concrete problems even though the stack was still fragile [@scipy02report; @vannini2002scipy02gbb].
For domain scientists, Python already looked promising, but the underlying stack was fragmented enough that building on it felt risky.

Numeric underpinned SciPy and many existing tools.
On the other hand, numarray---developed at the Space Telescope Science Institute under Perry Greenfield's leadership---introduced a second, incompatible array implementation aimed at very large images; new projects were encouraged to use numarray, older ones could not easily migrate, and no agreed path to unification existed [@oliphant2004status; @oliphant2004comments].
Meanwhile, the `numpy-discussion` list and the SciPy workshop acted as shared communication spaces across this divide, so the same people were debating Numeric vs. numarray in public even as their code bases remained split.

## The 2005 Meeting and the Future of SciPy

By early 2005, the NIPY team was taking shape: Jonathan Taylor, JB Poline, Fernando Pérez (the creator of IPython and later the co-founder of Project Jupyter), John Hunter (the creator of Matplotlib), Matthew Brett, and me, together with a small international group of collaborators.
As we planned a NIPY meeting at Berkeley for March, we were watching the `numpy-discussion` threads that followed Travis's January Numeric3 design proposal [@oliphant2005updating] and February status update [@oliphant2005numeric3].
In those posts, Travis described Numeric3 as a bridge between Numeric and numarray, based largely on the Numeric code base, and emphasized the need to keep Numeric's tight homogeneous-array implementation moving forward while still learning from numarray.
The idea that Numeric and numarray should eventually merge was there, but only as an aspiration.
By inviting Travis and Perry to join the Berkeley meeting and to speak with Guido van Rossum (the creator of Python) and Paul, we hoped to turn that aspiration into a shared plan for a unified array object and package.

The first day was a hands-on laboratory, "Scientific Python for Neuroscience Research," taught by Fernando and John and designed to make Numeric, SciPy, IPython, matplotlib, and related tools immediately usable for working neuroimaging scientists.
The next two days were split into two parallel tracks: one, anchored by the neuroimagers and statisticians, focused on NIPY; the other, Travis and Perry---including separate conversations with Guido and Paul---worked through whether and how a unified array object could be built that preserved the strengths of both Numeric and numarray.
Each evening the two groups reconvened to discuss the day's work.

After the meeting, two `numpy-discussion` threads communicated the outcomes and made the unification plan explicit.
Perry's "Notes from meeting with Guido regarding inclusion of array package in Python core" [@perry2005guido] concluded that including an array package in Python core would be a distraction that would sap energy from the unification work that needed to happen.
Travis' "Future directions for SciPy in light of meeting at Berkeley" [@oliphant2005berkeley] opened a public discussion that quickly resolved into a modular plan for SciPy: a minimal, easy‑to‑install scipy_core that could replace both Numeric and numarray; SciPy itself as a separate package (or series of packages) of algorithms built on that core; plotting moved out into its own libraries such as Matplotlib; and domain packages, like astropy and NIPY, living as independent projects rather than inside SciPy.
Travis committed to spending the following five to six months making `scipy_core`.
That commitment culminated, eighteen months later, in the release of NumPy 1.0 in October 2006 [@oliphant2006numpy1.0].

## Building the Foundation

NumPy 1.0 was not the foundation; it was what made the foundation possible.
A scientific computing platform, comparable to what MATLAB or IDL provided, required far more: a vast collection of mature, performant algorithms spanning optimization, signal processing, linear algebra, statistics, differential equations, and more; a plotting system; an interactive environment; and the documentation, testing, and packaging discipline that makes all of it installable, trustworthy, and teachable.
<!-- a better word than discipline? --^ -->
But NumPy 1.0 managed to unlock a lot of pent-up energy.
By resolving the array fragmentation that had made building on the ecosystem feel risky, it gave the community a single stable library to build on, together.

<!--
https://github.com/numpy/numpy/graphs/contributors?from=10%2F5%2F2006&to=3%2F13%2F2009
https://github.com/scipy/scipy/graphs/contributors?from=10%2F5%2F2006&to=3%2F13%2F2009
-->
In December 2007, I organized a Berkeley sprint with Travis Oliphant, Eric Jones, Robert Kern, and others, with newer contributors like David Cournapeau and Stéfan van der Walt participating remotely, to put together a SciPy 1.0 roadmap, improve the organizational structure around SciPy, and plan more activities to help SciPy development build momentum.
After the sprint, Travis' `scipy-dev` email communicated the outcomes and announced a new SciPy board (Jones, Kern, Millman, Oliphant) with a specific commitment: monthly virtual doc-days and bug-days, coordinated over IRC, with at least one board member present at each [@oliphant2007sprint].
Over the following two years, David and Stéfan became two of the most active committers to both NumPy and SciPy, working alongside long‑time contributors, remote collaborators, and newer volunteers to restructure and expand the library, build and improve the test infrastructure, standardize and vastly expand the documentation, and create development processes and tooling that made contributions from a widely distributed, cross‑disciplinary community sustainable.
By the time SciPy 0.7 shipped in February 2009, the platform that had been rough and fragile in 2007 was something scientists could install, trust, and build on [@millman2009scipy070; @scipy070notes].

## Lesson 1

What started as a neuroimaging software problem did not stay one.
Looking for a way to build community‑owned neuroimaging tools, rather than relying on single‑owner lab software, pulled me and my collaborators into the broader scientific Python effort, because the array foundation that any such shared platform would need was itself unresolved.
Solving our problem meant first helping solve SciPy's.

The Berkeley meeting turned out to be pivotal that story.
What made it work was not the agenda but the room: a hands-on lab that got working scientists using the tools immediately, two parallel tracks that let the NIPY discussion and the array negotiation proceed side by side, and evenings when both groups came back together to compare notes.
Travis and Perry were in the building, not posting to a list---and the difference showed in what the discussion produced.

What it produced was a plan: a unified array object, a roadmap that ran through NumPy 1.0 and, two years later, the 2007 sprint's board.
What it did not yet produce was an ecosystem.
The people in that room---neuroimagers, astronomers, statisticians---were oriented around one shared technical problem: having a foundation solid enough to build on.
None of us was yet thinking about how to develop, or hold together, a distributed collection of projects across dozens of scientific domains.

:::{important} Shared problems need shared rooms.
The array unification, the plotting decision, the board and its commitments---none of it came from consensus reached asynchronously.
It came from a small, cross-disciplinary group choosing to be in the same building, more than once, until an aspiration became a plan and a plan became a foundation.
:::

# How to Grow

For the first decade, NumPy and SciPy developers were essentially the same people, releasing two different packages from the same small, tightly connected group.
The SciPy toolkits, or `scikits`, began as another layer that we built on the same shared infrastructure, largely with the same hands.
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

In February 2009, Stéfan van der Walt posted to the `scipy-dev` list with a subject line that, unknown to him, echoed Travis Oliphant's email after the 2005 Berkeley meeting: "The future of SciPy and its development infrastructure" [vanderwalt2009future].
In Travis' 2005 email, he had worried that there were "few SciPy devotees" and even fewer contributors.
Now, four years later, Stéfan worried that SciPy was a "big library of code, used by many scientists," but still maintained by only a few people.

Stéfan's diagnosis pointed to a different kind of crisis: getting commit access was too hard, and contributors' patches could sit for a year or more without feedback.
His proposed solution was distributed version control, formal code review, and higher standards for tests and documentation.
The thread that followed captured the tension the ecosystem was living through.
There was no community consensus about distributed version control at the time; for some of us it looked like necessary infrastructure, for others like unfamiliar machinery that might raise the barrier to contribution rather than lower it.
Travis worried that adding formal process would drive away contributors.
Stefan and David Cournapeau argued that without it, the burden would become unsustainable.

Gaël Varoquaux---a member of the NIPY team---wrote to me the Saturday after SciPy 2009 [varoquaux2009scikitlearn].
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
The scikits had started as SciPy Toolkits---extensions to SciPy, built by much the same small group, on the same shared infrastructure.
Once they left, most stopped describing themselves that way at all.
`scikits.learn` became scikit-learn; other projects grew up with no connection to the scikits name or idea whatsoever.
What had been imagined as one layer of a tool stack (`numpy`, `scipy`, and the `scikits`) became part of a larger ecosystem of independent projects built on top of a shared foundation.

## Stewarding Community and Conference

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
The toolchain---developed with Gaël Varoquaux and extended by Stéfan van der Walt into a system using Sphinx, LaTeX, custom scripts, and the `procbuild` preview bot---was an early instance of what would later be called "open peer review," embodying the principles I articulated in a 2012 paper titled "Learning from Open Source Software Projects to Improve Scientific Review" [cite].
Its goal was explicitly iterative: reviewers and editors worked with authors to guide papers toward acceptance, so that submitting to SciPy meant opening your writing to the same collaborative scrutiny that good open source development demands.

My last year as co-chair for the SciPy conference was 2011.
The people organizing the conference by then were no longer, by and large, the people maintaining the library.
That shift was deliberate in a way the infrastructure diaspora was not.
The scikits left `scipy.org` because the server and processes could no longer scale; the conference moved because we wanted it to---from company stewardship to community governance, from one annual meeting in California to a network of meetings on three continents.
Proceedings made it easier for academic developers and users of scientific Python to justify attending; as more of those people came, the organizers naturally became drawn from a broader pool.
By the end of the decade, "SciPy the conference" was clearly its own institution, with its own leadership and rhythms, loosely connected to the library and no longer run by the same small group.

## Lesson 2

A shared technical foundation is not just scaffolding; it is part of what holds a community together, but holding together and staying uniform are not the same thing.
When that foundation stopped scaling, the people who depended on it did not disappear; they found their own paths, and what followed looked, from the outside, like fragmentation.
From the inside it was something closer to growth: the same people, carrying norms first worked out together in Part I, building new things in new places, and discovering by experiment what a much larger ecosystem would need next.

The conference took a different route to the same outcome.
Rather than scattering under pressure, it was deliberately opened: from a company-run event serving one small circle to a community-governed institution with its own proceedings, its own international meetings, and eventually its own leadership drawn from far beyond the group that had built NumPy and SciPy.
Where the infrastructure diaspora and the scikits' departure were responses to something breaking, the conference's growth was a choice---proof that differentiation did not have to wait for a crisis to happen on purpose.

:::{important} One thing became many.
NumPy and SciPy largely split into communities of their own; the scikits scattered and mostly dropped the name; the conference outgrew the people who founded it.
Diaspora was part of that story, but not the whole of it---what actually happened was differentiation, and differentiation is what let a handful of people become an ecosystem.
:::

# How to last

## Anchoring the Ecosystem in Academic Life

In 2013, the Berkeley Institute for Data Science (BIDS) opened as a cross-disciplinary research institute at the center of a public research university.
BIDS was not a computer science department or a statistics group.
It was deliberately discipline-agnostic, a convening space where researchers from astronomy, neuroscience, genomics, and the social sciences worked alongside one another and alongside the people building the tools they used.
Fernando Pérez, who had been doing much of this work informally at Berkeley since 2008, became faculty director; Stefan van der Walt and I work there.
That co-location mattered because it kept ecosystem leadership embedded in the environment where the scientific and pedagogical consequences of software decisions are directly felt, where a poorly documented release breaks a graduate student's analysis, and where a well-designed reproducibility workflow can be tested in real courses with real students.
By 2015, STAT 159/259 ("Reproducible and Collaborative Statistical Data Science") was doing exactly that: students reproduced published neuroimaging results from raw data, graded in part on whether their instructors could reproduce the students' own results [@10.3389/fnins.2018.00727].

:::{important} Lesson 6
Open source scientific infrastructure must remain anchored in academic life, not because universities are better-managed than companies, but because reproducibility and teaching are academic concerns.
Industry partners contribute tools, compute, and engineering talent.
But the communities that depend on this ecosystem for research and for training the next generation of scientists need institutions that are accountable to those same goals.
:::

## Bringing It All Back Home

By the late-2010s, the distributed ownership that had made the post-diaspora years so prolific had produced a new problem: there was no longer anyone with the mandate, or the mechanism, to hold the ecosystem together.
As individual projects matured, whole scientific domains---neuroimaging, geospatial data science, high-energy physics---developed their own internal coordination structures, mailing lists, and governance conventions.
This domain-level organization was healthy in many respects.
It also meant that an increasing amount of work that was really ecosystem-wide in scope, packaging standards, CI practices, documentation tooling, was being developed in domain-specific silos and reinvented independently across communities.
Newer maintainers and contributors built careers within a single project or domain rather than across several.
The cross-disciplinary experience that those of us who had been there at the beginning carried was no longer naturally transmitted to the next generation.
The `scipy.org` website---nominally maintained by the SciPy library team, effectively serving as the ecosystem's front door---had become a symbol of that failure of transmission: a shared resource that no one had the mandate to govern.

In 2020, Stéfan and I co-founded the Scientific Python Project (SPP) with support from the Alfred P. Sloan Foundation [@scientificpython2020planning] to provide what the ecosystem needed, not the SciPy server's centralized infrastructure, which individual projects had rightly outgrown, but a shared layer above the individual projects: cross-project standards through SPECs, developer summits that worked like the early sprints (small, cross-disciplinary, and work focused), shared tooling and discussion forums, and a common landing page for the ecosystem `scientific-python.org`.
SPECs differ from individual project enhancement proposals (NEPs, SKIPs, and their equivalents) in two important ways: they address ecosystem-wide architectural standards rather than single-project decisions, and because individual projects retain full autonomy, SPECs operate entirely by building trust and consensus; they have no enforcement mechanism.
Early community outreach by Juanita Gomez and project leadership by Brigitta Sipőcz were especially important in turning SPP from a small node into a functioning ecosystem hub.

The 2023 Scientific Python Developer Summit illustrated what this cross-project coordination makes possible.
Henry Schreiner had developed a comprehensive development guide for the scikit-hep community, high-energy physics, that covered modern packaging, testing, CI, documentation, and more; in parallel, Dan Allan at the National Synchrotron Light Source II had developed complementary guidelines for his community.
Both guides addressed concerns that had nothing to do with the specific scientific domain and everything to do with the shared challenge of building maintainable scientific Python packages.
At the summit, that work was merged, rebranded as an ecosystem-wide resource, and released as the Scientific Python Development Guide [sppdevguide2023]---a signal, concrete and deliberate, that domain-specific silos could be dissolved when the right forum existed to bring people together.
The summit worked because it worked like the early meetings and sprints: small, cross-disciplinary, organized around real work rather than reports.

The ongoing migration from scipy.sparse matrix objects to sparse arrays is a second example of what cross-project coordination makes possible, and of how the summit mechanism accelerates work that had already begun [sppsparsegrant2022; sppsparsesummit2023].
The migration touches NumPy, SciPy, scikit-learn, scikit-image, NetworkX, and dozens of downstream libraries simultaneously.
No individual project could plan or execute it alone [scipysparsearray].
Dan Schult---a mathematician and one of the ecosystem's contributors since the early days, whose work on scipy.sparse has drawn him into active contribution across NetworkX, SciPy, and the broader ecosystem---is one example of the cross-project contributor the SPP model is designed to support.
The sparse work had begun before the first summit, but bringing a cross-domain group into the same room accelerated it in the same way the early Berkeley sprints had accelerated NumPy.

:::{important} Lesson 7
Ecosystem health is a shared problem, not a project-level detail.
A diaspora that distributes ownership across many projects and domains is productive until the shared vision that held the ecosystem together can no longer be transmitted across project boundaries, and domain-specific solutions start duplicating work that belongs to everyone.
The answer is not recentralization but deliberate coordination: cross-project standards that succeed by consensus rather than authority, summits that work like the early sprints, and shared resources that make explicit what had always been true, that the packaging guide for a particle physicist and the packaging guide for a neuroimager are the same guide.
:::

## Conclusion

The story in this paper is one route by which open, community-owned tools became the backbone of scientific computing, but it is not the last.
If you are a user or teacher, you can help by treating open source stewardship and open review as scientific contributions in their own right, by citing and teaching the tools you rely on, and by asking your institutions to recognize and support the people who keep them working.
If you maintain or build scientific Python projects---foundational libraries or domain-specific tools---you are invited to join Scientific Python's discussions, adopt shared SPECs, web infrastructure, and documentation practices, and use them as a common layer rather than rebuilding them alone.
SciPy 2026 is one of the places where this coordination still happens face to face; Scientific Python is where it continues throughout the year, so that the pattern we discovered around NumPy and SciPy can keep evolving with the ecosystem that rests on it.

## Acknowledgements

I am grateful to Matthew Brett and Stéfan van der Walt for their careful reading of the manuscript and for thoughtful comments that helped clarify the narrative and sharpen several of its central arguments.
